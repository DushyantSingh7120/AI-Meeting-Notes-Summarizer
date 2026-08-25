# Security Notes

## Client-Side Gemini API Key

This project calls the Gemini API **directly from the browser** for simplicity as a demonstration/portfolio project. The API key is visible in the client-side source code. The following mitigations are in place to limit the risk of abuse:

### Mitigations

| Layer | Detail |
|---|---|
| **HTTP Referrer Restriction** | The key is restricted in Google Cloud Console to only accept requests originating from authorised referrer domains. Requests from unknown origins are rejected by Google's API gateway. |
| **API Scope Restriction** | The key is scoped exclusively to the Generative Language API. It cannot be used to access any other Google Cloud service or resource. |
| **Free Tier / No Billing Account** | The key uses Google AI Studio's free tier with no billing account attached. Worst-case abuse results in temporary rate-limiting or quota exhaustion — **not financial exposure**. There is no credit card or billing instrument that can be charged. |

### What This Means in Practice

- An attacker who extracts the key can, at most, consume the free-tier quota until Google's rate limiter kicks in.
- They cannot incur charges, access other APIs, or reach any backend data.
- Firebase Firestore access is governed separately by Firebase Security Rules and is authenticated via Firebase Auth — the Gemini key grants no access to Firestore.

---

## Production Recommendation

> [!IMPORTANT]
> A production version of this app would **never expose an API key on the client**.

The correct architecture is to proxy every Gemini request through a **server-side function** (e.g., a Firebase Cloud Function, Next.js API route, or any backend endpoint). The flow would be:

```
Browser  →  POST /api/summarize (no key)
              ↓
         Server-side function (key stored in env var / Secret Manager)
              ↓
         Gemini API  →  response back to browser
```

This eliminates client-side key exposure entirely and allows:
- Per-user rate limiting enforced server-side
- Request logging and abuse detection
- Easy key rotation without a frontend deploy

---

## Reporting a Vulnerability

If you discover a security issue in this project, please open a GitHub Issue or contact the maintainer directly. Do not file public issues for sensitive disclosures.
