# Secure your app in just a few lines of code using Arcjet! ✈️

![arcjet](<Arcjet (1).gif>)

Hi there! I belong to the 21st century. Oh, wait! You too?? Then you must be using something to add security to your applications. If not, then this blog is _for you_. If so, then this blog is _definitely for you_!

It's always been a complicated task to secure your application. And thoughts of adding a security layer make us feel hectic. But some people in the world still think of us and for our boon.

Introducing you **Arcjet**! _A security layer for developers in just a few lines of code_. In this blog, I'll be covering about Arcjet with a short tutorial on adding a security layer to your next.js app.

&nbsp;

## What is [Arcjet](https://arcjet.com/)?? 🚀
Arcjet is an open-source product that helps developers protect their apps in just a few lines of code.

![arcjet](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/nqjzszpwcqahzdxjk9rh.png)
<figcaption> Arcjet features </figcaption>

Arcjet is designed to process data primarily within your environment to minimize latency and reduce the amount of sensitive information sent to their systems (only a small data is sent through their real-time decision API to do some analysis for better future updates).

![architecture](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/foqctfkgjmrjx6i4r0x4.png)
<figcaption> Arcjet Architecture and Components </figcaption>

In many cases, Arcjet can make a decision locally and report that decision asynchronously to the Arcjet API. The Arcjet SDK includes a WebAssembly module which is used to analyze requests locally in your own environment. Where possible, a decision is taken locally and then reported to Arcjet so that you can view the details in the [Arcjet Dashboard](https://app.arcjet.com/auth/signin). However, there are some cases where Arcjet needs to make a decision in the cloud.The Cloud API has been designed for high performance and low latency and combines all the security functionality into a single request.

## Security layers you can inject into your app using Arcjet 💉
Arcjet provides 4 sets of security layers that can build security functionality. Each layer can be used independently or combined as part of a pre-configured product.
- **[Shield](https://docs.arcjet.com/shield/concepts)** - Protect against common attacks.
- **[Rate Limiting](https://docs.arcjet.com/rate-limiting/concepts)** - Limit the number of requests from a client over a period of time.
- **[Bot Protection](https://docs.arcjet.com/bot-protection/concepts)** - Detect and block automated clients.
- **[Email Validation & Verification](https://docs.arcjet.com/email-validation/concepts)** - Check that an email address is valid.

Let's discuss more while coding!! 

&nbsp;

## A Quick Guide to Inject Some Security to Your Application 👏🚂
As stated at the beginning of the article, we'll be using next.js as the tech stack to add some security tests!
But, does Arcjet support only NextJs??
The answer is **BIG NO**!! Here is the list of the tech stacks, you can use to add a security layer:
![tech](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qchka1cjor8y4rof9bqw.png)
<figcaption> Tech Stack Supported by Arcjet </figcaption>

Let's get back to our coding world!

1️⃣ Create a next app
Use the command `npx create-next-app@latest` and configure it as provided.

2️⃣ Install the Arcjet SDK
In your project root, run the following command to install the SDK:
- Using npm: `npm i @arcjet/next`
- Using pnpm: `pnpm add @arcjet/next`
- using yarn: `yarn add @arcjet/next`

3️⃣ Set your key
[Create a free Arcjet account](https://app.arcjet.com/auth/signin) then follow the instructions to add a site and get a key. Add it to a `.env.local` file in your Next.js project root:
 
![api key](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8s46sc90xx03qntv238m.png)

```
ARCJET_KEY=your_arcjet_api_key
```

&nbsp;

### 🔰 Applying all four Security Layers one by one
1️⃣ Test **Shield**!

Add this file inside the `/app/api/arcjet/route.ts` of your next.js project folder.
```js
import arcjet, { shield } from "@arcjet/next";
import { NextResponse } from "next/server";

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    shield({
      mode: "DRY_RUN",
    }),
  ],
});

export async function GET(req: Request) {
  const decision = await aj.protect(req);

  for (const result of decision.results) {
    console.log("Rule Result", result);
  }

  console.log("Conclusion", decision.conclusion);

  if (decision.isDenied() && decision.reason.isShield()) {
    return NextResponse.json(
      {
        error: "You are suspicious!",
        // Useful for debugging, but don't return it to the client in
        // production
        //reason: decision.reason,
      },
      { status: 403 },
    );
  }

  return NextResponse.json({
    message: "Hello world",
  });
}
```

Now, start your app and load `http://localhost:3000`. Refresh the page and you will see the requests showing up in the [Arcjet dashboard](https://app.arcjet.com/auth/signin).

After 5 requests, Arcjet Shield will be triggered. This simulates the threshold being reached and is a constant, so you can use it as part of your tests.

```json
Rule Result ArcjetRuleResult {
   ttl: 0,
   state: 'DRY_RUN',
   conclusion: 'DENY',
   reason: ArcjetShieldReason { type: 'SHIELD', shieldTriggered: true }
}
Conclusion ALLOW
```

2️⃣ Test **Rate Limiting**!

Add this file inside the `/app/api/arcjet/route.ts` of your next.js project folder.

```js
import arcjet, { tokenBucket } from "@arcjet/next";
import { NextResponse } from "next/server";

const aj = arcjet({
  key: process.env.ARCJET_KEY!, // Get your site key from https://app.arcjet.com
  characteristics: ["userId"], // track requests by a custom user ID
  rules: [
    // Create a token bucket rate limit. Other algorithms are supported.
    tokenBucket({
      mode: "LIVE", // will block requests. Use "DRY_RUN" to log only
      refillRate: 5, // refill 5 tokens per interval
      interval: 10, // refill every 10 seconds
      capacity: 10, // bucket maximum capacity of 10 tokens
    }),
  ],
});

export async function GET(req: Request) {
  const userId = "user123"; // Replace with your authenticated user ID
  const decision = await aj.protect(req, { userId, requested: 5 }); // Deduct 5 tokens from the bucket
  console.log("Arcjet decision", decision);

  if (decision.isDenied()) {
    return NextResponse.json(
      { error: "Too Many Requests", reason: decision.reason },
      { status: 429 },
    );
  }

  return NextResponse.json({ message: "Hello world" });
}
```

Now, start your app and load `http://localhost:3000`. Refresh the page and you will see the requests showing up in the [Arcjet dashboard](https://app.arcjet.com/auth/signin).


3️⃣ Test **Bot Protection**!

Create a file called `/middleware.ts` in your project root (at the same level as `pages` or `app` or inside `src`).

```js
import arcjet, { createMiddleware, detectBot } from "@arcjet/next";
export const config = {
  // matcher tells Next.js which routes to run the middleware on.
  // This runs the middleware on all routes except for static assets.
  matcher: ["/((?!_next/static|_next/image|favicon.ico).*)"],
};
const aj = arcjet({
  key: process.env.ARCJET_KEY!, // Get your site key from https://app.arcjet.com
  rules: [
    detectBot({
      mode: "LIVE", // will block requests. Use "DRY_RUN" to log only
      // configured with a list of bots to allow from
      // https://arcjet.com/bot-list
      allow: [], // "allow none" will block all detected bots
    }),
  ],
});
// Pass any existing middleware with the optional existingMiddleware prop
export default createMiddleware(aj);
```

Now, start your app and load `http://localhost:3000`. Refresh the page and you will see the requests showing up in the [Arcjet dashboard](https://app.arcjet.com/auth/signin).

4️⃣ Test **Email Validation**!

Add this file inside the `/app/api/arcjet/route.ts` of your next.js project folder.

```js
import arcjet, { validateEmail } from "@arcjet/next";
import { NextResponse } from "next/server";

const aj = arcjet({
  key: process.env.ARCJET_KEY!, // Get your site key from https://app.arcjet.com
  rules: [
    validateEmail({
      mode: "LIVE", // will block requests. Use "DRY_RUN" to log only
      block: ["DISPOSABLE"], // block disposable email addresses
    }),
  ],
});

export async function POST(req: Request) {
  const decision = await aj.protect(req, {
    // The email prop is required when a validateEmail rule is configured.
    // TypeScript will guide you based on the configured rules
    email: "test@0zc7eznv3rsiswlohu.tk",
  });

  if (decision.isDenied()) {
    return NextResponse.json({ error: "Forbidden" }, { status: 403 });
  }

  return NextResponse.json({
    message: "Hello world",
  });
}
```

Now, start your app and load `http://localhost:3000`. Refresh the page and you will see the requests showing up in the [Arcjet dashboard](https://app.arcjet.com/auth/signin).

**This is how your dashboard will look like**:

![dashboard](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/h4sx9555jxibvo1wj7r0.png)
<figcaption> Arcjet Dashboard </figcaption>

&nbsp;

> I followed the official Arcjet Docs for the above demonstration, find the link here: [Arcjet Offical Documentation](https://docs.arcjet.com/get-started)


In case, you don't want to run this manually, you can use the **[LIVE EXAMPLE](https://example.arcjet.com/)** of Arcjet to test all these features.

I know it's tougher to understand just by reading. But don't worry, Arcjet has its own personal YouTube channel. You can learn many things there. **Subscribe it** to show your support 🦚

{% embed https://www.youtube.com/@arcjethq %}

&nbsp;

## Conclusion 📄
Arcjet can be used with specific rules on individual routes or as general protection on your entire application. You can customize bot protection, rate limiting for your API and minimize fraudulent registrations with the signup form protection.

In just a few lines of code, you just added a security layer to your application. The product is fully open-sourced, that means you are adding a security layer at free of cost!

**Github Repo Link**: https://github.com/arcjet/arcjet-js (give it a 🌟)

In case, you have any doubts, reach out to their **Official Discord Server**: https://discord.gg/nqhasbvd (see you there 👋)

That's all for today. Thanks for your time. You're amazing! All good forces are on your side. 💝

{% cta https://github.com/arcjet/arcjet-js %} Star Arcjet on Github ⭐ {% endcta %}