Rizz Club
A dark, neon conversation toolkit with quick pickup lines, a playful soundboard, and an AI texting coach that reads the conversation before suggesting a reply.
Features
- Charcoal theme with electric lime, pink, and violet accents, including responsive mobile layouts.
- 18 quick starters on the home screen and chat welcome screen, with copy, save, and more-line actions.
- 100 curated pickup lines across five styles: smooth, funny, sweet, bold, and desi. Each style cycles through a shuffled deck before repeating.
- Wingman AI chat: paste a message or describe the exchange in your own words. Recent conversation history, chosen tone, language, and previous suggestions shape up to three distinct replies.
- Fresh angle, Shorter, and Smoother follow-ups that keep the conversation context.
- English, Hinglish, Hindi, or automatic language matching.
- Bookmarked lines stored on the current device and original, synthesized meme-style sounds.
The quick starters and line library work without an API key. Wingman uses a real server-side OpenAI integration and displays service errors instead of pretending a preset response came from AI. Suggestions encourage mutual interest and respect boundaries; they cannot guarantee a reply.
Tech stack
- HTML, CSS, and browser JavaScript for the main interface.
- TypeScript, React, Vinext, and Vite for the application framework.
- Cloudflare Workers runtime for server routes.
- OpenAI Responses API, called from the server with fetch.
- Node's built-in test runner for backend tests.
The complete Sites starter is retained, including reusable UI components and database/connector examples. Rizz Club does not currently use a database or those connector examples.
Run locally
Use Node.js 22.13.0 or newer and pnpm 11.25.0, as specified in package.json. Run commands from the project folder.
# If pnpm is not installed:
npm install --global pnpm@11.25.0

pnpm install --frozen-lockfile
pnpm dev
Open http://localhost:5173. Keep pnpm-lock.yaml and use pnpm for this project. The install:ci script is an additional managed Linux installer; it requires Bash, flock, and GNU timeout and is not the general Windows installation command.
Connect Wingman locally
1. Copy .dev.vars.example to .dev.vars in the project root. You can copy and rename it in your file manager; on macOS/Linux, run cp .dev.vars.example .dev.vars.
2. Set OPENAI_API_KEY in that local file to your own OpenAI API key. Keep the key out of browser JavaScript and GitHub.
3. Leave OPENAI_MODEL=gpt-6-luna, or choose a model available to your API project that supports the request options in lib/wingman.mjs.
4. Restart pnpm dev after changing the file.
5. Visit http://localhost:5173/signin-with-chatgpt?return_to=%2F%23wingman once to enable the starter's local test identity. Then send a message in Wingman.
The development sign-in is a loopback-only mock identity, not a real ChatGPT login. It is available during pnpm dev in a clean clone. The built Worker preview does not supply that mock sign-in. Production authentication must be supplied by the hosting platform or an authentication adapter.

Environment variables:
Variable	Purpose
OPENAI_API_KEY	Server-only API credential. Required for generated chat replies.
OPENAI_MODEL	Optional model override. Defaults to gpt-6-luna.


This export contains no API key. Hosted environment secrets are not part of the source repository and do not transfer with a ZIP. API access and available credits must be configured separately from signing into ChatGPT.
Commands
# Development server
pnpm dev

# Backend tests; no API key or paid API call required
node --test tests/wingman.test.mjs

# Type checking and linting after dependencies are installed
pnpm exec tsc --noEmit
pnpm lint

# Build, then preview the built Worker locally
pnpm build
pnpm start
pnpm start uses the generated dist/server/wrangler.json; it requires a successful build first. It previews the Worker locally and does not publish the website.
Main files
Path	What it contains
app/shell.html	Complete page structure.
app/route.ts	Serves the HTML shell.
public/style.css, public/dark.css	Layout, responsive styles, and dark neon theme.
public/openers.js	Curated line library and quick starters.
public/app.js	Navigation, copying, saving, soundboard, and chat interactions.
app/api/wingman/route.ts	Worker environment and authenticated user adapter.
lib/wingman.mjs	AI instructions, request/response validation, provider calls, and error handling.
tests/wingman.test.mjs	Eight mocked backend tests.
worker/index.ts, vite.config.ts	Worker entry point and build configuration.
.openai/hosting.json	Existing Sites project identity and binding configuration.
.dev.vars.example	Blank local environment template.
scripts/, components/, lib/, db/, examples/	Retained framework utilities, components, and examples.


To change the appearance, edit the CSS files. To change the curated lines, edit public/openers.js. To improve the AI's style, edit SYSTEM_PROMPT in lib/wingman.mjs and rerun the tests.
Upload to GitHub
1. Extract rizz-club.zip. The resulting rizz-club folder contains the entire source project, including this README.
2. Create an empty repository on GitHub. Do not initialize it with another README if you use the commands below.
3. Open a terminal in the extracted rizz-club folder and run:
git init
git add .
git status
git commit -m "Add Rizz Club website"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git
git push -u origin main
Replace YOUR_USERNAME and YOUR_REPOSITORY with your GitHub details. Check git status before committing: .dev.vars, real .env files, dependencies, and generated output should not be staged. The included .gitignore excludes them and allows the blank environment template.

The command-line method includes the required project configuration files that some graphical upload flows hide. No previous Git history or source-repository credentials are included in this export.
Hosting
Uploading code to GitHub does not deploy the website. GitHub Pages cannot run the /api/wingman server route or securely hold the API key, so the full application needs a server/Worker host.

The source retains the original Sites configuration. Its project ID is metadata, not an API key. Do not reuse that identity to publish an unrelated site: provision a new project with the appropriate hosting tools instead. Changes to this downloaded copy do not change the original hosted site.

When moving to another host, configure the Worker runtime and server secrets, and adapt authentication in app/api/wingman/route.ts. Production relies on a trusted gateway providing oai-authenticated-user-id; never treat a public caller-supplied identity header as verified authentication. Add durable usage limits before opening paid inference to a broad audience. The existing rate limiter is only a per-Worker-instance burst guard.
Privacy and operational limits
- Chat is held in page memory. Sending a message sends it and recent conversation context to OpenAI.
- The app does not persist chat on its server. Requests use store: false; this does not claim zero provider retention.
- Bookmarks remain in browser localStorage under rizzclub.saved.v1 and do not sync between devices.
- Each message is limited to 4,000 characters. The server accepts up to 16 supplied history messages within a 24,000-character history limit, and checks up to 36 earlier suggestions for repeats.
- Requests have a 35-second provider timeout. The in-memory burst guard permits 10 requests per minute per user per Worker instance; it is not an account-wide spending cap.
- GET /api/wingman reports whether a key is configured. It does not verify that the key works, the model is available, or the account has credit.
Troubleshooting
Problem	What to check
“Live AI is not connected yet”	Add the server key to .dev.vars locally or to the hosting environment, then restart/redeploy as appropriate.
“Sign in with ChatGPT” locally	Use the local sign-in URL above on the same localhost:5173 origin, in pnpm dev.
Provider usage limit	Check the API project's available credits and rate limits.
AI connection needs attention	Check the server API key and project/model access.
Full application fails on GitHub Pages	Use a server/Worker host; Pages only serves static content.


Verification and export notes
This package was exported from source commit 82b1b0fbfd0b8caec2086d20d18915f269775da9 on November 10, 2026. Application code, dependency versions, the lockfile, and third-party license notices are retained. Export-only changes are this README, an empty environment template, and its .gitignore exception.

The eight backend tests use mocked responses. The export validation runs these tests and JavaScript syntax checks without making paid API calls. A fresh dependency install, full build, and live model response are not verified as part of packaging. At the last earlier live check, generation was blocked by the API account's credit balance; that result does not establish the account's current status.
Licensing
No project-wide open-source license has been selected. Choose one before inviting public reuse. Retain the included license notices for vendored code and respect dependency licenses.
