# Testing CVNova

## Application Overview
CVNova is a monolithic client-side SPA (`index.html`, ~2500 lines) hosted on GitHub Pages. It uses Firebase (Auth + Firestore), Google Analytics 4, EmailJS, PayPal SDK, and html2pdf.js.

## Live URLs
- **Production (GitHub Pages):** https://chteamtemplates.github.io/cvgeneratorAI/
- **Vercel Preview (PR branches):** Check PR comments for preview URLs

## Key Testing Areas

### 1. Google Analytics
- Verify `gtag` function exists: run `typeof gtag === 'function'` in browser console
- Verify `window.dataLayer` is populated (should have 2+ entries after page load)
- Check Network tab for requests to `googletagmanager.com` with the correct Measurement ID
- Real-time data takes 24-48 hours to appear in Analytics dashboard

### 2. EmailJS
- Verify SDK loads: run `typeof emailjs` in browser console (should return `"object"`)
- The `sendWelcomeEmail()` function is called inside `doRegister()` after successful Firebase account creation
- To test email delivery end-to-end, you must register a new user account
- Check EmailJS dashboard (https://dashboard.emailjs.com) for delivery logs

### 3. i18n Language Switching
- Language buttons are in the nav bar: EN, FR, عربي
- When switching languages, verify:
  - Nav items change language
  - CTA buttons change language
  - Template "Unlock"/"Débloquer"/"فتح القفل" buttons change
  - Footer text changes
  - Arabic mode activates RTL layout
- Common bug: French text leaking into EN/AR mode. Check template gallery buttons especially.
- The `setLang()` function at ~line 1418 handles language switching
- `applyI18n()` updates all DOM elements with `data-i18n` attributes
- Template gallery is re-rendered via `renderTplGallery()` which uses template literals with `T().ui.*`

### 4. Firebase Auth
- Google Sign-In requires the domain to be added in Firebase Console → Authentication → Authorized domains
- iOS browsers use redirect flow instead of popup (see `isIOSBrowser` check)
- Registration flow: `doRegister()` → creates Firebase user → saves to Firestore → calls `sendWelcomeEmail()` → updates nav UI

### 5. Console Errors
- After page load, console should be clean (no red errors)
- Common issues to watch for:
  - `ReferenceError` from undefined variables
  - Firebase auth errors (check error codes)
  - EmailJS initialization errors (wrapped in try/catch, may be silent)

## Testing with Browser Console
Useful commands:
```js
// Check GA
typeof gtag === 'function'  // should be true
window.dataLayer.length     // should be 2+

// Check EmailJS
typeof emailjs              // should be 'object'

// Check current language
state.lang                  // 'en', 'fr', or 'ar'

// Check auth state
authState.user              // null if not logged in
```

## Devin Secrets Needed
No secrets needed for basic testing. The app's Firebase config and API keys are public (client-side).

## Notes
- The site defaults to French (FR) on first load
- PRs must be merged to main for changes to appear on GitHub Pages
- Vercel preview URLs are available for PR branches before merging
- Admin dashboard is accessible only to the admin email configured in the code
