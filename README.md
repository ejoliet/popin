# Popin

> Paste a link, friends pop in. Any URL becomes a temporary P2P room — watch, react, vote, decide. Room vanishes when the host tab closes. Zero backend.

## Overview

Sharing a link starts a conversation somewhere else (chat app, thread). Popin puts the conversation *on* the link, live, with no account and no server. The consumer wedge is viral sharing; the B2B play is an embeddable widget for publishers, ecommerce, edu, and internal docs.

## Architecture

- **Topology**: PeerJS star. Room creator's tab = hub + authority (roster, poll tallies). Same pattern as popclass.
- **Signaling**: public PeerJS cloud broker (`0.peerjs.com`). Data flows browser-to-browser after handshake.
- **Room lifecycle**: invite link = `#r=<hostPeerId>&u=<encodedURL>`. Host refresh mints a new peer id → old invites die by design.
- **Content**: iframe attempt + always-visible "open in tab" companion mode. YouTube `watch` URLs auto-rewritten to `/embed/`.
- **State**: single file, vanilla JS, no build step. `pi.*` localStorage namespace with in-memory fallback.

> ⚠️ **Framing constraint**: most major sites send `X-Frame-Options`/`frame-ancestors` and cannot be embedded. Denial is not reliably detectable cross-origin, so fallback is manual ("page won't load?" toggle). The room is fully useful in companion mode — this is the honest v1 shape without an extension.

## Protocol (host-authoritative)

| Direction | Messages |
|-----------|----------|
| guest → host | `hello{name,emoji}`, `chat{text}`, `react{e}`, `pollStart{q,opts}`, `vote{i}`, `pollEnd` |
| host → all | `roster`, `chat`, `react`, `poll{q,opts,votes}`, sys messages |

One vote per peer id, changeable. Host relays and re-renders its own broadcasts (single render path).

## Quick Start

1. Open `index.html` (or host on `gisthost.github.io` like the other tools).
2. Paste a URL, name yourself, copy the invite ticket.
3. Send the invite. Guests join in one click. Close your tab → room gone.

## Non-Goals (v1)

- Synced video playback (needs per-provider player APIs)
- Overlay annotation on arbitrary sites (needs browser extension)
- Persistence, accounts, history
- Full mesh (star only, host = hub)

## Open Questions

- [ ] TURN relay for symmetric-NAT peers — public broker gives STUN only; some corp networks will fail
- [ ] Host handoff on disconnect (elect oldest guest?) vs. keep ephemeral purity
- [ ] B2B embed: `<script>` snippet that mounts the panel beside publisher content (this dodges the iframe problem entirely — strongest wedge)

## Next Steps

1. Spike: 3-device NAT test (home wifi + LTE + corp VPN) — measure connect success rate
2. If NAT failures > ~15%, evaluate free TURN (Open Relay) before any launch
3. B2B embed spike: mount panel as sidebar on a page you control — validates the real revenue shape
