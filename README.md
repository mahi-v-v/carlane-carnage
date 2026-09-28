# Carlane Carnage — by Bugbear

Multiplayer top-down merge-and-survive game. Static site, no backend to run.

## Deploy to Vercel
1. Push this folder to a GitHub repo, then "Add New → Project" in Vercel and import it
   (Framework preset: **Other**, no build command). Or run `npx vercel` in this folder.
2. Share the URL.

## How it plays
- The road never ends. Enter a name, get a seat in a room, join from the on-ramp, survive.
- **Leader**: whoever has the longest current run (over 3 s) is the leader. They get a small gold star on their car's roof
  and their name in the sign at the top. The goal is to take them down.
- **Blocking, no pushing**: cars are solid. You can't drive through another car, and nobody can shove you: a car that drives
  into you just stops at your bumper. So you win by getting in the way. If someone runs into you and then crashes within 2 s
  (pothole, traffic, barrier, or the on-ramp running out), the knockout (KO) is yours.
  Taking out the leader also earns a crown (★) on the leaderboard. Aggressive bots try to box the leader in too.
- **Pushing (host toggle)**: the room's host gets a "Pushing off / on" button at the bottom of the screen. With pushing on,
  ramming a car shoves it (you slow down a little while shoving), and if they crash within 2 s the KO is yours.
  The setting applies to everyone in the room, new rooms start with it off, and it carries over if the host changes.
- **Exit** (bottom of the screen) leaves the room and takes you back to the name screen.
- **Leaderboard** on the side: everyone's current run, best run, KOs and crowns. On phones it's behind the "Board" button.

## Rooms
- Rooms hold **16 players**. Players 1–16 share room 1; player 17 automatically opens room 2, and so on.
  When a seat frees up in room 1, the next newcomer takes it.
- There is no start button: the road is always running and people drop in and out.
- **Lanes** = max(3, cars in the room − 2), up to 14. So there are always fewer lanes than cars.
  The road widens or narrows a couple of seconds after someone joins or leaves, and the camera zooms out to fit.
- **Bots** fill small rooms: solo 4 bots, duo 3, trio 2, 4+ players none.
- Want a private set of rooms? Add a hash: `https://your-site.vercel.app/#friday`.
- Each room's host is one player's browser. If the host leaves, the longest-standing player takes over
  and everyone keeps driving (a short hiccup, no reset).
- Hosts should keep the tab open. Background tabs keep ticking, but a closed tab hands the room over.

## Under the hood
- Players connect peer-to-peer (WebRTC) using PeerJS (`public/peerjs.min.js`, v1.5.5, bundled).
- Matchmaking uses the free public PeerJS server (0.peerjs.com) and its TURN relays. That's what keeps this free and backend-free,
  but it isn't guaranteed uptime. To use your own PeerJS server, open the game with
  `?peer=your-host:443` (optional `&peerpath=/myapp`, `&peersecure=0` for plain http).
- Netcode: everyone shares the host's clock and the road is a pure function of it, so it scrolls identically on every
  screen. The host only sends new obstacles once; after that every screen computes them itself. Each player drives
  their own car locally (no input lag) and sends its position; other cars are drawn ~0.1 s in the past and smoothly
  interpolated, with the delay adapting to each connection.
- Tunables (room size, bots, lanes, speeds, block-KO timing) are in `CFG` at the top of the script in `public/index.html`.
