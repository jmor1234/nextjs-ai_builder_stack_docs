# AI SDK API Reference

## API Reference For AI SDK Core

### `generateText()`

Generates text and calls tools for a given prompt using a language model.

It is ideal for non-interactive use cases such as automation tasks where you need to write text (e.g. drafting email or summarizing web pages) and for agents that use tools.

```ts
import { openai } from '@ai-sdk/openai';
import { generateText } from 'ai';

const { text } = await generateText({
  model: openai('gpt-4-turbo'),
  prompt: 'Invent a new holiday and describe its traditions.',
});

console.log(text);
```

To see `generateText` in action, check out [these examples](#examples).

#### Import

<Snippet text={`import { generateText } from "ai"`} prompt={false} />

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'model',
      type: 'LanguageModel',
      description: "The language model to use. Example: openai('gpt-4-turbo')",
    },
    {
      name: 'system',
      type: 'string',
      description:
        'The system prompt to use that specifies the behavior of the model.',
    },
    {
      name: 'prompt',
      type: 'string',
      description: 'The input prompt to generate the text from.',
    },
    {
      name: 'messages',
      type: 'Array<CoreSystemMessage | CoreUserMessage | CoreAssistantMessage | CoreToolMessage>',
      description: 'A list of messages that represent a conversation.',
      properties: [
        {
          type: 'CoreSystemMessage',
          parameters: [
            {
              name: 'role',
              type: "'system'",
              description: 'The role for the system message.',
            },
            {
              name: 'content',
              type: 'string',
              description: 'The content of the message.',
            },
          ],
        },
        {
          type: 'CoreUserMessage',
          parameters: [
            {
              name: 'role',
              type: "'user'",
              description: 'The role for the user message.',
            },
            {
              name: 'content',
              type: 'string | Array<TextPart | ImagePart>',
              description: 'The content of the message.',
              properties: [
                {
                  type: 'TextPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'text'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'text',
                      type: 'string',
                      description: 'The text content of the message part.',
                    },
                  ],
                },
                {
                  type: 'ImagePart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'image'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'image',
                      type: 'string | Uint8Array | Buffer | ArrayBuffer | URL',
                      description:
                        'The image content of the message part. String are either base64 encoded content, base64 data URLs, or http(s) URLs.',
                    },
                  ],
                },
              ],
            },
          ],
        },
        {
          type: 'CoreAssistantMessage',
          parameters: [
            {
              name: 'role',
              type: "'assistant'",
              description: 'The role for the assistant message.',
            },
            {
              name: 'content',
              type: 'string | Array<TextPart | ToolCallPart>',
              description: 'The content of the message.',
              properties: [
                {
                  type: 'TextPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'text'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'text',
                      type: 'string',
                      description: 'The text content of the message part.',
                    },
                  ],
                },
                {
                  type: 'ToolCallPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'tool-call'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'toolCallId',
                      type: 'string',
                      description: 'The id of the tool call.',
                    },
                    {
                      name: 'toolName',
                      type: 'string',
                      description:
                        'The name of the tool, which typically would be the name of the function.',
                    },
                    {
                      name: 'args',
                      type: 'object based on zod schema',
                      description:
                        'Parameters generated by the model to be used by the tool.',
                    },
                  ],
                },
              ],
            },
          ],
        },
        {
          type: 'CoreToolMessage',
          parameters: [
            {
              name: 'role',
              type: "'tool'",
              description: 'The role for the assistant message.',
            },
            {
              name: 'content',
              type: 'Array<ToolResultPart>',
              description: 'The content of the message.',
              properties: [
                {
                  type: 'ToolResultPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'tool-result'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'toolCallId',
                      type: 'string',
                      description:
                        'The id of the tool call the result corresponds to.',
                    },
                    {
                      name: 'toolName',
                      type: 'string',
                      description:
                        'The name of the tool the result corresponds to.',
                    },
                    {
                      name: 'result',
                      type: 'unknown',
                      description:
                        'The result returned by the tool after execution.',
                    },
                    {
                      name: 'isError',
                      type: 'boolean',
                      isOptional: true,
                      description:
                        'Whether the result is an error or an error message.',
                    },
                  ],
                },
              ],
            },
          ],
        },
      ],
    },
    {
      name: 'tools',
      type: 'Record<string, CoreTool>',
      description:
        'Tools that are accessible to and can be called by the model. The model needs to support calling tools.',
      properties: [
        {
          type: 'CoreTool',
          parameters: [
            {
              name: 'description',
              isOptional: true,
              type: 'string',
              description:
                'Information about the purpose of the tool including details on how and when it can be used by the model.',
            },
            {
              name: 'parameters',
              type: 'Zod Schema | JSON Schema',
              description:
                'The schema of the input that the tool expects. The language model will use this to generate the input. It is also used to validate the output of the language model. Use descriptions to make the input understandable for the language model. You can either pass in a Zod schema or a JSON schema (using the `jsonSchema` function).',
            },
            {
              name: 'execute',
              isOptional: true,
              type: 'async (parameters) => any',
              description:
                'An async function that is called with the arguments from the tool call and produces a result. If not provided, the tool will not be executed automatically.',
            },
          ],
        },
      ],
    },
    {
      name: 'toolChoice',
      isOptional: true,
      type: '"auto" | "none" | "required" | { "type": "tool", "toolName": string }',
      description:
        'The tool choice setting. It specifies how tools are selected for execution. The default is "auto". "none" disables tool execution. "required" requires tools to be executed. { "type": "tool", "toolName": string } specifies a specific tool to execute.',
    },
    {
      name: 'maxTokens',
      type: 'number',
      isOptional: true,
      description: 'Maximum number of tokens to generate.',
    },
    {
      name: 'temperature',
      type: 'number',
      isOptional: true,
      description:
        'Temperature setting. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.',
    },
    {
      name: 'topP',
      type: 'number',
      isOptional: true,
      description:
        'Nucleus sampling. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.',
    },
    {
      name: 'topK',
      type: 'number',
      isOptional: true,
      description:
        'Only sample from the top K options for each subsequent token. Used to remove "long tail" low probability responses. Recommended for advanced use cases only. You usually only need to use temperature.',
    },
    {
      name: 'presencePenalty',
      type: 'number',
      isOptional: true,
      description:
        'Presence penalty setting. It affects the likelihood of the model to repeat information that is already in the prompt. The value is passed through to the provider. The range depends on the provider and model.',
    },
    {
      name: 'frequencyPenalty',
      type: 'number',
      isOptional: true,
      description:
        'Frequency penalty setting. It affects the likelihood of the model to repeatedly use the same words or phrases. The value is passed through to the provider. The range depends on the provider and model.',
    },
    {
      name: 'stopSequences',
      type: 'string[]',
      isOptional: true,
      description:
        'Sequences that will stop the generation of the text. If the model generates any of these sequences, it will stop generating further text.',
    },
    {
      name: 'seed',
      type: 'number',
      isOptional: true,
      description:
        'The seed (integer) to use for random sampling. If set and supported by the model, calls will generate deterministic results.',
    },
    {
      name: 'maxRetries',
      type: 'number',
      isOptional: true,
      description:
        'Maximum number of retries. Set to 0 to disable retries. Default: 2.',
    },
    {
      name: 'abortSignal',
      type: 'AbortSignal',
      isOptional: true,
      description:
        'An optional abort signal that can be used to cancel the call.',
    },
    {
      name: 'headers',
      type: 'Record<string, string>',
      isOptional: true,
      description:
        'Additional HTTP headers to be sent with the request. Only applicable for HTTP-based providers.',
    },
    {
      name: 'maxToolRoundtrips',
      type: 'number',
      isOptional: true,
      description:
        'Maximum number of automatic roundtrips for tool calls. An automatic tool call roundtrip is another LLM call with the tool call results when all tool calls of the last assistant message have results. A maximum number is required to prevent infinite loops in the case of misconfigured tools. By default, it is set to 0, which will disable the feature.',
    },
    {
      name: 'experimental_telemetry',
      type: 'TelemetrySettings',
      isOptional: true,
      description: 'Telemetry configuration. Experimental feature.',
      properties: [
        {
          type: 'TelemetrySettings',
          parameters: [
            {
              name: 'isEnabled',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable telemetry. Disabled by default while experimental.',
            },
            {
              name: 'recordInputs',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable input recording. Enabled by default.',
            },
            {
              name: 'recordOutputs',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable output recording. Enabled by default.',
            },
            {
              name: 'functionId',
              type: 'string',
              isOptional: true,
              description:
                'Identifier for this function. Used to group telemetry data by function.',
            },
            {
              name: 'metadata',
              isOptional: true,
              type: 'Record<string, string | number | boolean | Array<null | undefined | string> | Array<null | undefined | number> | Array<null | undefined | boolean>>',
              description:
                'Additional information to include in the telemetry data.',
            },
          ],
        },
      ],
    },
  ]}
/>

#### Returns

<PropertiesTable
  content={[
    {
      name: 'text',
      type: 'string',
      description: 'The generated text by the model.',
    },
    {
      name: 'toolCalls',
      type: 'array',
      description: 'A list of tool calls made by the model.',
    },
    {
      name: 'toolResults',
      type: 'array',
      description:
        'A list of tool results returned as responses to earlier tool calls.',
    },
    {
      name: 'finishReason',
      type: "'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown'",
      description: 'The reason the model finished generating the text.',
    },
    {
      name: 'usage',
      type: 'CompletionTokenUsage',
      description: 'The token usage of the generated text.',
      properties: [
        {
          type: 'CompletionTokenUsage',
          parameters: [
            {
              name: 'promptTokens',
              type: 'number',
              description: 'The total number of tokens in the prompt.',
            },
            {
              name: 'completionTokens',
              type: 'number',
              description: 'The total number of tokens in the completion.',
            },
            {
              name: 'totalTokens',
              type: 'number',
              description: 'The total number of tokens generated.',
            },
          ],
        },
      ],
    },
    {
      name: 'rawResponse',
      type: 'RawResponse',
      optional: true,
      description: 'Optional raw response data.',
      properties: [
        {
          type: 'RawResponse',
          parameters: [
            {
              name: 'headers',
              optional: true,
              type: 'Record<string, string>',
              description: 'Response headers.',
            },
          ],
        },
      ],
    },
    {
      name: 'warnings',
      type: 'Warning[] | undefined',
      description:
        'Warnings from the model provider (e.g. unsupported settings).',
    },
    {
      name: 'experimental_providerMetadata',
      type: 'Record<string,Record<string,JSONValue>> | undefined',
      description:
        'Optional metadata from the provider. The outer key is the provider name. The inner values are the metadata. Details depend on the provider.',
    },
    {
      name: 'responseMessages',
      type: 'Array<CoreAssistantMessage | CoreToolMessage>',
      description:
        'The response messages that were generated during the call. It consists of an assistant message, potentially containing tool calls.  When there are tool results, there is an additional tool message with the tool results that are available. If there are tools that do not have execute functions, they are not included in the tool results and need to be added separately.',
    },
    {
      name: 'roundtrips',
      type: 'Array<Roundtrip>',
      description:
        'Response information for every roundtrip. You can use this to get information about intermediate steps, such as the tool calls or the response headers.',
      properties: [
        {
          type: 'Roundtrip',
          parameters: [
            {
              name: 'text',
              type: 'string',
              description: 'The generated text by the model.',
            },
            {
              name: 'toolCalls',
              type: 'array',
              description: 'A list of tool calls made by the model.',
            },
            {
              name: 'toolResults',
              type: 'array',
              description:
                'A list of tool results returned as responses to earlier tool calls.',
            },
            {
              name: 'finishReason',
              type: "'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown'",
              description: 'The reason the model finished generating the text.',
            },
            {
              name: 'usage',
              type: 'CompletionTokenUsage',
              description: 'The token usage of the generated text.',
              properties: [
                {
                  type: 'CompletionTokenUsage',
                  parameters: [
                    {
                      name: 'promptTokens',
                      type: 'number',
                      description: 'The total number of tokens in the prompt.',
                    },
                    {
                      name: 'completionTokens',
                      type: 'number',
                      description:
                        'The total number of tokens in the completion.',
                    },
                    {
                      name: 'totalTokens',
                      type: 'number',
                      description: 'The total number of tokens generated.',
                    },
                  ],
                },
              ],
            },
            {
              name: 'rawResponse',
              type: 'RawResponse',
              optional: true,
              description: 'Optional raw response data.',
              properties: [
                {
                  type: 'RawResponse',
                  parameters: [
                    {
                      name: 'headers',
                      optional: true,
                      type: 'Record<string, string>',
                      description: 'Response headers.',
                    },
                  ],
                },
              ],
            },
            {
              name: 'warnings',
              type: 'Warning[] | undefined',
              description:
                'Warnings from the model provider (e.g. unsupported settings).',
            },
          ],
        },
      ],
    },
  ]}
/>

### `streamText()`

Streams text generations from a language model.

You can use the streamText function for interactive use cases such as chat bots and other real-time applications. You can also generate UI components with tools.

```ts
import { openai } from '@ai-sdk/openai';
import { streamText } from 'ai';

const { textStream } = await streamText({
  model: openai('gpt-4-turbo'),
  prompt: 'Invent a new holiday and describe its traditions.',
});

for await (const textPart of textStream) {
  process.stdout.write(textPart);
}
```

To see `streamText` in action, check out [these examples](#examples).

#### Import

<Snippet text={`import { streamText } from "ai"`} prompt={false} />

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'model',
      type: 'LanguageModel',
      description: "The language model to use. Example: openai('gpt-4-turbo')",
    },
    {
      name: 'system',
      type: 'string',
      description:
        'The system prompt to use that specifies the behavior of the model.',
    },
    {
      name: 'prompt',
      type: 'string',
      description: 'The input prompt to generate the text from.',
    },
    {
      name: 'messages',
      type: 'Array<CoreSystemMessage | CoreUserMessage | CoreAssistantMessage | CoreToolMessage>',
      description: 'A list of messages that represent a conversation.',
      properties: [
        {
          type: 'CoreSystemMessage',
          parameters: [
            {
              name: 'role',
              type: "'system'",
              description: 'The role for the system message.',
            },
            {
              name: 'content',
              type: 'string',
              description: 'The content of the message.',
            },
          ],
        },
        {
          type: 'CoreUserMessage',
          parameters: [
            {
              name: 'role',
              type: "'user'",
              description: 'The role for the user message.',
            },
            {
              name: 'content',
              type: 'string | Array<TextPart | ImagePart>',
              description: 'The content of the message.',
              properties: [
                {
                  type: 'TextPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'text'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'text',
                      type: 'string',
                      description: 'The text content of the message part.',
                    },
                  ],
                },
                {
                  type: 'ImagePart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'image'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'image',
                      type: 'string | Uint8Array | Buffer | ArrayBuffer | URL',
                      description:
                        'The image content of the message part. String are either base64 encoded content, base64 data URLs, or http(s) URLs.',
                    },
                  ],
                },
              ],
            },
          ],
        },
        {
          type: 'CoreAssistantMessage',
          parameters: [
            {
              name: 'role',
              type: "'assistant'",
              description: 'The role for the assistant message.',
            },
            {
              name: 'content',
              type: 'string | Array<TextPart | ToolCallPart>',
              description: 'The content of the message.',
              properties: [
                {
                  type: 'TextPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'text'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'text',
                      type: 'string',
                      description: 'The text content of the message part.',
                    },
                  ],
                },
                {
                  type: 'ToolCallPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'tool-call'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'toolCallId',
                      type: 'string',
                      description: 'The id of the tool call.',
                    },
                    {
                      name: 'toolName',
                      type: 'string',
                      description:
                        'The name of the tool, which typically would be the name of the function.',
                    },
                    {
                      name: 'args',
                      type: 'object based on zod schema',
                      description:
                        'Parameters generated by the model to be used by the tool.',
                    },
                  ],
                },
              ],
            },
          ],
        },
        {
          type: 'CoreToolMessage',
          parameters: [
            {
              name: 'role',
              type: "'tool'",
              description: 'The role for the assistant message.',
            },
            {
              name: 'content',
              type: 'Array<ToolResultPart>',
              description: 'The content of the message.',
              properties: [
                {
                  type: 'ToolResultPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'tool-result'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'toolCallId',
                      type: 'string',
                      description:
                        'The id of the tool call the result corresponds to.',
                    },
                    {
                      name: 'toolName',
                      type: 'string',
                      description:
                        'The name of the tool the result corresponds to.',
                    },
                    {
                      name: 'result',
                      type: 'unknown',
                      description:
                        'The result returned by the tool after execution.',
                    },
                    {
                      name: 'isError',
                      type: 'boolean',
                      isOptional: true,
                      description:
                        'Whether the result is an error or an error message.',
                    },
                  ],
                },
              ],
            },
          ],
        },
      ],
    },
    {
      name: 'tools',
      type: 'Record<string, CoreTool>',
      description:
        'Tools that are accessible to and can be called by the model. The model needs to support calling tools.',
      properties: [
        {
          type: 'CoreTool',
          parameters: [
            {
              name: 'description',
              isOptional: true,
              type: 'string',
              description:
                'Information about the purpose of the tool including details on how and when it can be used by the model.',
            },
            {
              name: 'parameters',
              type: 'Zod Schema | JSON Schema',
              description:
                'The schema of the input that the tool expects. The language model will use this to generate the input. It is also used to validate the output of the language model. Use descriptions to make the input understandable for the language model. You can either pass in a Zod schema or a JSON schema (using the `jsonSchema` function).',
            },
            {
              name: 'execute',
              isOptional: true,
              type: 'async (parameters) => any',
              description:
                'An async function that is called with the arguments from the tool call and produces a result. If not provided, the tool will not be executed automatically.',
            },
          ],
        },
      ],
    },
    {
      name: 'toolChoice',
      isOptional: true,
      type: '"auto" | "none" | "required" | { "type": "tool", "toolName": string }',
      description:
        'The tool choice setting. It specifies how tools are selected for execution. The default is "auto". "none" disables tool execution. "required" requires tools to be executed. { "type": "tool", "toolName": string } specifies a specific tool to execute.',
    },
    {
      name: 'maxTokens',
      type: 'number',
      isOptional: true,
      description: 'Maximum number of tokens to generate.',
    },
    {
      name: 'temperature',
      type: 'number',
      isOptional: true,
      description:
        'Temperature setting. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.',
    },
    {
      name: 'topP',
      type: 'number',
      isOptional: true,
      description:
        'Nucleus sampling. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.',
    },
    {
      name: 'topK',
      type: 'number',
      isOptional: true,
      description:
        'Only sample from the top K options for each subsequent token. Used to remove "long tail" low probability responses. Recommended for advanced use cases only. You usually only need to use temperature.',
    },
    {
      name: 'presencePenalty',
      type: 'number',
      isOptional: true,
      description:
        'Presence penalty setting. It affects the likelihood of the model to repeat information that is already in the prompt. The value is passed through to the provider. The range depends on the provider and model.',
    },
    {
      name: 'frequencyPenalty',
      type: 'number',
      isOptional: true,
      description:
        'Frequency penalty setting. It affects the likelihood of the model to repeatedly use the same words or phrases. The value is passed through to the provider. The range depends on the provider and model.',
    },
    {
      name: 'stopSequences',
      type: 'string[]',
      isOptional: true,
      description:
        'Sequences that will stop the generation of the text. If the model generates any of these sequences, it will stop generating further text.',
    },
    {
      name: 'seed',
      type: 'number',
      isOptional: true,
      description:
        'The seed (integer) to use for random sampling. If set and supported by the model, calls will generate deterministic results.',
    },
    {
      name: 'maxRetries',
      type: 'number',
      isOptional: true,
      description:
        'Maximum number of retries. Set to 0 to disable retries. Default: 2.',
    },
    {
      name: 'abortSignal',
      type: 'AbortSignal',
      isOptional: true,
      description:
        'An optional abort signal that can be used to cancel the call.',
    },
    {
      name: 'headers',
      type: 'Record<string, string>',
      isOptional: true,
      description:
        'Additional HTTP headers to be sent with the request. Only applicable for HTTP-based providers.',
    },
    {
      name: 'experimental_telemetry',
      type: 'TelemetrySettings',
      isOptional: true,
      description: 'Telemetry configuration. Experimental feature.',
      properties: [
        {
          type: 'TelemetrySettings',
          parameters: [
            {
              name: 'isEnabled',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable telemetry. Disabled by default while experimental.',
            },
            {
              name: 'recordInputs',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable input recording. Enabled by default.',
            },
            {
              name: 'recordOutputs',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable output recording. Enabled by default.',
            },
            {
              name: 'functionId',
              type: 'string',
              isOptional: true,
              description:
                'Identifier for this function. Used to group telemetry data by function.',
            },
            {
              name: 'metadata',
              isOptional: true,
              type: 'Record<string, string | number | boolean | Array<null | undefined | string> | Array<null | undefined | number> | Array<null | undefined | boolean>>',
              description:
                'Additional information to include in the telemetry data.',
            },
          ],
        },
      ],
    },
    {
      name: 'experimental_toolCallStreaming',
      type: 'boolean',
      isOptional: true,
      description:
        'Enable streaming of tool call deltas as they are generated. Disabled by default.',
    },
    {
      name: 'onChunk',
      type: '(event: OnChunkResult) => Promise<void> |void',
      isOptional: true,
      description:
        'Callback that is called for each chunk of the stream. The stream processing will pause until the callback promise is resolved.',
      properties: [
        {
          type: 'OnChunkResult',
          parameters: [
            {
              name: 'chunk',
              type: 'TextStreamPart',
              description: 'The chunk of the stream.',
              properties: [
                {
                  type: 'TextStreamPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'text-delta'",
                      description:
                        'The type to identify the object as text delta.',
                    },
                    {
                      name: 'textDelta',
                      type: 'string',
                      description: 'The text delta.',
                    },
                  ],
                },
                {
                  type: 'TextStreamPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'tool-call'",
                      description:
                        'The type to identify the object as tool call.',
                    },
                    {
                      name: 'toolCallId',
                      type: 'string',
                      description: 'The id of the tool call.',
                    },
                    {
                      name: 'toolName',
                      type: 'string',
                      description:
                        'The name of the tool, which typically would be the name of the function.',
                    },
                    {
                      name: 'args',
                      type: 'object based on zod schema',
                      description:
                        'Parameters generated by the model to be used by the tool.',
                    },
                  ],
                },
                {
                  type: 'TextStreamPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'tool-call-streaming-start'",
                      description:
                        'Indicates the start of a tool call streaming. Only available when streaming tool calls.',
                    },
                    {
                      name: 'toolCallId',
                      type: 'string',
                      description: 'The id of the tool call.',
                    },
                    {
                      name: 'toolName',
                      type: 'string',
                      description:
                        'The name of the tool, which typically would be the name of the function.',
                    },
                  ],
                },
                {
                  type: 'TextStreamPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'tool-call-delta'",
                      description:
                        'The type to identify the object as tool call delta. Only available when streaming tool calls.',
                    },
                    {
                      name: 'toolCallId',
                      type: 'string',
                      description: 'The id of the tool call.',
                    },
                    {
                      name: 'toolName',
                      type: 'string',
                      description:
                        'The name of the tool, which typically would be the name of the function.',
                    },
                    {
                      name: 'argsTextDelta',
                      type: 'string',
                      description: 'The text delta of the tool call arguments.',
                    },
                  ],
                },
                {
                  type: 'TextStreamPart',
                  description: 'The result of a tool call execution.',
                  parameters: [
                    {
                      name: 'type',
                      type: "'tool-result'",
                      description:
                        'The type to identify the object as tool result.',
                    },
                    {
                      name: 'toolCallId',
                      type: 'string',
                      description: 'The id of the tool call.',
                    },
                    {
                      name: 'toolName',
                      type: 'string',
                      description:
                        'The name of the tool, which typically would be the name of the function.',
                    },
                    {
                      name: 'args',
                      type: 'object based on zod schema',
                      description:
                        'Parameters generated by the model to be used by the tool.',
                    },
                    {
                      name: 'result',
                      type: 'any',
                      description:
                        'The result returned by the tool after execution has completed.',
                    },
                  ],
                },
              ],
            },
          ],
        },
      ],
    },
    {
      name: 'onFinish',
      type: '(result: OnFinishResult) => Promise<void> | void',
      isOptional: true,
      description:
        'Callback that is called when the LLM response and all request tool executions (for tools that have an `execute` function) are finished.',
      properties: [
        {
          type: 'OnFinishResult',
          parameters: [
            {
              name: 'finishReason',
              type: '"stop" | "length" | "content-filter" | "tool-calls" | "error" | "other" | "unknown"',
              description: 'The reason the model finished generating the text.',
            },
            {
              name: 'usage',
              type: 'TokenUsage',
              description: 'The token usage of the generated text.',
              properties: [
                {
                  type: 'TokenUsage',
                  parameters: [
                    {
                      name: 'promptTokens',
                      type: 'number',
                      description: 'The total number of tokens in the prompt.',
                    },
                    {
                      name: 'completionTokens',
                      type: 'number',
                      description:
                        'The total number of tokens in the completion.',
                    },
                    {
                      name: 'totalTokens',
                      type: 'number',
                      description: 'The total number of tokens generated.',
                    },
                  ],
                },
              ],
            },
            {
              name: 'experimental_providerMetadata',
              type: 'Record<string,Record<string,JSONValue>> | undefined',
              description:
                'Optional metadata from the provider. The outer key is the provider name. The inner values are the metadata. Details depend on the provider.',
            },
            {
              name: 'text',
              type: 'string',
              description: 'The full text that has been generated.',
            },
            {
              name: 'toolCalls',
              type: 'ToolCall[]',
              description: 'The tool calls that have been executed.',
            },
            {
              name: 'toolResults',
              type: 'ToolResult[]',
              description: 'The tool results that have been generated.',
            },
            {
              name: 'warnings',
              type: 'Warning[] | undefined',
              description:
                'Warnings from the model provider (e.g. unsupported settings).',
            },
            {
              name: 'rawResponse',
              type: 'RawResponse',
              description: 'Optional raw response data.',
              properties: [
                {
                  type: 'RawResponse',
                  parameters: [
                    {
                      name: 'headers',
                      optional: true,
                      type: 'Record<string, string>',
                      description: 'Response headers.',
                    },
                  ],
                },
              ],
            },
          ],
        },
      ],
    },
  ]}
/>

##### Returns

<PropertiesTable
  content={[
    {
      name: 'finishReason',
      type: "Promise<'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown'>",
      description:
        'The reason why the generation finished. Resolved when the response is finished.',
    },
    {
      name: 'usage',
      type: 'Promise<CompletionTokenUsage>',
      description:
        'The token usage of the generated text. Resolved when the response is finished.',
      properties: [
        {
          type: 'CompletionTokenUsage',
          parameters: [
            {
              name: 'promptTokens',
              type: 'number',
              description: 'The total number of tokens in the prompt.',
            },
            {
              name: 'completionTokens',
              type: 'number',
              description: 'The total number of tokens in the completion.',
            },
            {
              name: 'totalTokens',
              type: 'number',
              description: 'The total number of tokens generated.',
            },
          ],
        },
      ],
    },
    {
      name: 'experimental_providerMetadata',
      type: 'Promise<Record<string,Record<string,JSONValue>> | undefined>',
      description:
        'Optional metadata from the provider. Resolved whe the response is finished. The outer key is the provider name. The inner values are the metadata. Details depend on the provider.',
    },
    {
      name: 'text',
      type: 'Promise<string>',
      description:
        'The full text that has been generated. Resolved when the response is finished.',
    },
    {
      name: 'toolCalls',
      type: 'Promise<ToolCall[]>',
      description:
        'The tool calls that have been executed. Resolved when the response is finished.',
    },
    {
      name: 'toolResults',
      type: 'Promise<ToolResult[]>',
      description:
        'The tool results that have been generated. Resolved when the all tool executions are finished.',
    },
    {
      name: 'rawResponse',
      type: 'RawResponse',
      optional: true,
      description: 'Optional raw response data.',
      properties: [
        {
          type: 'RawResponse',
          parameters: [
            {
              name: 'headers',
              optional: true,
              type: 'Record<string, string>',
              description: 'Response headers.',
            },
          ],
        },
      ],
    },
    {
      name: 'warnings',
      type: 'Warning[] | undefined',
      description:
        'Warnings from the model provider (e.g. unsupported settings).',
    },
    {
      name: 'textStream',
      type: 'AsyncIterable<string> & ReadableStream<string>',
      description:
        'A text stream that returns only the generated text deltas. You can use it as either an AsyncIterable or a ReadableStream. When an error occurs, the stream will throw the error.',
    },
    {
      name: 'fullStream',
      type: 'AsyncIterable<TextStreamPart> & ReadableStream<TextStreamPart>',
      description:
        'A stream with all events, including text deltas, tool calls, tool results, and errors. You can use it as either an AsyncIterable or a ReadableStream. Only errors that stop the stream, such as network errors, are thrown.',
      properties: [
        {
          type: 'TextStreamPart',
          parameters: [
            {
              name: 'type',
              type: "'text-delta'",
              description: 'The type to identify the object as text delta.',
            },
            {
              name: 'textDelta',
              type: 'string',
              description: 'The text delta.',
            },
          ],
        },
        {
          type: 'TextStreamPart',
          parameters: [
            {
              name: 'type',
              type: "'tool-call'",
              description: 'The type to identify the object as tool call.',
            },
            {
              name: 'toolCallId',
              type: 'string',
              description: 'The id of the tool call.',
            },
            {
              name: 'toolName',
              type: 'string',
              description:
                'The name of the tool, which typically would be the name of the function.',
            },
            {
              name: 'args',
              type: 'object based on zod schema',
              description:
                'Parameters generated by the model to be used by the tool.',
            },
          ],
        },
        {
          type: 'TextStreamPart',
          parameters: [
            {
              name: 'type',
              type: "'tool-call-streaming-start'",
              description:
                'Indicates the start of a tool call streaming. Only available when streaming tool calls.',
            },
            {
              name: 'toolCallId',
              type: 'string',
              description: 'The id of the tool call.',
            },
            {
              name: 'toolName',
              type: 'string',
              description:
                'The name of the tool, which typically would be the name of the function.',
            },
          ],
        },
        {
          type: 'TextStreamPart',
          parameters: [
            {
              name: 'type',
              type: "'tool-call-delta'",
              description:
                'The type to identify the object as tool call delta. Only available when streaming tool calls.',
            },
            {
              name: 'toolCallId',
              type: 'string',
              description: 'The id of the tool call.',
            },
            {
              name: 'toolName',
              type: 'string',
              description:
                'The name of the tool, which typically would be the name of the function.',
            },
            {
              name: 'argsTextDelta',
              type: 'string',
              description: 'The text delta of the tool call arguments.',
            },
          ],
        },
        {
          type: 'TextStreamPart',
          description: 'The result of a tool call execution.',
          parameters: [
            {
              name: 'type',
              type: "'tool-result'",
              description: 'The type to identify the object as tool result.',
            },
            {
              name: 'toolCallId',
              type: 'string',
              description: 'The id of the tool call.',
            },
            {
              name: 'toolName',
              type: 'string',
              description:
                'The name of the tool, which typically would be the name of the function.',
            },
            {
              name: 'args',
              type: 'object based on zod schema',
              description:
                'Parameters generated by the model to be used by the tool.',
            },
            {
              name: 'result',
              type: 'any',
              description:
                'The result returned by the tool after execution has completed.',
            },
          ],
        },
        {
          type: 'TextStreamPart',
          parameters: [
            {
              name: 'type',
              type: "'error'",
              description: 'The type to identify the object as error.',
            },
            {
              name: 'error',
              type: 'Error',
              description:
                'Describes the error that may have occurred during execution.',
            },
          ],
        },
        {
          type: 'TextStreamPart',
          parameters: [
            {
              name: 'type',
              type: "'finish'",
              description: 'The type to identify the object as finish.',
            },
            {
              name: 'finishReason',
              type: "'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown'",
              description: 'The reason the model finished generating the text.',
            },
            {
              name: 'usage',
              type: 'TokenUsage',
              description: 'The token usage of the generated text.',
              properties: [
                {
                  type: 'TokenUsage',
                  parameters: [
                    {
                      name: 'promptTokens',
                      type: 'number',
                      description: 'The total number of tokens in the prompt.',
                    },
                    {
                      name: 'completionTokens',
                      type: 'number',
                      description:
                        'The total number of tokens in the completion.',
                    },
                    {
                      name: 'totalTokens',
                      type: 'number',
                      description: 'The total number of tokens generated.',
                    },
                  ],
                },
              ],
            },
          ],
        },
      ],
    },
    {
      name: 'pipeDataStreamToResponse',
      type: '(response: ServerResponse, init?: { headers?: Record<string, string>; status?: number } => void',
      description:
        'Writes stream data output to a Node.js response-like object. It sets a `Content-Type` header to `text/plain; charset=utf-8` and writes each stream data part as a separate chunk.',
    },
    {
      name: 'pipeTextStreamToResponse',
      type: '(response: ServerResponse, init?: { headers?: Record<string, string>; status?: number } => void',
      description:
        'Writes text delta output to a Node.js response-like object. It sets a `Content-Type` header to `text/plain; charset=utf-8` and writes each text delta as a separate chunk.',
    },
    {
      name: 'toDataStreamResponse',
      type: '(options?: toDataStreamOptions) => Response',
      description:
        'Converts the result to a streamed response object with a stream data part stream. It can be used with the `useChat` and `useCompletion` hooks.',
      properties: [
        {
          type: 'toDataStreamOptions',
          parameters: [
            {
              name: 'init',
              type: 'ResponseInit',
              optional: true,
              description: 'The response init options.',
              properties: [
                {
                  type: 'ResponseInit',
                  parameters: [
                    {
                      name: 'status',
                      type: 'number',
                      optional: true,
                      description: 'The response status code.',
                    },
                    {
                      name: 'headers',
                      type: 'Record<string, string>',
                      optional: true,
                      description: 'The response headers.',
                    },
                  ],
                },
              ],
            },
            {
              name: 'data',
              type: 'StreamData',
              optional: true,
              description: 'The stream data object.',
            },
            {
              name: 'getErrorMessage',
              type: '(error: unknown) => string',
              description:
                'A function to get the error message from the error object. By default, all errors are masked as "" for safety reasons.',
              optional: true,
            },
          ],
        },
      ],
    },
    {
      name: 'toTextStreamResponse',
      type: '(init?: ResponseInit) => Response',
      description:
        'Creates a simple text stream response. Each text delta is encoded as UTF-8 and sent as a separate chunk. Non-text-delta events are ignored.',
    },
  ]}
/>

### `generateObject()`

Generates a typed, structured object for a given prompt and schema using a language model.

It can be used to force the language model to return structured data, e.g. for information extraction, synthetic data generation, or classification tasks.

```ts
import { openai } from '@ai-sdk/openai';
import { generateObject } from 'ai';
import { z } from 'zod';

const { object } = await generateObject({
  model: openai('gpt-4-turbo'),
  schema: z.object({
    recipe: z.object({
      name: z.string(),
      ingredients: z.array(z.string()),
      steps: z.array(z.string()),
    }),
  }),
  prompt: 'Generate a lasagna recipe.',
});

console.log(JSON.stringify(object, null, 2));
```

To see `generateObject` in action, check out [these examples](#examples).

#### Import

<Snippet text={`import { generateObject } from "ai"`} prompt={false} />

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'model',
      type: 'LanguageModel',
      description: "The language model to use. Example: openai('gpt-4-turbo')",
    },
    {
      name: 'mode',
      type: "'auto' | 'json' | 'tool'",
      description:
        "The mode to use for object generation. Not every model supports all modes. Defaults to 'auto'.",
    },
    {
      name: 'schema',
      type: 'Zod Schema | JSON Schema',
      description:
        'The schema that describes the shape of the object to generate. It is sent to the model to generate the object and used to validate the output. You can either pass in a Zod schema or a JSON schema (using the `jsonSchema` function).',
    },
    {
      name: 'schemaName',
      type: 'string | undefined',
      description:
        'Optional name of the output that should be generated. Used by some providers for additional LLM guidance, e.g. via tool or schema name.',
    },
    {
      name: 'schemaDescription',
      type: 'string | undefined',
      description:
        'Optional description of the output that should be generated. Used by some providers for additional LLM guidance, e.g. via tool or schema name.',
    },
    {
      name: 'system',
      type: 'string',
      description:
        'The system prompt to use that specifies the behavior of the model.',
    },
    {
      name: 'prompt',
      type: 'string',
      description: 'The input prompt to generate the text from.',
    },
    {
      name: 'messages',
      type: 'Array<CoreSystemMessage | CoreUserMessage | CoreAssistantMessage | CoreToolMessage>',
      description: 'A list of messages that represent a conversation.',
      properties: [
        {
          type: 'CoreSystemMessage',
          parameters: [
            {
              name: 'role',
              type: "'system'",
              description: 'The role for the system message.',
            },
            {
              name: 'content',
              type: 'string',
              description: 'The content of the message.',
            },
          ],
        },
        {
          type: 'CoreUserMessage',
          parameters: [
            {
              name: 'role',
              type: "'user'",
              description: 'The role for the user message.',
            },
            {
              name: 'content',
              type: 'string | Array<TextPart | ImagePart>',
              description: 'The content of the message.',
              properties: [
                {
                  type: 'TextPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'text'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'text',
                      type: 'string',
                      description: 'The text content of the message part.',
                    },
                  ],
                },
                {
                  type: 'ImagePart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'image'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'image',
                      type: 'string | Uint8Array | Buffer | ArrayBuffer | URL',
                      description:
                        'The image content of the message part. String are either base64 encoded content, base64 data URLs, or http(s) URLs.',
                    },
                  ],
                },
              ],
            },
          ],
        },
        {
          type: 'CoreAssistantMessage',
          parameters: [
            {
              name: 'role',
              type: "'assistant'",
              description: 'The role for the assistant message.',
            },
            {
              name: 'content',
              type: 'string | Array<TextPart | ToolCallPart>',
              description: 'The content of the message.',
              properties: [
                {
                  type: 'TextPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'text'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'text',
                      type: 'string',
                      description: 'The text content of the message part.',
                    },
                  ],
                },
                {
                  type: 'ToolCallPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'tool-call'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'toolCallId',
                      type: 'string',
                      description: 'The id of the tool call.',
                    },
                    {
                      name: 'toolName',
                      type: 'string',
                      description:
                        'The name of the tool, which typically would be the name of the function.',
                    },
                    {
                      name: 'args',
                      type: 'object based on schema',
                      description:
                        'Parameters generated by the model to be used by the tool.',
                    },
                  ],
                },
              ],
            },
          ],
        },
        {
          type: 'CoreToolMessage',
          parameters: [
            {
              name: 'role',
              type: "'tool'",
              description: 'The role for the assistant message.',
            },
            {
              name: 'content',
              type: 'Array<ToolResultPart>',
              description: 'The content of the message.',
              properties: [
                {
                  type: 'ToolResultPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'tool-result'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'toolCallId',
                      type: 'string',
                      description:
                        'The id of the tool call the result corresponds to.',
                    },
                    {
                      name: 'toolName',
                      type: 'string',
                      description:
                        'The name of the tool the result corresponds to.',
                    },
                    {
                      name: 'result',
                      type: 'unknown',
                      description:
                        'The result returned by the tool after execution.',
                    },
                    {
                      name: 'isError',
                      type: 'boolean',
                      isOptional: true,
                      description:
                        'Whether the result is an error or an error message.',
                    },
                  ],
                },
              ],
            },
          ],
        },
      ],
    },
    {
      name: 'maxTokens',
      type: 'number',
      isOptional: true,
      description: 'Maximum number of tokens to generate.',
    },
    {
      name: 'temperature',
      type: 'number',
      isOptional: true,
      description:
        'Temperature setting. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.',
    },
    {
      name: 'topP',
      type: 'number',
      isOptional: true,
      description:
        'Nucleus sampling. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.',
    },
    {
      name: 'topK',
      type: 'number',
      isOptional: true,
      description:
        'Only sample from the top K options for each subsequent token. Used to remove "long tail" low probability responses. Recommended for advanced use cases only. You usually only need to use temperature.',
    },
    {
      name: 'presencePenalty',
      type: 'number',
      isOptional: true,
      description:
        'Presence penalty setting. It affects the likelihood of the model to repeat information that is already in the prompt. The value is passed through to the provider. The range depends on the provider and model.',
    },
    {
      name: 'frequencyPenalty',
      type: 'number',
      isOptional: true,
      description:
        'Frequency penalty setting. It affects the likelihood of the model to repeatedly use the same words or phrases. The value is passed through to the provider. The range depends on the provider and model.',
    },
    {
      name: 'seed',
      type: 'number',
      isOptional: true,
      description:
        'The seed (integer) to use for random sampling. If set and supported by the model, calls will generate deterministic results.',
    },
    {
      name: 'maxRetries',
      type: 'number',
      isOptional: true,
      description:
        'Maximum number of retries. Set to 0 to disable retries. Default: 2.',
    },
    {
      name: 'abortSignal',
      type: 'AbortSignal',
      isOptional: true,
      description:
        'An optional abort signal that can be used to cancel the call.',
    },
    {
      name: 'headers',
      type: 'Record<string, string>',
      isOptional: true,
      description:
        'Additional HTTP headers to be sent with the request. Only applicable for HTTP-based providers.',
    },
    {
      name: 'experimental_telemetry',
      type: 'TelemetrySettings',
      isOptional: true,
      description: 'Telemetry configuration. Experimental feature.',
      properties: [
        {
          type: 'TelemetrySettings',
          parameters: [
            {
              name: 'isEnabled',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable telemetry. Disabled by default while experimental.',
            },
            {
              name: 'recordInputs',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable input recording. Enabled by default.',
            },
            {
              name: 'recordOutputs',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable output recording. Enabled by default.',
            },
            {
              name: 'functionId',
              type: 'string',
              isOptional: true,
              description:
                'Identifier for this function. Used to group telemetry data by function.',
            },
            {
              name: 'metadata',
              isOptional: true,
              type: 'Record<string, string | number | boolean | Array<null | undefined | string> | Array<null | undefined | number> | Array<null | undefined | boolean>>',
              description:
                'Additional information to include in the telemetry data.',
            },
          ],
        },
      ],
    },
  ]}
/>

##### Returns

<PropertiesTable
  content={[
    {
      name: 'object',
      type: 'based on the schema',
      description:
        'The generated object, validated by the schema (if it supports validation).',
    },
    {
      name: 'finishReason',
      type: "'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown'",
      description: 'The reason the model finished generating the text.',
    },
    {
      name: 'usage',
      type: 'CompletionTokenUsage',
      description: 'The token usage of the generated text.',
      properties: [
        {
          type: 'CompletionTokenUsage',
          parameters: [
            {
              name: 'promptTokens',
              type: 'number',
              description: 'The total number of tokens in the prompt.',
            },
            {
              name: 'completionTokens',
              type: 'number',
              description: 'The total number of tokens in the completion.',
            },
            {
              name: 'totalTokens',
              type: 'number',
              description: 'The total number of tokens generated.',
            },
          ],
        },
      ],
    },
    {
      name: 'rawResponse',
      type: 'RawResponse',
      optional: true,
      description: 'Optional raw response data.',
      properties: [
        {
          type: 'RawResponse',
          parameters: [
            {
              name: 'headers',
              optional: true,
              type: 'Record<string, string>',
              description: 'Response headers.',
            },
          ],
        },
      ],
    },
    {
      name: 'warnings',
      type: 'Warning[] | undefined',
      description:
        'Warnings from the model provider (e.g. unsupported settings).',
    },
    {
      name: 'experimental_providerMetadata',
      type: 'Record<string,Record<string,JSONValue>> | undefined',
      description:
        'Optional metadata from the provider. The outer key is the provider name. The inner values are the metadata. Details depend on the provider.',
    },
    {
      name: 'toJsonResponse',
      type: '(init?: ResponseInit) => Response',
      description:
        'Converts the object to a JSON response. The response will have a status code of 200 and a content type of `application/json; charset=utf-8`.',
    },
  ]}
/>

### `streamObject()`

Streams a typed, structured object for a given prompt and schema using a language model.

It can be used to force the language model to return structured data, e.g. for information extraction, synthetic data generation, or classification tasks.

```ts
import { openai } from '@ai-sdk/openai';
import { streamObject } from 'ai';
import { z } from 'zod';

const { partialObjectStream } = await streamObject({
  model: openai('gpt-4-turbo'),
  schema: z.object({
    recipe: z.object({
      name: z.string(),
      ingredients: z.array(z.string()),
      steps: z.array(z.string()),
    }),
  }),
  prompt: 'Generate a lasagna recipe.',
});

for await (const partialObject of partialObjectStream) {
  console.clear();
  console.log(partialObject);
}
```

To see `streamObject` in action, check out [these examples](#examples).

#### Import

<Snippet text={`import { streamObject } from "ai"`} prompt={false} />

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'model',
      type: 'LanguageModel',
      description: "The language model to use. Example: openai('gpt-4-turbo')",
    },
    {
      name: 'mode',
      type: "'auto' | 'json' | 'tool'",
      description:
        "The mode to use for object generation. Not every model supports all modes. Defaults to 'auto'.",
    },
    {
      name: 'schema',
      type: 'Zod Schema | JSON Schema',
      description:
        'The schema that describes the shape of the object to generate. It is sent to the model to generate the object and used to validate the output. You can either pass in a Zod schema or a JSON schema (using the `jsonSchema` function).',
    },
    {
      name: 'schemaName',
      type: 'string | undefined',
      description:
        'Optional name of the output that should be generated. Used by some providers for additional LLM guidance, e.g. via tool or schema name.',
    },
    {
      name: 'schemaDescription',
      type: 'string | undefined',
      description:
        'Optional description of the output that should be generated. Used by some providers for additional LLM guidance, e.g. via tool or schema name.',
    },
    {
      name: 'system',
      type: 'string',
      description:
        'The system prompt to use that specifies the behavior of the model.',
    },
    {
      name: 'prompt',
      type: 'string',
      description: 'The input prompt to generate the text from.',
    },
    {
      name: 'messages',
      type: 'Array<CoreSystemMessage | CoreUserMessage | CoreAssistantMessage | CoreToolMessage>',
      description: 'A list of messages that represent a conversation.',
      properties: [
        {
          type: 'CoreSystemMessage',
          parameters: [
            {
              name: 'role',
              type: "'system'",
              description: 'The role for the system message.',
            },
            {
              name: 'content',
              type: 'string',
              description: 'The content of the message.',
            },
          ],
        },
        {
          type: 'CoreUserMessage',
          parameters: [
            {
              name: 'role',
              type: "'user'",
              description: 'The role for the user message.',
            },
            {
              name: 'content',
              type: 'string | Array<TextPart | ImagePart>',
              description: 'The content of the message.',
              properties: [
                {
                  type: 'TextPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'text'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'text',
                      type: 'string',
                      description: 'The text content of the message part.',
                    },
                  ],
                },
                {
                  type: 'ImagePart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'image'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'image',
                      type: 'string | Uint8Array | Buffer | ArrayBuffer | URL',
                      description:
                        'The image content of the message part. String are either base64 encoded content, base64 data URLs, or http(s) URLs.',
                    },
                  ],
                },
              ],
            },
          ],
        },
        {
          type: 'CoreAssistantMessage',
          parameters: [
            {
              name: 'role',
              type: "'assistant'",
              description: 'The role for the assistant message.',
            },
            {
              name: 'content',
              type: 'string | Array<TextPart | ToolCallPart>',
              description: 'The content of the message.',
              properties: [
                {
                  type: 'TextPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'text'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'text',
                      type: 'string',
                      description: 'The text content of the message part.',
                    },
                  ],
                },
                {
                  type: 'ToolCallPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'tool-call'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'toolCallId',
                      type: 'string',
                      description: 'The id of the tool call.',
                    },
                    {
                      name: 'toolName',
                      type: 'string',
                      description:
                        'The name of the tool, which typically would be the name of the function.',
                    },
                    {
                      name: 'args',
                      type: 'object based on schema',
                      description:
                        'Parameters generated by the model to be used by the tool.',
                    },
                  ],
                },
              ],
            },
          ],
        },
        {
          type: 'CoreToolMessage',
          parameters: [
            {
              name: 'role',
              type: "'tool'",
              description: 'The role for the assistant message.',
            },
            {
              name: 'content',
              type: 'Array<ToolResultPart>',
              description: 'The content of the message.',
              properties: [
                {
                  type: 'ToolResultPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'tool-result'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'toolCallId',
                      type: 'string',
                      description:
                        'The id of the tool call the result corresponds to.',
                    },
                    {
                      name: 'toolName',
                      type: 'string',
                      description:
                        'The name of the tool the result corresponds to.',
                    },
                    {
                      name: 'result',
                      type: 'unknown',
                      description:
                        'The result returned by the tool after execution.',
                    },
                    {
                      name: 'isError',
                      type: 'boolean',
                      isOptional: true,
                      description:
                        'Whether the result is an error or an error message.',
                    },
                  ],
                },
              ],
            },
          ],
        },
      ],
    },
    {
      name: 'maxTokens',
      type: 'number',
      isOptional: true,
      description: 'Maximum number of tokens to generate.',
    },
    {
      name: 'temperature',
      type: 'number',
      isOptional: true,
      description:
        'Temperature setting. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.',
    },
    {
      name: 'topP',
      type: 'number',
      isOptional: true,
      description:
        'Nucleus sampling. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.',
    },
    {
      name: 'topK',
      type: 'number',
      isOptional: true,
      description:
        'Only sample from the top K options for each subsequent token. Used to remove "long tail" low probability responses. Recommended for advanced use cases only. You usually only need to use temperature.',
    },
    {
      name: 'presencePenalty',
      type: 'number',
      isOptional: true,
      description:
        'Presence penalty setting. It affects the likelihood of the model to repeat information that is already in the prompt. The value is passed through to the provider. The range depends on the provider and model.',
    },
    {
      name: 'frequencyPenalty',
      type: 'number',
      isOptional: true,
      description:
        'Frequency penalty setting. It affects the likelihood of the model to repeatedly use the same words or phrases. The value is passed through to the provider. The range depends on the provider and model.',
    },
    {
      name: 'seed',
      type: 'number',
      isOptional: true,
      description:
        'The seed (integer) to use for random sampling. If set and supported by the model, calls will generate deterministic results.',
    },
    {
      name: 'maxRetries',
      type: 'number',
      isOptional: true,
      description:
        'Maximum number of retries. Set to 0 to disable retries. Default: 2.',
    },
    {
      name: 'abortSignal',
      type: 'AbortSignal',
      isOptional: true,
      description:
        'An optional abort signal that can be used to cancel the call.',
    },
    {
      name: 'headers',
      type: 'Record<string, string>',
      isOptional: true,
      description:
        'Additional HTTP headers to be sent with the request. Only applicable for HTTP-based providers.',
    },
    {
      name: 'experimental_telemetry',
      type: 'TelemetrySettings',
      isOptional: true,
      description: 'Telemetry configuration. Experimental feature.',
      properties: [
        {
          type: 'TelemetrySettings',
          parameters: [
            {
              name: 'isEnabled',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable telemetry. Disabled by default while experimental.',
            },
            {
              name: 'recordInputs',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable input recording. Enabled by default.',
            },
            {
              name: 'recordOutputs',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable output recording. Enabled by default.',
            },
            {
              name: 'functionId',
              type: 'string',
              isOptional: true,
              description:
                'Identifier for this function. Used to group telemetry data by function.',
            },
            {
              name: 'metadata',
              isOptional: true,
              type: 'Record<string, string | number | boolean | Array<null | undefined | string> | Array<null | undefined | number> | Array<null | undefined | boolean>>',
              description:
                'Additional information to include in the telemetry data.',
            },
          ],
        },
      ],
    },
    {
      name: 'onFinish',
      type: '(result: OnFinishResult) => void',
      isOptional: true,
      description:
        'Callback that is called when the LLM response has finished.',
      properties: [
        {
          type: 'OnFinishResult',
          parameters: [
            {
              name: 'usage',
              type: 'CompletionTokenUsage',
              description: 'The token usage of the generated text.',
              properties: [
                {
                  type: 'CompletionTokenUsage',
                  parameters: [
                    {
                      name: 'promptTokens',
                      type: 'number',
                      description: 'The total number of tokens in the prompt.',
                    },
                    {
                      name: 'completionTokens',
                      type: 'number',
                      description:
                        'The total number of tokens in the completion.',
                    },
                    {
                      name: 'totalTokens',
                      type: 'number',
                      description: 'The total number of tokens generated.',
                    },
                  ],
                },
              ],
            },
            {
              name: 'experimental_providerMetadata',
              type: 'Record<string,Record<string,JSONValue>> | undefined',
              description:
                'Optional metadata from the provider. The outer key is the provider name. The inner values are the metadata. Details depend on the provider.',
            },
            {
              name: 'object',
              type: 'T | undefined',
              description:
                'The generated object (typed according to the schema). Can be undefined if the final object does not match the schema.',
            },
            {
              name: 'error',
              type: 'unknown | undefined',
              description:
                'Optional error object. This is e.g. a TypeValidationError when the final object does not match the schema.',
            },
            {
              name: 'warnings',
              type: 'Warning[] | undefined',
              description:
                'Warnings from the model provider (e.g. unsupported settings).',
            },
            {
              name: 'rawResponse',
              type: 'RawResponse',
              description: 'Optional raw response data.',
              properties: [
                {
                  type: 'RawResponse',
                  parameters: [
                    {
                      name: 'headers',
                      optional: true,
                      type: 'Record<string, string>',
                      description: 'Response headers.',
                    },
                  ],
                },
              ],
            },
          ],
        },
      ],
    },
  ]}
/>

##### Returns

<PropertiesTable
  content={[
    {
      name: 'usage',
      type: 'Promise<CompletionTokenUsage>',
      description:
        'The token usage of the generated text. Resolved when the response is finished.',
      properties: [
        {
          type: 'CompletionTokenUsage',
          parameters: [
            {
              name: 'promptTokens',
              type: 'number',
              description: 'The total number of tokens in the prompt.',
            },
            {
              name: 'completionTokens',
              type: 'number',
              description: 'The total number of tokens in the completion.',
            },
            {
              name: 'totalTokens',
              type: 'number',
              description: 'The total number of tokens generated.',
            },
          ],
        },
      ],
    },
    {
      name: 'experimental_providerMetadata',
      type: 'Promise<Record<string,Record<string,JSONValue>> | undefined>',
      description:
        'Optional metadata from the provider. Resolved whe the response is finished. The outer key is the provider name. The inner values are the metadata. Details depend on the provider.',
    },
    {
      name: 'object',
      type: 'Promise<T>',
      description:
        'The generated object (typed according to the schema). Resolved when the response is finished.',
    },
    {
      name: 'partialObjectStream',
      type: 'AsyncIterableStream<DeepPartial<T>>',
      description:
        'Stream of partial objects. It gets more complete as the stream progresses. Note that the partial object is not validated. If you want to be certain that the actual content matches your schema, you need to implement your own validation for partial results.',
    },
    {
      name: 'textStream',
      type: 'AsyncIterableStream<string>',
      description:
        'Text stream of the JSON representation of the generated object. It contains text chunks. When the stream is finished, the object is valid JSON that can be parsed.',
    },
    {
      name: 'fullStream',
      type: 'AsyncIterableStream<ObjectStreamPart<T>>',
      description:
        'Stream of different types of events, including partial objects, errors, and finish events. Only errors that stop the stream, such as network errors, are thrown.',
      properties: [
        {
          type: 'ObjectPart',
          parameters: [
            {
              name: 'type',
              type: "'object'",
            },
            {
              name: 'object',
              type: 'DeepPartial<T>',
              description: 'The partial object that was generated.',
            },
          ],
        },
        {
          type: 'TextDeltaPart',
          parameters: [
            {
              name: 'type',
              type: "'text-delta'",
            },
            {
              name: 'textDelta',
              type: 'string',
              description: 'The text delta for the underlying raw JSON text.',
            },
          ],
        },
        {
          type: 'ErrorPart',
          parameters: [
            {
              name: 'type',
              type: "'error'",
            },
            {
              name: 'error',
              type: 'unknown',
              description: 'The error that occurred.',
            },
          ],
        },
        {
          type: 'FinishPart',
          parameters: [
            {
              name: 'type',
              type: "'finish'",
            },
            {
              name: 'finishReason',
              type: 'FinishReason',
            },
            {
              name: 'logprobs',
              type: 'Logprobs',
              optional: true,
            },
            {
              name: 'usage',
              type: 'Usage',
              description: 'Token usage.',
            },
          ],
        },
      ],
    },
    {
      name: 'rawResponse',
      type: 'RawResponse',
      optional: true,
      description: 'Optional raw response data.',
      properties: [
        {
          type: 'RawResponse',
          parameters: [
            {
              name: 'headers',
              optional: true,
              type: 'Record<string, string>',
              description: 'Response headers.',
            },
          ],
        },
      ],
    },
    {
      name: 'warnings',
      type: 'Warning[] | undefined',
      description:
        'Warnings from the model provider (e.g. unsupported settings).',
    },
    {
      name: 'pipeTextStreamToResponse',
      type: '(response: ServerResponse, init?: { headers?: Record<string, string>; status?: number } => void',
      description:
        'Writes text delta output to a Node.js response-like object. It sets a `Content-Type` header to `text/plain; charset=utf-8` and writes each text delta as a separate chunk.',
    },
    {
      name: 'toTextStreamResponse',
      type: '(init?: ResponseInit) => Response',
      description:
        'Creates a simple text stream response. Each text delta is encoded as UTF-8 and sent as a separate chunk. Non-text-delta events are ignored.',
    },
  ]}
/>

### `embed()`

Generate an embedding for a single value using an embedding model.

This is ideal for use cases where you need to embed a single value to e.g. retrieve similar items or to use the embedding in a downstream task.

```ts
import { openai } from '@ai-sdk/openai';
import { embed } from 'ai';

const { embedding } = await embed({
  model: openai.embedding('text-embedding-3-small'),
  value: 'sunny day at the beach',
});
```

#### Import

<Snippet text={`import { embed } from "ai"`} prompt={false} />

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'model',
      type: 'EmbeddingModel',
      description:
        "The embedding model to use. Example: openai.embedding('text-embedding-3-small')",
    },
    {
      name: 'value',
      type: 'VALUE',
      description: 'The value to embed. The type depends on the model.',
    },
    {
      name: 'maxRetries',
      type: 'number',
      isOptional: true,
      description:
        'Maximum number of retries. Set to 0 to disable retries. Default: 2.',
    },
    {
      name: 'abortSignal',
      type: 'AbortSignal',
      isOptional: true,
      description:
        'An optional abort signal that can be used to cancel the call.',
    },
    {
      name: 'headers',
      type: 'Record<string, string>',
      isOptional: true,
      description:
        'Additional HTTP headers to be sent with the request. Only applicable for HTTP-based providers.',
    },
    {
      name: 'experimental_telemetry',
      type: 'TelemetrySettings',
      isOptional: true,
      description: 'Telemetry configuration. Experimental feature.',
      properties: [
        {
          type: 'TelemetrySettings',
          parameters: [
            {
              name: 'isEnabled',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable telemetry. Disabled by default while experimental.',
            },
            {
              name: 'recordInputs',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable input recording. Enabled by default.',
            },
            {
              name: 'recordOutputs',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable output recording. Enabled by default.',
            },
            {
              name: 'functionId',
              type: 'string',
              isOptional: true,
              description:
                'Identifier for this function. Used to group telemetry data by function.',
            },
            {
              name: 'metadata',
              isOptional: true,
              type: 'Record<string, string | number | boolean | Array<null | undefined | string> | Array<null | undefined | number> | Array<null | undefined | boolean>>',
              description:
                'Additional information to include in the telemetry data.',
            },
          ],
        },
      ],
    },
  ]}
/>

##### Returns

<PropertiesTable
  content={[
    {
      name: 'value',
      type: 'VALUE',
      description: 'The value that was embedded.',
    },
    {
      name: 'embedding',
      type: 'number[]',
      description: 'The embedding of the value.',
    },
    {
      name: 'usage',
      type: 'EmbeddingTokenUsage',
      description: 'The token usage for generating the embeddings.',
      properties: [
        {
          type: 'EmbeddingTokenUsage',
          parameters: [
            {
              name: 'tokens',
              type: 'number',
              description: 'The total number of input tokens.',
            },
          ],
        },
      ],
    },
    {
      name: 'rawResponse',
      type: 'RawResponse',
      optional: true,
      description: 'Optional raw response data.',
      properties: [
        {
          type: 'RawResponse',
          parameters: [
            {
              name: 'headers',
              optional: true,
              type: 'Record<string, string>',
              description: 'Response headers.',
            },
          ],
        },
      ],
    },
  ]}
/>

### `embedMany()`

Embed several values using an embedding model. The type of the value is defined
by the embedding model.

`embedMany` automatically splits large requests into smaller chunks if the model
has a limit on how many embeddings can be generated in a single call.

```ts
import { openai } from '@ai-sdk/openai';
import { embedMany } from 'ai';

const { embeddings } = await embedMany({
  model: openai.embedding('text-embedding-3-small'),
  values: [
    'sunny day at the beach',
    'rainy afternoon in the city',
    'snowy night in the mountains',
  ],
});
```

#### Import

<Snippet text={`import { embedMany } from "ai"`} prompt={false} />

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'model',
      type: 'EmbeddingModel',
      description:
        "The embedding model to use. Example: openai.embedding('text-embedding-3-small')",
    },
    {
      name: 'values',
      type: 'Array<VALUE>',
      description: 'The values to embed. The type depends on the model.',
    },
    {
      name: 'maxRetries',
      type: 'number',
      isOptional: true,
      description:
        'Maximum number of retries. Set to 0 to disable retries. Default: 2.',
    },
    {
      name: 'abortSignal',
      type: 'AbortSignal',
      isOptional: true,
      description:
        'An optional abort signal that can be used to cancel the call.',
    },
    {
      name: 'headers',
      type: 'Record<string, string>',
      isOptional: true,
      description:
        'Additional HTTP headers to be sent with the request. Only applicable for HTTP-based providers.',
    },
    {
      name: 'experimental_telemetry',
      type: 'TelemetrySettings',
      isOptional: true,
      description: 'Telemetry configuration. Experimental feature.',
      properties: [
        {
          type: 'TelemetrySettings',
          parameters: [
            {
              name: 'isEnabled',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable telemetry. Disabled by default while experimental.',
            },
            {
              name: 'recordInputs',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable input recording. Enabled by default.',
            },
            {
              name: 'recordOutputs',
              type: 'boolean',
              isOptional: true,
              description:
                'Enable or disable output recording. Enabled by default.',
            },
            {
              name: 'functionId',
              type: 'string',
              isOptional: true,
              description:
                'Identifier for this function. Used to group telemetry data by function.',
            },
            {
              name: 'metadata',
              isOptional: true,
              type: 'Record<string, string | number | boolean | Array<null | undefined | string> | Array<null | undefined | number> | Array<null | undefined | boolean>>',
              description:
                'Additional information to include in the telemetry data.',
            },
          ],
        },
      ],
    },
  ]}
/>

##### Returns

<PropertiesTable
  content={[
    {
      name: 'values',
      type: 'Array<VALUE>',
      description: 'The values that were embedded.',
    },
    {
      name: 'embeddings',
      type: 'number[][]',
      description: 'The embeddings. They are in the same order as the values.',
    },
    {
      name: 'usage',
      type: 'EmbeddingTokenUsage',
      description: 'The token usage for generating the embeddings.',
      properties: [
        {
          type: 'EmbeddingTokenUsage',
          parameters: [
            {
              name: 'tokens',
              type: 'number',
              description: 'The total number of input tokens.',
            },
          ],
        },
      ],
    },
  ]}
/>

### `tool()`

Tool is a helper function that infers the tool paramaters for its `execute` method.

It does not have any runtime behavior, but it helps TypeScript infer the types of the parameters for the `execute` method.

Without this helper function, TypeScript is unable to connect the `parameters` property to the `execute` method,
and the argument types of `execute` cannot be inferred.

```ts highlight={"1,4,9,10"}
import { tool } from 'ai';
import { z } from 'zod';

export const weatherTool = tool({
  description: 'Get the weather in a location',
  parameters: z.object({
    location: z.string().describe('The location to get the weather for'),
  }),
  // location below is inferred to be a string:
  execute: async ({ location }) => ({
    location,
    temperature: 72 + Math.floor(Math.random() * 21) - 10,
  }),
});
```

#### Import

<Snippet text={`import { tool } from "ai"`} prompt={false} />

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'tool',
      type: 'CoreTool',
      description: 'The tool definition.',
      properties: [
        {
          type: 'CoreTool',
          parameters: [
            {
              name: 'description',
              isOptional: true,
              type: 'string',
              description:
                'Information about the purpose of the tool including details on how and when it can be used by the model.',
            },
            {
              name: 'parameters',
              type: 'Zod Schema | JSON Schema',
              description:
                'The schema of the input that the tool expects. The language model will use this to generate the input. It is also used to validate the output of the language model. Use descriptions to make the input understandable for the language model. You can either pass in a Zod schema or a JSON schema (using the `jsonSchema` function).',
            },
            {
              name: 'execute',
              isOptional: true,
              type: 'async (parameters) => any',
              description:
                'An async function that is called with the arguments from the tool call and produces a result. If not provided, the tool will not be executed automatically.',
            },
          ],
        },
      ],
    },
  ]}
/>

##### Returns

The tool that was passed in.


### `jsonSchema()`

`jsonSchema` is a helper function that creates a JSON schema object that is compatible with the AI SDK.
It takes the JSON schema and an optional validation function as inputs, and can be typed.

You can use it to [generate structured data](/docs/ai-sdk-core/generating-structured-data) and in [tools](/docs/ai-sdk-core/tools-and-tool-calling).

`jsonSchema` is an alternative to using Zod schemas that provides you with flexibility in dynamic situations
(e.g. when using OpenAPI definitions) or for using other validation libraries.

```ts
import { jsonSchema } from 'ai';

const mySchema = jsonSchema<{
  recipe: {
    name: string;
    ingredients: { name: string; amount: string }[];
    steps: string[];
  };
}>({
  type: 'object',
  properties: {
    recipe: {
      type: 'object',
      properties: {
        name: { type: 'string' },
        ingredients: {
          type: 'array',
          items: {
            type: 'object',
            properties: {
              name: { type: 'string' },
              amount: { type: 'string' },
            },
            required: ['name', 'amount'],
          },
        },
        steps: {
          type: 'array',
          items: { type: 'string' },
        },
      },
      required: ['name', 'ingredients', 'steps'],
    },
  },
  required: ['recipe'],
});
```

#### Import

<Snippet text={`import { jsonSchema } from "ai"`} prompt={false} />

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'schema',
      type: 'JSONSchema7',
      description: 'The JSON schema definition.',
    },
    {
      name: 'options',
      type: 'SchemaOptions',
      description: 'Additional options for the JSON schema.',
      properties: [
        {
          type: 'SchemaOptions',
          parameters: [
            {
              name: 'validate',
              isOptional: true,
              type: '(value: unknown) => { success: true; value: OBJECT } | { success: false; error: Error };',
              description:
                'A function that validates the value against the JSON schema. If the value is valid, the function should return an object with a `success` property set to `true` and a `value` property set to the validated value. If the value is invalid, the function should return an object with a `success` property set to `false` and an `error` property set to the error.',
            },
          ],
        },
      ],
    },
  ]}
/>

##### Returns

A JSON schema object that is compatible with the AI SDK.

### `CoreMessage`

`CoreMessage` represents the fundamental message structure used with AI SDK Core functions. It encompasses various message types that can be used in the `messages` field of any AI SDK Core functions.

#### `CoreMessage` Types

##### `CoreSystemMessage`

A system message that can contain system information.

```typescript
type CoreSystemMessage = {
  role: 'system';
  content: string;
};
```

<Note>
  {' '}
  Using the "system" part of the prompt is strongly recommended to enhance resilience
  against prompt injection attacks and because not all providers support multiple
  system messages.
</Note>

##### `CoreUserMessage`

A user message that can contain text or a combination of text and images.

```typescript
type CoreUserMessage = {
  role: 'user';
  content: UserContent;
};

type UserContent = string | Array<TextPart | ImagePart>;
```

##### `CoreAssistantMessage`

An assistant message that can contain text, tool calls, or a combination of both.

```typescript
type CoreAssistantMessage = {
  role: 'assistant';
  content: AssistantContent;
};

type AssistantContent = string | Array<TextPart | ToolCallPart>;
```

##### `CoreToolMessage`

A tool message that contains the result of one or more tool calls.

```typescript
type CoreToolMessage = {
  role: 'tool';
  content: ToolContent;
};

type ToolContent = Array<ToolResultPart>;
```

#### `CoreMessage` Parts

##### `TextPart`

Represents a text content part of a prompt. It contains a string of text.

```typescript
export interface TextPart {
  type: 'text';
  /**
   * The text content.
   */
  text: string;
}
```

##### `ImagePart`

Represents an image part in a user message.

```typescript
export interface ImagePart {
  type: 'image';
  /**
   * Image data. Can either be:
   * - data: a base64-encoded string, a Uint8Array, an ArrayBuffer, or a Buffer
   * - URL: a URL that points to the image
   */
  image: DataContent | URL;
  /**
   * Optional mime type of the image.
   */
  mimeType?: string;
}
```

##### `ToolCallPart`

Represents a tool call content part of a prompt, typically generated by the AI model.

```typescript
export interface ToolCallPart {
  type: 'tool-call';
  /**
   * ID of the tool call. This ID is used to match the tool call with the tool result.
   */
  toolCallId: string;
  /**
   * Name of the tool that is being called.
   */
  toolName: string;
  /**
   * Arguments of the tool call. This is a JSON-serializable object that matches the tool's input schema.
   */
  args: unknown;
}
```

##### `ToolResultPart`

Represents the result of a tool call in a tool message.

```typescript
export interface ToolResultPart {
  type: 'tool-result';
  /**
   * ID of the tool call that this result is associated with.
   */
  toolCallId: string;
  /**
   * Name of the tool that generated this result.
   */
  toolName: string;
  /**
   * Result of the tool call. This is a JSON-serializable object.
   */
  result: unknown;
  /**
   * Optional flag if the result is an error or an error message.
   */
  isError?: boolean;
}
```

### `experimental_createProviderRegistry()`

<Note>Provider management is an experimental feature.</Note>

When you work with multiple providers and models, it is often desirable to manage them
in a central place and access the models through simple string ids.

`createProviderRegistry` lets you create a registry with multiple providers that you
can access by their ids.

#### Import

<Snippet
  text={`import { experimental_createProviderRegistry as createProviderRegistry } from "ai"`}
  prompt={false}
/>

#### API Signature

Registers a language model provider with a given id.

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'providers',
      type: 'Record<string, { languageModel: (id: string) => LanguageModel; textEmbedding: (id: string) => EmbeddingModel<string> }>',
      description:
        'The unique identifier for the provider. It should be unique within the registry.',
    },
  ]}
/>

##### Returns

The `experimental_createProviderRegistry` function returns a `experimental_ProviderRegistry` instance. It has the following methods:

<PropertiesTable
  content={[
    {
      name: 'languageModel',
      type: '(id: string) => LanguageModel',
      description:
        'A function that returns a language model by its id (format: providerId:modelId)',
    },
    {
      name: 'textEmbeddingModel',
      type: '(id: string) => EmbeddingModel<string>',
      description:
        'A function that returns a text embedding model by its id (format: providerId:modelId)',
    },
  ]}
/>

#### Examples

##### Setup

You can create a registry with multiple providers and models using `createProviderRegistry`.

```ts
import { anthropic } from '@ai-sdk/anthropic';
import { createOpenAI } from '@ai-sdk/openai';
import { experimental_createProviderRegistry as createProviderRegistry } from 'ai';

export const registry = createProviderRegistry({
  // register provider with prefix and default setup:
  anthropic,

  // register provider with prefix and custom setup:
  openai: createOpenAI({
    apiKey: process.env.OPENAI_API_KEY,
  }),
});
```

##### Language models

You can access language models by using the `languageModel` method on the registry.
The provider id will become the prefix of the model id: `providerId:modelId`.

```ts highlight={"5"}
import { generateText } from 'ai';
import { registry } from './registry';

const { text } = await generateText({
  model: registry.languageModel('openai:gpt-4-turbo'),
  prompt: 'Invent a new holiday and describe its traditions.',
});
```

##### Text embedding models

You can access text embedding models by using the `textEmbeddingModel` method on the registry.
The provider id will become the prefix of the model id: `providerId:modelId`.

```ts highlight={"5"}
import { embed } from 'ai';
import { registry } from './registry';

const { embedding } = await embed({
  model: registry.textEmbeddingModel('openai:text-embedding-3-small'),
  value: 'sunny day at the beach',
});
```

### `cosineSimilarity()`

When you want to compare the similarity of embeddings, standard vector similarity metrics
like cosine similarity are often used.

`cosineSimilarity` calculates the cosine similarity between two vectors.
A high value (close to 1) indicates that the vectors are very similar, while a low value (close to -1) indicates that they are different.

```ts
import { openai } from '@ai-sdk/openai';
import { cosineSimilarity, embedMany } from 'ai';

const { embeddings } = await embedMany({
  model: openai.embedding('text-embedding-3-small'),
  values: ['sunny day at the beach', 'rainy afternoon in the city'],
});

console.log(
  `cosine similarity: ${cosineSimilarity(embeddings[0], embeddings[1])}`,
);
```

#### Import

<Snippet text={`import { cosineSimilarity } from "ai"`} prompt={false} />

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'vector1',
      type: 'number[]',
      description: 'The first vector to compare',
    },
    {
      name: 'vector2',
      type: 'number[]',
      description: 'The second vector to compare',
    },
  ]}
/>

##### Returns

A number between -1 and 1 representing the cosine similarity between the two vectors.

## API Reference For AI SDK RSC

### `streamUI`

A helper function to create a streamable UI from LLM providers. This function is similar to AI SDK Core APIs and supports the same model interfaces.

To see `streamUI` in action, check out [these examples](#examples).

#### Import

<Snippet text={`import { streamUI } from "ai/rsc"`} prompt={false} />

#### Parameters

<PropertiesTable
  content={[
    {
      name: 'model',
      type: 'LanguageModel',
      description: 'The language model to use. Example: openai("gpt-4-turbo")',
    },
    {
      name: 'initial',
      isOptional: true,
      type: 'ReactNode',
      description: 'The initial UI to render.',
    },
    {
      name: 'system',
      type: 'string',
      description:
        'The system prompt to use that specifies the behavior of the model.',
    },
    {
      name: 'prompt',
      type: 'string',
      description: 'The input prompt to generate the text from.',
    },
    {
      name: 'messages',
      type: 'Array<CoreSystemMessage | CoreUserMessage | CoreAssistantMessage | CoreToolMessage>',
      description: 'A list of messages that represent a conversation.',
      properties: [
        {
          type: 'CoreSystemMessage',
          parameters: [
            {
              name: 'role',
              type: "'system'",
              description: 'The role for the system message.',
            },
            {
              name: 'content',
              type: 'string',
              description: 'The content of the message.',
            },
          ],
        },
        {
          type: 'CoreUserMessage',
          parameters: [
            {
              name: 'role',
              type: "'user'",
              description: 'The role for the user message.',
            },
            {
              name: 'content',
              type: 'string | Array<TextPart | ImagePart>',
              description: 'The content of the message.',
              properties: [
                {
                  type: 'TextPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'text'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'text',
                      type: 'string',
                      description: 'The text content of the message part.',
                    },
                  ],
                },
                {
                  type: 'ImagePart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'image'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'image',
                      type: 'string | Uint8Array | Buffer | ArrayBuffer | URL',
                      description:
                        'The image content of the message part. String are either base64 encoded content, base64 data URLs, or http(s) URLs.',
                    },
                  ],
                },
              ],
            },
          ],
        },
        {
          type: 'CoreAssistantMessage',
          parameters: [
            {
              name: 'role',
              type: "'assistant'",
              description: 'The role for the assistant message.',
            },
            {
              name: 'content',
              type: 'string | Array<TextPart | ToolCallPart>',
              description: 'The content of the message.',
              properties: [
                {
                  type: 'TextPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'text'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'text',
                      type: 'string',
                      description: 'The text content of the message part.',
                    },
                  ],
                },
                {
                  type: 'ToolCallPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'tool-call'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'toolCallId',
                      type: 'string',
                      description: 'The id of the tool call.',
                    },
                    {
                      name: 'toolName',
                      type: 'string',
                      description:
                        'The name of the tool, which typically would be the name of the function.',
                    },
                    {
                      name: 'args',
                      type: 'object based on zod schema',
                      description:
                        'Parameters generated by the model to be used by the tool.',
                    },
                  ],
                },
              ],
            },
          ],
        },
        {
          type: 'CoreToolMessage',
          parameters: [
            {
              name: 'role',
              type: "'tool'",
              description: 'The role for the assistant message.',
            },
            {
              name: 'content',
              type: 'Array<ToolResultPart>',
              description: 'The content of the message.',
              properties: [
                {
                  type: 'ToolResultPart',
                  parameters: [
                    {
                      name: 'type',
                      type: "'tool-result'",
                      description: 'The type of the message part.',
                    },
                    {
                      name: 'toolCallId',
                      type: 'string',
                      description:
                        'The id of the tool call the result corresponds to.',
                    },
                    {
                      name: 'toolName',
                      type: 'string',
                      description:
                        'The name of the tool the result corresponds to.',
                    },
                    {
                      name: 'result',
                      type: 'unknown',
                      description:
                        'The result returned by the tool after execution.',
                    },
                    {
                      name: 'isError',
                      type: 'boolean',
                      isOptional: true,
                      description:
                        'Whether the result is an error or an error message.',
                    },
                  ],
                },
              ],
            },
          ],
        },
      ],
    },
    {
      name: 'maxTokens',
      type: 'number',
      isOptional: true,
      description: 'Maximum number of tokens to generate.',
    },
    {
      name: 'temperature',
      type: 'number',
      isOptional: true,
      description:
        'Temperature setting. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.',
    },
    {
      name: 'topP',
      type: 'number',
      isOptional: true,
      description:
        'Nucleus sampling. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.',
    },
    {
      name: 'topK',
      type: 'number',
      isOptional: true,
      description:
        'Only sample from the top K options for each subsequent token. Used to remove "long tail" low probability responses. Recommended for advanced use cases only. You usually only need to use temperature.',
    },
    {
      name: 'presencePenalty',
      type: 'number',
      isOptional: true,
      description:
        'Presence penalty setting. It affects the likelihood of the model to repeat information that is already in the prompt. The value is passed through to the provider. The range depends on the provider and model.',
    },
    {
      name: 'frequencyPenalty',
      type: 'number',
      isOptional: true,
      description:
        'Frequency penalty setting. It affects the likelihood of the model to repeatedly use the same words or phrases. The value is passed through to the provider. The range depends on the provider and model.',
    },
    {
      name: 'stopSequences',
      type: 'string[]',
      isOptional: true,
      description:
        'Sequences that will stop the generation of the text. If the model generates any of these sequences, it will stop generating further text.',
    },
    {
      name: 'seed',
      type: 'number',
      isOptional: true,
      description:
        'The seed (integer) to use for random sampling. If set and supported by the model, calls will generate deterministic results.',
    },
    {
      name: 'maxRetries',
      type: 'number',
      isOptional: true,
      description:
        'Maximum number of retries. Set to 0 to disable retries. Default: 2.',
    },
    {
      name: 'abortSignal',
      type: 'AbortSignal',
      isOptional: true,
      description:
        'An optional abort signal that can be used to cancel the call.',
    },
    {
      name: 'headers',
      type: 'Record<string, string>',
      isOptional: true,
      description:
        'Additional HTTP headers to be sent with the request. Only applicable for HTTP-based providers.',
    },
    {
      name: 'tools',
      type: 'Record<string, Tool>',
      description:
        'Tools that are accessible to and can be called by the model.',
      properties: [
        {
          type: 'Tool',
          parameters: [
            {
              name: 'description',
              isOptional: true,
              type: 'string',
              description:
                'Information about the purpose of the tool including details on how and when it can be used by the model.',
            },
            {
              name: 'parameters',
              type: 'zod schema',
              description:
                'The typed schema that describes the parameters of the tool that can also be used to validation and error handling.',
            },
            {
              name: 'generate',
              isOptional: true,
              type: '(async (parameters) => ReactNode) | AsyncGenerator<ReactNode, ReactNode, void>',
              description:
                'A function or a generator function that is called with the arguments from the tool call and yields React nodes as the UI.',
            },
          ],
        },
      ],
    },
    {
      name: 'toolChoice',
      isOptional: true,
      type: '"auto" | "none" | "required" | { "type": "tool", "toolName": string }',
      description:
        'The tool choice setting. It specifies how tools are selected for execution. The default is "auto". "none" disables tool execution. "required" requires tools to be executed. { "type": "tool", "toolName": string } specifies a specific tool to execute.',
    },
    {
      name: 'text',
      isOptional: true,
      type: '(Text) => ReactNode',
      description: 'Callback to handle the generated tokens from the model.',
      properties: [
        {
          type: 'Text',
          parameters: [
            {
              name: 'content',
              type: 'string',
              description: 'The full content of the completion.',
            },
            { name: 'delta', type: 'string', description: 'The delta.' },
            { name: 'done', type: 'boolean', description: 'Is it done?' },
          ],
        },
      ],
    },
    {
      name: 'onFinish',
      type: '(result: OnFinishResult) => void',
      isOptional: true,
      description:
        'Callback that is called when the LLM response and all request tool executions (for tools that have a `generate` function) are finished.',
      properties: [
        {
          type: 'OnFinishResult',
          parameters: [
            {
              name: 'usage',
              type: 'TokenUsage',
              description: 'The token usage of the generated text.',
              properties: [
                {
                  type: 'TokenUsage',
                  parameters: [
                    {
                      name: 'promptTokens',
                      type: 'number',
                      description: 'The total number of tokens in the prompt.',
                    },
                    {
                      name: 'completionTokens',
                      type: 'number',
                      description:
                        'The total number of tokens in the completion.',
                    },
                    {
                      name: 'totalTokens',
                      type: 'number',
                      description: 'The total number of tokens generated.',
                    },
                  ],
                },
              ],
            },
            {
              name: 'value',
              type: 'ReactNode',
              description: 'The final ui node that was generated.',
            },
            {
              name: 'warnings',
              type: 'Warning[] | undefined',
              description:
                'Warnings from the model provider (e.g. unsupported settings).',
            },
            {
              name: 'rawResponse',
              type: 'RawResponse',
              description: 'Optional raw response data.',
              properties: [
                {
                  type: 'RawResponse',
                  parameters: [
                    {
                      name: 'headers',
                      optional: true,
                      type: 'Record<string, string>',
                      description: 'Response headers.',
                    },
                  ],
                },
              ],
            },
          ],
        },
      ],
    },
  ]}
/>

#### Returns

<PropertiesTable
  content={[
    {
      name: 'value',
      type: 'ReactNode',
      description: 'The user interface based on the stream output.',
    },
    {
      name: 'text',
      type: 'Promise<string>',
      description:
        'The full text that has been generated. Resolved when the response is finished.',
    },
    {
      name: 'toolCalls',
      type: 'Promise<ToolCall[]>',
      description:
        'The tool calls that have been executed. Resolved when the response is finished.',
    },
    {
      name: 'toolResults',
      type: 'Promise<ToolResult[]>',
      description:
        'The tool results that have been generated. Resolved when the all tool executions are finished.',
    },
    {
      name: 'finishReason',
      type: "Promise<'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown'>",
      description:
        'The reason why the generation finished. Resolved when the response is finished.',
    },
    {
      name: 'usage',
      type: 'Promise<TokenUsage>',
      description:
        'The token usage of the generated text. Resolved when the response is finished.',
      properties: [
        {
          type: 'TokenUsage',
          parameters: [
            {
              name: 'promptTokens',
              type: 'number',
              description: 'The total number of tokens in the prompt.',
            },
            {
              name: 'completionTokens',
              type: 'number',
              description: 'The total number of tokens in the completion.',
            },
            {
              name: 'totalTokens',
              type: 'number',
              description: 'The total number of tokens generated.',
            },
          ],
        },
      ],
    },
    {
      name: 'rawResponse',
      type: 'RawResponse',
      optional: true,
      description: 'Optional raw response data.',
      properties: [
        {
          type: 'RawResponse',
          parameters: [
            {
              name: 'headers',
              optional: true,
              type: 'Record<string, string>',
              description: 'Response headers.',
            },
          ],
        },
      ],
    },
    {
      name: 'warnings',
      type: 'Warning[] | undefined',
      description:
        'Warnings from the model provider (e.g. unsupported settings).',
    },
    {
      name: 'textStream',
      type: 'AsyncIterable<string> & ReadableStream<string>',
      description:
        'A text stream that returns only the generated text deltas. You can use it as either an AsyncIterable or a ReadableStream. When an error occurs, the stream will throw the error.',
    },
    {
      name: 'fullStream',
      type: 'AsyncIterable<TextStreamPart> & ReadableStream<TextStreamPart>',
      description:
        'A stream with all events, including text deltas, tool calls, tool results, and errors. You can use it as either an AsyncIterable or a ReadableStream. When an error occurs, the stream will throw the error.',
      properties: [
        {
          type: 'TextStreamPart',
          parameters: [
            {
              name: 'type',
              type: "'text-delta'",
              description: 'The type to identify the object as text delta.',
            },
            {
              name: 'textDelta',
              type: 'string',
              description: 'The text delta.',
            },
          ],
        },
        {
          type: 'TextStreamPart',
          parameters: [
            {
              name: 'type',
              type: "'tool-call'",
              description: 'The type to identify the object as tool call.',
            },
            {
              name: 'toolCallId',
              type: 'string',
              description: 'The id of the tool call.',
            },
            {
              name: 'toolName',
              type: 'string',
              description:
                'The name of the tool, which typically would be the name of the function.',
            },
            {
              name: 'args',
              type: 'object based on zod schema',
              description:
                'Parameters generated by the model to be used by the tool.',
            },
          ],
        },
        {
          type: 'TextStreamPart',
          description: 'The result of a tool call execution.',
          parameters: [
            {
              name: 'type',
              type: "'tool-result'",
              description: 'The type to identify the object as tool result.',
            },
            {
              name: 'toolCallId',
              type: 'string',
              description: 'The id of the tool call.',
            },
            {
              name: 'toolName',
              type: 'string',
              description:
                'The name of the tool, which typically would be the name of the function.',
            },
            {
              name: 'args',
              type: 'object based on zod schema',
              description:
                'Parameters generated by the model to be used by the tool.',
            },
            {
              name: 'result',
              type: 'any',
              description:
                'The result returned by the tool after execution has completed.',
            },
          ],
        },
        {
          type: 'TextStreamPart',
          parameters: [
            {
              name: 'type',
              type: "'error'",
              description: 'The type to identify the object as error.',
            },
            {
              name: 'error',
              type: 'Error',
              description:
                'Describes the error that may have occurred during execution.',
            },
          ],
        },
        {
          type: 'TextStreamPart',
          parameters: [
            {
              name: 'type',
              type: "'finish'",
              description: 'The type to identify the object as finish.',
            },
            {
              name: 'finishReason',
              type: "'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown'",
              description: 'The reason the model finished generating the text.',
            },
            {
              name: 'usage',
              type: 'TokenUsage',
              description: 'The token usage of the generated text.',
              properties: [
                {
                  type: 'TokenUsage',
                  parameters: [
                    {
                      name: 'promptTokens',
                      type: 'number',
                      description: 'The total number of tokens in the prompt.',
                    },
                    {
                      name: 'completionTokens',
                      type: 'number',
                      description:
                        'The total number of tokens in the completion.',
                    },
                    {
                      name: 'totalTokens',
                      type: 'number',
                      description: 'The total number of tokens generated.',
                    },
                  ],
                },
              ],
            },
          ],
        },
      ],
    },
    {
      name: 'toDataStream',
      type: '(callbacks?: AIStreamCallbacksAndOptions) => data stream',
      description: 'Converts the result to a data stream.',
    },
    {
      name: 'pipeDataStreamToResponse',
      type: '(response: ServerResponse, init?: { headers?: Record<string, string>; status?: number } => void',
      description:
        'Writes stream data output to a Node.js response-like object. It sets a `Content-Type` header to `text/plain; charset=utf-8` and writes each stream data part as a separate chunk.',
    },
    {
      name: 'pipeTextStreamToResponse',
      type: '(response: ServerResponse, init?: { headers?: Record<string, string>; status?: number } => void',
      description:
        'Writes text delta output to a Node.js response-like object. It sets a `Content-Type` header to `text/plain; charset=utf-8` and writes each text delta as a separate chunk.',
    },
    {
      name: 'toDataStreamResponse',
      type: '(init?: ResponseInit) => Response',
      description:
        'Converts the result to a streamed response object with a stream data part stream. It can be used with the `useChat` and `useCompletion` hooks.',
    },
    {
      name: 'toTextStreamResponse',
      type: '(init?: ResponseInit) => Response',
      description:
        'Creates a simple text stream response. Each text delta is encoded as UTF-8 and sent as a separate chunk. Non-text-delta events are ignored.',
    },
  ]}
/>

### `createAI`

Creates a client-server context provider that can be used to wrap parts of your application tree to easily manage both UI and AI states of your application.

#### Import

<Snippet text={`import { createAI } from "ai/rsc"`} prompt={false} />

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'actions',
      type: 'Record<string, Action>',
      description: 'Server side actions that can be called from the client.',
    },
    {
      name: 'initialAIState',
      type: 'any',
      description: 'Initial AI state to be used in the client.',
    },
    {
      name: 'initialUIState',
      type: 'any',
      description: 'Initial UI state to be used in the client.',
    },
    {
      name: 'onGetUIState',
      type: '() => UIState',
      description: 'is called during SSR to compare and update UI state.',
    },
    {
      name: 'onSetAIState',
      type: '(Event) => void',
      description:
        'is triggered whenever an update() or done() is called by the mutable AI state in your action, so you can safely store your AI state in the database.',
      properties: [
        {
          type: 'Event',
          parameters: [
            {
              name: 'state',
              type: 'AIState',
              description: 'The resulting AI state after the update.',
            },
            {
              name: 'done',
              type: 'boolean',
              description:
                'Whether the AI state updates have been finalized or not.',
            },
          ],
        },
      ],
    },
  ]}
/>

##### Returns

It returns an `<AI/>` context provider.

### `createStreamableUI`

Create a stream that sends UI from the server to the client. On the client side, it can be rendered as a normal React node.

#### Import

<Snippet text={`import { createStreamableUI } from "ai/rsc"`} prompt={false} />

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'initialValue',
      type: 'ReactNode',
      isOptional: true,
      description: 'The initial value of the streamable UI.',
    },
  ]}
/>

##### Returns

<PropertiesTable
  content={[
    {
      name: 'value',
      type: 'ReactNode',
      description:
        'The value of the streamable UI. This can be returned from a Server Action and received by the client.',
    },
  ]}
/>

##### Methods

<PropertiesTable
  content={[
    {
      name: 'update',
      type: '(ReactNode) => void',
      description:
        'Updates the current UI node. It takes a new UI node and replaces the old one.',
    },
    {
      name: 'append',
      type: '(ReactNode) => void',
      description:
        'Appends a new UI node to the end of the old one. Once appended a new UI node, the previous UI node cannot be updated anymore.',
    },
    {
      name: 'done',
      type: '(ReactNode | null) => void',
      description:
        'Marks the UI node as finalized and closes the stream. Once called, the UI node cannot be updated or appended anymore. This method is always required to be called, otherwise the response will be stuck in a loading state.',
    },
    {
      name: 'error',
      type: '(Error) => void',
      description:
        'Signals that there is an error in the UI stream. It will be thrown on the client side and caught by the nearest error boundary component.',
    },
  ]}
/>

### `createStreamableValue`

Create a stream that sends values from the server to the client. The value can be any serializable data.

#### Import

<Snippet
  text={`import { createStreamableValue } from "ai/rsc"`}
  prompt={false}
/>

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'value',
      type: 'any',
      description: 'Any data that RSC supports. Example, JSON.',
    },
  ]}
/>

##### Returns

<PropertiesTable
  content={[
    {
      name: 'value',
      type: 'streamable',
      description:
        'This creates a special value that can be returned from Actions to the client. It holds the data inside and can be updated via the update method.',
    },
  ]}
/>

### `readStreamableValue`

It is a function that helps you read the streamable value from the client that was originally created using [`createStreamableValue`](/docs/reference/ai-sdk-rsc/create-streamable-value) on the server.

#### Import

<Snippet text={`import { readStreamableValue } from "ai/rsc"`} prompt={false} />

#### Example

```ts filename="app/actions.ts"
async function generate() {
  'use server';
  const streamable = createStreamableValue();

  streamable.update(1);
  streamable.update(2);
  streamable.done(3);

  return streamable.value;
}
```

```tsx filename="app/page.tsx" highlight="12"
import { readStreamableValue } from 'ai/rsc';

export default function Page() {
  const [generation, setGeneration] = useState('');

  return (
    <div>
      <button
        onClick={async () => {
          const stream = await generate();

          for await (const delta of readStreamableValue(stream)) {
            setGeneration(generation => generation + delta);
          }
        }}
      >
        Generate
      </button>
    </div>
  );
}
```

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'stream',
      type: 'StreamableValue',
      description: 'The streamable value to read from.',
    },
  ]}
/>

##### Returns

It returns an async iterator that contains the values emitted by the streamable value.


### `getAIState`

Get the current AI state.

#### Import

<Snippet text={`import { getAIState } from "ai/rsc"`} prompt={false} />

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'key',
      type: 'string',
      isOptional: true,
      description:
        "Returns the value of the specified key in the AI state, if it's an object.",
    },
  ]}
/>

##### Returns

The AI state.

### `getMutableAIState`

Get a mutable copy of the AI state. You can use this to update the state in the server.

#### Import

<Snippet text={`import { getMutableAIState } from "ai/rsc"`} prompt={false} />

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'key',
      isOptional: true,
      type: 'string',
      description:
        "Returns the value of the specified key in the AI state, if it's an object.",
    },
  ]}
/>

##### Returns

The mutable AI state.

##### Methods

<PropertiesTable
  content={[
    {
      name: 'update',
      type: '(newState: any) => void',
      description: 'Updates the AI state with the new state.',
    },
    {
      name: 'done',
      type: '(newState: any) => void',
      description:
        'Updates the AI state with the new state, marks it as finalized and closes the stream.',
    },
  ]}
/>

### `useAIState`

It is a hook that enables you to read and update the AI state. The AI state is shared globally between all `useAIState` hooks under the same `<AI/>` provider.

The AI state is intended to contain context and information shared with the AI model, such as system messages, function responses, and other relevant data.

#### Import

<Snippet text={`import { useAIState } from "ai/rsc"`} prompt={false} />

#### API Signature

##### Returns

A single element array where the first element is the current AI state.


### `useActions`

It is a hook to help you access your Server Actions from the client. This is particularly useful for building interfaces that require user interactions with the server.

It is required to access these server actions via this hook because they are patched when passed through the context. Accessing them directly may result in a [Cannot find Client Component error](/docs/troubleshooting/common-issues/server-actions-in-client-components).

#### Import

<Snippet text={`import { useActions } from "ai/rsc"`} prompt={false} />

#### API Signature

##### Returns

`Record<string, Action>`, a dictionary of server actions.

### `useUIState`

It is a hook that enables you to read and update the UI State. The state is client-side and can contain functions, React nodes, and other data. UIState is the visual representation of the AI state.

#### Import

<Snippet text={`import { useUIState } from "ai/rsc"`} prompt={false} />

#### API Signature

##### Returns

Similar to useState, it is an array, where the first element is the current UI state and the second element is the function that updates the state.

### `useStreamableValue`

It is a React hook that takes a streamable value created using [`createStreamableValue`](/docs/reference/ai-sdk-rsc/create-streamable-value) and returns the current value, error, and pending state.

#### Import

<Snippet text={`import { useStreamableValue } from "ai/rsc"`} prompt={false} />

#### Example

This is useful for consuming streamable values received from a component's props.

```tsx
function MyComponent({ streamableValue }) {
  const [data, error, pending] = useStreamableValue(streamableValue);

  if (pending) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return <div>Data: {data}</div>;
}
```

#### API Signature

##### Parameters

It accepts a streamable value created using `createStreamableValue`.

##### Returns

It is an array, where the first element contains the data, the second element contains an error if it is thrown anytime during the stream, and the third is a boolean indicating if the value is pending.

## API Reference For AI SDK UI

### `useChat()`

Allows you to easily create a conversational user interface for your chatbot application. It enables the streaming of chat messages from your AI provider, manages the state for chat input, and updates the UI automatically as new messages are received.

#### Import

<Tabs items={['React', 'Svelte', 'Vue', 'Solid']}>
  <Tab>
    <Snippet text="import { useChat } from 'ai/react'" dark prompt={false} />
  </Tab>
  <Tab>
    <Snippet
      text="import { useChat } from '@ai-sdk/svelte'"
      dark
      prompt={false}
    />
  </Tab>
  <Tab>
    <Snippet text="import { useChat } from '@ai-sdk/vue'" dark prompt={false} />
  </Tab>
  <Tab>
    <Snippet
      text="import { useChat } from '@ai-sdk/solid'"
      dark
      prompt={false}
    />
  </Tab>
</Tabs>

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'api',
      type: "string = '/api/chat'",
      description: 'The chat completion API endpoint offered by the provider.',
    },
    {
      name: 'keepLastMessageOnError',
      type: 'boolean',
      description:
        'Keeps the last message when an error happens. This will be the default behavior starting with the next major release. The flag was introduced for backwards compatibility and currently defaults to `false`. Please enable it and update your error handling/resubmit behavior.',
    },
    {
      name: 'id',
      type: 'string',
      description:
        'An unique identifier for the chat. If not provided, a random one will be generated. When provided, the `useChat` hook with the same `id` will have shared states across components. This is useful when you have multiple components showing the same chat stream.',
    },
    {
      name: 'initialInput',
      type: "string = ''",
      description: 'An optional string for the initial prompt input.',
    },
    {
      name: 'initialMessages',
      type: 'Messages[] = []',
      description: 'An optional array of initial chat messages',
    },
    {
      name: 'onToolCall',
      type: '({toolCall: ToolCall}) => void | unknown| Promise<unknown>',
      description:
        'Optional callback function that is invoked when a tool call is received. Intended for automatic client-side tool execution. You can optionally return a result for the tool call, either synchronously or asynchronously.',
    },
    {
      name: 'onResponse',
      type: '(response: Response) => void',
      description:
        'An optional callback that will be called with the response from the API endpoint. Useful for throwing customized errors or logging',
    },
    {
      name: 'onFinish',
      type: '(message: Message, options: OnFinishOptions) => void',
      description:
        'An optional callback function that is called when the completion stream ends.',
      properties: [
        {
          type: 'OnFinishOptions',
          parameters: [
            {
              name: 'usage',
              type: 'CompletionTokenUsage',
              description: 'The token usage for the completion.',
              properties: [
                {
                  type: 'CompletionTokenUsage',
                  parameters: [
                    {
                      name: 'promptTokens',
                      type: 'number',
                      description: 'The total number of tokens in the prompt.',
                    },
                    {
                      name: 'completionTokens',
                      type: 'number',
                      description:
                        'The total number of tokens in the completion.',
                    },
                    {
                      name: 'totalTokens',
                      type: 'number',
                      description: 'The total number of tokens generated.',
                    },
                  ],
                },
              ],
            },
            {
              name: 'finishReason',
              type: "'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown'",
              description: 'The reason why the generation ended.',
            },
          ],
        },
      ],
    },
    {
      name: 'onError',
      type: '(error: Error) => void',
      description:
        'An optional callback that will be called when the chat stream encounters an error.',
    },
    {
      name: 'generateId',
      type: '() => string',
      description:
        'Optional. A way to provide a function that is going to be used for ids for messages. If not provided generateId is used by default.',
    },
    {
      name: 'headers',
      type: 'Record<string, string> | Headers',
      description:
        'An optional object of headers to be passed to the API endpoint.',
    },
    {
      name: 'body',
      type: 'any',
      description:
        'An optional, additional body object to be passed to the API endpoint.',
    },
    {
      name: 'credentials',
      type: "'omit' | 'same-origin' | 'include'",
      description:
        'An optional literal that sets the mode of credentials to be used on the request. Defaults to same-origin.',
    },
    {
      name: 'sendExtraMessageFields',
      type: 'boolean',
      description:
        "An optional boolean that determines whether to send extra fields you've added to `messages`. Defaults to `false` and only the `content` and `role` fields will be sent to the API endpoint. If set to `true`, the `name`, `data`, and `annotations` fields will also be sent.",
    },
    {
      name: 'maxToolRoundtrips',
      type: 'number',
      description:
        'React and SolidJS only. Maximal number of automatic roundtrips for tool calls. An automatic tool call roundtrip is a call to the server with the  tool call results when all tool calls in the last assistant  message have results. A maximum number is required to prevent infinite loops in the case of misconfigured tools. By default, it is set to 0, which will disable the feature.',
    },
    {
      name: 'streamProtocol',
      type: "'text' | 'data'",
      optional: true,
      description:
        'An optional literal that sets the type of stream to be used. Defaults to `data`. If set to `text`, the stream will be treated as a text stream.',
    },
    {
      name: 'fetch',
      type: 'FetchFunction',
      optional: true,
      description:
        'Optional. A custom fetch function to be used for the API call. Defaults to the global fetch function.',
    },
    {
      name: 'experimental_prepareRequestBody',
      type: '(options: { messages: Message[]; requestData?: JSONValue; requestBody?: object }) => JSONValue',
      optional: true,
      description:
        'Experimental (React only). When a function is provided, it will be used to prepare the request body for the chat API. This can be useful for customizing the request body based on the messages and data in the chat.',
    },
  ]}
/>

##### Returns

<PropertiesTable
  content={[
    {
      name: 'messages',
      type: 'Message[]',
      description: 'The current array of chat messages.',
      properties: [
        {
          type: 'Message',
          parameters: [
            {
              name: 'id',
              type: 'string',
              description: 'The unique identifier of the message.',
            },
            {
              name: 'role',
              type: "'system' | 'user' | 'assistant' | 'data'",
              description: 'The role of the message.',
            },
            {
              name: 'content',
              type: 'string',
              description: 'The content of the message.',
            },
            {
              name: 'createdAt',
              type: 'Date',
              isOptional: true,
              description: 'The creation date of the message.',
            },
            {
              name: 'name',
              type: 'string',
              isOptional: true,
              description: 'The name of the message.',
            },
            {
              name: 'data',
              type: 'JSONValue',
              isOptional: true,
              description: 'Additional data sent along with the message.',
            },
            {
              name: 'annotations',
              type: 'Array<JSONValue>',
              isOptional: true,
              description:
                'Additional annotations sent along with the message.',
            },
            {
              name: 'toolInvocations',
              type: 'Array<ToolInvocation>',
              isOptional: true,
              description:
                'An array of tool invocations that are associated with the (assistant) message.',
              properties: [
                {
                  type: 'ToolInvocation',
                  parameters: [
                    {
                      name: 'state',
                      type: "'partial-call'",
                      description:
                        'The state of the tool call when it was partially created.',
                    },
                    {
                      name: 'toolCallId',
                      type: 'string',
                      description:
                        'ID of the tool call. This ID is used to match the tool call with the tool result.',
                    },
                    {
                      name: 'toolName',
                      type: 'string',
                      description: 'Name of the tool that is being called.',
                    },
                    {
                      name: 'args',
                      type: 'any',
                      description:
                        'Partial arguments of the tool call. This is a JSON-serializable object.',
                    },
                  ],
                },
                {
                  type: 'ToolInvocation',
                  parameters: [
                    {
                      name: 'state',
                      type: "'call'",
                      description:
                        'The state of the tool call when it was fully created.',
                    },
                    {
                      name: 'toolCallId',
                      type: 'string',
                      description:
                        'ID of the tool call. This ID is used to match the tool call with the tool result.',
                    },
                    {
                      name: 'toolName',
                      type: 'string',
                      description: 'Name of the tool that is being called.',
                    },
                    {
                      name: 'args',
                      type: 'any',
                      description:
                        'Arguments of the tool call. This is a JSON-serializable object that matches the tools input schema.',
                    },
                  ],
                },
                {
                  type: 'ToolInvocation',
                  parameters: [
                    {
                      name: 'state',
                      type: "'result'",
                      description:
                        'The state of the tool call when the result is available.',
                    },
                    {
                      name: 'toolCallId',
                      type: 'string',
                      description:
                        'ID of the tool call. This ID is used to match the tool call with the tool result.',
                    },
                    {
                      name: 'toolName',
                      type: 'string',
                      description: 'Name of the tool that is being called.',
                    },
                    {
                      name: 'args',
                      type: 'any',
                      description:
                        'Arguments of the tool call. This is a JSON-serializable object that matches the tools input schema.',
                    },
                    {
                      name: 'result',
                      type: 'any',
                      description: 'The result of the tool call.',
                    },
                  ],
                },
              ],
            },
            {
              name: 'experimental_attachments',
              type: 'Array<Attachment>',
              isOptional: true,
              description:
                'Additional attachments sent along with the message.',
              properties: [
                {
                  type: 'Attachment',
                  description:
                    'An attachment object that can be used to describe the metadata of the file.',
                  parameters: [
                    {
                      name: 'name',
                      type: 'string',
                      isOptional: true,
                      description:
                        'The name of the attachment, usually the file name.',
                    },
                    {
                      name: 'contentType',
                      type: 'string',
                      isOptional: true,
                      description:
                        'A string indicating the media type of the file.',
                    },
                    {
                      name: 'url',
                      type: 'string',
                      description:
                        'The URL of the attachment. It can either be a URL to a hosted file or a Data URL.',
                    },
                  ],
                },
              ],
            },
          ],
        },
      ],
    },
    {
      name: 'error',
      type: 'Error | undefined',
      description: 'An error object returned by SWR, if any.',
    },
    {
      name: 'append',
      type: '(message: Message | CreateMessage, options?: ChatRequestOptions) => Promise<string | undefined>',
      description:
        'Function to append a message to the chat, triggering an API call for the AI response. It returns a promise that resolves to full response message content when the API call is successfully finished, or throws an error when the API call fails.',
      properties: [
        {
          type: 'ChatRequestOptions',
          parameters: [
            {
              name: 'headers',
              type: 'Record<string, string> | Headers',
              description:
                'Additional headers that should be to be passed to the API endpoint.',
            },
            {
              name: 'body',
              type: 'object',
              description:
                'Additional body JSON properties that should be sent to the API endpoint.',
            },
            {
              name: 'data',
              type: 'JSONValue',
              description: 'Additional data to be sent to the API endpoint.',
            },
          ],
        },
      ],
    },
    {
      name: 'reload',
      type: '() => Promise<string | undefined>',
      description:
        "Function to reload the last AI chat response for the given chat history. If the last message isn't from the assistant, it will request the API to generate a new response.",
    },
    {
      name: 'stop',
      type: '() => void',
      description: 'Function to abort the current API request.',
    },
    {
      name: 'setMessages',
      type: '(messages: Message[] | ((messages: Message[]) => Message[]) => void',
      description:
        'Function to update the `messages` state locally without triggering an API call.',
    },
    {
      name: 'input',
      type: 'string',
      description: 'The current value of the input field.',
    },
    {
      name: 'setInput',
      type: 'React.Dispatch<React.SetStateAction<string>>',
      description: 'Function to update the `input` value.',
    },
    {
      name: 'handleInputChange',
      type: '(event: any) => void',
      description:
        "Handler for the `onChange` event of the input field to control the input's value.",
    },
    {
      name: 'handleSubmit',
      type: '(event?: { preventDefault?: () => void }, options?: ChatRequestOptions) => void',
      description:
        'Form submission handler that automatically resets the input field and appends a user message. You can use the `options` parameter to send additional data, headers and more to the server.',
      properties: [
        {
          type: 'ChatRequestOptions',
          parameters: [
            {
              name: 'headers',
              type: 'Record<string, string> | Headers',
              description:
                'Additional headers that should be to be passed to the API endpoint.',
            },
            {
              name: 'body',
              type: 'object',
              description:
                'Additional body JSON properties that should be sent to the API endpoint.',
            },
            {
              name: 'data',
              type: 'JSONValue',
              description: 'Additional data to be sent to the API endpoint.',
            },
            {
              name: 'allowEmptySubmit',
              type: 'boolean',
              isOptional: true,
              description:
                'A boolean that determines whether to allow submitting an empty input that triggers a generation. Defaults to `false`.',
            },
            {
              name: 'experimental_attachments',
              type: 'FileList | Array<Attachment>',
              isOptional: true,
              description:
                'An array of attachments to be sent to the API endpoint.',
              properties: [
                {
                  type: 'FileList',
                  parameters: [
                    {
                      name: '',
                      type: '',
                      description:
                        "A list of files that have been selected by the user using an <input type='file'> element. It's also used for a list of files dropped into web content when using the drag and drop API.",
                    },
                  ],
                },
                {
                  type: 'Attachment',
                  description:
                    'An attachment object that can be used to describe the metadata of the file.',
                  parameters: [
                    {
                      name: 'name',
                      type: 'string',
                      isOptional: true,
                      description:
                        'The name of the attachment, usually the file name.',
                    },
                    {
                      name: 'contentType',
                      type: 'string',
                      isOptional: true,
                      description:
                        'A string indicating the media type of the file.',
                    },
                    {
                      name: 'url',
                      type: 'string',
                      description:
                        'The URL of the attachment. It can either be a URL to a hosted file or a Data URL.',
                    },
                  ],
                },
              ],
            },
          ],
        },
      ],
    },
    {
      name: 'isLoading',
      type: 'boolean',
      description:
        'Boolean flag indicating whether a request is currently in progress.',
    },
    {
      name: 'data',
      type: 'JSONValue[]',
      description: 'Data returned from StreamData',
    },
    {
      name: 'addToolResult',
      type: '({toolCallId: string; result: any;}) => void',
      description:
        'React and SolidJS only. Function to add a tool result to the chat. This will update the chat messages with the tool result and call the API route if all tool results for the last message are available.',
    },
  ]}
/>


### `useCompletion()`

Allows you to create text completion based capabilities for your application. It enables the streaming of text completions from your AI provider, manages the state for chat input, and updates the UI automatically as new messages are received.

#### Import

<Tabs items={['React', 'Svelte', 'Vue', 'Solid']}>
  <Tab>
    <Snippet
      text="import { useCompletion } from 'ai/react'"
      dark
      prompt={false}
    />
  </Tab>
  <Tab>
    <Snippet
      text="import { useCompletion } from '@ai-sdk/svelte'"
      dark
      prompt={false}
    />
  </Tab>
  <Tab>
    <Snippet
      text="import { useCompletion } from '@ai-sdk/vue'"
      dark
      prompt={false}
    />
  </Tab>
  <Tab>
    <Snippet
      text="import { useCompletion } from '@ai-sdk/solid'"
      dark
      prompt={false}
    />
  </Tab>
</Tabs>

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'api',
      type: "string = '/api/completion'",
      description: 'The text completion API endpoint offered by the provider.',
    },
    {
      name: 'id',
      type: 'string',
      description:
        'An unique identifier for the completion. If not provided, a random one will be generated. When provided, the `useCompletion` hook with the same `id` will have shared states across components. This is useful when you have multiple components showing the same chat stream',
    },
    {
      name: 'initialInput',
      type: 'string',
      description: 'An optional string for the initial prompt input.',
    },
    {
      name: 'initialCompletion',
      type: 'string',
      description: 'An optional string for the initial completion result.',
    },
    {
      name: 'onResponse',
      type: '(response: Response) => void',
      description:
        'An optional callback function that is called with the response from the API endpoint. Useful for throwing customized errors or logging.',
    },
    {
      name: 'onFinish',
      type: '(prompt: string, completion: string) => void',
      description:
        'An optional callback function that is called when the completion stream ends.',
    },
    {
      name: 'onError',
      type: '(error: Error) => void',
      description:
        'An optional callback that will be called when the chat stream encounters an error.',
    },
    {
      name: 'headers',
      type: 'Record<string, string> | Headers',
      description:
        'An optional object of headers to be passed to the API endpoint.',
    },
    {
      name: 'body',
      type: 'any',
      description:
        'An optional, additional body object to be passed to the API endpoint.',
    },
    {
      name: 'credentials',
      type: "'omit' | 'same-origin' | 'include'",
      description:
        'An optional literal that sets the mode of credentials to be used on the request. Defaults to same-origin.',
    },
    {
      name: 'sendExtraMessageFields',
      type: 'boolean',
      description:
        "An optional boolean that determines whether to send extra fields you've added to `messages`. Defaults to `false` and only the `content` and `role` fields will be sent to the API endpoint.",
    },
    {
      name: 'streamProtocol',
      type: "'text' | 'data'",
      optional: true,
      description:
        'An optional literal that sets the type of stream to be used. Defaults to `data`. If set to `text`, the stream will be treated as a text stream.',
    },
    {
      name: 'fetch',
      type: 'FetchFunction',
      optional: true,
      description:
        'Optional. A custom fetch function to be used for the API call. Defaults to the global fetch function.',
    },
  ]}
/>

##### Returns

<PropertiesTable
  content={[
    {
      name: 'completion',
      type: 'string',
      description: 'The current text completion.',
    },
    {
      name: 'complete',
      type: '(prompt: string, options: { headers, body }) => void',
      description:
        'Function to execute text completion based on the provided prompt.',
    },
    {
      name: 'error',
      type: 'undefined | Error',
      description: 'The error thrown during the completion process, if any.',
    },
    {
      name: 'setCompletion',
      type: '(completion: string) => void',
      description: 'Function to update the `completion` state.',
    },
    {
      name: 'stop',
      type: '() => void',
      description: 'Function to abort the current API request.',
    },
    {
      name: 'input',
      type: 'string',
      description: 'The current value of the input field.',
    },
    {
      name: 'setInput',
      type: 'React.Dispatch<React.SetStateAction<string>>',
      description: 'The current value of the input field.',
    },
    {
      name: 'handleInputChange',
      type: '(event: any) => void',
      description:
        "Handler for the `onChange` event of the input field to control the input's value.",
    },
    {
      name: 'handleSubmit',
      type: '(event?: { preventDefault?: () => void }) => void',
      description:
        'Form submission handler that automatically resets the input field and appends a user message.',
    },
    {
      name: 'isLoading',
      type: 'boolean',
      description:
        'Boolean flag indicating whether a fetch operation is currently in progress.',
    },
  ]}
/>

### `experimental_useObject()`

<Note>`useObject` is an experimental feature and only available in React.</Note>

Allows you to consume text streams that represent a JSON object and parse them into a complete object based on a schema.
You can use it together with [`streamObject`](/docs/reference/ai-sdk-core/stream-object) in the backend.

```tsx
'use client';

import { experimental_useObject as useObject } from 'ai/react';

export default function Page() {
  const { object, submit } = useObject({
    api: '/api/use-object',
    schema: z.object({ content: z.string() }),
  });

  return (
    <div>
      <button onClick={() => submit('example input')}>Generate</button>
      {object?.content && <p>{object.content}</p>}
    </div>
  );
}
```

#### Import

<Snippet
  text="import { experimental_useObject as useObject } from 'ai/react'"
  dark
  prompt={false}
/>

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'api',
      type: 'string',
      description:
        'The API endpoint. It should stream JSON that matches the schema as chunked text.',
    },
    {
      name: 'schema',
      type: 'Zod Schema | JSON Schema',
      description:
        'A schema that defines the shape of the complete object. You can either pass in a Zod schema or a JSON schema (using the `jsonSchema` function).',
    },
    {
      name: 'id?',
      type: 'string',
      description:
        'A unique identifier. If not provided, a random one will be generated. When provided, the `useObject` hook with the same `id` will have shared states across components.',
    },
    {
      name: 'initialValue?',
      type: 'DeepPartial<RESULT> | undefined',
      description: 'An optional value for the initial object.',
    },
    {
      name: 'fetch',
      type: 'FetchFunction',
      optional: true,
      description:
        'Optional. A custom fetch function to be used for the API call. Defaults to the global fetch function.',
    },
    {
      name: 'onError',
      type: '(error: Error) => void',
      optional: true,
      description:
        'Optional. Callback function to be called when an error is encountered.',
    },
    {
      name: 'onFinish',
      type: '(result: OnFinishResult) => void',
      isOptional: true,
      description: 'Called when the streaming response has finished.',
      properties: [
        {
          type: 'OnFinishResult',
          parameters: [
            {
              name: 'object',
              type: 'T | undefined',
              description:
                'The generated object (typed according to the schema). Can be undefined if the final object does not match the schema.',
            },
            {
              name: 'error',
              type: 'unknown | undefined',
              description:
                'Optional error object. This is e.g. a TypeValidationError when the final object does not match the schema.',
            },
          ],
        },
      ],
    },
  ]}
/>

##### Returns

<PropertiesTable
  content={[
    {
      name: 'submit',
      type: '(input: INPUT) => void',
      description: 'Calls the API with the provided input as JSON body.',
    },
    {
      name: 'object',
      type: 'DeepPartial<RESULT> | undefined',
      description:
        'The current value for the generated object. Updated as the API streams JSON chunks.',
    },
    {
      name: 'error',
      type: 'undefined | unknown',
      description: 'The error object if the API call fails.',
    },
    {
      name: 'isLoading',
      type: 'boolean',
      description:
        'Boolean flag indicating whether a request is currently in progress.',
    },
    {
      name: 'stop',
      type: '() => void',
      description: 'Function to abort the current API request.',
    },
  ]}
/>

### `convertToCoreMessages()`

The `convertToCoreMessages` function is used to transform an array of UI messages from the `useChat` hook into an array of `CoreMessage` objects. These `CoreMessage` objects are compatible with AI core functions like `streamText`.

```ts filename="app/api/chat/route.ts"
import { openai } from '@ai-sdk/openai';
import { convertToCoreMessages, streamText } from 'ai';

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = await streamText({
    model: openai('gpt-4o'),
    messages: convertToCoreMessages(messages),
  });

  return result.toDataStreamResponse();
}
```

#### Import

<Snippet text={`import { convertToCoreMessages } from "ai"`} prompt={false} />

#### API Signature

##### Parameters

<PropertiesTable
  content={[
    {
      name: 'messages',
      type: 'Message[]',
      description:
        'An array of UI messages from the useChat hook to be converted',
    },
  ]}
/>

##### Returns

An array of [`CoreMessage`](/docs/reference/ai-sdk-core/core-message) objects.

<PropertiesTable
  content={[
    {
      name: 'CoreMessage[]',
      type: 'Array',
      description: 'An array of CoreMessage objects',
    },
  ]}
/>
