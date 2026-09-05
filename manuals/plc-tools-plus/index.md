---
layout: blank
title: PLC Tools Plus:Manual
---

<!--
  Published as manuals/plc-tools-plus/index.md in the serafintech.github.io repo
  (SerafinTech/SerafinTech.github.io), alongside privacy/plc-tools-plus/index.md
  and support/plc-tools-plus/index.md. That repo is a single Jekyll site rooted
  at its own _config.yml (theme: jekyll-theme-minimal) - there's no per-page
  _config.yml/Gemfile/theme override support, and "layout: blank" (matching
  those sibling pages) is what renders this as a bare, unstyled fragment with
  no site chrome, which is why the styling below is inlined directly rather
  than done via a separate Sass asset importing another theme (that approach
  previously broke the live build with "File to import not found: minima",
  since the site's real theme has no such partial to import).
-->

<style>
  body {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
    color: #333;
    max-width: 760px;
    margin: 0 auto;
    padding: 32px 20px 64px;
    line-height: 1.6;
  }
  h1 { font-size: 28px; font-weight: 800; margin-top: 0; color: #222; }
  h2 { font-size: 20px; font-weight: 700; margin-top: 2.2em; padding-bottom: 6px; border-bottom: 2px solid #E6F4FE; color: #222; }
  h3 { font-size: 16px; font-weight: 700; color: #222; }
  a { color: #2196F3; text-decoration: none; }
  a:hover { color: #1769aa; text-decoration: underline; }
  code { background-color: #f2f2f2; border: 1px solid #ddd; border-radius: 6px; padding: 2px 6px; font-size: 0.9em; }
  pre { background-color: #f2f2f2; border: 1px solid #ddd; border-radius: 8px; padding: 12px 14px; overflow-x: auto; }
  pre code { background: none; border: none; padding: 0; }
  blockquote { border-left: 4px solid #2196F3; background-color: #E6F4FE; color: #444; border-radius: 0 8px 8px 0; padding: 10px 16px; margin-left: 0; }
  table { border-collapse: collapse; width: 100%; margin: 1em 0; }
  th, td { border: 1px solid #ddd; padding: 8px 10px; text-align: left; }
  th { background-color: #f2f2f2; }
  hr { border: none; border-top: 1px solid #ddd; margin: 2em 0; }
</style>

# PLC Tools Plus — User Manual

This is the reference manual for **PLC Tools Plus**, a mobile EtherNet/IP diagnostic
tool for Allen‑Bradley CompactLogix/ControlLogix controllers. It covers every screen
in the app plus the nuances and technical trade-offs behind them — the kind of detail
that matters once you're troubleshooting on a live network rather than just tapping
around.

*PLC Tools Plus is an independent, third-party application and is not affiliated
with, sponsored by, or endorsed by Rockwell Automation — see
[Trademarks](#trademarks).*

If you're looking for a quick start, read [Getting Started](#getting-started) and
[The Devices Tab](#the-devices-tab) and you'll be discovering controllers in a minute.
Everything after that is reference material for when you need it.

## Table of Contents

1. [Overview](#overview)
2. [Getting Started](#getting-started)
3. [The Devices Tab](#the-devices-tab)
4. [The Device Detail Screen](#the-device-detail-screen)
5. [The Tag List Tab](#the-tag-list-tab)
6. [The Data Monitor Tab](#the-data-monitor-tab)
7. [The IO Tab](#the-io-tab)
8. [IO Scanning — Connecting to a Real Device's IO](#io-scanning--connecting-to-a-real-devices-io)
9. [The Simulated IO Device](#the-simulated-io-device)
10. [Network & Interface Selection](#network--interface-selection)
11. [The Demo PLC](#the-demo-plc)
12. [Subscription & Pro Features](#subscription--pro-features)
13. [Troubleshooting](#troubleshooting)
14. [Glossary](#glossary)
15. [Trademarks](#trademarks)

---

## Overview

PLC Tools Plus scans your local network for EtherNet/IP devices, lets you browse a
controller's tag list, watch live tag values update in real time, write values back,
and — going further than a typical browsing tool — act as an **IO Scanner**
(originator) that opens real Class 1 I/O connections to a device's assemblies, or as a
**Simulated IO Device** (target/adapter) that answers those same connections itself,
for testing without a second physical controller. No programming software required.

The app has four tabs: **Home**, **Devices**, **Data Monitor**, and **IO**.

## Getting Started

The **Home** tab is the app's landing page. It links out to the three main tabs and
has one setting worth knowing about immediately:

- **Demo PLC** — a toggle that adds a fully simulated controller ("Demo PLC") to the
  Devices tab, with no real hardware required. It exposes a realistic mix of tags
  (bools, integers, reals, an array, a UDT, and a program-scoped tag) so you can try
  tag browsing and live monitoring immediately. See [The Demo PLC](#the-demo-plc) for
  exactly what it contains and what it does *not* support.

## The Devices Tab

The Devices tab (internally called the **Browser**) sends EtherNet/IP `ListIdentity`
broadcasts on your network and lists every device that answers, showing its product
name, product code, and revision.

- **Interface** — the button at the top of the screen shows which local network
  interface the scan binds to and lets you change it. See
  [Network & Interface Selection](#network--interface-selection) for what your choices
  actually mean.
- **Scan for Devices** — runs a 15-second broadcast scan. Devices that respond are
  added to the list live as they're found; tap one to open its
  [Device Detail Screen](#the-device-detail-screen).
- If scanning fails outright (for example, the selected interface no longer exists),
  the scan stops and shows **"Network Error"** instead of hanging indefinitely.

## The Device Detail Screen

Tapping a device from the Browser opens its detail screen, which offers:

- **Read Device Properties** — reads and displays the controller's identity: vendor
  ID, device type, product code, name, serial number, slot, firmware revision, and
  status flags (Run/Program mode, minor/major recoverable and unrecoverable faults, IO
  faulted).
- **Slot** and **Update Rate** — editable fields that control which backplane slot is
  addressed and how often subscribed tag values are re-read (used by Live Update on
  the Tag List tab).
- **Read Tag List** — reads the controller's tag list (only shown for controllers that
  support it) and opens the [Tag List tab](#the-tag-list-tab) for that device.
- **IO Connect Setup** — opens a form to configure and open a Class 1 I/O connection
  to this device (Config/Input/Output assembly numbers and sizes, data type, and RPI).
  See [IO Scanning](#io-scanning--connecting-to-a-real-devices-io). This button is
  hidden if the device's IP is actually one of your own device's network interfaces
  (you can't IO-connect to yourself), if the device doesn't advertise Class 1 I/O
  support (checked via a quick one-off ListServices query when this screen loads),
  and is Pro-gated.
- If an IO connection already exists for this device, the screen instead shows its
  live status (Connecting/Connected/error) with **Disconnect**, **Remove**, and
  **Go To IO Connections** actions.

## The Tag List Tab

Reached via **Read Tag List** on a device, this tab lists every tag the controller
reported, including structures (UDTs) and arrays — tap into either to drill down to
their members/elements. Browsing the list itself — tag names, types, and structure —
is free for any device.

- **Live Update** (top-right toggle) subscribes to visible, readable tags and is what
  actually populates their values at the controller's configured **Update Rate** —
  without it, a real device's tags show no value at all. Toggling it on is a **Pro
  feature** and prompts the subscription paywall if you aren't already Pro.
- **Writing values** on a real device is its own **Pro feature** — tapping **Write
  Value** prompts the subscription paywall directly if you aren't already Pro, the same
  as toggling Live Update does. Once you're Pro, the write still needs the tag to
  already be actively subscribed (Live Update on, with its value showing) or it fails
  with **"Tag is not available to write."** Turn on Live Update first, then tap a
  writable tag to open the write modal (boolean tags get a True/False selector; numeric
  tags get a validated text field).
- **The Demo PLC is exempt** from all of the above — viewing values, writing, and Live
  Update are always free there, so you can try the full flow before subscribing.

## The Data Monitor Tab

Data Monitor is a cross-device watch list: pin any readable tag from any controller's
Tag List (via its "+" button) and it shows up here, grouped by device, regardless of
which controller you currently have open. Pinning a tag is itself always free.

- Tags update live the same way Tag List's Live Update does, and are gated by the same
  Pro check — with the same Demo PLC exemption, checked per-tag (so a mix of real and
  Demo PLC tags in the same list works correctly, only the real-device tags are
  gated).
- **Writing values** from Data Monitor works the same way as Tag List — free for the
  Demo PLC; for a real device it prompts the Pro paywall directly if you aren't
  subscribed yet, and still requires the tag to already be actively live (Live Update
  on) before the write itself succeeds.

## The IO Tab

The IO tab is dual-purpose, and it's worth understanding the distinction up front:

- **IO Scanner** (originator role) — the app connects *out* to a real device's
  Config/Input/Output assemblies, the same role a PLC or scanner card plays.
- **Simulated IO Device** (target/adapter role) — the app *answers* those same kinds
  of connections, acting as if it were the field device, for testing your own scanning
  logic (or another instance of this app) without a second physical controller.

Both roles are configured and monitored from the IO tab; the Simulated Device also has
its own dedicated screen reachable from the top of this tab.

## IO Scanning — Connecting to a Real Device's IO

Set up via **IO Connect Setup** on a device's detail screen. The form asks for:

- **Config / Input / Output** — each needs an **Assembly** instance number and a
  **Size** in bytes (0–505 bytes each).
- **Data Type** — how raw bytes are interpreted when you drill into individual
  elements (e.g. `SINT`, `INT`, `DINT`, `REAL`).
- **RPI** (Requested Packet Interval) — how often, in milliseconds, data is exchanged
  (1–100,000ms; 20ms is a reasonable default for most devices).

Once connected, the IO tab shows the connection with its live status, and lets you
drill into **Input**, **Output**, and **Config** byte-by-byte or bit-by-bit:

- **Output** values are writable (you're the originator, so you control what's sent to
  the device).
- **Input** values are read-only and refresh at a rate that tracks the connection's own
  RPI — polling faster than the device can actually produce new data would be
  pointless, so the UI doesn't try to.
- **Shortcuts** — tap the **+** button on any byte or bit row to pin it for quick
  access from the connection's own screen, without drilling back down every time.
- A connection you've configured is remembered even after disconnecting, so you don't
  have to re-enter assembly numbers next time — it's listed with a **Connect** button
  instead. Connections marked active also reconnect automatically the next time the
  app launches.
- **Editing** a connection's settings is allowed even while it's connected — saving
  reconnects it with the new settings, since assembly/size/RPI changes need a full
  disconnect-and-reconnect either way. The first time you edit a connected connection,
  a one-time warning explains that saving will briefly interrupt communications; it
  won't show again after that (see [The Simulated IO Device](#the-simulated-io-device),
  which shares the same warning).
- Starting an IO connection (both the initial setup and reconnecting) is a **Pro
  feature**.
- If your Pro entitlement lapses while a connection is active (subscription expires,
  is refunded, etc.), the app disconnects it automatically the next time it re-checks
  your entitlement — at launch or when returning to the foreground — rather than
  leaving it running unpaid.

## The Simulated IO Device

Reached from the top of the IO tab, this turns your phone/tablet into a simulated
EtherNet/IP target device that any real scanner (or this app's own IO Scanner) can
connect to.

- **Identity** — configurable Vendor ID, Device Type, Product Code, Serial Number,
  Major/Minor Revision, and Product Name, all reported to whatever connects to it.
- **Config / Input / Output assemblies** — configurable instance numbers and sizes,
  same as the real-device side. Note the roles are inverted from IO Scanning: this
  device *produces* Input data (readable/writable here) and *receives* Output data
  from whoever connects to it.
- **Data Type** — same purpose as on the scanner side, for interpreting bytes when you
  drill in.
- **Network** — which local interface the device's TCP explicit-messaging port and
  its identity/broadcast-discovery responder bind to. This does **not** scope the
  actual Class 1 I/O data socket — see
  [Network & Interface Selection](#network--interface-selection) for why, and what
  trade-off picking a specific interface actually buys you. The first time you attempt
  to change this, a one-time warning explains that moving off "All interfaces"
  (0.0.0.0) can make the device undiscoverable outside that one interface - it won't
  show again after that.
- **Editing** is allowed even while the device is running — saving restarts it with the
  new settings (briefly dropping any active connections). The first time you edit a
  running device, a one-time warning explains that saving will briefly interrupt
  communications; it won't show again after that (shared with the same warning on
  editing a connected IO connection, above).
- Once started, the screen shows live status (**Running**, with a connected-device
  count) and, once a connected originator has actually sent its first packet of I/O
  data, a **Connected From** line showing that device's IP address.
- **Shortcuts** work the same way as on the IO Scanner side — pin any byte/bit for
  quick access.
- Starting the simulated device is a **Pro feature**, and it's also **stopped
  automatically** if your Pro entitlement lapses while it's running — re-checked at
  app launch and whenever the app returns to the foreground, not just when you first
  start it.
- **Stale connections are cleaned up automatically.** If a connected originator goes
  silent (crashes, network drops, its own app is killed) without a clean disconnect,
  the simulated device notices after roughly 4 missed RPI intervals and drops that
  connection on its own, rather than holding it open forever.

## Network & Interface Selection

The Devices tab's scan and the Simulated Device's Network setting share the same
interface picker, and both come with the same real trade-offs, explained in full here.
The Devices tab's picker has a small **?** button for a short in-context reminder; the
Simulated Device's shows a one-time warning instead the first time you attempt to
change it (see [The Simulated IO Device](#the-simulated-io-device)), since that's the
one place where losing discoverability actually defeats the setting's purpose.

- **All interfaces (0.0.0.0)** — the default. On Android this genuinely listens on
  every network interface. **On iOS, binding to `0.0.0.0` only actually listens on
  whichever interface the OS considers primary** — a platform limitation, not a bug in
  this app.
- **A specific interface** — restricts the socket to exactly that one IP. The
  trade-off: broadcast packets (like `ListIdentity` discovery requests) are addressed
  to the broadcast address, not to your specific IP, so the OS won't deliver them to a
  socket bound that narrowly. Pick a specific interface when you need to control
  *which* network a connection goes out on (e.g. your device has both Wi-Fi and
  cellular active); leave it on "All interfaces" if you want to be discoverable by
  broadcast scans. Only interfaces this device actually has are offered - there's no
  freeform IP entry.
- **Why the Simulated Device's Class 1 I/O socket is always on all interfaces,
  regardless of the Network setting:** the IO Scanner and the Simulated Device can both
  be active on the same device at the same time, and they must share the exact same
  well-known UDP port for I/O data (real EtherNet/IP peers always target that port by
  convention — it can't be changed to avoid the collision). A wildcard-bound socket and
  a specific-address-bound socket can't both usefully listen on the same port — only
  one of them would ever actually receive a given packet, and which one is
  platform-defined. To avoid that silent failure mode, this one socket always stays
  wildcard-bound and is shared between both roles. The Network setting still fully
  controls the TCP explicit-messaging port and the identity/broadcast responder, which
  don't have this constraint.

## The Demo PLC

Enabled from the Home tab, the Demo PLC is a fully simulated controller (no network
traffic at all) that shows up as **"Demo PLC"** in the Devices tab. It exists so you
can evaluate tag browsing and Live Update before connecting to real hardware, and its
Live Update is free (exempt from the Pro paywall) for exactly that reason.

It exposes a representative mix of tag types: booleans, a counter, real-number tags
(including a slowly-changing simulated tank level and a speed setpoint/actual pair), a
UDINT, a SINT, an INT, a LINT, a string, integer and real arrays, a UDT (with an array
of the same UDT), and a program-scoped tag — enough to exercise every tag-rendering
path in the app.

**The Demo PLC does not support IO Connect or the IO tab** — it's a Tag
List/Data Monitor-only stand-in, not a simulated target device (use the
[Simulated IO Device](#the-simulated-io-device) for that).

## Subscription & Pro Features

PLC Tools Plus is free to download and use for discovery and browsing. A subscription
("Pro") unlocks everything that involves an actual live value — viewing it, writing
it, or exchanging it continuously:

**Free, no subscription required:**
- Discovering devices (Devices tab / Browser)
- Reading device properties and the tag list
- Browsing tags, structures, and arrays (names and types, not live values)
- Pinning tags to Data Monitor
- The entire Demo PLC, including viewing values, writing values, and Live Update

**Requires Pro:**
- Viewing tag values on real devices (Live Update), in both Tag List and Data Monitor
- Writing tag values on real devices, in both Tag List and Data Monitor — gated
  independently of Live Update, so tapping Write prompts the paywall even if Live
  Update was never turned on
- Setting up, starting, and reconnecting an IO Scanner connection to a real device
- Starting the Simulated IO Device

These aren't just gated at the moment you start them — the app re-verifies your
entitlement at launch and whenever it returns to the foreground, and stops the
Simulated IO Device and any active IO connections automatically if your subscription
has lapsed in the meantime.

A free trial is available on first subscribing (see the paywall for current terms and
pricing, which are managed through the App Store/Play Store). If you've subscribed on
another device or reinstalled the app, use **Restore Purchases** on the Home tab to
recover your entitlement.

## Troubleshooting

- **"Tag is not available to write"** — the tag you're trying to write to isn't
  currently subscribed. If you're not Pro on a real device, tapping Write shows the
  subscription paywall instead of this message; if you are Pro (or on the Demo PLC)
  and still see it, Live Update is off — turn it on and wait for the tag's value to
  appear before writing.
- **"Network Error" while scanning for devices** — the scan's socket failed, most
  often because the selected interface no longer exists (Wi-Fi was turned off or
  switched networks). Reopen the interface picker and re-select or re-detect an
  interface, then scan again.
- **"Failed to start IO scanner: …"** shown on the IO tab — the IO Scanner's socket
  failed to bind, typically because the port is already in use elsewhere.
- **A real scanner or PLC can't discover my Simulated Device** — check the Simulated
  Device's Network setting. If it's bound to a specific interface instead of "All
  interfaces," broadcast `ListIdentity` discovery won't reach it (see
  [Network & Interface Selection](#network--interface-selection)); it will still
  accept a direct/unicast connection to that specific IP.
  Also confirm you're on the same subnet (Wi-Fi, not cellular) as the device
  scanning for it, and that Local Network access is granted to the app in system
  settings on iOS.
- **An IO connection shows "Connecting…" indefinitely on first setup** — the initial
  connection attempt failed (unreachable IP, rejected ForwardOpen, timeout) and the
  app has stopped retrying to avoid looping forever with nothing to show you; check the
  assembly numbers/sizes against the target device and reconnect. A connection that
  *was* working and then drops keeps retrying automatically, since that's normal
  recovery from a transient network blip.
- **A Simulated Device connection disappeared on its own** — this is expected
  behavior, not a bug, if the other side went quiet for a while; see
  [The Simulated IO Device](#the-simulated-io-device)'s note on automatic stale
  connection cleanup.
- **The Simulated Device or an IO connection stopped on its own, with no disconnect
  from either side** — your Pro entitlement lapsed (subscription expired, was
  refunded, etc.). The app checks entitlement at launch and whenever it returns to the
  foreground, and stops both features immediately if you're no longer Pro. Resubscribe,
  or use **Restore Purchases** on the Home tab if you believe this is a mistake, then
  start it again.

## Glossary

- **CIP (Common Industrial Protocol)** — the application-layer protocol EtherNet/IP
  carries; covers both explicit messaging and Class 1 I/O.
- **Originator** — the device that initiates a Class 1 I/O connection (a scanner or
  PLC). PLC Tools Plus plays this role in [IO Scanning](#io-scanning--connecting-to-a-real-devices-io).
- **Target / Adapter** — the device that accepts and answers a Class 1 I/O connection
  (a field device). PLC Tools Plus plays this role in the
  [Simulated IO Device](#the-simulated-io-device).
- **Assembly** — a named, sized block of I/O data (Config, Input, or Output) exchanged
  over a Class 1 connection.
- **ForwardOpen / ForwardClose** — the CIP services used to open and close a Class 1
  I/O connection.
- **RPI (Requested Packet Interval)** — the interval, in milliseconds, at which Class 1
  I/O data is exchanged on an open connection.
- **Explicit Messaging** — request/response CIP messaging over TCP (used for reading
  device properties, tag lists, and tag values), as opposed to the continuous Class 1
  I/O data exchange.
- **ListIdentity** — the EtherNet/IP broadcast request used to discover devices on the
  network (what the Devices tab's scan sends).
- **UDT (User-Defined Type)** — a structured tag type made of other tags/types, as
  defined in the controller's own program.

## Trademarks

Allen-Bradley, CompactLogix, ControlLogix, and Rockwell Automation are trademarks of
Rockwell Automation, Inc. EtherNet/IP and CIP (Common Industrial Protocol) are
trademarks of ODVA, Inc. Any other product or company names mentioned in this manual
are the property of their respective owners.

References to these names throughout this manual are solely to describe device and
protocol compatibility. **PLC Tools Plus is an independent, third-party application
and is not affiliated with, sponsored by, or endorsed by Rockwell Automation, ODVA, or
any controller manufacturer.**
