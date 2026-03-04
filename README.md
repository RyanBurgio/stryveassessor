# Stryve PE Marketing Value Model (V6)

AI-powered PE marketing due diligence tool. Enter a portfolio company, scan their digital presence, and model the enterprise value impact of fixing their marketing.

## How It Works

1. **Enter** a company name + website URLs
2. **AI scans** their digital presence (brand, demand gen, analytics, governance)
3. **Enter** 3 financial inputs (revenue, EBITDA margin, multiple)
4. **Dashboard** shows: meeting script, evidence, EV opportunity, personalized recommendations

---

## Deploy to Vercel (Step by Step)

### Step 1 — Get an Anthropic API Key

1. Go to [console.anthropic.com](https://console.anthropic.com)
2. Sign up or log in
3. Go to **Settings → API Keys**
4. Click **Create Key**, give it a name like "Stryve Tool"
5. Copy the key (starts with `sk-ant-...`) — you'll need it in Step 4

> **Cost note:** Each company scan uses ~2 API calls. At typical Sonnet pricing, expect roughly $0.05–0.15 per scan. Running this for a handful of prospects before a meeting costs pennies.

### Step 2 — Push to GitHub

1. Create a new repository on [github.com](https://github.com/new) (private is fine)
2. Upload all the files from this folder to that repo, **or** use the terminal:

```bash
cd stryve-pe-tool
git init
git add .
git commit -m "Stryve PE Value Model v6"
git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPO-NAME.git
git push -u origin main
```

### Step 3 — Connect to Vercel

1. Go to [vercel.com](https://vercel.com) and sign in with GitHub
2. Click **"Add New Project"**
3. Select your repository from the list
4. Vercel will auto-detect it as a Vite project — leave the defaults:
   - **Framework Preset:** Vite
   - **Build Command:** `npm run build`
   - **Output Directory:** `dist`
5. Click **Deploy**

### Step 4 — Add Your API Key

1. In your Vercel project, go to **Settings → Environment Variables**
2. Add a new variable:
   - **Name:** `ANTHROPIC_API_KEY`
   - **Value:** paste your `sk-ant-...` key
   - **Environment:** Production (and Preview if you want it on preview URLs too)
3. Click **Save**
4. Go to **Deployments** and click the **⋮** menu on the latest deployment → **Redeploy**

### Step 5 — Done

Your tool is live at `your-project-name.vercel.app`. Share the URL or connect a custom domain in Vercel Settings → Domains.

---

## Local Development

If you want to run it locally to test changes:

```bash
# 1. Install dependencies
npm install

# 2. Create a local env file with your API key
cp .env.example .env.local

# 3. Edit .env.local and paste your real API key

# 4. Run the dev server
npm run dev
```

Open [http://localhost:5173](http://localhost:5173) in your browser.

> **Note:** During local development, the API proxy (`/api/anthropic`) only works when deployed to Vercel. For local testing, you can temporarily run `npx vercel dev` instead of `npm run dev` to emulate the serverless function locally.

---

## Project Structure

```
stryve-pe-tool/
├── api/
│   └── anthropic.js      ← Serverless proxy (keeps your API key hidden)
├── src/
│   ├── App.jsx            ← The entire tool (all UI + logic)
│   └── main.jsx           ← React entry point
├── index.html             ← HTML shell
├── package.json           ← Dependencies
├── vite.config.js         ← Build config
├── vercel.json            ← Routing config
├── .env.example           ← Template for your API key
└── .gitignore             ← Keeps secrets and node_modules out of git
```

## What the API Proxy Does

When the tool runs inside Claude, API calls happen automatically without a key. On Vercel, the browser can't call Anthropic directly (the key would be exposed). So:

- The browser sends requests to `/api/anthropic` (your Vercel URL)
- The serverless function in `api/anthropic.js` receives the request
- It adds your API key (from environment variables) and forwards it to Anthropic
- It sends the response back to the browser

Your API key never leaves the server.
