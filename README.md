# openai-realtime-api-beta

Conversational apps need a way to send text or audio to OpenAI and handle responses as they arrive. openai-realtime-api-beta is a JavaScript Realtime API client for Node.js and browser prototypes, based on OpenAI's reference implementation.

**This client targets the beta API and should not be treated as a final implementation.**

## Features

- Send user text and request a model response through `RealtimeClient`.
- Stream input audio with `appendInputAudio()` and explicitly request responses with `createResponse()`.
- Track conversation items and receive incremental changes through `conversation.updated` events.
- Register tools with callbacks through `addTool()` and return their results to the model automatically.
- Access lower-level WebSocket events through `client.realtime` when you need direct API control.

## Installation

The upstream beta client installs directly from GitHub. To use that implementation with the examples below:

```sh
npm install openai/openai-realtime-api-beta --save
```

The package import name is `@openai/realtime-api-beta`. The standalone project is named `openai-realtime-api-beta`; no separate npm publication is assumed here.

To download this standalone project's source archive, set `GITHUB_OWNER` to its GitHub owner. This command requires a published `v0.1.0` tag; it does not download the upstream repository:

```sh
curl -fL "https://github.com/${GITHUB_OWNER}/openai-realtime-api-beta/archive/refs/tags/v0.1.0.tar.gz" -o openai-realtime-api-beta-v0.1.0.tar.gz
```

## Usage

This is a library, not a command-line application. Save the following as `example.mjs`. The script accepts a text prompt as its first argument and logs conversation updates.

```javascript
import { RealtimeClient } from '@openai/realtime-api-beta';

if (!process.env.OPENAI_API_KEY) {
  throw new Error('Set OPENAI_API_KEY before running this example.');
}

const client = new RealtimeClient({
  apiKey: process.env.OPENAI_API_KEY,
});

client.updateSession({
  instructions: 'Answer briefly and clearly.',
});

client.on('conversation.updated', ({ item, delta }) => {
  console.log({ item, delta });
});

await client.connect();

client.sendUserMessageContent([
  {
    type: 'input_text',
    text: process.argv[2] || 'How are you?',
  },
]);
```

Run it from Node.js:

```sh
export OPENAI_API_KEY='your-api-key'
node example.mjs 'Explain WebSockets in two sentences.'
```

The connection stays open for subsequent events. Stop this example with Ctrl+C.

For audio input, the default format is PCM16 at 24,000 Hz. Pass an `Int16Array` or `ArrayBuffer` to `client.appendInputAudio()`. With automatic turn detection disabled, call `client.createResponse()` after appending the audio for a turn.

**Keep API keys on the server.** Direct browser connections require `dangerouslyAllowAPIKeyInBrowser: true` and expose the key to browser users. A browser application can instead connect to your relay with `new RealtimeClient({ url: RELAY_SERVER_URL })`.

## License

MIT. This project is based on [openai/openai-realtime-api-beta](https://github.com/openai/openai-realtime-api-beta), which is also MIT-licensed. Retain the upstream copyright and permission notice when redistributing its code or substantial portions of it.

## Related projects

The following repositories were supplied as other tools from the same author. Their functionality was not included in the source material, so descriptions are left unverified rather than inferred from their names.

- [sakthivelj10-ai/kstone](https://github.com/sakthivelj10-ai/kstone) — No verified functionality description was supplied.
- [xylakis/secret-llama](https://github.com/xylakis/secret-llama) — No verified functionality description was supplied.
- [Cabral-it/docker-ikev2-vpn-server](https://github.com/Cabral-it/docker-ikev2-vpn-server) — No verified functionality description was supplied.

