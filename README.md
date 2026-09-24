# Carlane Carnage — by Bugbear

Multiplayer top-down merge-and-survive game. Static site, no backend to run.

## Deploy to Vercel
1. Push this folder to a GitHub repo, then "Add New → Project" in Vercel and import it
   (Framework preset: **Other**, no build command). Or run `npx vercel` in this folder.
2. Share the URL.

## How the room works
- Everyone who opens the link lands in the same waiting lobby after entering a name.
- The first person in is the **host** and gets the **Start** button. People who arrive after the start go straight to "Join the road".
- Want a private room? Add a hash: `https://your-site.vercel.app/#friday`.
- The host's browser runs the game; everyone else streams it. If the host closes the page the game ends and the next person to rejoin becomes the new host.
- The host should keep the tab in the foreground: browsers slow down background tabs.

## Under the hood
- Players connect peer-to-peer (WebRTC) using PeerJS (`public/peerjs.min.js`, v1.5.5, bundled).
- Matchmaking uses the free public PeerJS server (0.peerjs.com) and its TURN relays. That's what keeps this free and backend-free,
  but it isn't guaranteed uptime. If it ever gets flaky, host your own PeerJS server and pass `{host, port, path}` to `new Peer(...)` in `index.html`.
- Tunables (bot count, speed, lanes) are in `CFG` at the top of the script in `public/index.html`.
