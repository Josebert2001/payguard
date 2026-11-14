# PayGuard demo script for the PayPal AI Hackathon (keep under 2:55)

## Before you record (about 15 min)
1. In `.env`, set the sandbox `PAYPAL_CLIENT_ID` and `PAYPAL_CLIENT_SECRET` and `GEMINI_API_KEY`. Get the PayPal keys from developer.paypal.com → Apps & Credentials → Sandbox.
2. In developer.paypal.com → Testing Tools → Sandbox Accounts, copy the **Personal (buyer)** email and password. You'll log in with it on camera.
3. Run `start.bat`. Check that http://localhost:8000/api/health shows `"db2": true` and that the PayPal buttons appear after a safe check.
4. Do one full dry run so the PayPal sandbox popup has already loaded once. It's slow the first time.
5. Record at 1080p (Win + Alt + R, or OBS) with browser zoom at 100%. Close other tabs and notifications.

**Don't show:** IBM or Google logos, music, your `.env` file, or the PayPal sandbox password.

## Script
| # | Time | What to show | What to say |
|---|---|---|---|
| 1 | 0:00–0:15 | Home page | "Scammers talk buyers out of buyer protection: 'send it as friends and family'. PayGuard is an AI firewall in front of PayPal checkout. It checks the seller before your money leaves." |
| 2 | 0:15–0:45 | Drag `docs/demo/sample-chat.png` in → **Check now** | "I share a screenshot of the chat. Gemini reads the seller, the account number and the Pidgin. Verdict: don't pay, with three plain reasons. And notice: no PayPal button. The server refuses to create a PayPal order for a blocked seller, even if someone edits the page." |
| 3 | 0:45–1:25 | **Real shop** → check → PayPal button → log in with the sandbox buyer → approve → show "Payment captured" | "A genuine shop passes, so PayPal opens with buyer protection. The order carries the PayGuard verdict in its custom ID, and after capture the status is written back to the audit record." |
| 4 | 1:25–2:05 | **Shopping agent** → **Ticket seller** → Run, then **Safe order** → Run | "This is agentic commerce that can't be tricked. The agent refuses the friends-and-family ticket scam. For the bookshop it builds a PayPal Goods and Services order, and I approve the payment. The agent never moves money on its own." |
| 5 | 2:05–2:35 | `/market` → **Buy now with PayPal** on the PS5 (stopped), then the textbook (PayPal opens) | "Marketplaces get this in one API call at checkout. The scam listing is stopped, and the real one goes straight to PayPal." |
| 6 | 2:35–2:50 | Switch to **Pidgin** on a verdict, then **Report this seller** | "It speaks Pidgin, and every report protects the next buyer. Three reports block a seller for everyone." |
| 7 | 2:50–2:55 | Home page | "PayGuard: check before you pay. Next: Truecaller for account numbers. Thank you." |

## Upload
Upload to YouTube as **Public**, titled "PayGuard – AI payment firewall for PayPal checkout". Paste the link into Devpost, then replace the video link in `PAYPAL_DEVPOST.md` and the README.
