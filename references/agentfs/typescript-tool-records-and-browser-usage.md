# TypeScript tool records and browser usage

[Context index](agentfs-language-sdks.md) · [Setup and preceding context](typescript-setup-and-agent-options.md) · [Previous phase](typescript-file-reading-and-metadata.md)

#### `tools.record(name, started_at, completed_at, parameters?, result?, error?): Promise<number>`

 Record a tool invocation. The first declaration describes the call signature; the example assumes your application supplies `callOpenAI(prompt)` and `prompt`.

```
type RecordToolCall = (
  name: string,
  started_at: number,
  completed_at: number,
  parameters?: unknown,
  result?: unknown,
  error?: string
) => Promise<number>;

// Example: Track an API call
const start = Date.now() / 1000;
const response = await callOpenAI(prompt);

const id = await agent.tools.record(
  'openai_completion',
  start,
  Date.now() / 1000,
  { prompt, model: 'gpt-4', temperature: 0.7 },
  { response, tokensUsed: 150 }
);

```

#### `tools.get(id: number): Promise<ToolCall | undefined>`

 Fetch information for one specific tool call.

```
const toolCall = await agent.tools.get(42);
if (toolCall) {
  console.log('Duration:', toolCall.duration_ms, 'ms');
}

```

### Browser Support

 AgentFS can run in browser environments through WebAssembly:

```
<!DOCTYPE html>
<html>
<head>
  <script type="module">
    import { AgentFS } from 'https://unpkg.com/agentfs-sdk/dist/browser.js';

    const agent = await AgentFS.open({ id: 'browser-agent' });

    // All APIs work the same in browser
    await agent.kv.set('browser:data', { platform: 'web' });
    await agent.fs.writeFile('/notes.txt', 'Hello from browser!');
  </script>
</head>
</html>

```

### Support

- [GitHub Issues](https://github.com/tursodatabase/agentfs/issues) - Send bug reports or feature requests
- [Discord](https://tur.so/discord) - Community help and discussion
- [Examples](https://github.com/tursodatabase/agentfs/tree/main/examples) - Sample applications
