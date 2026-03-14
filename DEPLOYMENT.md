# Deployment Guide (Vercel + PostgreSQL)

This app is a Next.js 15 finance platform with Clerk auth, Prisma + Postgres, Arcjet middleware, Gemini usage, Resend email, and Inngest functions.

## 1) Prerequisites

- Node.js 20 LTS
- A PostgreSQL database (Neon, Supabase, Railway, Render, etc.)
- Clerk project keys
- Arcjet key
- Gemini API key
- Resend API key

## 2) Prepare environment variables

1. Copy `.env.example` to `.env` locally.
2. Fill every value.
3. Add the same keys in Vercel Project Settings > Environment Variables.

Required keys:

- `DATABASE_URL`
- `DIRECT_URL`
- `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY`
- `CLERK_SECRET_KEY`
- `NEXT_PUBLIC_CLERK_SIGN_IN_URL`
- `NEXT_PUBLIC_CLERK_SIGN_UP_URL`
- `NEXT_PUBLIC_CLERK_AFTER_SIGN_IN_URL`
- `NEXT_PUBLIC_CLERK_AFTER_SIGN_UP_URL`
- `GEMINI_API_KEY`
- `RESEND_API_KEY`
- `ARCJET_KEY`

## 3) Validate locally before deploy

Run these commands:

```bash
npm install
npm run db:generate
npm run build:deploy
```

If build passes locally, deployment is much more likely to pass.

## 4) Connect and deploy on Vercel

1. Import this repository into Vercel.
2. Configure project settings:
   - Framework preset: `Next.js`
   - Install Command: `npm install`
   - Build Command: `npm run build:deploy`
3. Add all environment variables from step 2.
4. Deploy.

## 5) Post-deploy checks

After deployment, verify:

- App loads at the production URL.
- Sign-up/sign-in via Clerk works.
- Protected routes redirect correctly.
- Database writes work (create account/transaction).
- Email features (Resend) work.
- AI categorization or Gemini-dependent actions work.
- Arcjet middleware does not block legitimate traffic.

## 6) Inngest notes

- Local dev command:

```bash
npx inngest-cli@latest dev -u http://localhost:3000/api/inngest
```

- Your serve handler exists at `/api/inngest`.
- The `inngest dev` process can end with code 1 when stopped; that alone is not a deployment failure.
- For scheduled jobs in production, ensure your Inngest app is set up to call the deployed `/api/inngest` endpoint.

## 7) Common failure points and fixes

- `ERESOLVE` during install:
  - Fixed in this repo by using `react@18.3.1`, `react-dom@18.3.1`, and `date-fns@3.6.0`.

- Prisma connection errors:
  - Re-check `DATABASE_URL` and `DIRECT_URL`.
  - Ensure DB allows connections from Vercel.

- Auth redirect loop:
  - Verify Clerk keys and redirect URLs exactly match your app setup.

- 500 errors in middleware:
  - Usually missing `ARCJET_KEY` or auth env values.

## 8) Useful commands

```bash
npm run dev
npm run lint
npm run db:migrate:deploy
npm run build:deploy
npm run start
```
