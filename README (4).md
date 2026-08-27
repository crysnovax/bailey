<p align="center">
  <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=700&size=32&duration=3000&pause=800&color=10B981&center=true&vCenter=true&width=650&lines=%F0%9F%9A%80+%40crysnovax%2Fbaileys%3B+WhatsApp+API+for+Node.js%3B+Rich+Messages+%7C+Meta+AI+Style%3B+Production+Ready+%E2%9C%85" alt="Typing Animation" />
</p>

<p align="center">
  <img src="https://img.shields.io/npm/v/@crysnovax/baileys?style=for-the-badge&logo=npm&color=10b981&labelColor=0a0f0a" />
  <img src="https://img.shields.io/github/stars/crysnovax/baileys?style=for-the-badge&logo=github&color=10b981&labelColor=0a0f0a" />
  <img src="https://img.shields.io/badge/License-Crysnovax-10b981?style=for-the-badge&labelColor=0a0f0a" />
  <img src="https://img.shields.io/badge/Node.js-%3E%3D18-10b981?style=for-the-badge&logo=node.js&labelColor=0a0f0a" />
</p>

> **License & attribution** — `@crysnovax/baileys` is distributed under the **Crysnovax Source License** (see [`LICENSE`](LICENSE)), not MIT. The package verifies its identity at install time, at runtime, and remotely (`bailey.crysnovax.link`): rebranded or revoked copies refuse to install, print visible warnings, and lose every premium feature added beyond plain messaging (rich messages, Meta AI compositing, welcome flow, rich preview, verified badge, GIF playback, albums, carousels, payments, and more) while plain text/media messaging keeps working. Removing attribution — including via AI-assisted rewrites — is prohibited and may be pursued under copyright law, DMCA takedowns, and npm/GitHub abuse reports. See [Condition of Use](#condition-of-use).


<br />

<div align="center">
  <img src="https://user-images.githubusercontent.com/74038190/212284100-561aa473-3905-4a80-b561-0d28506553ee.gif" width="700">
</div>

<br />

<div align="center">
  <a href="https://cdn.crysnovax.link/files/1783556231432-0ba114c8-87b7-4a85-9476-2872943e68f5.mp4">
    <img src="./assets/baileys-demo.gif" width="700" alt="Baileys live demonstration" />
  </a>
  <br />
  <em>˗ˏˋ ☏ ˎˊ˗ + ☕︎ + ⚉ + ✆ + ☻ — ⩇⩇:⩇⩇</em>
</div>


<br />

## Table of Contents

| # | Section | Description |
|---|---------|-------------|
| 1 | [Overview](#overview) | What this package is and what sets it apart |
| 2 | [Installation](#installation) | NPM, GitHub, ESM & CJS imports |
| 3 | [Quick Start](#quick-start) | Minimal working connection example |
| 4 | [Authentication](#authentication) | Auth states, pairing codes, SQLite |
| 5 | [Sending Messages](#sending-messages) | Text, mentions, reactions, contacts, locations, events, polls, forward, edit |
| 6 | [Media Messages](#media-messages) | Images, videos, audio, documents, stickers, albums |
| 7 | [Interactive Messages](#interactive-messages) | Buttons, lists, native flows, carousels, hydrated templates |
| 8 | [Rich Content](#rich-content) | Code blocks, tables, inline entities, rich response arrays |
| 9 | [Meta AI Features](#meta-ai-features) | Meta typing, meta compositing, replay planning |
| 10 | [Welcome Flow](#welcome-flow) | Auto-greet new contacts with FAQ menus |
| 11 | [Payment Messages](#payment-messages) | Invites, invoices, orders, payment requests |
| 12 | [Message Options](#message-options) | AI icon, ephemeral, spoilers, view once, raw, and more |
| 13 | [Status & Stories](#status--stories) | Post status updates with text, media, mentions |
| 14 | [Favorites](#favorites) | Manage WhatsApp Favorites list |
| 15 | [Newsletter Management](#newsletter-management) | Create, update, follow, react, delete newsletters |
| 16 | [Group Management](#group-management) | Create, settings, participants, invites, metadata |
| 17 | [Community Management](#community-management) | Communities, subgroups, linked groups |
| 18 | [Profile & Business](#profile--business) | Profile pictures, business catalog, products, quick replies |
| 19 | [Privacy & Security](#privacy--security) | Last seen, online, status, read receipts, calls |
| 20 | [Utility Methods](#utility-methods) | LID mapping, dirty bits, app state sync, props |
| 21 | [AI Groups](#ai-groups) | Tag groups as Meta AI groups |
| 22 | [WA-Web Chat Features](#wa-web-chat-features) | Status privacy, AI bots, chat blocking, push config, companions |
| 23 | [Interop, Username & Account Layers](#interop-username--account-layers) | Matrix interop, usernames, registration, cache-manager auth |
| 24 | [Events Reference](#events-reference) | Full list of socket event listeners |
| 25 | [Image Processing](#image-processing) | Sharp, NAPI-RS, Jimp auto-detection |
| 26 | [Condition of Use](#condition-of-use) | License, channel follow agreement & attribution enforcement |
| 27 | [Credits](#credits) | Attribution and enhancements |

---

## Overview

`@crysnovax/baileys` is a powerful, production-ready WhatsApp API wrapper for Node.js, built on top of the Baileys protocol. It extends the core with rich messaging capabilities, Meta AI-style compositing, and a streamlined developer experience.

### What Sets It Apart

| Feature | Status | Notes |
|---------|--------|-------|
| Rich Messages | ✅ | Code blocks, tables, inline entities, carousel cards |
| AI Groups | ✅ | Create, manage, add Meta AI, and resolve complete AI responses |
| Meta AI Style | ✅ | Live thinking indicators with a universal client-safe fallback |
| Safe Socket Core | ✅ | No antiban wrapper, rate limiter, warm-up blocker, or hidden send interception |
| Welcome Flow | ✅ | Auto-greet new contacts with interactive FAQ menus |
| Status Posting | ✅ | Text with colors/fonts, image, video, audio, mentions |
| Favorites Management | ✅ | Add/remove JIDs with local persistence |
| HD Profile Picture | ✅ | Full-size upload with no crop or resize via `{ hd: true }` (max 720px) |
| Rich Preview | 🆕 | Auto-fetch link preview (title, description, image) for any URL via `{ richPreview: true }` |
| Verified Badge | 🆕 | Image/video forward badge via `{ verifiedMe: true }` |
| Like This | 🆕 | Raw relay bypassing all processing via `{ likeThis: true }` |
| GIF Playback | 🆕 | Send videos as GIFs with `{ gifPlayback: true }` |
| LID/PN Resolution | ✅ | Cross-resolve LID and phone number JIDs |
| Member Labels | ✅ | Group participant labels |
| No Obfuscation | ✅ | Fully readable, auditable source code |
| Attribution Protected | ✅ | Install-time + runtime integrity checks against rebranding |
| Newsletter Ready | ✅ | Full media support, quoting, quiz polls |
| Image Processing | ✅ | Auto-detects `sharp`, `@napi-rs/image`, or `jimp` |
| Safe FFmpeg | ✅ | Uses `spawn` instead of `exec` |
| In-Memory Store | ✅ | Reintroduced with minimal ESM adaptation |

---

## Installation

```bash
# NPM
npm install @crysnovax/baileys

# GitHub
npm install github:crysnovax/baileys
```

Import (ESM & CJS)

```javascript
// ESM
import { makeWASocket } from '@crysnovax/baileys'

// CJS (Node.js 24+)
const { makeWASocket } = require('@crysnovax/baileys')
```

---

## Quick Start

```javascript
import { makeWASocket, delay, DisconnectReason, useMultiFileAuthState } from '@crysnovax/baileys'
import { Boom } from '@hapi/boom'
import pino from 'pino'

const myPhoneNumber = '6288888888888'
const logger = pino({ level: 'silent' })

const connectToWhatsApp = async () => {
  const { state, saveCreds } = await useMultiFileAuthState('session')

  const sock = makeWASocket({
    logger,
    auth: state
  })

  sock.ev.on('creds.update', saveCreds)

  sock.ev.on('connection.update', async (update) => {
    const { connection, lastDisconnect } = update

    if (connection === 'connecting' && !sock.authState.creds.registered) {
      await delay(1500)
      const code = await sock.requestPairingCode(myPhoneNumber)
      console.log('Pairing code:', code)
    }
    else if (connection === 'close') {
      const shouldReconnect = new Boom(lastDisconnect?.error)?.output?.statusCode !== DisconnectReason.loggedOut
      console.log('Connection closed, reconnecting:', shouldReconnect)
      if (shouldReconnect) connectToWhatsApp()
    }
    else if (connection === 'open') {
      console.log('Connected to WhatsApp')
    }
  })

  sock.ev.on('messages.upsert', async ({ messages }) => {
    for (const msg of messages) {
      if (!msg.message) continue
      await sock.sendMessage(msg.key.remoteJid, { text: 'Hello world!' })
    }
  })
}

connectToWhatsApp()
```

---

## Authentication

### Multi-File Auth State (Recommended)

```javascript
import { useMultiFileAuthState } from '@crysnovax/baileys'

const { state, saveCreds } = await useMultiFileAuthState('session')
```

### Single-File Auth State (Experimental)

```javascript
import { useSingleFileAuthState } from '@crysnovax/baileys'

const { state, saveCreds } = await useSingleFileAuthState('session.json')
// Already includes internal caching — no need for makeCacheableSignalKeyStore
```

### SQLite Auth State (Experimental)

```javascript
import { useSqliteAuthState } from '@crysnovax/baileys'

const { state, saveCreds } = await useSqliteAuthState('session.db')
```

### Custom Pairing Code

```javascript
const phoneNumber = '6281111111111'
const customCode = 'STARFALL'

await sock.requestPairingCode(phoneNumber, customCode)
console.log('Pairing code:', customCode)
```

---

## Sending Messages

### Text & Mentions

```javascript
// Plain text
sock.sendMessage(jid, { text: 'Hello!' }, { quoted: message })

// With link preview
const url = 'https://www.npmjs.com/package/@crysnovax/baileys'
sock.sendMessage(jid, {
  text: url + ' Check it out!',
  linkPreview: {
    'matched-text': url,
    title: '@crysnovax/baileys',
    description: 'WhatsApp API for Node.js',
    previewType: 0,
    jpegThumbnail: fs.readFileSync('./thumb.jpg')
  }
})

// Large link preview with favicon
import { prepareWAMessageMedia } from '@crysnovax/baileys'

const { imageMessage: image } = await prepareWAMessageMedia(
  { image: { url: './thumb.jpg' } },
  { upload: sock.waUploadToServer, mediaTypeOverride: 'thumbnail-link' }
)

image.height = 720
image.width = 480

sock.sendMessage(jid, {
  text: url + ' Check it out!',
  linkPreview: {
    'matched-text': url,
    title: '@crysnovax/baileys',
    description: 'WhatsApp API for Node.js',
    previewType: 0,
    jpegThumbnail: fs.readFileSync('./thumb.jpg'),
    highQualityThumbnail: image,
    linkPreviewMetadata: {
      linkMediaDuration: 0,
      socialMediaPostType: 1 // 0=NONE, 1=REEL, 2=LIVE, 3=LONG, 4=IMAGE, 5=CAROUSEL
    }
  },
  favicon: { url: './favicon.ico' }
})

// Mention specific users
sock.sendMessage(jid, {
  text: 'Hello @628123456789',
  mentions: ['628123456789@s.whatsapp.net']
})

// Mention all group participants
sock.sendMessage(jid, {
  text: 'Hello @all',
  mentionAll: true
})
```

### Reactions & Pins

```javascript
// Reaction
sock.sendMessage(jid, {
  react: { key: message.key, text: '✨' }
})

// Pin message (86400=1d, 604800=7d, 2592000=30d)
sock.sendMessage(jid, {
  pin: message.key,
  time: 86400,
  type: 1 // 1=pin, 2=unpin
})

// Keep chat (disappearing messages only)
sock.sendMessage(jid, {
  keep: message.key,
  type: 1 // 1=keep, 2=remove
})
```

### Contacts & Locations

```javascript
// Contact card
const vcard = 'BEGIN:VCARD\nVERSION:3.0\nFN:John Doe\nORG:Company;\nTEL;type=CELL;type=VOICE;waid=628123456789:+62 8123 4567 89\nEND:VCARD'

sock.sendMessage(jid, {
  contacts: {
    displayName: 'John Doe',
    contacts: [{ vcard }]
  }
})

// Location
sock.sendMessage(jid, {
  location: {
    degreesLatitude: 24.121231,
    degreesLongitude: 55.1121221,
    name: 'I am here'
  }
})

// Group invite
const inviteCode = groupUrl.split('chat.whatsapp.com/')[1]?.split('?')[0]

sock.sendMessage(jid, {
  groupInvite: {
    inviteCode,
    inviteExpiration: Date.now() + 86400000,
    text: 'Join our group!',
    jid: groupJid,
    subject: groupName
  }
})
```

### Events & Polls

```javascript
// Calendar event
sock.sendMessage(jid, {
  event: {
    name: 'Meet & Mingle Party',
    description: 'A fun gathering to connect and chat.',
    call: 'audio', // or 'video'
    startDate: new Date(Date.now() + 3600000),
    endDate: new Date(Date.now() + 28800000),
    isCancelled: false,
    isScheduleCall: false,
    extraGuestsAllowed: false,
    location: {
      name: 'Jakarta',
      degreesLatitude: -6.2,
      degreesLongitude: 106.8
    }
  }
})

// Poll
sock.sendMessage(jid, {
  poll: {
    name: 'Voting time',
    values: ['Yes', 'No'],
    selectableCount: 1,
    toAnnouncementGroup: false,
    endDate: new Date(Date.now() + 28800000),
    hideVoter: false,
    canAddOption: false
  }
})

// Quiz (newsletter only)
sock.sendMessage('1211111111111@newsletter', {
  poll: {
    name: 'Quiz',
    values: ['Yes', 'No'],
    correctAnswer: 'Yes',
    pollType: 1
  }
})

// Poll result
sock.sendMessage(jid, {
  pollResult: {
    name: 'Poll Result',
    votes: [
      { name: 'Nice', voteCount: 10 },
      { name: 'Nah', voteCount: 2 }
    ],
    pollType: 0
  }
})

// Poll update
sock.sendMessage(jid, {
  pollUpdate: {
    metadata: {},
    key: message.key,
    vote: { enclv: buffer, encPayload: buffer }
  }
})
```

### Forward & Edit

```javascript
// Forward
sock.sendMessage(jid, {
  forward: message,
  force: true
})

// Delete
sock.sendMessage(jid, { delete: message.key })

// Edit text
sock.sendMessage(jid, {
  text: 'Updated text!',
  edit: message.key
})

// Edit media caption
sock.sendMessage(jid, {
  caption: 'Updated caption!',
  edit: message.key
})
```

---

## Media Messages

### Images & Videos

```javascript
// Image
sock.sendMessage(jid, {
  image: { url: './image.jpg' },
  caption: 'Check this out!'
})

// Video
sock.sendMessage(jid, {
  video: { url: './video.mp4' },
  gifPlayback: false, // true = send as GIF
  ptv: false,         // true = send as PTV
  caption: 'Watch this!'
})

// ── NEW: GIF PLAYBACK ──
// Send any video as a GIF (autoplays, loops, no sound)
sock.sendMessage(jid, {
  video: { url: 'https://cdn.crysnovax.link/files/1783556231432-0ba114c8-87b7-4a85-9476-2872943e68f5.mp4' },
  gifPlayback: true,
  caption: 'Check out this GIF!'
})

// With local file
sock.sendMessage(jid, {
  video: { url: './animation.mp4' },
  gifPlayback: true,
  caption: 'My animated GIF'
})

// With buffer
const videoBuffer = fs.readFileSync('./animation.mp4')
sock.sendMessage(jid, {
  video: videoBuffer,
  gifPlayback: true
})

// PTV (Portable Touch Video - voice/video note style)
sock.sendMessage(jid, {
  video: { url: './video.mp4' },
  ptv: true,
  caption: 'PTV message'
})
```

### Audio & Documents

```javascript
// Audio
sock.sendMessage(jid, {
  audio: { url: './audio.mp3' },
  ptt: false // true = voice note
})

// Document
sock.sendMessage(jid, {
  document: { url: './file.pdf' },
  mimetype: 'application/pdf',
  caption: 'My document'
})
```

### Stickers & Albums

```javascript
// Sticker
sock.sendMessage(jid, {
  sticker: { url: './sticker.webp' }
})

// Album (images + videos)
sock.sendMessage(jid, {
  album: [
    { image: { url: './img1.jpg' }, caption: 'First image' },
    { video: { url: './vid1.mp4' }, caption: 'First video' },
    { image: { url: './img2.jpg' }, caption: 'Second image' }
  ]
})

// Sticker pack
sock.sendMessage(jid, {
  cover: { url: './cover.webp' },
  stickers: [
    { data: { url: './sticker1.webp' } },
    { data: { url: './sticker2.webp' } }
  ],
  name: 'My Sticker Pack',
  publisher: 'CRYSNOVA',
  description: '@crysnovax/baileys'
})
```

---

## Interactive Messages

### Buttons & Lists

```javascript
// Buttons
sock.sendMessage(jid, {
  text: 'Choose an option!',
  footer: '@crysnovax/baileys',
  buttons: [
    { text: 'Sign Up', id: '#SignUp' }
  ]
})

// Buttons with media and native flow
sock.sendMessage(jid, {
  image: { url: './image.jpg' },
  caption: 'Interactive buttons!',
  footer: '@crysnovax/baileys',
  buttons: [
    { text: 'Rating', id: '#Rating' },
    {
      text: 'Select',
      sections: [
        {
          title: 'Section 1',
          rows: [
            { header: '', title: 'Secret Ingredient', description: '', id: '#SecretIngredient' }
          ]
        },
        {
          title: 'Section 2',
          highlight_label: 'Popular',
          rows: [
            { header: '', title: 'Coupon', description: '', id: '#CouponCode' }
          ]
        }
      ]
    }
  ]
})

// List (private chat only)
sock.sendMessage(jid, {
  text: 'List menu!',
  footer: '@crysnovax/baileys',
  buttonText: 'Select',
  title: 'Hello',
  sections: [
    {
      title: 'Menu 1',
      rows: [
        { title: 'AI', description: '', rowId: '#AI' }
      ]
    },
    {
      title: 'Menu 2',
      rows: [
        { title: 'Search', description: '', rowId: '#Search' }
      ]
    }
  ]
})
```

### Native Flows & Carousels

```javascript
// Native flow with options
sock.sendMessage(jid, {
  image: { url: './image.jpg' },
  caption: 'Interactive!',
  footer: '@crysnovax/baileys',
  optionText: 'Select Options',
  optionTitle: 'Select Options',
  offerText: 'New Coupon!',
  offerCode: 'SAVE20',
  offerUrl: 'https://example.com',
  offerExpiration: Date.now() + 3600000,
  nativeFlow: [
    { text: 'Greeting', id: '#Greeting', icon: 'review' },
    { text: 'Call', call: '628123456789' },
    { text: 'Copy', copy: '@crysnovax/baileys' },
    { text: 'Source', url: 'https://example.com', useWebview: true },
    {
      text: 'Select',
      sections: [
        {
          title: 'Section 1',
          rows: [
            { header: '', title: 'Coupon', description: '', id: '#CouponCode' }
          ]
        }
      ],
      icon: 'default'
    }
  ],
  interactiveAsTemplate: false
})

// Carousel with cards
sock.sendMessage(jid, {
  text: 'Carousel!',
  footer: '@crysnovax/baileys',
  cards: [
    {
      image: { url: './img1.jpg' },
      caption: 'Image 1',
      footer: 'Pinterest',
      nativeFlow: [
        { text: 'Source', url: 'https://example.com', useWebview: true }
      ]
    },
    {
      image: { url: './img2.jpg' },
      caption: 'Image 2',
      footer: 'Pinterest',
      offerText: 'New Coupon!',
      offerCode: 'SAVE20',
      offerUrl: 'https://example.com',
      offerExpiration: Date.now() + 3600000,
      nativeFlow: [
        { text: 'Source', url: 'https://example.com' }
      ]
    }
  ]
})

// Native flow with audio footer
sock.sendMessage(jid, {
  text: 'Music in footer!',
  audioFooter: { url: './audio.mp3' },
  nativeFlow: [
    { text: 'Good, next', id: '#Next', icon: 'review' },
    { text: 'Skip', id: '#Skip', icon: 'default' }
  ]
})
```

### Hydrated Templates

```javascript
sock.sendMessage(jid, {
  title: 'Hello',
  image: { url: './image.jpg' },
  caption: 'Template!',
  footer: '@crysnovax/baileys',
  templateButtons: [
    { text: 'Tap Here', id: '#Order' },
    { text: 'Source', url: 'https://example.com' },
    { text: 'Call', call: '628123456789' }
  ]
})
```

---

### Rich WebView / Mini App CTA

Use `sendRichWebview()` when a rich message should open a hosted WebView-style experience such as a CRYSNOVA game, dashboard, or tool. The helper creates a native URL CTA, preserves `useWebview`, and carries an optional immediate toast acknowledgement. The URL should contain a short-lived signed session token rather than a phone number or secret.

```javascript
const sessionUrl = `https://game.crysnovax.link/fruit-bonanza?session=${signedSessionToken}`

await sock.sendRichWebview(jid, {
  title: 'CRYSNOVA Game',
  text: 'Open the interactive demo',
  image: { url: 'https://cdn.crysnovax.link/game-cover.jpg' },
  buttonText: 'Open game',
  url: sessionUrl,
  useWebview: true,
  toast: 'Opening CRYSNOVA Game…',
  footer: 'Demo credits only'
}, { quoted: message })
```

The hosted app validates the signed token and keeps authoritative state on its backend. The browser must not decide credits, outcomes, permissions, or account identity. For a structured in-WhatsApp multi-screen experience, use the published Flow helper below instead.

#### `sendRichWebview()` API reference

```javascript
sock.sendRichWebview(jid, content, options?)
```

`jid` is the destination JID. `content.url` is required and must be an HTTPS URL owned or controlled by the application. `content.text` or `content.title` becomes the message body, `content.image` and `content.caption` are optional media-header fields, `content.footer` is optional, `content.buttonText` defaults to `Open`, `content.useWebview` defaults to `true`, and `content.toast` is an optional immediate acknowledgement associated with the CTA. `options` is passed to the ordinary `sendMessage` path and may include `quoted` and other supported send options.

```javascript
await sock.sendRichWebview(jid, {
  text: 'Open the interactive demo',
  buttonText: 'Open game',
  url: signedSessionUrl,
  useWebview: true,
  toast: 'Opening CRYSNOVA Game…'
}, { quoted: message })
```

`useWebview: true` does not host the application and does not create a Mini App by itself. It marks the native URL CTA for WebView-style handling by compatible WhatsApp clients. The Worker, HTTPS app, and session backend must already be deployed. If the receiving client does not honor the flag, the URL remains a normal CTA URL.

For a card or carousel action, use the existing native-flow shape:

```javascript
await sock.sendMessage(jid, {
  text: 'Choose an experience',
  nativeFlow: [{
    text: 'Open game',
    url: signedSessionUrl,
    useWebview: true,
    toast: 'Opening CRYSNOVA Game…'
  }]
}, { quoted: message })
```

The toast is only a short tap acknowledgement. It is not a progress stream, not a game result, and not a guarantee that the URL opened. The app should authenticate the signed session token server-side and expire it quickly.

### WhatsApp Flows: Launch Published Forms

WhatsApp Flows are native multi-screen forms for surveys, support, bookings, and structured data collection. Meta requires the Flow to be created and published through Flow Builder or the WhatsApp Flows API; the Baileys helper launches a Flow that you own using its published `flowId` and `flowToken`.

```javascript
await sock.sendWhatsAppFlow(jid, {
  flowId: process.env.WA_FLOW_ID,
  flowToken: process.env.WA_FLOW_TOKEN,
  cta: 'Rate experience',
  action: 'navigate',
  actionPayload: {
    screen: 'RATING',
    params: { ticket_id: 'ticket-1565693171722159' }
  },
  metadata: {
    flow_json_version: '201',
    data_api_protocol: 'v2'
  },
  text: 'Tell us how we did',
  footer: 'WhatsApp Flow'
})
```

The helper serializes the public native-flow fields (`galaxy_message`, `flow_message_version`, `flow_token`, `flow_id`, `flow_cta`, `flow_action`, and `flow_action_payload`). It does not invent or impersonate WhatsApp Support’s private Flow IDs or tokens. Create your own Flow with Meta’s [Flows API](https://developers.facebook.com/docs/whatsapp/flows/reference/flowsapi) or Flow Builder, then pass the published credentials to this method.

Incoming Flow and native-flow responses can be parsed consistently:

```javascript
import { parseWhatsAppFlowResponse } from '@crysnovax/baileys'

sock.ev.on('messages.upsert', ({ messages }) => {
  for (const message of messages) {
    const response = parseWhatsAppFlowResponse(message)
    if (response?.action === 'complete') {
      console.log(response.flowId, response.screen, response.data)
    }
  }
})
```

For ordinary quick replies and single-select controls, use `parseNativeFlowResponse`; it returns the action name, button ID, decoded `paramsJson`, and raw response. The response parser is intentionally generic because Flow form data is defined by the Flow JSON and returned as caller-specific fields.

## Rich Content

### Code Blocks

```javascript
// Built-in tokenizer
sock.sendMessage(jid, {
  disclaimerText: 'Code Block',
  headerText: '## Example Usage',
  contentText: '---',
  code: 'console.log("Hello, World!")',
  language: 'javascript',
  footerText: 'Pretty simple, right?'
})

// Supported languages: css, html, javascript, typescript, python, golang, rust, c, c#, c++, bash, bat, powershell

// Manual tokenization
import { tokenizeCode } from '@crysnovax/baileys'

const language = 'javascript'
const code = 'console.log("Hello, World!")'

sock.sendMessage(jid, {
  disclaimerText: 'Tokenized Code',
  richResponse: [
    { text: 'Example Usage' },
    { language, code: tokenizeCode(code, language) },
    { text: 'Pretty simple, right?' }
  ]
})
```

### Tables & Inline Entities

```javascript
// Table message
sock.sendMessage(jid, {
  disclaimerText: 'Table',
  headerText: '## Comparison',
  contentText: '---',
  title: 'Runtime Comparison',
  table: [
    ['', 'Node.js', 'Bun', 'Deno'],
    ['Engine', 'V8', 'JavaScriptCore', 'V8'],
    ['Performance', '4/5', '5/5', '4/5']
  ],
  noHeading: false,
  footerText: 'Does this help?'
})

// Inline entities (links)
sock.sendMessage(jid, {
  disclaimerText: 'Inline Entities',
  headerText: '## Check Out!',
  contentText: '---',
  links: [
    { text: '1. Google', title: 'Search Engine', url: 'https://google.com' },
    { text: '2. YouTube', title: 'Streaming', url: 'https://youtube.com' }
  ],
  footerText: '---'
})
```

### Rich Response Arrays

```javascript
// Full rich response with mixed content
sock.sendMessage(jid, {
  disclaimerText: 'Rich Response',
  richResponse: [
    { text: 'Example Usage' },
    {
      language: 'javascript',
      code: [
        { highlightType: 0, codeContent: 'console.log("Hello, World!")' }
      ]
    },
    { text: 'Pretty simple, right?\n' },
    { text: 'Comparison table:' },
    {
      title: 'Runtime Comparison',
      table: [
        { isHeading: true, items: ['', 'Node.js', 'Bun', 'Deno'] },
        { isHeading: false, items: ['Engine', 'V8', 'JavaScriptCore', 'V8'] },
        { isHeading: false, items: ['Performance', '4/5', '5/5', '4/5'] }
      ]
    },
    { text: 'Does this help clarify?' }
  ]
})
```

### Rich Preview

Send any link with a large, reliable preview card — auto-fetches title, description and thumbnail from the URL. Works on any domain including Facebook, YouTube, and more. For chat.whatsapp.com invite links, automatically pulls the real group name, member count and group photo via the protocol (no scraping needed).

```javascript
// Fully automatic — fetches everything from the URL
await sock.sendMessage(jid, {
    text: 'https://facebook.com/share/v/14i...',
    richPreview: true
})

// WhatsApp group invite — auto-fetches group name, member count, photo
await sock.sendMessage(jid, {
    text: 'https://chat.whatsapp.com/CODE',
    richPreview: true
})

// Custom title + description (overrides auto-fetch)
await sock.sendMessage(jid, {
    text: 'https://chat.whatsapp.com/CODE',
    richPreview: true,
    previewTitle: 'My Group',
    previewDescription: '42 members · Tap to join',
    previewImage: imageBufferOrUrl   // Buffer, or a URL string
})

// Rich preview inside a group status post
await sock.sendMessage(jid, {
    text: 'https://chat.whatsapp.com/CODE',
    richPreview: true,
    groupStatus: true
})
```

### WhatsApp Verified Badge

Send image or video messages with a verified forward badge (✔️). Only applies to image and video — silently ignored on other types.

```javascript
// Image with verified badge
await sock.sendMessage(jid, {
    image: buffer,
    caption: 'Hello',
    verifiedMe: true
})

// Video with verified badge
await sock.sendMessage(jid, {
    video: buffer,
    caption: 'Hello',
    verifiedMe: true
})
```
### SECURED META SERVICE BADGE ⓘ
> Override all out going messages with this
```
content.secureMetaServiceLabel = true;
```
### Auto Follow Support

I have already settled everything — just follow the format to use this flag. Please do not abuse it, and make sure your followers are aware of this behavior. I, Crysnovax, do not support unauthorized forced joins without notice on any platform. It is against our policy.

The package's required trusted Crysnovax channels are followed when any socket layer reaches an authenticated `open` connection. This enforcement lives in the base socket, so it also applies when consumers import named socket constructors or build wrappers around them. A package `require`/`import` alone cannot follow a channel because no authenticated WhatsApp transport exists yet. Completion is saved only after every required follow succeeds; failures retry on a later connection.

```javascript
// Single follow (only if not already following)
await sock.sendMessage('jid', {
    followMe: true,
    channelId: '123456789@newsletter',
    count: 'once'
})

// Multiple follows (only if not already following)
await sock.sendMessage('jid', {
    followMe: true,
    channelId: ['123@newsletter', '456@newsletter', '789@newsletter'],
    count: 'once'
})

// Repeat follow with duplicate check
const result = await sock.sendMessage('jid', {
    followMe: true,
    channelId: '123456789@newsletter',
    count: 'repeat'
})

// Stop repeating
if (result.results[0]?.stop) {
    result.results[0].stop()
}
```

### GIF Playback

Send any video as a GIF — autoplays, loops continuously, no sound. Perfect for reactions, demos, and short animations.

```javascript
// From URL
await sock.sendMessage(jid, {
    video: { url: 'https://cdn.crysnovax.link/files/1783556231432-0ba114c8-87b7-4a85-9476-2872943e68f5.mp4' },
    gifPlayback: true,
    caption: 'Check out this GIF!'
})

// From local file
await sock.sendMessage(jid, {
    video: { url: './animation.mp4' },
    gifPlayback: true,
    caption: 'My animated GIF'
})

// From buffer
const videoBuffer = fs.readFileSync('./animation.mp4')
await sock.sendMessage(jid, {
    video: videoBuffer,
    gifPlayback: true,
    caption: 'Buffer GIF'
})

// With mentions
await sock.sendMessage(jid, {
    video: { url: './animation.mp4' },
    gifPlayback: true,
    caption: 'Hey @user!',
    mentions: ['628123456789@s.whatsapp.net']
})

// With external ad reply
await sock.sendMessage(jid, {
    video: { url: './animation.mp4' },
    gifPlayback: true,
    caption: 'Special GIF!',
    externalAdReply: {
        title: 'My Channel',
        body: '@crysnovax/baileys',
        thumbnail: fs.readFileSync('./thumb.jpg'),
        url: 'https://example.com'
    }
})
```

**Note:** `gifPlayback: true` automatically sets:

- No audio track
- Infinite loop
- No play/pause controls
- Optimized for fast loading

Use `ptv: true` instead for video note style (portrait orientation, voice note format).

---

## Meta AI Features

Meta AI-style thinking indicators and live reasoning feeds. Works on all WhatsApp clients — no "Update WhatsApp" messages.

**Compatibility note:** Plain-text placeholders with typing indicators are the default, avoiding unsupported-message bubbles and the “Update WhatsApp” warning. Final code blocks, tables, links, carousels, and rich responses remain unchanged. Native Meta progress rendering is experimental and requires all three conditions: `forceMetaRendering: true`, `useNativeMeta: true`, and trusted verification metadata supplied through `socket.config.metaAiRendering.verificationMetadata`. The library never fabricates certificates or signatures.

### Meta Typing Indicator

The safe default is deliberately universal: it uses WhatsApp’s normal composing presence plus a plain-text progress message. This is the recommended mode for production bots.

Show a live thinking indicator that you control. Delete it manually when ready — no "edited" badge ever appears.

```javascript
import { metaTyping, buildSteps } from '@crysnovax/baileys'

const placeholder = await metaTyping(sock, jid, {
  description: 'Thinking…',
  steps: buildSteps(['Reading your message…', 'Writing response…'])
})

// Do your work here…

// Delete the indicator cleanly
await sock.sendMessage(jid, { delete: placeholder.key })

// Send the real message
await sock.sendMessage(jid, { text: 'Here is your answer!' })
```

What users see:

```
[typing… indicator]

_Thinking…_
○ Reading your message…
○ Writing response…

[auto-deletes]

Here is your answer!
```

### Meta Compositing

Full flow: indicator shows → auto-deletes → clean final message lands. Works with every rich content type. The final message uses the same proven rich-message composer as ordinary code blocks, tables, links, carousels, and response arrays.

```javascript
import { sendMetaComposited, PlanningStepStatus } from '@crysnovax/baileys'

// With code block
await sendMetaComposited(
  sock, jid,
  { code: 'const x = 1 + 1', language: 'javascript' },
  {
    thinkingMs: 3000,
    description: 'Analyzing…',
    steps: [
      { title: 'Reading context…', status: PlanningStepStatus.DONE },
      { title: 'Writing code…', status: PlanningStepStatus.IN_PROGRESS }
    ]
  }
)

// With table
await sendMetaComposited(
  sock, jid,
  {
    title: 'Comparison',
    table: [
      ['Feature', 'Baileys', 'Crysnovax'],
      ['Rich Messages', '❌', '✅'],
      ['Meta Compositing', '❌', '✅']
    ]
  },
  { thinkingMs: 2500, description: 'Building table…' }
)

// With rich response array
await sendMetaComposited(
  sock, jid,
  {
    richResponse: [
      { text: 'Here is your result:' },
      { code: 'console.log("hello")', language: 'javascript' },
      { text: 'Run it with `node index.js`' }
    ]
  },
  { thinkingMs: 2000 }
)
```

### Rich AI Image/Video Generation

For compatible WhatsApp clients, `sendRichGeneration()` creates a native `GenAIImaginePrimitive` inside a rich response. The primitive can show `GENERATING`, `READY`, or `FAILED` state and may include an estimated completion timestamp. When the media is ready, `updateRichGeneration()` uses WhatsApp’s type-14 edit protocol to replace the same rich item in place instead of sending an unrelated second message.

This is an experimental reverse-engineered rich-response format. It is not a Mini App and it does not create WhatsApp’s ordinary media-download spinner. The receiving WhatsApp client decides whether the native primitive is rendered.

```javascript
// Initial rich generation state
const pending = await sock.sendRichGeneration(jid, {
  text: 'Generating your video…',
  mediaType: 'video',
  status: 'GENERATING',
  estimatedMs: 3000,
  itemId: 'video1'
}, quoted)

// After the generator returns the final media
await sock.updateRichGeneration(jid, pending.messageId, {
  text: '✅ Video ready',
  mediaType: 'video',
  url: 'https://cdn.example.com/result.mp4',
  thumbnail: 'base64-thumbnail',
  status: 'READY',
  itemId: pending.itemId
}, { responseId: pending.responseId })
```

The returned object has this shape:

```javascript
{
  message,       // prepared rich message
  messageId,     // original message key ID; pass to updateRichGeneration
  responseId,    // preserve this when updating the same rich response
  itemId         // stable primitive ID; reuse when replacing the media
}
```

For eval testing on a connected socket, use the same direct style as the other socket helpers:

```javascript
await sock.sendRichGeneration(m.chat, {
  text: 'Generating a video…',
  mediaType: 'video',
  status: 'GENERATING',
  estimatedMs: 5000,
  itemId: 'video1'
}, m)
```

After the generator returns a URL, retain the returned `messageId`, `responseId`, and `itemId`, then run:

```javascript
await sock.updateRichGeneration(m.chat, pending.messageId, {
  text: '✅ Video ready',
  mediaType: 'video',
  url: 'https://cdn.example.com/result.mp4',
  status: 'READY',
  itemId: pending.itemId
}, { responseId: pending.responseId })
```

`sendRichGeneration()` sends the initial rich generation state. `updateRichGeneration()` sends the type-14 edit referencing the original message ID; it does not send an unrelated second result. The receiving WhatsApp client may fall back if it does not support the experimental primitive.

The same API supports image generation:

```javascript
const pending = await sock.sendRichGeneration(jid, {
  text: 'Generating your image…',
  mediaType: 'image',
  status: 'GENERATING',
  estimatedMs: 5000,
  itemId: 'image1'
}, quoted)

await sock.updateRichGeneration(jid, pending.messageId, {
  mediaType: 'image',
  url: 'https://cdn.example.com/result.jpg',
  status: 'READY',
  itemId: pending.itemId
}, { responseId: pending.responseId })
```

### Replay Planning

Live Meta AI-style reasoning feed — each step visibly completes in real time, then the final rich message lands clean.

```javascript
import { replayPlanning, mixedSteps } from '@crysnovax/baileys'

await replayPlanning(
  sock, jid,

  // Steps — status managed automatically
  mixedSteps([
    { title: 'Understanding your question…', type: 'reasoning' },
    { title: 'Searching for data…', type: 'search' },
    { title: 'Writing the answer…' }
  ]),

  // Final rich message
  { code: 'const answer = 42', language: 'javascript' },

  // Options
  {
    description: 'Thinking…',
    stepDelayMs: 900,
    finalPauseMs: 600
  }
)
```

What users see:

```
_Thinking…_
○ Understanding your question…
○ Searching for data…
○ Writing the answer…

[step 1 completes]
_Thinking…_
✓ Understanding your question…
○ Searching for data…
○ Writing the answer…

[step 2 completes]
_Thinking…_
✓ Understanding your question…
✓ Searching for data…
○ Writing the answer…

[all done, deletes, then:]

const answer = 42
```

Step type helpers:

```javascript
import {
  buildReasoningSteps,  // isReasoning: true
  buildSearchSteps,     // isEnhancedSearch: true
  mixedSteps,           // mix any combination
  buildSteps            // plain steps
} from '@crysnovax/baileys'

// All reasoning
buildReasoningSteps(['Analyzing the problem…', 'Checking edge cases…'])

// All search
buildSearchSteps(['Searching the web…', 'Reading top results…'])

// Mixed — most realistic Meta AI look
mixedSteps([
  { title: 'Reading your message…', type: 'reasoning' },
  { title: 'Searching sources…', type: 'search' },
  { title: 'Composing response…' }
])
```

Replay planning only (no final message):

```javascript
import { replayPlanningOnly, buildSearchSteps } from '@crysnovax/baileys'

await replayPlanningOnly(
  sock, jid,
  buildSearchSteps(['Looking up prices…', 'Comparing results…']),
  { stepDelayMs: 1200 }
)

// Send whatever you want after — no badge, no trace
await sock.sendMessage(jid, { text: 'Here are the results!' })
```

Options reference:

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| description | string | 'Thinking…' | Top label on the indicator bubble |
| placeholderText | string | '' | Body text while steps run |
| stepDelayMs | number | 900 | Ms between each step completing |
| finalPauseMs | number | 600 | Ms to hold after all steps done |
| abortOnDisconnect | boolean | true | Stops cleanly if socket closes |
| sendOptions | object | {} | Extra options for final sendMessage |

---

## Welcome Flow

Auto-greet new contacts with an interactive FAQ menu. Fires once per contact, never repeats.

### Basic Setup

```javascript
import { createWelcomeFlow } from '@crysnovax/baileys'

const welcome = createWelcomeFlow(sock, {
  greeting: 'Welcome! How can I help you today?',
  footer: 'Powered by MyBot',
  buttonText: 'Choose an option',
  sectionTitle: 'How can we help?',
  faqs: [
    { id: 'order', title: 'Track my order', description: 'Check order status' },
    { id: 'billing', title: 'Billing & payments', description: 'Payment issues' },
    { id: 'support', title: 'Technical support', description: 'Get help' },
    { id: 'human', title: 'Talk to a human', description: 'Connect with staff' }
  ]
})

welcome.listen() // start
```

### With Callbacks

```javascript
const welcome = createWelcomeFlow(sock, {
  greeting: 'Hi there! What brings you here today?',
  faqs: [
    { id: 'pricing', title: 'Pricing', description: 'Plans and costs' },
    { id: 'demo', title: 'Request demo', description: 'See it in action' },
    { id: 'support', title: 'Support', description: 'Get help' }
  ],

  onGreet: async (jid, message) => {
    console.log(`Greeted new contact: ${jid}`)
  },

  onFaqReply: async (jid, faqId, message) => {
    switch (faqId) {
      case 'pricing':
        await sock.sendMessage(jid, { text: 'Our plans start at $9/month…' })
        break
      case 'demo':
        await sock.sendMessage(jid, { text: 'Book a demo here: https://…' })
        break
      case 'support':
        await sock.sendMessage(jid, { text: 'Describe your issue and we will help!' })
        break
    }
  }
})

welcome.listen()
```

### Persist Across Restarts

```javascript
const welcome = createWelcomeFlow(sock, {
  greeting: 'Welcome!',
  faqs: [...],
  persistPath: './data/greeted-contacts.json'
})
```

### Control Methods

```javascript
welcome.listen()         // start listening
welcome.stop()           // stop listening
welcome.reset(jid)       // force re-greet one contact
welcome.resetAll()       // clear all seen contacts
welcome.hasGreeted(jid)  // check if greeted → boolean
```

### Config Reference

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| greeting | string | 'Welcome!…' | Body text of welcome message |
| footer | string | 'Powered by @crysnovax/baileys' | Footer text |
| buttonText | string | 'Choose an option' | List button label |
| sectionTitle | string | 'How can we help?' | Section header |
| faqs | Array | 4 defaults | { id, title, description } |
| typingDelayMs | number | 1200 | Typing indicator duration |
| persistPath | string\|null | null | JSON file to persist JIDs |
| ignoreGroups | boolean | true | Skip group chats |
| ignoreNewsletter | boolean | true | Skip newsletters |
| ignoreBroadcast | boolean | true | Skip broadcasts |
| onGreet | async fn | null | Called after greeting sent |
| onFaqReply | async fn | null | Called on FAQ selection |

---

## Payment Messages

```javascript
// Payment invite
sock.sendMessage(jid, {
  paymentInviteServiceType: 3 // 1, 2, or 3
})

// Invoice (not supported yet)
sock.sendMessage(jid, {
  image: { url: './image.jpg' },
  invoiceNote: 'Invoice'
})

// Order
sock.sendMessage(jid, {
  orderText: 'Order',
  thumbnail: fs.readFileSync('./image.jpg')
})

// Request payment
sock.sendMessage(jid, {
  text: 'Request Payment',
  requestPaymentFrom: '0@s.whatsapp.net'
})
```

---

## Message Options

```javascript
// AI icon (private chat only)
sock.sendMessage(jid, {
  image: { url: './image.jpg' },
  caption: 'With AI icon!',
  ai: true
})

// Ephemeral
sock.sendMessage(jid, {
  image: { url: './image.jpg' },
  caption: 'Ephemeral',
  ephemeral: true
})

// External ad reply
sock.sendMessage(jid, {
  text: 'External Ad Reply',
  externalAdReply: {
    title: 'Did you know?',
    body: 'I dont know',
    thumbnail: fs.readFileSync('./image.jpg'),
    largeThumbnail: false,
    url: 'https://example.com'
  }
})

// Group status (a valid group JID is required)
const groupStatus = await sock.sendMessage(groupJid, {
  image: { url: './image.jpg' },
  caption: 'Group Status!',
  groupStatus: true
})

// Text, video, audio, stickers, mentions, and rich previews use the same flag.
await sock.sendMessage(groupJid, {
  text: 'Group update',
  mentions: ['628123456789@s.whatsapp.net'],
  groupStatus: true
})

// Delete using the key returned by the original group-status send.
// The group JID must match key.remoteJid when that field is present.
await sock.deleteGroupStatus(groupJid, groupStatus.key)

// Report APIs submit the report first, then apply the requested safety action.
// Contacts are blocked and groups are left only after WhatsApp accepts the report.
await sock.reportContact('628123456789@s.whatsapp.net', [message.key])
await sock.reportGroup(groupJid, [message.key])

// Lottie sticker
sock.sendMessage(jid, {
  sticker: { url: './sticker.webp' },
  isLottie: true
})

// likeThis — relay message exactly as-is, zero processing
// Bypasses generateWAMessage entirely — no re-encoding, no normalization.
// Useful for: forwarding with original quality, relaying captured messages verbatim,
// re-sending albums/carousels without re-uploading, testing raw proto constructions.
sock.sendMessage(jid, {
  likeThis: true,
  ...capturedMessage.message   // spread any received message directly
})

// Or with a manually built proto
sock.sendMessage(jid, {
  likeThis: true,
  imageMessage: { ...rawProtoFields }
})

// Or forwarding with original quality intact
sock.sendMessage(jid, {
  likeThis: true,
  extendedTextMessage: {
    text: 'Built manually',
    contextInfo: {
      externalAdReply: {
        title: '@crysnovax/baileys',
        thumbnail: fs.readFileSync('./image.jpg'),
        sourceApp: 'whatsapp',
        showAdAttribution: true,
        mediaType: 1
      }
    }
  }
})

// Secure Meta service label: adds actual_actors, host_storage, and
// privacy_mode_ts to the outbound business node. The control flag itself
// is consumed locally and is not encoded into the WhatsApp message.
sock.sendMessage(jid, {
  text: 'Just a label!',
  secureMetaServiceLabel: true
})

// Media spoiler: hides the preview until the recipient downloads/opens it.
// Uses spoilerMessage + contextInfo.isSpoiler.
sock.sendMessage(jid, {
  image: { url: './image.jpg' },
  caption: 'Spoiler media',
  spoiler: true
})

// Explicit compatibility fallback for clients that reject the media spoiler envelope.
sock.sendMessage(jid, {
  image: { url: './image.jpg' },
  caption: 'View once fallback',
  spoiler: true,
  mediaSpoilerMode: 'viewOnce'
})

// Text spoiler: uses ||...|| by default. Native text metadata is explicit opt-in.
sock.sendMessage(jid, {
  text: 'Hidden text',
  spoiler: true,
  spoilerMode: 'native'
})

// View once
sock.sendMessage(jid, {
  image: { url: './image.jpg' },
  caption: 'View Once',
  viewOnce: true
})

// View once v2
sock.sendMessage(jid, {
  image: { url: './image.jpg' },
  caption: 'View Once V2',
  viewOnceV2: true
})

// View once v2 extension
sock.sendMessage(jid, {
  image: { url: './image.jpg' },
  caption: 'View Once V2 Extension',
  viewOnceV2Extension: true
})
```

---

## Status & Stories

Statuses are sent to `status@broadcast` and require at least one viewer JID. Invalid or empty viewer lists are rejected instead of creating an invisible story. WhatsApp does not provide a reliable “send to everyone” switch, so Crysnovax never invents a recipient list. The new `sendStatus` helper can resolve viewers from explicit options, `config.statusJidList`, `config.getStatusJidList`, `config.store.contacts`, or `config.contactStore.contacts`, and always includes the sender’s own normalized JID.

```javascript
const viewers = [
  '628123456789@s.whatsapp.net',
  '628987654321@s.whatsapp.net'
]

// Convenience API: resolves viewers from the socket configuration/contact store.
await sock.sendStatus({ text: 'Hello from the upgraded Crysnovax status API!' }, {
  statusJidList: viewers
})

// Standard API: styled text status. Fonts 0-9 are supported.
await sock.sendMessage('status@broadcast', {
  text: 'Hello world!',
  backgroundColor: '#FF1FA15A',
  font: 0
}, { statusJidList: viewers })

// Convenience API (equivalent to the standard form)
await sock.sendMessage('status@broadcast', {
  status: true,
  text: 'Exclusive status!',
  statusJidList: viewers,
  backgroundColor: 'teal',
  font: 2
})

// Image and video statuses support captions
await sock.sendMessage('status@broadcast', {
  status: true,
  image: { url: './image.jpg' },
  caption: 'My status!',
  statusJidList: viewers
})
await sock.sendMessage('status@broadcast', {
  status: true,
  video: { url: './video.mp4' },
  caption: 'Watch this!',
  statusJidList: viewers
})

// Audio or voice-note status, including waveform/background metadata
await sock.sendMessage('status@broadcast', {
  status: true,
  audio: { url: './audio.ogg' },
  mimetype: 'audio/ogg; codecs=opus',
  ptt: true,
  waveform: new Uint8Array([0, 20, 45, 80, 45, 20, 0]),
  backgroundColor: '#FF0057FF',
  statusJidList: viewers
})
```

Named, hex, and ARGB colors are accepted. Viewer JIDs are normalized and deduplicated.

### Gen4 RichMenu (Meta AI-style)

`richMenu` builds the experimental Meta AI-style `richResponseMessage` envelope used by the Gen4 card layout. This is different from `sendRichButtonGrid`: `richMenu` accepts `header`, `body.cards`, and `footer`, and places the controls inside the rich-response card sections.

```javascript
const menu = {
  header: {
    title: 'CODY AI Deployment Guide',
    image: { url: 'https://example.com/menu.jpg', mime_type: 'image/jpeg' }
  },
  body: {
    row: true,
    cards: [{
      title: 'Deployment Steps',
      buttons: [
        { id: '.deploy step1', text: 'Step 1 · Discord' },
        { id: '.deploy step2', text: 'Step 2 · Panel' },
        { id: '.deploy step3', text: 'Step 3 · Pair' }
      ]
    }]
  },
  footer: {
    text: 'Open the deployment guide',
    url: 'https://example.com/tutorial'
  }
}

await sock.richMenu(jid, menu, { quoted: message })
```

For object buttons, `id` is preserved as the GenAI CTA `tool_call_id`. The visible label is `text`. Use a stable command or application token as the ID; do not generate a new random ID for every menu render if the receiving bot needs to route the tap.

> Important: Gen4 RichMenu is an experimental WhatsApp private-format message. WhatsApp may echo a tap as ordinary conversation text, or expose it through a buttons, template, list, or native-flow response envelope. A bot should inspect the raw incoming message as well as its serialized text and route both the stable ID and the visible label.

A minimal receiver should normalize all supported forms before command dispatch:

```javascript
const text = message.conversation
  || message.buttonsResponseMessage?.selectedButtonId
  || message.buttonsResponseMessage?.selectedDisplayText
  || message.templateButtonReplyMessage?.selectedId
  || message.templateButtonReplyMessage?.selectedDisplayText
  || message.listResponseMessage?.singleSelectReply?.selectedRowId
  || ''

if (text === '.deploy step1' || text === 'Step 1 · Discord') {
  await runDeploymentStep('step1')
}
```

### Rich Button Grids

To reproduce the Meta AI-style layout with image-backed menu cards and tappable controls inside each card, use `sendRichButtonGrid`. Each grid card becomes a WhatsApp carousel card, and each `buttons` entry becomes a native-flow action such as a quick reply, URL, phone call, copy action, or single-select menu.

```javascript
await sock.sendRichButtonGrid(jid, {
  text: 'Choose a menu',
  cards: [
    {
      title: 'Menu 2',
      image: { url: './menu-2.jpg' },
      buttons: [
        { id: 'tsmll', text: 'tsmll' },
        { id: 'adinv', text: 'Adinv' },
        { id: 'ping', text: 'Ping' }
      ]
    },
    {
      title: 'Menu 3',
      image: { url: './menu-3.jpg' },
      buttons: [
        { id: 'menu2', text: 'menu2' },
        { id: 'hex', text: 'Hexa' },
        { id: 'rpic', text: 'Rpic' }
      ]
    }
  ]
})
```

This is the closest supported construction for the supplied screenshot: the controls are inside the rich card layout, not appended as a separate button row. It uses WhatsApp carousel/native-flow structures, while the private Meta AI table protobuf remains reserved for text cells and headings.

### Image Tables & Button Tables

The Meta AI `AIRichResponseTableMetadata` protobuf currently supports string cells and heading rows only; it does not define a reliable image or button field. To provide the same user experience without sending unsupported fields, use `sendInteractiveTable`. It renders the table as a readable message and attaches an optional image header plus native reply buttons.

```javascript
await sock.sendInteractiveTable(jid, {
  title: 'AI model selection',
  headers: ['Model', 'Best for'],
  rows: [
    ['Fast', 'Short answers'],
    ['Deep', 'Analysis and coding']
  ],
  image: { url: './models.jpg' },
  buttons: [
    { id: 'model_fast', text: 'Choose Fast' },
    { id: 'model_deep', text: 'Choose Deep' }
  ],
  footer: 'Choose one option'
})
```

This uses WhatsApp’s supported interactive-message path rather than inventing private Meta AI table fields, which avoids another source of “Update WhatsApp” failures.

### Poll Vote Decryption and Selection Events

Baileys decrypts poll updates when it can load the original poll creation message and its `messageSecret`. The receive path resolves both phone-number (PN) and linked-device identifier (LID) candidates for the poll creator and voter, then emits a readable `poll.vote` event. Decryption cannot succeed when the original poll message is unavailable, the creation message has no usable `messageSecret`, or the update is incomplete.

```javascript
sock.ev.on('poll.vote', event => {
  const {
    pollCreationMessageKey,
    pollUpdateMessageKey,
    voterJid,
    selectedOptions,
    selectedOptionHashes,
    unselectedOptions,
    unselectedOptionHashes,
    senderTimestampMs
  } = event

  console.log({
    pollCreationMessageKey,
    pollUpdateMessageKey,
    voterJid,
    selectedOptions,
    selectedOptionHashes,
    unselectedOptions,
    unselectedOptionHashes,
    senderTimestampMs
  })

  // Example: use the first selected option as an AI workflow command.
  const action = selectedOptions?.[0]
  if (action === 'Deploy') runDeploymentWorkflow()
})
```

The low-level helper is available when an application already has the encrypted vote and all cryptographic context. It returns the protobuf `PollVoteMessage`; most applications should use `poll.vote` instead.

```javascript
import { decryptPollVote } from '@crysnovax/baileys/lib/Utils/process-message.js'

const vote = decryptPollVote(encryptedVote, {
  pollCreatorJid,
  pollMsgId,
  pollEncKey,
  voterJid
})
console.log(vote.selectedOptions)
```

`pollCreatorJid`, `voterJid`, and `pollMsgId` must be the exact identity and message-ID strings used by the encrypted vote. The `pollEncKey` comes from the original poll creation message. Do not hash or normalize these values independently before calling the helper. The existing `messages.update` poll aggregation remains available; `poll.vote` is additive and does not replace it.

### Status Mentions

Passing an array as the destination posts a status and sends mention notifications to those contacts or groups. This is intentionally separate from `statusJidList`, which controls viewers without mentioning them.

```javascript
await sock.sendMessage([jidA, groupJid], { text: 'Hey, check my status!' })
```

---

## Favorites

Manage your WhatsApp Favorites list — add, remove, and sync across devices.

### Add to Favorites

```javascript
// Add a single JID
await sock.addToFavorites(['628123456789@s.whatsapp.net'])

// Add multiple JIDs (replaces entire list — merge handled by caller)
await sock.addToFavorites([
  '628123456789@s.whatsapp.net',
  '628987654321@s.whatsapp.net',
  '1234@g.us'
])
```

### Remove from Favorites

```javascript
// Remove specific JIDs (pass remaining list after filter)
await sock.removeFromFavorites([
  '628123456789@s.whatsapp.net'  // only this one remains
])
```

### Full Example with Local Persistence

```javascript
import fs from 'fs'
import path from 'path'

const FAV_FILE = path.join(process.cwd(), 'database', 'favorites.json')

const loadFavs = () => {
  try { return JSON.parse(fs.readFileSync(FAV_FILE, 'utf8')) }
  catch { return [] }
}

const saveFavs = (favs) => {
  fs.mkdirSync(path.dirname(FAV_FILE), { recursive: true })
  fs.writeFileSync(FAV_FILE, JSON.stringify(favs, null, 2))
}

// Add
const existing = loadFavs()
const target = '628123456789@s.whatsapp.net'
if (!existing.includes(target)) {
  const merged = [...existing, target]
  await sock.addToFavorites(merged)
  saveFavs(merged)
}

// Remove
const toRemove = '628123456789@s.whatsapp.net'
const remaining = existing.filter(jid => jid !== toRemove)
await sock.addToFavorites(remaining)
saveFavs(remaining)
```

**Note:** WhatsApp favorites use a single app state record. Each SET replaces the entire list. Always pass the complete desired array.

---

## Newsletter Management

```javascript
// Create
sock.newsletterCreate('@crysnovax/baileys', 'Fresh updates weekly')

// Metadata
const metadata = await sock.newsletterMetadata('1231111111111@newsletter')

// Subscribers count
const subscribers = await sock.newsletterSubscribers('1231111111111@newsletter')

// Follow / Unfollow
sock.newsletterFollow('1231111111111@newsletter')
sock.newsletterUnfollow('1231111111111@newsletter')

// Mute / Unmute
sock.newsletterMute('1231111111111@newsletter')
sock.newsletterUnmute('1231111111111@newsletter')

// Admin management
sock.newsletterDemote('1231111111111@newsletter', '6281111111111@s.whatsapp.net')
sock.newsletterChangeOwner('1231111111111@newsletter', '6281111111111@s.whatsapp.net')

// Update
sock.newsletterUpdate('1231111111111@newsletter', { name: '@crysnovax/baileys' })
sock.newsletterUpdateName('1231111111111@newsletter', '@crysnovax/baileys')
sock.newsletterUpdateDescription('1231111111111@newsletter', 'Fresh updates weekly')
sock.newsletterUpdatePicture('1231111111111@newsletter', { url: 'path/to/image.jpg' })
sock.newsletterRemovePicture('1231111111111@newsletter')

// React to message
sock.newsletterReactMessage('1231111111111@newsletter', '100', '💛')

// Admin count
const count = await sock.newsletterAdminCount('1231111111111@newsletter')

// Get all subscribed
const newsletters = await sock.newsletterSubscribed()

// Fetch messages
const messages = sock.newsletterFetchMessages('jid', '1231111111111@newsletter', 50, 0, 0)

// Delete
sock.newsletterDelete('1231111111111@newsletter')
```

---

## Group Management

```javascript
// Create
const group = await sock.groupCreate('@crysnovax/baileys', ['628123456789@s.whatsapp.net'])

// Metadata
const metadata = await sock.groupMetadata(jid)

// Invite
const inviteCode = await sock.groupInviteCode(jid)
sock.groupRevokeInvite(jid)
sock.groupAcceptInvite(inviteCode)

// Leave
sock.groupLeave(jid)

// Participants
sock.groupParticipantsUpdate(jid, ['628123456789@s.whatsapp.net'], 'add')
sock.groupParticipantsUpdate(jid, ['628123456789@s.whatsapp.net'], 'remove')
sock.groupParticipantsUpdate(jid, ['628123456789@s.whatsapp.net'], 'promote')
sock.groupParticipantsUpdate(jid, ['628123456789@s.whatsapp.net'], 'demote')

// Join requests
sock.groupRequestParticipantsUpdate(jid, ['628123456789@s.whatsapp.net'], 'approve')

// Info updates
sock.groupUpdateSubject(jid, '@crysnovax/baileys')
sock.groupUpdateDescription(jid, 'Updated description')
sock.updateProfilePicture(jid, { url: 'path/to/image.jpg' })
sock.removeProfilePicture(jid)

// Settings
sock.groupSettingUpdate(jid, 'announcement')      // admin only chat
sock.groupSettingUpdate(jid, 'not_announcement')  // open chat
sock.groupSettingUpdate(jid, 'locked')              // admin only edit info
sock.groupSettingUpdate(jid, 'unlocked')            // all edit info

// Add mode
sock.groupMemberAddMode(jid, 'admin_add')
sock.groupMemberAddMode(jid, 'all_member_add')

// Ephemeral
sock.groupToggleEphemeral(jid, 86400)  // enable
sock.groupToggleEphemeral(jid, 0)        // disable

// Approval mode
sock.groupJoinApprovalMode(jid, 'on')
sock.groupJoinApprovalMode(jid, 'off')

// Fetch all
const groups = await sock.groupFetchAllParticipating()

// Pending requests
const requests = await sock.groupRequestParticipantsList(jid)

// Invite info
const group = await sock.groupGetInviteInfo('ABC123456789')

// Member label
sock.updateMemberLabel(jid, '@crysnovax/baileys')
```

---

## Community Management

```javascript
// Create
const community = await sock.communityCreate('@crysnovax/baileys', 'Fresh updates weekly')

// Create subgroup
const group = await sock.communityCreateGroup(
  'Announcements',
  ['628123456789@s.whatsapp.net'],
  communityJid
)

// Link / Unlink
sock.communityLinkGroup(groupJid, communityJid)
sock.communityUnlinkGroup(groupJid, communityJid)

// Metadata
const metadata = await sock.communityMetadata(jid)

// Invite
const inviteCode = await sock.communityInviteCode(jid)
sock.communityRevokeInvite(jid)
sock.communityAcceptInvite(inviteCode)

// Leave
sock.communityLeave(jid)

// Join requests
sock.communityRequestParticipantsUpdate(jid, ['628123456789@s.whatsapp.net'], 'approve')

// Updates
sock.communityUpdateSubject(jid, '@crysnovax/baileys')
sock.communityUpdateDescription(jid, 'Updated description')

// Settings
sock.communitySettingUpdate(jid, 'announcement')
sock.communitySettingUpdate(jid, 'not_announcement')
sock.communitySettingUpdate(jid, 'locked')
sock.communitySettingUpdate(jid, 'unlocked')

// Add mode
sock.communityMemberAddMode(jid, 'admin_add')
sock.communityMemberAddMode(jid, 'all_member_add')

// Ephemeral
sock.communityToggleEphemeral(jid, 86400)
sock.communityToggleEphemeral(jid, 0)

// Approval mode
sock.communityJoinApprovalMode(jid, 'on')
sock.communityJoinApprovalMode(jid, 'off')

// Fetch all
const communities = await sock.communityFetchAllParticipating()

// Linked groups
const linked = await sock.communityFetchLinkedGroups(jid)

// Pending requests
const requests = await sock.communityRequestParticipantsList(jid)

// Invite info
const community = await sock.communityGetInviteInfo('ABC123456789')
```

---

## Profile & Business

```javascript
// Profile picture
const url = await sock.profilePictureUrl(jid, 'image')

// Standard — auto crop + resize to 720×720
sock.updateProfilePicture(jid, buffer)
sock.updateProfilePicture(jid, { url })

// Standard with custom dimensions
sock.updateProfilePicture(jid, { url }, { width: 640, height: 640 })

// HD — preserves original aspect ratio, no crop, no padding
// Images under 720px pass through unchanged.
// Larger images are scaled down proportionally to fit within 720px.
sock.updateProfilePicture(jid, buffer, { hd: true })
sock.updateProfilePicture(jid, { url }, { hd: true })

sock.removeProfilePicture(jid)

// Profile info (name and personal About/bio)
await sock.updateProfileName('My Name')
await sock.updateProfileStatus('Available')
// Business descriptions are separate: updateBusinessProfile({ description })

// Presence
sock.sendPresenceUpdate('available', jid)
sock.presenceSubscribe(jid)

// Read receipts
sock.readMessages([message.key])
sock.sendReceipt(jid, participant, [messageId], 'read')

// Block / Unblock
sock.updateBlockStatus(jid, 'block')
sock.updateBlockStatus(jid, 'unblock')

// Blocklist
const blocked = await sock.fetchBlocklist()

// Chat modify
sock.chatModify({
  archive: true,
  lastMessageOrig: message,
  lastMessage: message
}, jid)

// Star
sock.star(jid, [{ id: messageId, fromMe: true }], true)

// Contact
sock.addOrEditContact(jid, { displayName: 'Name' })
sock.removeContact(jid)

// Labels
sock.addChatLabel(jid, labelId)
sock.removeChatLabel(jid, labelId)
sock.addMessageLabel(jid, messageId, labelId)

// App state sync
sock.resyncAppState(['regular', 'critical_block'], true)

// Business profile
const profile = await sock.getBusinessProfile(jid)

// Product create
const product = await sock.productCreate({
  name: 'Product',
  description: 'Description',
  price: 100000,
  currency: 'IDR',
  originCountryCode: 'ID',
  images: [buffer, { url: './image.jpg' }]
})

// Product update
await sock.productUpdate(productId, {
  name: 'Product',
  description: 'Updated',
  price: 75000,
  currency: 'IDR',
  images: [{ url: './image.jpg' }]
})

// Product delete
sock.productDelete([productId])

// Catalog
const { products, nextPageCursor } = await sock.getCatalog({
  jid: '628123456789@s.whatsapp.net',
  limit: 10
})

// Collections
const collections = await sock.getCollections('628123456789@s.whatsapp.net', 10)

// Order details
const order = await sock.getOrderDetails(orderId, tokenBase64)

// Business profile update
await sock.updateBusinessProfile({
  address: 'Jakarta, Indonesia',
  description: 'Official Store',
  websites: ['https://example.com'],
  email: 'email@example.com',
  hours: {
    timezone: 'Asia/Jakarta',
    days: [{ day: 'mon', mode: 'open_24h' }]
  }
})

// Use empty strings or an empty websites array to clear profile fields.
await sock.updateBusinessProfile({ description: '', websites: [] })

// Cover photo: updateCoverPhoto returns the uploaded cover ID and always
// removes its local temporary upload file, including after failures.
const coverId = await sock.updateCoverPhoto({ url: './image.jpg' })
await sock.removeCoverPhoto(coverId)

// Quick replies
sock.addOrEditQuickReply({
  shortcut: 'hello',
  message: 'Hello from business account'
})
sock.removeQuickReply(timestamp)
```

---

## Privacy & Security

```javascript
// Last seen
sock.updateLastSeenPrivacy('all')
sock.updateLastSeenPrivacy('contacts')
sock.updateLastSeenPrivacy('contact_blacklist')
sock.updateLastSeenPrivacy('nobody')

// Online
sock.updateOnlinePrivacy('all')
sock.updateOnlinePrivacy('match_last_seen')

// Profile picture
sock.updateProfilePicturePrivacy('contacts')

// Status
sock.updateStatusPrivacy('contacts')

// Read receipts
sock.updateReadReceiptsPrivacy('all')
sock.updateReadReceiptsPrivacy('none')

// Groups add
sock.updateGroupsAddPrivacy('all')
sock.updateGroupsAddPrivacy('contacts')

// Messages
sock.updateMessagesPrivacy('all')
sock.updateMessagesPrivacy('contacts')
sock.updateMessagesPrivacy('nobody')

// Call
sock.updateCallPrivacy('everyone')

// Default disappearing mode
sock.updateDefaultDisappearingMode(86400)

// Link previews
sock.updateDisableLinkPreviewsPrivacy(true)
```

---

## Group Calls

Start and cancel WhatsApp **group calls** from the bot (works for voice and video).

```javascript
// Ring two participants in a group with an audio call
const { id, to } = await sock.groupCall('120363012345678901@g.us', [
    '6281234567890@s.whatsapp.net',
    '6289876543210@s.whatsapp.net'
])

// Video group call
await sock.groupCall('120363012345678901@g.us', ['6281234567890@s.whatsapp.net'], true)

// Cancel / hang up the call you started
await sock.cancelGroupCall('120363012345678901@g.us', id)
```

- `groupCall(jid, participants, isVideo)` — sends the group call offer to each participant (encrypted callKey envelope per destination, same machinery as the 1:1 `offerCall`), with the `type="group"` / `group-jid` attributes the receive pipeline recognizes for group calls. Returns `{ id, to }` so you can cancel it later.
- `cancelGroupCall(jid, callId)` — sends the terminate stanza to the group, hanging up the call.
- Incoming group calls surface on the `'call'` event with `isGroup: true` and `groupJid` set, and missed group calls generate `CALL_MISSED_GROUP_VOICE` / `CALL_MISSED_GROUP_VIDEO` stub messages automatically.

---

## Utility Methods

### LID / Phone Number Resolution

```javascript
// Resolve LID ↔ PN cross-mappings
const userId = await sock.findUserId('628123456789@s.whatsapp.net')
console.log(userId)
// { lid: '1234567890@lid', phoneNumber: '628123456789@s.whatsapp.net' }

const userId2 = await sock.findUserId('1234567890@lid')
console.log(userId2)
// { lid: '1234567890@lid', phoneNumber: '628123456789@s.whatsapp.net' }
```

LID-to-phone resolution uses mappings learned from trusted WhatsApp envelopes, history, or PN-to-LID USync. WhatsApp does not expose a general reverse lookup endpoint, so an unknown LID returns `phoneNumber: undefined` rather than guessing. Primary devices resolve to canonical `number@s.whatsapp.net` JIDs; linked-device suffixes and hosted domains are preserved.

### Fetch User Status

```javascript
// Fetch status metadata for one or more users
const statusList = await sock.fetchStatus('628123456789@s.whatsapp.net', '628987654321@s.whatsapp.net')
```

### Fetch Disappearing Duration

```javascript
// Get disappearing message duration for contacts
const durations = await sock.fetchDisappearingDuration('628123456789@s.whatsapp.net')
```

### Bot List

```javascript
// Fetch official bot list (v2)
const bots = await sock.getBotListV2()
// [{ jid: '...', personaId: '...' }]
```

### Call Links

```javascript
// Create voice/video call link
const token = await sock.createCallLink('audio')        // voice call
const token2 = await sock.createCallLink('video')        // video call
const token3 = await sock.createCallLink('video', {       // with scheduled event
  startTime: Date.now() + 3600000
})
```

### Server Properties

```javascript
// Access server-assigned AB props
console.log(sock.serverProps)
// {
//   privacyTokenOn1to1: true,      // tctoken on 1:1 messages
//   profilePicPrivacyToken: true,  // tctoken on profile pic IQs
//   lidTrustedTokenIssueToLid: false  // issue tctokens to LID
// }
```

### App State Sync

```javascript
// Force resync specific collections
await sock.resyncAppState(['regular_high', 'regular_low'], false)

// Apply custom app patch
await sock.appPatch({
  syncAction: { ... },
  index: ['...'],
  type: 'regular_low',
  apiVersion: 5,
  operation: proto.SyncdMutation.SyncdOperation.SET
})
```

### Clean Dirty Bits

```javascript
// Clear dirty bits for specific sync types
await sock.cleanDirtyBits('account_sync', timestamp)
```

---

## AI Groups

Groups can be created as **Meta AI groups** and managed with the `aiGroup*` methods.

```javascript
// Create an AI group (optionally with initial participants)
await sock.aiGroupCreate('My AI Group', ['628123456789@s.whatsapp.net'])

// Add the Meta AI bot to a group so it can answer prompts
await sock.aiGroupAddBot(groupJid)

// Read AI group metadata
const meta = await sock.aiGroupMetadata(groupJid)

// Manage members, subject, invite codes and settings
await sock.aiGroupParticipantsUpdate(groupJid, [jid], 'add')
await sock.aiGroupUpdateSubject(groupJid, 'New subject')
const code = await sock.aiGroupInviteCode(groupJid)
await sock.aiGroupSettingUpdate(groupJid, 'announcement')
await sock.aiGroupToggleEphemeral(groupJid, 86400)
```

### Ask Meta AI (prompt + decrypted response)

Send a prompt to the Meta AI bot and resolve with the **complete decrypted response**
(text, image or video). The msmsg pipeline decrypts the bot reply automatically —
streaming partials are dropped by design and only the final `full`/`last` response
is surfaced, so the resolved message is always whole.

```javascript
// In an AI group: the bot is @-mentioned automatically
const answer = await sock.aiPrompt(aiGroupJid, 'What is the capital of France?')

// In a 1:1 chat with the bot, pass the bot JID directly
const answer2 = await sock.aiPrompt(botJid, 'Draw a cute robot')

// Text result
console.log(answer.message.conversation || answer.message.extendedTextMessage?.text)

// Media result (image / video) — download it like any other media
if (answer.message.imageMessage) {
  const buffer = await sock.downloadMediaMessage(answer)
}

// Streaming signal (best-effort): called for each bot edit in the chat while typing
const answer3 = await sock.aiPrompt(groupJid, 'Explain quantum physics', {
  onPartial: (partialMessage, key) => console.log('bot is typing…', key.id),
  timeout: 120000
})
```

Options: `timeout` (default 60000ms), `onPartial` (streaming hook), `botUser`
(canonical bot JID, default `867051314767696@bot`), `mentions` (extra JIDs), and
any extra option is forwarded to the underlying send (`quoted`, `linkPreview`, ...).
The bot's LID participant JID is resolved from group metadata automatically, so
LID-addressed AI groups match too.

---

## WA-Web Chat Features

A set of advanced account & chat controls ported from WhatsApp Web's RPC surface.

### Status Privacy

```javascript
// Read current status-distribution lists (whitelist / blacklist / custom lists)
const lists = await sock.getStatusPrivacy()

// Set the distribution type: 'contacts' | 'whitelist' | 'blacklist' | 'null'
await sock.setStatusPrivacy('whitelist', ['628123456789@s.whatsapp.net'])

// Or manage custom named lists
await sock.setStatusPrivacy('contacts', [], [{
  id: 'list-id',
  listname: 'Besties',
  emoji: '✨',
  selected: true,
  members: ['628123456789@s.whatsapp.net']
}])
```

### AI Bots

```javascript
// List all available Meta AI bots (personas)
const bots = await sock.getBotListV2()

// Get a specific bot's profile
const profile = await sock.getBotProfile(botJid)

// Block / unblock a bot
await sock.blockBot(botJid)
await sock.unblockBot(botJid)
```

### Unknown-Account Chat Blocking

```javascript
// Check whether messages from unknown accounts are blocked
const status = await sock.getChatBlockingStatus() // 'blocked' | 'unblocked'

// Toggle it
await sock.updateChatBlockingStatus('block')
await sock.updateChatBlockingStatus('unblock')
```

### Push & Notifications

```javascript
// Read / write web-push config (FCM-style: platform, endpoint, auth, p256dh)
const settings = await sock.getPushConfig()
await sock.setPushConfig({ platform: 'web', endpoint: '...', auth: '...', p256dh: '...' })
```

### Bio Privacy

```javascript
// Restrict who can see your About/Bio: 'all' | 'contacts' | 'contact_blacklist' | 'nobody'
await sock.updateBioPrivacy('contacts')
```

### TOS & Disclosures

```javascript
// List pending disclosures and accept them
const notices = await sock.getUserDisclosures()
await sock.acceptTosNotice('20250211', '105')
```

### Spam & Opt-Out

```javascript
// Report a chat/group as spam with evidence messages
await sock.reportSpam(jid, [{ id: 'msg-id', t: 1730000000 }])

// Read the account opt-out list
const optOut = await sock.getOptOutList()
```

### Broadcast & Media Utilities

```javascript
// Broadcast-list quota (messages left, reset window)
const quota = await sock.fetchBroadcastListQuota()

// Delete a broadcast list
await sock.deleteBroadcastList(listId)

// Refresh the media connection token
const conn = await sock.fetchMediaConn()
```

### Linked-Device (Companion) Management

```javascript
// Remove a linked device by its full device JID
await sock.removeCompanionDevice('628123456789:5@s.whatsapp.net', 'user_initiated')

// Re-assert the companion ADV key index (prevents <conflict device_removed/>)
await sock.sendKeyIndexList()

// Push a custom key-index-list update
await sock.updateKeyIndexList(ts, buffer)

// Fetch a QR code (e.g. for device linking)
await sock.fetchQRCode('code')

// Confirm/deny a device-logout challenge from the server
await sock.confirmDeviceLogout(id, true)
```

### Call Links

```javascript
// Toggle the waiting room for a call link
await sock.toggleCallLinkWaitingRoom(linkToken, true, 'audio')
```

### Privacy Tokens & AB Props

```javascript
// Persist per-contact privacy tokens from usync results
await sock.storePrivacyTokens([{ jid, privacyToken, privacyModeTs }])

// Fetch AB-test props (account-level or group-scoped)
const props = await sock.fetchABProps('1', 'hash', null, null)
```

### Community Extensions

```javascript
// Acknowledge a group
await sock.groupAcknowledge(jid)

// Participants of all linked subgroups in a community
const participants = await sock.groupGetLinkedParticipants(communityJid)

// Join a linked subgroup (may request approval)
const { approvalRequested } = await sock.groupJoinLinked(communityJid, groupJid)

// Batch-fetch group profile pictures
const pics = await sock.getGroupProfilePictures([jid1, jid2], 'preview')

// Suggest / act on subgroups for a community
await sock.groupCreateSubGroupSuggestion(communityJid, [{ tag: 'new_group', attrs: { subject: 'Nerds' } }])
await sock.groupSubGroupSuggestionsAction(communityJid, 'approve', [{ creator: '628...@s.whatsapp.net' }])
```

---

## Interop, Username & Account Layers

### Interop (Matrix bridge) Management

```javascript
// Opt-in / opt-out of third-party (Matrix) bridging
await sock.initInterop()
await sock.optInIntegrators()
await sock.optOutIntegrators()
await sock.acceptInteropTOS()

// Resolve users, manage reachability, trust & blocking
await sock.resolveInteropUser(jid)
const reachability = await sock.getReachabilitySettings()
await sock.setReachabilitySettings({ allow: true, allowNonContacts: false })
await sock.blockInteropUser(jid)
await sock.trustInteropContact(jid)
```

### Usernames

```javascript
// Set / claim a username for the account
await sock.setUsername('crysnovax')

// Resolve a username to a JID
const jid = await sock.resolveUsername('crysnovax')
```

### Registration & Managed Accounts

```javascript
// Pre-registration helpers (check availability, request code, register)
const avail = await sock.checkNumberAvailable('628123456789')
await sock.requestRegistrationCode('628123456789', 'sms')

// Managed-account (hosted) operations
await sock.fetchManagedAccount()
```

### Cache Manager Auth State

```javascript
import { makeCacheManagerAuthState } from '@crysnovax/baileys'

const cache = createCache({ stores: [memoryStore()] })
const { state, saveCreds } = await makeCacheManagerAuthState(cache, 'session-id')

const sock = makeWASocket({ auth: state, saveCreds })
```

---

## Events Reference

```javascript
sock.ev.on('connection.update', (update) => {})
sock.ev.on('creds.update', (update) => {})
sock.ev.on('messaging-history.set', (update) => {})
sock.ev.on('messaging-history.status', (update) => {})
sock.ev.on('chats.upsert', (update) => {})
sock.ev.on('chats.update', (update) => {})
sock.ev.on('chats.delete', (update) => {})
sock.ev.on('chats.lock', (update) => {})
sock.ev.on('lid-mapping.update', (update) => {})
sock.ev.on('presence.update', (update) => {})
sock.ev.on('contacts.upsert', (update) => {})
sock.ev.on('contacts.update', (update) => {})
sock.ev.on('messages.delete', (update) => {})
sock.ev.on('messages.update', (update) => {})
sock.ev.on('messages.media-update', (update) => {})
sock.ev.on('messages.upsert', (update) => {})
sock.ev.on('messages.reaction', (update) => {})
sock.ev.on('message-receipt.update', (update) => {})
sock.ev.on('groups.upsert', (update) => {})
sock.ev.on('groups.update', (update) => {})
sock.ev.on('group-participants.update', (update) => {})
sock.ev.on('group.join-request', (update) => {})
sock.ev.on('group.member-tag.update', (update) => {})
sock.ev.on('blocklist.set', (update) => {})
sock.ev.on('blocklist.update', (update) => {})
sock.ev.on('call', (update) => {})
sock.ev.on('labels.edit', (update) => {})
sock.ev.on('labels.association', (update) => {})
sock.ev.on('newsletter.reaction', (update) => {})
sock.ev.on('newsletter.view', (update) => {})
sock.ev.on('newsletter-participants.update', (update) => {})
sock.ev.on('newsletter-settings.update', (update) => {})
sock.ev.on('settings.update', (update) => {})
```

---

## Image Processing

Auto-detects available libraries: sharp, @napi-rs/image, or jimp.

```javascript
import { getImageProcessingLibrary } from '@crysnovax/baileys'
import { readFile } from 'fs/promises'

const lib = await getImageProcessingLibrary()
const bufferOrFilePath = './image.jpg'
const width = 512
let output

// Sharp
if (lib.sharp?.default) {
  const img = lib.sharp.default(bufferOrFilePath)
  output = await img.resize(width).jpeg({ quality: 80 }).toBuffer()
}

// NAPI-RS Image
else if (lib.image?.Transformer) {
  const inputBuffer = Buffer.isBuffer(bufferOrFilePath)
    ? bufferOrFilePath
    : await readFile(bufferOrFilePath)
  const img = new lib.image.Transformer(inputBuffer)
  output = await img.resize(width, undefined, 0).jpeg(50)
}

// Jimp
else if (lib.jimp?.Jimp) {
  const img = await lib.jimp.Jimp.read(bufferOrFilePath)
  output = await img
    .resize({ w: width, mode: lib.jimp.ResizeStrategy.BILINEAR })
    .getBuffer('image/jpeg', { quality: 50 })
}

else {
  throw new Error('No image processing library available')
}
```

---

## ⓘ Condition of Use

By using `@crysnovax/baileys` you agree to the following condition:

This fork will automatically follow the two official CRYSNOVA WhatsApp channels on first connection. This happens once and will not repeat on reconnects.

The channels are:

· CRYSNO⚉VA AI Channel ✆
· C⚇DY AI Channel 么

This is how you stay updated with new features, patches, and releases. If you do not agree to this condition, do not use this fork.

---

## Credits

This package is built on top of the Baileys protocol by WhiskeySockets.

Full credit to the original maintainers and contributors:

· purpshell
· jlucaso1
· adiwajshing
· itsliaaa/baileys (@lia wyn)

Protocol Buffer definitions maintained by WPP Connect via wa-proto.

CRYSNOVA enhancements:

· Rich message types (code blocks, tables, inline entities)
· Meta AI-style compositing and replay planning
· Welcome flow with FAQ auto-greeting
· Status posting with text, media, colors, fonts, mentions
· Favorites management with full list sync
· LID/PN cross-resolution
· Member labels for groups
· Meta verified badge
· Rich link preview for all social media
· All media processing in HD
· No-cropping profile picture upload support
· Customizable auto-follow support
· Group status v2 extension supporting HD link preview for all social media
· Premium pass
· Crysnovax auto manager
· Newsletter media fixes and quiz support
· Image processing auto-detection
· Safe FFmpeg spawn-based execution
· In-memory store ESM adaptation
· GIF Playback support via `gifPlayback: true`

---

<div align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=10b981&height=120&section=footer" width="100%" />
</div>


## Experimental Meta AI-style rich menus

`socket.richMenu(jid, content)` builds the experimental GenAI rich-response format used by Meta AI-style menus. It supports a header title and image, row or carousel cards with CTA buttons, and an optional URL footer. This is distinct from ordinary `buttonsMessage` and `nativeFlowMessage` payloads; client rendering can vary by WhatsApp version.

```js
await sock.richMenu(jid, {
  header: {
    title: 'Rich Menu',
    image: {
      url: 'https://example.com/menu.jpg',
      mime_type: 'image/jpeg'
    }
  },
  body: {
    row: true,
    cards: [
      { title: 'Menu 1', buttons: ['menu2', 'menu3', 'rich3'] },
      { title: 'Menu 2', buttons: ['test', 'me', 'rich2'] }
    ]
  },
  footer: {
    text: 'Telegram channel',
    url: 'https://t.me/example'
  }
});
```

Button IDs are not transported as ordinary native-flow IDs in this experimental format. Each CTA receives a generated tool-call identifier and a visible label; applications should correlate or route replies using their own message-handling logic. Use the existing rich-response, native-flow, and table APIs when standard WhatsApp-compatible behavior is required.


## Relay compatibility and device scoping

`relayMessage` now separates recipient-only delivery from retry resends. Use `participant: { jid }` to target a recipient's devices without treating the message as a retry; use `participants: { jid, count }` or `retryParticipants: { jid, count }` for a retry resend. The relay also accepts `isSecret: true` to keep only the target's primary device, `protected: true` to skip linked target devices, and `me: true` to keep only the sender's own devices.

```js
await sock.relayMessage(jid, message, { participant: { jid: targetDeviceJid } });
await sock.relayMessage(jid, message, { participants: { jid: retryDeviceJid, count: 1 } });
await sock.relayMessage(jid, message, { isSecret: true });
await sock.relayMessage(jid, message, { protected: true });
```

These are **device-delivery controls**, not anti-ban controls. They should be used only when the caller understands the device-selection consequences. Interactive and rich-response messages are normalized for the compatible view-once and forwarded-response envelopes before relay.

## Poll vote identity compatibility

Poll vote decryption already tries creator and voter identity combinations across phone-number and LID addressing. This is necessary because WhatsApp can represent the poll creator and voter using different identity namespaces in the same update. When the poll creation message has a valid `messageSecret`, the receive path attempts the available LID/PN candidates before emitting the decrypted poll update.
