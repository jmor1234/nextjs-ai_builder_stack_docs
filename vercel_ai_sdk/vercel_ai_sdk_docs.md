# Vercel AI SDK

The Vercel AI SDK is the TypeScript toolkit designed to help developers build AI-powered applications with React, Next.js, Vue, Svelte, Node.js, and more.

## Why use the Vercel AI SDK?

Integrating large language models (LLMs) into applications is complicated and heavily dependent on the specific model provider you use.

The Vercel AI SDK:
- Abstracts away the differences between model providers
- Eliminates boilerplate code for building chatbots
- Allows you to go beyond text output to generate rich, interactive components

## Components

1. **AI SDK Core**: A unified API for generating text, structured objects, and tool calls with LLMs.
2. **AI SDK UI**: A set of framework-agnostic hooks for quickly building chat and generative user interface.
3. **AI SDK RSC**: A library to stream generative user interfaces with React Server Components (RSC).

# Overview

This page is a beginner-friendly introduction to high-level artificial intelligence (AI) concepts. To dive right into implementing the Vercel AI SDK, feel free to skip ahead to our [quickstarts](#) or learn about our supported models and providers.

The Vercel AI SDK standardizes integrating artificial intelligence (AI) models across supported providers. This enables developers to focus on building great AI applications, not waste time on technical details.

For example, here’s how you can generate text with various models using the Vercel AI SDK:

```javascript
import { generateText } from "ai"
import { anthropic } from "@ai-sdk/anthropic"

const { text } = await generateText({
  model: anthropic("claude-3-opus-20240229"),
  prompt: "What is love?"
})

console.log(text)
// Output:
// Love is a complex and multifaceted concept that has been explored and defined in many different ways throughout history and across cultures. At its core, love is often described as a deep, intense feeling of affection, care, and attachment towards another person, object, or idea.

import { generateText } from "ai"
import { openai } from "@ai-sdk/openai"

const { text } = await generateText({
  model: openai("gpt-4-turbo"),
  prompt: "What is love?"
})

console.log(text)
// Output:
// Love is a complex and multifaceted emotion that can be felt and expressed in many different ways. It involves deep affection, care, compassion, and connection towards another person or thing. Love can take on various forms such as romantic love, platonic love, familial love, or self-love.

# Prompts

Prompts are instructions that you give a large language model (LLM) to tell it what to do. It's like when you ask someone for directions; the clearer your question, the better the directions you'll get.

Many LLM providers offer complex interfaces for specifying prompts. They involve different roles and message types. While these interfaces are powerful, they can be hard to use and understand.

In order to simplify prompting across compatible providers, the Vercel AI SDK offers two categories of prompts: text prompts and message prompts.

## Text Prompts

Text prompts are strings. They are ideal for simple generation use cases, e.g. repeatedly generating content for variants of the same prompt text.

You can set text prompts using the `prompt` property made available by AI SDK functions like `generateText` or `streamUI`. You can structure the text in any way and inject variables, e.g. using a template literal.

```javascript
const result = await generateText({
  model: yourModel,
  prompt: 'Invent a new holiday and describe its traditions.',
});
```

You can also use template literals to provide dynamic data to your prompt.

```javascript
const result = await generateText({
  model: yourModel,
  prompt:
    `I am planning a trip to ${destination} for ${lengthOfStay} days. ` +
    `Please suggest the best tourist activities for me to do.`,
});
```

## Message Prompts

A message prompt is an array of user, assistant, and tool messages. They are great for chat interfaces and more complex, multi-modal prompts.

Each message has a `role` and a `content` property. The content can either be text (for user and assistant messages), or an array of relevant parts (data) for that message type.

```javascript
const result = await streamUI({
  model: yourModel,
  messages: [
    { role: 'user', content: 'Hi!' },
    { role: 'assistant', content: 'Hello, how can I help?' },
    { role: 'user', content: 'Where can I buy the best Currywurst in Berlin?' },
  ],
});
```

*Not all language models support all message and content types. For example, some models might not be capable of handling multi-modal inputs or tool messages. Learn more about the capabilities of select models.*

## Multi-modal messages

Multi-modal refers to interacting with a model across different data types such as text, image, or audio data.

Instead of sending a text in the `content` property, you can send an array of parts that include text and other data types. Currently image and text parts are supported.

For models that support multi-modal inputs, user messages can include images. An image can be one of the following:

1. base64-encoded image:
   - string with base-64 encoded content
   - data URL string, e.g. `data:image/png;base64,...`
2. binary image:
   - ArrayBuffer
   - Uint8Array
   - Buffer
3. URL:
   - http(s) URL string, e.g. `https://example.com/image.png`
   - URL object, e.g. `new URL('https://example.com/image.png')`

It is possible to mix text and multiple images.

*Not all models support all types of multi-modal inputs. Check the model's capabilities before using this feature.*

### Example: Binary image (Buffer)

```javascript
const result = await generateText({
  model,
  messages: [
    {
      role: 'user',
      content: [
        { type: 'text', text: 'Describe the image in detail.' },
        {
          type: 'image',
          image: fs.readFileSync('./data/comic-cat.png'),
        },
      ],
    },
  ],
});
```

### Example: Base-64 encoded image (string)

```javascript
const result = await generateText({
  model: yourModel,
  messages: [
    {
      role: 'user',
      content: [
        { type: 'text', text: 'Describe the image in detail.' },
        {
          type: 'image',
          image: fs.readFileSync('./data/comic-cat.png').toString('base64'),
        },
      ],
    },
  ],
});
```

### Example: Image URL (string)

```javascript
const result = await generateText({
  model: yourModel,
  messages: [
    {
      role: 'user',
      content: [
        { type: 'text', text: 'Describe the image in detail.' },
        {
          type: 'image',
          image:
            'https://github.com/vercel/ai/blob/main/examples/ai-core/data/comic-cat.png?raw=true',
        },
      ],
    },
  ],
});
```

## Tool messages

Tools (also known as function calling) are programs that you can provide an LLM to extend its built-in functionality. This can be anything from calling an external API to calling functions within your UI. Learn more about Tools in the next section.

For models that support tool calls, assistant messages can contain tool call parts, and tool messages can contain tool result parts. A single assistant message can call multiple tools, and a single tool message can contain multiple tool results.

```javascript
const result = await generateText({
  model: yourModel,
  messages: [
    {
      role: 'user',
      content: [
        {
          type: 'text',
          text: 'How many calories are in this block of cheese?',
        },
        { type: 'image', image: fs.readFileSync('./data/roquefort.jpg') },
      ],
    },
    {
      role: 'assistant',
      content: [
        {
          type: 'tool-call',
          toolCallId: '12345',
          toolName: 'get-nutrition-data',
          args: { cheese: 'Roquefort' },
        },
        // there could be more tool calls here (parallel calling)
      ],
    },
    {
      role: 'tool',
      content: [
        {
          type: 'tool-result',
          toolCallId: '12345', // needs to match the tool call id
          toolName: 'get-nutrition-data',
          result: {
            name: 'Cheese, roquefort',
            calories: 369,
            fat: 31,
            protein: 22,
          },
        },
        // there could be more tool results here (parallel calling)
      ],
    },
  ],
});
```

## System Messages

System messages are the initial set of instructions given to models that help guide and constrain the models' behaviors and responses. You can set system prompts using the `system` property. System messages work with both the `prompt` and the `messages` properties.

```javascript
const result = await generateText({
  model: yourModel,
  system:
    `You help planning travel itineraries. ` +
    `Respond to the users' request with a list ` +
    `of the best stops to make in their destination.`,
  prompt:
    `I am planning a trip to ${destination} for ${lengthOfStay} days. ` +
    `Please suggest the best tourist activities for me to do.`,
});
```

# Tools

While large language models (LLMs) have incredible generation capabilities, they struggle with discrete tasks (e.g., mathematics) and interacting with the outside world (e.g., getting the weather).

Tools can be thought of as programs you give to a model which can be run as and when the model deems applicable.

## What is a tool?

A tool is an object that can be called by the model to perform a specific task. You can use tools with functions across the AI SDK (like `generateText` or `streamUI`) by passing a tool(s) to the `tools` parameter.

There are three elements of a tool:

1. `description`: An optional description of the tool that can influence when the tool is picked.
2. `parameters`: A Zod schema or a JSON schema that defines the parameters. The schema is consumed by the LLM, and also used to validate the LLM tool calls.
3. `execute` or `generate`: An optional async or generator function that is called with the arguments from the tool call.

## Tool Calls

If the LLM decides to use a tool, it will generate a tool call. Tools with an `execute` or `generate` function are run automatically when these calls are generated. The results of the tool calls are returned using tool result objects. Each tool result object has a `toolCallId`, a `toolName`, a typed `args` object, and a typed `result`.

## Tool Choice

You can use the `toolChoice` setting to influence when a tool is selected. It supports the following settings:

- `auto` (default): the model can choose whether and which tools to call.
- `required`: the model must call a tool. It can choose which tool to call.
- `none`: the model must not call tools
- `{ type: 'tool', toolName: string (typed) }`: the model must call the specified tool

Here's an example of using a tool:

```javascript
import { z } from 'zod';
import { generateText, tool } from 'ai';

const result = await generateText({
  model: yourModel,
  tools: {
    weather: tool({
      description: 'Get the weather in a location',
      parameters: z.object({
        location: z.string().describe('The location to get the weather for'),
      }),
      execute: async ({ location }) => ({
        location,
        temperature: 72 + Math.floor(Math.random() * 21) - 10,
      }),
    }),
  },
  toolChoice: 'required', // force the model to call a tool
  prompt:
    'What is the weather in San Francisco and what attractions should I visit?',
});
```

## Schema Specification and Validation with Zod

Tool usage and structured object generation require the specification of schemas. The AI SDK uses Zod, the most popular JavaScript schema validation library, for schema specification and validation.

You can install Zod with:

```bash
npm install zod
```

You can then specify schemas, for example:

```javascript
import z from 'zod';

const recipeSchema = z.object({
  recipe: z.object({
    name: z.string(),
    ingredients: z.array(
      z.object({
        name: z.string(),
        amount: z.string(),
      }),
    ),
    steps: z.array(z.string()),
  }),
});
```

These schemas can be used to define parameters for tool calls and generated structured objects with `generateObject` and `streamObject`.

# AI SDK Core

Large Language Models (LLMs) are advanced programs that can understand, create, and engage with human language on a large scale. They are trained on vast amounts of written material to recognize patterns in language and predict what might come next in a given piece of text.

The Vercel AI SDK Core simplifies working with LLMs by offering a standardized way of integrating them into your app - so you can focus on building great AI applications for your users, not waste time on technical details.

For example, here's how you can generate text with various models using the Vercel AI SDK:

## OpenAI

```javascript
import { generateText } from "ai"
import { openai } from "@ai-sdk/openai"

const { text } = await generateText({
  model: openai("gpt-4-turbo"),
  prompt: "What is love?"
})
```

*Love is a complex and multifaceted emotion that can be felt and expressed in many different ways. It involves deep affection, care, compassion, and connection towards another person or thing. Love can take on various forms such as romantic love, platonic love, familial love, or self-love.*

## AI SDK Core Functions

AI SDK Core has various functions designed for text generation, structured data generation, and tool usage. These functions take a standardized approach to setting up prompts and settings, making it easier to work with different models.

- **generateText**: Generates text and tool calls. This function is ideal for non-interactive use cases such as automation tasks where you need to write text (e.g. drafting email or summarizing web pages) and for agents that use tools.

- **streamText**: Stream text and tool calls. You can use the streamText function for interactive use cases such as chat bots and content streaming. You can also generate UI components with tools (see Generative UI).

- **generateObject**: Generates a typed, structured object that matches a Zod schema. You can use this function to force the language model to return structured data, e.g. for information extraction, synthetic data generation, or classification tasks.

- **streamObject**: Stream a structured object that matches a Zod schema. You can use this function to stream generated UIs in combination with React Server Components (see Generative UI).

# Generating and Streaming Text

Large language models (LLMs) can generate text in response to a prompt, which can contain instructions and information to process. For example, you can ask a model to come up with a recipe, draft an email, or summarize a document.

The Vercel AI SDK Core provides two functions to generate text and stream it from LLMs:

- `generateText`: Generates text for a given prompt and model.
- `streamText`: Streams text from a given prompt and model.

Advanced LLM features such as tool calling and structured data generation are built on top of text generation.

## generateText

You can generate text using the `generateText` function. This function is ideal for non-interactive use cases where you need to write text (e.g., drafting email or summarizing web pages) and for agents that use tools.

```javascript
import { generateText } from 'ai';

const { text } = await generateText({
  model: yourModel,
  prompt: 'Write a vegetarian lasagna recipe for 4 people.',
});
```

You can use more advanced prompts to generate text with more complex instructions and content:

```javascript
import { generateText } from 'ai';

const { text } = await generateText({
  model: yourModel,
  system:
    'You are a professional writer. You write simple, clear, and concise content.',
  prompt: `summarize the following article in 3-5 sentences:\n${article}`,
});
```

## streamText

Depending on your model and prompt, it can take a large language model (LLM) up to a minute to finish generating its response. This delay can be unacceptable for interactive use cases such as chatbots or real-time applications, where users expect immediate responses.

Vercel AI SDK Core provides the `streamText` function which simplifies streaming text from LLMs.

```javascript
import { streamText } from 'ai';

const result = await streamText({
  model: yourModel,
  prompt: 'Invent a new holiday and describe its traditions.',
});

// use textStream as an async iterable:
for await (const textPart of result.textStream) {
  console.log(textPart);
}
```

You can use `streamText` on its own or in combination with AI SDK UI and AI SDK RSC.

`result.textStream` is also a ReadableStream, so you can use it in a browser or Node.js environment.

```javascript
import { streamText } from 'ai';

const result = await streamText({
  model: yourModel,
  prompt: 'Invent a new holiday and describe its traditions.',
});

// use textStream as a ReadableStream:
const reader = result.textStream.getReader();
while (true) {
  const { done, value } = await reader.read();
  if (done) {
    break;
  }
  process.stdout.write(value);
}
```

### onFinish callback

When using `streamText`, you can provide an `onFinish` callback that is triggered when the model finishes generating the response and all tool executions.

```javascript
import { streamText } from 'ai';

const result = await streamText({
  model: yourModel,
  prompt: 'Invent a new holiday and describe its traditions.',
  onFinish({ text, toolCalls, toolResults, finishReason, usage }) {
    // your own logic, e.g. for saving the chat history or recording usage
  },
});
```

### Result helper functions

The `result` object of `streamText` contains several helper functions to make the integration into AI SDK UI easier:

- `result.toDataStreamResponse()`: Creates a data stream response (with tool calls etc.).
- `result.toTextStreamResponse()`: Creates a simple text stream response.
- `result.pipeDataStreamToResponse()`: Writes AI stream delta output to a Node.js response-like object.
- `result.pipeTextStreamToResponse()`: Writes text delta output to a Node.js response-like object.

### Result promises

The `result` object of `streamText` contains several promises that resolve when all required data is available:

- `result.text`: The generated text.
- `result.toolCalls`: The tool calls made during text generation.
- `result.toolResults`: The tool results from the tool calls.
- `result.finishReason`: The reason the model finished generating text.
- `result.usage`: The usage of the model during text generation.

# Generating Structured Data

While text generation can be useful, your usecase will likely call for generating structured data. For example, you might want to extract information from text, classify data, or generate synthetic data.

Many language models are capable of generating structured data, often defined as using "JSON modes" or "tools". However, you need to manually provide schemas and then validate the generated data as LLMs can produce incorrect or incomplete structured data.

The Vercel AI SDK standardises structured object generation across model providers with the `generateObject` function.

The `generateObject` function uses Zod schemas or JSON schemas to specify the shape of the data that you want, and the AI model will generate data that conforms to that structure. The schema is also used to validate the generated data, ensuring type safety and correctness.

```javascript
import { generateObject } from 'ai';
import { z } from 'zod';

const { object } = await generateObject({
  model: yourModel,
  schema: z.object({
    recipe: z.object({
      name: z.string(),
      ingredients: z.array(z.object({ name: z.string(), amount: z.string() })),
      steps: z.array(z.string()),
    }),
  }),
  prompt: 'Generate a lasagna recipe.',
});
```

## Specifying Generation Mode

While some models (like OpenAI) natively support object generation, others require alternative methods, like modified tool calling. The `generateObject` function allows you to specify the method it will use to return structured data.

- `auto`: The provider will choose the best mode for the model. This recommended mode is used by default.
- `tool`: A tool with the JSON schema as parameters is provided and the provider is instructed to use it.
- `json`: The response format is set to JSON when supported by the provider, e.g. via json modes or grammar-guided generation. If grammar-guided generation is not supported, the JSON schema and instructions to generate JSON that conforms to the schema are injected into the system prompt.

Please note that not every provider supports all generation modes. Some providers do not support object generation at all.

## Specifying Schema Name and Description

You can optionally specify a name and description for the schema. These are used by some providers for additional LLM guidance, e.g. via tool or schema name.

```javascript
import { generateObject } from 'ai';
import { z } from 'zod';

const { object } = await generateObject({
  model: yourModel,
  schemaName: 'Recipe',
  schemaDescription: 'A recipe for a dish.',
  schema: z.object({
    name: z.string(),
    ingredients: z.array(z.object({ name: z.string(), amount: z.string() })),
    steps: z.array(z.string()),
  }),
  prompt: 'Generate a lasagna recipe.',
});
```

## Streaming Objects

Given the added complexity of returning structured data, model response time can be unacceptable for your interactive use case. With the `streamObject` function, you can stream the model's response as it is generated.

```javascript
import { streamObject } from 'ai';

const { partialObjectStream } = await streamObject({
  // ...
});

// use partialObjectStream as an async iterable
for await (const partialObject of partialObjectStream) {
  console.log(partialObject);
}
```

You can use `streamObject` to stream generated UIs in combination with React Server Components (see Generative UI)) or the `useObject` hook.

## Schema Writing Tips

The mapping from Zod schemas to LLM inputs (typically JSON schema) is not always straightforward, since the mapping is not one-to-one. Please checkout the following tips and the Prompt Engineering with Tools guide.

### Generating Arrays

Most models require an object as the top-level schema. If you want to generate an array, you can wrap it in an object with a single descriptive key and use destructuring to access the array.

```javascript
const {
  object: { users },
} = await generateObject({
  model: yourModel,
  schema: z.object({
    users: z.array(
      z.object({
        login: z.string(),
        fullName: z.string(),
        age: z.number(),
      }),
    ),
  }),
  prompt: 'Generate a list of fake user profiles for testing.',
});

console.log('users', users);
```

### Dates

Zod expects JavaScript Date objects, but models return dates as strings. You can specify and validate the date format using `z.string().datetime()` or `z.string().date()`, and then use a Zod transformer to convert the string to a Date object.

```javascript
const result = await generateObject({
  model: openai('gpt-4-turbo'),
  schema: z.object({
    events: z.array(
      z.object({
        event: z.string(),
        date: z
          .string()
          .date()
          .transform(value => new Date(value)),
      }),
    ),
  }),
  prompt: 'List 5 important events from the the year 2000.',
});
```

### Error Handling

When you use `generateObject`, errors are thrown when the model fails to generate proper JSON (`JSONParseError`) or when the generated JSON does not match the schema (`TypeValidationError`). Both error types contain additional information, e.g. the generated text or the invalid value.

You can use this to e.g. design a function that safely process the result object and also returns values in error cases:

```javascript
import { openai } from '@ai-sdk/openai';
import { JSONParseError, TypeValidationError, generateObject } from 'ai';
import { z } from 'zod';

const recipeSchema = z.object({
  recipe: z.object({
    name: z.string(),
    ingredients: z.array(z.object({ name: z.string(), amount: z.string() })),
    steps: z.array(z.string()),
  }),
});

type Recipe = z.infer<typeof recipeSchema>;

async function generateRecipe(
  food: string,
): Promise<
  | { type: 'success'; recipe: Recipe }
  | { type: 'parse-error'; text: string }
  | { type: 'validation-error'; value: unknown }
  | { type: 'unknown-error'; error: unknown }
> {
  try {
    const result = await generateObject({
      model: openai('gpt-4-turbo'),
      schema: recipeSchema,
      prompt: `Generate a ${food} recipe.`,
    });

    return { type: 'success', recipe: result.object };
  } catch (error) {
    if (TypeValidationError.isTypeValidationError(error)) {
      return { type: 'validation-error', value: error.value };
    } else if (JSONParseError.isJSONParseError(error)) {
      return { type: 'parse-error', text: error.text };
    } else {
      return { type: 'unknown-error', error };
    }
  }
}

# Tool Calling

As covered under Foundations, tools are objects that can be called by the model to perform a specific task.

When used with AI SDK Core, tools contain three elements:

1. **description**: An optional description of the tool that can influence when the tool is picked.
2. **parameters**: A Zod schema or a JSON schema that defines the parameters. The schema is consumed by the LLM, and also used to validate the LLM tool calls.
3. **execute**: An optional async function that is called with the arguments from the tool call. It produces a value of type `RESULT` (generic type). It is optional because you might want to forward tool calls to the client or to a queue instead of executing them in the same process.

The `tools` parameter of `generateText` and `streamText` is an object that has the tool names as keys and the tools as values:

```javascript
import { z } from 'zod';
import { generateText, tool } from 'ai';

const result = await generateText({
  model: yourModel,
  tools: {
    weather: tool({
      description: 'Get the weather in a location',
      parameters: z.object({
        location: z.string().describe('The location to get the weather for'),
      }),
      execute: async ({ location }) => ({
        location,
        temperature: 72 + Math.floor(Math.random() * 21) - 10,
      }),
    }),
  },
  prompt:
    'What is the weather in San Francisco and what attractions should I visit?',
});
```

You can use the `tool` helper function to infer the types of the `execute` parameters.

When a model uses a tool, it is called a "tool call" and the output of the tool is called a "tool result".

Tool calling is not restricted to only text generation. You can also use it to render user interfaces with Generative AI.

## Tool Choice

You can use the `toolChoice` setting to influence when a tool is selected. It supports the following settings:

- `auto` (default): the model can choose whether and which tools to call.
- `required`: the model must call a tool. It can choose which tool to call.
- `none`: the model must not call tools
- `{ type: 'tool', toolName: string (typed) }`: the model must call the specified tool

```javascript
import { z } from 'zod';
import { generateText, tool } from 'ai';

const result = await generateText({
  model: yourModel,
  tools: {
    weather: tool({
      description: 'Get the weather in a location',
      parameters: z.object({
        location: z.string().describe('The location to get the weather for'),
      }),
      execute: async ({ location }) => ({
        location,
        temperature: 72 + Math.floor(Math.random() * 21) - 10,
      }),
    }),
  },
  toolChoice: 'required', // force the model to call a tool
  prompt:
    'What is the weather in San Francisco and what attractions should I visit?',
});
```

## Tool Roundtrips

Tool roundtrips are currently only supported by `generateText`.

The large language model needs to know the tool results before it can continue generating text. This requires sending the tool results back to the model. You can enable this feature by setting the `maxToolRoundtrips` setting to a number greater than 0.

```javascript
import { z } from 'zod';
import { generateText, tool } from 'ai';

const result = await generateText({
  model: yourModel,
  tools: {
    weather: tool({
      description: 'Get the weather in a location',
      parameters: z.object({
        location: z.string().describe('The location to get the weather for'),
      }),
      execute: async ({ location }) => ({
        location,
        temperature: 72 + Math.floor(Math.random() * 21) - 10,
      }),
    }),
  },
  maxToolRoundtrips: 5, // allow up to 5 tool roundtrips
  prompt:
    'What is the weather in San Francisco and what attractions should I visit?',
});
```

# Prompt Engineering with Tools

When you create prompts that include tools, getting good results can be tricky as the number and complexity of your tools increases.

Here are a few tips to help you get the best results:

1. Use a model that is strong at tool calling, such as GPT-4 or GPT-4-Turbo. Weaker models will often struggle to call tools effectively and flawlessly.
2. Keep the number of tools low, e.g. to 5 or less.
3. Keep the complexity of the tool parameters low. Complex Zod schemas with many nested and optional elements, unions, etc. can be challenging for the model to work with.
4. Use semantically meaningful names for your tools, parameters, parameter properties, etc. The more information you pass to the model, the better it can understand what you want.
5. Add `.describe("...")` to your Zod schema properties to give the model hints about what a particular property is for.
6. When the output of a tool might be unclear to the model and there are dependencies between tools, use the description field of a tool to provide information about the output of the tool execution.
7. You can include example input/outputs of tool calls in your prompt to help the model understand how to use the tools. Keep in mind that the tools work with JSON objects, so the examples should use JSON.
8. In general, the goal should be to give the model all information it needs in a clear way.

# Agents

AI agents let the language model execute several steps in a non-deterministic way. The model can make decisions based on the context of the conversation and the user's input.

One approach to implementing agents is to allow the LLM to choose the next step in a loop. With `generateText`, you can combine tools with `maxToolRoundtrips`. This makes it possible to implement basic agents that reason at each step and make decisions based on the context.

## Example

This example demonstrates how to create an agent that solves math problems. It has a calculator tool (using `math.js`) that it can call to evaluate mathematical expressions.

```javascript
import { openai } from '@ai-sdk/openai';
import { generateText, tool } from 'ai';
import * as mathjs from 'mathjs';
import { z } from 'zod';

const problem =
  'A taxi driver earns $9461 per 1-hour of work. ' +
  'If he works 12 hours a day and in 1 hour ' +
  'he uses 12 liters of petrol with a price  of $134 for 1 liter. ' +
  'How much money does he earn in one day?';

console.log(`PROBLEM: ${problem}`);

const { text: answer } = await generateText({
  model: openai('gpt-4-turbo'),
  system:
    'You are solving math problems. ' +
    'Reason step by step. ' +
    'Use the calculator when necessary. ' +
    'When you give the final answer, ' +
    'provide an explanation for how you arrived at it.',
  prompt: problem,
  tools: {
    calculate: tool({
      description:
        'A tool for evaluating mathematical expressions. ' +
        'Example expressions: ' +
        "'1.2 * (2 + 4.5)', '12.7 cm to inch', 'sin(45 deg) ^ 2'.",
      parameters: z.object({ expression: z.string() }),
      execute: async ({ expression }) => mathjs.evaluate(expression),
    }),
  },
  maxToolRoundtrips: 10,
});

console.log(`ANSWER: ${answer}`);
```

### Accessing information from all roundtrips

Calling `generateText` with `maxToolRoundtrips` can result in several calls to the LLM (roundtrips). You can access information from all roundtrips by using the `roundtrips` property of the response.

```javascript
const { roundtrips } = await generateText({
  model: openai('gpt-4-turbo'),
  maxToolRoundtrips: 10,
  // ...
});

// extract all tool calls from the roundtrips:
const allToolCalls = roundtrips.flatMap(roundtrip => roundtrip.toolCalls);
```

## Settings

Large language models (LLMs) typically provide settings to augment their output.

All Vercel AI SDK functions support the following common settings in addition to the model, the prompt, and additional provider-specific settings:

```javascript
const result = await generateText({
  model: yourModel,
  maxTokens: 512,
  temperature: 0.3,
  maxRetries: 5,
  prompt: 'Invent a new holiday and describe its traditions.',
});
```

Some providers do not support all common settings. If you use a setting with a provider that does not support it, a warning will be generated. You can check the `warnings` property in the `result` object to see if any warnings were generated.

### `maxTokens`
Maximum number of tokens to generate.

### `temperature`
Temperature setting.

The value is passed through to the provider. The range depends on the provider and model. For most providers, `0` means almost deterministic results, and higher values mean more randomness.

It is recommended to set either `temperature` or `topP`, but not both.

### `topP`
Nucleus sampling.

The value is passed through to the provider. The range depends on the provider and model. For most providers, nucleus sampling is a number between `0` and `1`. E.g., `0.1` would mean that only tokens with the top 10% probability mass are considered.

It is recommended to set either `temperature` or `topP`, but not both.

### `topK`
Only sample from the top K options for each subsequent token.

Used to remove "long tail" low probability responses. Recommended for advanced use cases only. You usually only need to use `temperature`.

### `presencePenalty`
The presence penalty affects the likelihood of the model to repeat information that is already in the prompt.

The value is passed through to the provider. The range depends on the provider and model. For most providers, `0` means no penalty.

### `frequencyPenalty`
The frequency penalty affects the likelihood of the model to repeatedly use the same words or phrases.

The value is passed through to the provider. The range depends on the provider and model. For most providers, `0` means no penalty.

### `stopSequences`
The stop sequences to use for stopping the text generation.

If set, the model will stop generating text when one of the stop sequences is generated. Providers may have limits on the number of stop sequences.

### `seed`
It is the seed (integer) to use for random sampling. If set and supported by the model, calls will generate deterministic results.

### `maxRetries`
Maximum number of retries. Set to `0` to disable retries. Default: `2`.

### `abortSignal`
An optional abort signal that can be used to cancel the call.

### `headers`
Additional HTTP headers to be sent with the request. Only applicable for HTTP-based providers.

You can use the request headers to provide additional information to the provider, depending on what the provider supports. For example, some observability providers support headers such as `Prompt-Id`.

```javascript
import { generateText } from 'ai';
import { openai } from '@ai-sdk/openai';

const result = await generateText({
  model: openai('gpt-3.5-turbo'),
  prompt: 'Invent a new holiday and describe its traditions.',
  headers: {
    'Prompt-Id': 'my-prompt-id',
  },
});
```

# Embeddings

Embeddings are a way to represent words, phrases, or images as vectors in a high-dimensional space. In this space, similar words are close to each other, and the distance between words can be used to measure their similarity.

## Embedding a Single Value

The Vercel AI SDK provides the `embed` function to embed single values, which is useful for tasks such as finding similar words or phrases or clustering text. You can use it with embeddings models, e.g. `openai.embedding('text-embedding-3-large')` or `mistral.embedding('mistral-embed')`.

```javascript
import { embed } from 'ai';
import { openai } from '@ai-sdk/openai';

// 'embedding' is a single embedding object (number[])
const { embedding } = await embed({
  model: openai.embedding('text-embedding-3-small'),
  value: 'sunny day at the beach',
});
```

## Embedding Many Values

When loading data, e.g. when preparing a data store for retrieval-augmented generation (RAG), it is often useful to embed many values at once (batch embedding).

The Vercel AI SDK provides the `embedMany` function for this purpose. Similar to `embed`, you can use it with embeddings models, e.g. `openai.embedding('text-embedding-3-large')` or `mistral.embedding('mistral-embed')`.

```javascript
import { openai } from '@ai-sdk/openai';
import { embedMany } from 'ai';

// 'embeddings' is an array of embedding objects (number[][]).
// It is sorted in the same order as the input values.
const { embeddings } = await embedMany({
  model: openai.embedding('text-embedding-3-small'),
  values: [
    'sunny day at the beach',
    'rainy afternoon in the city',
    'snowy night in the mountains',
  ],
});
```

## Embedding Similarity

After embedding values, you can calculate the similarity between them using the `cosineSimilarity` function. This is useful to e.g. find similar words or phrases in a dataset. You can also rank and filter related items based on their similarity.

```javascript
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

## Token Usage

Many providers charge based on the number of tokens used to generate embeddings. Both `embed` and `embedMany` provide token usage information in the `usage` property of the result object:

```javascript
import { openai } from '@ai-sdk/openai';
import { embed } from 'ai';

const { embedding, usage } = await embed({
  model: openai.embedding('text-embedding-3-small'),
  value: 'sunny day at the beach',
});

console.log(usage); // { tokens: 10 }
```

# Provider Management

Provider management is an experimental feature. When you work with multiple providers and models, it is often desirable to manage them in a central place and access the models through simple string ids.

The Vercel AI SDK provides a `ProviderRegistry` for this purpose. You can register multiple providers. The provider id will become the prefix of the model id: `providerId:modelId`.

## Provider Registry

### Setup

You can create a registry with multiple providers and models using `experimental_createProviderRegistry`.

It is common to keep the registry setup in a separate file and import it where needed.

**registry.ts**

```typescript
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

### Language models

You can access language models by using the `languageModel` method on the registry. The provider id will become the prefix of the model id: `providerId:modelId`.

```typescript
import { generateText } from 'ai';
import { registry } from './registry';

const { text } = await generateText({
  model: registry.languageModel('openai:gpt-4-turbo'),
  prompt: 'Invent a new holiday and describe its traditions.',
});
```

### Text embedding models

You can access text embedding models by using the `textEmbeddingModel` method on the registry. The provider id will become the prefix of the model id: `providerId:modelId`.

```typescript
import { embed } from 'ai';
import { registry } from './registry';

const { embedding } = await embed({
  model: registry.textEmbeddingModel('openai:text-embedding-3-small'),
  value: 'sunny day at the beach',
});
```

## Model Maps

An alternative approach to the provider registry is to just create an object that maps ids to models. This has the following advantages:

1. You can restrict the models that you want to use (e.g., when you let your users choose models)
2. You can chose custom ids for the models (e.g., semantic ids)
3. You can predefine custom settings for the models

However, it is more verbose, and you need to specify each model individually.

**Examples**

```typescript
import { anthropic } from '@ai-sdk/anthropic';
import { openai } from '@ai-sdk/openai';
import { streamText } from 'ai';

const myModels = {
  'structuring-model': openai('gpt-4o-2024-08-06', {
    structuredOutputs: true,
  }),
  'smart-model': anthropic('claude-3-5-sonnet-20240620'),
} as const;

const result = await streamText({
  model: myModels['structuring-model'],
  prompt: 'Invent a new holiday and describe its traditions.',
});
```
# Error Handling

## Handling Regular Errors
Regular errors are thrown and can be handled using the `try/catch` block.

```javascript
import { generateText } from 'ai';

try {
  const { text } = await generateText({
    model: yourModel,
    prompt: 'Write a vegetarian lasagna recipe for 4 people.',
  });
} catch (error) {
  // handle error
}
```

## Handling Streaming Errors (Simple Streams)
When errors occur during streams that do not support error chunks, the error is thrown as a regular error. You can handle these errors using the `try/catch` block.

```javascript
import { generateText } from 'ai';

try {
  const { textStream } = await streamText({
    model: yourModel,
    prompt: 'Write a vegetarian lasagna recipe for 4 people.',
  });

  for await (const textPart of textStream) {
    process.stdout.write(textPart);
  }
} catch (error) {
  // handle error
}
```

## Handling Streaming Errors (Streaming with Error Support)
Full streams support error parts. You can handle those parts similar to other parts. It is recommended to also add a `try-catch` block for errors that happen outside of the streaming.

```javascript
import { generateText } from 'ai';

try {
  const { fullStream } = await streamText({
    model: yourModel,
    prompt: 'Write a vegetarian lasagna recipe for 4 people.',
  });

  for await (const part of fullStream) {
    switch (part.type) {
      // ... handle other part types

      case 'error': {
        const error = part.error;
        // handle error
        break;
      }
    }
  }
} catch (error) {
  // handle error
}

## Next.js App Router Quickstart

In this quick start tutorial, you'll build a simple AI-chatbot with a streaming user interface. Along the way, you'll learn key concepts and techniques that are fundamental to using the SDK in your own projects.

Check out *Prompt Engineering* and *HTTP Streaming* if you haven't heard of them.

### Prerequisites

To follow this quickstart, you'll need:

1. Node.js 18+ and pnpm installed on your local development machine.
2. An OpenAI API key.

If you haven't obtained your OpenAI API key, you can do so by signing up on the OpenAI website.

### Create Your Application

Start by creating a new Next.js application. This command will create a new directory named `my-ai-app` and set up a basic Next.js application inside it.

Be sure to select yes when prompted to use the App Router. If you are looking for the Next.js Pages Router quickstart guide, you can find it [here](link).

```
pnpm create next-app@latest my-ai-app
```

Navigate to the newly created directory:

```
cd my-ai-app
```

### Install dependencies

Install `ai` and `@ai-sdk/openai`, the Vercel AI package and Vercel AI SDK's OpenAI provider respectively.

Vercel AI SDK is designed to be a unified interface to interact with any large language model. This means that you can change model and providers with just one line of code! Learn more about available providers and building custom providers in the [providers section](link).

```
pnpm add ai @ai-sdk/openai zod
```

Make sure you are using `ai` version 3.1 or higher.

### Configure OpenAI API key

Create a `.env.local` file in your project root and add your OpenAI API Key. This key is used to authenticate your application with the OpenAI service.

```
touch .env.local
```

Edit the `.env.local` file:

```
OPENAI_API_KEY=xxxxxxxxx
```

Replace `xxxxxxxxx` with your actual OpenAI API key.

Vercel AI SDK's OpenAI Provider will default to using the `OPENAI_API_KEY` environment variable.

### Create a Route Handler

Create a route handler, `app/api/chat/route.ts` and add the following code:

```typescript
import { openai } from '@ai-sdk/openai';
import { streamText } from 'ai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = await streamText({
    model: openai('gpt-4-turbo'),
    messages,
  });

  return result.toDataStreamResponse();
}
```

Let's take a look at what is happening in this code:

1. Define an asynchronous POST request handler and extract `messages` from the body of the request. The `messages` variable contains a history of the conversation between you and the chatbot and provides the chatbot with the necessary context to make the next generation.
2. Call `streamText`, which is imported from the `ai` package. This function accepts a configuration object that contains a model provider (imported from `@ai-sdk/openai`) and `messages` (defined in step 1). You can pass additional settings to further customise the model's behaviour.
3. The `streamText` function returns a `StreamTextResult`. This result object contains the `toDataStreamResponse` function which converts the result to a streamed response object.
4. Finally, return the result to the client to stream the response.

This Route Handler creates a POST request endpoint at `/api/chat`.

### Wire up the UI

Now that you have a Route Handler that can query an LLM, it's time to setup your frontend. Vercel AI SDK's UI package abstracts the complexity of a chat interface into one hook, `useChat`.

Update your root page (`app/page.tsx`) with the following code to show a list of chat messages and provide a user message input:

```typescript
'use client';

import { useChat } from 'ai/react';

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit } = useChat();
  return (
    <div className="flex flex-col w-full max-w-md py-24 mx-auto stretch">
      {messages.map(m => (
        <div key={m.id} className="whitespace-pre-wrap">
          {m.role === 'user' ? 'User: ' : 'AI: '}
          {m.content}
        </div>
      ))}

      <form onSubmit={handleSubmit}>
        <input
          className="fixed bottom-0 w-full max-w-md p-2 mb-8 border border-gray-300 rounded shadow-xl"
          value={input}
          placeholder="Say something..."
          onChange={handleInputChange}
        />
      </form>
    </div>
  );
}
```

Make sure you add the "use client" directive to the top of your file. This allows you to add interactivity with Javascript.

This page utilizes the `useChat` hook, which will, by default, use the POST API route you created earlier (`/api/chat`). The hook provides functions and state for handling user input and form submission. The `useChat` hook provides multiple utility functions and state variables:

- `messages` - the current chat messages (an array of objects with `id`, `role`, and `content` properties).
- `input` - the current value of the user's input field.
- `handleInputChange` and `handleSubmit` - functions to handle user interactions (typing into the input field and submitting the form, respectively).
- `isLoading` - boolean that indicates whether the API request is in progress.

### Running Your Application

With that, you have built everything you need for your chatbot! To start your application, use the command:

```
pnpm run dev
```

Head to your browser and open [http://localhost:3000](http://localhost:3000). You should see an input field. Test it out by entering a message and see the AI chatbot respond in real-time! Vercel AI SDK makes it fast and easy to build AI chat interfaces with Next.js.

### Stream Data Alongside Response

Depending on your use case, you may want to stream additional data alongside the model's response. This can be done using `StreamData`.

#### Update your Route Handler

Make the following changes to your Route Handler (`app/api/chat/route.ts`):

```typescript
import { openai } from '@ai-sdk/openai';
import { streamText, StreamData } from 'ai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { messages } = await req.json();

  const data = new StreamData();
  data.append({ test: 'value' });

  const result = await streamText({
    model: openai('gpt-4-turbo'),
    messages,
    onFinish() {
      data.close();
    },
  });

  return result.toDataStreamResponse({ data });
}
```

In this code, you:

1. Create a new instance of `StreamData`.
2. Append the data you want to stream alongside the model's response.
3. Listen for the `onFinish` callback on `streamText` and close the stream data.
4. Pass the data into the `toDataStreamResponse` method.

#### Update your frontend

To access this data on the frontend, the `useChat` hook returns an optional value that stores this data. Update your root route with the following code to render the streamed data:

```typescript
'use client';

import { useChat } from 'ai/react';

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit, data } = useChat();
  return (
    <div className="flex flex-col w-full max-w-md py-24 mx-auto stretch">
      {data && <pre>{JSON.stringify(data, null, 2)}</pre>}
      {messages.map(m => (
        <div key={m.id} className="whitespace-pre-wrap">
          {m.role === 'user' ? 'User: ' : 'AI: '}
          {m.content}
        </div>
      ))}

      <form onSubmit={handleSubmit}>
        <input
          className="fixed bottom-0 w-full max-w-md p-2 mb-8 border border-gray-300 rounded shadow-xl"
          value={input}
          placeholder="Say something..."
          onChange={handleInputChange}
        />
      </form>
    </div>
  );
}
```

Head back to your browser ([http://localhost:3000](http://localhost:3000)) and enter a new message. You should see a JSON object appear with the value you sent from your API route!

### Introducing more granular control with `ai/rsc`

So far, you have used Vercel AI SDK's UI package to connect your frontend to your API route. This package is framework agnostic and provides simple abstractions for quickly building chat-like interfaces with LLMs.

The Vercel AI SDK also has a package (`ai/rsc`) specifically designed for frameworks that support the React Server Component architecture. With `ai/rsc`, you can build AI applications that go beyond pure text.

#### Next.js App Router

The Next.js App Router is a React Server Component (RSC) framework. This means that pages and components are rendered on the server. Optionally, you can add directives, like "use client" when you want to add interactivity using Javascript and "use server" when you want to ensure code will only run on the server.

The server-first architecture of RSCs enables a number of powerful features, like Server Actions, which we will use as our server-side environment to query the language model.

Server Actions are functions that run on a server, but can be called directly from your Next.js frontend. Server Actions reduce the amount of code you write while also providing end-to-end type safety between the client and server. You can learn more [here](link).

#### Create a Server Action

Create your first Server Action (`app/actions.tsx`) and add the following code:

```typescript
'use server';

import { createStreamableValue } from 'ai/rsc';
import { CoreMessage, streamText } from 'ai';
import { openai } from '@ai-sdk/openai';

export async function continueConversation(messages: CoreMessage[]) {
  const result = await streamText({
    model: openai('gpt-4-turbo'),
    messages,
  });

  const stream = createStreamableValue(result.textStream);
  return stream.value;
}
```

Let's take a look at what is happening in this code:

1. Add the "use server" directive at the top of the file to mark the function as a Server Action.
2. Define and export an async function (`continueConversation`) that takes one argument, `messages`, which is an array of type `CoreMessage`. The `messages` variable contains a history of the conversation between you and the chatbot and will provide the chatbot with the necessary context to make the next generation.
3. Call the `streamText` function which is imported from the `ai` package. To use this function, you pass it a configuration object that contains a model provider (imported from `@ai-sdk/openai`) and `messages` (defined in step 2). You can pass additional settings to further customise the model's behaviour.
4. Create a streamable value using the `createStreamableValue` function imported from the `ai/rsc` package. To use this function you pass the model's response as a text stream which can be accessed directly on the model response object (`result.textStream`).
5. Finally, return the value of the stream (`stream.value`).

#### Update the UI

Now that you have created a Server Action that can query an LLM, it's time to update your frontend. With `ai/rsc`, you have much finer control over how you send and receive streamable values from the LLM.

Update your root page (`app/page.tsx`) with the following code:

```typescript
'use client';

import { type CoreMessage } from 'ai';
import { useState } from 'react';
import { continueConversation } from './actions';
import { readStreamableValue } from 'ai/rsc';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Chat() {
  const [messages, setMessages] = useState<CoreMessage[]>([]);
  const [input, setInput] = useState('');
  return (
    <div className="flex flex-col w-full max-w-md py-24 mx-auto stretch">
      {messages.map((m, i) => (
        <div key={i} className="whitespace-pre-wrap">
          {m.role === 'user' ? 'User: ' : 'AI: '}
          {m.content as string}
        </div>
      ))}

      <form
        onSubmit={async e => {
          e.preventDefault();
          const newMessages: CoreMessage[] = [
            ...messages,
            { content: input, role: 'user' },
          ];

          setMessages(newMessages);
          setInput('');

          const result = await continueConversation(newMessages);

          for await (const content of readStreamableValue(result)) {
            setMessages([
              ...newMessages,
              {
                role: 'assistant',
                content: content as string,
              },
            ]);
          }
        }}
      >
        <input
          className="fixed bottom-0 w-full max-w-md p-2 mb-8 border border-gray-300 rounded shadow-xl"
          value={input}
          placeholder="Say something..."
          onChange={e => setInput(e.target.value)}
        />
      </form>
    </div>
  );
}
```

Let's look at how your implementation has changed. Without `useChat`, you have to manage your own state using the `useState` hook to manage the user's input and messages, respectively. The biggest change in your implementation is how you manage the form submission behaviour:

1. Define a new variable to house the existing messages and append the user's message.
2. Update the `messages` state by passing `newMessages` to the `setMessages` function.
3. Clear the `input` state with `setInput("")`.
4. Call your Server Action just like any other asynchronous function, passing the `newMessages` variable declared in the first step. This function will return a streamable value.
5. Use an asynchronous for-loop in conjunction with the `readStreamableValue` function to iterate over the stream returned by the Server Action and read its value.
6. Finally, update the `messages` state with the content streamed via the Server Action.

#### Streaming Additional Data

Stream additional data alongside the response from the model by simply returning an additional value in your Server Action. Update your `app/actions.tsx` with the following code:

```typescript
'use server';

import { createStreamableValue } from 'ai/rsc';
import { CoreMessage, streamText } from 'ai';
import { openai } from '@ai-sdk/openai';

export async function continueConversation(messages: CoreMessage[]) {
  'use server';
  const result = await streamText({
    model: openai('gpt-4-turbo'),
    messages,
  });
  const data = { test: 'hello' };
  const stream = createStreamableValue(result.textStream);
  return { message: stream.value, data };
}
```

The only change that you make here is to declare a new value (`data`) and return it alongside the stream.

## Update the UI

Update your root page with the following code:

### `app/page.tsx`

```typescript
'use client';

import { type CoreMessage } from 'ai';
import { useState } from 'react';
import { continueConversation } from './actions';
import { readStreamableValue } from 'ai/rsc';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Chat() {
  const [messages, setMessages] = useState<CoreMessage[]>([]);
  const [input, setInput] = useState('');
  const [data, setData] = useState<any>();
  return (
    <div className="flex flex-col w-full max-w-md py-24 mx-auto stretch">
      {data && <pre>{JSON.stringify(data, null, 2)}</pre>}
      {messages.map((m, i) => (
        <div key={i} className="whitespace-pre-wrap">
          {m.role === 'user' ? 'User: ' : 'AI: '}
          {m.content as string}
        </div>
      ))}

      <form
        onSubmit={async e => {
          e.preventDefault();
          const newMessages: CoreMessage[] = [
            ...messages,
            { content: input, role: 'user' },
          ];

          setMessages(newMessages);
          setInput('');

          const result = await continueConversation(newMessages);
          setData(result.data);

          for await (const content of readStreamableValue(result.message)) {
            setMessages([
              ...newMessages,
              {
                role: 'assistant',
                content: content as string,
              },
            ]);
          }
        }}
      >
        <input
          className="fixed bottom-0 w-full max-w-md p-2 mb-8 border border-gray-300 rounded shadow-xl"
          value={input}
          placeholder="Say something..."
          onChange={e => setInput(e.target.value)}
        />
      </form>
    </div>
  );
}
```

In the code above, you first create a new variable to manage the state of the additional data (`data`). Then, you update the state of the additional data with `setData(result.data)`. Just like that, you've sent additional data alongside the model's response.

The `ai/rsc` library is designed to give you complete control over streamable values. This unlocks LLM applications beyond the traditional chat format.

# Navigating the Library

The Vercel AI SDK is a powerful toolkit for building AI applications. This page will help you pick the right tools for your requirements.

Let's start with a quick overview of the Vercel AI SDK, which is comprised of three parts:

- **AI SDK Core**: A unified API for generating text, structured objects, and tool calls with LLMs.
- **AI SDK UI**: A set of framework-agnostic hooks for quickly building chat and generative user interface.
- **AI SDK RSC**: A library to stream generative user interfaces with React Server Components (RSC).

## Choosing the Right Tool for Your Environment

When deciding which part of the Vercel AI SDK to use, your first consideration should be the environment and existing stack you are working with. Different components of the SDK are tailored to specific frameworks and environments.

| Library | Purpose | Environment Compatibility |
| --- | --- | --- |
| AI SDK Core | Call any LLM with unified API (e.g. `generateText` and `generateObject`) | Any JS environment (e.g. Node.js, Deno, Browser) |
| AI SDK UI | Build streaming chat and generative UIs (e.g. `useChat`) | React & Next.js, Vue & Nuxt, Svelte & SvelteKit, Solid.js & SolidStart |
| AI SDK RSC | Stream generative UIs from Server to Client (e.g. `streamUI`) | Any framework that supports React Server Components (e.g. Next.js) |

### When to use AI SDK RSC

React Server Components (RSCs) provide a new approach to building React applications that allow components to render on the server, fetch data directly, and stream the results to the client, reducing bundle size and improving performance. They also introduce a new way to call server-side functions from anywhere in your application called Server Actions.

When considering whether to use AI SDK RSC, it's important to be aware of the current limitations of RSCs and Server Actions:

- **Cancellation**: currently, it is not possible to abort a stream using Server Actions. This will be improved in future releases of React and Next.js.
- **Increased Data Transfer**: using `createStreamableUI` can lead to quadratic data transfer (quadratic to the length of generated text). You can avoid this using `createStreamableValue` instead, and rendering the component client-side.
- **Re-mounting Issue During Streaming**: when using `createStreamableUI`, components re-mount on `.done()`, causing flickering.

If any of the above limitations are important to your application, we recommend using AI SDK UI. If these limitations don't affect your use case, AI SDK RSC is a great choice that enables powerful server-side rendering capabilities and seamless integration with RSCs.

# Examples in Next.js
# Examples for Next.js App Router

The Vercel AI SDK is designed to work with Next.js App Router. Since the App Router has first-class support for React Server Components, you can use the `ai/rsc` module to build applications with features like Generative User Interfaces and better Server-Client State Management.

As a result, the examples will demonstrate querying the language model and streaming the response to the client using React Server Components and Server Actions from `ai/rsc`, instead of using Route Handlers and hooks such as `useChat` and `useCompletion` from `ai/react`.

# Basics

One of the most basic things you can do with language models is to generate text. In this section, you will learn to generate text, and also *stream* it to the client.

Beyond text, you will also learn to generate structured data by providing a schema of your choice, and also *stream* it to the client.

# Generate Text

This example uses React Server Components (RSC). If you want to client-side rendering and hooks instead, check out the "generate text" example with `useState`.

A situation may arise when you need to generate text based on a prompt. For example, you may want to generate a response to a question or summarize a body of text. The `generateText` function can be used to generate text based on the input prompt.

[http://localhost:3000](http://localhost:3000)

## Answer

### Client

Let's create a simple React component that will call the `getAnswer` function when a button is clicked. The `getAnswer` function will call the `generateText` function from the `ai` module, which will then generate text based on the input prompt.

```typescript
// app/page.tsx

'use client';

import { useState } from 'react';
import { getAnswer } from './actions';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Home() {
  const [generation, setGeneration] = useState<string>('');

  return (
    <div>
      <button
        onClick={async () => {
          const { text } = await getAnswer('Why is the sky blue?');
          setGeneration(text);
        }}
      >
        Answer
      </button>
      <div>{generation}</div>
    </div>
  );
}
```

### Server

On the server side, we need to implement the `getAnswer` function, which will call the `generateText` function from the `ai` module. The `generateText` function will generate text based on the input prompt.

```typescript
// app/actions.ts

'use server';

import { generateText } from 'ai';
import { openai } from '@ai-sdk/openai';

export async function getAnswer(question: string) {
  const { text, finishReason, usage } = await generateText({
    model: openai('gpt-3.5-turbo'),
    prompt: question,
  });

  return { text, finishReason, usage };
}
```

# Stream Text Generation

This example uses React Server Components (RSC). If you want to client-side rendering and hooks instead, check out the "stream text" example with `useCompletion`.

Text generation can sometimes take a long time to complete, especially when you're generating a couple of paragraphs. In such cases, it is useful to stream the text generation process to the client in real-time. This allows the client to display the generated text as it is being generated, rather than have users wait for it to complete before displaying the result.

[http://localhost:3000](http://localhost:3000)

## Answer

### Client

Let's create a simple React component that will call the `generate` function when a button is clicked. The `generate` function will call the `streamText` function, which will then generate text based on the input prompt. To consume the stream of text in the client, we will use the `readStreamableValue` function from the `ai/rsc` module.

```typescript
'use client';

import { useState } from 'react';
import { generate } from './actions';
import { readStreamableValue } from 'ai/rsc';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Home() {
  const [generation, setGeneration] = useState<string>('');

  return (
    <div>
      <button
        onClick={async () => {
          const { output } = await generate('Why is the sky blue?');

          for await (const delta of readStreamableValue(output)) {
            setGeneration(currentGeneration => `${currentGeneration}${delta}`);
          }
        }}
      >
        Ask
      </button>

      <div>{generation}</div>
    </div>
  );
}
```

### Server

On the server side, we need to implement the `generate` function, which will call the `streamText` function. The `streamText` function will generate text based on the input prompt. In order to stream the text generation to the client, we will use `createStreambleValue` that can wrap any changable value and stream it to the client.

Using DevTools, we can see the text generation being streamed to the client in real-time.

```typescript
'use server';

import { streamText } from 'ai';
import { openai } from '@ai-sdk/openai';
import { createStreamableValue } from 'ai/rsc';

export async function generate(input: string) {
  const stream = createStreamableValue('');

  (async () => {
    const { textStream } = await streamText({
      model: openai('gpt-3.5-turbo'),
      prompt: input,
    });

    for await (const delta of textStream) {
      stream.update(delta);
    }

    stream.done();
  })();

  return { output: stream.value };
}
```

# Generate Object

This example uses React Server Components (RSC). If you want to client-side rendering and hooks instead, check out the "generate object" example with `useState`.

Earlier functions like `generateText` and `streamText` gave us the ability to generate unstructured text. However, if you want to generate structured data like JSON, you can provide a schema that describes the structure of your desired object to the `generateObject` function.

The function requires you to provide a schema using `zod`, a library for defining schemas for JavaScript objects. By using `zod`, you can also use it to validate the generated object and ensure that it conforms to the specified structure.

## View Notifications

### Client

Let's create a simple React component that will call the `getNotifications` function when a button is clicked. The function will generate a list of notifications as described in the schema.

```typescript
'use client';

import { useState } from 'react';
import { getNotifications } from './actions';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Home() {
  const [generation, setGeneration] = useState<string>('');

  return (
    <div>
      <button
        onClick={async () => {
          const { notifications } = await getNotifications(
            'Messages during finals week.'
          );

          setGeneration(JSON.stringify(notifications, null, 2));
        }}
      >
        View Notifications
      </button>

      <pre>{generation}</pre>
    </div>
  );
}
```

### Server

Now let's implement the `getNotifications` function. We'll use the `generateObject` function to generate the list of notifications based on the schema we defined earlier.

```typescript
'use server';

import { generateObject } from 'ai';
import { openai } from '@ai-sdk/openai';
import { z } from 'zod';

export async function getNotifications(input: string) {
  'use server';

  const { object: notifications } = await generateObject({
    model: openai('gpt-4-turbo'),
    system: 'You generate three notifications for a messages app.',
    prompt: input,
    schema: z.object({
      notifications: z.array(
        z.object({
          name: z.string().describe('Name of a fictional person.'),
          message: z.string().describe('Do not use emojis or links.'),
          minutesAgo: z.number(),
        })
      ),
    }),
  });

  return { notifications };
}
```

## Stream Object Generation

This example uses React Server Components (RSC). If you want to client-side rendering and hooks instead, check out the "streaming object generation" example with `useObject`.

Object generation can sometimes take a long time to complete, especially when you're generating a large schema. In such cases, it is useful to stream the object generation process to the client in real-time. This allows the client to display the generated object as it is being generated, rather than have users wait for it to complete before displaying the result.

[http://localhost:3000](http://localhost:3000)
*View Notifications*

### Client

Let's create a simple React component that will call the `getNotifications` function when a button is clicked. The function will generate a list of notifications as described in the schema.

```typescript
// app/page.tsx

'use client';

import { useState } from 'react';
import { generate } from './actions';
import { readStreamableValue } from 'ai/rsc';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Home() {
  const [generation, setGeneration] = useState<string>('');

  return (
    <div>
      <button
        onClick={async () => {
          const { object } = await generate('Messages during finals week.');

          for await (const partialObject of readStreamableValue(object)) {
            if (partialObject) {
              setGeneration(
                JSON.stringify(partialObject.notifications, null, 2),
              );
            }
          }
        }}
      >
        Ask
      </button>

      <pre>{generation}</pre>
    </div>
  );
}
```

### Server

Now let's implement the `getNotifications` function. We'll use the `generateObject` function to generate the list of fictional notifications based on the schema we defined earlier.

```typescript
// app/actions.ts

'use server';

import { streamObject } from 'ai';
import { openai } from '@ai-sdk/openai';
import { createStreamableValue } from 'ai/rsc';
import { z } from 'zod';

export async function generate(input: string) {
  'use server';

  const stream = createStreamableValue();

  (async () => {
    const { partialObjectStream } = await streamObject({
      model: openai('gpt-4-turbo'),
      system: 'You generate three notifications for a messages app.',
      prompt: input,
      schema: z.object({
        notifications: z.array(
          z.object({
            name: z.string().describe('Name of a fictional person.'),
            message: z.string().describe('Do not use emojis or links.'),
            minutesAgo: z.number(),
          }),
        ),
      }),
    });

    for await (const partialObject of partialObjectStream) {
      stream.update(partialObject);
    }

    stream.done();
  })();

  return { object: stream.value };
}
```

## Chat

So far we've learned how to generate text and structured data using single prompts. In this section, we will learn to use messages to add a sequence of messages to the language model and generate the response based on the context of the conversation – called *chat completion*.

# Generate Chat Completion

Previously, we were able to generate text and objects using either a single message prompt, a system prompt, or a combination of both of them. However, there may be times when you want to generate text based on a series of messages.

A chat completion allows you to generate text based on a series of messages. This series of messages can be any series of interactions between any number systems, but the most popular and relatable use case has been a series of messages that represent a conversation between a user and a model.

`http://localhost:3000`

**User:** How is it going?
**Assistant:** All good, how may I help you?
Why is the sky blue?

## Client

Let's create a simple conversation between a user and a model, and place a button that will call `continueConversation`.

```typescript
// app/page.tsx

'use client';

import { useState } from 'react';
import { Message, continueConversation } from './actions';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Home() {
  const [conversation, setConversation] = useState<Message[]>([]);
  const [input, setInput] = useState<string>('');

  return (
    <div>
      <div>
        {conversation.map((message, index) => (
          <div key={index}>
            {message.role}: {message.content}
          </div>
        ))}
      </div>

      <div>
        <input
          type="text"
          value={input}
          onChange={event => {
            setInput(event.target.value);
          }}
        />
        <button
          onClick={async () => {
            const { messages } = await continueConversation([
              ...conversation,
              { role: 'user', content: input },
            ]);

            setConversation(messages);
          }}
        >
          Send Message
        </button>
      </div>
    </div>
  );
}
```

## Server

Now, let's implement the `continueConversation` function that will insert the user's message into the conversation and generate a response.

```typescript
// app/actions.ts

'use server';

import { generateText } from 'ai';
import { openai } from '@ai-sdk/openai';

export interface Message {
  role: 'user' | 'assistant';
  content: string;
}

export async function continueConversation(history: Message[]) {
  'use server';

  const { text } = await generateText({
    model: openai('gpt-3.5-turbo'),
    system: 'You are a friendly assistant!',
    messages: history,
  });

  return {
    messages: [
      ...history,
      {
        role: 'assistant' as const,
        content: text,
      },
    ],
  };
}
```

# Stream Chat Completion

Chat completion can sometimes take a long time to finish, especially when the response is big. In such cases, it is useful to stream the chat completion to the client in real-time. This allows the client to display the new message as it is being generated by the model, rather than have users wait for it to finish.

`http://localhost:3000`
User: How is it going?
Assistant: All good, how may I help you?
Why is the sky blue?
Send Message

## Client

Let's create a simple conversation between a user and a model, and place a button that will call `continueConversation`.

```typescript
'use client';

import { useState } from 'react';
import { Message, continueConversation } from './actions';
import { readStreamableValue } from 'ai/rsc';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Home() {
  const [conversation, setConversation] = useState<Message[]>([]);
  const [input, setInput] = useState<string>('');

  return (
    <div>
      <div>
        {conversation.map((message, index) => (
          <div key={index}>
            {message.role}: {message.content}
          </div>
        ))}
      </div>

      <div>
        <input
          type="text"
          value={input}
          onChange={event => {
            setInput(event.target.value);
          }}
        />
        <button
          onClick={async () => {
            const { messages, newMessage } = await continueConversation([
              ...conversation,
              { role: 'user', content: input },
            ]);

            let textContent = '';

            for await (const delta of readStreamableValue(newMessage)) {
              textContent = `${textContent}${delta}`;

              setConversation([
                ...messages,
                { role: 'assistant', content: textContent },
              ]);
            }
          }}
        >
          Send Message
        </button>
      </div>
    </div>
  );
}
```

## Server

Now, let's implement the `continueConversation` function that will insert the user's message into the conversation and stream back the new message.

```typescript
'use server';

import { streamText } from 'ai';
import { openai } from '@ai-sdk/openai';
import { createStreamableValue } from 'ai/rsc';

export interface Message {
  role: 'user' | 'assistant';
  content: string;
}

export async function continueConversation(history: Message[]) {
  'use server';

  const stream = createStreamableValue();

  (async () => {
    const { textStream } = await streamText({
      model: openai('gpt-3.5-turbo'),
      system:
        "You are a dude that doesn't drop character until the DVD commentary.",
      messages: history,
    });

    for await (const text of textStream) {
      stream.update(text);
    }

    stream.done();
  })();

  return {
    messages: history,
    newMessage: stream.value,
  };
}
```

# Multi-Modal Chatbot

In this guide, you will build a multi-modal AI-chatbot with a streaming user interface.

Multi-modal refers to the ability of the chatbot to understand and generate responses in multiple formats, such as text, images, and videos. In this example, we will focus on sending images and generating text-based responses.

## Implementation Plan

To build a multi-modal chatbot, you will need to:

1. Create a Route Handler to handle incoming chat messages and generate responses.
2. Wire up the UI to display chat messages, provide a user input, and handle submitting new messages.
3. Add the ability to upload images and attach them alongside the chat messages.

### Create a Route Handler

Create a route handler, `app/api/chat/route.ts` and add the following code:

```typescript
import { openai } from '@ai-sdk/openai';
import { convertToCoreMessages, streamText } from 'ai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = await streamText({
    model: openai('gpt-4-turbo'),
    messages: convertToCoreMessages(messages),
  });

  return result.toDataStreamResponse();
}
```

Let's take a look at what is happening in this code:

1. Define an asynchronous POST request handler and extract messages from the body of the request. The `messages` variable contains a history of the conversation between you and the chatbot and provides the chatbot with the necessary context to make the next generation.
2. Call `streamText`, which is imported from the `ai` package. This function accepts a configuration object that contains a model provider (imported from `@ai-sdk/openai`) and `messages` (defined in step 1). You can pass additional settings to further customise the model's behaviour.
3. The `streamText` function returns a `StreamTextResult`. This result object contains the `toDataStreamResponse` function which converts the result to a streamed response object.
4. Finally, return the result to the client to stream the response.

This Route Handler creates a POST request endpoint at `/api/chat`.

### Wire up the UI

Now that you have a Route Handler that can query a large language model (LLM), it's time to setup your frontend. AI SDK UI abstracts the complexity of a chat interface into one hook, `useChat`.

Update your root page (`app/page.tsx`) with the following code to show a list of chat messages and provide a user message input:

```typescript
'use client';

import { useChat } from 'ai/react';

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit } = useChat();
  return (
    <div className="flex flex-col w-full max-w-md py-24 mx-auto stretch">
      {messages.map(m => (
        <div key={m.id} className="whitespace-pre-wrap">
          {m.role === 'user' ? 'User: ' : 'AI: '}
          {m.content}
        </div>
      ))}

      <form
        onSubmit={handleSubmit}
        className="fixed bottom-0 w-full max-w-md mb-8 border border-gray-300 rounded shadow-xl"
      >
        <input
          className="w-full p-2"
          value={input}
          placeholder="Say something..."
          onChange={handleInputChange}
        />
      </form>
    </div>
  );
}
```

Make sure you add the "use client" directive to the top of your file. This allows you to add interactivity with Javascript.

This page utilizes the `useChat` hook, which will, by default, use the POST API route you created earlier (`/api/chat`). The hook provides functions and state for handling user input and form submission. The `useChat` hook provides multiple utility functions and state variables:

- `messages` - the current chat messages (an array of objects with `id`, `role`, and `content` properties).
- `input` - the current value of the user's input field.
- `handleInputChange` and `handleSubmit` - functions to handle user interactions (typing into the input field and submitting the form, respectively).
- `isLoading` - boolean that indicates whether the API request is in progress.

### Add Image Upload

To make your chatbot multi-modal, let's add the ability to upload and send images to the model. There are two ways to send attachments alongside a message with the `useChat` hook: by providing a `FileList` object or a list of URLs to the `handleSubmit` function. In this guide, you will be using the `FileList` approach as it does not require any additional setup.

Update your root page (`app/page.tsx`) with the following code:

```typescript
'use client';

import { useChat } from 'ai/react';
import { useRef, useState } from 'react';

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit } = useChat();

  const [files, setFiles] = useState<FileList | undefined>(undefined);
  const fileInputRef = useRef<HTMLInputElement>(null);

  return (
    <div className="flex flex-col w-full max-w-md py-24 mx-auto stretch">
      {messages.map(m => (
        <div key={m.id} className="whitespace-pre-wrap">
          {m.role === 'user' ? 'User: ' : 'AI: '}
          {m.content}
          <div>
            {m?.experimental_attachments
              ?.filter(attachment =>
                attachment?.contentType?.startsWith('image/'),
              )
              .map((attachment, index) => (
                <img
                  key={`${m.id}-${index}`}
                  src={attachment.url}
                  width={500}
                  alt={attachment.name}
                />
              ))}
          </div>
        </div>
      ))}

      <form
        className="fixed bottom-0 w-full max-w-md p-2 mb-8 border border-gray-300 rounded shadow-xl space-y-2"
        onSubmit={event => {
          handleSubmit(event, {
            experimental_attachments: files,
          });

          setFiles(undefined);

          if (fileInputRef.current) {
            fileInputRef.current.value = '';
          }
        }}
      >
        <input
          type="file"
          className=""
          onChange={event => {
            if (event.target.files) {
              setFiles(event.target.files);
            }
          }}
          multiple
          ref={fileInputRef}
        />
        <input
          className="w-full p-2"
          value={input}
          placeholder="Say something..."
          onChange={handleInputChange}
        />
      </form>
    </div>
  );
}
```

In this code, you:

1. Create state to hold the files and create a ref to the file input field.
2. Display the "uploaded" files in the UI.
3. Update the `onSubmit` function, to call the `handleSubmit` function manually, passing the the files as an option using the `experimental_attachments` key.
4. Add a file input field to the form, including an `onChange` handler to handle updating the `files` state.

# Tools

Certain language models have the ability to use external tools to perform tasks, like using a calculator to solve a math problem or using a browser to search for information. The most common way to share tool information with language models is to share a function definition, along with its description, for it to execute and generate a response based on the output.

In this section, you will learn how to use the `tools` parameter to allow language models to call these functions in your Next.js application.

You will also briefly explore rendering React components as part of a function's output, which can be useful for creating user interfaces that go beyond text.

# Call Tools

Some models allow developers to provide a list of *tools* that can be called at any time during a generation. This is useful for extending the capabilities of a language model to either use logic or data to interact with systems external to the model.

## Client

Let's create a simple conversation between a user and model and place a button that will call `continueConversation`.

### `app/page.tsx`

```typescript
'use client';

import { useState } from 'react';
import { Message, continueConversation } from './actions';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Home() {
  const [conversation, setConversation] = useState<Message[]>([]);
  const [input, setInput] = useState<string>('');

  return (
    <div>
      <div>
        {conversation.map((message, index) => (
          <div key={index}>
            {message.role}: {message.content}
          </div>
        ))}
      </div>

      <div>
        <input
          type="text"
          value={input}
          onChange={event => {
            setInput(event.target.value);
          }}
        />
        <button
          onClick={async () => {
            const { messages } = await continueConversation([
              ...conversation,
              { role: 'user', content: input },
            ]);

            setConversation(messages);
          }}
        >
          Send Message
        </button>
      </div>
    </div>
  );
}
```

## Server

Now, let's implement the `continueConversation` action that uses `generateText` to generate a response to the user's question. We will use the `tools` parameter to specify our own function called `celsiusToFahrenheit` that will convert a user-given value in Celsius to Fahrenheit.

We will use `zod` to specify the schema for the `celsiusToFahrenheit` function's parameters.

### `app/actions.ts`

```typescript
'use server';

import { generateText } from 'ai';
import { openai } from '@ai-sdk/openai';
import { z } from 'zod';

export interface Message {
  role: 'user' | 'assistant';
  content: string;
}

export async function continueConversation(history: Message[]) {
  'use server';

  const { text, toolResults } = await generateText({
    model: openai('gpt-3.5-turbo'),
    system: 'You are a friendly assistant!',
    messages: history,
    tools: {
      celsiusToFahrenheit: {
        description: 'Converts celsius to fahrenheit',
        parameters: z.object({
          value: z.string().describe('The value in celsius'),
        }),
        execute: async ({ value }) => {
          const celsius = parseFloat(value);
          const fahrenheit = celsius * (9 / 5) + 32;
          return `${celsius}°C is ${fahrenheit.toFixed(2)}°F`;
        },
      },
    },
  });

  return {
    messages: [
      ...history,
      {
        role: 'assistant' as const,
        content:
          text || toolResults.map(toolResult => toolResult.result).join('\n'),
      },
    ],
  };
}
```

# Call Tools in Parallel

Some language models support calling tools in parallel. This is particularly useful when multiple tools are independent of each other and can be executed in parallel during the same generation step.

## Client

Let's modify our previous example to call `getWeather` tool for multiple cities in parallel.

### `app/page.tsx`

```typescript
'use client';

import { useState } from 'react';
import { Message, continueConversation } from './actions';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Home() {
  const [conversation, setConversation] = useState<Message[]>([]);
  const [input, setInput] = useState<string>('');

  return (
    <div>
      <div>
        {conversation.map((message, index) => (
          <div key={index}>
            {message.role}: {message.content}
          </div>
        ))}
      </div>

      <div>
        <input
          type="text"
          value={input}
          onChange={event => {
            setInput(event.target.value);
          }}
        />
        <button
          onClick={async () => {
            const { messages } = await continueConversation([
              ...conversation,
              { role: 'user', content: input },
            ]);

            setConversation(messages);
          }}
        >
          Send Message
        </button>
      </div>
    </div>
  );
}
```

## Server

Let's update the `tools` object to now use the `getWeather` function instead.

### `app/actions.ts`

```typescript
'use server';

import { generateText } from 'ai';
import { openai } from '@ai-sdk/openai';
import { z } from 'zod';

export interface Message {
  role: 'user' | 'assistant';
  content: string;
}

function getWeather({ city, unit }) {
  // This function would normally make an
  // API request to get the weather.

  return { value: 25, description: 'Sunny' };
}

export async function continueConversation(history: Message[]) {
  'use server';

  const { text, toolResults } = await generateText({
    model: openai('gpt-3.5-turbo'),
    system: 'You are a friendly weather assistant!',
    messages: history,
    tools: {
      getWeather: {
        description: 'Get the weather for a location',
        parameters: z.object({
          city: z.string().describe('The city to get the weather for'),
          unit: z
            .enum(['C', 'F'])
            .describe('The unit to display the temperature in'),
        }),
        execute: async ({ city, unit }) => {
          const weather = getWeather({ city, unit });
          return `It is currently ${weather.value}°${unit} and ${weather.description} in ${city}!`;
        },
      },
    },
  });

  return {
    messages: [
      ...history,
      {
        role: 'assistant' as const,
        content:
          text || toolResults.map(toolResult => toolResult.result).join('\n'),
      },
    ],
  };
}
```

# Render Interface During Tool Call

An interesting consequence of language models that can call tools is that this ability can be used to render visual interfaces by streaming React components to the client.

## Client

We can make a few changes to our previous example where the assistant could get the weather for any city by calling the `getWeather` tool. This time, instead of returning text during the tool call, we will stream a React component that will be rendered on the client using `createStreamableUI` from `ai/rsc`.

```typescript
// app/page.tsx

'use client';

import { useState } from 'react';
import { Message, continueConversation } from './actions';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Home() {
  const [conversation, setConversation] = useState<Message[]>([]);
  const [input, setInput] = useState<string>('');

  return (
    <div>
      <div>
        {conversation.map((message, index) => (
          <div key={index}>
            {message.role}: {message.content}
            {message.display}
          </div>
        ))}
      </div>

      <div>
        <input
          type="text"
          value={input}
          onChange={event => {
            setInput(event.target.value);
          }}
        />
        <button
          onClick={async () => {
            const { messages } = await continueConversation([
              // exclude React components from being sent back to the server:
              ...conversation.map(({ role, content }) => ({ role, content })),
              { role: 'user', content: input },
            ]);

            setConversation(messages);
          }}
        >
          Send Message
        </button>
      </div>
    </div>
  );
}
```

```typescript
// components/weather.tsx

export async function Weather({ city, unit }) {
  const data = await fetch(
    `https://api.example.com/weather?city=${city}&unit=${unit}`,
  );

  return (
    <div>
      <div>{data.temperature}</div>
      <div>{data.unit}</div>
      <div>{data.description}</div>
    </div>
  );
}
```

## Server

```typescript
// app/actions.tsx

'use server';

import { Weather } from '@/components/weather';
import { generateText } from 'ai';
import { openai } from '@ai-sdk/openai';
import { createStreamableUI } from 'ai/rsc';
import { ReactNode } from 'react';
import { z } from 'zod';

export interface Message {
  role: 'user' | 'assistant';
  content: string;
  display?: ReactNode;
}

export async function continueConversation(history: Message[]) {
  const stream = createStreamableUI();

  const { text, toolResults } = await generateText({
    model: openai('gpt-3.5-turbo'),
    system: 'You are a friendly weather assistant!',
    messages: history,
    tools: {
      showWeather: {
        description: 'Show the weather for a given location.',
        parameters: z.object({
          city: z.string().describe('The city to show the weather for.'),
          unit: z
            .enum(['C', 'F'])
            .describe('The unit to display the temperature in'),
        }),
        execute: async ({ city, unit }) => {
          stream.done(<Weather city={city} unit={unit} />);
          return `Here's the weather for ${city}!`;
        },
      },
    },
  });

  return {
    messages: [
      ...history,
      {
        role: 'assistant' as const,
        content:
          text || toolResults.map(toolResult => toolResult.result).join(),
        display: stream.value,
      },
    ],
  };
}
```

# State Management

In this section you will learn how to manage states between the language model on the server, and the user interface on the client. You will learn how to use `createAI` to initiate a context provider that can be used to manage these two states.

You will also learn about the function callbacks that `createAI` supports, which can be used to save and restore the AI and UI state of your application.

## AI and UI States

In our previous examples, there seems to be a recurring pattern of having a state for the language model on the server, and a state for the UI on the client. However, it can get tricky to manage these two states separately. For example, if the user types something in the input field, we need to update the UI state, but we also need to send the input to the server to update the AI state.

As a result, the `ai/rsc` library provides a way to seamlessly manage both states together using a context provider that wraps the client application and makes the AI state available to all its children. This way, the client application can access and update the AI state directly keeping the two states in sync.

# Client

Let's use layout to wrap the children components of page with the AI context provider.

## `app/layout.tsx`

```typescript
import { ReactNode } from 'react';
import { AI } from './actions';

export default function RootLayout({
  children,
}: Readonly<{ children: ReactNode }>) {
  return (
    <html lang="en">
      <body>
        <AI>{children}</AI>
      </body>
    </html>
  );
}
```

## `app/page.tsx`

```typescript
'use client';

import { useState } from 'react';
import { ClientMessage } from './actions';
import { useActions, useUIState } from 'ai/rsc';
import { generateId } from 'ai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Home() {
  const [input, setInput] = useState<string>('');
  const [conversation, setConversation] = useUIState();
  const { continueConversation } = useActions();

  return (
    <div>
      <div>
        {conversation.map((message: ClientMessage) => (
          <div key={message.id}>
            {message.role}: {message.display}
          </div>
        ))}
      </div>

      <div>
        <input
          type="text"
          value={input}
          onChange={event => {
            setInput(event.target.value);
          }}
        />
        <button
          onClick={async () => {
            setConversation((currentConversation: ClientMessage[]) => [
              ...currentConversation,
              { id: generateId(), role: 'user', display: input },
            ]);

            const message = await continueConversation(input);

            setConversation((currentConversation: ClientMessage[]) => [
              ...currentConversation,
              message,
            ]);
          }}
        >
          Send Message
        </button>
      </div>
    </div>
  );
}
```

# Server

## `app/actions.tsx`

```typescript
'use server';

import { createAI, getMutableAIState, streamUI } from 'ai/rsc';
import { openai } from '@ai-sdk/openai';
import { ReactNode } from 'react';
import { z } from 'zod';
import { generateId } from 'ai';
import { Stock } from '@ai-studio/components/stock';

export interface ServerMessage {
  role: 'user' | 'assistant';
  content: string;
}

export interface ClientMessage {
  id: string;
  role: 'user' | 'assistant';
  display: ReactNode;
}

export async function continueConversation(
  input: string,
): Promise<ClientMessage> {
  'use server';

  const history = getMutableAIState();

  const result = await streamUI({
    model: openai('gpt-3.5-turbo'),
    messages: [...history.get(), { role: 'user', content: input }],
    text: ({ content, done }) => {
      if (done) {
        history.done((messages: ServerMessage[]) => [
          ...messages,
          { role: 'assistant', content },
        ]);
      }

      return <div>{content}</div>;
    },
    tools: {
      showStockInformation: {
        description:
          'Get stock information for symbol for the last numOfMonths months',
        parameters: z.object({
          symbol: z
            .string()
            .describe('The stock symbol to get information for'),
          numOfMonths: z
            .number()
            .describe('The number of months to get historical information for'),
        }),
        generate: async ({ symbol, numOfMonths }) => {
          history.done((messages: ServerMessage[]) => [
            ...messages,
            {
              role: 'assistant',
              content: `Showing stock information for ${symbol}`,
            },
          ]);

          return <Stock symbol={symbol} numOfMonths={numOfMonths} />;
        },
      },
    },
  });

  return {
    id: generateId(),
    role: 'assistant',
    display: result.value,
  };
}

export const AI = createAI<ServerMessage[], ClientMessage[]>({
  actions: {
    continueConversation,
  },
  initialAIState: [],
  initialUIState: [],
});
```

# Save and Restore AI State

Sometimes conversations with language models can get interesting and you might want to save the state so you can revisit it or continue the conversation later.

`createAI` has an experimental callback function called `onSetAIState` that gets called whenever the AI state changes. You can use this to save the AI state to a filename or a database.

## Client

### `app/layout.tsx`

```typescript
import { AI, ServerMessage } from './actions';

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  // get chat history from database
  const history: ServerMessage[] = getChat();

  return (
    <html lang="en">
      <body>
        <AI initialAIState={history} initialUIState={[]}>
          {children}
        </AI>
      </body>
    </html>
  );
}
```

### `app/page.tsx`

```typescript
'use client';

import { useState } from 'react';
import { ClientMessage } from './actions';
import { useActions, useUIState } from 'ai/rsc';
import { generateId } from 'ai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Home() {
  const [input, setInput] = useState<string>('');
  const [conversation, setConversation] = useUIState();
  const { continueConversation } = useActions();

  return (
    <div>
      <div>
        {conversation.map((message: ClientMessage) => (
          <div key={message.id}>
            {message.role}: {message.display}
          </div>
        ))}
      </div>

      <div>
        <input
          type="text"
          value={input}
          onChange={event => {
            setInput(event.target.value);
          }}
        />
        <button
          onClick={async () => {
            setConversation((currentConversation: ClientMessage[]) => [
              ...currentConversation,
              { id: generateId(), role: 'user', display: input },
            ]);

            const message = await continueConversation(input);

            setConversation((currentConversation: ClientMessage[]) => [
              ...currentConversation,
              message,
            ]);
          }}
        >
          Send Message
        </button>
      </div>
    </div>
  );
}
```

## Server

We will use the callback function to listen to state changes and save the conversation once we receive a done event.

### `app/actions.tsx`

```typescript
'use server';

import { createAI, getAIState, getMutableAIState, streamUI } from 'ai/rsc';
import { openai } from '@ai-sdk/openai';
import { ReactNode } from 'react';
import { z } from 'zod';
import { generateId } from 'ai';
import { Stock } from '@ai-studio/components/stock';

export interface ServerMessage {
  role: 'user' | 'assistant' | 'function';
  content: string;
}

export interface ClientMessage {
  id: string;
  role: 'user' | 'assistant' | 'function';
  display: ReactNode;
}

export async function continueConversation(
  input: string,
): Promise<ClientMessage> {
  'use server';

  const history = getMutableAIState();

  const result = await streamUI({
    model: openai('gpt-3.5-turbo'),
    messages: [...history.get(), { role: 'user', content: input }],
    text: ({ content, done }) => {
      if (done) {
        history.done([
          ...history.get(),
          { role: 'user', content: input },
          { role: 'assistant', content },
        ]);
      }

      return <div>{content}</div>;
    },
    tools: {
      showStockInformation: {
        description:
          'Get stock information for symbol for the last numOfMonths months',
        parameters: z.object({
          symbol: z
            .string()
            .describe('The stock symbol to get information for'),
          numOfMonths: z
            .number()
            .describe('The number of months to get historical information for'),
        }),
        generate: async ({ symbol, numOfMonths }) => {
          history.done([
            ...history.get(),
            {
              role: 'function',
              name: 'showStockInformation',
              content: JSON.stringify({ symbol, numOfMonths }),
            },
          ]);

          return <Stock symbol={symbol} numOfMonths={numOfMonths} />;
        },
      },
    },
  });

  return {
    id: generateId(),
    role: 'assistant',
    display: result.value,
  };
}

export const AI = createAI<ServerMessage[], ClientMessage[]>({
  actions: {
    continueConversation,
  },
  onSetAIState: async ({ state, done }) => {
    'use server';

    if (done) {
      saveChat(state);
    }
  },
  onGetUIState: async () => {
    'use server';

    const history: ServerMessage[] = getAIState();

    return history.map(({ role, content }) => ({
      id: generateId(),
      role,
      display:
        role === 'function' ? <Stock {...JSON.parse(content)} /> : content,
    }));
  },
});
```

# Generative User Interface

In this section, you will learn to use the `streamUI` function to stream generative user interfaces to the client based on the response from the language model.

## Route Components

We've now seen how a language model can call a function and render a component based on a conversation with the user.

When we define multiple functions in tools, it is possible for the model to reason out the right functions to call based on whatever the user's intent is. This means that you can write a bunch of functions without the burden of implementing complex routing logic to run them.

### Client

```typescript
// app/page.tsx

'use client';

import { useState } from 'react';
import { ClientMessage } from './actions';
import { useActions, useUIState } from 'ai/rsc';
import { generateId } from 'ai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Home() {
  const [input, setInput] = useState<string>('');
  const [conversation, setConversation] = useUIState();
  const { continueConversation } = useActions();

  return (
    <div>
      <div>
        {conversation.map((message: ClientMessage) => (
          <div key={message.id}>
            {message.role}: {message.display}
          </div>
        ))}
      </div>

      <div>
        <input
          type="text"
          value={input}
          onChange={event => {
            setInput(event.target.value);
          }}
        />
        <button
          onClick={async () => {
            setConversation((currentConversation: ClientMessage[]) => [
              ...currentConversation,
              { id: generateId(), role: 'user', display: input },
            ]);

            const message = await continueConversation(input);

            setConversation((currentConversation: ClientMessage[]) => [
              ...currentConversation,
              message,
            ]);
          }}
        >
          Send Message
        </button>
      </div>
    </div>
  );
}
```

### Components

#### Stock

```typescript
// components/stock.tsx

export async function Stock({ symbol, numOfMonths }) {
  const data = await fetch(
    `https://api.example.com/stock/${symbol}/${numOfMonths}`,
  );

  return (
    <div>
      <div>{symbol}</div>

      <div>
        {data.timeline.map(data => (
          <div>
            <div>{data.date}</div>
            <div>{data.value}</div>
          </div>
        ))}
      </div>
    </div>
  );
}
```

#### Flight

```typescript
// components/flight.tsx

export async function Flight({ flightNumber }) {
  const data = await fetch(`https://api.example.com/flight/${flightNumber}`);

  return (
    <div>
      <div>{flightNumber}</div>
      <div>{data.status}</div>
      <div>{data.source}</div>
      <div>{data.destination}</div>
    </div>
  );
}
```

### Server

```typescript
// app/actions.tsx

'use server';

import { createAI, getMutableAIState, streamUI } from 'ai/rsc';
import { openai } from '@ai-sdk/openai';
import { ReactNode } from 'react';
import { z } from 'zod';
import { generateId } from 'ai';
import { Stock } from '@/components/stock';
import { Flight } from '@/components/flight';

export interface ServerMessage {
  role: 'user' | 'assistant';
  content: string;
}

export interface ClientMessage {
  id: string;
  role: 'user' | 'assistant';
  display: ReactNode;
}

export async function continueConversation(
  input: string,
): Promise<ClientMessage> {
  'use server';

  const history = getMutableAIState();

  const result = await streamUI({
    model: openai('gpt-3.5-turbo'),
    messages: [...history.get(), { role: 'user', content: input }],
    text: ({ content, done }) => {
      if (done) {
        history.done((messages: ServerMessage[]) => [
          ...messages,
          { role: 'assistant', content },
        ]);
      }

      return <div>{content}</div>;
    },
    tools: {
      showStockInformation: {
        description:
          'Get stock information for symbol for the last numOfMonths months',
        parameters: z.object({
          symbol: z
            .string()
            .describe('The stock symbol to get information for'),
          numOfMonths: z
            .number()
            .describe('The number of months to get historical information for'),
        }),
        generate: async ({ symbol, numOfMonths }) => {
          history.done((messages: ServerMessage[]) => [
            ...messages,
            {
              role: 'assistant',
              content: `Showing stock information for ${symbol}`,
            },
          ]);

          return <Stock symbol={symbol} numOfMonths={numOfMonths} />;
        },
      },
      showFlightStatus: {
        description: 'Get the status of a flight',
        parameters: z.object({
          flightNumber: z
            .string()
            .describe('The flight number to get status for'),
        }),
        generate: async ({ flightNumber }) => {
          history.done((messages: ServerMessage[]) => [
            ...messages,
            {
              role: 'assistant',
              content: `Showing flight status for ${flightNumber}`,
            },
          ]);

          return <Flight flightNumber={flightNumber} />;
        },
      },
    },
  });

  return {
    id: generateId(),
    role: 'assistant',
    display: result.value,
  };
}

export const AI = createAI<ServerMessage[], ClientMessage[]>({
  actions: {
    continueConversation,
  },
  initialAIState: [],
  initialUIState: [],
});
```

# Stream Component Updates

If you haven't noticed already, in our previous example we've been streaming React components from the server to the client. By streaming the components, we open up the possibility to update these components based on state changes that occur in the server.

## Client
`app/page.tsx`

```typescript
'use client';

import { useState } from 'react';
import { ClientMessage } from './actions';
import { useActions, useUIState } from 'ai/rsc';
import { generateId } from 'ai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Home() {
  const [input, setInput] = useState<string>('');
  const [conversation, setConversation] = useUIState();
  const { continueConversation } = useActions();

  return (
    <div>
      <div>
        {conversation.map((message: ClientMessage) => (
          <div key={message.id}>
            {message.role}: {message.display}
          </div>
        ))}
      </div>

      <div>
        <input
          type="text"
          value={input}
          onChange={event => {
            setInput(event.target.value);
          }}
        />
        <button
          onClick={async () => {
            setConversation((currentConversation: ClientMessage[]) => [
              ...currentConversation,
              { id: generateId(), role: 'user', display: input },
            ]);

            const message = await continueConversation(input);

            setConversation((currentConversation: ClientMessage[]) => [
              ...currentConversation,
              message,
            ]);
          }}
        >
          Send Message
        </button>
      </div>
    </div>
  );
}
```

## Server
`app/actions.tsx`

```typescript
'use server';

import { createAI, getMutableAIState, streamUI } from 'ai/rsc';
import { openai } from '@ai-sdk/openai';
import { ReactNode } from 'react';
import { z } from 'zod';
import { generateId } from 'ai';

export interface ServerMessage {
  role: 'user' | 'assistant';
  content: string;
}

export interface ClientMessage {
  id: string;
  role: 'user' | 'assistant';
  display: ReactNode;
}

export async function continueConversation(
  input: string,
): Promise<ClientMessage> {
  'use server';

  const history = getMutableAIState();

  const result = await streamUI({
    model: openai('gpt-3.5-turbo'),
    messages: [...history.get(), { role: 'user', content: input }],
    text: ({ content, done }) => {
      if (done) {
        history.done((messages: ServerMessage[]) => [
          ...messages,
          { role: 'assistant', content },
        ]);
      }

      return <div>{content}</div>;
    },
    tools: {
      deploy: {
        description: 'Deploy repository to Vercel',
        parameters: z.object({
          repositoryName: z
            .string()
            .describe('The name of the repository, example: vercel/ai-chatbot'),
        }),
        generate: async function* ({ repositoryName }) {
          yield <div>Cloning repository {repositoryName}...</div>;
          await new Promise(resolve => setTimeout(resolve, 3000));
          yield <div>Building repository {repositoryName}...</div>;
          await new Promise(resolve => setTimeout(resolve, 2000));
          return <div>{repositoryName} deployed!</div>;
        },
      },
    },
  });

  return {
    id: generateId(),
    role: 'assistant',
    display: result.value,
  };
}

export const AI = createAI<ServerMessage[], ClientMessage[]>({
  actions: {
    continueConversation,
  },
  initialAIState: [],
  initialUIState: [],
});

# Recording Token Usage

When you're streaming structured data with `streamUI`, you may want to record the token usage for billing purposes.

## `onFinish` Callback

You can use the `onFinish` callback to record token usage. It is called when the stream is finished.

```typescript
'use client';

import { useState } from 'react';
import { ClientMessage } from './actions';
import { useActions, useUIState } from 'ai/rsc';
import { generateId } from 'ai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Home() {
  const [input, setInput] = useState<string>('');
  const [conversation, setConversation] = useUIState();
  const { continueConversation } = useActions();

  return (
    <div>
      <div>
        {conversation.map((message: ClientMessage) => (
          <div key={message.id}>
            {message.role}: {message.display}
          </div>
        ))}
      </div>

      <div>
        <input
          type="text"
          value={input}
          onChange={event => {
            setInput(event.target.value);
          }}
        />
        <button
          onClick={async () => {
            setConversation((currentConversation: ClientMessage[]) => [
              ...currentConversation,
              { id: generateId(), role: 'user', display: input },
            ]);

            const message = await continueConversation(input);

            setConversation((currentConversation: ClientMessage[]) => [
              ...currentConversation,
              message,
            ]);
          }}
        >
          Send Message
        </button>
      </div>
    </div>
  );
}
```

## Server

```typescript
'use server';

import { createAI, getMutableAIState, streamUI } from 'ai/rsc';
import { openai } from '@ai-sdk/openai';
import { ReactNode } from 'react';
import { z } from 'zod';
import { generateId } from 'ai';

export interface ServerMessage {
  role: 'user' | 'assistant';
  content: string;
}

export interface ClientMessage {
  id: string;
  role: 'user' | 'assistant';
  display: ReactNode;
}

export async function continueConversation(
  input: string,
): Promise<ClientMessage> {
  'use server';

  const history = getMutableAIState();

  const result = await streamUI({
    model: openai('gpt-3.5-turbo'),
    messages: [...history.get(), { role: 'user', content: input }],
    text: ({ content, done }) => {
      if (done) {
        history.done((messages: ServerMessage[]) => [
          ...messages,
          { role: 'assistant', content },
        ]);
      }

      return <div>{content}</div>;
    },
    tools: {
      deploy: {
        description: 'Deploy repository to vercel',
        parameters: z.object({
          repositoryName: z
            .string()
            .describe('The name of the repository, example: vercel/ai-chatbot'),
        }),
        generate: async function* ({ repositoryName }) {
          yield <div>Cloning repository {repositoryName}...</div>;
          await new Promise(resolve => setTimeout(resolve, 3000));
          yield <div>Building repository {repositoryName}...</div>;
          await new Promise(resolve => setTimeout(resolve, 2000));
          return <div>{repositoryName} deployed!</div>;
        },
      },
    },
    onFinish: ({ usage }) => {
      const { promptTokens, completionTokens, totalTokens } = usage;
      // your own logic, e.g. for saving the chat history or recording usage
      console.log('Prompt tokens:', promptTokens);
      console.log('Completion tokens:', completionTokens);
      console.log('Total tokens:', totalTokens);
    },
  });

  return {
    id: generateId(),
    role: 'assistant',
    display: result.value,
  };
}

export const AI = createAI<ServerMessage[], ClientMessage[]>({
  actions: {
    continueConversation,
  },
  initialAIState: [],
  initialUIState: [],
});
```

# API Reference

## AI SDK Core

**AI SDK Core** is a set of functions that allow you to interact with language models and other AI models. These functions are designed to be easy-to-use and flexible, allowing you to generate text, structured data, and embeddings from language models and other AI models.

AI SDK Core contains the following main functions:

1. `generateText()`
   Generate text and call tools from a language model.
2. `streamText()`
   Stream text and call tools from a language model.
3. `generateObject()`
   Generate structured data from a language model.
4. `streamObject()`
   Stream structured data from a language model.
5. `embed()`
   Generate an embedding for a single value using an embedding model.
6. `embedMany()`
   Generate embeddings for several values using an embedding model (batch embedding).

It also contains the following helper functions:

- `tool()`
  Type inference helper function for tools.
- `jsonSchema()`
  Creates AI SDK compatible JSON schema objects.
- `createProviderRegistry()`
  Creates a registry for using models from multiple providers.
- `cosineSimilarity()`
  Calculates the cosine similarity between two vectors, e.g. embeddings.

## `generateText()`

Generates text and calls tools for a given prompt using a language model.

It is ideal for non-interactive use cases such as automation tasks where you need to write text (e.g. drafting email or summarizing web pages) and for agents that use tools.

```javascript
import { openai } from '@ai-sdk/openai';
import { generateText } from 'ai';

const { text } = await generateText({
  model: openai('gpt-4-turbo'),
  prompt: 'Invent a new holiday and describe its traditions.'
});

console.log(text);
```

## Import

```javascript
import { generateText } from "ai"
```

## API Signature

### Parameters

`model`: `LanguageModel`
The language model to use. Example: `openai('gpt-4-turbo')`

`system`: `string`
The system prompt to use that specifies the behavior of the model.

`prompt`: `string`
The input prompt to generate the text from.

`messages`: `Array<CoreSystemMessage | CoreUserMessage | CoreAssistantMessage | CoreToolMessage>`
A list of messages that represent a conversation.

#### `CoreSystemMessage`

`role`: `'system'`
The role for the system message.

`content`: `string`
The content of the message.

#### `CoreUserMessage`

`role`: `'user'`
The role for the user message.

`content`: `string | Array<TextPart | ImagePart>`
The content of the message.

##### `TextPart`

`type`: `'text'`
The type of the message part.

`text`: `string`
The text content of the message part.

##### `ImagePart`

`type`: `'image'`
The type of the message part.

`image`: `string | Uint8Array | Buffer | ArrayBuffer | URL`
The image content of the message part. String are either base64 encoded content, base64 data URLs, or http(s) URLs.

#### `CoreAssistantMessage`

`role`: `'assistant'`
The role for the assistant message.

`content`: `string | Array<TextPart | ToolCallPart>`
The content of the message.

##### `TextPart`

`type`: `'text'`
The type of the message part.

`text`: `string`
The text content of the message part.

##### `ToolCallPart`

`type`: `'tool-call'`
The type of the message part.

`toolCallId`: `string`
The id of the tool call.

`toolName`: `string`
The name of the tool, which typically would be the name of the function.

`args`: `object based on zod schema`
Parameters generated by the model to be used by the tool.

#### `CoreToolMessage`

`role`: `'tool'`
The role for the assistant message.

`content`: `Array<ToolResultPart>`
The content of the message.

##### `ToolResultPart`

`type`: `'tool-result'`
The type of the message part.

`toolCallId`: `string`
The id of the tool call the result corresponds to.

`toolName`: `string`
The name of the tool the result corresponds to.

`result`: `unknown`
The result returned by the tool after execution.

`isError?`: `boolean`
Whether the result is an error or an error message.

### `tools`

`Record<string, CoreTool>`
Tools that are accessible to and can be called by the model. The model needs to support calling tools.

#### `CoreTool`

`description?`: `string`
Information about the purpose of the tool including details on how and when it can be used by the model.

`parameters`: `Zod Schema | JSON Schema`
The schema of the input that the tool expects. The language model will use this to generate the input. It is also used to validate the output of the language model. Use descriptions to make the input understandable for the language model. You can either pass in a Zod schema or a JSON schema (using the `jsonSchema` function).

`execute?`: `async (parameters) => any`
An async function that is called with the arguments from the tool call and produces a result. If not provided, the tool will not be executed automatically.

`toolChoice?`: `"auto" | "none" | "required" | { "type": "tool", "toolName": string }`
The tool choice setting. It specifies how tools are selected for execution. The default is "auto". "none" disables tool execution. "required" requires tools to be executed. `{ "type": "tool", "toolName": string }` specifies a specific tool to execute.

`maxTokens?`: `number`
Maximum number of tokens to generate.

`temperature?`: `number`
Temperature setting. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.

`topP?`: `number`
Nucleus sampling. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.

`topK?`: `number`
Only sample from the top K options for each subsequent token. Used to remove "long tail" low probability responses. Recommended for advanced use cases only. You usually only need to use temperature.

`presencePenalty?`: `number`
Presence penalty setting. It affects the likelihood of the model to repeat information that is already in the prompt. The value is passed through to the provider. The range depends on the provider and model.

`frequencyPenalty?`: `number`
Frequency penalty setting. It affects the likelihood of the model to repeatedly use the same words or phrases. The value is passed through to the provider. The range depends on the provider and model.

`stopSequences?`: `string[]`
Sequences that will stop the generation of the text. If the model generates any of these sequences, it will stop generating further text.

`seed?`: `number`
The seed (integer) to use for random sampling. If set and supported by the model, calls will generate deterministic results.

`maxRetries?`: `number`
Maximum number of retries. Set to 0 to disable retries. Default: 2.

`abortSignal?`: `AbortSignal`
An optional abort signal that can be used to cancel the call.

`headers?`: `Record<string, string>`
Additional HTTP headers to be sent with the request. Only applicable for HTTP-based providers.

`maxAutomaticRoundtrips?`: `number`
Maximum number of automatic roundtrips for tool calls. An automatic tool call roundtrip is another LLM call with the tool call results when all tool calls of the last assistant message have results. A maximum number is required to prevent infinite loops in the case of misconfigured tools. By default, it is set to 0, which will disable the feature.

`experimental_telemetry?`: `TelemetrySettings`
Telemetry configuration. Experimental feature.

#### `TelemetrySettings`

`isEnabled?`: `boolean`
Enable or disable telemetry. Disabled by default while experimental.

`recordInputs?`: `boolean`
Enable or disable input recording. Enabled by default.

`recordOutputs?`: `boolean`
Enable or disable output recording. Enabled by default.

`functionId?`: `string`
Identifier for this function. Used to group telemetry data by function.

`metadata?`: `Record<string, string | number | boolean | Array<null | undefined | string> | Array<null | undefined | number> | Array<null | undefined | boolean>>`
Additional information to include in the telemetry data.

### Returns

`text`: `string`
The generated text by the model.

`toolCalls`: `array`
A list of tool calls made by the model.

`toolResults`: `array`
A list of tool results returned as responses to earlier tool calls.

`finishReason`: `'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown'`
The reason the model finished generating the text.

`usage`: `CompletionTokenUsage`
The token usage of the generated text.

#### `CompletionTokenUsage`

`promptTokens`: `number`
The total number of tokens in the prompt.

`completionTokens`: `number`
The total number of tokens in the completion.

`totalTokens`: `number`
The total number of tokens generated.

`rawResponse`: `RawResponse`
Optional raw response data.

#### `RawResponse`

`headers`: `Record<string, string>`
Response headers.

`warnings`: `Warning[] | undefined`
Warnings from the model provider (e.g. unsupported settings).

`responseMessages`: `Array<CoreAssistantMessage | CoreToolMessage>`
The response messages that were generated during the call. It consists of an assistant message, potentially containing tool calls. When there are tool results, there is an additional tool message with the tool results that are available. If there are tools that do not have execute functions, they are not included in the tool results and need to be added separately.

`roundtrips`: `Array<Roundtrip>`
Response information for every roundtrip. You can use this to get information about intermediate steps, such as the tool calls or the response headers.

#### `Roundtrip`

`text`: `string`
The generated text by the model.

`toolCalls`: `array`
A list of tool calls made by the model.

`toolResults`: `array`
A list of tool results returned as responses to earlier tool calls.

`finishReason`: `'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown'`
The reason the model finished generating the text.

`usage`: `CompletionTokenUsage`
The token usage of the generated text.

`rawResponse`: `RawResponse`
Optional raw response data.

# AI SDK Core

## `streamText`
`streamText()`

Streams text generations from a language model.

You can use the `streamText` function for interactive use cases such as chat bots and other real-time applications. You can also generate UI components with tools.

```javascript
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

## Import
```javascript
import { streamText } from "ai"
```

## API Signature

### Parameters
- `model`: `LanguageModel` - The language model to use. Example: `openai('gpt-4-turbo')`
- `system`: `string` - The system prompt to use that specifies the behavior of the model.
- `prompt`: `string` - The input prompt to generate the text from.
- `messages`: `Array<CoreSystemMessage | CoreUserMessage | CoreAssistantMessage | CoreToolMessage>` - A list of messages that represent a conversation.

#### `CoreSystemMessage`
- `role`: `'system'` - The role for the system message.
- `content`: `string` - The content of the message.

#### `CoreUserMessage`
- `role`: `'user'` - The role for the user message.
- `content`: `string | Array<TextPart | ImagePart>` - The content of the message.

##### `TextPart`
- `type`: `'text'` - The type of the message part.
- `text`: `string` - The text content of the message part.

##### `ImagePart`
- `type`: `'image'` - The type of the message part.
- `image`: `string | Uint8Array | Buffer | ArrayBuffer | URL` - The image content of the message part. Strings are either base64 encoded content, base64 data URLs, or http(s) URLs.

#### `CoreAssistantMessage`
- `role`: `'assistant'` - The role for the assistant message.
- `content`: `string | Array<TextPart | ToolCallPart>` - The content of the message.

##### `TextPart`
- `type`: `'text'` - The type of the message part.
- `text`: `string` - The text content of the message part.

##### `ToolCallPart`
- `type`: `'tool-call'` - The type of the message part.
- `toolCallId`: `string` - The id of the tool call.
- `toolName`: `string` - The name of the tool, which typically would be the name of the function.
- `args`: `object based on zod schema` - Parameters generated by the model to be used by the tool.

#### `CoreToolMessage`
- `role`: `'tool'` - The role for the assistant message.
- `content`: `Array<ToolResultPart>` - The content of the message.

##### `ToolResultPart`
- `type`: `'tool-result'` - The type of the message part.
- `toolCallId`: `string` - The id of the tool call the result corresponds to.
- `toolName`: `string` - The name of the tool the result corresponds to.
- `result`: `unknown` - The result returned by the tool after execution.
- `isError?`: `boolean` - Whether the result is an error or an error message.

#### `tools`
`Record<string, CoreTool>` - Tools that are accessible to and can be called by the model. The model needs to support calling tools.

##### `CoreTool`
- `description?`: `string` - Information about the purpose of the tool including details on how and when it can be used by the model.
- `parameters`: `Zod Schema | JSON Schema` - The schema of the input that the tool expects. The language model will use this to generate the input. It is also used to validate the output of the language model. Use descriptions to make the input understandable for the language model. You can either pass in a Zod schema or a JSON schema (using the `jsonSchema` function).
- `execute?`: `async (parameters) => any` - An async function that is called with the arguments from the tool call and produces a result. If not provided, the tool will not be executed automatically.
- `toolChoice?`: `"auto" | "none" | "required" | { "type": "tool", "toolName": string }` - The tool choice setting. It specifies how tools are selected for execution. The default is "auto". "none" disables tool execution. "required" requires tools to be executed. `{ "type": "tool", "toolName": string }` specifies a specific tool to execute.
- `maxTokens?`: `number` - Maximum number of tokens to generate.
- `temperature?`: `number` - Temperature setting. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.
- `topP?`: `number` - Nucleus sampling. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.
- `topK?`: `number` - Only sample from the top K options for each subsequent token. Used to remove "long tail" low probability responses. Recommended for advanced use cases only. You usually only need to use temperature.
- `presencePenalty?`: `number` - Presence penalty setting. It affects the likelihood of the model to repeat information that is already in the prompt. The value is passed through to the provider. The range depends on the provider and model.
- `frequencyPenalty?`: `number` - Frequency penalty setting. It affects the likelihood of the model to repeatedly use the same words or phrases. The value is passed through to the provider. The range depends on the provider and model.
- `stopSequences?`: `string[]` - Sequences that will stop the generation of the text. If the model generates any of these sequences, it will stop generating further text.
- `seed?`: `number` - The seed (integer) to use for random sampling. If set and supported by the model, calls will generate deterministic results.
- `maxRetries?`: `number` - Maximum number of retries. Set to 0 to disable retries. Default: 2.
- `abortSignal?`: `AbortSignal` - An optional abort signal that can be used to cancel the call.
- `headers?`: `Record<string, string>` - Additional HTTP headers to be sent with the request. Only applicable for HTTP-based providers.
- `experimental_telemetry?`: `TelemetrySettings` - Telemetry configuration. Experimental feature.

##### `TelemetrySettings`
- `isEnabled?`: `boolean` - Enable or disable telemetry. Disabled by default while experimental.
- `recordInputs?`: `boolean` - Enable or disable input recording. Enabled by default.
- `recordOutputs?`: `boolean` - Enable or disable output recording. Enabled by default.
- `functionId?`: `string` - Identifier for this function. Used to group telemetry data by function.
- `metadata?`: `Record<string, string | number | boolean | Array<null | undefined | string> | Array<null | undefined | number> | Array<null | undefined | boolean>>` - Additional information to include in the telemetry data.

##### `experimental_toolCallStreaming?`: `boolean` - Enable streaming of tool call deltas as they are generated. Disabled by default.

##### `onChunk?`: `(event: OnChunkResult) => Promise<void> |void` - Callback that is called for each chunk of the stream. The stream processing will pause until the callback promise is resolved.

###### `OnChunkResult`
- `chunk`: `TextStreamPart` - The chunk of the stream.

###### `TextStreamPart`
- `type`: `'text-delta'` - The type to identify the object as text delta.
- `textDelta`: `string` - The text delta.

###### `TextStreamPart`
- `type`: `'tool-call'` - The type to identify the object as tool call.
- `toolCallId`: `string` - The id of the tool call.
- `toolName`: `string` - The name of the tool, which typically would be the name of the function.
- `args`: `object based on zod schema` - Parameters generated by the model to be used by the tool.

###### `TextStreamPart`
- `type`: `'tool-call-streaming-start'` - Indicates the start of a tool call streaming. Only available when streaming tool calls.
- `toolCallId`: `string` - The id of the tool call.
- `toolName`: `string` - The name of the tool, which typically would be the name of the function.

###### `TextStreamPart`
- `type`: `'tool-call-delta'` - The type to identify the object as tool call delta. Only available when streaming tool calls.
- `toolCallId`: `string` - The id of the tool call.
- `toolName`: `string` - The name of the tool, which typically would be the name of the function.
- `argsTextDelta`: `string` - The text delta of the tool call arguments.

###### `TextStreamPart`
- `type`: `'tool-result'` - The type to identify the object as tool result.
- `toolCallId`: `string` - The id of the tool call.
- `toolName`: `string` - The name of the tool, which typically would be the name of the function.
- `args`: `object based on zod schema` - Parameters generated by the model to be used by the tool.
- `result`: `any` - The result returned by the tool after execution has completed.

##### `onFinish?`: `(result: OnFinishResult) => Promise<void> | void` - Callback that is called when the LLM response and all request tool executions (for tools that have an `execute` function) are finished.

## `finishReason`
- `"stop"` | `"length"` | `"content-filter"` | `"tool-calls"` | `"error"` | `"other"` | `"unknown"`
The reason the model finished generating the text.

## `usage`
`TokenUsage`
The token usage of the generated text.

### `TokenUsage`
- `promptTokens`: `number`
  The total number of tokens in the prompt.
- `completionTokens`: `number`
  The total number of tokens in the completion.
- `totalTokens`: `number`
  The total number of tokens generated.

## `text`
`string`
The full text that has been generated.

## `toolCalls`
`ToolCall[]`
The tool calls that have been executed.

## `toolResults`
`ToolResult[]`
The tool results that have been generated.

## `warnings`
`Warning[] | undefined`
Warnings from the model provider (e.g., unsupported settings).

## `rawResponse`
`RawResponse`
Optional raw response data.

### `RawResponse`
- `headers`: `Record<string, string>`
  Response headers.

## Returns

### `finishReason`
`Promise<'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown'>`
The reason why the generation finished. Resolved when the response is finished.

### `usage`
`Promise<CompletionTokenUsage>`
The token usage of the generated text. Resolved when the response is finished.

#### `CompletionTokenUsage`
- `promptTokens`: `number`
  The total number of tokens in the prompt.
- `completionTokens`: `number`
  The total number of tokens in the completion.
- `totalTokens`: `number`
  The total number of tokens generated.

### `text`
`Promise<string>`
The full text that has been generated. Resolved when the response is finished.

### `toolCalls`
`Promise<ToolCall[]>`
The tool calls that have been executed. Resolved when the response is finished.

### `toolResults`
`Promise<ToolResult[]>`
The tool results that have been generated. Resolved when the all tool executions are finished.

### `rawResponse`
`RawResponse`
Optional raw response data.

### `headers`
`Record<string, string>`
Response headers.

### `warnings`
`Warning[] | undefined`
Warnings from the model provider (e.g., unsupported settings).

### `textStream`
`AsyncIterable<string> & ReadableStream<string>`
A text stream that returns only the generated text deltas. You can use it as either an `AsyncIterable` or a `ReadableStream`. When an error occurs, the stream will throw the error.

### `fullStream`
`AsyncIterable<TextStreamPart> & ReadableStream<TextStreamPart>`
A stream with all events, including text deltas, tool calls, tool results, and errors. You can use it as either an `AsyncIterable` or a `ReadableStream`. Only errors that stop the stream, such as network errors, are thrown.

#### `TextStreamPart`
- `type`: `'text-delta'`
  The type to identify the object as text delta.
- `textDelta`: `string`
  The text delta.

#### `TextStreamPart`
- `type`: `'tool-call'`
  The type to identify the object as tool call.
- `toolCallId`: `string`
  The id of the tool call.
- `toolName`: `string`
  The name of the tool, which typically would be the name of the function.
- `args`: `object based on zod schema`
  Parameters generated by the model to be used by the tool.

#### `TextStreamPart`
- `type`: `'tool-call-streaming-start'`
  Indicates the start of a tool call streaming. Only available when streaming tool calls.
- `toolCallId`: `string`
  The id of the tool call.
- `toolName`: `string`
  The name of the tool, which typically would be the name of the function.

#### `TextStreamPart`
- `type`: `'tool-call-delta'`
  The type to identify the object as tool call delta. Only available when streaming tool calls.
- `toolCallId`: `string`
  The id of the tool call.
- `toolName`: `string`
  The name of the tool, which typically would be the name of the function.
- `argsTextDelta`: `string`
  The text delta of the tool call arguments.

#### `TextStreamPart`
- `type`: `'tool-result'`
  The type to identify the object as tool result.
- `toolCallId`: `string`
  The id of the tool call.
- `toolName`: `string`
  The name of the tool, which typically would be the name of the function.
- `args`: `object based on zod schema`
  Parameters generated by the model to be used by the tool.
- `result`: `any`
  The result returned by the tool after execution has completed.

#### `TextStreamPart`
- `type`: `'error'`
  The type to identify the object as error.
- `error`: `Error`
  Describes the error that may have occurred during execution.

#### `TextStreamPart`
- `type`: `'finish'`
  The type to identify the object as finish.
- `finishReason`: `'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown'`
  The reason the model finished generating the text.
- `usage`: `TokenUsage`
  The token usage of the generated text.

### `pipeDataStreamToResponse`
`(response: ServerResponse, init?: { headers?: Record<string, string>; status?: number }) => void`
Writes stream data output to a Node.js response-like object. It sets a `Content-Type` header to `text/plain; charset=utf-8` and writes each stream data part as a separate chunk.

### `pipeTextStreamToResponse`
`(response: ServerResponse, init?: { headers?: Record<string, string>; status?: number }) => void`
Writes text delta output to a Node.js response-like object. It sets a `Content-Type` header to `text/plain; charset=utf-8` and writes each text delta as a separate chunk.

### `toDataStreamResponse`
`(options?: toDataStreamOptions) => Response`
Converts the result to a streamed response object with a stream data part stream. It can be used with the `useChat` and `useCompletion` hooks.

#### `toDataStreamOptions`
- `init`: `ResponseInit`
  The response init options.

##### `ResponseInit`
- `status`: `number`
  The response status code.
- `headers`: `Record<string, string>`
  The response headers.
- `data`: `StreamData`
  The stream data object.

### `getErrorMessage`
`(error: unknown) => string`
A function to get the error message from the error object. By default, all errors are masked as "" for safety reasons.

### `toTextStreamResponse`
`(init?: ResponseInit) => Response`
Creates a simple text stream response. Each text delta is encoded as UTF-8 and sent as a separate chunk. Non-text-delta events are ignored.

# `generateObject()`

Generates a typed, structured object for a given prompt and schema using a language model.

It can be used to force the language model to return structured data, e.g. for information extraction, synthetic data generation, or classification tasks.

```javascript
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

## Import

```javascript
import { generateObject } from "ai"
```

## API Signature

### Parameters

- `model`: `LanguageModel` - The language model to use. Example: `openai('gpt-4-turbo')`
- `mode`: `'auto' | 'json' | 'tool'` - The mode to use for object generation. Not every model supports all modes. Defaults to `'auto'`.
- `schema`: `Zod Schema | JSON Schema` - The schema that describes the shape of the object to generate. It is sent to the model to generate the object and used to validate the output. You can either pass in a Zod schema or a JSON schema (using the `jsonSchema` function).
- `schemaName`: `string | undefined` - Optional name of the output that should be generated. Used by some providers for additional LLM guidance, e.g. via tool or schema name.
- `schemaDescription`: `string | undefined` - Optional description of the output that should be generated. Used by some providers for additional LLM guidance, e.g. via tool or schema name.
- `system`: `string` - The system prompt to use that specifies the behavior of the model.
- `prompt`: `string` - The input prompt to generate the text from.
- `messages`: `Array<CoreSystemMessage | CoreUserMessage | CoreAssistantMessage | CoreToolMessage>` - A list of messages that represent a conversation.

### Returns

- `object`: `based on the schema` - The generated object, validated by the schema (if it supports validation).
- `finishReason`: `'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown'` - The reason the model finished generating the text.
- `usage`: `CompletionTokenUsage` - The token usage of the generated text.
- `rawResponse`: `RawResponse` - Optional raw response data.

# `streamObject()`

Streams a typed, structured object for a given prompt and schema using a language model.

It can be used to force the language model to return structured data, e.g. for information extraction, synthetic data generation, or classification tasks.

```javascript
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

## Import

```javascript
import { streamObject } from "ai"
```

## API Signature

### Parameters

**`model`**: `LanguageModel`
The language model to use. Example: `openai('gpt-4-turbo')`

**`mode`**: `'auto' | 'json' | 'tool'`
The mode to use for object generation. Not every model supports all modes. Defaults to `'auto'`.

**`schema`**: `Zod Schema | JSON Schema`
The schema that describes the shape of the object to generate. It is sent to the model to generate the object and used to validate the output. You can either pass in a Zod schema or a JSON schema (using the `jsonSchema` function).

**`schemaName`**: `string | undefined`
Optional name of the output that should be generated. Used by some providers for additional LLM guidance, e.g. via tool or schema name.

**`schemaDescription`**: `string | undefined`
Optional description of the output that should be generated. Used by some providers for additional LLM guidance, e.g. via tool or schema name.

**`system`**: `string`
The system prompt to use that specifies the behavior of the model.

**`prompt`**: `string`
The input prompt to generate the text from.

**`messages`**: `Array<CoreSystemMessage | CoreUserMessage | CoreAssistantMessage | CoreToolMessage>`
A list of messages that represent a conversation.

**`maxTokens`**: `number`
Maximum number of tokens to generate.

**`temperature`**: `number`
Temperature setting. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.

**`topP`**: `number`
Nucleus sampling. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.

**`topK`**: `number`
Only sample from the top K options for each subsequent token. Used to remove "long tail" low probability responses. Recommended for advanced use cases only. You usually only need to use temperature.

**`presencePenalty`**: `number`
Presence penalty setting. It affects the likelihood of the model to repeat information that is already in the prompt. The value is passed through to the provider. The range depends on the provider and model.

**`frequencyPenalty`**: `number`
Frequency penalty setting. It affects the likelihood of the model to repeatedly use the same words or phrases. The value is passed through to the provider. The range depends on the provider and model.

**`seed`**: `number`
The seed (integer) to use for random sampling. If set and supported by the model, calls will generate deterministic results.

**`maxRetries`**: `number`
Maximum number of retries. Set to 0 to disable retries. Default: 2.

**`abortSignal`**: `AbortSignal`
An optional abort signal that can be used to cancel the call.

**`headers`**: `Record<string, string>`
Additional HTTP headers to be sent with the request. Only applicable for HTTP-based providers.

**`experimental_telemetry`**: `TelemetrySettings`
Telemetry configuration. Experimental feature.

**`onFinish`**: `(result: OnFinishResult) => void`
Callback that is called when the LLM response has finished.

## `tool()`

`tool()` is a helper function that infers the tool parameters for its `execute` method.

It does not have any runtime behavior, but it helps TypeScript infer the types of the parameters for the `execute` method.

Without this helper function, TypeScript is unable to connect the `parameters` property to the `execute` method, and the argument types of `execute` cannot be inferred.

```typescript
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

## Import

```typescript
import { tool } from "ai"
```

## API Signature

### Parameters

`tool`:
`CoreTool`
The tool definition.

`CoreTool`:
`description?`:
*string*
Information about the purpose of the tool including details on how and when it can be used by the model.
`parameters`:
*Zod Schema | JSON Schema*
The schema of the input that the tool expects. The language model will use this to generate the input. It is also used to validate the output of the language model. Use descriptions to make the input understandable for the language model. You can either pass in a Zod schema or a JSON schema (using the `jsonSchema` function).
`execute?`:
*async (parameters) => any*
An async function that is called with the arguments from the tool call and produces a result. If not provided, the tool will not be executed automatically.

### Returns

The tool that was passed in.

## `jsonSchema()`

`jsonSchema` is a helper function that creates a JSON schema object that is compatible with the AI SDK. It takes the JSON schema and an optional validation function as inputs, and can be typed.

You can use it to generate structured data and in tools.

`jsonSchema` is an alternative to using Zod schemas that provides you with flexibility in dynamic situations (e.g., when using OpenAPI definitions) or for using other validation libraries.

```typescript
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

### Import

```typescript
import { jsonSchema } from "ai"
```

### API Signature

#### Parameters

- `schema`: `JSONSchema7`
  - The JSON schema definition.
- `options`: `SchemaOptions`
  - Additional options for the JSON schema.

#### `SchemaOptions`

- `validate?`: `(value: unknown) => { success: true; value: OBJECT } | { success: false; error: Error }`
  - A function that validates the value against the JSON schema. If the value is valid, the function should return an object with a `success` property set to `true` and a `value` property set to the validated value. If the value is invalid, the function should return an object with a `success` property set to `false` and an `error` property set to the error.

#### Returns

A JSON schema object that is compatible with the AI SDK.

# CoreMessage

**CoreMessage** represents the fundamental message structure used with AI SDK Core functions. It encompasses various message types that can be used in the `messages` field of any AI SDK Core functions.

## CoreMessage Types

### CoreSystemMessage

A system message that can contain system information.

```typescript
type CoreSystemMessage = {
  role: 'system';
  content: string;
};
```

Using the "system" part of the prompt is strongly recommended to enhance resilience against prompt injection attacks and because not all providers support multiple system messages.

### CoreUserMessage

A user message that can contain text or a combination of text and images.

```typescript
type CoreUserMessage = {
  role: 'user';
  content: UserContent;
};

type UserContent = string | Array<TextPart | ImagePart>;
```

### CoreAssistantMessage

An assistant message that can contain text, tool calls, or a combination of both.

```typescript
type CoreAssistantMessage = {
  role: 'assistant';
  content: AssistantContent;
};

type AssistantContent = string | Array<TextPart | ToolCallPart>;
```

### CoreToolMessage

A tool message that contains the result of one or more tool calls.

```typescript
type CoreToolMessage = {
  role: 'tool';
  content: ToolContent;
};

type ToolContent = Array<ToolResultPart>;
```

## CoreMessage Parts

### TextPart

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

### ImagePart

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

### ToolCallPart

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

### ToolResultPart

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
# AI SDK RSC

The `ai/rsc` package is compatible with frameworks that support React Server Components.

## React Server Components (RSC)

React Server Components (RSC) allow you to write UI that can be rendered on the server and streamed to the client. RSCs enable *Server Actions*, a new way to call server-side code directly from the client just like any other function with end-to-end type-safety. This combination opens the door to a new way of building AI applications, allowing the large language model (LLM) to generate and stream UI directly from the server to the client.

## AI SDK RSC Functions

AI SDK RSC has various functions designed to help you build AI-native applications with React Server Components. These functions:

1. Provide abstractions for building Generative UI applications.
   - `streamUI`: calls a model and allows it to respond with React Server Components.
   - `useUIState`: returns the current UI state and a function to update the UI State (like React's `useState`). UI State is the visual representation of the AI state.
   - `useAIState`: returns the current AI state and a function to update the AI State (like React's `useState`). The AI state is intended to contain context and information shared with the AI model, such as system messages, function responses, and other relevant data.
   - `useActions`: provides access to your Server Actions from the client. This is particularly useful for building interfaces that require user interactions with the server.
   - `createAI`: creates a client-server context provider that can be used to wrap parts of your application tree to easily manage both UI and AI states of your application.
2. Make it simple to work with streamable values between the server and client.
   - `createStreamableValue`: creates a stream that sends values from the server to the client. The value can be any serializable data.
   - `readStreamableValue`: reads a streamable value from the client that was originally created using `createStreamableValue`.
   - `createStreamableUI`: creates a stream that sends UI from the server to the client.
   - `useStreamableValue`: accepts a streamable value created using `createStreamableValue` and returns the current value, error, and pending state.

## Streaming React Components

The RSC API allows you to stream React components from the server to the client with the `streamUI` function. This is useful when you want to go beyond raw text and stream components to the client in real-time.

Similar to AI SDK Core APIs (like `streamText` and `streamObject`), `streamUI` provides a single function to call a model and allow it to respond with React Server Components. It supports the same model interfaces as AI SDK Core APIs.

### Concepts

To give the model the ability to respond to a user's prompt with a React component, you can leverage *tools*.

Remember, tools are like programs you can give to the model, and the model can decide as and when to use based on the context of the conversation.

With the `streamUI` function, you provide tools that return React components. With the ability to stream components, the model is akin to a dynamic router that is able to understand the user's intention and display relevant UI.

At a high level, the `streamUI` works like other AI SDK Core functions: you can provide the model with a prompt or some conversation history and, optionally, some tools. If the model decides, based on the context of the conversation, to call a tool, it will generate a tool call. The `streamUI` function will then run the respective tool, returning a React component. If the model doesn't have a relevant tool to use, it will return a text generation, which will be passed to the `text` function, for you to handle (render and return as a React component).

Remember, the `streamUI` function must return a React component.

```javascript
const result = await streamUI({
  model: openai('gpt-4o'),
  prompt: 'Get the weather for San Francisco',
  text: ({ content }) => <div>{content}</div>,
  tools: {},
});
```

This example calls the `streamUI` function using OpenAI's `gpt-4o` model, passes a prompt, specifies how the model's plain text response (`content`) should be rendered, and then provides an empty object for tools. Even though this example does not define any tools, it will stream the model's response as a div rather than plain text.

### Adding A Tool

Using tools with `streamUI` is similar to how you use tools with `generateText` and `streamText`. A tool is an object that has:

- `description`: a string telling the model what the tool does and when to use it
- `parameters`: a Zod schema describing what the tool needs in order to run
- `generate`: an asynchronous function that will be run if the model calls the tool. This must return a React component

Let's expand the previous example to add a tool.

```javascript
const result = await streamUI({
  model: openai('gpt-4o'),
  prompt: 'Get the weather for San Francisco',
  text: ({ content }) => <div>{content}</div>,
  tools: {
    getWeather: {
      description: 'Get the weather for a location',
      parameters: z.object({ location: z.string() }),
      generate: async function* ({ location }) {
        yield <LoadingComponent />;
        const weather = await getWeather(location);
        return <WeatherComponent weather={weather} location={location} />;
      },
    },
  },
});
```

This tool would be run if the user asks for the weather for their location. If the user hasn't specified a location, the model will ask for it before calling the tool. When the model calls the tool, the `generate` function will initially return a `LoadingComponent`. This component will show until the awaited call to `getWeather` is resolved, at which point, the model will stream the `<WeatherComponent />` to the user.

Note: This example uses a generator function (`function*`), which allows you to pause its execution and return a value, then resume from where it left off on the next call. This is useful for handling data streams, as you can fetch and return data from an asynchronous source like an API, then resume the function to fetch the next chunk when needed. By yielding values one at a time, generator functions enable efficient processing of streaming data without blocking the main thread.

## Using `streamUI` with Next.js

Let's see how you can use the example above in a Next.js application.

To use `streamUI` in a Next.js application, you will need two things:

1. A Server Action (where you will call `streamUI`)
2. A page to call the Server Action and render the resulting components

### Step 1: Create a Server Action

Server Actions are server-side functions that you can call directly from the frontend. For more info, see the documentation.

Create a Server Action at `app/actions.tsx` and add the following code:

```typescript
'use server';

import { streamUI } from 'ai/rsc';
import { openai } from '@ai-sdk/openai';
import { z } from 'zod';

const LoadingComponent = () => (
  <div className="animate-pulse p-4">getting weather...</div>
);

const getWeather = async (location: string) => {
  await new Promise(resolve => setTimeout(resolve, 2000));
  return '82°F️ ☀️';
};

interface WeatherProps {
  location: string;
  weather: string;
}

const WeatherComponent = (props: WeatherProps) => (
  <div className="border border-neutral-200 p-4 rounded-lg max-w-fit">
    The weather in {props.location} is {props.weather}
  </div>
);

export async function streamComponent() {
  const result = await streamUI({
    model: openai('gpt-4o'),
    prompt: 'Get the weather for San Francisco',
    text: ({ content }) => <div>{content}</div>,
    tools: {
      getWeather: {
        description: 'Get the weather for a location',
        parameters: z.object({
          location: z.string(),
        }),
        generate: async function* ({ location }) {
          yield <LoadingComponent />;
          const weather = await getWeather(location);
          return <WeatherComponent weather={weather} location={location} />;
        },
      },
    },
  });

  return result.value;
}
```

The `getWeather` tool should look familiar as it is identical to the example in the previous section. In order for this tool to work:

1. First, define a `LoadingComponent`, which renders a pulsing div that will show some loading text.
2. Next, define a `getWeather` function that will timeout for 2 seconds (to simulate fetching the weather externally) before returning the "weather" for a location. Note: you could run any asynchronous TypeScript code here.
3. Finally, define a `WeatherComponent` which takes in `location` and `weather` as props, which are then rendered within a div.

Your Server Action is an asynchronous function called `streamComponent` that takes no inputs, and returns a `ReactNode`. Within the action, you call the `streamUI` function, specifying the model (`gpt-4o`), the prompt, the component that should be rendered if the model chooses to return text, and finally, your `getWeather` tool. Last but not least, you return the resulting component generated by the model with `result.value`.

To call this Server Action and display the resulting React Component, you will need a page.

### Step 2: Create a Page

Create or update your root page (`app/page.tsx`) with the following code:

```typescript
'use client';

import { useState } from 'react';
import { Button } from '@/components/ui/button';
import { streamComponent } from './actions';

export default function Page() {
  const [component, setComponent] = useState<React.ReactNode>();

  return (
    <div>
      <form
        onSubmit={async e => {
          e.preventDefault();
          setComponent(await streamComponent());
        }}
      >
        <Button>Stream Component</Button>
      </form>
      <div>{component}</div>
    </div>
  );
}
```

This page is first marked as a client component with the `'use client';` directive given it will be using hooks and interactivity. On the page, you render a form. When that form is submitted, you call the `streamComponent` action created in the previous step (just like any other function). The `streamComponent` action returns a `ReactNode` that you can then render on the page using React state (`setComponent`).

### Going beyond a single prompt

You can now allow the model to respond to your prompt with a React component. However, this example is limited to a static prompt that is set within your Server Action. You could make this example interactive by turning it into a chatbot.

# Managing Generative UI State

State is an essential part of any application. State is particularly important in AI applications as it is passed to large language models (LLMs) on each request to ensure they have the necessary context to produce a great generation. Traditional chatbots are text-based and have a structure that mirrors that of any chat application.

For example, in a chatbot, state is an array of messages where each message has:

1. `id`: a unique identifier
2. `role`: who sent the message (user/assistant/system/tool)
3. `content`: the content of the message

This state can be rendered in the UI and sent to the model without any modifications.

With Generative UI, the model can now return a React component, rather than a plain text message. The client can render that component without issue, but that state can't be sent back to the model because React components aren't serialisable. So, what can you do?

The solution is to split the state in two, where one (AI State) becomes a proxy for the other (UI State).

One way to understand this concept is through a Lego analogy. Imagine a 10,000 piece Lego model that, once built, cannot be easily transported because it is fragile. By taking the model apart, it can be easily transported, and then rebuilt following the steps outlined in the instructions pamphlet. In this way, the instructions pamphlet is a proxy to the physical structure. Similarly, AI State provides a serialisable (JSON) representation of your UI that can be passed back and forth to the model.

## What is AI and UI State?

The RSC API simplifies how you manage AI State and UI State, providing a robust way to keep them in sync between your database, server and client.

### AI State

AI State refers to the state of your application in a serialisable format that will be used on the server and can be shared with the language model.

For a chat app, the AI State is the conversation history (messages) between the user and the assistant. Components generated by the model would be represented in a JSON format as a tool alongside any necessary props. AI State can also be used to store other values and meta information such as `createdAt` for each message and `chatId` for each conversation. The LLM reads this history so it can generate the next message. This state serves as the source of truth for the current application state.

*Note: AI state can be accessed/modified from both the server and the client.*

### UI State

UI State refers to the state of your application that is rendered on the client. It is a fully client-side state (similar to `useState`) that can store anything from JavaScript values to React elements. UI state is a list of actual UI elements that are rendered on the client.

*Note: UI State can only be accessed client-side.*

## Using AI / UI State

### Creating the AI Context

AI SDK RSC simplifies managing AI and UI state across your application by providing several hooks. These hooks are powered by React context under the hood.

Notably, this means you do not have to pass the message history to the server explicitly for each request. You also can access and update your application state in any child component of the context provider. As you begin building multistep generative interfaces, this will be particularly helpful.

To use `ai/rsc` to manage AI and UI State in your application, you can create a React context using `createAI`:

```typescript
// app/actions.tsx

// Define the AI state and UI state types
export type ServerMessage = {
  role: 'user' | 'assistant';
  content: string;
};

export type ClientMessage = {
  id: string;
  role: 'user' | 'assistant';
  display: ReactNode;
};

export const sendMessage = async (input: string): Promise<ClientMessage> => {
  "use server"
  ...
}

export type AIState = ServerMessage[];
export type UIState = ClientMessage[];

// Create the AI provider with the initial states and allowed actions
export const AI = createAI<AIState, UIState>({
  initialAIState: [],
  initialUIState: [],
  actions: {
    sendMessage,
  },
});
```

You must pass Server Actions to the `actions` object.

In this example, you define types for AI State and UI State, respectively.

Next, wrap your application with your newly created context. With that, you can get and set AI and UI State across your entire application.

```typescript
// app/layout.tsx

import { type ReactNode } from 'react';
import { AI } from './actions';

export default function RootLayout({
  children,
}: Readonly<{ children: ReactNode }>) {
  return (
    <AI>
      <html lang="en">
        <body>{children}</body>
      </html>
    </AI>
  );
}
```

### Reading UI State in Client

The UI state can be accessed in Client Components using the `useUIState` hook provided by the RSC API. The hook returns the current UI state and a function to update the UI state like React's `useState`.

```typescript
// app/page.tsx

'use client';

import { useUIState } from 'ai/rsc';

export default function Page() {
  const [messages, setMessages] = useUIState();

  return (
    <ul>
      {messages.map(message => (
        <li key={message.id}>{message.display}</li>
      ))}
    </ul>
  );
}
```

### Reading AI State in Client

The AI state can be accessed in Client Components using the `useAIState` hook provided by the RSC API. The hook returns the current AI state.

```typescript
// app/page.tsx

'use client';

import { useAIState } from 'ai/rsc';

export default function Page() {
  const [messages, setMessages] = useAIState();

  return (
    <ul>
      {messages.map(message => (
        <li key={message.id}>{message.content}</li>
      ))}
    </ul>
  );
}
```

### Reading AI State on Server

The AI State can be accessed within any Server Action provided to the `createAI` context using the `getAIState` function. It returns the current AI state as a read-only value:

```typescript
// app/actions.ts

import { getAIState } from 'ai/rsc';

export async function sendMessage(message: string) {
  'use server';

  const history = getAIState();

  const response = await generateText({
    model: openai('gpt-3.5-turbo'),
    messages: [...history, { role: 'user', content: message }],
  });

  return response;
}
```

Remember, you can only access state within actions that have been passed to the `createAI` context within the `actions` key.

### Updating AI State on Server

The AI State can also be updated from within your Server Action with the `getMutableAIState` function. This function is similar to `getAIState`, but it returns the state with methods to read and update it:

```typescript
// app/actions.ts

import { getMutableAIState } from 'ai/rsc';

export async function sendMessage(message: string) {
  'use server';

  const history = getMutableAIState();

  // Update the AI state with the new user message.
  history.update([...history.get(), { role: 'user', content: message }]);

  const response = await generateText({
    model: openai('gpt-3.5-turbo'),
    messages: history.get(),
  });

  // Update the AI state again with the response from the model.
  history.done([...history.get(), { role: 'assistant', content: response }]);

  return response;
}
```

It is important to update the AI State with new responses using `.update()` and `.done()` to keep the conversation history in sync.

### Calling Server Actions from the Client

To call the `sendMessage` action from the client, you can use the `useActions` hook. The hook returns all the available Actions that were provided to `createAI`:

```typescript
// app/page.tsx

'use client';

import { useActions, useUIState } from 'ai/rsc';
import { AI } from './actions';

export default function Page() {
  const { sendMessage } = useActions<typeof AI>();
  const [messages, setMessages] = useUIState();

  const handleSubmit = async event => {
    event.preventDefault();

    setMessages([
      ...messages,
      { id: Date.now(), role: 'user', display: event.target.message.value },
    ]);

    const response = await sendMessage(event.target.message.value);

    setMessages([
      ...messages,
      { id: Date.now(), role: 'assistant', display: response },
    ]);
  };

  return (
    <>
      <ul>
        {messages.map(message => (
          <li key={message.id}>{message.display}</li>
        ))}
      </ul>
      <form onSubmit={handleSubmit}>
        <input type="text" name="message" />
        <button type="submit">Send</button>
      </form>
    </>
  );
}
```

When the user submits a message, the `sendMessage` action is called with the message content. The response from the action is then added to the UI state, updating the displayed messages.

*Important! Don't forget to update the UI State after you call your Server Action otherwise the streamed component will not show in the UI.*

# Saving and Restoring States

AI SDK RSC provides convenient methods for saving and restoring AI and UI state. This is useful for saving the state of your application after every model generation, and restoring it when the user revisits the generations.

## AI State

### Saving AI State

The AI state can be saved using the `onSetAIState` callback, which gets called whenever the AI state is updated. In the following example, you save the chat history to a database whenever the generation is marked as done.

```javascript
export const AI = createAI<ServerMessage[], ClientMessage[]>({
  actions: {
    continueConversation,
  },
  onSetAIState: async ({ state, done }) => {
    'use server';

    if (done) {
      saveChatToDB(state);
    }
  },
});
```

### Restoring AI State

The AI state can be restored using the `initialAIState` prop passed to the context provider created by the `createAI` function. In the following example, you restore the chat history from a database when the component is mounted.

```javascript
import { ReactNode } from 'react';
import { AI } from './actions';

export default async function RootLayout({
  children,
}: Readonly<{ children: ReactNode }>) {
  const chat = await loadChatFromDB();

  return (
    <html lang="en">
      <body>
        <AI initialAIState={chat}>{children}</AI>
      </body>
    </html>
  );
}
```

## UI State

### Saving UI State

The UI state cannot be saved directly, since the contents aren't yet serializable. Instead, you can use the AI state as a proxy to store details about the UI state and use it to restore the UI state when needed.

### Restoring UI State

The UI state can be restored using the AI state as a proxy. In the following example, you restore the chat history from the AI state when the component is mounted. You use the `onGetUIState` callback to listen for SSR events and restore the UI state.

```javascript
export const AI = createAI<ServerMessage[], ClientMessage[]>({
  actions: {
    continueConversation,
  },
  onGetUIState: async () => {
    'use server';

    const historyFromDB: ServerMessage[] = await loadChatFromDB();
    const historyFromApp: ServerMessage[] = getAIState();

    // If the history from the database is different from the
    // history in the app, they're not in sync so return the UIState
    // based on the history from the database

    if (historyFromDB.length !== historyFromApp.length) {
      return historyFromDB.map(({ role, content }) => ({
        id: generateId(),
        role,
        display:
          role === 'function' ? (
            <Component {...JSON.parse(content)} />
          ) : (
            content
          ),
      }));
    }
  },
});
```

To learn more, check out [this example](https://example.com) that persists and restores states in your Next.js application.

Next, you will learn how you can use `ai/rsc` functions like `useActions` and `useUIState` to create interactive, multistep interfaces.

# Designing Multistep Interfaces

Multistep interfaces refer to user interfaces that require multiple independent steps to be executed in order to complete a specific task.

For example, if you wanted to build a Generative UI chatbot capable of booking flights, it could have three steps:

1. Search all flights
2. Pick flight
3. Check availability

To build this kind of application you will leverage two concepts, *tool composition* and *application context*.

*Tool composition* is the process of combining multiple tools to create a new tool. This is a powerful concept that allows you to break down complex tasks into smaller, more manageable steps. In the example above, "search all flights", "pick flight", and "check availability" come together to create a holistic "book flight" tool.

*Application context* refers to the state of the application at any given point in time. This includes the user's input, the output of the language model, and any other relevant information. In the example above, the flight selected in "pick flight" would be used as context necessary to complete the "check availability" task.

## Overview

In order to build a multistep interface with `ai/rsc`, you will need a few things:

- A Server Action that calls and returns the result from the `streamUI` function
- Tool(s) (sub-tasks necessary to complete your overall task)
- React component(s) that should be rendered when the tool is called
- A page to render your chatbot

The general flow that you will follow is:

1. User sends a message (calls your Server Action with `useActions`, passing the message as an input)
2. Message is appended to the AI State and then passed to the model alongside a number of tools
3. Model can decide to call a tool, which will render the `<SomeTool />` component
4. Within that component, you can add interactivity by using `useActions` to call the model with your Server Action and `useUIState` to append the model's response (`<SomeOtherTool />`) to the UI State
5. And so on...

## Implementation

The turn-by-turn implementation is the simplest form of multistep interfaces. In this implementation, the user and the model take turns during the conversation. For every user input, the model generates a response, and the conversation continues in this turn-by-turn fashion.

In the following example, you specify two tools (`searchFlights` and `lookupFlight`) that the model can use to search for flights and lookup details for a specific flight.

```typescript
// app/actions.tsx
import { createAI, streamUI } from 'ai/rsc';
import { openai } from '@ai-sdk/openai';
import { z } from 'zod';

const searchFlights = async (
  source: string,
  destination: string,
  date: string,
) => {
  return [
    {
      id: '1',
      flightNumber: 'AA123',
    },
    {
      id: '2',
      flightNumber: 'AA456',
    },
  ];
};

const lookupFlight = async (flightNumber: string) => {
  return {
    flightNumber: flightNumber,
    departureTime: '10:00 AM',
    arrivalTime: '12:00 PM',
  };
};

export async function submitUserMessage(input: string) {
  'use server';

  const ui = await streamUI({
    model: openai('gpt-4o'),
    system: 'you are a flight booking assistant',
    prompt: input,
    text: async ({ content }) => <div>{content}</div>,
    tools: {
      searchFlights: {
        description: 'search for flights',
        parameters: z.object({
          source: z.string().describe('The origin of the flight'),
          destination: z.string().describe('The destination of the flight'),
          date: z.string().describe('The date of the flight'),
        }),
        generate: async function* ({ source, destination, date }) {
          yield `Searching for flights from ${source} to ${destination} on ${date}...`;
          const results = await searchFlights(source, destination, date);

          return (
            <div>
              {results.map(result => (
                <div key={result.id}>
                  <div>{result.flightNumber}</div>
                </div>
              ))}
            </div>
          );
        },
      },
      lookupFlight: {
        description: 'lookup details for a flight',
        parameters: z.object({
          flightNumber: z.string().describe('The flight number'),
        }),
        generate: async function* ({ flightNumber }) {
          yield `Looking up details for flight ${flightNumber}...`;
          const details = await lookupFlight(flightNumber);

          return (
            <div>
              <div>Flight Number: {details.flightNumber}</div>
              <div>Departure Time: {details.departureTime}</div>
              <div>Arrival Time: {details.arrivalTime}</div>
            </div>
          );
        },
      },
    },
  });

  return ui.value;
}

export const AI = createAI<any[], React.ReactNode[]>({
  initialUIState: [],
  initialAIState: [],
  actions: {
    submitUserMessage,
  },
});
```

Next, wrap your application with your newly created context.

```typescript
// app/layout.tsx
import { type ReactNode } from 'react';
import { AI } from './actions';

export default function RootLayout({
  children,
}: Readonly<{ children: ReactNode }>) {
  return (
    <AI>
      <html lang="en">
        <body>{children}</body>
      </html>
    </AI>
  );
}
```

To call your Server Action, update your root page with the following:

```typescript
// app/page.tsx
'use client';

import { useState } from 'react';
import { AI } from './actions';
import { useActions, useUIState } from 'ai/rsc';

export default function Page() {
  const [input, setInput] = useState<string>('');
  const [conversation, setConversation] = useUIState<typeof AI>();
  const { submitUserMessage } = useActions();

  const handleSubmit = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    setInput('');
    setConversation(currentConversation => [
      ...currentConversation,
      <div>{input}</div>,
    ]);
    const message = await submitUserMessage(input);
    setConversation(currentConversation => [...currentConversation, message]);
  };

  return (
    <div>
      <div>
        {conversation.map((message, i) => (
          <div key={i}>{message}</div>
        ))}
      </div>
      <div>
        <form onSubmit={handleSubmit}>
          <input
            type="text"
            value={input}
            onChange={e => setInput(e.target.value)}
          />
          <button>Send Message</button>
        </form>
      </div>
    </div>
  );
}
```

This page pulls in the current UI State using the `useUIState` hook, which is then mapped over and rendered in the UI. To access the Server Action, you use the `useActions` hook which will return all actions that were passed to the `actions` key of the `createAI` function in your `actions.tsx` file. Finally, you call the `submitUserMessage` function like any other TypeScript function. This function returns a React component (message) that is then rendered in the UI by updating the UI State with `setConversation`.

In this example, to call the next tool, the user must respond with plain text. Given you are streaming a React component, you can add a button to trigger the next step in the conversation.

To add user interaction, you will have to convert the component into a client component and use the `useAction` hook to trigger the next step in the conversation.

```typescript
// components/flights.tsx
'use client';

import { useActions, useUIState } from 'ai/rsc';
import { ReactNode } from 'react';

interface FlightsProps {
  flights: { id: string; flightNumber: string }[];
}

export const Flights = ({ flights }: FlightsProps) => {
  const { submitUserMessage } = useActions();
  const [_, setMessages] = useUIState();

  return (
    <div>
      {flights.map(result => (
        <div key={result.id}>
          <div
            onClick={async () => {
              const display = await submitUserMessage(
                `lookupFlight ${result.flightNumber}`,
              );

              setMessages((messages: ReactNode[]) => [...messages, display]);
            }}
          >
            {result.flightNumber}
          </div>
        </div>
      ))}
    </div>
  );
};
```

Now, update your `searchFlights` tool to render the new `<Flights />` component.

```typescript
// actions.tsx
...
searchFlights: {
  description: 'search for flights',
  parameters: z.object({
    source: z.string().describe('The origin of the flight'),
    destination: z.string().describe('The destination of the flight'),
    date: z.string().describe('The date of the flight'),
  }),
  generate: async function* ({ source, destination, date }) {
    yield `Searching for flights from ${source} to ${destination} on ${date}...`;
    const results = await searchFlights(source, destination, date);
    return (<Flights flights={results} />);
  },
}
...
```

In the above example, the `Flights` component is used to display the search results. When the user clicks on a flight number, the `lookupFlight` tool is called with the flight number as a parameter. The `submitUserMessage` action is then called to trigger the next step in the conversation.

# Streaming Values

The RSC API provides several utility functions to allow you to stream values from the server to the client. This is useful when you need more granular control over what you are streaming and how you are streaming it.

These utilities can also be paired with AI SDK Core functions like `streamText` and `streamObject` to easily stream LLM generations from the server to the client.

There are two functions provided by the RSC API that allow you to create streamable values:

- `createStreamableValue` - creates a streamable (serializable) value, with full control over how you create, update, and close the stream.
- `createStreamableUI` - creates a streamable React component, with full control over how you create, update, and close the stream.

## `createStreamableValue`

The RSC API allows you to stream serializable JavaScript values from the server to the client using `createStreamableValue`, such as strings, numbers, objects, and arrays.

This is useful when you want to stream:

- Text generations from the language model in real-time.
- Buffer values of image and audio generations from multi-modal models.
- Progress updates from multi-step agent runs.

### Creating a Streamable Value

You can import `createStreamableValue` from `ai/rsc` and use it to create a streamable value.

```javascript
'use server';

import { createStreamableValue } from 'ai/rsc';

export const runThread = async () => {
  const streamableStatus = createStreamableValue('thread.init');

  setTimeout(() => {
    streamableStatus.update('thread.run.create');
    streamableStatus.update('thread.run.update');
    streamableStatus.update('thread.run.end');
    streamableStatus.done('thread.end');
  }, 1000);

  return {
    status: streamableStatus.value,
  };
};
```

### Reading a Streamable Value

You can read streamable values on the client using `readStreamableValue`. It returns an async iterator that yields the value of the streamable as it is updated:

```javascript
import { readStreamableValue } from 'ai/rsc';
import { runThread } from '@/actions';

export default function Page() {
  return (
    <button
      onClick={async () => {
        const { status } = await runThread();

        for await (const value of readStreamableValue(status)) {
          console.log(value);
        }
      }}
    >
      Ask
    </button>
  );
}
```

Learn how to stream a text generation (with `streamText`) using the Next.js App Router and `createStreamableValue` in this example.

## `createStreamableUI`

`createStreamableUI` creates a stream that holds a React component. Unlike AI SDK Core APIs, this function does not call a large language model. Instead, it provides a primitive that can be used to have granular control over streaming a React component.

### Using `createStreamableUI`

Let's look at how you can use the `createStreamableUI` function with a Server Action.

**app/actions.tsx**

```javascript
'use server';

import { createStreamableUI } from 'ai/rsc';

export async function getWeather() {
  const weatherUI = createStreamableUI();

  weatherUI.update(<div style={{ color: 'gray' }}>Loading...</div>);

  setTimeout(() => {
    weatherUI.done(<div>It's a sunny day!</div>);
  }, 1000);

  return weatherUI.value;
}
```

First, you create a streamable UI with an empty state and then update it with a loading message. After 1 second, you mark the stream as done passing in the actual weather information as its final value. The `.value` property contains the actual UI that can be sent to the client.

### Reading a Streamable UI

On the client side, you can call the `getWeather` Server Action and render the returned UI like any other React component.

**app/page.tsx**

```javascript
import { useState } from 'react';
import { readStreamableValue } from 'ai/rsc';
import { getWeather } from '@/actions';

export default function Page() {
  const [weather, setWeather] = useState(null);

  return (
    <div>
      <button
        onClick={async () => {
          const weatherUI = await getWeather();
          setWeather(weatherUI);
        }}
      >
        What's the weather?
      </button>

      {weather}
    </div>
  );
}
```

When the button is clicked, the `getWeather` function is called, and the returned UI is set to the `weather` state and rendered on the page. Users will see the loading message first and then the actual weather information after 1 second.

# Handling Loading State

Given that responses from language models can often take a while to complete, it's crucial to be able to show loading state to users. This provides visual feedback that the system is working on their request and helps maintain a positive user experience.

There are two approaches you can take to handle loading state with the AI SDK RSC:

1. Managing loading state similar to how you would in a traditional Next.js application. This involves setting a loading state variable in the client and updating it when the response is received.
2. Streaming loading state from the server to the client. This approach allows you to track loading state on a more granular level and provide more detailed feedback to the user.

## Handling Loading State on the Client

### Client

Let's create a simple Next.js page that will call the `generateResponse` function when the form is submitted. The function will take in the user's prompt (input) and then generate a response (response). To handle the loading state, we'll use the loading state variable. When the form is submitted, we'll set `loading` to `true`, and when the response is received, we'll set it back to `false`. While the response is being streamed, we will disable the input field.

```typescript
'use client';

import { useState } from 'react';
import { generateResponse } from './actions';
import { readStreamableValue } from 'ai/rsc';

// Force the page to be dynamic and allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Home() {
  const [input, setInput] = useState<string>('');
  const [generation, setGeneration] = useState<string>('');
  const [loading, setLoading] = useState<boolean>(false);

  return (
    <div>
      <div>{generation}</div>
      <form
        onSubmit={async e => {
          e.preventDefault();
          setLoading(true);
          const response = await generateResponse(input);

          let textContent = '';

          for await (const delta of readStreamableValue(response)) {
            textContent = `${textContent}${delta}`;
            setGeneration(textContent);
          }
          setInput('');
          setLoading(false);
        }}
      >
        <input
          type="text"
          value={input}
          disabled={loading}
          className="disabled:opacity-50"
          onChange={event => {
            setInput(event.target.value);
          }}
        />
        <button>Send Message</button>
      </form>
    </div>
  );
}
```

### Server

Now let's implement the `generateResponse` function. We'll use the `streamText` function to generate a response to the input.

```typescript
'use server';

import { streamText } from 'ai';
import { openai } from '@ai-sdk/openai';
import { createStreamableValue } from 'ai/rsc';

export async function generateResponse(prompt: string) {
  const stream = createStreamableValue();

  (async () => {
    const { textStream } = await streamText({
      model: openai('gpt-4o'),
      prompt,
    });

    for await (const text of textStream) {
      stream.update(text);
    }

    stream.done();
  })();

  return stream.value;
}
```

## Streaming Loading State from the Server

If you are looking to track loading state on a more granular level, you can create a new streamable value to store a custom variable and then read this on the frontend. Let's update the example to create a new streamable value for tracking loading state:

### Server

```typescript
'use server';

import { streamText } from 'ai';
import { openai } from '@ai-sdk/openai';
import { createStreamableValue } from 'ai/rsc';

export async function generateResponse(prompt: string) {
  const stream = createStreamableValue();
  const loadingState = createStreamableValue({ loading: true });

  (async () => {
    const { textStream } = await streamText({
      model: openai('gpt-4o'),
      prompt,
    });

    for await (const text of textStream) {
      stream.update(text);
    }

    stream.done();
    loadingState.done({ loading: false });
  })();

  return { response: stream.value, loadingState: loadingState.value };
}
```

### Client

```typescript
'use client';

import { useState } from 'react';
import { generateResponse } from './actions';
import { readStreamableValue } from 'ai/rsc';

// Force the page to be dynamic and allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Home() {
  const [input, setInput] = useState<string>('');
  const [generation, setGeneration] = useState<string>('');
  const [loading, setLoading] = useState<boolean>(false);

  return (
    <div>
      <div>{generation}</div>
      <form
        onSubmit={async e => {
          e.preventDefault();
          setLoading(true);
          const { response, loadingState } = await generateResponse(input);

          let textContent = '';

          for await (const responseDelta of readStreamableValue(response)) {
            textContent = `${textContent}${responseDelta}`;
            setGeneration(textContent);
          }
          for await (const loadingDelta of readStreamableValue(loadingState)) {
            if (loadingDelta) {
              setLoading(loadingDelta.loading);
            }
          }
          setInput('');
          setLoading(false);
        }}
      >
        <input
          type="text"
          value={input}
          disabled={loading}
          className="disabled:opacity-50"
          onChange={event => {
            setInput(event.target.value);
          }}
        />
        <button>Send Message</button>
      </form>
    </div>
  );
}
```

This allows you to provide more detailed feedback about the generation process to your users.

## Streaming Loading Components with `streamUI`

If you are using the `streamUI` function, you can stream the loading state to the client in the form of a React component. `streamUI` supports the usage of JavaScript generator functions, which allow you to yield some value (in this case a React component) while some other blocking work completes.

### Server

```typescript
'use server';

import { openai } from '@ai-sdk/openai';
import { streamUI } from 'ai/rsc';

export async function generateResponse(prompt: string) {
  const result = await streamUI({
    model: openai('gpt-4o'),
    prompt,
    text: async function* ({ content }) {
      yield <div>loading...</div>;
      return <div>{content}</div>;
    },
  });

  return result.value;
}
```

Because we are defining a React component in our `streamUI` function, we must update the file from `.ts` to `.tsx`.

### Client

```typescript
'use client';

import { useState } from 'react';
import { generateResponse } from './actions';
import { readStreamableValue } from 'ai/rsc';

// Force the page to be dynamic and allow streaming responses up to 30 seconds
export const maxDuration = 30;

export default function Home() {
  const [input, setInput] = useState<string>('');
  const [generation, setGeneration] = useState<React.ReactNode>();

  return (
    <div>
      <div>{generation}</div>
      <form
        onSubmit={async e => {
          e.preventDefault();
          const result = await generateResponse(input);
          setGeneration(result);
          setInput('');
        }}
      >
        <input
          type="text"
          value={input}
          onChange={event => {
            setInput(event.target.value);
          }}
        />
        <button>Send Message</button>
      </form>
    </div>
  );
}
```

# Error Handling

Two categories of errors can occur when working with the RSC API: errors while streaming user interfaces and errors while streaming other values.

## Handling UI Errors

To handle errors while generating UI, the `streamableUI` object exposes an `error()` method.

```typescript
'use server';

import { createStreamableUI } from 'ai/rsc';

export async function getStreamedUI() {
  const ui = createStreamableUI();

  (async () => {
    ui.update(<div>loading</div>);
    const data = await fetchData();
    ui.done(<div>{data}</div>);
  })().catch(e => {
    ui.error(<div>Error: {e.message}</div>);
  });

  return ui.value;
}
```

With this method, you can catch any error with the stream, and return relevant UI. On the client, you can also use a React Error Boundary to wrap the streamed component and catch any additional errors.

```typescript
import { getStreamedUI } from '@/actions';
import { useState } from 'react';
import { ErrorBoundary } from './ErrorBoundary';

export default function Page() {
  const [streamedUI, setStreamedUI] = useState(null);

  return (
    <div>
      <button
        onClick={async () => {
          const newUI = await getStreamedUI();
          setStreamedUI(newUI);
        }}
      >
        What does the new UI look like?
      </button>
      <ErrorBoundary>{streamedUI}</ErrorBoundary>
    </div>
  );
}
```

## Handling Other Errors

To handle other errors while streaming, you can return an error object that the receiver can use to determine why the failure occurred.

```typescript
'use server';

import { createStreamableValue } from 'ai/rsc';
import { fetchData, emptyData } from '../utils/data';

export const getStreamedData = async () => {
  const streamableData = createStreamableValue<string>(emptyData);

  try {
    (() => {
      const data1 = await fetchData();
      streamableData.update(data1);

      const data2 = await fetchData();
      streamableData.update(data2);

      const data3 = await fetchData();
      streamableData.done(data3);
    })();

    return { data: streamableData.value };
  } catch (e) {
    return { error: e.message };
  }
};

# Authentication

The RSC API makes extensive use of *Server Actions* to power streaming values and UI from the server.

Server Actions are exposed as public, unprotected endpoints. As a result, you should treat Server Actions as you would public-facing API endpoints and ensure that the user is authorized to perform the action before returning any data.

## app/actions.tsx

```typescript
'use server';

import { cookies } from 'next/headers';
import { createStremableUI } from 'ai/rsc';
import { validateToken } from '../utils/auth';

export const getWeather = async () => {
  const token = cookies().get('token');

  if (!token || !validateToken(token)) {
    return {
      error: 'This action requires authentication',
    };
  }
  const streamableDisplay = createStreamableUI(null);

  streamableDisplay.update(<Skeleton />);
  streamableDisplay.done(<Weather />);

  return {
    display: streamableDisplay.value,
  };
};
```

# API Reference

# AI SDK RSC

## streamUI
Use a helper function that streams React Server Components on tool execution.

## createAI
Create a *context provider* that wraps your application and shares state between the client and language model on the server.

## createStreamableUI
Create a *streamable UI component* that can be rendered on the server and streamed to the client.

## createStreamableValue
Create a *streamable value* that can be rendered on the server and streamed to the client.

## getAIState
Read the AI state on the server.

## getMutableAIState
Read and update the AI state on the server.

## useAIState
Get the AI state on the client from the context provider.

## useUIState
Get the UI state on the client from the context provider.

## useActions
Call server actions from the client.

# streamUI

A helper function to create a streamable UI from LLM providers. This function is similar to AI SDK Core APIs and supports the same model interfaces.

## Import

```javascript
import { streamUI } from "ai/rsc"
```

## Parameters

**model**:
*LanguageModel*
The language model to use. Example: `openai("gpt-4-turbo")`

**initial**?:
*ReactNode*
The initial UI to render.

**system**:
*string*
The system prompt to use that specifies the behavior of the model.

**prompt**:
*string*
The input prompt to generate the text from.

**messages**:
*Array<CoreSystemMessage | CoreUserMessage | CoreAssistantMessage | CoreToolMessage>*
A list of messages that represent a conversation.

**CoreSystemMessage**
- **role**: `'system'`
  The role for the system message.
- **content**: *string*
  The content of the message.

**CoreUserMessage**
- **role**: `'user'`
  The role for the user message.
- **content**: *string | Array<TextPart | ImagePart>*
  The content of the message.

**TextPart**
- **type**: `'text'`
  The type of the message part.
- **text**: *string*
  The text content of the message part.

**ImagePart**
- **type**: `'image'`
  The type of the message part.
- **image**: *string | Uint8Array | Buffer | ArrayBuffer | URL*
  The image content of the message part. String are either base64 encoded content, base64 data URLs, or http(s) URLs.

**CoreAssistantMessage**
- **role**: `'assistant'`
  The role for the assistant message.
- **content**: *string | Array<TextPart | ToolCallPart>*
  The content of the message.

**ToolCallPart**
- **type**: `'tool-call'`
  The type of the message part.
- **toolCallId**: *string*
  The id of the tool call.
- **toolName**: *string*
  The name of the tool, which typically would be the name of the function.
- **args**: *object based on zod schema*
  Parameters generated by the model to be used by the tool.

**CoreToolMessage**
- **role**: `'tool'`
  The role for the assistant message.
- **content**: *Array<ToolResultPart>*
  The content of the message.

**ToolResultPart**
- **type**: `'tool-result'`
  The type of the message part.
- **toolCallId**: *string*
  The id of the tool call the result corresponds to.
- **toolName**: *string*
  The name of the tool the result corresponds to.
- **result**: *unknown*
  The result returned by the tool after execution.
- **isError**?: *boolean*
  Whether the result is an error or an error message.

**maxTokens**?:
*number*
Maximum number of tokens to generate.

**temperature**?:
*number*
Temperature setting. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.

**topP**?:
*number*
Nucleus sampling. The value is passed through to the provider. The range depends on the provider and model. It is recommended to set either `temperature` or `topP`, but not both.

**topK**?:
*number*
Only sample from the top K options for each subsequent token. Used to remove "long tail" low probability responses. Recommended for advanced use cases only. You usually only need to use temperature.

**presencePenalty**?:
*number*
Presence penalty setting. It affects the likelihood of the model to repeat information that is already in the prompt. The value is passed through to the provider. The range depends on the provider and model.

**frequencyPenalty**?:
*number*
Frequency penalty setting. It affects the likelihood of the model to repeatedly use the same words or phrases. The value is passed through to the provider. The range depends on the provider and model.

**stopSequences**?:
*string[]*
Sequences that will stop the generation of the text. If the model generates any of these sequences, it will stop generating further text.

**seed**?:
*number*
The seed (integer) to use for random sampling. If set and supported by the model, calls will generate deterministic results.

**maxRetries**?:
*number*
Maximum number of retries. Set to 0 to disable retries. Default: 2.

**abortSignal**?:
*AbortSignal*
An optional abort signal that can be used to cancel the call.

**headers**?:
*Record<string, string>*
Additional HTTP headers to be sent with the request. Only applicable for HTTP-based providers.

**tools**:
*Record<string, Tool>*
Tools that are accessible to and can be called by the model.

**Tool**
- **description**?: *string*
  Information about the purpose of the tool including details on how and when it can be used by the model.
- **parameters**: *zod schema*
  The typed schema that describes the parameters of the tool that can also be used to validation and error handling.
- **generate**?: *(async (parameters) => ReactNode) | AsyncGenerator<ReactNode, ReactNode, void>*
  A function or a generator function that is called with the arguments from the tool call and yields React nodes as the UI.
- **toolChoice**?: `"auto" | "none" | "required" | { "type": "tool", "toolName": string }`
  The tool choice setting. It specifies how tools are selected for execution. The default is "auto". "none" disables tool execution. "required" requires tools to be executed. `{ "type": "tool", "toolName": string }` specifies a specific tool to execute.

**text**?:
*(Text) => ReactNode*
Callback to handle the generated tokens from the model.

**Text**
- **content**: *string*
  The full content of the completion.
- **delta**: *string*
  The delta.
- **done**: *boolean*
  Is it done?

**onFinish**?:
*(result: OnFinishResult) => void*
Callback that is called when the LLM response and all request tool executions (for tools that have a `generate` function) are finished.

**OnFinishResult**
- **usage**: *TokenUsage*
  The token usage of the generated text.
- **value**: *ReactNode*
  The final ui node that was generated.
- **warnings**: *Warning[] | undefined*
  Warnings from the model provider (e.g. unsupported settings).
- **rawResponse**: *RawResponse*
  Optional raw response data.

**TokenUsage**
- **promptTokens**: *number*
  The total number of tokens in the prompt.
- **completionTokens**: *number*
  The total number of tokens in the completion.
- **totalTokens**: *number*
  The total number of tokens generated.

**RawResponse**
- **headers**: *Record<string, string>*
  Response headers.

## Returns

**value**:
*ReactNode*
The user interface based on the stream output.

**text**:
*Promise<string>*
The full text that has been generated. Resolved when the response is finished.

**toolCalls**:
*Promise<ToolCall[]>*
The tool calls that have been executed. Resolved when the response is finished.

**toolResults**:
*Promise<ToolResult[]>*
The tool results that have been generated. Resolved when the all tool executions are finished.

**finishReason**:
*Promise<'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown'>*
The reason why the generation finished. Resolved when the response is finished.

**usage**:
*Promise<TokenUsage>*
The token usage of the generated text. Resolved when the response is finished.

**textStream**:
*AsyncIterable<string> & ReadableStream<string>*
A text stream that returns only the generated text deltas. You can use it as either an AsyncIterable or a ReadableStream. When an error occurs, the stream will throw the error.

**fullStream**:
*AsyncIterable<TextStreamPart> & ReadableStream<TextStreamPart>*
A stream with all events, including text deltas, tool calls, tool results, and errors. You can use it as either an AsyncIterable or a ReadableStream. When an error occurs, the stream will throw the error.

**TextStreamPart**
- **type**: `'text-delta'`
  The type to identify the object as text delta.
- **textDelta**: *string*
  The text delta.

**TextStreamPart**
- **type**: `'tool-call'`
  The type to identify the object as tool call.
- **toolCallId**: *string*
  The id of the tool call.
- **toolName**: *string*
  The name of the tool, which typically would be the name of the function.
- **args**: *object based on zod schema*
  Parameters generated by the model to be used by the tool.

**TextStreamPart**
- **type**: `'tool-result'`
  The type to identify the object as tool result.
- **toolCallId**: *string*
  The id of the tool call.
- **toolName**: *string*
  The name of the tool, which typically would be the name of the function.
- **args**: *object based on zod schema*
  Parameters generated by the model to be used by the tool.
- **result**: *any*
  The result returned by the tool after execution has completed.

**TextStreamPart**
- **type**: `'error'`
  The type to identify the object as error.
- **error**: *Error*
  Describes the error that may have occurred during execution.

**TextStreamPart**
- **type**: `'finish'`
  The type to identify the object as finish.
- **finishReason**: `'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown'`
  The reason the model finished generating the text.
- **usage**: *TokenUsage*
  The token usage of the generated text.

**toDataStream**:
*(callbacks?: AIStreamCallbacksAndOptions) => data stream*
Converts the result to a data stream.

**pipeDataStreamToResponse**:
*(response: ServerResponse, init?: { headers?: Record<string, string>; status?: number } => void*
Writes stream data output to a Node.js response-like object. It sets a `Content-Type` header to `text/plain; charset=utf-8` and writes each stream data part as a separate chunk.

**pipeTextStreamToResponse**:
*(response: ServerResponse, init?: { headers?: Record<string, string>; status?: number } => void*
Writes text delta output to a Node.js response-like object. It sets a `Content-Type` header to `text/plain; charset=utf-8` and writes each text delta as a separate chunk.

**toDataStreamResponse**:
*(init?: ResponseInit) => Response*
Converts the result to a streamed response object with a stream data part stream. It can be used with the `useChat` and `useCompletion` hooks.

**toTextStreamResponse**:
*(init?: ResponseInit) => Response*
Creates a simple text stream response. Each text delta is encoded as UTF-8 and sent as a separate chunk. Non-text-delta events are ignored.

# createAI

Creates a client-server context provider that can be used to wrap parts of your application tree to easily manage both UI and AI states of your application.

## Import

```javascript
import { createAI } from "ai/rsc"
```

## API Signature

### Parameters

**actions:**
*Record<string, Action>*
Server side actions that can be called from the client.

**initialAIState:**
*any*
Initial AI state to be used in the client.

**initialUIState:**
*any*
Initial UI state to be used in the client.

**onGetUIState:**
*() => UIState*
is called during SSR to compare and update UI state.

**onSetAIState:**
*(Event) => void*
is triggered whenever an `update()` or `done()` is called by the mutable AI state in your action, so you can safely store your AI state in the database.

**Event**
- **state:** *AIState*
  The resulting AI state after the update.
- **done:** *boolean*
  Whether the AI state updates have been finalized or not.

### Returns

It returns an `<AI/>` context provider.

# createStreamableUI

Create a stream that sends UI from the server to the client. On the client side, it can be rendered as a normal React node.

## Import

```javascript
import { createStreamableUI } from "ai/rsc"
```

## API Signature

### Parameters

**initialValue?:**
*ReactNode*
The initial value of the streamable UI.

### Returns

**value:**
*ReactNode*
The value of the streamable UI. This can be returned from a Server Action and received by the client.

### Methods

**update:**
*(ReactNode) => void*
Updates the current UI node. It takes a new UI node and replaces the old one.

**append:**
*(ReactNode) => void*
Appends a new UI node to the end of the old one. Once appended a new UI node, the previous UI node cannot be updated anymore.

**done:**
*(ReactNode | null) => void*
Marks the UI node as finalized and closes the stream. Once called, the UI node cannot be updated or appended anymore. This method is always required to be called, otherwise the response will be stuck in a loading state.

**error:**
*(Error) => void*
Signals that there is an error in the UI stream. It will be thrown on the client side and caught by the nearest error boundary component.

# createStreamableValue

Create a stream that sends values from the server to the client. The value can be any serializable data.

## Import

```javascript
import { createStreamableValue } from "ai/rsc"
```

## API Signature

### Parameters

**value:**
*any*
Any data that RSC supports. Example, JSON.

### Returns

**value:**
*streamable*
This creates a special value that can be returned from Actions to the client. It holds the data inside and can be updated via the `update` method.

# readStreamableValue

It is a function that helps you read the streamable value from the client that was originally created using `createStreamableValue` on the server.

## Import

```javascript
import { readStreamableValue } from "ai/rsc"
```

## Example

```javascript
// app/actions.ts
async function generate() {
  'use server';
  const streamable = createStreamableValue();

  streamable.update(1);
  streamable.update(2);
  streamable.done(3);

  return streamable.value;
}

// app/page.tsx
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

## API Signature

### Parameters

**stream:**
*StreamableValue*
The streamable value to read from.

### Returns

It returns an async iterator that contains the values emitted by the streamable value.

# getAIState

Get the current AI state.

## Import

```javascript
import { getAIState } from "ai/rsc"
```

## API Signature

### Parameters

**key?:**
*string*
Returns the value of the specified key in the AI state, if it's an object.

### Returns

The AI state.

# getMutableAIState

Get a mutable copy of the AI state. You can use this to update the state in the server.

## Import

```javascript
import { getMutableAIState } from "ai/rsc"
```

## API Signature

### Parameters

**key?:**
*string*
Returns the value of the specified key in the AI state, if it's an object.

### Returns

The mutable AI state.

### Methods

**update:**
*(newState: any) => void*
Updates the AI state with the new state.

**done:**
*(newState: any) => void*
Updates the AI state with the new state, marks it as finalized and closes the stream.

# useAIState

It is a hook that enables you to read and update the AI state. The AI state is shared globally between all `useAIState` hooks under the same `<AI/>` provider.

The AI state is intended to contain context and information shared with the AI model, such as system messages, function responses, and other relevant data.

## Import

```javascript
import { useAIState } from "ai/rsc"
```

## API Signature

### Returns

A single element array where the first element is the current AI state.

# useActions

It is a hook to help you access your Server Actions from the client. This is particularly useful for building interfaces that require user interactions with the server.

It is required to access these server actions via this hook because they are patched when passed through the context. Accessing them directly may result in a `Cannot find Client Component` error.

## Import

```javascript
import { useActions } from "ai/rsc"
```

## API Signature

### Returns

`Record<string, Action>`, a dictionary of server actions.

# useUIState

It is a hook that enables you to read and update the UI State. The state is client-side and can contain functions, React nodes, and other data. `UIState` is the visual representation of the AI state.

## Import

```javascript
import { useUIState } from "ai/rsc"
```

## API Signature

### Returns

Similar to `useState`, it is an array, where the first element is the current UI state and the second element is the function that updates the state.

# useStreamableValue

It is a React hook that takes a streamable value created using `createStreamableValue` and returns the current value, error, and pending state.

## Import

```javascript
import { useStreamableValue } from "ai/rsc"
```

## Example

This is useful for consuming streamable values received from a component's props.

```javascript
function MyComponent({ streamableValue }) {
  const [data, error, pending] = useStreamableValue(streamableValue);

  if (pending) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return <div>Data: {data}</div>;
}
```

## API Signature

### Parameters

It accepts a streamable value created using `createStreamableValue`.

### Returns

It is an array, where the first element contains the data, the second element contains an error if it is thrown anytime during the stream, and the third is a boolean indicating if the value is pending.

# Advanced

This section covers *advanced topics and concepts* for the AI SDK and RSC API. Working with LLMs often requires a different mental model compared to traditional software development.

After these concepts, you should have a better understanding of the paradigms behind the AI SDK and RSC API, and how to use them to build more AI applications.

# Stopping Streams

Cancelling ongoing streams is often needed. For example, users might want to stop a stream when they realize that the response is not what they want.

The different parts of the Vercel AI SDK support cancelling streams in different ways.

## AI SDK Core

The AI SDK functions have an `abortSignal` argument that you can use to cancel a stream. You would use this if you want to cancel a stream from the server side to the LLM API, e.g. by forwarding the `abortSignal` from the request.

```javascript
import { openai } from '@ai-sdk/openai';
import { streamText } from 'ai';

export async function POST(req: Request) {
  const { prompt } = await req.json();

  const result = await streamText({
    model: openai('gpt-4-turbo'),
    prompt,
    // forward the abort signal:
    abortSignal: req.signal,
  });

  return result.toTextStreamResponse();
}
```

## AI SDK UI

# Vercel AI SDK UI

**Vercel AI SDK UI** is designed to help you build interactive chat, completion, and assistant applications with ease. It is a framework-agnostic toolkit, streamlining the integration of advanced AI functionalities into your applications.

Vercel AI SDK UI provides robust abstractions that simplify the complex tasks of managing chat streams and UI updates on the frontend, enabling you to develop dynamic AI-driven interfaces more efficiently. With four main hooks — `useChat`, `useCompletion`, `useObject`, and `useAssistant` — you can incorporate real-time chat capabilities, text completions, streamed JSON, and interactive assistant features into your app.

## useChat

`useChat` offers real-time streaming of chat messages, abstracting state management for inputs, messages, loading, and errors, allowing for seamless integration into any UI design.

## useCompletion

`useCompletion` enables you to handle text completions in your applications, managing chat input state and automatically updating the UI as new completions are streamed from your AI provider.

## useObject

`useObject` is a hook that allows you to consume streamed JSON objects, providing a simple way to handle and display structured data in your application.

## useAssistant

`useAssistant` is designed to facilitate interaction with OpenAI-compatible assistant APIs, managing UI state and updating it automatically as responses are streamed.

These hooks are designed to reduce the complexity and time required to implement AI interactions, letting you focus on creating exceptional user experiences.

# Chatbot

The `useChat` hook makes it effortless to create a conversational user interface for your chatbot application. It enables the streaming of chat messages from your AI provider, manages the chat state, and updates the UI automatically as new messages arrive.

To summarize, the `useChat` hook provides the following features:

1. **Message Streaming**: All the messages from the AI provider are streamed to the chat UI in real-time.
2. **Managed States**: The hook manages the states for input, messages, loading, error and more for you.
3. **Seamless Integration**: Easily integrate your chat AI into any design or layout with minimal effort.

In this guide, you will learn how to use the `useChat` hook to create a chatbot application with real-time message streaming. Let's start with the following example first.

## Example

```typescript
'use client';

import { useChat } from 'ai/react';

export default function Page() {
  const { messages, input, handleInputChange, handleSubmit } = useChat({
    keepLastMessageOnError: true,
  });

  return (
    <>
      {messages.map(message => (
        <div key={message.id}>
          {message.role === 'user' ? 'User: ' : 'AI: '}
          {message.content}
        </div>
      ))}

      <form onSubmit={handleSubmit}>
        <input name="prompt" value={input} onChange={handleInputChange} />
        <button type="submit">Submit</button>
      </form>
    </>
  );
}
```

```typescript
import { openai } from '@ai-sdk/openai';
import { convertToCoreMessages, streamText } from 'ai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = await streamText({
    model: openai('gpt-4-turbo'),
    system: 'You are a helpful assistant.',
    messages: convertToCoreMessages(messages),
  });

  return result.toDataStreamResponse();
}
```

In the `Page` component, the `useChat` hook will request to your AI provider endpoint whenever the user submits a message. The messages are then streamed back in real-time and displayed in the chat UI.

This enables a seamless chat experience where the user can see the AI response as soon as it is available, without having to wait for the entire response to be received.

`useChat` has a `keepLastMessageOnError` option that defaults to `false`. This option can be enabled to keep the last message on error. We will make this the default behavior in the next major release. Please enable it and update your error handling/resubmit behavior.

### Customized UI

`useChat` also provides ways to manage the chat message and input states via code, show loading and error states, and update messages without being triggered by user interactions.

#### Loading State

The `isLoading` state returned by the `useChat` hook can be used for several purposes:

1. To show a loading spinner while the chatbot is processing the user's message.
2. To show a "Stop" button to abort the current message.
3. To disable the submit button.

```typescript
'use client';

import { useChat } from 'ai/react';

export default function Page() {
  const { messages, input, handleInputChange, handleSubmit, isLoading, stop } =
    useChat({
      keepLastMessageOnError: true,
    });

  return (
    <>
      {messages.map(message => (
        <div key={message.id}>
          {message.role === 'user' ? 'User: ' : 'AI: '}
          {message.content}
        </div>
      ))}

      {isLoading && (
        <div>
          <Spinner />
          <button type="button" onClick={() => stop()}>
            Stop
          </button>
        </div>
      )}

      <form onSubmit={handleSubmit}>
        <input
          name="prompt"
          value={input}
          onChange={handleInputChange}
          disabled={isLoading}
        />
        <button type="submit">Submit</button>
      </form>
    </>
  );
}
```

#### Error State

Similarly, the `error` state reflects the error object thrown during the fetch request. It can be used to display an error message, disable the submit button, or show a retry button:

We recommend showing a generic error message to the user, such as "Something went wrong." This is a good practice to avoid leaking information from the server.

```typescript
'use client';

import { useChat } from 'ai/react';

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit, error, reload } =
    useChat({
      keepLastMessageOnError: true,
    });

  return (
    <div>
      {messages.map(m => (
        <div key={m.id}>
          {m.role}: {m.content}
        </div>
      ))}

      {error && (
        <>
          <div>An error occurred.</div>
          <button type="button" onClick={() => reload()}>
            Retry
          </button>
        </>
      )}

      <form onSubmit={handleSubmit}>
        <input
          value={input}
          onChange={handleInputChange}
          disabled={error != null}
        />
      </form>
    </div>
  );
}
```

Please also see the [error handling guide](https://example.com/error-handling) for more information.

#### Modify messages

Sometimes, you may want to directly modify some existing messages. For example, a delete button can be added to each message to allow users to remove them from the chat history.

The `setMessages` function can help you achieve these tasks:

```typescript
const { messages, setMessages, ... } = useChat()

const handleDelete = (id) => {
  setMessages(messages.filter(message => message.id !== id))
}

return <>
  {messages.map(message => (
    <div key={message.id}>
      {message.role === 'user' ? 'User: ' : 'AI: '}
      {message.content}
      <button onClick={() => handleDelete(message.id)}>Delete</button>
    </div>
  ))}
  ...
```

You can think of `messages` and `setMessages` as a pair of state and setState in React.

#### Controlled input

In the initial example, we have `handleSubmit` and `handleInputChange` callbacks that manage the input changes and form submissions. These are handy for common use cases, but you can also use uncontrolled APIs for more advanced scenarios such as form validation or customized components.

The following example demonstrates how to use more granular APIs like `setInput` and `append` with your custom input and submit button components:

```typescript
const { input, setInput, append } = useChat()

return <>
  <MyCustomInput value={input} onChange={value => setInput(value)} />
  <MySubmitButton onClick={() => {
    // Send a new message to the AI provider
    append({
      role: 'user',
      content: input,
    })
  }}/>
  ...
```

#### Cancelation and regeneration

It's also a common use case to abort the response message while it's still streaming back from the AI provider. You can do this by calling the `stop` function returned by the `useChat` hook.

```typescript
const { stop, isLoading, ... } = useChat()

return <>
  <button onClick={stop} disabled={!isLoading}>Stop</button>
  ...
```

When the user clicks the "Stop" button, the fetch request will be aborted. This avoids consuming unnecessary resources and improves the UX of your chatbot application.

Similarly, you can also request the AI provider to reprocess the last message by calling the `reload` function returned by the `useChat` hook:

```typescript
const { reload, isLoading, ... } = useChat()

return <>
  <button onClick={reload} disabled={isLoading}>Regenerate</button>
  ...
</>
```

When the user clicks the "Regenerate" button, the AI provider will regenerate the last message and replace the current one correspondingly.

#### Event Callbacks

`useChat` provides optional event callbacks that you can use to handle different stages of the chatbot lifecycle:

- `onFinish`: Called when the assistant message is completed
- `onError`: Called when an error occurs during the fetch request.
- `onResponse`: Called when the response from the API is received.

These callbacks can be used to trigger additional actions, such as logging, analytics, or custom UI updates.

```typescript
import { Message } from 'ai/react';

const {
  /* ... */
} = useChat({
  onFinish: (message, { usage, finishReason }) => {
    console.log('Finished streaming message:', message);
    console.log('Token usage:', usage);
    console.log('Finish reason:', finishReason);
  },
  onError: error => {
    console.error('An error occurred:', error);
  },
  onResponse: response => {
    console.log('Received HTTP response from server:', response);
  },
});
```

It's worth noting that you can abort the processing by throwing an error in the `onResponse` callback. This will trigger the `onError` callback and stop the message from being appended to the chat UI. This can be useful for handling unexpected responses from the AI provider.

# Chatbot with Tools

With `useChat` and `streamText`, you can use tools in your chatbot application. The Vercel AI SDK supports three types of tools in this context:

1. Automatically executed server-side tools
2. Automatically executed client-side tools
3. Tools that require user interaction, such as confirmation dialogs

The flow is as follows:

1. The user enters a message in the chat UI.
2. The message is sent to the API route.
3. The messages from the client are converted to AI SDK Core messages using `convertToCoreMessages`.
4. In your server-side route, the language model generates tool calls during the `streamText` call.
5. All tool calls are forwarded to the client.
6. Server-side tools are executed using their `execute` method and their results are forwarded to the client.
7. Client-side tools that should be automatically executed are handled with the `onToolCall` callback. You can return the tool result from the callback.
8. Client-side tools that require user interactions can be displayed in the UI. The tool calls and results are available in the `toolInvocations` property of the last assistant message.
9. When the user interaction is done, `addToolResult` can be used to add the tool result to the chat.
10. When there are tool calls in the last assistant message and all tool results are available, the client sends the updated messages back to the server. This triggers another iteration of this flow.

The tool call and tool executions are integrated into the assistant message as `toolInvocations`. A tool invocation is at first a tool call, and then it becomes a tool result when the tool is executed. The tool result contains all information about the tool call as well as the result of the tool execution.

In order to automatically send another request to the server when all tool calls are server-side, you need to set `maxToolRoundtrips` to a value greater than 0 in the `useChat` options. It is disabled by default for backward compatibility.

## Example

In this example, we'll use three tools:

1. `getWeatherInformation`: An automatically executed server-side tool that returns the weather in a given city.
2. `askForConfirmation`: A user-interaction client-side tool that asks the user for confirmation.
3. `getLocation`: An automatically executed client-side tool that returns a random city.

### API route

```typescript
// app/api/chat/route.ts
import { openai } from '@ai-sdk/openai';
import { convertToCoreMessages, streamText } from 'ai';
import { z } from 'zod';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = await streamText({
    model: openai('gpt-4-turbo'),
    messages: convertToCoreMessages(messages),
    tools: {
      // server-side tool with execute function:
      getWeatherInformation: {
        description: 'show the weather in a given city to the user',
        parameters: z.object({ city: z.string() }),
        execute: async ({}: { city: string }) => {
          const weatherOptions = ['sunny', 'cloudy', 'rainy', 'snowy', 'windy'];
          return weatherOptions[
            Math.floor(Math.random() * weatherOptions.length)
          ];
        },
      },
      // client-side tool that starts user interaction:
      askForConfirmation: {
        description: 'Ask the user for confirmation.',
        parameters: z.object({
          message: z.string().describe('The message to ask for confirmation.'),
        }),
      },
      // client-side tool that is automatically executed on the client:
      getLocation: {
        description:
          'Get the user location. Always ask for confirmation before using this tool.',
        parameters: z.object({}),
      },
    },
  });

  return result.toDataStreamResponse();
}
```

### Client-side page

The client-side page uses the `useChat` hook to create a chatbot application with real-time message streaming. Tool invocations are displayed in the chat UI.

There are three things worth mentioning:

1. The `onToolCall` callback is used to handle client-side tools that should be automatically executed. In this example, the `getLocation` tool is a client-side tool that returns a random city.
2. The `toolInvocations` property of the last assistant message contains all tool calls and results. The client-side tool `askForConfirmation` is displayed in the UI. It asks the user for confirmation and displays the result once the user confirms or denies the execution. The result is added to the chat using `addToolResult`.
3. The `maxToolRoundtrips` option is set to 5. This enables several tool use iterations between the client and the server.

```typescript
// app/page.tsx
'use client';

import { ToolInvocation } from 'ai';
import { Message, useChat } from 'ai/react';

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit, addToolResult } =
    useChat({
      maxToolRoundtrips: 5,

      // run client-side tools that are automatically executed:
      async onToolCall({ toolCall }) {
        if (toolCall.toolName === 'getLocation') {
          const cities = [
            'New York',
            'Los Angeles',
            'Chicago',
            'San Francisco',
          ];
          return cities[Math.floor(Math.random() * cities.length)];
        }
      },
    });

  return (
    <>
      {messages?.map((m: Message) => (
        <div key={m.id}>
          <strong>{m.role}:</strong>
          {m.content}
          {m.toolInvocations?.map((toolInvocation: ToolInvocation) => {
            const toolCallId = toolInvocation.toolCallId;
            const addResult = (result: string) =>
              addToolResult({ toolCallId, result });

            // render confirmation tool (client-side tool with user interaction)
            if (toolInvocation.toolName === 'askForConfirmation') {
              return (
                <div key={toolCallId}>
                  {toolInvocation.args.message}
                  <div>
                    {'result' in toolInvocation ? (
                      <b>{toolInvocation.result}</b>
                    ) : (
                      <>
                        <button onClick={() => addResult('Yes')}>Yes</button>
                        <button onClick={() => addResult('No')}>No</button>
                      </>
                    )}
                  </div>
                </div>
              );
            }

            // other tools:
            return 'result' in toolInvocation ? (
              <div key={toolCallId}>
                Tool call {`${toolInvocation.toolName}: `}
                {toolInvocation.result}
              </div>
            ) : (
              <div key={toolCallId}>Calling {toolInvocation.toolName}...</div>
            );
          })}
          <br />
        </div>
      ))}

      <form onSubmit={handleSubmit}>
        <input value={input} onChange={handleInputChange} />
      </form>
    </>
  );
}
```

### Tool call streaming

This feature is experimental.

You can stream tool calls while they are being generated by enabling the `experimental_toolCallStreaming` option in `streamText`.

```typescript
// app/api/chat/route.ts
export async function POST(req: Request) {
  // ...

  const result = await streamText({
    experimental_toolCallStreaming: true,
    // ...
  });

  return result.toDataStreamResponse();
}
```

When the flag is enabled, partial tool calls will be streamed as part of the AI stream. They are available through the `useChat` hook. The `toolInvocations` property of assistant messages will also contain partial tool calls. You can use the `state` property of the tool invocation to render the correct UI.

```typescript
// app/page.tsx
export default function Chat() {
  // ...
  return (
    <>
      {messages?.map((m: Message) => (
        <div key={m.id}>
          {m.toolInvocations?.map((toolInvocation: ToolInvocation) => {
            switch (toolInvocation.state) {
              case 'partial-call':
                return <>render partial tool call</>;
              case 'call':
                return <>render full tool call</>;
              case 'result':
                return <>render tool result</>;
            }
          })}
        </div>
      ))}
    </>
  );
}
```

# Completion

The `useCompletion` hook allows you to create a user interface to handle text completions in your application. It enables the streaming of text completions from your AI provider, manages the state for chat input, and updates the UI automatically as new messages are received.

In this guide, you will learn how to use the `useCompletion` hook in your application to generate text completions and stream them in real-time to your users.

## Example

### `app/page.tsx`

```typescript
'use client';

import { useCompletion } from 'ai/react';

export default function Page() {
  const { completion, input, handleInputChange, handleSubmit } = useCompletion({
    api: '/api/completion',
  });

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="prompt"
        value={input}
        onChange={handleInputChange}
        id="input"
      />
      <button type="submit">Submit</button>
      <div>{completion}</div>
    </form>
  );
}
```

### `app/api/completion/route.ts`

```typescript
import { streamText } from 'ai';
import { openai } from '@ai-sdk/openai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { prompt }: { prompt: string } = await req.json();

  const result = await streamText({
    model: openai('gpt-3.5-turbo'),
    prompt,
  });

  return result.toDataStreamResponse();
}
```

In the `Page` component, the `useCompletion` hook will request to your AI provider endpoint whenever the user submits a message. The completion is then streamed back in real-time and displayed in the UI.

This enables a seamless text completion experience where the user can see the AI response as soon as it is available, without having to wait for the entire response to be received.

## Customized UI

`useCompletion` also provides ways to manage the prompt via code, show loading and error states, and update messages without being triggered by user interactions.

### Loading and error states

To show a loading spinner while the chatbot is processing the user's message, you can use the `isLoading` state returned by the `useCompletion` hook:

```typescript
const { isLoading, ... } = useCompletion()

return(
  <>
    {isLoading ? <Spinner /> : null}
  </>
)
```

Similarly, the `error` state reflects the error object thrown during the fetch request. It can be used to display an error message, or show a toast notification:

```typescript
const { error, ... } = useCompletion()

useEffect(() => {
  if (error) {
    toast.error(error.message)
  }
}, [error])

// Or display the error message in the UI:
return (
  <>
    {error ? <div>{error.message}</div> : null}
  </>
)
```

### Controlled input

In the initial example, we have `handleSubmit` and `handleInputChange` callbacks that manage the input changes and form submissions. These are handy for common use cases, but you can also use uncontrolled APIs for more advanced scenarios such as form validation or customized components.

The following example demonstrates how to use more granular APIs like `setInput` with your custom input and submit button components:

```typescript
const { input, setInput } = useCompletion();

return (
  <>
    <MyCustomInput value={input} onChange={value => setInput(value)} />
  </>
);
```

### Cancelation

It's also a common use case to abort the response message while it's still streaming back from the AI provider. You can do this by calling the `stop` function returned by the `useCompletion` hook.

```typescript
const { stop, isLoading, ... } = useCompletion()

return (
  <>
    <button onClick={stop} disabled={!isLoading}>Stop</button>
  </>
)
```

When the user clicks the "Stop" button, the fetch request will be aborted. This avoids consuming unnecessary resources and improves the UX of your application.

### Event Callbacks

`useCompletion` also provides optional event callbacks that you can use to handle different stages of the chatbot lifecycle. These callbacks can be used to trigger additional actions, such as logging, analytics, or custom UI updates.

```typescript
const { ... } = useCompletion({
  onResponse: (response: Response) => {
    console.log('Received response from server:', response)
  },
  onFinish: (message: Message) => {
    console.log('Finished streaming message:', message)
  },
  onError: (error: Error) => {
    console.error('An error occurred:', error)
  },
})
```

It's worth noting that you can abort the processing by throwing an error in the `onResponse` callback. This will trigger the `onError` callback and stop the message from being appended to the chat UI. This can be useful for handling unexpected responses from the AI provider.

### Configure Request Options

By default, the `useCompletion` hook sends a HTTP POST request to the `/api/completion` endpoint with the prompt as part of the request body. You can customize the request by passing additional options to the `useCompletion` hook:

```typescript
const { messages, input, handleInputChange, handleSubmit } = useCompletion({
  api: '/api/custom-completion',
  headers: {
    Authorization: 'your_token',
  },
  body: {
    user_id: '123',
  },
  credentials: 'same-origin',
});
```

In this example, the `useCompletion` hook sends a POST request to the `/api/completion` endpoint with the specified headers, additional body fields, and credentials for that fetch request. On your server side, you can handle the request with these additional information.

# Object Generation

The `useObject` hook allows you to create interfaces that represent a structured JSON object that is being streamed.

In this guide, you will learn how to use the `useObject` hook in your application to generate UIs for structured data on the fly.

## Example

The example shows a small notifications demo app that generates fake notifications in real-time.

### Schema

It is helpful to set up the schema in a separate file that is imported on both the client and server.

**app/api/notifications/schema.ts**

```typescript
import { z } from 'zod';

// define a schema for the notifications
export const notificationSchema = z.object({
  notifications: z.array(
    z.object({
      name: z.string().describe('Name of a fictional person.'),
      message: z.string().describe('Message. Do not use emojis or links.'),
    }),
  ),
});
```

### Client

The client uses `useObject` to stream the object generation process.

The results are partial and are displayed as they are received. Please note the code for handling undefined values in the JSX.

**app/page.tsx**

```typescript
'use client';

import { experimental_useObject as useObject } from 'ai/react';
import { notificationSchema } from './api/notifications/schema';

export default function Page() {
  const { object, submit } = useObject({
    api: '/api/notifications',
    schema: notificationSchema,
  });

  return (
    <div>
      <button onClick={() => submit('Messages during finals week.')}>
        Generate notifications
      </button>

      {object?.notifications?.map((notification, index) => (
        <div key={index}>
          <p>{notification?.name}</p>
          <p>{notification?.message}</p>
        </div>
      ))}
    </div>
  );
}
```

### Server

On the server, we use `streamObject` to stream the object generation process.

**app/api/notifications/route.ts**

```typescript
import { openai } from '@ai-sdk/openai';
import { streamObject } from 'ai';
import { notificationSchema } from './schema';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const context = await req.json();

  const result = await streamObject({
    model: openai('gpt-4-turbo'),
    schema: notificationSchema,
    prompt:
      `Generate 3 notifications for a messages app in this context:` + context,
  });

  return result.toTextStreamResponse();
}
```

## Event Callbacks

`useObject` provides optional event callbacks that you can use to handle life-cycle events.

- `onFinish`: Called when the object generation is completed.
- `onError`: Called when an error occurs during the fetch request.

These callbacks can be used to trigger additional actions, such as logging, analytics, or custom UI updates.

**app/page.tsx**

```typescript
'use client';

import { experimental_useObject as useObject } from 'ai/react';
import { notificationSchema } from './api/notifications/schema';

export default function Page() {
  const { object, submit } = useObject({
    api: '/api/notifications',
    schema: notificationSchema,
    onFinish({ object, error }) {
      // typed object, undefined if schema validation fails:
      console.log('Object generation completed:', object);

      // error, undefined if schema validation succeeds:
      console.log('Schema validation error:', error);
    },
    onError(error) {
      // error during fetch request:
      console.error('An error occurred:', error);
    },
  });

  return (
    <div>
      <button onClick={() => submit('Messages during finals week.')}>
        Generate notifications
      </button>

      {object?.notifications?.map((notification, index) => (
        <div key={index}>
          <p>{notification?.name}</p>
          <p>{notification?.message}</p>
        </div>
      ))}
    </div>
  );
}
```

# Storing Messages

The ability to store message history is essential for chatbot use cases. The Vercel AI SDK simplifies the process of storing chat history through the `onFinish` callback of the `streamText` function.

`onFinish` is called after the model's response and all tool executions have completed. It provides the final text, tool calls, tool results, and usage information, making it an ideal place to, e.g., store the chat history in a database.

## Implementing Persistent Chat History

To implement persistent chat storage, you can utilize the `onFinish` callback on the `streamText` function. This callback is triggered upon the completion of the model's response and all tool executions, making it an ideal place to handle the storage of each interaction.

### API Route Example

```javascript
import { openai } from '@ai-sdk/openai';
import { streamText, convertToCoreMessages } from 'ai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = await streamText({
    model: openai('gpt-4-turbo'),
    messages: convertToCoreMessages(messages),
    async onFinish({ text, toolCalls, toolResults, usage, finishReason }) {
      // implement your own storage logic:
      await saveChat({ text, toolCalls, toolResults });
    },
  });

  return result.toDataStreamResponse();
}
```

### Server Action Example

```javascript
'use server';

import { openai } from '@ai-sdk/openai';
import { CoreMessage, streamText } from 'ai';

export async function continueConversation(messages: CoreMessage[]) {
  const result = await streamText({
    model: openai('gpt-4-turbo'),
    messages,
    async onFinish({ text, toolCalls, toolResults, finishReason, usage }) {
      // implement your own storage logic:
      await saveChat({ text, toolCalls, toolResults });
    },
  });

  return result.toDataStreamResponse();
}
```

# Streaming Data

Depending on your use case, you may want to stream additional data alongside the model's response. This can be achieved with `StreamData`.

## What is StreamData?

The `StreamData` class allows you to stream arbitrary data to the client alongside your LLM response. This can be particularly useful in applications that need to augment AI responses with metadata, auxiliary information, or custom data structures that are relevant to the ongoing interaction.

## How To Use StreamData

To use `StreamData`, create a `StreamData` value on the server, append some data, and then include it in `toDataStreamResponse`.

You need to call `close()` on the `StreamData` object to ensure the data is sent to the client. This can best be done in the `onFinish` callback of `streamText`.

On the client, the `useChat` hook returns `data`, which will contain the additional data.

### On the server

While this example uses Next.js (App Router), `StreamData` is compatible with any framework.

```javascript
import { openai } from '@ai-sdk/openai';
import { streamText, StreamData } from 'ai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  // Extract the `messages` from the body of the request
  const { messages } = await req.json();

  // Create a new StreamData
  const data = new StreamData();

  // Append additional data
  data.append({ test: 'value' });

  // Call the language model
  const result = await streamText({
    model: openai('gpt-4-turbo'),
    onFinish() {
      data.close();
    },
    messages,
  });

  // Respond with the stream and additional StreamData
  return result.toDataStreamResponse({ data });
}
```

### On the client

On the client, you can destructure `data` from the `useChat` hook, which stores all `StreamData` in an array. The `data` is of the type `JSONValue[]`.

```javascript
const { data } = useChat();
```
# Error Handling

## `useChat`: Keep Last Message on Error

`useChat` has a `keepLastMessageOnError` option that defaults to `false`. This option can be enabled to keep the last message on error. We will make this the default behavior in the next major release, and recommend enabling it.

## Error Helper Object

Each AI SDK UI hook also returns an error object that you can use to render the error in your UI. You can use the error object to show an error message, disable the submit button, or show a retry button.

We recommend showing a generic error message to the user, such as "Something went wrong." This is a good practice to avoid leaking information from the server.

```javascript
'use client';

import { useChat } from 'ai/react';

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit, error, reload } =
    useChat({ keepLastMessageOnError: true });

  return (
    <div>
      {messages.map(m => (
        <div key={m.id}>
          {m.role}: {m.content}
        </div>
      ))}

      {error && (
        <>
          <div>An error occurred.</div>
          <button type="button" onClick={() => reload()}>
            Retry
          </button>
        </>
      )}

      <form onSubmit={handleSubmit}>
        <input
          value={input}
          onChange={handleInputChange}
          disabled={error != null}
        />
      </form>
    </div>
  );
}
```

## Alternative: Replace Last Message

Alternatively, you can write a custom submit handler that replaces the last message when an error is present.

```javascript
'use client';

import { useChat } from 'ai/react';

export default function Chat() {
  const {
    handleInputChange,
    handleSubmit,
    error,
    input,
    messages,
    setMesages,
  } = useChat({ keepLastMessageOnError: true });

  function customSubmit(event: React.FormEvent<HTMLFormElement>) {
    if (error != null) {
      setMessages(messages.slice(0, -1)); // remove last message
    }

    handleSubmit(event);
  }

  return (
    <div>
      {messages.map(m => (
        <div key={m.id}>
          {m.role}: {m.content}
        </div>
      ))}

      {error && <div>An error occurred.</div>}

      <form onSubmit={customSubmit}>
        <input value={input} onChange={handleInputChange} />
      </form>
    </div>
  );
}
```

## Error Handling Callback

Errors can be processed by passing an `onError` callback function as an option to the `useChat`, `useCompletion`, or `useAssistant` hooks. The callback function receives an error object as an argument.

```javascript
import { useChat } from 'ai/react';

export default function Page() {
  const {
    /* ... */
  } = useChat({
    keepLastMessageOnError: true,
    // handle error:
    onError: error => {
      console.error(error);
    },
  });
}
```

## Injecting Errors for Testing

You might want to create errors for testing. You can easily do so by throwing an error in your route handler:

```javascript
export async function POST(req: Request) {
  throw new Error('This is a test error');
}
```

# Stream Protocols

AI SDK UI functions such as `useChat` and `useCompletion` support both text streams and data streams. The stream protocol defines how the data is streamed to the frontend on top of the HTTP protocol.

This page describes both protocols and how to use them in the backend and frontend.

You can use this information to develop custom backends and frontends for your use case, e.g., to provide compatible API endpoints that are implemented in a different language such as Python.

For instance, here's an example using FastAPI as a backend.

## Text Stream Protocol

A text stream contains chunks in plain text, that are streamed to the frontend. Each chunk is then appended together to form a full text response.

Text streams are supported by `useChat`, `useCompletion`, and `useObject`. When you use `useChat` or `useCompletion`, you need to enable text streaming by setting the `streamProtocol` options to `text`.

You can generate text streams with `streamText` in the backend. When you call `toTextStreamResponse()` on the result object, a streaming HTTP response is returned.

Text streams only support basic text data. If you need to stream other types of data such as tool calls, use data streams.

### Text Stream Example

Here is a Next.js example that uses the text stream protocol:

```typescript
// app/page.tsx
'use client';

import { useCompletion } from 'ai/react';

export default function Page() {
  const { completion, input, handleInputChange, handleSubmit } = useCompletion({
    streamProtocol: 'text',
  });

  return (
    <form onSubmit={handleSubmit}>
      <input name="prompt" value={input} onChange={handleInputChange} />
      <button type="submit">Submit</button>
      <div>{completion}</div>
    </form>
  );
}
```

```typescript
// app/api/completion/route.ts
import { streamText } from 'ai';
import { openai } from '@ai-sdk/openai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { prompt }: { prompt: string } = await req.json();

  const result = await streamText({
    model: openai('gpt-4o'),
    prompt,
  });

  return result.toTextStreamResponse();
}
```

## Data Stream Protocol

A data stream follows a special protocol that the Vercel AI SDK provides to send information to the frontend.

Each stream part has the format `TYPE_ID:CONTENT_JSON\n`.

When you provide data streams from a custom backend, you need to set the `x-vercel-ai-data-stream` header to `v1`.

The following stream parts are currently supported:

### Text Part

The text parts are appended to the message as they are received.

Format: `0:string\n`

Example: `0:"example"\n`

### Data Part

The data parts are parsed as JSON and appended to the message as they are received. The data part is available through `data`.

Format: `2:Array<JSONValue>\n`

Example: `2:[{"key":"object1"},{"anotherKey":"object2"}]\n`

### Error Part

The error parts are appended to the message as they are received.

Format: `3:string\n`

Example: `3:"error message"\n`

### Tool Call Streaming Start Part

A part indicating the start of a streaming tool call. This part needs to be sent before any tool call delta for that tool call. Tool call streaming is optional, you can use tool call and tool result parts without it.

Format: `b:{toolCallId:string; toolName:string}\n`

Example: `b:{"toolCallId":"call-456","toolName":"streaming-tool"}\n`

### Tool Call Delta Part

A part representing a delta update for a streaming tool call.

Format: `c:{toolCallId:string; argsTextDelta:string}\n`

Example: `c:{"toolCallId":"call-456","argsTextDelta":"partial arg"}\n`

### Tool Call Part

A part representing a tool call. When there are streamed tool calls, the tool call part needs to come after the tool call streaming is finished.

Format: `9:{toolCallId:string; toolName:string; args:object}\n`

Example: `9:{"toolCallId":"call-123","toolName":"my-tool","args":{"some":"argument"}}\n`

### Tool Result Part

A part representing a tool result. The result part needs to be sent after the tool call part for that tool call.

Format: `a:{toolCallId:string; result:object}\n`

Example: `a:{"toolCallId":"call-123","result":"tool output"}\n`

### Finish Message Part

A part indicating the completion of a message with additional metadata, such as `FinishReason` and `Usage`. This part needs to be the last part in the stream.

Format: `d:{finishReason:'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown';usage:{promptTokens:number; completionTokens:number;}}\n`

Example: `d:{"finishReason":"stop","usage":{"promptTokens":10,"completionTokens":20}}\n`

The data stream protocol is supported by `useChat` and `useCompletion` on the frontend and used by default. `useCompletion` only supports the text and data stream parts.

On the backend, you can use `toDataStreamResponse()` from the `streamText` result object to return a streaming HTTP response.

### Data Stream Example

Here is a Next.js example that uses the data stream protocol:

```typescript
// app/page.tsx
'use client';

import { useCompletion } from 'ai/react';

export default function Page() {
  const { completion, input, handleInputChange, handleSubmit } = useCompletion({
    streamProtocol: 'data', // optional, this is the default
  });

  return (
    <form onSubmit={handleSubmit}>
      <input name="prompt" value={input} onChange={handleInputChange} />
      <button type="submit">Submit</button>
      <div>{completion}</div>
    </form>
  );
}
```

```typescript
// app/api/completion/route.ts
import { streamText } from 'ai';
import { openai } from '@ai-sdk/openai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { prompt }: { prompt: string } = await req.json();

  const result = await streamText({
    model: openai('gpt-4o'),
    prompt,
  });

  return result.toDataStreamResponse();
}
```

# API Reference
## AI SDK UI

AI SDK UI is designed to help you build interactive chat, completion, and assistant applications with ease. It is a framework-agnostic toolkit, streamlining the integration of advanced AI functionalities into your applications.

AI SDK UI contains the following hooks:

## useChat
Use a hook to interact with language models in a chat interface.

## useCompletion
Use a hook to interact with language models in a completion interface.

## useObject
Use a hook for consuming a streamed JSON objects.

## useAssistant
Use a hook to interact with OpenAI assistants.

# `useChat()`

Allows you to easily create a conversational user interface for your chatbot application. It enables the streaming of chat messages from your AI provider, manages the state for chat input, and updates the UI automatically as new messages are received.

## Import

- React
- Svelte
- Vue
- Solid

```javascript
import { useChat } from 'ai/react'
```

## API Signature

### Parameters

`api`: 
*string* = '/api/chat'
The chat completion API endpoint offered by the provider.

`keepLastMessageOnError`:
*boolean*
Keeps the last message when an error happens. This will be the default behavior starting with the next major release. The flag was introduced for backwards compatibility and currently defaults to `false`. Please enable it and update your error handling/resubmit behavior.

`id`:
*string*
An unique identifier for the chat. If not provided, a random one will be generated. When provided, the `useChat` hook with the same `id` will have shared states across components. This is useful when you have multiple components showing the same chat stream.

`initialInput`:
*string* = ''
An optional string for the initial prompt input.

`initialMessages`:
*Messages[]* = []
An optional array of initial chat messages.

`onToolCall`:
*({toolCall: ToolCall}) => void | unknown | Promise<unknown>*
Optional callback function that is invoked when a tool call is received. Intended for automatic client-side tool execution. You can optionally return a result for the tool call, either synchronously or asynchronously.

`onResponse`:
*(response: Response) => void*
An optional callback that will be called with the response from the API endpoint. Useful for throwing customized errors or logging.

`onFinish`:
*(message: Message, options: OnFinishOptions) => void*
An optional callback function that is called when the completion stream ends.

#### `OnFinishOptions`

`usage`:
*CompletionTokenUsage*
The token usage for the completion.

`CompletionTokenUsage`

`promptTokens`:
*number*
The total number of tokens in the prompt.

`completionTokens`:
*number*
The total number of tokens in the completion.

`totalTokens`:
*number*
The total number of tokens generated.

`finishReason`:
*'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown'*
The reason why the generation ended.

`onError`:
*(error: Error) => void*
An optional callback that will be called when the chat stream encounters an error.

`generateId`:
*() => string*
Optional. A way to provide a function that is going to be used for ids for messages. If not provided, `generateId` is used by default.

`headers`:
*Record<string, string> | Headers*
An optional object of headers to be passed to the API endpoint.

`body`:
*any*
An optional, additional body object to be passed to the API endpoint.

`credentials`:
*'omit' | 'same-origin' | 'include'*
An optional literal that sets the mode of credentials to be used on the request. Defaults to `same-origin`.

`sendExtraMessageFields`:
*boolean*
An optional boolean that determines whether to send extra fields you've added to `messages`. Defaults to `false` and only the `content` and `role` fields will be sent to the API endpoint. If set to `true`, the `name`, `data`, and `annotations` fields will also be sent.

`maxToolRoundtrips`:
*number*
React and SolidJS only. Maximal number of automatic roundtrips for tool calls. An automatic tool call roundtrip is a call to the server with the tool call results when all tool calls in the last assistant message have results. A maximum number is required to prevent infinite loops in the case of misconfigured tools. By default, it is set to 0, which will disable the feature.

`streamProtocol`:
*'text' | 'data'*
An optional literal that sets the type of stream to be used. Defaults to `data`. If set to `text`, the stream will be treated as a text stream.

`fetch`:
*FetchFunction*
Optional. A custom fetch function to be used for the API call. Defaults to the global `fetch` function.

`experimental_prepareRequestBody`:
*(options: { messages: Message[]; requestData?: JSONValue; requestBody?: object }) => JSONValue*
Experimental (React only). When a function is provided, it will be used to prepare the request body for the chat API. This can be useful for customizing the request body based on the messages and data in the chat.

### Returns

`messages`:
*Message[]*
The current array of chat messages.

#### `Message`

`id`:
*string*
The unique identifier of the message.

`role`:
*'system' | 'user' | 'assistant' | 'data'*
The role of the message.

`content`:
*string*
The content of the message.

`createdAt`?:
*Date*
The creation date of the message.

`name`?:
*string*
The name of the message.

`data`?:
*JSONValue*
Additional data sent along with the message.

`annotations`?:
*Array<JSONValue>*
Additional annotations sent along with the message.

`toolInvocations`?:
*Array<ToolInvocation>*
An array of tool invocations that are associated with the (assistant) message.

#### `ToolInvocation`

`state`:
*'partial-call'*
The state of the tool call when it was partially created.

`toolCallId`:
*string*
ID of the tool call. This ID is used to match the tool call with the tool result.

`toolName`:
*string*
Name of the tool that is being called.

`args`:
*any*
Partial arguments of the tool call. This is a JSON-serializable object.

#### `ToolInvocation`

`state`:
*'call'*
The state of the tool call when it was fully created.

`toolCallId`:
*string*
ID of the tool call. This ID is used to match the tool call with the tool result.

`toolName`:
*string*
Name of the tool that is being called.

`args`:
*any*
Arguments of the tool call. This is a JSON-serializable object that matches the tools input schema.

#### `ToolInvocation`

`state`:
*'result'*
The state of the tool call when the result is available.

`toolCallId`:
*string*
ID of the tool call. This ID is used to match the tool call with the tool result.

`toolName`:
*string*
Name of the tool that is being called.

`args`:
*any*
Arguments of the tool call. This is a JSON-serializable object that matches the tools input schema.

`result`:
*any*
The result of the tool call.

`experimental_attachments`?:
*Array<Attachment>*
Additional attachments sent along with the message.

#### `Attachment`

`name`?:
*string*
The name of the attachment, usually the file name.

`contentType`?:
*string*
A string indicating the media type of the file.

`url`:
*string*
The URL of the attachment. It can either be a URL to a hosted file or a Data URL.

`error`:
*Error | undefined*
An error object returned by SWR, if any.

`append`:
*(message: Message | CreateMessage, options?: ChatRequestOptions) => Promise<string | undefined>*
Function to append a message to the chat, triggering an API call for the AI response. It returns a promise that resolves to full response message content when the API call is successfully finished, or throws an error when the API call fails.

#### `ChatRequestOptions`

`headers`:
*Record<string, string> | Headers*
Additional headers that should be to be passed to the API endpoint.

`body`:
*object*
Additional body JSON properties that should be sent to the API endpoint.

`data`:
*JSONValue*
Additional data to be sent to the API endpoint.

`reload`:
*() => Promise<string | undefined>*
Function to reload the last AI chat response for the given chat history. If the last message isn't from the assistant, it will request the API to generate a new response.

`stop`:
*() => void*
Function to abort the current API request.

`setMessages`:
*(messages: Message[] | ((messages: Message[]) => Message[]) => void*
Function to update the `messages` state locally without triggering an API call.

`input`:
*string*
The current value of the input field.

`setInput`:
*React.Dispatch<React.SetStateAction<string>>*
Function to update the `input` value.

`handleInputChange`:
*(event: any) => void*
Handler for the `onChange` event of the input field to control the input's value.

`handleSubmit`:
*(event?: { preventDefault?: () => void }, options?: ChatRequestOptions) => void*
Form submission handler that automatically resets the input field and appends a user message. You can use the `options` parameter to send additional data, headers and more to the server.

#### `ChatRequestOptions`

`headers`:
*Record<string, string> | Headers*
Additional headers that should be to be passed to the API endpoint.

`body`:
*object*
Additional body JSON properties that should be sent to the API endpoint.

`data`:
*JSONValue*
Additional data to be sent to the API endpoint.

`allowEmptySubmit`?:
*boolean*
A boolean that determines whether to allow submitting an empty input that triggers a generation. Defaults to `false`.

`experimental_attachments`?:
*FileList | Array<Attachment>*
An array of attachments to be sent to the API endpoint.

`isLoading`:
*boolean*
Boolean flag indicating whether a request is currently in progress.

`data`:
*JSONValue[]*
Data returned from StreamData

`addToolResult`:
*({toolCallId: string; result: any;}) => void*
React and SolidJS only. Function to add a tool result to the chat. This will update the chat messages with the tool result and call the API route if all tool results for the last message are available.

# useCompletion()

Allows you to create text completion based capabilities for your application. It enables the streaming of text completions from your AI provider, manages the state for chat input, and updates the UI automatically as new messages are received.

## Import

- React
- Svelte
- Vue
- Solid

```javascript
import { useCompletion } from 'ai/react'
```

## API Signature

### Parameters

`api`:
: `string = '/api/completion'`
  The text completion API endpoint offered by the provider.

`id`:
: `string`
  An unique identifier for the completion. If not provided, a random one will be generated. When provided, the `useCompletion` hook with the same `id` will have shared states across components. This is useful when you have multiple components showing the same chat stream.

`initialInput`:
: `string`
  An optional string for the initial prompt input.

`initialCompletion`:
: `string`
  An optional string for the initial completion result.

`onResponse`:
: `(response: Response) => void`
  An optional callback function that is called with the response from the API endpoint. Useful for throwing customized errors or logging.

`onFinish`:
: `(prompt: string, completion: string) => void`
  An optional callback function that is called when the completion stream ends.

`onError`:
: `(error: Error) => void`
  An optional callback that will be called when the chat stream encounters an error.

`headers`:
: `Record<string, string> | Headers`
  An optional object of headers to be passed to the API endpoint.

`body`:
: `any`
  An optional, additional body object to be passed to the API endpoint.

`credentials`:
: `'omit' | 'same-origin' | 'include'`
  An optional literal that sets the mode of credentials to be used on the request. Defaults to same-origin.

`sendExtraMessageFields`:
: `boolean`
  An optional boolean that determines whether to send extra fields you've added to `messages`. Defaults to `false` and only the `content` and `role` fields will be sent to the API endpoint.

`streamProtocol`:
: `'text' | 'data'`
  An optional literal that sets the type of stream to be used. Defaults to `data`. If set to `text`, the stream will be treated as a text stream.

`fetch`:
: `FetchFunction`
  Optional. A custom fetch function to be used for the API call. Defaults to the global fetch function.

### Returns

`completion`:
: `string`
  The current text completion.

`complete`:
: `(prompt: string, options: { headers, body }) => void`
  Function to execute text completion based on the provided prompt.

`error`:
: `undefined | Error`
  The error thrown during the completion process, if any.

`setCompletion`:
: `(completion: string) => void`
  Function to update the `completion` state.

`stop`:
: `() => void`
  Function to abort the current API request.

`input`:
: `string`
  The current value of the input field.

`setInput`:
: `React.Dispatch<React.SetStateAction<string>>`
  The current value of the input field.

`handleInputChange`:
: `(event: any) => void`
  Handler for the `onChange` event of the input field to control the input's value.

`handleSubmit`:
: `(event?: { preventDefault?: () => void }) => void`
  Form submission handler that automatically resets the input field and appends a user message.

`isLoading`:
: `boolean`
  Boolean flag indicating whether a fetch operation is currently in progress.

  # `experimental_useObject()`

`useObject` is an *experimental feature* and only available in React. It allows you to consume text streams that represent a JSON object and parse them into a complete object based on a schema. You can use it together with `streamObject` in the backend.

```javascript
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

## Import

```javascript
import { experimental_useObject as useObject } from 'ai/react'
```

## API Signature

### Parameters

- `api`: `string`
  The API endpoint. It should stream JSON that matches the schema as chunked text.
- `schema`: `Zod Schema | JSON Schema`
  A schema that defines the shape of the complete object. You can either pass in a Zod schema or a JSON schema (using the `jsonSchema` function).
- `id?`: `string`
  A unique identifier. If not provided, a random one will be generated. When provided, the `useObject` hook with the same `id` will have shared states across components.
- `initialValue?`: `DeepPartial<RESULT> | undefined`
  An optional value for the initial object.
- `fetch`: `FetchFunction`
  Optional. A custom fetch function to be used for the API call. Defaults to the global `fetch` function.
- `onError`: `(error: Error) => void`
  Optional. Callback function to be called when an error is encountered.
- `onFinish?`: `(result: OnFinishResult) => void`
  Called when the streaming response has finished.

### `OnFinishResult`

- `object`: `T | undefined`
  The generated object (typed according to the schema). Can be `undefined` if the final object does not match the schema.
- `error`: `unknown | undefined`
  Optional error object. This is e.g. a `TypeValidationError` when the final object does not match the schema.

### Returns

- `submit`: `(input: INPUT) => void`
  Calls the API with the provided input as JSON body.
- `object`: `DeepPartial<RESULT> | undefined`
  The current value for the generated object. Updated as the API streams JSON chunks.
- `error`: `undefined | unknown`
  The error object if the API call fails.
- `isLoading`: `boolean`
  Boolean flag indicating whether a request is currently in progress.
- `stop`: `() => void`
  Function to abort the current API request.

  # `convertToCoreMessages()`

The `convertToCoreMessages` function is used to transform an array of UI messages from the `useChat` hook into an array of `CoreMessage` objects. These `CoreMessage` objects are compatible with AI core functions like `streamText`.

## `app/api/chat/route.ts`

```typescript
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

## Import

```typescript
import { convertToCoreMessages } from "ai"
```

## API Signature

### Parameters

`messages`: `Message[]`
An array of UI messages from the `useChat` hook to be converted

### Returns

`CoreMessage[]`: An array of `CoreMessage` objects.

The hooks, e.g. `useChat` or `useCompletion`, provide a `stop` helper function that can be used to cancel a stream. This will cancel the stream from the client side to the server.

```javascript
'use client';

import { useCompletion } from 'ai/react';

export default function Chat() {
  const {
    input,
    completion,
    stop,
    isLoading,
    handleSubmit,
    handleInputChange,
  } = useCompletion();

  return (
    <div>
      {isLoading && (
        <button type="button" onClick={() => stop()}>
          Stop
        </button>
      )}
      {completion}
      <form onSubmit={handleSubmit}>
        <input value={input} onChange={handleInputChange} />
      </form>
    </div>
  );
}
```

# Stream Back-pressure and Cancellation

This page focuses on understanding back-pressure and cancellation when working with streams. You do not need to know this information to use the Vercel AI SDK, but for those interested, it offers a deeper dive on why and how the SDK optimally streams responses.

In the following sections, we'll explore back-pressure and cancellation in the context of a simple example program. We'll discuss the issues that can arise from an eager approach and demonstrate how a lazy approach can resolve them.

## Back-pressure and Cancellation with Streams

Let's begin by setting up a simple example program:

```javascript
// A generator that will yield positive integers
async function* integers() {
  let i = 1;
  while (true) {
    console.log(`yielding ${i}`);
    yield i++;

    await sleep(100);
  }
}
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

// Wraps a generator into a ReadableStream
function createStream(iterator) {
  return new ReadableStream({
    async start(controller) {
      for await (const v of iterator) {
        controller.enqueue(v);
      }
      controller.close();
    },
  });
}

// Collect data from stream
async function run() {
  // Set up a stream of integers
  const stream = createStream(integers());

  // Read values from our stream
  const reader = stream.getReader();
  for (let i = 0; i < 10_000; i++) {
    // we know our stream is infinite, so there's no need to check `done`.
    const { value } = await reader.read();
    console.log(`read ${value}`);

    await sleep(1_000);
  }
}
run();
```

In this example, we create an async-generator that yields positive integers, a `ReadableStream` that wraps our integer generator, and a reader which will read values out of our stream. Notice, too, that our integer generator logs out "yielding ${i}", and our reader logs out "read ${value}". Both take an arbitrary amount of time to process data, represented with a 100ms sleep in our generator, and a 1sec sleep in our reader.

### Back-pressure

If you were to run this program, you'd notice something funny. We'll see roughly 10 "yield" logs for every "read" log. This might seem obvious, the generator can push values 10x faster than the reader can pull them out. But it represents a problem, our stream has to maintain an ever expanding queue of items that have been pushed in but not pulled out.

The problem stems from the way we wrap our generator into a stream. Notice the use of `for await (...)` inside our `start` handler. This is an eager for-loop, and it is constantly running to get the next value from our generator to be enqueued in our stream. This means our stream does not respect back-pressure, the signal from the consumer to the producer that more values aren't needed yet. We've essentially spawned a thread that will perpetually push more data into the stream, one that runs as fast as possible to push new data immediately. Worse, there's no way to signal to this thread to stop running when we don't need additional data.

To fix this, `ReadableStream` allows a `pull` handler. `pull` is called every time the consumer attempts to read more data from our stream (if there's no data already queued internally). But it's not enough to just move the `for await (...)` into `pull`, we also need to convert from an eager enqueuing to a lazy one. By making these 2 changes, we'll be able to react to the consumer. If they need more data, we can easily produce it, and if they don't, then we don't need to spend any time doing unnecessary work.

```javascript
function createStream(iterator) {
  return new ReadableStream({
    async pull(controller) {
      const { value, done } = await iterator.next();

      if (done) {
        controller.close();
      } else {
        controller.enqueue(value);
      }
    },
  });
}
```

Our `createStream` is a little more verbose now, but the new code is important. First, we need to manually call our `iterator.next()` method. This returns a Promise for an object with the type signature `{ done: boolean, value: T }`. If `done` is `true`, then we know that our iterator won't yield any more values and we must close the stream (this allows the consumer to know that the stream is also finished producing values). Else, we need to enqueue our newly produced value.

When we run this program, we see that our "yield" and "read" logs are now paired. We're no longer yielding 10x integers for every read! And, our stream now only needs to maintain 1 item in its internal buffer. We've essentially given control to the consumer, so that it's responsible for producing new values as it needs it. Neato!

### Cancellation

Let's go back to our initial eager example, with 1 small edit. Now instead of reading 10,000 integers, we're only going to read 3:

```javascript
// A generator that will yield positive integers
async function* integers() {
  let i = 1;
  while (true) {
    console.log(`yielding ${i}`);
    yield i++;

    await sleep(100);
  }
}
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

// Wraps a generator into a ReadableStream
function createStream(iterator) {
  return new ReadableStream({
    async start(controller) {
      for await (const v of iterator) {
        controller.enqueue(v);
      }
      controller.close();
    },
  });
}
// Collect data from stream
async function run() {
  // Set up a stream that of integers
  const stream = createStream(integers());

  // Read values from our stream
  const reader = stream.getReader();
  // We're only reading 3 items this time:
  for (let i = 0; i < 3; i++) {
    // we know our stream is infinite, so there's no need to check `done`.
    const { value } = await reader.read();
    console.log(`read ${value}`);

    await sleep(1000);
  }
}
run();
```

We're back to yielding 10x the number of values read. But notice now, after we've read 3 values, we're continuing to yield new values. We know that our reader will never read another value, but our stream doesn't! The eager `for await (...)` will continue forever, loudly enqueuing new values into our stream's buffer and increasing our memory usage until it consumes all available program memory.

The fix to this is exactly the same: use `pull` and manual iteration. By producing values lazily, we tie the lifetime of our integer generator to the lifetime of the reader. Once the reads stop, the yields will stop too:

```javascript
// Wraps a generator into a ReadableStream
function createStream(iterator) {
  return new ReadableStream({
    async pull(controller) {
      const { value, done } = await iterator.next();

      if (done) {
        controller.close();
      } else {
        controller.enqueue(value);
      }
    },
  });
}
```

Since the solution is the same as implementing back-pressure, it shows that they're just 2 facets of the same problem: Pushing values into a stream should be done lazily, and doing it eagerly results in expected problems.

## Tying Stream Laziness to AI Responses

Now let's imagine you're integrating AIBot service into your product. Users will be able to prompt "count from 1 to infinity", the browser will fetch your AI API endpoint, and your servers connect to AIBot to get a response. But "infinity" is, well, infinite. The response will never end!

After a few seconds, the user gets bored and navigates away. Or maybe you're doing local development and a hot-module reload refreshes your page. The browser will have ended its connection to the API endpoint, but will your server end its connection with AIBot?

If you used the eager `for await (...)` approach, then the connection is still running and your server is asking for more and more data from AIBot. Our server spawned a "thread" and there's no signal when we can end the eager pulls. Eventually, the server is going to run out of memory (remember, there's no active fetch connection to read the buffering responses and free them).

With the lazy approach, this is taken care of for you. Because the stream will only request new data from AIBot when the consumer requests it, navigating away from the page naturally frees all resources. The fetch connection aborts and the server can clean up the response. The `ReadableStream` tied to that response can now be garbage collected. When that happens, the connection it holds to AIBot can then be freed.

# Caching Responses

Depending on the type of application you're building, you may want to cache the responses you receive from your AI provider, at least temporarily.

Each stream helper for each provider has special lifecycle callbacks you can use. The one of interest is likely `onFinish`, which is called when the stream is closed. This is where you can cache the full response.

Here's an example of how you can implement caching using Vercel KV and Next.js to cache the OpenAI response for 1 hour:

## Example: Vercel KV

This example uses Vercel KV and Next.js to cache the OpenAI response for 1 hour.

```typescript
// app/api/chat/route.ts

import { openai } from '@ai-sdk/openai';
import { convertToCoreMessages, formatStreamPart, streamText } from 'ai';
import kv from '@vercel/kv';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

// simple cache implementation, use Vercel KV or a similar service for production
const cache = new Map<string, string>();

export async function POST(req: Request) {
  const { messages } = await req.json();

  // come up with a key based on the request:
  const key = JSON.stringify(messages);

  // Check if we have a cached response
  const cached = await kv.get(key);
  if (cached != null) {
    return new Response(formatStreamPart('text', cached), {
      status: 200,
      headers: { 'Content-Type': 'text/plain' },
    });
  }

  // Call the language model:
  const result = await streamText({
    model: openai('gpt-4o'),
    messages: convertToCoreMessages(messages),
    async onFinish({ text }) {
      // Cache the response text:
      await kv.set(key, text);
      await kv.expire(key, 60 * 60);
    },
  });

  // Respond with the stream
  return result.toDataStreamResponse();
}
```

## Multiple Streams
### Multiple Streamable UIs
The AI SDK RSC APIs allow you to compose and return any number of streamable UIs, along with other data, in a single request. This can be useful when you want to decouple the UI into smaller components and stream them separately.

```javascript
'use server';

import { createStreamableUI } from 'ai/rsc';

export async function getWeather() {
  const weatherUI = createStreamableUI();
  const forecastUI = createStreamableUI();

  weatherUI.update(<div>Loading weather...</div>);
  forecastUI.update(<div>Loading forecast...</div>);

  getWeatherData().then(weatherData => {
    weatherUI.done(<div>{weatherData}</div>);
  });

  getForecastData().then(forecastData => {
    forecastUI.done(<div>{forecastData}</div>);
  });

  // Return both streamable UIs and other data fields.
  return {
    requestedAt: Date.now(),
    weather: weatherUI.value,
    forecast: forecastUI.value,
  };
}
```

The client-side code is similar to the previous example, but the tool call will return the new data structure with the weather and forecast UIs. Depending on the speed of getting weather and forecast data, these two components might be updated independently.

### Nested Streamable UIs
You can stream UI components within other UI components. This allows you to create complex UIs that are built up from smaller, reusable components. In the example below, we pass a `historyChart` streamable as a prop to a `StockCard` component. The `StockCard` can render the `historyChart` streamable, and it will automatically update as the server responds with new data.

```javascript
async function getStockHistoryChart({ symbol: string }) {
  'use server';

  const ui = createStreamableUI(<Spinner />);

  // We need to wrap this in an async IIFE to avoid blocking.
  (async () => {
    const price = await getStockPrice({ symbol });

    // Show a spinner as the history chart for now.
    const historyChart = createStreamableUI(<Spinner />);
    ui.done(<StockCard historyChart={historyChart.value} price={price} />);

    // Getting the history data and then update that part of the UI.
    const historyData = await fetch('https://my-stock-data-api.com');
    historyChart.done(<HistoryChart data={historyData} />);
  })();

  return ui;
}
```

# Rate Limiting

Rate limiting helps you protect your APIs from abuse. It involves setting a maximum threshold on the number of requests a client can make within a specified timeframe. This simple technique acts as a gatekeeper, preventing excessive usage that can degrade service performance and incur unnecessary costs.

## Rate Limiting with Vercel KV and Upstash Ratelimit

In this example, you will protect an API endpoint using Vercel KV and Upstash Ratelimit.

### `app/api/generate/route.ts`

```typescript
import kv from '@vercel/kv';
import { openai } from '@ai-sdk/openai';
import { streamText } from 'ai';
import { Ratelimit } from '@upstash/ratelimit';
import { NextRequest } from 'next/server';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

// Create Rate limit
const ratelimit = new Ratelimit({
  redis: kv,
  limiter: Ratelimit.fixedWindow(5, '30s'),
});

export async function POST(req: NextRequest) {
  // call ratelimit with request ip
  const ip = req.ip ?? 'ip';
  const { success, remaining } = await ratelimit.limit(ip);

  // block the request if unsuccessfull
  if (!success) {
    return new Response('Ratelimited!', { status: 429 });
  }

  const { messages } = await req.json();

  const result = await streamText({
    model: openai('gpt-3.5-turbo'),
    messages,
  });

  return result.toDataStreamResponse();
}
```

## Simplify API Protection

With Vercel KV and Upstash Ratelimit, it is possible to protect your APIs from such attacks with ease. To learn more about how Ratelimit works and how it can be configured to your needs, see [Ratelimit Documentation](https://docs.upstash.com/ratelimit).

# Rendering User Interfaces with Language Models

Language models generate text, so at first it may seem like you would only need to render text in your application.

## `app/actions.tsx`

```typescript
const text = generateText({
  model: openai('gpt-3.5-turbo'),
  system: 'You are a friendly assistant',
  prompt: 'What is the weather in SF?',
  tools: {
    getWeather: {
      description: 'Get the weather for a location',
      parameters: z.object({
        city: z.string().describe('The city to get the weather for'),
        unit: z
          .enum(['C', 'F'])
          .describe('The unit to display the temperature in'),
      }),
      execute: async ({ city, unit }) => {
        const weather = getWeather({ city, unit });
        return `It is currently ${weather.value}°${unit} and ${weather.description} in ${city}!`;
      },
    },
  },
});
```

Above, the language model is passed a tool called `getWeather` that returns the weather information as text. However, instead of returning text, if you return a JSON object that represents the weather information, you can use it to render a React component instead.

## `app/action.ts`

```typescript
const text = generateText({
  model: openai('gpt-3.5-turbo'),
  system: 'You are a friendly assistant',
  prompt: 'What is the weather in SF?',
  tools: {
    getWeather: {
      description: 'Get the weather for a location',
      parameters: z.object({
        city: z.string().describe('The city to get the weather for'),
        unit: z
          .enum(['C', 'F'])
          .describe('The unit to display the temperature in'),
      }),
      execute: async ({ city, unit }) => {
        const weather = getWeather({ city, unit });
        const { temperature, unit, description, forecast } = weather;

        return {
          temperature,
          unit,
          description,
          forecast,
        };
      },
    },
  },
});
```

Now you can use the object returned by the `getWeather` function to conditionally render a React component `<WeatherCard/>` that displays the weather information by passing the object as props.

## `app/page.tsx`

```typescript
return (
  <div>
    {messages.map(message => {
      if (message.role === 'function') {
        const { name, content } = message
        const { temperature, unit, description, forecast } = content;

        return (
          <WeatherCard
            weather={{
              temperature: 47,
              unit: 'F',
              description: 'sunny'
              forecast,
            }}
          />
        )
      }
    })}
  </div>
)
```

Rendering interfaces as part of language model generations elevates the user experience of your application, allowing people to interact with language models beyond text.

They also make it easier for you to interpret sequential tool calls that take place in multiple steps and help identify and debug where the model reasoned incorrectly.

## Rendering Multiple User Interfaces

To recap, an application has to go through the following steps to render user interfaces as part of model generations:

1. The user prompts the language model.
2. The language model generates a response that includes a tool call.
3. The tool call returns a JSON object that represents the user interface.
4. The response is sent to the client.
5. The client receives the response and checks if the latest message was a tool call.
6. If it was a tool call, the client renders the user interface based on the JSON object returned by the tool call.

Most applications have multiple tools that are called by the language model, and each tool can return a different user interface. As this list grows, the complexity of your application will grow as well and it can become increasingly difficult to manage these user interfaces.

## `app/page.tsx`

```typescript
{
  message.role === 'tool' ? (
    message.name === 'api-search-course' ? (
      <Courses courses={message.content} />
    ) : message.name === 'api-search-profile' ? (
      <People people={message.content} />
    ) : message.name === 'api-meetings' ? (
      <Meetings meetings={message.content} />
    ) : message.name === 'api-search-building' ? (
      <Buildings buildings={message.content} />
    ) : message.name === 'api-events' ? (
      <Events events={message.content} />
    ) : message.name === 'api-meals' ? (
      <Meals meals={message.content} />
    ) : null
  ) : (
    <div>{message.content}</div>
  );
}
```

## Rendering User Interfaces on the Server

The AI SDK RSC (`ai/rsc`) takes advantage of RSCs to solve the problem of managing all your React components on the client side, allowing you to render React components on the server and stream them to the client.

Rather than conditionally rendering user interfaces on the client based on the data returned by the language model, you can directly stream them from the server during a model generation.

## `app/action.ts`

```typescript
import { createStreamableUI } from 'ai/rsc'

const uiStream = createStreamableUI();

const text = generateText({
  model: openai('gpt-3.5-turbo'),
  system: 'you are a friendly assistant'
  prompt: 'what is the weather in SF?'
  tools: {
    getWeather: {
      description: 'Get the weather for a location',
      parameters: z.object({
        city: z.string().describe('The city to get the weather for'),
        unit: z
          .enum(['C', 'F'])
          .describe('The unit to display the temperature in')
      }),
      execute: async ({ city, unit }) => {
        const weather = getWeather({ city, unit })
        const { temperature, unit, description, forecast } = weather

        uiStream.done(
          <WeatherCard
            weather={{
              temperature: 47,
              unit: 'F',
              description: 'sunny'
              forecast,
            }}
          />
        )
      }
    }
  }
})

return {
  display: uiStream.value
}
```

The `createStreamableUI` function belongs to the `ai/rsc` module and creates a stream that can send React components to the client.

On the server, you render the `<WeatherCard/>` component with the props passed to it, and then stream it to the client. On the client side, you only need to render the UI that is streamed from the server.

## `app/page.tsx`

```typescript
return (
  <div>
    {messages.map(message => (
      <div>{message.display}</div>
    ))}
  </div>
);
```

Now the steps involved are simplified:

1. The user prompts the language model.
2. The language model generates a response that includes a tool call.
3. The tool call renders a React component along with relevant props that represent the user interface.
4. The response is streamed to the client and rendered directly.

Note: You can also render text on the server and stream it to the client using React Server Components. This way, all operations from language model generation to UI rendering can be done on the server, while the client only needs to render the UI that is streamed from the server.

# Generative User Interfaces

Since language models can render user interfaces as part of their generations, the resulting model generations are referred to as *generative user interfaces*.

In this section, we will learn more about generative user interfaces and their impact on the way AI applications are built.

## Deterministic Routes and Probabilistic Routing

Generative user interfaces are not deterministic in nature because they depend on the model's generation output. Since these generations are probabilistic in nature, it is possible for every user query to result in a different user interface.

Users expect their experience using your application to be predictable, so non-deterministic user interfaces can sound like a bad idea at first. However, language models can be set up to limit their generations to a particular set of outputs using their ability to call functions.

When language models are provided with a set of function definitions and instructed to execute any of them based on user query, they do either one of the following things:

1. Execute a function that is most relevant to the user query.
2. Not execute any function if the user query is out of bounds of the set of functions available to them.

```typescript
// app/actions.ts
const sendMessage = (prompt: string) =>
  generateText({
    model: 'gpt-3.5-turbo',
    system: 'you are a friendly weather assistant!',
    prompt,
    tools: {
      getWeather: {
        description: 'Get the weather in a location',
        parameters: z.object({
          location: z.string().describe('The location to get the weather for'),
        }),
        execute: async ({ location }: { location: string }) => ({
          location,
          temperature: 72 + Math.floor(Math.random() * 21) - 10,
        }),
      },
    },
  });

sendMessage('What is the weather in San Francisco?'); // getWeather is called
sendMessage('What is the weather in New York?'); // getWeather is called
sendMessage('What events are happening in London?'); // No function is called
```

This way, it is possible to ensure that the generations result in deterministic outputs, while the choice a model makes still remains to be probabilistic.

This emergent ability exhibited by a language model to choose whether a function needs to be executed or not based on a user query is believed to be models emulating "reasoning".

As a result, the combination of language models being able to reason which function to execute as well as render user interfaces at the same time gives you the ability to build applications where language models can be used as a router.

## Language Models as Routers

Historically, developers had to write routing logic that connected different parts of an application to be navigable by a user and complete a specific task.

In web applications today, most of the routing logic takes place in the form of routes:

- `/login` would navigate you to a page with a login form.
- `/user/john` would navigate you to a page with profile details about John.
- `/api/events?limit=5` would display the five most recent events from an events database.

While routes help you build web applications that connect different parts of an application into a seamless user experience, it can also be a burden to manage them as the complexity of applications grow.

Next.js has helped reduce complexity in developing with routes by introducing:

- File-based routing system
- Dynamic routing
- API routes
- Middleware
- App router, and so on...

With language models becoming better at reasoning, we believe that there is a future where developers only write core application-specific components while models take care of routing them based on the user's state in an application.

With generative user interfaces, the language model decides which user interface to render based on the user's state in the application, giving users the flexibility to interact with your application in a conversational manner instead of navigating through a series of predefined routes.

### Routing by Parameters

For routes like:

- `/profile/[username]`
- `/search?q=[query]`
- `/media/[id]`

that have segments dependent on dynamic data, the language model can generate the correct parameters and render the user interface.

For example, when you're in a search application, you can ask the language model to search for artworks from different artists. The language model will call the search function with the artist's name as a parameter and render the search results.

### Routing by Sequence

For actions that require a sequence of steps to be completed by navigating through different routes, the language model can generate the correct sequence of routes to complete in order to fulfill the user's request.

For example, when you're in a calendar application, you can ask the language model to schedule a happy hour evening with your friends. The language model will then understand your request and will perform the right sequence of tool calls to:

1. Lookup your calendar
2. Lookup your friends' calendars
3. Determine the best time for everyone
4. Search for nearby happy hour spots
5. Create an event and send out invites to your friends

Just by defining functions to lookup contacts, pull events from a calendar, and search for nearby locations, the model is able to sequentially navigate the routes for you.

To learn more, check out these examples using the `streamUI` function to stream generative user interfaces to the client based on the response from the language model.

# Multistep Interfaces

Multistep interfaces refer to user interfaces that require multiple independent steps to be executed in order to complete a specific task.

In order to understand multistep interfaces, it is important to understand two concepts:

1. **Tool Composition**
2. **Application Context**

## Tool Composition

Tool composition is the process of combining multiple tools to create a new tool. This is a powerful concept that allows you to break down complex tasks into smaller, more manageable steps.

## Application Context

Application context refers to the state of the application at any given point in time. This includes the user's input, the output of the language model, and any other relevant information.

When designing multistep interfaces, you need to consider how the tools in your application can be composed together to form a coherent user experience as well as how the application context changes as the user progresses through the interface.

### Application Context Example

The application context can be thought of as the conversation history between the user and the language model. The richer the context, the more information the model has to generate relevant responses.

In the context of multistep interfaces, the application context becomes even more important. This is because the user's input in one step may affect the output of the model in the next step.

For example, consider a meal logging application that helps users track their daily food intake. The language model is provided with the following tools:

- `log_meal` takes in parameters like the name of the food, the quantity, and the time of consumption to log a meal.
- `delete_meal` takes in the name of the meal to be deleted.

When the user logs a meal, the model generates a response confirming the meal has been logged.

```
User: Log a chicken shawarma for lunch.
Tool: log_meal("chicken shawarma", "250g", "12:00 PM")
Model: Chicken shawarma has been logged for lunch.
```

Now when the user decides to delete the meal, the model should be able to reference the previous step to identify the meal to be deleted.

```
User: Log a chicken shawarma for lunch.
Tool: log_meal("chicken shawarma", "250g", "12:00 PM")
Model: Chicken shawarma has been logged for lunch.
...
...
User: I skipped lunch today, can you update my log?
Tool: delete_meal("chicken shawarma")
Model: Chicken shawarma has been deleted from your log.
```

In this example, managing the application context is important for the model to generate the correct response. The model needs to have information about the previous actions in order for it to use generate the parameters for the `delete_meal` tool.

### Tool Composition Example

Tool composition is the process of combining multiple tools to create a new tool. This involves defining the inputs and outputs of each tool, as well as how they interact with each other.

The design of how these tools can be composed together to form a multistep interface is crucial to both the user experience of your application and the model's ability to generate the correct output.

For example, consider a flight booking assistant that can help users book flights. The assistant can be designed to have the following tools:

- `searchFlights`: Searches for flights based on the user's query.
- `lookupFlight`: Looks up details of a specific flight based on the flight number.
- `bookFlight`: Books a flight based on the user's selection.

The `searchFlights` tool is called when the user wants to lookup flights for a specific route. This would typically mean the tool should be able to take in parameters like the origin and destination of the flight.

The `lookupFlight` tool is called when the user wants to get more details about a specific flight. This would typically mean the tool should be able to take in parameters like the flight number and return information about seat availability.

The `bookFlight` tool is called when the user decides to book a flight. In order to identify the flight to book, the tool should be able to take in parameters like the flight number, trip date, and passenger details.

So the conversation between the user and the model could look like this:

```
User: I want to book a flight from New York to London.
Tool: searchFlights("New York", "London")
Model: Here are the available flights from New York to London.
User: I want to book flight number BA123 on 12th December for myself and my wife.
Tool: lookupFlight("BA123") -> "4 seats available"
Model: Sure, there are seats available! Can you provide the names of the passengers?
User: John Doe and Jane Doe.
Tool: bookFlight("BA123", "12th December", ["John Doe", "Jane Doe"])
Model: Your flight has been booked!
```

In the last tool call, the `bookFlight` tool does not include passenger details as it is not available in the application context. As a result, it requests the user to provide the passenger details before proceeding with the booking.

Looking up passenger information could've been another tool that the model could've called before calling the `bookFlight` tool assuming that the user is logged into the application. This way, the model does not have to ask the user for the passenger details and can proceed with the booking.

```
User: I want to book a flight from New York to London.
Tool: searchFlights("New York", "London")
Model: Here are the available flights from New York to London.
User: I want to book flight number BA123 on 12th December for myself an my wife.
Tool: lookupContacts() -> ["John Doe", "Jane Doe"]
Tool: bookFlight("BA123", "12th December", ["John Doe", "Jane Doe"])
Model: Your flight has been booked!
```

The `lookupContacts` tool is called before the `bookFlight` tool to ensure that the passenger details are available in the application context when booking the flight. This way, the model can reduce the number of steps required from the user and use its ability to call tools that populate its context and use that information to complete the booking process.

Now, let's introduce another tool called `lookupBooking` that can be used to show booking details by taking in the name of the passenger as parameter. This tool can be composed with the existing tools to provide a more complete user experience.

```
User: What's the status of my wife's upcoming flight?
Tool: lookupContacts() -> ["John Doe", "Jane Doe"]
Tool: lookupBooking("Jane Doe") -> "BA123 confirmed"
Tool: lookupFlight("BA123") -> "Flight BA123 is scheduled to depart on 12th December."
Model: Your wife's flight BA123 is confirmed and scheduled to depart on 12th December.
```

In this example, the `lookupBooking` tool is used to provide the user with the status of their wife's upcoming flight. By composing this tool with the existing tools, the model is able to generate a response that includes the booking status and the departure date of the flight without requiring the user to provide additional information.

As a result, the more tools you design that can be composed together, the more complex and powerful your application can become.
