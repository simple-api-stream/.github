# simple-api-stream

Streaming utilities for **incremental JSON, XML, CSV, TOML, and SSE processing** built on the [Web Streams API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Streams_API).

Text is parsed chunk by chunk into a flat sequence of **events** — **SAJ** (Simple API for JSON), **SAX** (Simple API for XML), **SAC** (Simple API for CSV), **SAT** (Simple API for TOML), and **SSE** events (`text/event-stream`). You can consume the events directly, rebuild JavaScript objects / ASTs / DOM nodes from them, convert between formats, or serialize them back into text. Because every component is a standard `ReadableStream`, `WritableStream`, or `TransformStream`, they compose naturally with `pipeThrough()` / `pipeTo()` and respect backpressure.

## ✨ Highlights

- **Zero external runtime dependencies**
- **Web Streams native** — works anywhere Web Streams are supported (Node.js, browsers, Deno, Bun, edge runtimes, …)
- **TypeScript-first ESM packages** — ships with full type definitions (also directly `require()`-able via Node.js `require(esm)`)
- **Modular monorepo** — install only the format packages you need
- **Cross-format bridges** — optional `./json` entry points bridge XML, CSV, and TOML event streams directly to and from SAJ (JSON) event streams
- **Fetch `Response` helpers** — `parseJSONResponse`, `parseXMLResponse`, `parseCSVResponse`, `parseTOMLResponse`, and `parseSSEResponse` with automatic `Content-Type` validation and `charset` detection

## 📦 Packages

| Package | Description |
|---------|-------------|
| [`@simple-api-stream/core`](https://github.com/simple-api-stream/simple-api-stream/tree/main/packages/core) | Core event classes (`SAEvent`, `StartDocumentEvent`, `EndDocumentEvent`, `ErrorEvent`, `CommentEvent`), parser interfaces (`SimpleApiParser`), and Fetch `Response` helpers (`responseToTextStream`, `ResponseStreamError`) |
| [`@simple-api-stream/json`](https://github.com/simple-api-stream/simple-api-stream/tree/main/packages/json) | Streaming JSON (**SAJ**) parser, object materializer, serializer, imperative builder, and `parseJSONResponse` (including NDJSON / JSONL / RFC 7464 `json-seq` & JSONC) |
| [`@simple-api-stream/xml`](https://github.com/simple-api-stream/simple-api-stream/tree/main/packages/xml) | Streaming XML (**SAX**) parser, AST & DOM converters, serializer, imperative builder, `parseXMLResponse`, and optional SAJ bridge (`@simple-api-stream/xml/json`) |
| [`@simple-api-stream/csv`](https://github.com/simple-api-stream/simple-api-stream/tree/main/packages/csv) | Streaming CSV (**SAC**) parser, row object converters, serializer, imperative builder, `parseCSVResponse`, and optional SAJ bridge (`@simple-api-stream/csv/json`) |
| [`@simple-api-stream/toml`](https://github.com/simple-api-stream/simple-api-stream/tree/main/packages/toml) | Streaming TOML (**SAT**) parser, object converters, serializer, imperative builder, `parseTOMLResponse`, and optional SAJ bridge (`@simple-api-stream/toml/json`) |
| [`@simple-api-stream/sse`](https://github.com/simple-api-stream/simple-api-stream/tree/main/packages/sse) | Streaming Server-Sent Events (`text/event-stream`) parser, serializer, and `parseSSEResponse` |

## ⚡ Quick Example

```ts
import { parseJSONResponse } from "@simple-api-stream/json";

const response = await fetch("https://example.com/data.ndjson");
for await (const item of parseJSONResponse(response)) {
  console.log(item);
}
```

Or compose the underlying Web Streams directly:

```ts
import {
  JSONTextToSAJTransformStream,
  SAJToObjectTransformStream,
} from "@simple-api-stream/json";

const response = await fetch("https://example.com/data.json");
const objects = response.body!
  .pipeThrough(new TextDecoderStream())
  .pipeThrough(new JSONTextToSAJTransformStream())
  .pipeThrough(new SAJToObjectTransformStream());

for await (const obj of objects) {
  console.log(obj);
}
```

## 🔗 Repository & Documentation

See the main repository [**simple-api-stream/simple-api-stream**](https://github.com/simple-api-stream/simple-api-stream) for full documentation, API reference, and usage examples.

