# PayGuard — PayPal AI Hackathon submission (copy each part into the matching Devpost box)

Deadline: Thu Nov 12, 2026, 12:00 PM PST = **9:00 PM Lagos**.

## Project name
PayGuard

## Tagline
An AI payment firewall in front of PayPal checkout: check the seller before your money leaves.

## Inspiration
I got scammed buying online, and so has almost everyone I know in Nigeria. The scripts repeat: "pay as Friends and Family", "your PayPal is limited, log in here", "pay a registration fee for the job". Social engineering was 47% of fraud in Nigeria in 2025 (NIBSS). Buyer protection only works if the buyer uses it, and scammers work hard to talk people out of it. I built ScamShield earlier to check single messages. PayGuard goes further: it checks the whole seller chat and sits *in front of* PayPal checkout, so a scam is stopped before the order is ever created.

## What it does
- **Check a seller in one tap:** share a chat screenshot (installable web app, Android share target) or paste the text. Gemini vision reads the seller, phone and account numbers, price and requests. PayGuard answers 🟢 No scam signs / 🟡 Be careful / 🔴 Don't pay, with at most three plain reasons, in English or Nigerian Pidgin.
- **Protected PayPal checkout:** PayPal buttons only work after a check. A blocked verdict means no PayPal order can be created. A "be careful" verdict needs explicit acknowledgement first. Safe payments go straight to PayPal checkout with buyer protection.
- **Agentic commerce, safely:** tell the shopping agent "Buy the PS5 from FastDeals for $210". It builds the order, screens it, refuses Friends-and-Family or scam sellers, and prepares a PayPal Goods & Services order for your approval. The agent never moves money on its own.
- **Checkout API for marketplaces:** a platform calls `POST /api/v1/checkout/verify` when the buyer taps Pay; PayPal only opens if PayGuard allows it. Live demo marketplace at `/market`.
- **Community protection:** report a seller's account number, phone or email; three reports block it automatically for everyone.
- **Fraud dashboard:** live numbers, plus plain-English questions turned into guarded, read-only SQL.

## How we built it
- **PayPal (central to the design):** PayPal **Orders v2 (sandbox)** called server-side, plus **PayPal JS SDK** buttons in the browser.
  - `POST /api/paypal/orders` re-reads the verdict from the database: `BLOCK` → 403, `REVIEW` without acknowledgement → 409. The amount comes from the server, never the browser.
  - `custom_id = payguard:<screening>:<verdict>:<score>` links every PayPal order to its fraud-check audit record.
  - `POST /api/paypal/orders/{id}/capture` checks the order belongs to that screening, captures it, and writes the status back.
  - The client secret never reaches the browser, and a modified browser can't force a blocked payment through.
- **AI:** Google **Gemini 2.5 Flash** for screenshot reading (vision), an investigator agent (LangChain tools that query the scam knowledge base, blocklist, community reports and payee history), the shopping agent, and text-to-SQL. Rules and evidence set the score; the AI may raise risk or explain it but **can never lower a verdict**, so the AI can't become a loophole.
- **Data:** IBM Db2 12.1 with vector search over 25 scam playbooks, 105 Nigerian scam/safe examples and community reports; auto-escalating blocklist.
- **App:** Python, FastAPI, plain HTML/CSS/JS PWA. 45 automated tests, including PayPal gating tests.

## Challenges we ran into
Keeping the AI helpful without letting it override evidence (it can only raise risk). Making sure the PayPal order can't be created for a blocked seller even if someone edits the page (server re-checks before every order and capture). Few public Nigerian scam datasets, so playbooks and examples were written by hand. Keeping the answer short enough for a buyer in a hurry.

## Accomplishments that we're proud of
A buyer gets a clear answer in seconds, in Pidgin if they want, without copying anything. Safe buyers land in PayPal checkout with buyer protection; scam sellers never get an order created. The same check plugs into any marketplace checkout with one API call, and the shopping agent shows agentic commerce that refuses to be scammed.

## What we learned
Small, well-chosen local data and transparent rules beat a big generic model on a local problem. Explaining *why* matters as much as the verdict. And payment safety has to be enforced on the server, at the moment the PayPal order is created, not just shown in the UI.

## What's next for PayGuard
- WhatsApp bot: forward a message or voice note, get the verdict in the chat.
- Browser extension that spots fake PayPal and bank login pages.
- "Truecaller for account numbers": banks, fintechs and marketplaces call PayGuard when a user enters a recipient, and get a warning before the money leaves.
- Yoruba, Hausa and Igbo support.

## Built with
python, fastapi, paypal, paypal-orders-api, paypal-js-sdk, gemini, langchain, ibm-db2, vector-search, docker, javascript, pwa

## AI tools used (the form asks for this)
Google Gemini 2.5 Flash: screenshot reading (vision), investigator agent with tool calls, shopping agent, text-to-SQL. LangChain: tool orchestration. Local 768-d hashing embeddings (optional Gemini embeddings) for scam similarity search.

## Updates during the hackathon (rule: explain significant work)
Built from scratch during the submission period (repo first commit Nov 10, 2026). It extends the idea of my earlier ScamShield message checker, but all PayGuard code — PayPal gating, checkout API, shopping agent, screenshot check, database and dashboard — is new.

## Links
- Code (public, MIT licence): https://github.com/Josebert2001/payguard
- Demo video (YouTube, public, under 3 min): https://youtu.be/Xl68sUC8XLY

## Testing instructions (paste into "Testing instructions" if asked)
1. `git clone https://github.com/Josebert2001/payguard && cd payguard`
2. `pip install -r requirements.txt && pytest -q` — runs without a database or API keys (includes PayPal gating tests).
3. Full app: `cp .env.example .env`, add PayPal sandbox `PAYPAL_CLIENT_ID`/`PAYPAL_CLIENT_SECRET` (and optional `GEMINI_API_KEY`), `docker compose up -d`, `python src/seed.py --reset`, `uvicorn main:app --app-dir src`, open http://localhost:8000.
4. Try: the "Instagram vendor" case (blocked, PayPal button disabled), the "real shop" case (allowed, PayPal sandbox checkout opens), and `/market` for the platform checkout.
