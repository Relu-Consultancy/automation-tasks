EOD — 2026-07-02

Indeed — "No Apply button" + "Indeed job @Indeed" title (root cause fixed)

The session-based apply flow calls scrapePlatformJobUrls(), not scrapeIndeedUK(). The generic anchor-href pattern was stripping ?jk= query params from Indeed URLs, producing bare /viewjob paths with no job key — hence "No Apply button found."
Added a dedicated slug === "indeed" branch in scrapePlatformJobUrls() that reads [data-jk] card attributes (React hydrates hrefs lazily, so anchor hrefs are unreliable at DOM-load time) and builds canonical https://uk.indeed.com/viewjob?jk=<key> URLs.
Title "Indeed job @Indeed" also fixed — now reads aria-label from a[data-jk] anchor element.
Arkose FunCaptcha detection

captchaSolver.ts: added page.frames() scan before DOM scan — Arkose loads in cross-origin iframes that are invisible to page.evaluate().
CAPSOLVER_API_KEY added to .env.
LinkedIn PIN modal — isVisible() instant-false root cause found and fixed

isVisible({ timeout: 10000 }) was returning false in ~224ms. The reason: form input[type="text"] in the selector was immediately matching a hidden field on the checkpoint page. Playwright's isVisible() does not wait when it already has a match — it checks the matched element immediately and returns false.
Fix: replaced with filter({ visible: true }).first().waitFor({ state: "visible", timeout }) which actively polls until a visible input appears — completely bypasses the hidden-element false-positive.
Added input[type="tel"] (LinkedIn uses tel-type for numeric code entry on mobile-first forms); removed overbroad form input[type="text"].
Added [linkedin-inputs] diagnostic log that dumps all input attributes on the checkpoint page for debugging if still failing.
Frontend PIN modal (Connect.tsx) was already fully built — 6-slot InputOTP, LinkedIn-specific label text, POST /platforms/linkedin/pin submit. Nothing was missing on the UI side; the backend was just never emitting __PIN_REQUIRED__.
