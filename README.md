Spotify Now Playing – OBS Overlay

A clean, single-file web overlay that shows your currently playing Spotify track — album art on the right, title/artist on the left, and a progress bar. Designed to drop straight into OBS as a Browser source, or run on any static site (GitHub Pages, Netlify, Squarespace, etc.).

Live URL example: https://9train.github.io/spotify-obs-overlay/
Replace with your Pages URL when you deploy.

Features

✅ Now Playing: song title, artist(s), elapsed / remaining time

🎨 Auto-themed: soft gradient matches the album art colors

🖼️ Layout: info on the left, album art locked on the right (rounded square)

🔒 No secrets required: uses Spotify’s OAuth PKCE (client ID only)

🧩 OBS-ready: transparent background, sized nicely for 1600×420

🧹 Smart UI: “Connect to Spotify” hides after you authenticate

Prerequisites

A Spotify account (Free or Premium is fine; this overlay only reads playback).

A Spotify Developer App (free).

Somewhere to host a static file (GitHub Pages recommended).

1) Create a Spotify App

Go to https://developer.spotify.com/dashboard
 and log in.

Create an App → give it any name (e.g., spotify-obs-overlay).

Open your app → Edit Settings → Redirect URIs → add your exact hosted URL (we’ll get this in step 3).

If using GitHub Pages project site, it will look like:
https://YOURUSERNAME.github.io/YOUR-REPO-NAME/ (include the trailing slash!)

You can add the Redirect URI now if you already know your final URL, or come back after deploying.

2) Configure the Overlay

Open index.html and set these constants near the top:

const CLIENT_ID = "YOUR_SPOTIFY_CLIENT_ID";              // from developer dashboard

const REDIRECT_URI = "https://YOURUSER.github.io/YOUR-REPO-NAME/"; // exact public URL, trailing slash

const SCOPES = ["user-read-currently-playing", "user-read-playback-state"];


Important:

CLIENT_ID must be in quotes.

REDIRECT_URI must exactly match the one saved in your Spotify app (same domain/path and trailing slash).

3) Deploy to GitHub Pages

Project site (recommended):

Create a public repo, e.g., spotify-obs-overlay.

Add index.html at the root of the repo.

Repo → Settings → Pages → Build and deployment → Source = Deploy from a branch.

Branch = main, Folder = / (root) → Save.

Wait ~1 minute. GitHub shows:

Your site is live at https://YOURUSERNAME.github.io/spotify-obs-overlay/

Now go back to your Spotify app’s Edit Settings → Redirect URIs and add that exact URL (with trailing slash), then Save.

Got a 404? Ensure the repo is public, index.html is at the root, and Pages is set to main + / (root).

4) Use It in OBS

Sources → + → Browser.

URL = your Pages URL (e.g., https://YOURUSER.github.io/spotify-obs-overlay/).

Width/Height: try 1600 × 420 (adjust to taste).

Enable Transparent background if you want it floating.

Click OK.

Right-click the Browser source → Interact → click Connect to Spotify and approve.

The overlay loads your track info and hides the connect button once authenticated.

If you update the file, right-click the Browser source → Properties → Refresh cache of current page to pull the latest code.

Local Development (optional)

If you want to test locally before pushing:

# from the folder that contains index.html

python3 -m http.server 5500


Open http://localhost:5500/ and temporarily set:

const REDIRECT_URI = "http://localhost:5500/"; // also add this exact URL in Spotify Redirect URIs


Switch back to your Pages URL for production.

Customization

Spacing between boxes: in .overlay, adjust column-gap: 16px;

Album art size: change .art-card { width/height: 340px; }

Keep layout on small sizes: media query adjusts to 260px art; tweak as needed.

Typography: .title, .subtitle, .time font sizes are easy to scale.

Gradient strength: edit setGradientFromImage() to alter opacity/lightening.

How It Works (in short)

Uses Spotify OAuth PKCE: safe in the browser (no client secret).

On “Connect”, the user logs in at Spotify and approves read-only scopes:

user-read-currently-playing

user-read-playback-state

Access token is stored in localStorage and refreshed with a refresh_token.

The page polls GET /v1/me/player/currently-playing every ~4s and animates the progress bar locally for smoothness.

Album art colors are sampled to create the background gradient.

Troubleshooting
INVALID_CLIENT: Invalid client

You pasted the Client Secret by mistake — use Client ID only.

Your REDIRECT_URI in code doesn’t exactly match the one in Spotify settings (domain, path, trailing slash).

Fix both to the same exact string and click Save in the dashboard.

GitHub Pages shows 404

Repo must be public.

index.html must be at repo root.

Settings → Pages → Source = Deploy from a branch, main, / (root).

Wait ~1 minute; then use the shown URL:

https://YOURUSER.github.io/YOUR-REPO-NAME/

Can’t click the button in OBS

Use Right-click → Interact to click web content in OBS.

Ensure nothing overlays the button (this repo sets z-index correctly).

After edits, Refresh cache of current page in OBS.

Button doesn’t hide after login

OBS may be caching an older file. Refresh the Browser source cache.

If you manually changed code, ensure setConnectedUI(true) runs after token exchange/refresh and after a successful fetch.

Album art drops under the info box

Ensure .overlay uses:

grid-template-columns: minmax(0, 1fr) 340px;


Ensure .info-card has min-width: 0; (not a fixed min width).

Avoid rules like .overlay { grid-auto-flow: row; } in media queries.

If you previously added random CSS inside other blocks, remove them.

I see recorder.js:48 [Recorder] consumeInfo...

That isn’t from this app. Remove any <script src="recorder.js"> tags and calls like Recorder.install() from your page/theme if present.

Privacy & Notes

This overlay stores tokens in the browser’s localStorage where it’s running (e.g., OBS’s embedded browser or your normal browser).

Client ID is not a secret; it’s fine to keep in your HTML.

This overlay does not stream music. You must route Spotify audio into OBS separately (Audio Output Capture on Windows, or a virtual device like BlackHole/Loopback on macOS).

Streaming commercial music may be restricted on Twitch/YouTube; check platform policies.

Project Structure
/
└─ index.html        # the whole app (HTML + CSS + JS)

FAQ
Can anyone use my link to see their own “Now Playing”?

Yes. Each visitor clicks Connect to Spotify and authorizes your app. They’ll see their playback.

Can I make one public link that always shows my track with no login?

Yes, but you’ll need a tiny backend that holds your refresh token and exposes a /now endpoint. The front end then polls your server. (Ask if you want a ready-to-deploy example.)

Do I need Spotify Premium?

No. The API endpoint we use is read-only and works with Free.

License

You can use and modify this overlay freely for your stream or site.

If you want a formal license, drop an MIT LICENSE file in the repo—happy to generate one for you.

Credits

Spotify Web API (OAuth PKCE + Player endpoints)

You, for the design direction ✨

Support / Questions

Open an issue or ask for help with your exact URL and a screenshot of any console error. I can also tailor a server-backed version if you’d like a global, no-login public overlay.
