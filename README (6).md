# Plogme

**Plogme** is a Node.js WhatsApp Web client built on the Baileys protocol implementation. It provides authentication, messaging, media uploads, groups, status updates, interactive messages, albums, rich responses, Meta AI-style envelopes, HTML screens, and selected WhatsApp Web protocol features.

> [!IMPORTANT]
> Plogme is a protocol library. WhatsApp server behavior and client rendering can change without notice. Rich responses, HTML primitives, native flows, reels, carousels, and experimental protocol fields may render differently across WhatsApp versions.

[![npm version](https://img.shields.io/npm/v/plogme?style=flat-square)](https://www.npmjs.com/package/plogme)
[![Node.js](https://img.shields.io/badge/Node.js-%3E%3D20-10b981?style=flat-square&logo=node.js)](https://nodejs.org/)
[![Tests](https://img.shields.io/badge/tests-30%20passing-10b981?style=flat-square)](https://github.com/crysnovax/baileys)

## Contents

1. [Installation](#installation)
2. [Quick start](#quick-start)
3. [Authentication and pairing](#authentication-and-pairing)
4. [Sending messages](#sending-messages)
5. [Media and albums](#media-and-albums)
6. [Interactive messages](#interactive-messages)
7. [Rich messages](#rich-messages)
8. [Rich menus, reels, and grids](#rich-menus-reels-and-grids)
9. [HTML screens and built-in apps](#html-screens-and-built-in-apps)
10. [Status updates](#status-updates)
11. [Groups and group history](#groups-and-group-history)
12. [Reports and moderation](#reports-and-moderation)
13. [Experimental bot metadata](#experimental-bot-metadata)
14. [WhatsApp flows and web views](#whatsapp-flows-and-web-views)
15. [View-once utilities](#view-once-utilities)
16. [Connection handling](#connection-handling)
17. [Security and removed automatic behavior](#security-and-removed-automatic-behavior)
18. [Development](#development)
19. [License and support](#license-and-support)

## Installation

```bash
npm install plogme
```

The package currently requires **Node.js 20 or newer**.

To install the development branch directly from GitHub:

```bash
npm install github:crysnovax/baileys
```

Plogme is an ES module package. Use `import` syntax in Node.js projects configured with `"type": "module"`.

> [!WARNING]
> Never commit authentication state, private keys, `.env` files, or downloaded session data. Add your session directory to `.gitignore`.

## Quick start

This is the smallest complete connection example. It saves credentials, reconnects after temporary disconnects, and stops reconnecting after logout.

```js
import makeWASocket, {
  DisconnectReason,
  useMultiFileAuthState
} from 'plogme'
import { Boom } from '@hapi/boom'
import pino from 'pino'

async function connect() {
  const { state, saveCreds } = await useMultiFileAuthState('./session')
  const sock = makeWASocket({
    auth: state,
    logger: pino({ level: 'silent' })
  })

  sock.ev.on('creds.update', saveCreds)

  sock.ev.on('connection.update', ({ connection, lastDisconnect }) => {
    if (connection === 'open') {
      console.log('Connected to WhatsApp')
      return
    }

    if (connection !== 'close') return

    const statusCode = new Boom(lastDisconnect?.error)?.output?.statusCode
    if (statusCode === DisconnectReason.loggedOut) {
      console.error('Logged out. Remove the old session and pair again.')
      return
    }

    console.log('Connection closed; reconnecting')
    connect().catch(console.error)
  })

  return sock
}

await connect()
```

## Authentication and pairing

### Multi-file authentication

`useMultiFileAuthState` is the recommended development setup because credentials and keys are stored as separate files.

```js
const { state, saveCreds } = await useMultiFileAuthState('./session')
const sock = makeWASocket({ auth: state })
sock.ev.on('creds.update', saveCreds)
```

### Pairing code

Request a pairing code after creating the socket. Supply a phone number in international format without `+`, spaces, or punctuation.

```js
const code = await sock.requestPairingCode('15551234567')
console.log('Pairing code:', code)
```

Keep the pairing code private and use it only with the WhatsApp account you control.

## Sending messages

### Text, mentions, and reactions

```js
await sock.sendMessage(jid, { text: 'Hello from Plogme' })

await sock.sendMessage(jid, {
  text: 'Hello @15551234567',
  mentions: ['15551234567@s.whatsapp.net']
})

await sock.sendMessage(jid, {
  react: {
    text: '👍',
    key: message.key
  }
})
```

### WhatsApp Channels

Resolve a Channel invite code or a full WhatsApp Channel invite URL without
automatically following the Channel:

```js
const channel = await sock.newsletterGetInviteInfo(
  'https://whatsapp.com/channel/0029Vb6pe77K0IBn48HLKb38'
)

console.log(channel?.id, channel?.name, channel?.description)
```

React to a Channel post with its Channel JID and `serverId`. Pass `null`,
`undefined`, or an empty string to remove the reaction:

```js
await sock.newsletterReactMessage(
  '120363000000000000@newsletter',
  '12345',
  '👍'
)

await sock.newsletterReactMessage(
  '120363000000000000@newsletter',
  '12345',
  null
)
```

### Chat themes and animated wallpapers

Send a chat-theme protocol message with a color scheme and an animated
wallpaper. `chatThemeId` is normalized to the WhatsApp color-scheme form by
adding `@Tonal`; use `colorSchemeId` when you already have the complete value.
`dimLevel` is a normalized value from `0` to `1`.

```js
await sock.sendMessage(jid, {
  chatTheme: {
    chatThemeId: 'Royal-Blue',
    animatedWallpaper: {
      id: 'wallpaper-1',
      dimLevel: 0.25
    }
  }
})

// Equivalent explicit color-scheme form:
await sock.sendMessage(jid, {
  chatTheme: {
    colorSchemeId: 'Royal-Blue@Tonal',
    animatedWallpaper: {
      animatedWallpaperId: 'wallpaper-1',
      opacity: 25
    }
  }
})
```

### Editing and deleting

```js
await sock.sendMessage(jid, {
  text: 'Updated text',
  edit: message.key
})

await sock.sendMessage(jid, {
  caption: 'Updated media caption',
  edit: message.key
})

await sock.sendMessage(jid, {
  delete: message.key
})
```

### Forwarding

```js
await sock.sendMessage(jid, {
  forward: message,
  force: true
})
```

### Profile name and About status

These methods update the connected account's own profile.

```js
await sock.updateProfileName('Cody AI')
await sock.updateProfileStatus('Building helpful WhatsApp tools')
```

### Events and polls

```js
await sock.sendMessage(jid, {
  event: {
    name: 'Meet and Mingle',
    description: 'A scheduled community event.',
    call: 'audio',
    startDate: new Date(Date.now() + 3600000),
    endDate: new Date(Date.now() + 28800000),
    isCancelled: false,
    isScheduleCall: false,
    extraGuestsAllowed: false,
    location: { name: 'Lagos', degreesLatitude: 6.5244, degreesLongitude: 3.3792 }
  }
})

await sock.sendMessage(jid, {
  poll: {
    name: 'Release preference',
    values: ['Stable', 'Beta'],
    selectableCount: 1,
    endDate: new Date(Date.now() + 28800000),
    hideVoter: false,
    canAddOption: false
  }
})
```

### Forward and edit

```js
await sock.sendMessage(jid, { forward: message, force: true })
await sock.sendMessage(jid, { delete: message.key })
await sock.sendMessage(jid, { text: 'Updated text', edit: message.key })
await sock.sendMessage(jid, { caption: 'Updated caption', edit: message.key })
```

## Media and albums

### Images, video, audio, documents, and stickers

```js
await sock.sendMessage(jid, {
  image: { url: './image.jpg' },
  caption: 'Image caption'
})

await sock.sendMessage(jid, {
  video: { url: './video.mp4' },
  caption: 'Video caption',
  gifPlayback: false,
  ptv: false
})

await sock.sendMessage(jid, {
  audio: { url: './voice.ogg' },
  mimetype: 'audio/ogg; codecs=opus',
  ptt: true
})

await sock.sendMessage(jid, {
  document: { url: './report.pdf' },
  mimetype: 'application/pdf',
  fileName: 'report.pdf',
  caption: 'Monthly report'
})

await sock.sendMessage(jid, {
  sticker: { url: './sticker.webp' }
})
```

Remote URLs, local file paths, and buffers are supported where the selected media helper accepts them.

> [!WARNING]
> A URL must point to a real, playable media file. A URL returning an HTML error page, an empty file, or an invalid video container can produce a WhatsApp media verification or download warning even when the library code is correct.

### GIF playback and video notes

```js
await sock.sendMessage(jid, {
  video: { url: './animation.mp4' },
  gifPlayback: true,
  caption: 'Animated clip'
})

await sock.sendMessage(jid, {
  video: { url: './video-note.mp4' },
  ptv: true
})
```

### Albums

Albums now use an AIRich vertical unified-response layout. Provide at least two image or video items; the client receives one forwarded rich message with one large media item per row, matching WhatsApp's full-width stacked album presentation. The old `albumMessage` parent/child collection and compact thumbnail grid are no longer generated by `sendAlbumMessage()`.

```js
await sock.sendAlbumMessage(jid, [
  { type: 'image', data: { url: 'https://example.com/one.jpg', width: 1200, height: 800 } },
  {
    image: {
      sourceUrl: 'https://example.com/two-preview.jpg',
      imageHighResUrl: 'https://example.com/two.jpg',
      darkModePreviewUrl: 'https://example.com/two-dark-preview.jpg',
      darkModeHighResUrl: 'https://example.com/two-dark.jpg',
      width: 1200,
      height: 800
    }
  },
  {
    type: 'video',
    data: {
      url: 'https://example.com/three.mp4',
      mimeType: 'video/mp4',
      duration: 12,
      thumbnailUrl: 'https://example.com/three.jpg'
    }
  }
])
```

The general `sendMessage` album form delegates to the same full-width vertical album implementation:

```js
await sock.sendMessage(jid, {
  album: [
    { image: { url: 'https://example.com/one.jpg' } },
    { image: { url: 'https://example.com/two.jpg' } }
  ]
})
```

`sendAlbumMessage()` accepts fetchable image and video URLs. Local `Buffer`/file inputs are not uploaded by this rich album API. The lower-level `prepareGridImageMessage()` helper remains available when a compact `GRID_IMAGE` layout is specifically required; `sendAlbumMessage()` itself uses a full-width `GenAIVStackLayoutViewModel` presentation for both images and videos. Image primitives explicitly request full-width presentation and carry preview/full URL fallbacks.

### Video grids

Video grids use the supported `GenAIVideoPrimitive` inside a unified-response `GenAIGridLayoutViewModel`. They are exposed separately from `sendAlbumMessage()` because WhatsApp's protobuf does not define a `GRID_VIDEO` submessage type. Provide at least two publicly fetchable video URLs:

```js
await sock.sendVideoGridMessage(jid, [
  {
    url: 'https://example.com/first.mp4',
    title: 'First clip',
    thumbnailUrl: 'https://example.com/first.jpg',
    progressiveUrls: ['https://cdn.example.com/first-720p.mp4']
  },
  {
    url: 'https://example.com/second.mp4',
    title: 'Second clip',
    thumbnailUrl: 'https://example.com/second.jpg',
    progressiveUrls: ['https://cdn.example.com/second-720p.mp4']
  }
])
```

The video-grid API is intentionally URL-based; it does not upload local buffers or files. `sendVideoGridMessage()` also uses the rich-message edit relay, so supported clients receive the unified response without an initial Download-only placeholder.

## Interactive messages

### Buttons and lists

```js
await sock.sendMessage(jid, {
  text: 'Choose an option',
  footer: 'Plogme',
  buttons: [
    { text: 'Confirm', id: 'confirm' },
    { text: 'Cancel', id: 'cancel' }
  ]
})

await sock.sendMessage(jid, {
  text: 'Choose a category',
  title: 'Categories',
  buttonText: 'Open list',
  sections: [
    {
      title: 'Available categories',
      rows: [
        { title: 'Support', description: 'Get help', rowId: 'support' },
        { title: 'News', description: 'Read updates', rowId: 'news' }
      ]
    }
  ]
})
```

Buttons can also carry media and a native-flow selector:

```js
await sock.sendMessage(jid, {
  image: { url: 'https://example.com/image.jpg' },
  caption: 'Interactive buttons',
  footer: 'CODY AI',
  buttons: [
    { text: 'Rating', id: '#Rating' },
    {
      text: 'Select',
      sections: [{
        title: 'Available options',
        rows: [
          { header: '', title: 'Secret Ingredient', description: '', id: '#SecretIngredient' },
          { header: '', title: 'Coupon', description: '', id: '#CouponCode' }
        ]
      }]
    }
  ]
})
```

### Native flow

```js
await sock.sendWhatsAppFlow(jid, {
  flowId: 'your-flow-id',
  flowToken: 'your-flow-token',
  cta: 'Open form',
  text: 'Complete this form'
})
```

For a rich URL call-to-action:

```js
await sock.sendRichWebview(jid, {
  url: 'https://example.com/app',
  text: 'Open the app',
  buttonText: 'Open',
  useWebview: true
})
```

The lower-level `sendMessage` form also supports native-flow options, offer metadata, calls, copy actions, and webviews:

```js
await sock.sendMessage(jid, {
  image: { url: 'https://example.com/image.jpg' },
  caption: 'Interactive options',
  footer: 'CODY AI',
  optionText: 'Select options',
  optionTitle: 'Select options',
  offerText: 'New coupon',
  offerCode: 'SAVE20',
  offerUrl: 'https://example.com/coupon',
  offerExpiration: Date.now() + 3600000,
  nativeFlow: [
    { text: 'Greeting', id: '#Greeting', icon: 'review' },
    { text: 'Call', call: '2348000000000' },
    { text: 'Copy', copy: 'CODY AI' },
    { text: 'Source', url: 'https://example.com', useWebview: true },
    {
      text: 'Select',
      icon: 'default',
      sections: [{
        title: 'Available options',
        rows: [{ header: '', title: 'Coupon', description: '', id: '#CouponCode' }]
      }]
    }
  ],
  interactiveAsTemplate: false
})
```

Carousel cards and an audio footer are supported by the same `sendMessage` shape:

```js
await sock.sendMessage(jid, {
  text: 'Choose a service',
  footer: 'CODY AI',
  cards: [
    {
      image: { url: 'https://example.com/service-a.jpg' },
      caption: 'Service A',
      footer: 'CODY AI',
      nativeFlow: [{ text: 'Open source', url: 'https://example.com/a', useWebview: true }]
    },
    {
      image: { url: 'https://example.com/service-b.jpg' },
      caption: 'Service B',
      footer: 'CODY AI',
      offerText: 'New coupon',
      offerCode: 'SAVE20',
      offerUrl: 'https://example.com/coupon',
      offerExpiration: Date.now() + 3600000,
      nativeFlow: [{ text: 'Open source', url: 'https://example.com/b' }]
    }
  ]
})

await sock.sendMessage(jid, {
  text: 'Music in footer',
  audioFooter: { url: 'https://example.com/footer.mp3' },
  nativeFlow: [
    { text: 'Next', id: '#Next', icon: 'review' },
    { text: 'Skip', id: '#Skip', icon: 'default' }
  ]
})
```

### Hydrated templates

```js
await sock.sendMessage(jid, {
  title: 'CODY AI',
  image: { url: 'https://example.com/image.jpg' },
  caption: 'Choose an action',
  footer: 'Plogme',
  templateButtons: [
    { text: 'Open menu', id: '#Menu' },
    { text: 'Open source', url: 'https://example.com' },
    { text: 'Call support', call: '2348000000000' }
  ]
})
```

### Interactive table

```js
await sock.sendInteractiveTable(jid, {
  title: 'Deployment status',
  headers: ['Service', 'State'],
  rows: [
    ['API', 'Online'],
    ['Worker', 'Online']
  ],
  buttons: [],
  footer: 'Updated now'
})
```

## Rich messages

Rich messages are forwarded GenAI-style envelopes. The sender now routes rich payloads through normal WhatsApp message generation before relay so clients receive the expected message metadata.

> [!NOTE]
> Rich rendering is client-dependent. A rich message can be structurally valid while a particular WhatsApp build chooses a fallback rendering.

### Rich text and quotes

```js
await sock.sendRichText(jid, {
  text: '**Deployment complete**\n\nThe service is online.'
})

await sock.sendMarkdownQuote(jid, {
  text: '> Use the production URL for verification.'
})
```

### Copy-to-clipboard actions

```js
await sock.sendCopyButton(jid, {
  text: 'npm install plogme',
  label: 'Copy command'
})

await sock.sendRichTextCopy(jid, {
  text: 'Install the package first.',
  copyText: 'npm install plogme',
  copyLabel: 'Copy command'
})

await sock.sendQuoteCopy(jid, {
  text: '> Run the tests before publishing.',
  copyText: 'npm test',
  copyLabel: 'Copy test command'
})
```

### Rich image and inline links

```js
await sock.sendRichImage(jid, {
  url: 'https://example.com/image.jpg',
  mimeType: 'image/jpeg'
})

await sock.sendRichLink(jid, {
  text: 'Open the documentation',
  links: [
    {
      text: 'Documentation',
      url: 'https://example.com/docs',
      isTrusted: true
    }
  ]
})
```

### Rich tables, lists, code, and LaTeX

```js
await sock.sendTable(
  jid,
  'Build status',
  ['Service', 'State'],
  [
    { items: ['API', 'Online'] },
    { items: ['Worker', 'Online'] }
  ]
)

await sock.sendList(jid, 'Next steps', [
  { title: 'Test', description: 'Run the test suite' },
  { title: 'Deploy', description: 'Publish the verified build' }
])

await sock.sendCodeBlock(jid, 'console.log("hello")', undefined, {
  language: 'javascript'
})

await sock.sendRichAIResponse(jid, {
  table: {
    title: 'Status',
    headers: ['Name', 'Value'],
    rows: [['version', '2.0.1']]
  }
})
```

`sendRichAIResponse` also accepts `list`, `codeBlock`, and `latex` content objects. For a pre-existing Meta AI response, `captureAndResendUnifiedResponse(jid, message, quoted)` captures and resends its unified response.

### One unified Plogme response

Use `sendPlogmeMessage` when a response may contain several optional sections. It combines every section that is present into one AIRich `unifiedResponse`; omitted sections are ignored, so callers do not need to construct a different payload for every result shape.

```js
const result = await sock.sendPlogmeMessage(jid, {
  headerText: 'CODY AI result',
  contentText: 'The deployment completed successfully.',

  // Optional highlighted code section.
  code: 'const status = "online"',
  language: 'javascript',

  // Optional table section. The first row is treated as the heading by default.
  title: 'Service status',
  table: [
    ['Service', 'State'],
    ['API', 'Online'],
    ['Worker', 'Online']
  ],
  noHeading: false,

  // Optional carousel/content items or reels.
  items: [
    { title: 'API documentation', url: 'https://example.com/docs' }
  ],
  reels: [
    {
      title: 'Release notes',
      url: 'https://example.com/release.mp4',
      thumbnail_url: 'https://example.com/release.jpg'
    }
  ],

  // Optional inline media and LaTeX sections.
  inlineImage: 'https://example.com/status.jpg',
  imageText: 'Deployment status',
  alignment: 2,
  latex: [
    { latexExpression: 'E = mc^2', url: 'https://example.com/equation.png' }
  ],

  // Optional citation links.
  links: [
    {
      text: 'Open the deployment logs',
      url: 'https://example.com/logs',
      title: 'Deployment logs',
      displayName: 'Logs'
    }
  ],

  // Optional AIRich product card (object = Single layout; array = HScroll).
  products: {
    title: 'CODY AI hosting',
    brand: 'Plogme',
    price: '$10',
    sale_price: '$7',
    url: 'https://example.com/product',
    image: 'https://example.com/product.jpg'
  },

  // Optional social-post cards (always HScroll).
  posts: [{
    title: 'Release announcement',
    username: 'codyai',
    profile: 'https://example.com/profile.jpg',
    thumbnail: 'https://example.com/post.jpg',
    caption: 'Deployment completed.',
    url: 'https://example.com/post',
    source: 'INSTAGRAM',
    verified: true
  }],

  // Optional source cards: [favicon URL, source URL, display text].
  sources: [[
    'https://example.com/favicon.jpg',
    'https://example.com/docs',
    'Documentation'
  ]],

  // Optional playable video card. Metadata may be supplied manually.
  inlineVideo: {
    url: 'https://example.com/clip.mp4',
    mimeType: 'video/mp4',
    duration: 12,
    fileLength: 123456
  },
  suggested: true,
  footerText: 'Generated by CODY AI'
}, undefined, {
  // The compatibility relay is enabled by default. It sends the rich
  // envelope normally, then uses WhatsApp's type-14 edit protocol so
  // compatible clients render it immediately instead of showing Download.
  renderRichResponse: true
})

console.log('Sent message:', result.key.id)
```

Every field in the example is optional. A minimal response is valid as well:

```js
await sock.sendPlogmeMessage(jid, {
  contentText: 'Only the text section is available right now.'
})
```

The compatibility edit can be disabled for a specific send with `{ renderRichResponse: false }`. This sends the generated rich envelope once without the follow-up edit.

### Rich response arrays

For mixed rich content, `richResponse` accepts text, code, and table items in one generated response:

```js
await sock.sendMessage(jid, {
  disclaimerText: 'CODY AI result',
  richResponse: [
    { text: 'Build result' },
    { language: 'javascript', code: [{ highlightType: 0, codeContent: 'console.log("online")' }] },
    { text: 'Runtime comparison:' },
    {
      title: 'Runtime',
      table: [
        { isHeading: true, items: ['', 'Node.js', 'Bun'] },
        { isHeading: false, items: ['State', 'Online', 'Online'] }
      ]
    }
  ]
})
```

### Rich URL previews and verified media

```js
await sock.sendMessage(jid, {
  text: 'https://example.com/docs',
  richPreview: true
})

await sock.sendMessage(jid, {
  text: 'https://example.com/docs',
  richPreview: true,
  previewTitle: 'CODY AI documentation',
  previewDescription: 'Open the verified documentation'
})

await sock.sendMessage(jid, {
  image: { url: 'https://example.com/verified-image.jpg' },
  caption: 'Verified image',
  verifiedMe: true
})
```

`verifiedMe` applies to image and video messages. Rich previews require a `text` field containing the URL; preview rendering remains client-dependent.

### Secured Meta service label

To request the supported secured Meta service label on an outgoing message:

```js
await sock.sendMessage(jid, {
  text: 'CODY AI secured response',
  secureMetaServiceLabel: true
})
```

## Rich menus, reels, and grids

### Original Gen4 rich menu

Use `sock.richMenu` for the original Gen4-style menu. This is the primary rich-menu API.

```js
await sock.richMenu(jid, {
  header: {
    title: 'Deployment tools',
    image: {
      url: 'https://example.com/cover.jpg',
      mimeType: 'image/jpeg',
      width: 1200,
      height: 800
    }
  },
  body: {
    title: 'Choose an action',
    buttons: [
      { id: 'deploy', text: 'Deploy', toast: 'Deploy selected' },
      { id: 'logs', text: 'View logs', toast: 'Logs selected' }
    ]
  },
  footer: {
    text: 'Open documentation',
    url: 'https://example.com/docs'
  }
})
```

### Universal A2UI message

`sendA2UIMessage` is the generic sender. It accepts arbitrary A2UI components, Bloks widget data, native-flow buttons, and optional media; it is not restricted to the command-table template. It also uses the immediate relay/edit compatibility path.

```js
await sock.sendA2UIMessage(jid, {
  bloksWidget: {
    type: 'im_a2ui',
    data: {
      version: 'v0.9',
      createSurface: {
        surfaceId: 'custom-card',
        catalogId: 'https://a2ui.org/specification/v0_9/catalogs/basic/catalog.json',
        components: [
          { id: 'root', component: 'Column', children: ['title'] },
          { id: 'title', component: 'Text', text: 'My custom A2UI card', variant: 'h2' }
        ]
      }
    },
    fallback: 'My custom A2UI card'
  },
  nativeFlowMessage: {
    buttons: [{ name: 'quick_reply', buttonParamsJson: JSON.stringify({ id: 'open', display_text: 'Open' }) }]
  }
})
```

### Large A2UI command card

Use `sendA2UICommandMenu` for the full-size card layout with an uploaded image header, a command/function table, URL buttons, and a native-flow command selector. This is an `interactiveMessage`/A2UI payload, not an AIRich `unifiedResponse`, so it is intentionally separate from `sendPlogmeMessage` and `richMenu`.

```js
await sock.sendA2UICommandMenu(jid, {
  image: 'https://example.com/cover.jpg',
  title: 'CODY AI',
  rows: [
    ['.meid', 'Check ID'],
    ['.fromid', 'Check Source'],
    ['.ping', 'Check Speed']
  ],
  buttons: [
    { text: '1st-Channel', url: 'https://example.com/first' },
    { text: '2nd-Channel', url: 'https://example.com/second' }
  ]
})
```

The image URL is uploaded as the interactive header media. Rows are also available through the native-flow `single_select` menu, and button URLs are represented as A2UI `openUrl` actions.

### Screenshot-style raw AIRich command card

Use `sendAIRichCommandMenu` when you want the manually serialized AIRich variant: an image primitive, widget CTAs, Markdown text with optional LaTeX inline metadata, a command table, and footer URL actions. Unlike A2UI, this path is an AIRich unified response and therefore uses the normal relay plus type-14 immediate-render edit.

```js
await sock.sendAIRichCommandMenu(jid, {
  image: 'https://example.com/cover.jpg',
  title: 'CODY AI',
  subtitle: 'Command List',
  code: 'CODY AI',
  rows: [
    ['.meid', 'Check ID'],
    ['.fromid', 'Check Source'],
    ['.ping', 'Check Speed']
  ],
  latexImage: 'https://example.com/logo.png',
  latexExpression: 'CODY',
  buttons: [
    { text: '1st-Channel', url: 'https://example.com/first' },
    { text: '2nd-Channel', url: 'https://example.com/second' }
  ],
  footer: 'Developed by - CODY AI'
})
```

Cards and carousel layout are supported:

```js
await sock.richMenu(jid, {
  body: {
    carousel: true,
    cards: [
      {
        title: 'Production',
        toast: 'Production selected',
        buttons: [{ id: 'production', text: 'Open' }]
      },
      {
        title: 'Staging',
        toast: 'Staging selected',
        buttons: [{ id: 'staging', text: 'Open' }]
      }
    ]
  }
})
```

Validate button IDs on the server. A button ID is an event value, not an authorization grant.

### Universal social/entity cards

The leaked profile-result format is exposed through a provider-neutral API. It supports Instagram and other providers without hard-coding the Instagram wire format. Every send uses the same immediate AIRich relay/edit path as the other rich messages.

```js
await sock.sendSocialEntity(jid, {
  platform: 'YOUTUBE',
  username: 'crysnovax',
  title: 'CODY AI',
  imageUrl: 'https://example.com/avatar.png',
  entityUrl: 'https://www.youtube.com/@crysnovax',
  fullName: 'CRYSNOVAX',
  isVerified: true,
  resultText: 'See results'
})
```

### Instagram profile card

Instagram is the primary dedicated convenience API. It emits the `IG_PROFILE` social entity metadata while sharing the universal implementation.

```js
await sock.sendInstagramProfile(jid, {
  username: 'crysnovax',
  title: 'CODY AI',
  subtitle: '⚉',
  imageUrl: 'https://example.com/avatar.png',
  entityId: '123456',
  entityUrl: 'https://www.instagram.com/crysnovax',
  fullName: 'CRYSNOVAX',
  isVerified: true,
  resultText: 'See results'
})
```

### Reels and rich media

`sendReels` accepts an array of reel records. The main rich-media path is used for reels, sources, links, tables, and future rich payloads.

```js
await sock.sendReels(jid, [
  {
    title: 'Example reel',
    creator: 'Example creator',
    videoUrl: 'https://cdn.example.com/reel.mp4',
    thumbnailUrl: 'https://cdn.example.com/reel.jpg',
    profileIconUrl: 'https://cdn.example.com/avatar.jpg',
    likesCount: 120,
    sharesCount: 8,
    viewCount: 2400,
    reelSource: 'IG',
    isVerified: true
  }
], undefined, {
  text: 'Featured reels'
})
```

Use a valid MP4 or another supported playable video. Test the URL with a media probe before sending it.

For a general rich response:

```js
await sock.sendRichMedia(jid, {
  contentText: 'A rich response',
  reels: [
    {
      title: 'Short clip',
      url: 'https://cdn.example.com/clip.mp4'
    }
  ]
})
```

### Rich button grid

`sendRichButtonGrid` sends ordinary message cards with native-flow buttons. It is distinct from the original Gen4 `richMenu` envelope.

```js
await sock.sendRichButtonGrid(jid, {
  text: 'Choose a service',
  footer: 'Select one',
  cards: [
    {
      title: 'Status',
      image: { url: './status.jpg' },
      buttons: [{ text: 'View status', id: 'status' }]
    },
    {
      title: 'Logs',
      image: { url: './logs.jpg' },
      buttons: [{ text: 'View logs', id: 'logs' }]
    }
  ]
})
```

## HTML screens and built-in apps

### Ordinary HTML message

```js
await sock.sendHtmlMessage(jid, {
  html: '<h1>Hello</h1><p>Rendered HTML content.</p>',
  trustedSources: []
})
```

### Tabbed rich HTML screen

```js
await sock.sendRichHtmlMessage(jid, {
  title: 'Example app',
  url: 'https://your-domain.example',
  trustedSources: ['your-domain.example'],
  tabs: [
    {
      tab_header: 'Home',
      html: '<h1>Home</h1><p>Welcome.</p>'
    },
    {
      tab_header: 'Help',
      html: '<h2>Help</h2><p>Tap the buttons to continue.</p>'
    }
  ]
})
```

`trustedSources` and `url` are application-specific. Sanitize user-controlled HTML before sending it.

### Built-in screens

```js
await sock.sendSlotMachine(jid)
await sock.sendMiniApp(jid, { title: 'Pairing mini-app' })
await sock.sendWebsitePreview(jid, 'https://example.com')
await sock.sendLinkPreview(jid, 'https://example.com')
```

The built-in mini-app uses its configured first-party source. Generic rich HTML remains configurable.

## Status updates

`sendStatus` sends a status update and resolves recipients from `statusJidList`.

```js
await sock.sendStatus({
  text: 'Service online',
  backgroundColor: 0xff102a43,
  font: 2
}, {
  statusJidList: ['15551234567@s.whatsapp.net']
})
```

For status mentions:

```js
await sock.sendStatusMention(
  { text: 'Welcome to the team' },
  ['15551234567@s.whatsapp.net']
)
```

## Groups and group history

```js
const metadata = await sock.groupMetadata(groupJid)

await sock.groupParticipantsUpdate(groupJid, [userJid], 'add')
await sock.groupParticipantsUpdate(groupJid, [userJid], 'remove')
await sock.groupSettingUpdate(groupJid, 'announcement')
```

### Member labels

`updateMemberLabel` sets the connected account's own label in a group.

```js
await sock.updateMemberLabel(groupJid, 'Developer')
await sock.updateMemberLabel(groupJid, '') // remove the label
```

### Group history sharing

```js
await sock.toggleGroupHistory(groupJid, true)  // all members
await sock.toggleGroupHistory(groupJid, false) // admins only
```

## Reports and moderation

The reporting APIs have different wire operations. Use the method that matches the action you intend to perform.

```js
await sock.reportSpam(
  groupJid,
  [{ id: messageId, from: userJid, t: Math.floor(Date.now() / 1000) }],
  'group_info_report',
  'Example group'
)

const userAccepted = await sock.reportUser(userJid, 'spam')
const messageAccepted = await sock.reportMessage(
  userJid,
  messageId,
  'harassment',
  participantJid
)
await sock.reportAndBlockUser(userJid, 'scam')
```

`reportUser` and `reportMessage` return whether WhatsApp acknowledged the request. An acknowledgement is not a guarantee of a particular moderation outcome.

## Experimental bot metadata

These methods attach experimental fields under `messageContextInfo.botMetadata`.

```js
await sock.sendBotDiagnostics(jid, {
  text: 'Diagnostics test',
  botBackend: 1,
  toolsUsed: ['web_search'],
  isThinking: false
})

await sock.sendBotCommand(jid, {
  text: 'Command test',
  commandName: 'summarize',
  commandDescription: 'Summarize the conversation',
  commandPrompt: 'Summarize the conversation in five bullets'
})

await sock.sendBotToolResult(jid, {
  text: 'Tool completed',
  toolCallId: 'tool-123',
  resolutionData: { status: 'success', value: 42 }
})
```

These methods do not provide Meta's private verification credentials and must not be used to impersonate a first-party Meta AI message.

## WhatsApp flows and web views

```js
await sock.sendWhatsAppFlow(jid, {
  flowId: 'your-flow-id',
  flowToken: 'your-flow-token',
  cta: 'Open form',
  text: 'Complete this form'
})

await sock.sendRichWebview(jid, {
  url: 'https://example.com/app',
  text: 'Open the app',
  buttonText: 'Open'
})
```

Flow identifiers and tokens must belong to a valid business configuration.

## View-once utilities

Text view-once messages use the client-compatible inner `ExtendedTextMessage.viewOnce` flag:

```js
await sock.sendMessage(jid, {
  text: 'viewOnceMessage',
  viewOnce: true
})
```

Media view-once messages continue to use WhatsApp's standard outer `viewOnceMessage` envelope:

```js
await sock.sendMessage(jid, {
  image: { url: 'https://example.com/secret.jpg' },
  caption: 'Open once',
  viewOnce: true
})
```

```js
const info = sock.getViewOnceInfo(message)
console.log(info)

if (sock.isViewOnce(message)) {
  await sock.autoVV(message)
}
```

`autoVV` and `antiVV` are utilities for view-once handling. Their behavior depends on the message shape and client version; use them only for messages you are authorized to process.

## Connection handling

A connection can close because of logout, network interruption, protocol changes, or session invalidation. Avoid starting multiple reconnect loops for the same socket.

```js
sock.ev.on('connection.update', ({ connection, lastDisconnect }) => {
  if (connection !== 'close') return

  const statusCode = new Boom(lastDisconnect?.error)?.output?.statusCode
  if (statusCode === DisconnectReason.loggedOut) {
    console.error('Logged out; pair again with a new session')
    return
  }

  setTimeout(() => {
    connect().catch(console.error)
  }, 3000)
})
```

## Security and removed automatic behavior

Plogme does not automatically follow WhatsApp channels, fingerprint the host machine, contact a remote attestation service, or enforce rebranding through runtime code.

> [!NOTE]
> Official update channels are optional. Users who want updates must join them manually:
>
> - [CODY AI WhatsApp channel](https://whatsapp.com/channel/0029Vb8296789inZCpWLuS3T)
> - [CRYSNOVA AI WhatsApp channel](https://whatsapp.com/channel/0029Vb6pe77K0IBn48HLKb38)

The project license still applies. Removing runtime enforcement does not change the license terms.

## Development

```bash
npm install
npm test
```

Before publishing, verify the package contents and ensure no session data, private keys, or environment files are included.

The repository includes generated WhatsApp protocol files under `WAProto/`. Changes to generated protocol definitions should be made deliberately and tested against the installed WhatsApp client versions you support.

## License and support

Review [`LICENSE`](LICENSE) before redistributing applications or modified source.

For implementation questions, include the Node.js version, Plogme version, operating system, relevant error text, and a minimal reproduction. Do not include authentication state or private account data.

## References

[1]: https://github.com/crysnovax/baileys "Plogme source repository"
[2]: https://www.npmjs.com/package/plogme "Plogme on npm"
[3]: https://github.com/WhiskeySockets/Baileys "Baileys protocol implementation"

---

# Additional APIs from the Bailey2 compatibility surface

The following reference sections document supported APIs that are not repeated in the core CODY/Plogme guide above. They are retained from the `crysnovax/baileys2` compatibility documentation and exclude package-specific ban-checker functionality.

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

// Spoiler (compatible context flag; no unsupported future-proof wrapper)
sock.sendMessage(jid, {
  image: { url: './image.jpg' },
  caption: 'Spoiler',
  spoiler: true
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

Statuses are sent to `status@broadcast` and require at least one viewer JID. Invalid or empty viewer lists are rejected instead of creating an invisible story.

```javascript
const viewers = [
  '628123456789@s.whatsapp.net',
  '628987654321@s.whatsapp.net'
]

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

## Meta AI Features

Meta AI-style thinking indicators and live reasoning feeds. Works on all WhatsApp clients — no "Update WhatsApp" messages.

**Note:** These use plain text placeholders with typing indicators by default, avoiding unsupported-message bubbles. Final code blocks, tables, links, and rich responses are unchanged. Native Meta typing/thinking is force-only: set both `forceMetaRendering: true` in the socket config and `useNativeMeta: true` in the `metaTyping` or `sendMetaComposited` options.

### Meta Typing Indicator

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

Full flow: indicator shows → auto-deletes → clean final message lands. Works with every rich content type.

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

## Credits and attribution

Plogme/CODY is a downstream fork and enhancement of the Baileys protocol implementation by [WhiskeySockets/Baileys](https://github.com/WhiskeySockets/Baileys). The underlying WhatsApp Web protocol work, structure, and many foundational APIs come from WhiskeySockets. Additional changes in this fork include CODY/Plogme rich-message composition, immediate AIRich rendering relay, A2UI and Gen4 menu helpers, albums, video grids, and related documentation.

This README also incorporates compatible API examples from [crysnovax/baileys2](https://github.com/crysnovax/baileys2). Custom package-specific ban-checker APIs are intentionally not documented here.
