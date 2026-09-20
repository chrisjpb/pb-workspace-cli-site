# PB Workspace CLI — OAuth branding site

Static site for the Google OAuth consent-screen branding requirements. Built so the
`gog` OAuth app (Cloud project number `928507236207`) can be moved from **Testing**
to **In production**, which removes the 7-day refresh-token expiry that breaks
unattended/headless runs.

## Files

| File | Purpose | Required by Google |
| --- | --- | --- |
| `index.html` | App homepage — describes what the app is and does, links the privacy policy | Yes |
| `privacy.html` | Privacy policy, incl. Google API Limited Use disclosure | Yes |
| `terms.html` | Terms of service | Referenced by the Branding page |

Verified locally: all links resolve, HTML is well-formed, all pages return HTTP 200,
and the homepage links the privacy policy (Google checks this link specifically).

## Requirements Google enforces

From Google's "Manage OAuth App Branding" doc:

> These links are required for all external production apps. You will not be able to
> submit your app for verification if it is missing these links.

- The homepage must be on a **domain you own and have verified** (searchable in the
  console's *Authorized domains* field).
- The homepage must describe the app's functionality — it cannot be only a login page.
- The privacy policy must be **linked from the homepage**, and the URL entered in the
  console must match that link.
- Pages must be publicly reachable over HTTPS; Google's checker fetches them.

Note: verification itself is **not** required. An unverified production app keeps
working; it shows a warning screen before consent and is capped at 100 new users.
This app has one user, so both are irrelevant.

## Steps

1. **Pick a domain you own** (e.g. `abodaconsulting.com`) and host these three files at
   its root. Any of these work:
   - **GitHub Pages** — push to a repo, set a custom domain via `CNAME`, enable HTTPS.
   - **Cloudflare Pages** — drag the folder in, attach the custom domain.
   - **Existing web host** — drop the three files in the web root.
2. **Verify the domain in [Search Console](https://search.google.com/search-console)**
   (DNS TXT record is easiest) so it can be selected under *Authorized domains*.
3. **Google Auth Platform → [Branding](https://console.cloud.google.com/auth/branding?project=928507236207)**:
   - App name: `PB Workspace CLI`
   - User support email: `chrisjpb04@gmail.com`
   - App domain → Application home page: `https://<domain>/`
   - App domain → Privacy policy link: `https://<domain>/privacy.html`
   - App domain → Terms of service link: `https://<domain>/terms.html`
   - Authorized domains: `<domain>`
   - Developer contact information: `chrisjpb04@gmail.com`
4. **[Audience](https://console.cloud.google.com/auth/audience?project=928507236207) →
   Publish app → Confirm.** Status flips Testing → In production. You do *not* submit
   for verification.
5. **Re-auth once** so a token minted under production rules replaces the Testing-era one:

   ```bash
   gog auth add chrisjpb04@gmail.com --services calendar,contacts,docs,drive,gmail,sheets --force-consent
   ```

   The current token was issued while the app was still in Testing; re-authorizing
   after publishing is the cheap way to be sure the durable token is the one in the
   keyring.

## Optional: swap the domain into the pages

If you want the domain spelled out in the pages (currently they use relative links,
which need no domain), edit the three files and replace links as needed. Relative
links are the safest choice — they keep working on any host.
