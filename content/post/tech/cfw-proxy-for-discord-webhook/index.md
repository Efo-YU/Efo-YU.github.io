---
title: "Discord Webhook 429エラーをProxyで回避する"
date: 2026-04-24T09:27:37+09:00
# tags: [""]
---

## 課題

Google Apps Script（GAS）からDiscordへWebhookでメッセージを送信する際，GASの共有IPアドレスが原因でDiscordのレートリミットに巻き込まれ，IPブロックされることがある：

> `Discord API Error (429): error code: 1015`

この問題は自身の送信頻度が適正であっても発生する．

## 解決策

Cloudflare Workers（CFW）を用いて，リクエストをDiscordへ横流しするだけのプロキシを構築する．

- **drop-in replacement**: クライアント側の変更を最小限にするため，Base URLの置換だけで機能するようパスをそのまま引き継ぐ仕様とする．
- **whitelist管理**: セキュリティ対策として，`WH_`というプレフィックスから始まるCloudflare Secretsに登録されたWebhook URLのみを許可する．

## 実装

`index.ts`のみ．

```ts
export interface Env {
  [key: string]: unknown;
}
export default {
  async fetch(
    request: Request,
    env: Env,
    ctx: ExecutionContext,
  ): Promise<Response> {
    if (request.method !== "POST") {
      return new Response(JSON.stringify({ error: "Method Not Allowed" }), {
        status: 405,
        headers: { "Content-Type": "application/json" },
      });
    }
    const requestUrl = new URL(request.url);
    const pathname = requestUrl.pathname;
    const targetWebhook = `https://discord.com${pathname}`;
    const allowedWebhooks: string[] = Object.keys(env)
      .filter((key) => key.startsWith("WH_") && typeof env[key] === "string")
      .map((key) => env[key] as string);
    if (!allowedWebhooks.includes(targetWebhook)) {
      return new Response(
        JSON.stringify({
          error: "Forbidden: Webhook URL is not in the allowlist",
        }),
        {
          status: 403,
          headers: { "Content-Type": "application/json" },
        },
      );
    }
    const contentType =
      request.headers.get("content-type") || "application/json";
    try {
      const body = await request.text();
      const discordResponse = await fetch(targetWebhook, {
        method: "POST",
        headers: {
          "Content-Type": contentType,
        },
        body: body,
      });
      const responseBody = await discordResponse.text();
      return new Response(responseBody, {
        status: discordResponse.status,
        headers: {
          "Content-Type":
            discordResponse.headers.get("Content-Type") || "application/json",
        },
      });
    } catch (error) {
      return new Response(
        JSON.stringify({ error: "Internal Server Error while forwarding" }),
        {
          status: 500,
          headers: { "Content-Type": "application/json" },
        },
      );
    }
  },
};
```

## 使用方法

Webhook URLを`WH_`というプレフィックスから始まる名前でSecretsに登録し，送信元で`discord.com`を`your-worker.your-subdomain.workers.dev`に置換するだけ．

**変更前:**
`https://discord.com/api/webhooks/123456789/ABCDEFG`

**変更後:**
`https://your-worker.your-subdomain.workers.dev/api/webhooks/123456789/ABCDEFG`
