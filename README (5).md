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

Use at least two image or video items. Albums are sent as a parent message followed by separately encrypted media children.

```js
await sock.sendAlbumMessage(jid, [
  { type: 'image', data: { url: './one.jpg' } },
  { type: 'image', data: { url: './two.jpg' } }
], {
  caption: 'Trip photos'
})
```

The general `sendMessage` album form is also supported:

```js
await sock.sendMessage(jid, {
  album: [
    { image: { url: './one.jpg' }, caption: 'First' },
    { video: { url: './clip.mp4' }, caption: 'Second' }
  ]
})
```

Rich-response and bot-forwarded messages cannot be nested as album children. Send them as separate messages.

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

## Rich menus, reels, and grids

### Original Gen4 rich menu

Use `sock.richMenu` for the original Gen4-style menu. This is the primary rich-menu API.

```js
await sock.richMenu(jid, {
  header: {
    title: 'Deployment tools'
  },
  body: {
    title: 'Choose an action',
    buttons: [
      { id: 'deploy', text: 'Deploy' },
      { id: 'logs', text: 'View logs' }
    ]
  },
  footer: {
    text: 'Open documentation',
    url: 'https://example.com/docs'
  }
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
