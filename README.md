# Online Intercom

A two-device intercom that runs entirely in the browser — one device stays
**indoors**, one goes **outdoors**. No app store, no backend server to run
and pay for, no accounts. Audio travels directly between the two devices
over WebRTC; only the initial "handshake" passes through a public signaling
broker (PeerJS Cloud), which never sees or touches your audio.

## How it works

- Each device opens the same page and picks a role: **Indoor** or **Outdoor**.
- Both devices enter the same **pairing code** (make one up, or tap
  "Generate a code" on one device and type it into the other).
- The outdoor unit's button rings the indoor unit; the indoor unit shows an
  incoming-call screen with **Answer** / **Ignore**. Either device can also
  be the one to initiate — whichever taps the button first calls the other.
- Once connected, audio is full-duplex (like a real intercom) with a mute
  button, a live input-level meter, and a hang-up button.
- Settings are remembered per device (`localStorage`), so after the first
  setup each unit just reopens straight into its role.
- Installable as a home-screen app (PWA) on both phones/tablets, so it opens
  full-screen without browser chrome.

## 1. Put it on GitHub

1. Create a new repository, e.g. `portable-intercom`.
2. Add the three files from this folder to the repo root:
   - `index.html`
   - `manifest.json`
   - `sw.js`
3. Commit and push.

## 2. Turn on GitHub Pages

1. In the repo, go to **Settings → Pages**.
2. Under **Build and deployment**, set **Source** to `Deploy from a branch`.
3. Pick your default branch (e.g. `main`) and `/ (root)`, then **Save**.
4. GitHub will give you a URL like `https://yourname.github.io/portable-intercom/`.
   This step matters: WebRTC's microphone access requires HTTPS, and GitHub
   Pages serves HTTPS by default.

## 3. Set up the two devices

- On the device that will live **indoors**, open the GitHub Pages URL,
  choose **Indoor**, enter a pairing code, and tap **Activate unit**.
- On the device that will live **outdoors** (a spare phone in a weatherproof
  case works well), open the same URL, choose **Outdoor**, enter the *same*
  code, and tap **Activate unit**.
- Optional: on each device, use the browser's "Add to Home Screen" /
  "Install app" option so it launches like a native app.
- Grant microphone permission when prompted — needed on both ends.

## Notes and limits

- **Network needed on both ends.** This isn't a walkie-talkie — both units
  need Wi-Fi or cellular data. It's built for "outdoor unit near the house
  on your own Wi-Fi," not for out-of-signal-range use.
- **Signaling only, not audio, goes through a third party.** By default this
  uses PeerJS's free public cloud server just to help the two devices find
  each other; the audio stream itself is peer-to-peer. For heavier/production
  use, you can run your own free PeerServer (see the [PeerJS docs](https://peerjs.com))
  and point `new Peer(...)` at it instead.
- **Strict NATs / corporate networks:** WebRTC uses STUN to punch through
  most home routers. A small number of restrictive networks need a TURN
  relay server to connect at all — if a pairing consistently shows "online"
  but never connects, that's usually why. Adding a TURN server (e.g. from
  Twilio, Cloudflare, or Metered) to the `Peer` config in `index.html` fixes
  this.
- **One pair per code.** Two devices sharing a pairing code form one pair;
  reuse a different code if you want a second, separate pair.

## Customizing

Everything is in `index.html` — a single self-contained page (no build step).
Colors, wording, and the ring pattern are near the top of the `<style>` block
and in the small script at the bottom if you want to retheme it or change
behavior (e.g. push-to-talk instead of full-duplex).