
# Intro and Overview

The Vercel AI SDK is the TypeScript toolkit designed to help developers build AI-powered applications with React, Next.js, Vue, Svelte, Node.js, and more.

## Why use the Vercel AI SDK?

Integrating large language models (LLMs) into applications is complicated and heavily dependent on the specific model provider you use.

The Vercel AI SDK abstracts away the differences between model providers, eliminates boilerplate code for building chatbots, and allows you to go beyond text output to generate rich, interactive components.

- **[AI SDK Core](/docs/ai-sdk-core):** A unified API for generating text, structured objects, and tool calls with LLMs.
- **[AI SDK UI](/docs/ai-sdk-ui):** A set of framework-agnostic hooks for quickly building chat and generative user interface.
- **[AI SDK RSC](/docs/ai-sdk-rsc):** A library to stream generative user interfaces with React Server Components (RSC).

The Vercel AI SDK standardizes integrating artificial intelligence (AI) models across [supported providers](/docs/foundations/providers-and-models). This enables developers to focus on building great AI applications, not waste time on technical details.

To effectively leverage the AI SDK, it helps to familiarize yourself with the following concepts:

## Generative Artificial Intelligence

**Generative artificial intelligence** refers to models that predict and generate various types of outputs (such as text, images, or audio) based on what’s statistically likely, pulling from patterns they’ve learned from their training data. For example:

- Given a photo, a generative model can generate a caption.
- Given an audio file, a generative model can generate a transcription.
- Given a text description, a generative model can generate an image.

## Large Language Models

A **large language model (LLM)** is a subset of generative models focused primarily on **text**. An LLM takes a sequence of words as input and aims to predict the most likely sequence to follow. It assigns probabilities to potential next sequences and then selects one. The model continues to generate sequences until it meets a specified stopping criterion.

LLMs learn by training on massive collections of written text, which means they will be better suited to some use cases than others. For example, a model trained on GitHub data would understand the probabilities of sequences in source code particularly well.

However, it's crucial to understand LLMs' limitations. When asked about less known or absent information, like the birthday of a personal relative, LLMs might "hallucinate" or make up information. It's essential to consider how well-represented the information you need is in the model.

## Embedding Models

An **embedding model** is used to convert complex data (like words or images) into a dense vector (a list of numbers) representation, known as an embedding. Unlike generative models, embedding models do not generate new text or data. Instead, they provide representations of semantic and synactic relationships between entities that can be used as input for other models or other natural language processing tasks.

In the next section, you will learn about the difference between models providers and models, and which ones are available in the Vercel AI SDK.

---
title: Providers and Models
description: Learn about the providers and models available in the Vercel AI SDK.
---

## Providers and Models

Companies such as OpenAI and Anthropic (providers) offer access to a range of large language models (LLMs) with differing strengths and capabilities through their own APIs.

Each provider typically has its own unique method for interfacing with their models, complicating the process of switching providers and increasing the risk of vendor lock-in.

To solve these challenges, Vercel AI SDK Core offers a standardized approach to interacting with LLMs through a [language model specification](https://github.com/vercel/ai/tree/main/packages/provider/src/language-model/v1) that abstracts differences between providers. This unified interface allows you to switch between providers with ease while using the same API for all providers.


### AI SDK Providers

Vercel AI SDK comes with several providers that you can use to interact with different language models:

- [OpenAI Provider](/providers/ai-sdk-providers/openai) (`@ai-sdk/openai`)
- [Azure OpenAI Provider](/providers/ai-sdk-providers/azure) (`@ai-sdk/azure`)
- [Anthropic Provider](/providers/ai-sdk-providers/anthropic) (`@ai-sdk/anthropic`)
- [Amazon Bedrock Provider](/providers/ai-sdk-providers/amazon-bedrock) (`@ai-sdk/amazon-bedrock`)
- [Google Generative AI Provider](/providers/ai-sdk-providers/google-generative-ai) (`@ai-sdk/google`)
- [Google Vertex Provider](/providers/ai-sdk-providers/google-vertex) (`@ai-sdk/google-vertex`)
- [Mistral Provider](/providers/ai-sdk-providers/mistral) (`@ai-sdk/mistral`)
- [Cohere Provider](/providers/ai-sdk-providers/cohere) (`@ai-sdk/cohere`)

You can also use the OpenAI provider with OpenAI-compatible APIs:

- [Groq](/providers/ai-sdk-providers/groq)
- [Perplexity](/providers/ai-sdk-providers/perplexity)
- [Fireworks](/providers/ai-sdk-providers/fireworks)

---
title: Prompts
description: Learn about the Prompt structure used in the Vercel AI SDK.
---

## Prompts

Prompts are instructions that you give a [large language model (LLM)](/docs/foundations/overview#large-language-models) to tell it what to do.
It's like when you ask someone for directions; the clearer your question, the better the directions you'll get.

Many LLM providers offer complex interfaces for specifying prompts. They involve different roles and message types.
While these interfaces are powerful, they can be hard to use and understand.

In order to simplify prompting across compatible providers, the Vercel AI SDK offers two categories of prompts: text prompts and message prompts.

### Text Prompts

Text prompts are strings.
They are ideal for simple generation use cases,
e.g. repeatedly generating content for variants of the same prompt text.

You can set text prompts using the `prompt` property made available by AI SDK functions like [`generateText`](/docs/reference/ai-sdk-core/generate-text) or [`streamUI`](/docs/reference/ai-sdk-rsc/stream-ui).
You can structure the text in any way and inject variables, e.g. using a template literal.

```ts highlight="3"
const result = await generateText({
  model: yourModel,
  prompt: 'Invent a new holiday and describe its traditions.',
});
```

You can also use template literals to provide dynamic data to your prompt.

```ts highlight="3-5"
const result = await generateText({
  model: yourModel,
  prompt:
    `I am planning a trip to ${destination} for ${lengthOfStay} days. ` +
    `Please suggest the best tourist activities for me to do.`,
});
```

### Message Prompts

A message prompt is an array of user, assistant, and tool messages.
They are great for chat interfaces and more complex, multi-modal prompts.

Each message has a `role` and a `content` property. The content can either be text (for user and assistant messages), or an array of relevant parts (data) for that message type.

```ts highlight="3-7"
const result = await streamUI({
  model: yourModel,
  messages: [
    { role: 'user', content: 'Hi!' },
    { role: 'assistant', content: 'Hello, how can I help?' },
    { role: 'user', content: 'Where can I buy the best Currywurst in Berlin?' },
  ],
});
```

<Note type="warning">
  Not all language models support all message and content types. For example,
  some models might not be capable of handling multi-modal inputs or tool
  messages. [Learn more about the capabilities of select
  models](./providers-and-models#model-capabilities).
</Note>

### Multi-modal messages

<Note>
  Multi-modal refers to interacting with a model across different data types
  such as text, image, or audio data.
</Note>

Instead of sending a text in the `content` property, you can send an array of parts that include text and other data types.
Currently image and text parts are supported.

For models that support multi-modal inputs, user messages can include images. An `image` can be one of the following:

- base64-encoded image:
  - `string` with base-64 encoded content
  - data URL `string`, e.g. `data:image/png;base64,...`
- binary image:
  - `ArrayBuffer`
  - `Uint8Array`
  - `Buffer`
- URL:
  - http(s) URL `string`, e.g. `https://example.com/image.png`
  - `URL` object, e.g. `new URL('https://example.com/image.png')`

It is possible to mix text and multiple images.

<Note type="warning">
  Not all models support all types of multi-modal inputs. Check the model's
  capabilities before using this feature.
</Note>

#### Example: Binary image (Buffer)

```ts highlight="8-11"
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

#### Example: Base-64 encoded image (string)

```ts highlight="8-11"
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

#### Example: Image URL (string)

```ts highlight="8-12"
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

### Tool messages

<Note>
  [Tools](/docs/foundations/tools) (also known as function calling) are programs
  that you can provide an LLM to extend it's built-in functionality. This can be
  anything from calling an external API to calling functions within your UI.
  Learn more about Tools in [the next section](/docs/foundations/tools).
</Note>

For models that support [tool](/docs/foundations/tools) calls, assistant messages can contain tool call parts, and tool messages can contain tool result parts.
A single assistant message can call multiple tools, and a single tool message can contain multiple tool results.

```ts highlight="14-42"
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

### System Messages

System messages are the initial set of instructions given to models that help guide and constrain the models' behaviors and responses.
You can set system prompts using the `system` property.
System messages work with both the `prompt` and the `messages` properties.

```ts highlight="3-6"
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
---
title: Tools
description: Learn about tools with the Vercel AI SDK.
---

## Tools

While [large language models (LLMs)](/docs/foundations/overview#large-language-models) have incredible generation capabilities,
they struggle with discrete tasks (e.g. mathematics) and interacting with the outside world (e.g. getting the weather).

Tools can be thought of as programs you give to a model which can be run as and when the model deems applicable.

### What is a tool?

A tool is an object that can be called by the model to perform a specific task.
You can use tools with functions across the AI SDK (like [`generateText`](/docs/reference/ai-sdk-core/generate-text) or [`streamUI`](/docs/reference/ai-sdk-rsc/stream-ui)) by passing a tool(s) to the `tools` parameter.

There are three elements of a tool, a description, parameters, and an optional `execute` or `generate` function (dependent on the SDK function).

- **`description`**: An optional description of the tool that can influence when the tool is picked.
- **`parameters`**: A [Zod schema](/docs/foundations/tools#schema-specification-and-validation-with-zod) or a [JSON schema](/docs/reference/ai-sdk-core/json-schema) that defines the parameters. The schema is consumed by the LLM, and also used to validate the LLM tool calls.
- **`execute`** or **`generate`**: An optional async or generator function that is called with the arguments from the tool call.

### Tool Calls

If the LLM decides to use a tool, it will generate a tool call.
Tools with an `execute` or `generate` function are run automatically when these calls are generated.
The results of the tool calls are returned using tool result objects.
Each tool result object has a `toolCallId`, a `toolName`, a typed `args` object, and a typed `result`.

### Tool Choice

You can use the `toolChoice` setting to influence when a tool is selected.
It supports the following settings:

- `auto` (default): the model can choose whether and which tools to call.
- `required`: the model must call a tool. It can choose which tool to call.
- `none`: the model must not call tools
- `{ type: 'tool', toolName: string (typed) }`: the model must call the specified tool

```ts highlight="18"
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

### Schema Specification and Validation with Zod

Tool usage and structured object generation require the specification of schemas.
The AI SDK uses [Zod](https://zod.dev/), the most popular JavaScript schema validation library, for schema specification and validation.

You can then specify schemas, for example:

```ts
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

These schemas can be used to define parameters for tool calls and generated structured objects with [`generateObject`](/docs/reference/ai-sdk-core/generate-object) and [`streamObject`](/docs/reference/ai-sdk-core/stream-object).

<Note>
  You can also use a JSON schema without Zod using the `jsonSchema` function to
  define the schema.
</Note>

---
title: Streaming
description: Why use streaming for AI applications?
---

## Streaming

Streaming conversational text UIs (like ChatGPT) have gained massive popularity over the past few months. This section explores the benefits and drawbacks of streaming and blocking interfaces.

[Large language models (LLMs)](/docs/foundations/overview#large-language-models) are extremely powerful. However, when generating long outputs, they can be very slow compared to the latency you're likely used to. If you try to build a traditional blocking UI, your users might easily find themselves staring at loading spinners for 5, 10, even up to 40s waiting for the entire LLM response to be generated. This can lead to a poor user experience, especially in conversational applications like chatbots. Streaming UIs can help mitigate this issue by **displaying parts of the response as they become available**.

<div className="grid lg:grid-cols-2 grid-cols-1 gap-4 mt-8">
  <Card
    title="Blocking UI"
    description="Blocking responses wait until the full response is available before displaying it."
  >
    <BrowserIllustration highlight blocking />
  </Card>
  <Card
    title="Streaming UI"
    description="Streaming responses can transmit parts of the response as they become available."
  >
    <BrowserIllustration highlight />
  </Card>
</div>

### Real-world Examples

Here are 2 examples that illustrate how streaming UIs can improve user experiences in a real-world setting – the first uses a blocking UI, while the second uses a streaming UI.

#### Blocking UI

<InlinePrompt
  initialInput="Come up with the first 200 characters of the first book in the Harry Potter series."
  blocking
/>

#### Streaming UI

<InlinePrompt initialInput="Come up with the first 200 characters of the first book in the Harry Potter series." />

As you can see, the streaming UI is able to start displaying the response much faster than the blocking UI. This is because the blocking UI has to wait for the entire response to be generated before it can display anything, while the streaming UI can display parts of the response as they become available.

While streaming interfaces can greatly enhance user experiences, especially with larger language models, they aren't always necessary or beneficial. If you can achieve your desired functionality using a smaller, faster model without resorting to streaming, this route can often lead to simpler and more manageable development processes.

However, regardless of the speed of your model, the Vercel AI SDK is designed to make implementing streaming UIs as simple as possible. In the example below, we stream text generation from OpenAI's `gpt-4-turbo` in under 10 lines of code using the SDK's [`streamText`](/docs/reference/ai-sdk-core/stream-text) function:

```ts
import { openai } from '@ai-sdk/openai';
import { streamText } from 'ai';

const { textStream } = await streamText({
  model: openai('gpt-4-turbo'),
  prompt: 'Write a poem about embedding models.',
});

for await (const textPart of textStream) {
  console.log(textPart);
}
```
---
title: Navigating the Library
description: Learn how to navigate the Vercel AI SDK.
---

## Navigating the Library

The Vercel AI SDK is a powerful toolkit for building AI applications. This page will help you pick the right tools for your requirements.

Let’s start with a quick overview of the Vercel AI SDK, which is comprised of three parts:

- **[AI SDK Core](/docs/ai-sdk-core/overview):** A unified API for generating text, structured objects, and tool calls with LLMs.
- **[AI SDK UI](/docs/ai-sdk-ui/overview):** A set of framework-agnostic hooks for quickly building chat and generative user interface.
- **[AI SDK RSC](/docs/ai-sdk-rsc/overview):** A library to stream generative user interfaces with React Server Components (RSC).

### Choosing the Right Tool for Your Environment

When deciding which part of the Vercel AI SDK to use, your first consideration should be the environment and existing stack you are working with. Different components of the SDK are tailored to specific frameworks and environments.

| Library                                   | Purpose                                                                                                                                                          | Environment Compatibility                                              |
| ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| [AI SDK Core](/docs/ai-sdk-core/overview) | Call any LLM with unified API (e.g. [generateText](/docs/reference/ai-sdk-core/generate-text) and [generateObject](/docs/reference/ai-sdk-core/generate-object)) | Any JS environment (e.g. Node.js, Deno, Browser)                       |
| [AI SDK UI](/docs/ai-sdk-ui/overview)     | Build streaming chat and generative UIs (e.g. [useChat](/docs/reference/ai-sdk-ui/use-chat))                                                                     | React & Next.js, Vue & Nuxt, Svelte & SvelteKit, Solid.js & SolidStart |
| [AI SDK RSC](/docs/ai-sdk-rsc/overview)   | Stream generative UIs from Server to Client (e.g. [streamUI](/docs/reference/ai-sdk-rsc/stream-ui))                                                              | Any framework that supports React Server Components (e.g. Next.js)     |

### When to use AI SDK RSC

[React Server Components](https://nextjs.org/docs/app/building-your-application/rendering/server-components) (RSCs) provide a new approach to building React applications that allow components to render on the server, fetch data directly, and stream the results to the client, reducing bundle size and improving performance. They also introduce a new way to call server-side functions from anywhere in your application called [Server Actions](https://nextjs.org/docs/app/building-your-application/data-fetching/server-actions-and-mutations).

When considering whether to use AI SDK RSC, it's important to be aware of the current limitations of RSCs and Server Actions:

- **Cancellation**: currently, it is not possible to abort a stream using Server Actions. This will be improved in future releases of React and Next.js.
- **Increased Data Transfer**: using [`createStreamableUI`](/docs/reference/ai-sdk-rsc/create-streamable-ui) can lead to quadratic data transfer (quadratic to the length of generated text). You can avoid this using [ `createStreamableValue` ](/docs/reference/ai-sdk-rsc/create-streamable-value) instead, and rendering the component client-side.
- **Re-mounting Issue During Streaming**: when using `createStreamableUI`, components re-mount on `.done()`, causing [flickering](https://github.com/vercel/ai/issues/2232).

If any of the above limitations are important to your application, we recommend using [AI SDK UI](/docs/ai-sdk-ui/overview). If these limitations don't affect your use case, [AI SDK RSC](/docs/ai-sdk-rsc/overview) is a great choice that enables powerful server-side rendering capabilities and seamless integration with RSCs.

## Next.js Quickstart

In this quick start tutorial, you'll build a simple AI-chatbot with a streaming user interface. Along the way, you'll learn key concepts and techniques that are fundamental to using the SDK in your own projects.

Check out [Prompt Engineering](/docs/advanced/prompt-engineering) and [HTTP Streaming](/docs/advanced/why-streaming) if you haven't heard of them.

### Prerequisites

To follow this quickstart, you'll need:

- Node.js 18+ and pnpm installed on your local development machine.
- An OpenAI API key.

If you haven't obtained your OpenAI API key, you can do so by [signing up](https://platform.openai.com/signup/) on the OpenAI website.

### Create Your Application

Start by creating a new Next.js application. This command will create a new directory named `my-ai-app` and set up a basic Next.js application inside it.

<div className="mb-4">
  <Note>
    Be sure to select yes when prompted to use the App Router. If you are
    looking for the Next.js Pages Router quickstart guide, you can find it
    [here](/docs/getting-started/nextjs-pages-router).
  </Note>
</div>

<Snippet text="pnpm create next-app@latest my-ai-app" />

Navigate to the newly created directory:

<Snippet text="cd my-ai-app" />

### Install dependencies

Install `ai` and `@ai-sdk/openai`, the Vercel AI package and Vercel AI SDK's [ OpenAI provider ](/providers/ai-sdk-providers/openai) respectively.

<Note>
  Vercel AI SDK is designed to be a unified interface to interact with any large
  language model. This means that you can change model and providers with just
  one line of code! Learn more about [available providers](/providers) and
  [building custom providers](/providers/community-providers/custom-providers)
  in the [providers](/providers) section.
</Note>
<div className="my-4">
  <Tabs items={['pnpm', 'npm', 'yarn']}>
    <Tab>
      <Snippet text="pnpm add ai @ai-sdk/openai zod" dark />
    </Tab>
    <Tab>
      <Snippet text="npm install ai @ai-sdk/openai zod" dark />
    </Tab>
    <Tab>
      <Snippet text="yarn add ai @ai-sdk/openai zod" dark />
    </Tab>
  </Tabs>
</div>

<Note type="secondary" fill>
  Make sure you are using `ai` version 3.1 or higher.
</Note>

### Configure OpenAI API key

Create a `.env.local` file in your project root and add your OpenAI API Key. This key is used to authenticate your application with the OpenAI service.

<Snippet text="touch .env.local" />

Edit the `.env.local` file:

```env filename=".env.local"
OPENAI_API_KEY=xxxxxxxxx
```

Replace `xxxxxxxxx` with your actual OpenAI API key.

<Note className="mb-4">
  Vercel AI SDK's OpenAI Provider will default to using the `OPENAI_API_KEY`
  environment variable.
</Note>

### Create a Route Handler

Create a route handler, `app/api/chat/route.ts` and add the following code:

```tsx filename="app/api/chat/route.ts"
import { openai } from '@ai-sdk/openai';
import { streamText, convertToCoreMessages } from 'ai';

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

1. Define an asynchronous `POST` request handler and extract `messages` from the body of the request. The `messages` variable contains a history of the conversation between you and the chatbot and provides the chatbot with the necessary context to make the next generation.
2. Call [`streamText`](/docs/reference/ai-sdk-core/stream-text), which is imported from the `ai` package. This function accepts a configuration object that contains a `model` provider (imported from `@ai-sdk/openai`) and `messages` (defined in step 1). You can pass additional [settings](/docs/ai-sdk-core/settings) to further customise the model's behaviour.
3. The `streamText` function returns a [`StreamTextResult`](/docs/reference/ai-sdk-core/stream-text#result-object). This result object contains the [ `toDataStreamResponse` ](/docs/reference/ai-sdk-core/stream-text#to-ai-stream-response) function which converts the result to a streamed response object.
4. Finally, return the result to the client to stream the response.

This Route Handler creates a POST request endpoint at `/api/chat`.

### Wire up the UI

Now that you have a Route Handler that can query an LLM, it's time to setup your frontend. Vercel AI SDK's [ UI ](/docs/ai-sdk-ui) package abstracts the complexity of a chat interface into one hook, [`useChat`](/docs/reference/ai-sdk-ui/use-chat).

Update your root page (`app/page.tsx`) with the following code to show a list of chat messages and provide a user message input:

```tsx filename="app/page.tsx"
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

<Note>
  Make sure you add the `"use client"` directive to the top of your file. This
  allows you to add interactivity with Javascript.
</Note>

This page utilizes the `useChat` hook, which will, by default, use the `POST` API route you created earlier (`/api/chat`). The hook provides functions and state for handling user input and form submission. The `useChat` hook provides multiple utility functions and state variables:

- `messages` - the current chat messages (an array of objects with `id`, `role`, and `content` properties).
- `input` - the current value of the user's input field.
- `handleInputChange` and `handleSubmit` - functions to handle user interactions (typing into the input field and submitting the form, respectively).
- `isLoading` - boolean that indicates whether the API request is in progress.

### Running Your Application

With that, you have built everything you need for your chatbot! To start your application, use the command:

<Snippet text="pnpm run dev" />

Head to your browser and open http://localhost:3000. You should see an input field. Test it out by entering a message and see the AI chatbot respond in real-time! Vercel AI SDK makes it fast and easy to build AI chat interfaces with Next.js.

### Stream Data Alongside Response

Depending on your use case, you may want to stream additional data alongside the model's response. This can be done using [`StreamData`](/docs/reference/stream-helpers/stream-data).

#### Update your Route Handler

Make the following changes to your Route Handler (`app/api/chat/route.ts`):

```ts filename="app/api/chat/route.ts" highlight="2,10-11,16-18,21"
import { openai } from '@ai-sdk/openai';
import { streamText, convertToCoreMessages, StreamData } from 'ai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { messages } = await req.json();

  const data = new StreamData();
  data.append({ test: 'value' });

  const result = await streamText({
    model: openai('gpt-4-turbo'),
    messages: convertToCoreMessages(messages),
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

```tsx filename="app/page.tsx" highlight="6, 9"
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

Head back to your browser (http://localhost:3000) and enter a new message. You should see a JSON object appear with the value you sent from your API route!

### Introducing more granular control with `ai/rsc`

So far, you have used Vercel AI SDK's [ UI ](/docs/ai-sdk-ui) package to connect your frontend to your API route. This package is framework agnostic and provides simple abstractions for quickly building chat-like interfaces with LLMs.

The Vercel AI SDK also has a package ([`ai/rsc`](/docs/ai-sdk-rsc)) specifically designed for frameworks that support the [React Server Component](https://nextjs.org/docs/app/building-your-application/rendering/server-components) architecture. With `ai/rsc`, you can build AI applications that go beyond pure text.

### Next.js App Router

The [Next.js App Router](https://nextjs.org/docs/app) is a React Server Component (RSC) framework. This means that pages and components are rendered on the server. Optionally, you can add directives, like `"use client"` when you want to add interactivity using Javascript and `"use server"` when you want to ensure code will only run on the server.

The server-first architecture of RSCs enables a number of powerful features, like Server Actions, which we will use as our server-side environment to query the language model.

<Note>
  Server Actions are functions that run on a server, but can be called directly
  from your Next.js frontend. Server Actions reduce the amount of code you write
  while also providing end-to-end type safety between the client and server. You
  can learn more
  [here](https://nextjs.org/docs/app/building-your-application/data-fetching/server-actions-and-mutations#with-client-components)
</Note>

### Create a Server Action

Create your first Server Action (`app/actions.tsx`) and add the following code:

```tsx filename="app/actions.tsx"
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

1. Add the `"use server"` directive at the top of the file to mark the function as a Server Action.
2. Define and export an async function (`continueConversation`) that takes one argument, `messages`, which is an array of type `CoreMessage`. The `messages` variable contains a history of the conversation between you and the chatbot and will provide the chatbot with the necessary context to make the next generation.
3. Call the [`streamText`](/docs/reference/ai-sdk-core/stream-text) function which is imported from the `ai` package. To use this function, you pass it a configuration object that contains a `model` provider (imported from `@ai-sdk/openai`) and `messages` (defined in step 2). You can pass additional [settings](/docs/ai-sdk-core/settings) to further customise the model's behaviour.
4. Create a streamable value using the [ `createStreamableValue` ](/docs/reference/ai-sdk-rsc/create-streamable-value) function imported from the `ai/rsc` package. To use this function you pass the model's response as a text stream which can be accessed directly on the model response object (`result.textStream`).
5. Finally, return the value of the stream (`stream.value`).

### Update the UI

Now that you have created a Server Action that can query an LLM, it's time to update your frontend. With `ai/rsc`, you have much finer control over how you send and receive streamable values from the LLM.

Update your root page (`app/page.tsx`) with the following code:

```tsx filename="app/page.tsx"
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

Let's look at how your implementation has changed. Without `useChat`, you have to manage your own state using the `useState` hook to manage the user\'s `input` and `messages`, respectively. The biggest change in your implementation is how you manage the form submission behaviour:

1. Define a new variable to house the existing `messages` and append the user's message.
2. Update the `messages` state by passing `newMessages` to the `setMessages` function.
3. Clear the `input` state with `setInput("")`.
4. Call your Server Action just like any other asynchronous function, passing the `newMessages` variable declared in the first step. This function will return a streamable value.
5. Use an asynchronous for-loop in conjunction with the `readStreamableValue` function to iterate over the stream returned by the Server Action and read its value.
6. Finally, update the `messages` state with the content streamed via the Server Action.

### Streaming Additional Data

Stream additional data alongside the response from the model by simply returning an additional value in your Server Action.
Update your `app/actions.tsx`with the following code:

```ts filename="app/actions.tsx" highlight="13,15"
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

#### Update the UI

Update your root page with the following code:

```tsx filename="app/page.tsx" highlight="14, 17, 37, 39"
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

# Guides

## RAG Chatbot Guide

In this guide, you will learn how to build a retrieval-augmented generation (RAG) chatbot application.

Before we dive in, let's look at what RAG is, and why we would want to use it.

### What is RAG?

RAG stands for retrieval augmented generation. In simple terms, RAG is the process of providing a Large Language Model (LLM) with specific information relevant to the prompt.

### Why is RAG important?

While LLMs are powerful, the information they can reason on is restricted to the data they were trained on. This problem becomes apparent when asking an LLM for information outside of their training data, like proprietary data or common knowledge that has occurred after the model’s training cutoff. RAG solves this problem by fetching information relevant to the prompt and then passing that to the model as context.

To illustrate with a basic example, imagine asking the model for your favorite food:

```txt
**input**
What is my favorite food?

**generation**
I don't have access to personal information about individuals, including their
favorite foods.
```

Not surprisingly, the model doesn’t know. But imagine, alongside your prompt, the model received some extra context:

```txt
**input**
Respond to the user's prompt using only the provided context.
user prompt: 'What is my favorite food?'
context: user loves chicken nuggets

**generation**
Your favorite food is chicken nuggets!
```

Just like that, you have augmented the model’s generation by providing relevant information to the query. Assuming the model has the appropriate information, it is now highly likely to return an accurate response to the users query. But how does it retrieve the relevant information? The answer relies on a concept called embedding.

<Note>
  You could fetch any context for your RAG application (eg. Google search).
  Embeddings and Vector Databases are just a specific retrieval approach to
  achieve semantic search.
</Note>

### Embedding

[Embeddings](/docs/ai-sdk-core/embeddings) are a way to represent words, phrases, or images as vectors in a high-dimensional space. In this space, similar words are close to each other, and the distance between words can be used to measure their similarity.

In practice, this means that if you embedded the words `cat` and `dog`, you would expect them to be plotted close to each other in vector space. The process of calculating the similarity between two vectors is called ‘cosine similarity’ where a value of 1 would indicate high similarity and a value of -1 would indicate high opposition.

<Note>
  Don’t worry if this seems complicated. a high level understanding is all you
  need to get started! For a more in-depth introduction to embeddings, check out
  [this guide](https://jalammar.github.io/illustrated-word2vec/).
</Note>

As mentioned above, embeddings are a way to represent the semantic meaning of **words and phrases**. The implication here is that the larger the input to your embedding, the lower quality the embedding will be. So how would you approach embedding content longer than a simple phrase?

### Chunking

Chunking refers to the process of breaking down a particular source material into smaller pieces. There are many different approaches to chunking and it’s worth experimenting as the most effective approach can differ by use case. A simple and common approach to chunking (and what you will be using in this guide) is separating written content by sentences.

Once your source material is appropriately chunked, you can embed each one and then store the embedding and the chunk together in a database. Embeddings can be stored in any database that supports vectors. For this tutorial, you will be using [Postgres](https://www.postgresql.org/) alongside the [pgvector](https://github.com/pgvector/pgvector) plugin.

### All Together Now

Combining all of this together, RAG is the process of enabling the model to respond with information outside of it’s training data by embedding a users query, retrieving the relevant source material (chunks) with the highest semantic similarity, and then passing them alongside the initial query as context. Going back to the example where you ask the model for your favorite food, the prompt preparation process would look like this.

By passing the appropriate context and refining the model’s objective, you are able to fully leverage its strengths as a reasoning machine.

Onto the project!

### Project Setup

In this project, you will build a chatbot that will only respond with information that it has within its knowledge base. The chatbot will be able to both store and retrieve information. This project has many interesting use cases from customer support through to building your own second brain!

This project will use the following stack:

- [Next.js](https://nextjs.org) 14 (App Router)
- [ Vercel AI SDK ](https://sdk.vercel.ai/docs)
- [OpenAI](https://openai.com)
- [ Drizzle ORM ](https://orm.drizzle.team)
- [ Postgres ](https://www.postgresql.org/) with [ pgvector ](https://github.com/pgvector/pgvector)
- [ shadcn-ui ](https://ui.shadcn.com) and [ TailwindCSS ](https://tailwindcss.com) for styling

#### Clone Repo

To reduce the scope of this guide, you will be starting with a [repository](https://github.com/vercel/ai-sdk-rag-starter) that already has a few things set up for you:

- Drizzle ORM (`lib/db`) including an initial migration and a script to migrate (`db:migrate`)
- a basic schema for the `resources` table (this will be for source material)
- a Server Action for creating a `resource`

To get started, clone the starter repository with the following command:

<Snippet
  text={[
    'git clone https://github.com/vercel/ai-sdk-rag-starter',
    'cd ai-sdk-rag-starter',
  ]}
/>

First things first, run the following command to install the project’s dependencies:

<Snippet text="pnpm install" />

#### Create Database

You will need a Postgres database to complete this tutorial. If you don’t have Postgres setup on your local machine you can:

- Create a free Postgres database with [Vercel Postgres](https://vercel.com/docs/storage/vercel-postgres); or
- Follow [this guide](https://www.prisma.io/dataguide/postgresql/setting-up-a-local-postgresql-database) to set it up locally

#### Migrate Database

Once you have a Postgres database, you need to add the connection string as an environment secret.

Make a copy of the `.env.example` file and rename it to `.env`.

<Snippet text="cp .env.example .env" />

Open the new `.env` file. You should see an item called `DATABASE_URL`. Copy in your database connection string after the equals sign.

With that set up, you can now run your first database migration. Run the following command:

<Snippet text="pnpm db:migrate" />

This will first add the `pgvector` extension to your database. Then it will create a new table for your `resources` schema that is defined in `lib/db/schema/resources.ts`. This schema has four columns: `id`, `content`, `createdAt`, and `updatedAt`.

<Note>
  If you experience an error with the migration, open your migration file
  (`lib/db/migrations/0000_yielding_bloodaxe.sql`), cut (copy and remove) the
  first line, and run it directly on your postgres instance. You should now be
  able to run the updated migration. [More
  info](https://github.com/vercel/ai-sdk-rag-starter/issues/1).
</Note>

#### OpenAI API Key

For this guide, you will need an OpenAI API key. To generate an API key, go to [platform.openai.com](http://platform.openai.com/).

Once you have your API key, paste it into your `.env` file (`OPENAI_API_KEY`).

### Build

Let’s build a quick task list of what needs to be done:

1. Create a table in your database to store embeddings
2. Add logic to chunk and create embeddings when creating resources
3. Create a chatbot
4. Give the chatbot tools to query / create resources for it’s knowledge base

### Create Embeddings Table

Currently, your application has one table (`resources`) which has a column (`content`) for storing content. Remember, each `resource` (source material) will have to be chunked, embedded, and then stored. Let’s create a table called `embeddings` to store these chunks.

Create a new file (`lib/db/schema/embeddings.ts`) and add the following code:

```tsx filename="lib/db/schema/embeddings.ts"
import { nanoid } from '@/lib/utils';
import { index, pgTable, text, varchar, vector } from 'drizzle-orm/pg-core';
import { resources } from './resources';

export const embeddings = pgTable(
  'embeddings',
  {
    id: varchar('id', { length: 191 })
      .primaryKey()
      .$defaultFn(() => nanoid()),
    resourceId: varchar('resource_id', { length: 191 }).references(
      () => resources.id,
      { onDelete: 'cascade' },
    ),
    content: text('content').notNull(),
    embedding: vector('embedding', { dimensions: 1536 }).notNull(),
  },
  table => ({
    embeddingIndex: index('embeddingIndex').using(
      'hnsw',
      table.embedding.op('vector_cosine_ops'),
    ),
  }),
);
```

This table has four columns:

- `id` - unique identifier
- `resourceId` - a foreign key relation to the full source material
- `content` - the plain text chunk
- `embedding` - the vector representation of the plain text chunk

To perform similarity search, you also need to include an index ([HNSW](https://github.com/pgvector/pgvector?tab=readme-ov-file#hnsw) or [IVFFlat](https://github.com/pgvector/pgvector?tab=readme-ov-file#ivfflat)) on this column for better performance.

To push this change to the database, run the following command:

<Snippet text="pnpm db:push" />

### Add Embedding Logic

Now that you have a table to store embeddings, it’s time to write the logic to create the embeddings.

Create a file with the following command:

<Snippet text="mkdir lib/ai && touch lib/ai/embedding.ts" />

### Generate Chunks

Remember, to create an embedding, you will start with a piece of source material (unknown length), break it down into smaller chunks, embed each chunk, and then save the chunk to the database. Let’s start by creating a function to break the source material into small chunks.

```tsx filename="lib/ai/embedding.ts"
const generateChunks = (input: string): string[] => {
  return input
    .trim()
    .split('.')
    .filter(i => i !== '');
};
```

This function will take an input string and split it by periods, filtering out any empty items. This will return an array of strings. It is worth experimenting with different chunking techniques in your projects as the best technique will vary.

### Install AI SDK

You will use the Vercel AI SDK to create embeddings. This will require two more dependencies, which you can install by running the following command:

<Snippet text="pnpm add ai @ai-sdk/openai" />

This will install the [Vercel AI SDK](https://sdk.vercel.ai/docs) and the [OpenAI provider](/providers/ai-sdk-providers/openai).

<Note>
  Vercel AI SDK is designed to be a unified interface to interact with any large
  language model. This means that you can change model and providers with just
  one line of code! Learn more about [available providers](/providers) and
  [building custom providers](/providers/community-providers/custom-providers)
  in the [providers](/providers) section.
</Note>

### Generate Embeddings

Let’s add a function to generate embeddings. Copy the following code into your `lib/ai/embedding.ts` file.

```tsx filename="lib/ai/embedding.ts" highlight="1-2,4,13-22"
import { embedMany } from 'ai';
import { openai } from '@ai-sdk/openai';

const embeddingModel = openai.embedding('text-embedding-ada-002');

const generateChunks = (input: string): string[] => {
  return input
    .trim()
    .split('.')
    .filter(i => i !== '');
};

export const generateEmbeddings = async (
  value: string,
): Promise<Array<{ embedding: number[]; content: string }>> => {
  const chunks = generateChunks(value);
  const { embeddings } = await embedMany({
    model: embeddingModel,
    values: chunks,
  });
  return embeddings.map((e, i) => ({ content: chunks[i], embedding: e }));
};
```

In this code, you first define the model you want to use for the embeddings. In this example, you are using OpenAI’s `text-embedding-ada-002` embedding model.

Next, you create an asynchronous function called `generateEmbeddings`. This function will take in the source material (`value`) as an input and return a promise of an array of objects, each containing an embedding and content. Within the function, you first generate chunks for the input. Then, you pass those chunks to the [`embedMany`](/docs/reference/ai-sdk-core/embed-many) function imported from the Vercel AI SDK which will return embeddings of the chunks you passed in. Finally, you map over and return the embeddings in a format that is ready to save in the database.

### Update Server Action

Open the file at `lib/actions/resources.ts`. This file has one function, `createResource`, which, as the name implies, allows you to create a resource.

```tsx filename="lib/actions/resources.ts"
'use server';

import {
  NewResourceParams,
  insertResourceSchema,
  resources,
} from '@/lib/db/schema/resources';
import { db } from '../db';

export const createResource = async (input: NewResourceParams) => {
  try {
    const { content } = insertResourceSchema.parse(input);

    const [resource] = await db
      .insert(resources)
      .values({ content })
      .returning();

    return 'Resource successfully created.';
  } catch (e) {
    if (e instanceof Error)
      return e.message.length > 0 ? e.message : 'Error, please try again.';
  }
};
```

This function is a [Server Action](https://nextjs.org/docs/app/building-your-application/data-fetching/server-actions-and-mutations#with-client-components), as denoted by the `“use server”;` directive at the top of the file. This means that it can be called anywhere in your Next.js application. This function will take an input, run it through a [Zod](https://zod.dev) schema to ensure it adheres to the correct schema, and then creates a new resource in the database. This is the ideal location to generate and store embeddings of the newly created resources.

Update the file with the following code:

```tsx filename="lib/actions/resources.ts" highlight="9-10,21-27,29"
'use server';

import {
  NewResourceParams,
  insertResourceSchema,
  resources,
} from '@/lib/db/schema/resources';
import { db } from '../db';
import { generateEmbeddings } from '../ai/embedding';
import { embeddings as embeddingsTable } from '../db/schema/embeddings';

export const createResource = async (input: NewResourceParams) => {
  try {
    const { content } = insertResourceSchema.parse(input);

    const [resource] = await db
      .insert(resources)
      .values({ content })
      .returning();

    const embeddings = await generateEmbeddings(content);
    await db.insert(embeddingsTable).values(
      embeddings.map(embedding => ({
        resourceId: resource.id,
        ...embedding,
      })),
    );

    return 'Resource successfully created and embedded.';
  } catch (error) {
    return error instanceof Error && error.message.length > 0
      ? error.message
      : 'Error, please try again.';
  }
};
```

First, you call the `generateEmbeddings` function created in the previous step, passing in the source material (`content`). Once you have your embeddings (`e`) of the source material, you can save them to the database, passing the `resourceId` alongside each embedding.

### Create Root Page

Great! Let's build the frontend. Vercel AI SDK’s [`useChat`](/docs/reference/ai-sdk-ui/use-chat) hook allows you to easily create a conversational user interface for your chatbot application.

Replace your root page (`app/page.tsx`) with the following code.

```tsx filename="app/page.tsx"
'use client';

import { useChat } from 'ai/react';

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit } = useChat();
  return (
    <div className="flex flex-col w-full max-w-md py-24 mx-auto stretch">
      <div className="space-y-4">
        {messages.map(m => (
          <div key={m.id} className="whitespace-pre-wrap">
            <div>
              <div className="font-bold">{m.role}</div>
              <p>{m.content}</p>
            </div>
          </div>
        ))}
      </div>

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

The `useChat` hook enables the streaming of chat messages from your AI provider (you will be using OpenAI), manages the state for chat input, and updates the UI automatically as new messages are received.

Run the following command to start the Next.js dev server:

<Snippet text="pnpm run dev" />

Head to [http://localhost:3000](http://localhost:3000/). You should see an empty screen with an input bar floating at the bottom. Try to send a message. The message shows up in the UI for a fraction of a second and then disappears. This is because you haven’t set up the corresponding API route to call the model! By default, `useChat` will send a POST request to the `/api/chat` endpoint with the `messages` as the request body.

<Note>You can customize the endpoint in the useChat configuration object</Note>

### Create API Route

In Next.js, you can create custom request handlers for a given route using [Route Handlers](https://nextjs.org/docs/app/building-your-application/routing/route-handlers). Route Handlers are defined in a `route.ts` file and can export HTTP methods like `GET`, `POST`, `PUT`, `PATCH` etc.

Create a file at `app/api/chat/route.ts` by running the following command:

<Snippet text="mkdir -p app/api/chat && touch app/api/chat/route.ts" />

Open the file and add the following code:

```tsx filename="app/api/chat/route.ts"
import { openai } from '@ai-sdk/openai';
import { convertToCoreMessages, streamText } from 'ai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = await streamText({
    model: openai('gpt-4o'),
    messages: convertToCoreMessages(messages),
  });

  return result.toDataStreamResponse();
}
```

In this code, you declare and export an asynchronous function called POST. You retrieve the `messages` from the request body and then pass them to the [`streamText`](/docs/reference/ai-sdk-core/stream-text) function imported from the Vercel AI SDK, alongside the model you would like to use. Finally, you return the model’s response in `AIStreamResponse` format.

Head back to the browser and try to send a message again. You should see a response from the model streamed directly in!

### Refining your prompt

While you now have a working chatbot, it isn't doing anything special.

Let’s add system instructions to refine and restrict the model’s behavior. In this case, you want the model to only use information it has retrieved to generate responses. Update your route handler with the following code:

```tsx filename="app/api/chat/route.ts" highlight="12-14"
import { openai } from '@ai-sdk/openai';
import { convertToCoreMessages, streamText } from 'ai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = await streamText({
    model: openai('gpt-4o'),
    system: `You are a helpful assistant. Check your knowledge base before answering any questions.
    Only respond to questions using information from tool calls.
    if no relevant information is found in the tool calls, respond, "Sorry, I don't know."`,
    messages: convertToCoreMessages(messages),
  });

  return result.toDataStreamResponse();
}
```

Head back to the browser and try to ask the model what your favorite food is. The model should now respond exactly as you instructed above (“Sorry, I don’t know”) given it doesn’t have any relevant information.

In its current form, your chatbot is now, well, useless. How do you give the model the ability to add and query information?

### Using Tools

A [tool](/docs/foundations/tools) is a function that can be called by the model to perform a specific task. You can think of a tool like a program you give to the model that it can run as and when it deems necessary.

Let’s see how you can create a tool to give the model the ability to create, embed and save a resource to your chatbots’ knowledge base.

### Add Resource Tool

Update your route handler with the following code:

```tsx filename="app/api/chat/route.ts" highlight="18-29"
import { createResource } from '@/lib/actions/resources';
import { openai } from '@ai-sdk/openai';
import { convertToCoreMessages, streamText, tool } from 'ai';
import { z } from 'zod';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = await streamText({
    model: openai('gpt-4o'),
    system: `You are a helpful assistant. Check your knowledge base before answering any questions.
    Only respond to questions using information from tool calls.
    if no relevant information is found in the tool calls, respond, "Sorry, I don't know."`,
    messages: convertToCoreMessages(messages),
    tools: {
      addResource: tool({
        description: `add a resource to your knowledge base.
          If the user provides a random piece of knowledge unprompted, use this tool without asking for confirmation.`,
        parameters: z.object({
          content: z
            .string()
            .describe('the content or resource to add to the knowledge base'),
        }),
        execute: async ({ content }) => createResource({ content }),
      }),
    },
  });

  return result.toDataStreamResponse();
}
```

In this code, you define a tool called `addResource`. This tool has three elements:

- **description**: description of the tool that will influence when the tool is picked.
- **parameters**: [Zod schema](https://sdk.vercel.ai/docs/foundations/tools#schema-specification-and-validation-with-zod) that defines the parameters necessary for the tool to run.
- **execute**: An asynchronous function that is called with the arguments from the tool call.

In simple terms, on each generation, the model will decide whether it should call the tool. If it deems it should call the tool, it will extract the parameters from the input and then append a new `message` to the `messages` array of type `tool-call`. The AI SDK will then run the `execute` function with the parameters provided by the `tool-call` message.

Head back to the browser and tell the model your favorite food. You should see an empty response in the UI. Did anything happen? Let’s see. Run the following command in a new terminal window.

<Snippet text="pnpm db:studio" />

This will start Drizzle Studio where we can view the rows in our database. You should see a new row in both the `embeddings` and `resources` table with your favorite food!

Let’s make a few changes in the UI to communicate to the user when a tool has been called. Head back to your root page (`app/page.tsx`) and add the following code:

```tsx filename="app/page.tsx" highlight="14-22"
'use client';

import { useChat } from 'ai/react';

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit } = useChat();
  return (
    <div className="flex flex-col w-full max-w-md py-24 mx-auto stretch">
      <div className="space-y-4">
        {messages.map(m => (
          <div key={m.id} className="whitespace-pre-wrap">
            <div>
              <div className="font-bold">{m.role}</div>
              <p>
                {m.content.length > 0 ? (
                  m.content
                ) : (
                  <span className="italic font-light">
                    {'calling tool: ' + m?.toolInvocations?.[0].toolName}
                  </span>
                )}
              </p>
            </div>
          </div>
        ))}
      </div>

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

With this change, you now conditionally render the tool that has been called directly in the UI. Save the file and head back to browser. Tell the model your favorite movie. You should see which tool is called in place of the model’s typical text response.

### Improving UX with Tool Roundtrips

It would be nice if the model could summarize the action too. However, technically, once the model calls a tool, it has completed its generation as it ‘generated’ a tool call. How could you achieve this desired behaviour?

The AI SDK has a feature called [`maxToolCallRoundtrips`](/docs/ai-sdk-core/tools-and-tool-calling#tool-roundtrips) which will automatically send tool call results back to the model!

Open your root page (`app/page.tsx`) and add the following key to the `useChat` configuration object:

```tsx filename="app/page.tsx" highlight="3-5"
// ... Rest of your code

const { messages, input, handleInputChange, handleSubmit } = useChat({
  maxToolRoundtrips: 2,
});

// ... Rest of your code
```

Head back to the browser and tell the model your favorite pizza topping (note: pineapple is not an option). You should see a follow-up response from the model confirming the action.

### Retrieve Resource Tool

The model can now add and embed arbitrary information to your knowledge base. However, it still isn’t able to query it. Let’s create a new tool to allow the model to answer questions by finding relevant information in your knowledge base.

To find similar content, you will need to embed the users query, search the database for semantic similarities, then pass those items to the model as context alongside the query. To achieve this, let’s update your embedding logic file (`lib/ai/embedding.ts`):

```tsx filename="lib/ai/embedding.ts" highlight="1,3-5,27-34,36-49"
import { embed, embedMany } from 'ai';
import { openai } from '@ai-sdk/openai';
import { db } from '../db';
import { cosineDistance, desc, gt, sql } from 'drizzle-orm';
import { embeddings } from '../db/schema/embeddings';

const embeddingModel = openai.embedding('text-embedding-ada-002');

const generateChunks = (input: string): string[] => {
  return input
    .trim()
    .split('.')
    .filter(i => i !== '');
};

export const generateEmbeddings = async (
  value: string,
): Promise<Array<{ embedding: number[]; content: string }>> => {
  const chunks = generateChunks(value);
  const { embeddings } = await embedMany({
    model: embeddingModel,
    values: chunks,
  });
  return embeddings.map((e, i) => ({ content: chunks[i], embedding: e }));
};

export const generateEmbedding = async (value: string): Promise<number[]> => {
  const input = value.replaceAll('\\n', ' ');
  const { embedding } = await embed({
    model: embeddingModel,
    value: input,
  });
  return embedding;
};

export const findRelevantContent = async (userQuery: string) => {
  const userQueryEmbedded = await generateEmbedding(userQuery);
  const similarity = sql<number>`1 - (${cosineDistance(
    embeddings.embedding,
    userQueryEmbedded,
  )})`;
  const similarGuides = await db
    .select({ name: embeddings.content, similarity })
    .from(embeddings)
    .where(gt(similarity, 0.5))
    .orderBy(t => desc(t.similarity))
    .limit(4);
  return similarGuides;
};
```

In this code, you add two functions:

- `generateEmbedding`: generate a single embedding from an input string
- `findRelevantContent`: embeds the user’s query, searches the database for similar items, then returns relevant items

With that done, it’s onto the final step: creating the tool.

Go back to your route handler (`api/chat/route.ts`) and add a new tool called `getInformation`:

```ts filename="api/chat/route.ts" highlight="5,30-36"
import { createResource } from '@/lib/actions/resources';
import { openai } from '@ai-sdk/openai';
import { convertToCoreMessages, streamText, tool } from 'ai';
import { z } from 'zod';
import { findRelevantContent } from '@/lib/ai/embedding';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = await streamText({
    model: openai('gpt-4o'),
    messages: convertToCoreMessages(messages),
    system: `You are a helpful assistant. Check your knowledge base before answering any questions.
    Only respond to questions using information from tool calls.
    if no relevant information is found in the tool calls, respond, "Sorry, I don't know."`,
    tools: {
      addResource: tool({
        description: `add a resource to your knowledge base.
          If the user provides a random piece of knowledge unprompted, use this tool without asking for confirmation.`,
        parameters: z.object({
          content: z
            .string()
            .describe('the content or resource to add to the knowledge base'),
        }),
        execute: async ({ content }) => createResource({ content }),
      }),
      getInformation: tool({
        description: `get information from your knowledge base to answer questions.`,
        parameters: z.object({
          question: z.string().describe('the users question'),
        }),
        execute: async ({ question }) => findRelevantContent(question),
      }),
    },
  });

  return result.toDataStreamResponse();
}
```

Head back to the browser, refresh the page, and ask for your favorite food. You should see the model call the `getInformation` tool, and then use the relevant information to formulate a response!

### Conclusion

Congratulations, you have successfully built an AI chatbot that can dynamically add and retrieve information to and from a knowledge base. Throughout this guide, you learned how to create and store embeddings, set up server actions to manage resources, and use tools to extend the capabilities of your chatbot.

---
title: Multi-Modal Chatbot
description: Learn how to build a multi-modal chatbot with the Vercel AI SDK!
---

## Multi-Modal Chatbot Guide

In this guide, you will build a multi-modal AI-chatbot with a streaming user interface.

Multi-modal refers to the ability of the chatbot to understand and generate responses in multiple formats, such as text, images, and videos. In this example, we will focus on sending images and generating text-based responses.

### Prerequisites

To follow this quickstart, you'll need:

- Node.js 18+ and pnpm installed on your local development machine.
- An OpenAI API key.

If you haven't obtained your OpenAI API key, you can do so by [signing up](https://platform.openai.com/signup/) on the OpenAI website.

### Create Your Application

Start by creating a new Next.js application. This command will create a new directory named `multi-modal-chatbot` and set up a basic Next.js application inside it.

<div className="mb-4">
  <Note>
    Be sure to select yes when prompted to use the App Router. If you are
    looking for the Next.js Pages Router quickstart guide, you can find it
    [here](/docs/getting-started/nextjs-pages-router).
  </Note>
</div>

<Snippet text="pnpm create next-app@latest multi-modal-chatbot" />

Navigate to the newly created directory:

<Snippet text="cd multi-modal-chatbot" />

### Install dependencies

Install `ai` and `@ai-sdk/openai`, the Vercel AI package and Vercel AI SDK's [ OpenAI provider ](/providers/ai-sdk-providers/openai) respectively.

<Note>
  Vercel AI SDK is designed to be a unified interface to interact with any large
  language model. This means that you can change model and providers with just
  one line of code! Learn more about [available providers](/providers) and
  [building custom providers](/providers/community-providers/custom-providers)
  in the [providers](/providers) section.
</Note>
<div className="my-4">
  <Tabs items={['pnpm', 'npm', 'yarn']}>
    <Tab>
      <Snippet text="pnpm add ai @ai-sdk/openai" dark />
    </Tab>
    <Tab>
      <Snippet text="npm install ai @ai-sdk/openai" dark />
    </Tab>
    <Tab>
      <Snippet text="yarn add ai @ai-sdk/openai" dark />
    </Tab>
  </Tabs>
</div>

<Note type="secondary" fill>
  Make sure you are using `ai` version 3.2.27 or higher.
</Note>

### Configure OpenAI API key

Create a `.env.local` file in your project root and add your OpenAI API Key. This key is used to authenticate your application with the OpenAI service.

<Snippet text="touch .env.local" />

Edit the `.env.local` file:

```env filename=".env.local"
OPENAI_API_KEY=xxxxxxxxx
```

Replace `xxxxxxxxx` with your actual OpenAI API key.

<Note className="mb-4">
  Vercel AI SDK's OpenAI Provider will default to using the `OPENAI_API_KEY`
  environment variable.
</Note>

### Implementation Plan

To build a multi-modal chatbot, you will need to:

- Create a Route Handler to handle incoming chat messages and generate responses.
- Wire up the UI to display chat messages, provide a user input, and handle submitting new messages.
- Add the ability to upload images and attach them alongside the chat messages.

### Create a Route Handler

Create a route handler, `app/api/chat/route.ts` and add the following code:

```tsx filename="app/api/chat/route.ts"
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

1. Define an asynchronous `POST` request handler and extract `messages` from the body of the request. The `messages` variable contains a history of the conversation between you and the chatbot and provides the chatbot with the necessary context to make the next generation.
2. Call [`streamText`](/docs/reference/ai-sdk-core/stream-text), which is imported from the `ai` package. This function accepts a configuration object that contains a `model` provider (imported from `@ai-sdk/openai`) and `messages` (defined in step 1). You can pass additional [settings](/docs/ai-sdk-core/settings) to further customise the model's behaviour.
3. The `streamText` function returns a [`StreamTextResult`](/docs/reference/ai-sdk-core/stream-text#result-object). This result object contains the [ `toDataStreamResponse` ](/docs/reference/ai-sdk-core/stream-text#to-ai-stream-response) function which converts the result to a streamed response object.
4. Finally, return the result to the client to stream the response.

This Route Handler creates a POST request endpoint at `/api/chat`.

### Wire up the UI

Now that you have a Route Handler that can query a large language model (LLM), it's time to setup your frontend. [ AI SDK UI ](/docs/ai-sdk-ui) abstracts the complexity of a chat interface into one hook, [`useChat`](/docs/reference/ai-sdk-ui/use-chat).

Update your root page (`app/page.tsx`) with the following code to show a list of chat messages and provide a user message input:

```tsx filename="app/page.tsx"
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

<Note>
  Make sure you add the `"use client"` directive to the top of your file. This
  allows you to add interactivity with Javascript.
</Note>

This page utilizes the `useChat` hook, which will, by default, use the `POST` API route you created earlier (`/api/chat`). The hook provides functions and state for handling user input and form submission. The `useChat` hook provides multiple utility functions and state variables:

- `messages` - the current chat messages (an array of objects with `id`, `role`, and `content` properties).
- `input` - the current value of the user's input field.
- `handleInputChange` and `handleSubmit` - functions to handle user interactions (typing into the input field and submitting the form, respectively).
- `isLoading` - boolean that indicates whether the API request is in progress.

### Add Image Upload

To make your chatbot multi-modal, let's add the ability to upload and send images to the model. There are two ways to send attachments alongside a message with the `useChat` hook: by [ providing a `FileList` object ](/docs/ai-sdk-ui/chatbot#filelist) or a [ list of URLs ](/docs/ai-sdk-ui/chatbot#urls) to the `handleSubmit` function. In this guide, you will be using the `FileList` approach as it does not require any additional setup.

Update your root page (`app/page.tsx`) with the following code:

```tsx filename="app/page.tsx" highlight="4,9-10,18-31,37-47,49-59"
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
4. Add a file input field to the form, including an `onChange` handler to handle updating the files state.

### Running Your Application

With that, you have built everything you need for your multi-modal chatbot! To start your application, use the command:

<Snippet text="pnpm run dev" />

Head to your browser and open http://localhost:3000. You should see an input field and a button to upload an image.

Upload a file and ask the model to describe what it sees. Watch as the model's response is streamed back to you!

# Advanced Concepts
---
title: Stopping Streams
description: Learn how to cancel streams with the Vercel AI SDK
---

## Stopping Streams

Cancelling ongoing streams is often needed.
For example, users might want to stop a stream when they realize that the response is not what they want.

The different parts of the Vercel AI SDK support cancelling streams in different ways.

### Stopping Streams within AI SDK Core

The AI SDK functions have an `abortSignal` argument that you can use to cancel a stream.
You would use this if you want to cancel a stream from the server side to the LLM API, e.g. by
forwarding the `abortSignal` from the request.

```tsx highlight="10,11"
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

### Stopping Streams AI SDK UI

The hooks, e.g. `useChat` or `useCompletion`, provide a `stop` helper function that can be used to cancel a stream.
This will cancel the stream from the client side to the server.

```tsx file="app/page.tsx" highlight="9,18-20"
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

### Stopping Streams AI SDK RSC

<Note type="warning">
  The AI SDK RSC does not currently support stopping streams.
</Note>

## Stream Back-pressure and Cancellation

This page focuses on understanding back-pressure and cancellation when working with streams. You do not need to know this information to use the Vercel AI SDK, but for those interested, it offers a deeper dive on why and how the SDK optimally streams responses.

In the following sections, we'll explore back-pressure and cancellation in the context of a simple example program. We'll discuss the issues that can arise from an eager approach and demonstrate how a lazy approach can resolve them.

### Back-pressure and Cancellation with Streams

Let's begin by setting up a simple example program:

```jsx
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

In this example, we create an async-generator that yields positive integers, a `ReadableStream` that wraps our integer generator, and a reader which will read values out of our stream. Notice, too, that our integer generator logs out `"yielding ${i}"`, and our reader logs out `"read ${value}"`. Both take an arbitrary amount of time to process data, represented with a 100ms sleep in our generator, and a 1sec sleep in our reader.

### Back-pressure

If you were to run this program, you'd notice something funny. We'll see roughly 10 "yield" logs for every "read" log. This might seem obvious, the generator can push values 10x faster than the reader can pull them out. But it represents a problem, our `stream` has to maintain an ever expanding queue of items that have been pushed in but not pulled out.

The problem stems from the way we wrap our generator into a stream. Notice the use of `for await (…)` inside our `start` handler. This is an **eager** for-loop, and it is constantly running to get the next value from our generator to be enqueued in our stream. This means our stream does not respect back-pressure, the signal from the consumer to the producer that more values aren't needed _yet_. We've essentially spawned a thread that will perpetually push more data into the stream, one that runs as fast as possible to push new data immediately. Worse, there's no way to signal to this thread to stop running when we don't need additional data.

To fix this, `ReadableStream` allows a `pull` handler. `pull` is called every time the consumer attempts to read more data from our stream (if there's no data already queued internally). But it's not enough to just move the `for await(…)` into `pull`, we also need to convert from an eager enqueuing to a **lazy** one. By making these 2 changes, we'll be able to react to the consumer. If they need more data, we can easily produce it, and if they don't, then we don't need to spend any time doing unnecessary work.

```jsx
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

Our `createStream` is a little more verbose now, but the new code is important. First, we need to manually call our `iterator.next()` method. This returns a `Promise` for an object with the type signature `{ done: boolean, value: T }`. If `done` is `true`, then we know that our iterator won't yield any more values and we must `close` the stream (this allows the consumer to know that the stream is also finished producing values). Else, we need to `enqueue` our newly produced value.

When we run this program, we see that our "yield" and "read" logs are now paired. We're no longer yielding 10x integers for every read! And, our stream now only needs to maintain 1 item in its internal buffer. We've essentially given control to the consumer, so that it's responsible for producing new values as it needs it. Neato!

### Cancellation

Let's go back to our initial eager example, with 1 small edit. Now instead of reading 10,000 integers, we're only going to read 3:

```jsx
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

We're back to yielding 10x the number of values read. But notice now, after we've read 3 values, we're continuing to yield new values. We know that our reader will never read another value, but our stream doesn't! The eager `for await (…)` will continue forever, loudly enqueuing new values into our stream's buffer and increasing our memory usage until it consumes all available program memory.

The fix to this is exactly the same: use `pull` and manual iteration. By producing values _**lazily**_, we tie the lifetime of our integer generator to the lifetime of the reader. Once the reads stop, the yields will stop too:

```jsx
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

Since the solution is the same as implementing back-pressure, it shows that they're just 2 facets of the same problem: Pushing values into a stream should be done **lazily**, and doing it eagerly results in expected problems.

### Tying Stream Laziness to AI Responses

Now let's imagine you're integrating AIBot service into your product. Users will be able to prompt "count from 1 to infinity", the browser will fetch your AI API endpoint, and your servers connect to AIBot to get a response. But "infinity" is, well, infinite. The response will never end!

After a few seconds, the user gets bored and navigates away. Or maybe you're doing local development and a hot-module reload refreshes your page. The browser will have ended its connection to the API endpoint, but will your server end its connection with AIBot?

If you used the eager `for await (...)` approach, then the connection is still running and your server is asking for more and more data from AIBot. Our server spawned a "thread" and there's no signal when we can end the eager pulls. Eventually, the server is going to run out of memory (remember, there's no active fetch connection to read the buffering responses and free them).

{/* When we started writing the streaming code for the Vercel AI SDK, we confirm aborting a fetch will end a streamed response from Next.js */}

With the lazy approach, this is taken care of for you. Because the stream will only request new data from AIBot when the consumer requests it, navigating away from the page naturally frees all resources. The fetch connection aborts and the server can clean up the response. The `ReadableStream` tied to that response can now be garbage collected. When that happens, the connection it holds to AIBot can then be freed.


## Caching Responses

Depending on the type of application you're building, you may want to cache the responses you receive from your AI provider, at least temporarily.

Each stream helper for each provider has special lifecycle callbacks you can use.
The one of interest is likely `onFinish`, which is called when the stream is closed. This is where you can cache the full response.

Here's an example of how you can implement caching using Vercel KV and Next.js to cache the OpenAI response for 1 hour:

### Example: Vercel KV

This example uses [Vercel KV](https://vercel.com/storage/kv) and Next.js to cache the OpenAI response for 1 hour.

```tsx filename="app/api/chat/route.ts"
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

---
title: Multiple Streamables
description: Learn to handle multiple streamables in your application.
---

## Multiple Streams

### Multiple Streamable UIs

The AI SDK RSC APIs allow you to compose and return any number of streamable UIs, along with other data, in a single request. This can be useful when you want to decouple the UI into smaller components and stream them separately.

```tsx file='app/actions.tsx'
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

The client side code is similar to the previous example, but the [tool call](/docs/ai-sdk-core/tools-and-tool-calling) will return the new data structure with the weather and forecast UIs. Depending on the speed of getting weather and forecast data, these two components might be updated independently.

### Nested Streamable UIs

You can stream UI components within other UI components. This allows you to create complex UIs that are built up from smaller, reusable components. In the example below, we pass a `historyChart` streamable as a prop to a `StockCard` component. The StockCard can render the `historyChart` streamable, and it will automatically update as the server responds with new data.

```tsx file='app/actions.tsx'
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

## Rate Limiting

Rate limiting helps you protect your APIs from abuse. It involves setting a
maximum threshold on the number of requests a client can make within a
specified timeframe. This simple technique acts as a gatekeeper,
preventing excessive usage that can degrade service performance and incur
unnecessary costs.

### Rate Limiting with Vercel KV and Upstash Ratelimit

In this example, you will protect an API endpoint using [Vercel KV](https://vercel.com/storage/kv)
and [Upstash Ratelimit](https://github.com/upstash/ratelimit).

```tsx filename='app/api/generate/route.ts'
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

### Simplify API Protection

With Vercel KV and Upstash Ratelimit, it is possible to protect your APIs
from such attacks with ease. To learn more about how Ratelimit works and
how it can be configured to your needs, see [Ratelimit Documentation](https://upstash.com/docs/oss/sdks/ts/ratelimit/overview).

## Rendering User Interfaces with Language Models

Language models generate text, so at first it may seem like you would only need to render text in your application.

```tsx highlight="16" filename="app/actions.tsx"
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

Above, the language model is passed a [tool](/docs/ai-sdk-core/tools-and-tool-calling) called `getWeather` that returns the weather information as text. However, instead of returning text, if you return a JSON object that represents the weather information, you can use it to render a React component instead.

```tsx highlight="18-23" filename="app/action.ts"
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

```tsx filename="app/page.tsx"
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

Here's a little preview of what that might look like.

<div className="not-prose flex flex-col2">
  <CardPlayer
    type="weather"
    title="Weather"
    description="An example of an assistant that renders the weather information in a streamed component."
  />
</div>

Rendering interfaces as part of language model generations elevates the user experience of your application, allowing people to interact with language models beyond text.

They also make it easier for you to interpret [sequential tool calls](/docs/ai-sdk-rsc/multistep-interfaces) that take place in multiple steps and help identify and debug where the model reasoned incorrectly.

### Rendering Multiple User Interfaces

To recap, an application has to go through the following steps to render user interfaces as part of model generations:

1. The user prompts the language model.
2. The language model generates a response that includes a tool call.
3. The tool call returns a JSON object that represents the user interface.
4. The response is sent to the client.
5. The client receives the response and checks if the latest message was a tool call.
6. If it was a tool call, the client renders the user interface based on the JSON object returned by the tool call.

Most applications have multiple tools that are called by the language model, and each tool can return a different user interface.

For example, a tool that searches for courses can return a list of courses, while a tool that searches for people can return a list of people. As this list grows, the complexity of your application will grow as well and it can become increasingly difficult to manage these user interfaces.

```tsx filename='app/page.tsx'
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

### Rendering User Interfaces on the Server

The **AI SDK RSC (`ai/rsc`)** takes advantage of RSCs to solve the problem of managing all your React components on the client side, allowing you to render React components on the server and stream them to the client.

Rather than conditionally rendering user interfaces on the client based on the data returned by the language model, you can directly stream them from the server during a model generation.

```tsx highlight="3,22-31,38" filename="app/action.ts"
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

The [`createStreamableUI`](/docs/reference/ai-sdk-rsc/create-streamable-ui) function belongs to the `ai/rsc` module and creates a stream that can send React components to the client.

On the server, you render the `<WeatherCard/>` component with the props passed to it, and then stream it to the client. On the client side, you only need to render the UI that is streamed from the server.

```tsx filename="app/page.tsx" highlight="4"
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

> **Note:** You can also render text on the server and stream it to the client using React Server Components. This way, all operations from language model generation to UI rendering can be done on the server, while the client only needs to render the UI that is streamed from the server.


## Generative User Interfaces

Since language models can render user interfaces as part of their generations, the resulting model generations are referred to as generative user interfaces.

In this section we will learn more about generative user interfaces and their impact on the way AI applications are built.

### Deterministic Routes and Probabilistic Routing

Generative user interfaces are not deterministic in nature because they depend on the model's generation output. Since these generations are probabilistic in nature, it is possible for every user query to result in a different user interface.

Users expect their experience using your application to be predictable, so non-deterministic user interfaces can sound like a bad idea at first. However, language models can be set up to limit their generations to a particular set of outputs using their ability to call functions.

When language models are provided with a set of function definitions and instructed to execute any of them based on user query, they do either one of the following things:

- Execute a function that is most relevant to the user query.
- Not execute any function if the user query is out of bounds of the set of functions available to them.

```tsx filename='app/actions.ts'
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

### Language Models as Routers

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

With language models becoming better at reasoning, we believe that there is a future where developers only write core application specific components while models take care of routing them based on the user's state in an application.

With generative user interfaces, the language model decides which user interface to render based on the user's state in the application, giving users the flexibility to interact with your application in a conversational manner instead of navigating through a series of predefined routes.

#### Routing by parameters

For routes like:

- `/profile/[username]`
- `/search?q=[query]`
- `/media/[id]`

that have segments dependent on dynamic data, the language model can generate the correct parameters and render the user interface.

For example, when you're in a search application, you can ask the language model to search for artworks from different artists. The language model will call the search function with the artist's name as a parameter and render the search results.

<div className="not-prose">
  <CardPlayer
    type="media-search"
    title="Media Search"
    description="Let your users see more than words can say by rendering components directly within your search experience."
  />
</div>

#### Routing by sequence

For actions that require a sequence of steps to be completed by navigating through different routes, the language model can generate the correct sequence of routes to complete in order to fulfill the user's request.

For example, when you're in a calendar application, you can ask the language model to schedule a happy hour evening with your friends. The language model will then understand your request and will perform the right sequence of [tool calls](/docs/ai-sdk-core/tools-and-tool-calling) to:

1. Lookup your calendar
2. Lookup your friends' calendars
3. Determine the best time for everyone
4. Search for nearby happy hour spots
5. Create an event and send out invites to your friends

<div className="not-prose">
  <CardPlayer
    type="event-planning"
    title="Planning an Event"
    description="The model calls functions and generates interfaces based on user intent, acting like a router."
  />
</div>

Just by defining functions to lookup contacts, pull events from a calendar, and search for nearby locations, the model is able to sequentially navigate the routes for you.

## Multistep Interfaces

Multistep interfaces refer to user interfaces that require multiple independent steps to be executed in order to complete a specific task.

In order to understand multistep interfaces, it is important to understand two concepts:

- Tool composition
- Application context

**Tool composition** is the process of combining multiple [tools](/docs/ai-sdk-core/tools-and-tool-calling) to create a new tool. This is a powerful concept that allows you to break down complex tasks into smaller, more manageable steps.

**Application context** refers to the state of the application at any given point in time. This includes the user's input, the output of the language model, and any other relevant information.

When designing multistep interfaces, you need to consider how the tools in your application can be composed together to form a coherent user experience as well as how the application context changes as the user progresses through the interface.

### Application Context

The application context can be thought of as the conversation history between the user and the language model. The richer the context, the more information the model has to generate relevant responses.

In the context of multistep interfaces, the application context becomes even more important. This is because **the user's input in one step may affect the output of the model in the next step**.

For example, consider a meal logging application that helps users track their daily food intake. The language model is provided with the following tools:

- `log_meal` takes in parameters like the name of the food, the quantity, and the time of consumption to log a meal.
- `delete_meal` takes in the name of the meal to be deleted.

When the user logs a meal, the model generates a response confirming the meal has been logged.

```txt highlight="2"
User: Log a chicken shawarma for lunch.
Tool: log_meal("chicken shawarma", "250g", "12:00 PM")
Model: Chicken shawarma has been logged for lunch.
```

Now when the user decides to delete the meal, the model should be able to reference the previous step to identify the meal to be deleted.

```txt highlight="7"
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

### Tool Composition

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

```txt highlight="8"
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

```txt highlight="5,6"
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

```txt highlight="2-4"
User: What's the status of my wife's upcoming flight?
Tool: lookupContacts() -> ["John Doe", "Jane Doe"]
Tool: lookupBooking("Jane Doe") -> "BA123 confirmed"
Tool: lookupFlight("BA123") -> "Flight BA123 is scheduled to depart on 12th December."
Model: Your wife's flight BA123 is confirmed and scheduled to depart on 12th December.
```

In this example, the `lookupBooking` tool is used to provide the user with the status of their wife's upcoming flight. By composing this tool with the existing tools, the model is able to generate a response that includes the booking status and the departure date of the flight without requiring the user to provide additional information.

As a result, the more tools you design that can be composed together, the more complex and powerful your application can become.


## Vercel Deployment Guide

In this guide, you will deploy an AI application to [Vercel](https://vercel.com) using [Next.js](https://nextjs.org) (App Router).

Vercel is a platform for developers that provides the tools, workflows, and infrastructure you need to build and deploy your web apps faster, without the need for additional configuration.

Vercel allows for automatic deployments on every branch push and merges onto the production branch of your GitHub, GitLab, and Bitbucket projects. It is a great option for deploying your AI application.

### Before You Begin

To follow along with this guide, you will need:

- a Vercel account
- an account with a Git provider (this tutorial will use [Github](https://github.com))
- an OpenAI API key

This guide will teach you how to deploy the application you built in the Next.js (App Router) quickstart tutorial to Vercel. If you haven’t completed the quickstart guide, you can start with [this repo](https://github.com/vercel-labs/ai-sdk-deployment-guide).

### Commit Changes

Vercel offers a powerful git-centered workflow that automatically deploys your application to production every time you push to your repository’s main branch.

Before committing your local changes, make sure that you have a `.gitignore`. Within your `.gitignore`, ensure that you are excluding your environment variables (`.env`) and your node modules (`node_modules`).

If you have any local changes, you can commit them by running the following commands:

```bash
git add .
git commit -m "init"
```

### Create Git Repo

You can create a GitHub repository from within your terminal, or on [github.com](https://github.com/). For this tutorial, you will use the GitHub CLI ([more info here](https://cli.github.com/)).

To create your GitHub repository:

1. Navigate to [github.com](http://github.com/)
2. In the top right corner, click the "plus" icon and select "New repository"
3. Pick a name for your repository (this can be anything)
4. Click "Create repository"

Once you have created your repository, GitHub will redirect you to your new repository.

1. Scroll down the page and copy the commands under the title "...or push an existing repository from the command line"
2. Go back to the terminal, paste and then run the commands

Note: if you run into the error "error: remote origin already exists.", this is because your local repository is still linked to the repository you cloned. To "unlink", you can run the following command:

```bash
rm -rf .git
git init
git add .
git commit -m "init"
```

Rerun the code snippet from the previous step.

### Import Project in Vercel

On the [New Project](https://vercel.com/new) page, under the **Import Git Repository** section, select the Git provider that you would like to import your project from. Follow the prompts to sign in to your GitHub account.

Once you have signed in, you should see your newly created repository from the previous step in the "Import Git Repository" section. Click the "Import" button next to that project.

### Add Environment Variables

Your application stores uses environment secrets to store your OpenAI API key using a `.env.local` file locally in development. To add this API key to your production deployment, expand the "Environment Variables" section and paste in your `.env.local` file. Vercel will automatically parse your variables and enter them in the appropriate `key:value` format.

### Deploy

Press the **Deploy** button. Vercel will create the Project and deploy it based on the chosen configurations.

### Enjoy the confetti!

To view your deployment, select the Project in the dashboard and then select the **Domain**. This page is now visible to anyone who has the URL.

### Considerations

When deploying an AI application, there are infrastructure-related considerations to be aware of.

### Function Duration

In most cases, you will call the large language model (LLM) on the server. By default, Vercel serverless functions have a maximum duration of 10 seconds on the Hobby Tier. Depending on your prompt, it can take an LLM more than this limit to complete a response. If the response is not resolved within this limit, the server will throw an error.

You can specify the maximum duration of your Vercel function using [route segment config](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config). To update your maximum duration, add the following route segment config to the top of your route handler or the page which is calling your server action.

```ts
export const maxDuration = 30;
```

You can increase the max duration to 60 seconds on the Hobby Tier. For other tiers, [see the documentation](https://vercel.com/docs/functions/runtimes#max-duration) for limits.

### Security Considerations

Given the high cost of calling an LLM, it's important to have measures in place that can protect your application from abuse.

#### Rate Limit

Rate limiting is a method used to regulate network traffic by defining a maximum number of requests that a client can send to a server within a given time frame.

Follow [this guide](https://vercel.com/guides/securing-ai-app-rate-limiting) to add rate limiting to your application.

#### Firewall

A firewall helps protect your applications and websites from DDoS attacks and unauthorized access.

[Vercel Firewall](https://vercel.com/docs/security/vercel-firewall) is a set of tools and infrastructure, created specifically with security in mind. It automatically mitigates DDoS attacks and Enterprise teams can get further customization for their site, including dedicated support and custom rules for IP blocking.

# AI SDK Core

Large Language Models (LLMs) are advanced programs that can understand, create, and engage with human language on a large scale.
They are trained on vast amounts of written material to recognize patterns in language and predict what might come next in a given piece of text.

The Vercel AI SDK Core **simplifies working with LLMs by offering a standardized way of integrating them into your app** - so you can focus on building great AI applications for your users, not waste time on technical details.

For example, here’s how you can generate text with various models using the Vercel AI SDK:

## AI SDK Core Functions

AI SDK Core has various functions designed for [text generation](./generating-text), [structured data generation](./generating-structured-data), and [tool usage](./tools-and-tool-calling).
These functions take a standardized approach to setting up [prompts](./prompts) and [settings](./settings), making it easier to work with different models.

- [`generateText`](/docs/reference/ai-sdk-core/generate-text): Generates text and [tool calls](./tools-and-tool-calling).
  This function is ideal for non-interactive use cases such as automation tasks where you need to write text (e.g. drafting email or summarizing web pages) and for agents that use tools.
- [`streamText`](/docs/reference/ai-sdk-core/stream-text): Stream text and tool calls.
  You can use the `streamText` function for interactive use cases such as chat bots and content streaming. You can also generate UI components with tools (see [Generative UI](../ai-sdk-rsc)).

- [`generateObject`](/docs/reference/ai-sdk-core/generate-object): Generates a typed, structured object that matches a [Zod](https://zod.dev/) schema.
  You can use this function to force the language model to return structured data, e.g. for information extraction, synthetic data generation, or classification tasks.

- [`streamObject`](/docs/reference/ai-sdk-core/stream-object): Stream a structured object that matches a Zod schema.
  You can use this function to stream generated UIs in combination with React Server Components (see [Generative UI](../ai-sdk-rsc)).

---
title: Generating Text
description: Learn how to generate text with the Vercel AI SDK.
---

## Generating and Streaming Text

Large language models (LLMs) can generate text in response to a prompt, which can contain instructions and information to process.
For example, you can ask a model to come up with a recipe, draft an email, or summarize a document.

The Vercel AI SDK Core provides two functions to generate text and stream it from LLMs:

- [`generateText`](#generatetext): Generates text for a given prompt and model.
- [`streamText`](#streamtext): Streams text from a given prompt and model.

Advanced LLM features such as [tool calling](./tools-and-tool-calling) and [structured data generation](./generating-structured-data) are built on top of text generation.

### `generateText`

You can generate text using the [`generateText`](/docs/reference/ai-sdk-core/generate-text) function. This function is ideal for non-interactive use cases where you need to write text (e.g. drafting email or summarizing web pages) and for agents that use tools.

```tsx
import { generateText } from 'ai';

const { text } = await generateText({
  model: yourModel,
  prompt: 'Write a vegetarian lasagna recipe for 4 people.',
});
```

You can use more [advanced prompts](./prompts) to generate text with more complex instructions and content:

```tsx
import { generateText } from 'ai';

const { text } = await generateText({
  model: yourModel,
  system:
    'You are a professional writer. You write simple, clear, and concise content.',
  prompt: `summarize the following article in 3-5 sentences:\n${article}`,
});
```

### `streamText`

Depending on your model and prompt, it can take a large language model (LLM) up to a minute to finish generating it's response. This delay can be unacceptable for interactive use cases such as chatbots or real-time applications, where users expect immediate responses.

Vercel AI SDK Core provides the [`streamText`](/docs/reference/ai-sdk-core/stream-text) function which simplifies streaming text from LLMs.
Its result has methods that can be used in different environments, e.g.
`result.toDataStreamResponse()` to create a response object that can be used
in a Next.js App Router API route.

```ts
import { streamText } from 'ai';

const result = await streamText({
  model: yourModel,
  prompt: 'Invent a new holiday and describe its traditions.',
});

// example: use textStream as an async iterable
for await (const textPart of result.textStream) {
  console.log(textPart);
}
```

<Note>
  You can use `streamText` on it's own or in combination with [AI SDK
  UI](/examples/next-pages/basics/streaming-text-generation) and [AI SDK
  RSC](/examples/next-app/basics/streaming-text-generation)
</Note>

`result.textStream` is also a `ReadableStream`, so you can use it in a browser or Node.js environment.

```ts
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

#### `onChunk` callback

When using `streamText`, you can provide an `onChunk` callback that is triggered for each chunk of the stream.

It receives the following chunk types:

- `text-delta`
- `tool-call`
- `tool-result`
- `tool-call-streaming-start` (when `experimental_streamToolCalls` is enabled)
- `tool-call-delta` (when `experimental_streamToolCalls` is enabled)

```tsx highlight="6-11"
import { streamText } from 'ai';

const result = await streamText({
  model: yourModel,
  prompt: 'Invent a new holiday and describe its traditions.',
  onChunk({ chunk }) {
    // implement your own logic here, e.g.:
    if (chunk.type === 'text-delta') {
      console.log(chunk.text);
    }
  },
});
```

#### `onFinish` callback

When using `streamText`, you can provide an `onFinish` callback that is triggered when the model finishes generating the response and all tool executions.

```tsx highlight="6-8"
import { streamText } from 'ai';

const result = await streamText({
  model: yourModel,
  prompt: 'Invent a new holiday and describe its traditions.',
  onFinish({ text, toolCalls, toolResults, finishReason, usage }) {
    // your own logic, e.g. for saving the chat history or recording usage
  },
});
```

#### Result helper functions

The result object of `streamText` contains several helper functions to make the integration into [AI SDK UI](/docs/ai-sdk-ui) easier:

- `result.toDataStreamResponse()`: Creates a data stream response (with tool calls etc.).
- `result.toTextStreamResponse()`: Creates a simple text stream response.
- `result.pipeDataStreamToResponse()`: Writes AI stream delta output to a Node.js response-like object.
- `result.pipeTextStreamToResponse()`: Writes text delta output to a Node.js response-like object.

#### Result promises

The result object of `streamText` contains several promises that resolve when all required data is available:

- `result.text`: The generated text.
- `result.toolCalls`: The tool calls made during text generation.
- `result.toolResults`: The tool results from the tool calls.
- `result.finishReason`: The reason the model finished generating text.
- `result.usage`: The usage of the model during text generation.

---
title: Generating Structured Data
description: Learn how to generate structured data with the Vercel AI SDK.
---

## Generating Structured Data

While text generation can be useful, your usecase will likely call for generating structured data.
For example, you might want to extract information from text, classify data, or generate synthetic data.

Many language models are capable of generating structured data, often defined as using "JSON modes" or "tools".
However, you need to manually provide schemas and then validate the generated data as LLMs can produce incorrect or incomplete structured data.

The Vercel AI SDK standardises structured object generation across model providers with the [`generateObject`](/docs/reference/ai-sdk-core/generate-object) function.

The `generateObject` function uses [Zod schemas](./schemas-and-zod) or [JSON schemas](/docs/reference/ai-sdk-core/json-schema) to specify the shape of the data that you want, and the AI model will generate data that conforms to that structure.
The schema is also used to validate the generated data, ensuring type safety and correctness.

```ts
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

### Specifying Generation Mode

While some models (like OpenAI) natively support object generation, others require alternative methods, like modified [tool calling](/docs/ai-sdk-core/tools-and-tool-calling). The `generateObject` function allows you to specify the method it will use to return structured data.

- `auto`: The provider will choose the best mode for the model. This recommended mode is used by default.
- `tool`: A tool with the JSON schema as parameters is provided and the provider is instructed to use it.
- `json`: The response format is set to JSON when supported by the provider, e.g. via json modes or grammar-guided generation. If grammar-guided generation is not supported, the JSON schema and instructions to generate JSON that conforms to the schema are injected into the system prompt.

<Note>
  Please note that not every provider supports all generation modes. Some
  providers do not support object generation at all.
</Note>

### Specifying Schema Name and Description

You can optionally specify a name and description for the schema. These are used by some providers for additional LLM guidance, e.g. via tool or schema name.

```ts highlight="6-7"
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

### Streaming Objects

Given the added complexity of returning structured data, model response time can be unacceptable for your interactive use case. With the [`streamObject`](/docs/reference/ai-sdk-core/stream-object) function, you can stream the model's response as it is generated.

```ts
import { streamObject } from 'ai';

const { partialObjectStream } = await streamObject({
  // ...
});

// use partialObjectStream as an async iterable
for await (const partialObject of partialObjectStream) {
  console.log(partialObject);
}
```

You can use `streamObject` to stream generated UIs in combination with React Server Components (see [Generative UI](../ai-sdk-rsc))) or the [`useObject`](/docs/reference/ai-sdk-ui/use-object) hook.

### Schema Writing Tips

The mapping from Zod schemas to LLM inputs (typically JSON schema) is not always straightforward, since the mapping is not one-to-one.
Please checkout the following tips and the [Prompt Engineering with Tools](/docs/ai-sdk-core/tools-and-tool-calling#prompt-engineering-with-tools) guide.

#### Generating Arrays

Most models require an object as the top-level schema. If you want to generate an array, you can wrap it in an object with a single descriptive key and use destructuring to access the array.

```ts highlight="2,5-6"
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

#### Dates

Zod expects JavaScript Date objects, but models return dates as strings.
You can specify and validate the date format using `z.string().datetime()` or `z.string().date()`,
and then use a Zod transformer to convert the string to a Date object.

```ts highlight="7-10"
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

When you use `generateObject`, errors are thrown when the model fails to generate proper JSON (`JSONParseError`)
or when the generated JSON does not match the schema (`TypeValidationError`).
Both error types contain additional information, e.g. the generated text or the invalid value.

You can use this to e.g. design a function that safely process the result object and also returns values in error cases:

```ts
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
```

---
title: Tool Calling
description: Learn about tool calling with Vercel AI SDK Core.
---

## Tool Calling

As covered under Foundations, [tools](/docs/foundations/tools) are objects that can be called by the model to perform a specific task.

When used with AI SDK Core, tools contain three elements:

- **`description`**: An optional description of the tool that can influence when the tool is picked.
- **`parameters`**: A [Zod schema](/docs/foundations/tools#schema-specification-and-validation-with-zod) or a [JSON schema](/docs/reference/ai-sdk-core/json-schema) that defines the parameters. The schema is consumed by the LLM, and also used to validate the LLM tool calls.
- **`execute`**: An optional async function that is called with the arguments from the tool call. It produces a value of type `RESULT` (generic type). It is optional because you might want to forward tool calls to the client or to a queue instead of executing them in the same process.

The `tools` parameter of `generateText` and `streamText` is an object that has the tool names as keys and the tools as values:

```ts highlight="6-17"
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

<Note className="mb-2">
  You can use the [`tool`](/docs/reference/ai-sdk-core/tool) helper function to
  infer the types of the `execute` parameters.
</Note>

<Note>
  When a model uses a tool, it is called a "tool call" and the output of the
  tool is called a "tool result".
</Note>

Tool calling is not restricted to only text generation.
You can also use it to render user interfaces with Generative AI.

### Tool Choice

You can use the `toolChoice` setting to influence when a tool is selected.
It supports the following settings:

- `auto` (default): the model can choose whether and which tools to call.
- `required`: the model must call a tool. It can choose which tool to call.
- `none`: the model must not call tools
- `{ type: 'tool', toolName: string (typed) }`: the model must call the specified tool

```ts highlight="18"
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

### Tool Roundtrips

<Note>Tool roundtrips are currently only supported by `generateText`.</Note>

The large language model needs to know the tool results before it can continue generating text.
This requires sending the tool results back to the model.
You can enable this feature by setting the `maxToolRoundtrips` setting to a number greater than 0.

```ts highlight="18"
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

### Pre-Built Tools

When you work with tools, you typically need a mix of application specific tools and general purpose tools.
There are several providers that offer pre-built tools that you can use out of the box:

- **[agentic](https://github.com/transitive-bullshit/agentic)** - A collection of 20+ tools. Most tools connect to access external APIs such as [Exa](https://exa.ai/) or [E2B](https://e2b.dev/).
- **[browserbase](https://github.com/browserbase/js-sdk?tab=readme-ov-file#vercel-ai-sdk-integration)** - Browser tool that runs a headless browser

<Note>
  Do you have open source tools or tool libraries that are compatible with the
  Vercel AI SDK? Please [file a pull
  request](https://github.com/vercel/ai/pulls) to add them to this list.
</Note>

### Prompt Engineering with Tools

When you create prompts that include tools, getting good results can be tricky as the number and complexity of your tools increases.

Here are a few tips to help you get the best results:

1. Use a model that is strong at tool calling, such as `gpt-4` or `gpt-4-turbo`. Weaker models will often struggle to call tools effectively and flawlessly.
1. Keep the number of tools low, e.g. to 5 or less.
1. Keep the complexity of the tool parameters low. Complex Zod schemas with many nested and optional elements, unions, etc. can be challenging for the model to work with.
1. Use semantically meaningful names for your tools, parameters, parameter properties, etc. The more information you pass to the model, the better it can understand what you want.
1. Add `.describe("...")` to your Zod schema properties to give the model hints about what a particular property is for.
1. When the output of a tool might be unclear to the model and there are dependencies between tools, use the `description` field of a tool to provide information about the output of the tool execution.
1. You can include example input/outputs of tool calls in your prompt to help the model understand how to use the tools. Keep in mind that the tools work with JSON objects, so the examples should use JSON.

In general, the goal should be to give the model all information it needs in a clear way.

---
title: Agents
description: Learn about creating agents with Vercel AI SDK Core.
---

## Agents

AI agents let the language model execute several steps in a non-deterministic way.
The model can make decisions based on the context of the conversation and the user's input.

One approach to implementing agents is to allow the LLM to choose the next step in a loop.
With `generateText`, you can combine [tools](/docs/ai-sdk-core/tools-and-tool-calling) with `maxToolRoundtrips`.
This makes it possible to implement basic agents that reason at each step and make decisions based on the context.

### Example

This example demonstrates how to create an agent that solves math problems.
It has a calculator tool (using [math.js](https://mathjs.org/)) that it can call to evaluate mathematical expressions.

```ts file='main.ts'
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

Calling `generateText` with `maxToolRoundtrips` can result in several calls to the LLM (roundtrips).
You can access information from all roundtrips by using the `roundtrips` property of the response.

```ts
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

All Vercel AI SDK functions support the following common settings in addition to the model, the [prompt](./prompts), and additional provider-specific settings:

```ts highlight="3-5"
const result = await generateText({
  model: yourModel,
  maxTokens: 512,
  temperature: 0.3,
  maxRetries: 5,
  prompt: 'Invent a new holiday and describe its traditions.',
});
```

<Note>
  Some providers do not support all common settings. If you use a setting with a
  provider that does not support it, a warning will be generated. You can check
  the `warnings` property in the result object to see if any warnings were
  generated.
</Note>

### `maxTokens`

Maximum number of tokens to generate.

### `temperature`

Temperature setting.

The value is passed through to the provider. The range depends on the provider and model.
For most providers, `0` means almost deterministic results, and higher values mean more randomness.

It is recommended to set either `temperature` or `topP`, but not both.

### `topP`

Nucleus sampling.

The value is passed through to the provider. The range depends on the provider and model.
For most providers, nucleus sampling is a number between 0 and 1.
E.g. 0.1 would mean that only tokens with the top 10% probability mass are considered.

It is recommended to set either `temperature` or `topP`, but not both.

### `topK`

Only sample from the top K options for each subsequent token.

Used to remove "long tail" low probability responses.
Recommended for advanced use cases only. You usually only need to use `temperature`.

### `presencePenalty`

The presence penalty affects the likelihood of the model to repeat information that is already in the prompt.

The value is passed through to the provider. The range depends on the provider and model.
For most providers, `0` means no penalty.

### `frequencyPenalty`

The frequency penalty affects the likelihood of the model to repeatedly use the same words or phrases.

The value is passed through to the provider. The range depends on the provider and model.
For most providers, `0` means no penalty.

### `stopSequences`

The stop sequences to use for stopping the text generation.

If set, the model will stop generating text when one of the stop sequences is generated.
Providers may have limits on the number of stop sequences.

### `seed`

It is the seed (integer) to use for random sampling.
If set and supported by the model, calls will generate deterministic results.

### `maxRetries`

Maximum number of retries. Set to 0 to disable retries. Default: `2`.

### `abortSignal`

An optional abort signal that can be used to cancel the call.

### `headers`

Additional HTTP headers to be sent with the request. Only applicable for HTTP-based providers.

You can use the request headers to provide additional information to the provider,
depending on what the provider supports. For example, some observability providers support
headers such as `Prompt-Id`.

```ts
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

<Note>
  The `headers` setting is for request-specific headers. You can also set
  `headers` in the provider configuration. These headers will be sent with every
  request made by the provider.
</Note>

---
title: Embeddings
description: Learn how to embed values with the Vercel AI SDK.
---

## Embeddings

Embeddings are a way to represent words, phrases, or images as vectors in a high-dimensional space.
In this space, similar words are close to each other, and the distance between words can be used to measure their similarity.

### Embedding a Single Value

The Vercel AI SDK provides the [`embed`](/docs/reference/ai-sdk-core/embed) function to embed single values, which is useful for tasks such as finding similar words
or phrases or clustering text.
You can use it with embeddings models, e.g. `openai.embedding('text-embedding-3-large')` or `mistral.embedding('mistral-embed')`.

```tsx
import { embed } from 'ai';
import { openai } from '@ai-sdk/openai';

// 'embedding' is a single embedding object (number[])
const { embedding } = await embed({
  model: openai.embedding('text-embedding-3-small'),
  value: 'sunny day at the beach',
});
```

### Embedding Many Values

When loading data, e.g. when preparing a data store for retrieval-augmented generation (RAG),
it is often useful to embed many values at once (batch embedding).

The Vercel AI SDK provides the [`embedMany`](/docs/reference/ai-sdk-core/embed-many) function for this purpose.
Similar to `embed`, you can use it with embeddings models,
e.g. `openai.embedding('text-embedding-3-large')` or `mistral.embedding('mistral-embed')`.

```tsx
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

### Embedding Similarity

After embedding values, you can calculate the similarity between them using the [`cosineSimilarity`](/docs/reference/ai-sdk-core/cosine-similarity) function.
This is useful to e.g. find similar words or phrases in a dataset.
You can also rank and filter related items based on their similarity.

```ts highlight={"2,10"}
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

### Token Usage

Many providers charge based on the number of tokens used to generate embeddings.
Both `embed` and `embedMany` provide token usage information in the `usage` property of the result object:

```ts highlight={"4,9"}
import { openai } from '@ai-sdk/openai';
import { embed } from 'ai';

const { embedding, usage } = await embed({
  model: openai.embedding('text-embedding-3-small'),
  value: 'sunny day at the beach',
});

console.log(usage); // { tokens: 10 }
```

---
title: Provider Management
description: Learn how to work with multiple providers
---

## Provider Management

<Note>Provider management is an experimental feature.</Note>

When you work with multiple providers and models, it is often desirable to manage them in a central place
and access the models through simple string ids.

The Vercel AI SDK provides a [`ProviderRegistry`](/docs/reference/ai-sdk-core/provider-registry) for this purpose.
You can register multiple providers. The provider id will become the prefix of the model id:
`providerId:modelId`.

### Provider Registry

#### Setup

You can create a registry with multiple providers and models using `experimental_createProviderRegistry`.

<Note>
  It is common to keep the registry setup in a separate file and import it where
  needed.
</Note>

```ts filename={"registry.ts"}
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

#### Language models

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

#### Text embedding models

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

### Model Maps

An alternative approach to the provider registry is to just create an object that maps ids
to models. This has the following advantages:

- You can restrict the models that you want to use (e.g. when you let your users choose models)
- You can chose custom ids for the models (e.g. semantic ids)
- You can predefine custom settings for the models

However, it is more verbose and you need to specify each model individually.

#### Examples

```ts
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

---
title: Error Handling
description: Learn how to handle errors in the AI SDK Core
---

## Error Handling

### Handling regular errors

Regular errors are thrown and can be handled using the `try/catch` block.

```ts highlight="3,8-10"
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

### Handling streaming errors (simple streams)

When errors occur during streams that do not support error chunks,
the error is thrown as a regular error.
You can handle these errors using the `try/catch` block.

```ts highlight="3,12-14"
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

### Handling streaming errors (streaming with `error` support)

Full streams support error parts.
You can handle those parts similar to other parts.
It is recommended to also add a try-catch block for errors that
happen outside of the streaming.

```ts highlight="13-17"
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
```

# AI SDK RSC

<Note>
  The `ai/rsc` package is compatible with frameworks that support React Server
  Components.
</Note>

[React Server Components](https://nextjs.org/docs/app/building-your-application/rendering/server-components) (RSC) allow you to write UI that can be rendered on the server and streamed to the client. RSCs enable [ Server Actions ](https://nextjs.org/docs/app/building-your-application/data-fetching/server-actions-and-mutations#with-client-components), a new way to call server-side code directly from the client just like any other function with end-to-end type-safety. This combination opens the door to a new way of building AI applications, allowing the large language model (LLM) to generate and stream UI directly from the server to the client.

## AI SDK RSC Functions

AI SDK RSC has various functions designed to help you build AI-native applications with React Server Components. These functions:

1. Provide abstractions for building Generative UI applications.
   - [`streamUI`](/docs/reference/ai-sdk-rsc/stream-ui): calls a model and allows it to respond with React Server Components.
   - [`useUIState`](/docs/reference/ai-sdk-rsc/use-ui-state): returns the current UI state and a function to update the UI State (like React's `useState`). UI State is the visual representation of the AI state.
   - [`useAIState`](/docs/reference/ai-sdk-rsc/use-ai-state): returns the current AI state and a function to update the AI State (like React's `useState`). The AI state is intended to contain context and information shared with the AI model, such as system messages, function responses, and other relevant data.
   - [`useActions`](/docs/reference/ai-sdk-rsc/use-actions): provides access to your Server Actions from the client. This is particularly useful for building interfaces that require user interactions with the server.
   - [`createAI`](/docs/reference/ai-sdk-rsc/create-ai): creates a client-server context provider that can be used to wrap parts of your application tree to easily manage both UI and AI states of your application.
2. Make it simple to work with streamable values between the server and client.
   - [`createStreamableValue`](/docs/reference/ai-sdk-rsc/create-streamable-value): creates a stream that sends values from the server to the client. The value can be any serializable data.
   - [`readStreamableValue`](/docs/reference/ai-sdk-rsc/read-streamable-value): reads a streamable value from the client that was originally created using `createStreamableValue`.
   - [`createStreamableUI`](/docs/reference/ai-sdk-rsc/create-streamable-ui): creates a stream that sends UI from the server to the client.
   - [`useStreamableValue`](/docs/reference/ai-sdk-rsc/use-streamable-value): accepts a streamable value created using `createStreamableValue` and returns the current value, error, and pending state.

---
title: Streaming React Components
description: Overview of streaming RSCs
---

import { UIPreviewCard, Card } from '@/components/home/card';
import { EventPlanning } from '@/components/home/event-planning';
import { Searching } from '@/components/home/searching';
import { Weather } from '@/components/home/weather';

## Streaming React Components

The RSC API allows you to stream React components from the server to the client with the [`streamUI`](/docs/reference/ai-sdk-rsc/stream-ui) function. This is useful when you want to go beyond raw text and stream components to the client in real-time.

Similar to [ AI SDK Core ](/docs/ai-sdk-core/overview) APIs (like [ `streamText` ](/docs/reference/ai-sdk-core/stream-text) and [ `streamObject` ](/docs/reference/ai-sdk-core/stream-object), `streamUI` provides a single function to call a model and allow it to respond with React Server Components.
It supports the same model interfaces as AI SDK Core APIs.

### Concepts

To give the model the ability to respond to a user's prompt with a React component, you can leverage [tools](/docs/ai-sdk-core/tools-and-tool-calling).

<Note>
  Remember, tools are like programs you can give to the model, and the model can
  decide as and when to use based on the context of the conversation.
</Note>

With the `streamUI` function, **you provide tools that return React components**. With the ability to stream components, the model is akin to a dynamic router that is able to understand the user's intention and display relevant UI.

At a high level, the `streamUI` works like other AI SDK Core functions: you can provide the model with a prompt or some conversation history and, optionally, some tools. If the model decides, based on the context of the conversation, to call a tool, it will generate a tool call. The `streamUI` function will then run the respective tool, returning a React component. If the model doesn't have a relevant tool to use, it will return a text generation, which will be passed to the `text` function, for you to handle (render and return as a React component).

<Note>Remember, the `streamUI` function must return a React component. </Note>

```tsx
const result = await streamUI({
  model: openai('gpt-4o'),
  prompt: 'Get the weather for San Francisco',
  text: ({ content }) => <div>{content}</div>,
  tools: {},
});
```

This example calls the `streamUI` function using OpenAI's `gpt-4o` model, passes a prompt, specifies how the model's plain text response (`content`) should be rendered, and then provides an empty object for tools. Even though this example does not define any tools, it will stream the model's response as a `div` rather than plain text.

#### Adding A Tool

Using tools with `streamUI` is similar to how you use tools with `generateText` and `streamText`.
A tool is an object that has:

- `description`: a string telling the model what the tool does and when to use it
- `parameters`: a Zod schema describing what the tool needs in order to run
- `generate`: an asynchronous function that will be run if the model calls the tool. This must return a React component

Let's expand the previous example to add a tool.

```tsx highlight="6-14"
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

This tool would be run if the user asks for the weather for their location. If the user hasn't specified a location, the model will ask for it before calling the tool. When the model calls the tool, the generate function will initially return a loading component. This component will show until the awaited call to `getWeather` is resolved, at which point, the model will stream the `<WeatherComponent />` to the user.

<Note>
  Note: This example uses a [ generator function
  ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)
  (`function*`), which allows you to pause its execution and return a value,
  then resume from where it left off on the next call. This is useful for
  handling data streams, as you can fetch and return data from an asynchronous
  source like an API, then resume the function to fetch the next chunk when
  needed. By yielding values one at a time, generator functions enable efficient
  processing of streaming data without blocking the main thread.
</Note>

### Using `streamUI` with Next.js

Let's see how you can use the example above in a Next.js application.

To use `streamUI` in a Next.js application, you will need two things:

1. A Server Action (where you will call `streamUI`)
2. A page to call the Server Action and render the resulting components

#### Step 1: Create a Server Action

<Note>
  Server Actions are server-side functions that you can call directly from the
  frontend. For more info, see [the
  documentation](https://nextjs.org/docs/app/building-your-application/data-fetching/server-actions-and-mutations#with-client-components).
</Note>

Create a Server Action at `app/actions.tsx` and add the following code:

```tsx filename="app/actions.tsx"
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

1. First define a `LoadingComponent`, which renders a pulsing `div` that will show some loading text.
2. Next, define a `getWeather` function that will timeout for 2 seconds (to simulate fetching the weather externally) before returning the "weather" for a `location`. Note: you could run any asynchronous TypeScript code here.
3. Finally, define a `WeatherComponent` which takes in `location` and `weather` as props, which are then rendered within a `div`.

Your Server Action is an asynchronous function called `streamComponent` that takes no inputs, and returns a `ReactNode`. Within the action, you call the `streamUI` function, specifying the model (`gpt-4o`), the prompt, the component that should be rendered if the model chooses to return text, and finally, your `getWeather` tool. Last but not least, you return the resulting component generated by the model with `result.value`.

To call this Server Action and display the resulting React Component, you will need a page.

#### Step 2: Create a Page

Create or update your root page (`app/page.tsx`) with the following code:

```tsx filename="app/page.tsx"
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

This page is first marked as a client component with the `"use client";` directive given it will be using hooks and interactivity. On the page, you render a form. When that form is submitted, you call the `streamComponent` action created in the previous step (just like any other function). The `streamComponent` action returns a `ReactNode` that you can then render on the page using React state (`setComponent`).

---
title: Managing Generative UI State
description: Overview of the AI and UI states
---

## Managing Generative UI State

State is an essential part of any application. State is particularly important in AI applications as it is passed to large language models (LLMs) on each request to ensure they have the necessary context to produce a great generation. Traditional chatbots are text-based and have a structure that mirrors that of any chat application.

For example, in a chatbot, state is an array of `messages` where each `message` has:

- `id`: a unique identifier
- `role`: who sent the message (user/assistant/system/tool)
- `content`: the content of the message

This state can be rendered in the UI and sent to the model without any modifications.

With Generative UI, the model can now return a React component, rather than a plain text message. The client can render that component without issue, but that state can't be sent back to the model because React components aren't serialisable. So, what can you do?

**The solution is to split the state in two, where one (AI State) becomes a proxy for the other (UI State)**.

One way to understand this concept is through a Lego analogy. Imagine a 10,000 piece Lego model that, once built, cannot be easily transported because it is fragile. By taking the model apart, it can be easily transported, and then rebuilt following the steps outlined in the instructions pamphlet. In this way, the instructions pamphlet is a proxy to the physical structure. Similarly, AI State provides a serialisable (JSON) representation of your UI that can be passed back and forth to the model.

### What is AI and UI State?

The RSC API simplifies how you manage AI State and UI State, providing a robust way to keep them in sync between your database, server and client.

### AI State

AI State refers to the state of your application in a serialisable format that will be used on the server and can be shared with the language model.

For a chat app, the AI State is the conversation history (messages) between the user and the assistant. Components generated by the model would be represented in a JSON format as a tool alongside any necessary props. AI State can also be used to store other values and meta information such as `createdAt` for each message and `chatId` for each conversation. The LLM reads this history so it can generate the next message. This state serves as the source of truth for the current application state.

<Note>
  **Note**: AI state can be accessed/modified from both the server and the
  client.
</Note>

### UI State

UI State refers to the state of your application that is rendered on the client. It is a fully client-side state (similar to `useState`) that can store anything from Javascript values to React elements. UI state is a list of actual UI elements that are rendered on the client.

<Note>**Note**: UI State can only be accessed client-side.</Note>

### Using AI / UI State

### Creating the AI Context

AI SDK RSC simplifies managing AI and UI state across your application by providing several hooks. These hooks are powered by [ React context ](https://react.dev/reference/react/hooks#context-hooks) under the hood.

Notably, this means you do not have to pass the message history to the server explicitly for each request. You also can access and update your application state in any child component of the context provider. As you begin building [multistep generative interfaces](/docs/ai-sdk-rsc/multistep-interfaces), this will be particularly helpful.

To use `ai/rsc` to manage AI and UI State in your application, you can create a React context using [`createAI`](/docs/reference/ai-sdk-rsc/create-ai):

```tsx filename='app/actions.tsx'
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

<Note>You must pass Server Actions to the `actions` object.</Note>

In this example, you define types for AI State and UI State, respectively.

Next, wrap your application with your newly created context. With that, you can get and set AI and UI State across your entire application.

```tsx filename='app/layout.tsx'
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

The UI state can be accessed in Client Components using the [`useUIState`](/docs/reference/ai-sdk-rsc/use-ui-state) hook provided by the RSC API. The hook returns the current UI state and a function to update the UI state like React's `useState`.

```tsx filename='app/page.tsx'
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

The AI state can be accessed in Client Components using the [`useAIState`](/docs/reference/ai-sdk-rsc/use-ai-state) hook provided by the RSC API. The hook returns the current AI state.

```tsx filename='app/page.tsx'
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

The AI State can be accessed within any Server Action provided to the `createAI` context using the [`getAIState`](/docs/reference/ai-sdk-rsc/get-ai-state) function. It returns the current AI state as a read-only value:

```tsx filename='app/actions.ts'
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

<Note>
  Remember, you can only access state within actions that have been passed to
  the `createAI` context within the `actions` key.
</Note>

### Updating AI State on Server

The AI State can also be updated from within your Server Action with the [`getMutableAIState`](/docs/reference/ai-sdk-rsc/get-mutable-ai-state) function. This function is similar to `getAIState`, but it returns the state with methods to read and update it:

```tsx filename='app/actions.ts'
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

<Note>
  It is important to update the AI State with new responses using `.update()`
  and `.done()` to keep the conversation history in sync.
</Note>

### Calling Server Actions from the Client

To call the `sendMessage` action from the client, you can use the [`useActions`](/docs/reference/ai-sdk-rsc/use-actions) hook. The hook returns all the available Actions that were provided to `createAI`:

```tsx filename='app/page.tsx'
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

<Note>
  Important! Don't forget to update the UI State after you call your Server
  Action otherwise the streamed component will not show in the UI.
</Note>

---
title: Saving and Restoring States
description: Saving and restoring AI and UI states with onGetUIState and onSetAIState
---

## Saving and Restoring States

AI SDK RSC provides convenient methods for saving and restoring AI and UI state. This is useful for saving the state of your application after every model generation, and restoring it when the user revisits the generations.

### AI State

#### Saving AI state

The AI state can be saved using the [`onSetAIState`](/docs/reference/ai-sdk-rsc/create-ai#on-set-ai-state) callback, which gets called whenever the AI state is updated. In the following example, you save the chat history to a database whenever the generation is marked as done.

```tsx
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

### Restoring AI state

The AI state can be restored using the [`initialAIState`](/docs/reference/ai-sdk-rsc/create-ai#initial-ai-state) prop passed to the context provider created by the [`createAI`](/docs/reference/ai-sdk-rsc/create-ai) function. In the following example, you restore the chat history from a database when the component is mounted.

```tsx file='app/layout.tsx'
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

### UI State

#### Saving UI state

The UI state cannot be saved directly, since the contents aren't yet serializable. Instead, you can use the AI state as proxy to store details about the UI state and use it to restore the UI state when needed.

### Restoring UI state

The UI state can be restored using the AI state as a proxy. In the following example, you restore the chat history from the AI state when the component is mounted. You use the [`onGetUIState`](/docs/reference/ai-sdk-rsc/create-ai#on-get-ui-state) callback to listen for SSR events and restore the UI state.

```tsx file='app/components/Chat.tsx'
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
---
title: Multistep Interfaces
description: Overview of Building Multistep Interfaces with AI SDK RSC
---

## Designing Multistep Interfaces

Multistep interfaces refer to user interfaces that require multiple independent steps to be executed in order to complete a specific task.

For example, if you wanted to build a Generative UI chatbot capable of booking flights, it could have three steps:

- Search all flights
- Pick flight
- Check availability

To build this kind of application you will leverage two concepts, **tool composition** and **application context**.

**Tool composition** is the process of combining multiple [tools](/docs/ai-sdk-core/tools-and-tool-calling) to create a new tool. This is a powerful concept that allows you to break down complex tasks into smaller, more manageable steps. In the example above, _"search all flights"_, _"pick flight"_, and _"check availability"_ come together to create a holistic _"book flight"_ tool.

**Application context** refers to the state of the application at any given point in time. This includes the user's input, the output of the language model, and any other relevant information. In the example above, the flight selected in _"pick flight"_ would be used as context necessary to complete the _"check availability"_ task.

### Overview

In order to build a multistep interface with `ai/rsc`, you will need a few things:

- A Server Action that calls and returns the result from the `streamUI` function
- Tool(s) (sub-tasks necessary to complete your overall task)
- React component(s) that should be rendered when the tool is called
- A page to render your chatbot

The general flow that you will follow is:

- User sends a message (calls your Server Action with `useActions`, passing the message as an input)
- Message is appended to the AI State and then passed to the model alongside a number of tools
- Model can decide to call a tool, which will render the `<SomeTool />` component
- Within that component, you can add interactivity by using `useActions` to call the model with your Server Action and `useUIState` to append the model's response (`<SomeOtherTool />`) to the UI State
- And so on...

### Implementation

The turn-by-turn implementation is the simplest form of multistep interfaces. In this implementation, the user and the model take turns during the conversation. For every user input, the model generates a response, and the conversation continues in this turn-by-turn fashion.

In the following example, you specify two tools (`searchFlights` and `lookupFlight`) that the model can use to search for flights and lookup details for a specific flight.

```tsx filename="app/actions.tsx"
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

```tsx filename='app/layout.tsx'
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

```tsx filename="app/page.tsx"
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

This page pulls in the current UI State using the `useUIState` hook, which is then mapped over and rendered in the UI. To access the Server Action, you use the `useActions` hook which will return all actions that were passed to the `actions` key of the `createAI` function in your `actions.tsx` file. Finally, you call the `submitUserMessage` function like any other TypeScript function. This function returns a React component (`message`) that is then rendered in the UI by updating the UI State with `setConversation`.

In this example, to call the next tool, the user must respond with plain text. **Given you are streaming a React component, you can add a button to trigger the next step in the conversation**.

To add user interaction, you will have to convert the component into a client component and use the `useAction` hook to trigger the next step in the conversation.

```tsx filename="components/flights.tsx"
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

```tsx filename="actions.tsx"
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

---
title: Streaming Values
description: Overview of streaming RSCs
---

import { UIPreviewCard, Card } from '@/components/home/card';
import { EventPlanning } from '@/components/home/event-planning';
import { Searching } from '@/components/home/searching';
import { Weather } from '@/components/home/weather';

## Streaming Values

The RSC API provides several utility functions to allow you to stream values from the server to the client. This is useful when you need more granular control over what you are streaming and how you are streaming it.

<Note>
  These utilities can also be paired with [AI SDK Core](/docs/ai-sdk-core)
  functions like [`streamText`](/docs/reference/ai-sdk-core/stream-text) and
  [`streamObject`](/docs/reference/ai-sdk-core/stream-object) to easily stream
  LLM generations from the server to the client.
</Note>

There are two functions provided by the RSC API that allow you to create streamable values:

- [`createStreamableValue`](/docs/reference/ai-sdk-rsc/create-streamable-value) - creates a streamable (serializable) value, with full control over how you create, update, and close the stream.
- [`createStreamableUI`](/docs/reference/ai-sdk-rsc/create-streamable-ui) - creates a streamable React component, with full control over how you create, update, and close the stream.

### `createStreamableValue`

The RSC API allows you to stream serializable Javascript values from the server to the client using [`createStreamableValue`](/docs/reference/ai-sdk-rsc/create-streamable-value), such as strings, numbers, objects, and arrays.

This is useful when you want to stream:

- Text generations from the language model in real-time.
- Buffer values of image and audio generations from multi-modal models.
- Progress updates from multi-step agent runs.

### Creating a Streamable Value

You can import `createStreamableValue` from `ai/rsc` and use it to create a streamable value.

```tsx file='app/actions.ts'
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

```tsx file='app/page.tsx'
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

Learn how to stream a text generation (with `streamText`) using the Next.js App Router and `createStreamableValue` in this [example](/examples/next-app/basics/streaming-text-generation).

### `createStreamableUI`

`createStreamableUI` creates a stream that holds a React component. Unlike AI SDK Core APIs, this function does not call a large language model. Instead, it provides a primitive that can be used to have granular control over streaming a React component.

### Using `createStreamableUI`

Let's look at how you can use the `createStreamableUI` function with a Server Action.

```tsx filename='app/actions.tsx'
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

First, you create a streamable UI with an empty state and then update it with a loading message. After 1 second, you mark the stream as done passing in the actual weather information as it's final value. The `.value` property contains the actual UI that can be sent to the client.

### Reading a Streamable UI

On the client side, you can call the `getWeather` Server Action and render the returned UI like any other React component.

```tsx filename='app/page.tsx'
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

Learn more about handling multiple streams in a single request in the [Multiple Streamables](/docs/advanced/multiple-streamables) guide.

Learn more about handling state for more complex use cases with [ AI/UI State ](/docs/ai-sdk-rsc/generative-ui-state).

---
title: Handling Loading State
description: Overview of handling loading state with AI SDK RSC
---

## Handling Loading State

Given that responses from language models can often take a while to complete, it's crucial to be able to show loading state to users. This provides visual feedback that the system is working on their request and helps maintain a positive user experience.

There are three approaches you can take to handle loading state with the AI SDK RSC:

- Managing loading state similar to how you would in a traditional Next.js application. This involves setting a loading state variable in the client and updating it when the response is received.
- Streaming loading state from the server to the client. This approach allows you to track loading state on a more granular level and provide more detailed feedback to the user.
- Streaming loading component from the server to the client. This approach allows you to stream a React Server Component to the client while awaiting the model's response.

### Handling Loading State on the Client

#### Client

Let's create a simple Next.js page that will call the `generateResponse` function when the form is submitted. The function will take in the user's prompt (`input`) and then generate a response (`response`). To handle the loading state, use the `loading` state variable. When the form is submitted, set `loading` to `true`, and when the response is received, set it back to `false`. While the response is being streamed, the input field will be disabled.

```tsx filename='app/page.tsx'
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

#### Server

Now let's implement the `generateResponse` function. Use the `streamText` function to generate a response to the input.

```typescript filename='app/actions.ts'
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

### Streaming Loading State from the Server

If you are looking to track loading state on a more granular level, you can create a new streamable value to store a custom variable and then read this on the frontend. Let's update the example to create a new streamable value for tracking loading state:

#### Server

```typescript filename='app/actions.ts' highlight='9,22,25'
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

#### Client

```tsx filename='app/page.tsx' highlight="22,30-34"
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

### Streaming Loading Components with `streamUI`

If you are using the [ `streamUI` ](/docs/reference/ai-sdk-rsc/stream-ui) function, you can stream the loading state to the client in the form of a React component. `streamUI` supports the usage of [ JavaScript generator functions ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*), which allow you to yield some value (in this case a React component) while some other blocking work completes.

### Server

```ts
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

<Note>
  Remember to update the file from `.ts` to `.tsx` because you are defining a
  React component in the `streamUI` function.
</Note>

### Client

```tsx
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

---
title: Error Handling
description: Learn how to handle errors with the AI SDK.
---

## Error Handling

Two categories of errors can occur when working with the RSC API: errors while streaming user interfaces and errors while streaming other values.

### Handling UI Errors

To handle errors while generating UI, the [`streamableUI`](/docs/reference/ai-sdk-rsc/create-streamable-ui) object exposes an `error()` method.

```tsx filename='app/actions.tsx'
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

With this method, you can catch any error with the stream, and return relevant UI. On the client, you can also use a [React Error Boundary](https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary) to wrap the streamed component and catch any additional errors.

```tsx filename='app/page.tsx'
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

### Handling Other Errors

To handle other errors while streaming, you can return an error object that the reciever can use to determine why the failure occurred.

```tsx filename='app/actions.tsx'
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
```
---
title: Handling Authentication
description: Learn how to authenticate with the AI SDK.
---

## Authentication

The RSC API makes extensive use of [`Server Actions`](https://nextjs.org/docs/app/building-your-application/data-fetching/server-actions-and-mutations) to power streaming values and UI from the server.

Server Actions are exposed as public, unprotected endpoints. As a result, you should treat Server Actions as you would public-facing API endpoints and ensure that the user is authorized to perform the action before returning any data.

```tsx filename="app/actions.tsx"
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

# AI SDK UI

Vercel AI SDK UI is designed to help you build interactive chat, completion, and assistant applications with ease. It is a framework-agnostic toolkit, streamlining the integration of advanced AI functionalities into your applications.

Vercel AI SDK UI provides robust abstractions that simplify the complex tasks of managing chat streams and UI updates on the frontend, enabling you to develop dynamic AI-driven interfaces more efficiently. With four main hooks — **`useChat`**, **`useCompletion`**, **`useObject`**, and **`useAssistant`** — you can incorporate real-time chat capabilities, text completions, streamed JSON, and interactive assistant features into your app.

- **[`useChat`](/docs/reference/ai-sdk-ui/use-chat)** offers real-time streaming of chat messages, abstracting state management for inputs, messages, loading, and errors, allowing for seamless integration into any UI design.
- **[`useCompletion`](/docs/reference/ai-sdk-ui/use-completion)** enables you to handle text completions in your applications, managing chat input state and automatically updating the UI as new completions are streamed from your AI provider.
- **[`useObject`](/docs/reference/ai-sdk-ui/use-object)** is a hook that allows you to consume streamed JSON objects, providing a simple way to handle and display structured data in your application.
- **[`useAssistant`](/docs/reference/ai-sdk-ui/use-assistant)** is designed to facilitate interaction with OpenAI-compatible assistant APIs, managing UI state and updating it automatically as responses are streamed.

These hooks are designed to reduce the complexity and time required to implement AI interactions, letting you focus on creating exceptional user experiences.

---
title: Chatbot
description: Learn how to use the useChat hook.
---

## Chatbot

The `useChat` hook makes it effortless to create a conversational user interface for your chatbot application. It enables the streaming of chat messages from your AI provider, manages the chat state, and updates the UI automatically as new messages arrive.

To summarize, the `useChat` hook provides the following features:

- **Message Streaming**: All the messages from the AI provider are streamed to the chat UI in real-time.
- **Managed States**: The hook manages the states for input, messages, loading, error and more for you.
- **Seamless Integration**: Easily integrate your chat AI into any design or layout with minimal effort.

In this guide, you will learn how to use the `useChat` hook to create a chatbot application with real-time message streaming. Let's start with the following example first.

### Example

```tsx filename='app/page.tsx'
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

```ts filename='app/api/chat/route.ts'
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

In the `Page` component, the `useChat` hook will request to your AI provider endpoint whenever the user submits a message.
The messages are then streamed back in real-time and displayed in the chat UI.

This enables a seamless chat experience where the user can see the AI response as soon as it is available,
without having to wait for the entire response to be received.

<Note>
  `useChat` has a `keepLastMessageOnError` option that defaults to `false`. This
  option can be enabled to keep the last message on error. We will make this the
  default behavior in the next major release. Please enable it and update your
  error handling/resubmit behavior.
</Note>

### Customized UI

`useChat` also provides ways to manage the chat message and input states via code, show loading and error states, and update messages without being triggered by user interactions.

#### Loading State

The `isLoading` state returned by the `useChat` hook can be used for several
purposes

- To show a loading spinner while the chatbot is processing the user's message.
- To show a "Stop" button to abort the current message.
- To disable the submit button.

```tsx filename='app/page.tsx' highlight="6,20-27,34"
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

Similarly, the `error` state reflects the error object thrown during the fetch request.
It can be used to display an error message, disable the submit button, or show a retry button:

<Note>
  We recommend showing a generic error message to the user, such as "Something
  went wrong." This is a good practice to avoid leaking information from the
  server.
</Note>

```tsx file="app/page.tsx" highlight="6,18-25,31"
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

Please also see the [error handling](/docs/ai-sdk-ui/error-handling) guide for more information.

#### Modify messages

Sometimes, you may want to directly modify some existing messages. For example, a delete button can be added to each message to allow users to remove them from the chat history.

The `setMessages` function can help you achieve these tasks:

```tsx
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

You can think of `messages` and `setMessages` as a pair of `state` and `setState` in React.

#### Controlled input

In the initial example, we have `handleSubmit` and `handleInputChange` callbacks that manage the input changes and form submissions. These are handy for common use cases, but you can also use uncontrolled APIs for more advanced scenarios such as form validation or customized components.

The following example demonstrates how to use more granular APIs like `setInput` and `append` with your custom input and submit button components:

```tsx
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

```tsx
const { stop, isLoading, ... } = useChat()

return <>
  <button onClick={stop} disabled={!isLoading}>Stop</button>
  ...
```

When the user clicks the "Stop" button, the fetch request will be aborted. This avoids consuming unnecessary resources and improves the UX of your chatbot application.

Similarly, you can also request the AI provider to reprocess the last message by calling the `reload` function returned by the `useChat` hook:

```tsx
const { reload, isLoading, ... } = useChat()

return <>
  <button onClick={reload} disabled={isLoading}>Regenerate</button>
  ...
</>
```

When the user clicks the "Regenerate" button, the AI provider will regenerate the last message and replace the current one correspondingly.

### Event Callbacks

`useChat` provides optional event callbacks that you can use to handle different stages of the chatbot lifecycle:

- `onFinish`: Called when the assistant message is completed
- `onError`: Called when an error occurs during the fetch request.
- `onResponse`: Called when the response from the API is received.

These callbacks can be used to trigger additional actions, such as logging, analytics, or custom UI updates.

```tsx
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

### Configure Request Options

By default, the `useChat` hook sends a HTTP POST request to the `/api/chat` endpoint with the message list as the request body. You can customize the request by passing additional options to the `useChat` hook:

```tsx
const { messages, input, handleInputChange, handleSubmit } = useChat({
  api: '/api/custom-chat',
  headers: {
    Authorization: 'your_token',
  },
  body: {
    user_id: '123',
  },
  credentials: 'same-origin',
});
```

In this example, the `useChat` hook sends a POST request to the `/api/custom-chat` endpoint with the specified headers, additional body fields, and credentials for that fetch request. On your server side, you can handle the request with these additional information.

### Setting custom body fields per request

You can configure custom `body` fields on a per-request basis using the `body` option of the `handleSubmit` function.
This is useful if you want to pass in additional information to your backend that is not part of the message list.

```tsx filename="app/page.tsx" highlight="18-20"
'use client';

import { useChat } from 'ai/react';

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit } = useChat();
  return (
    <div>
      {messages.map(m => (
        <div key={m.id}>
          {m.role}: {m.content}
        </div>
      ))}

      <form
        onSubmit={event => {
          handleSubmit(event, {
            body: {
              customKey: 'customValue',
            },
          });
        }}
      >
        <input value={input} onChange={handleInputChange} />
      </form>
    </div>
  );
}
```

You can retrieve these custom fields on your server side by destructuring the request body:

```ts filename="app/api/chat/route.ts" highlight="3"
export async function POST(req: Request) {
  // Extract addition information ("customKey") from the body of the request:
  const { messages, customKey } = await req.json();
  //...
}
```

### Empty Submissions

You can configure the `useChat` hook to allow empty submissions by setting the `allowEmptySubmit` option to `true`.

```tsx filename="app/page.tsx" highlight="18"
'use client';

import { useChat } from 'ai/react';

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit } = useChat();
  return (
    <div>
      {messages.map(m => (
        <div key={m.id}>
          {m.role}: {m.content}
        </div>
      ))}

      <form
        onSubmit={event => {
          handleSubmit(event, {
            allowEmptySubmit: true,
          });
        }}
      >
        <input value={input} onChange={handleInputChange} />
      </form>
    </div>
  );
}
```

### Attachments (Experimental)

The `useChat` hook supports sending attachments along with a message as well as rendering them on the client. This can be useful for building applications that involve sending images, files, or other media content to the AI provider.

> **Note:** Attachments is currently only available for React frameworks.

There are two ways to send attachments with a message, either by providing a `FileList` object or a list of URLs to the `handleSubmit` function:

#### FileList

By using `FileList`, you can send multiple files as attachments along with a message using the file input element. The `useChat` hook will automatically convert them into data URLs and send them to the AI provider.

> **Note:** Currently, only `image/*` and `text/*` content types get automatically converted into [multi-modal content parts](https://sdk.vercel.ai/docs/foundations/prompts#multi-modal-messages). You will need to handle other content types manually.

```tsx filename="app/page.tsx"
'use client';

import { useChat } from 'ai/react';
import { useRef, useState } from 'react';

export default function Page() {
  const { messages, input, handleSubmit, handleInputChange, isLoading } =
    useChat();

  const [files, setFiles] = useState<FileList | undefined>(undefined);
  const fileInputRef = useRef<HTMLInputElement>(null);

  return (
    <div>
      <div>
        {messages.map(message => (
          <div key={message.id}>
            <div>{`${message.role}: `}</div>

            <div>
              {message.content}

              <div>
                {message.experimental_attachments
                  ?.filter(attachment =>
                    attachment.contentType.startsWith('image/'),
                  )
                  .map((attachment, index) => (
                    <img
                      key={`${message.id}-${index}`}
                      src={attachment.url}
                      alt={attachment.name}
                    />
                  ))}
              </div>
            </div>
          </div>
        ))}
      </div>

      <form
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
          onChange={event => {
            if (event.target.files) {
              setFiles(event.target.files);
            }
          }}
          multiple
          ref={fileInputRef}
        />
        <input
          value={input}
          placeholder="Send message..."
          onChange={handleInputChange}
          disabled={isLoading}
        />
      </form>
    </div>
  );
}
```

#### URLs

You can also send URLs as attachments along with a message. This can be useful for sending links to external resources or media content.

> **Note:** The URL can also be a data URL, which is a base64-encoded string that represents the content of a file. Currently, only `image/*` content types get automatically converted into [multi-modal content parts](https://sdk.vercel.ai/docs/foundations/prompts#multi-modal-messages). You will need to handle other content types manually.

```tsx filename="app/page.tsx"
'use client';

import { useChat } from 'ai/react';
import { useState } from 'react';
import { Attachment } from '@ai-sdk/ui-utils';

export default function Page() {
  const { messages, input, handleSubmit, handleInputChange, isLoading } =
    useChat();

  const [attachments] = useState<Attachment[]>([
    {
      name: 'earth.png',
      contentType: 'image/png',
      url: 'https://example.com/earth.png',
    },
    {
      name: 'moon.png',
      contentType: 'image/png',
      url: 'data:image/png;base64,iVBORw0KGgo...',
    },
  ]);

  return (
    <div>
      <div>
        {messages.map(message => (
          <div key={message.id}>
            <div>{`${message.role}: `}</div>

            <div>
              {message.content}

              <div>
                {message.experimental_attachments
                  ?.filter(attachment =>
                    attachment.contentType?.startsWith('image/'),
                  )
                  .map((attachment, index) => (
                    <img
                      key={`${message.id}-${index}`}
                      src={attachment.url}
                      alt={attachment.name}
                    />
                  ))}
              </div>
            </div>
          </div>
        ))}
      </div>

      <form
        onSubmit={event => {
          handleSubmit(event, {
            experimental_attachments: attachments,
          });
        }}
      >
        <input
          value={input}
          placeholder="Send message..."
          onChange={handleInputChange}
          disabled={isLoading}
        />
      </form>
    </div>
  );
}
```
---
title: Chatbot with Tools
description: Learn how to use tools with the useChat hook.
---

## Chatbot with Tools

<Note type="warning">
  The tool calling functionality described here is currently only available for
  **React**, **Svelte**, and **SolidJS**.
</Note>

With `useChat` and `streamText`, you can use tools in your chatbot application.
The Vercel AI SDK supports three types of tools in this context:

1. Automatically executed server-side tools
2. Automatically executed client-side tools
3. Tools that require user interaction, such as confirmation dialogs

The flow is as follows:

1. The user enters a message in the chat UI.
1. The message is sent to the API route.
1. The messages from the client are converted to AI SDK Core messages using `convertToCoreMessages`.
1. In your server side route, the language model generates tool calls during the `streamText` call.
1. All tool calls are forwarded to the client.
1. Server-side tools are executed using their `execute` method and their results are forwarded to the client.
1. Client-side tools that should be automatically executed are handled with the `onToolCall` callback.
   You can return the tool result from the callback.
1. Client-side tool that require user interactions can be displayed in the UI.
   The tool calls and results are available in the `toolInvocations` property of the last assistant message.
1. When the user interaction is done, `addToolResult` can be used to add the tool result to the chat.
1. When there are tool calls in the last assistant message and all tool results are available, the client sends the updated messages back to the server.
   This triggers another iteration of this flow.

The tool call and tool executions are integrated into the assistant message as `toolInvocations`.
A tool invocation is at first a tool call, and then it becomes a tool result when the tool is executed.
The tool result contains all information about the tool call as well as the result of the tool execution.

<Note>
  In order to automatically send another request to the server when all tool
  calls are server-side, you need to set
  [`maxToolRoundtrips`](/docs/reference/ai-sdk-ui/use-chat#max-tool-roundtrips)
  to a value greater than 0 in the `useChat` options. It is disabled by default
  for backward compatibility.
</Note>

### Example

In this example, we'll use three tools:

- `getWeatherInformation`: An automatically executed server-side tool that returns the weather in a given city.
- `askForConfirmation`: A user-interaction client-side tool that asks the user for confirmation.
- `getLocation`: An automatically executed client-side tool that returns a random city.

#### API route

```tsx filename='app/api/chat/route.ts'
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

#### Client-side page

The client-side page uses the `useChat` hook to create a chatbot application with real-time message streaming.
Tool invocations are displayed in the chat UI.

There are three things worth mentioning:

1. The [`onToolCall`](/docs/reference/ai-sdk-ui/use-chat#on-tool-call) callback is used to handle client-side tools that should be automatically executed.
   In this example, the `getLocation` tool is a client-side tool that returns a random city.

2. The `toolInvocations` property of the last assistant message contains all tool calls and results.
   The client-side tool `askForConfirmation` is displayed in the UI.
   It asks the user for confirmation and displays the result once the user confirms or denies the execution.
   The result is added to the chat using `addToolResult`.

3. The [`maxToolRoundtrips`](/docs/reference/ai-sdk-ui/use-chat#max-tool-roundtrips) option is set to 5.
   This enables several tool use iterations between the client and the server.

```tsx filename='app/page.tsx' highlight="9,12,31"
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

<Note>This feature is experimental.</Note>

You can stream tool calls while they are being generated by enabling the
`experimental_toolCallStreaming` option in `streamText`.

```tsx filename='app/api/chat/route.ts' highlight="5"
export async function POST(req: Request) {
  // ...

  const result = await streamText({
    experimental_toolCallStreaming: true,
    // ...
  });

  return result.toDataStreamResponse();
}
```

When the flag is enabled, partial tool calls will be streamed as part of the AI stream.
They are available through the `useChat` hook.
The `toolInvocations` property of assistant messages will also contain partial tool calls.
You can use the `state` property of the tool invocation to render the correct UI.

```tsx filename='app/page.tsx' highlight="9,10"
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

---
title: Completion
description: Learn how to use the useCompletion hook.
---

## Completion

The `useCompletion` hook allows you to create a user interface to handle text completions in your application. It enables the streaming of text completions from your AI provider, manages the state for chat input, and updates the UI automatically as new messages are received.

In this guide, you will learn how to use the `useCompletion` hook in your application to generate text completions and stream them in real-time to your users.

### Example

```tsx filename='app/page.tsx'
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

```ts filename='app/api/completion/route.ts'
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

### Customized UI

`useCompletion` also provides ways to manage the prompt via code, show loading and error states, and update messages without being triggered by user interactions.

#### Loading and error states

To show a loading spinner while the chatbot is processing the user's message, you can use the `isLoading` state returned by the `useCompletion` hook:

```tsx
const { isLoading, ... } = useCompletion()

return(
  <>
    {isLoading ? <Spinner /> : null}
  </>
)
```

Similarly, the `error` state reflects the error object thrown during the fetch request. It can be used to display an error message, or show a toast notification:

```tsx
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

#### Controlled input

In the initial example, we have `handleSubmit` and `handleInputChange` callbacks that manage the input changes and form submissions. These are handy for common use cases, but you can also use uncontrolled APIs for more advanced scenarios such as form validation or customized components.

The following example demonstrates how to use more granular APIs like `setInput` with your custom input and submit button components:

```tsx
const { input, setInput } = useCompletion();

return (
  <>
    <MyCustomInput value={input} onChange={value => setInput(value)} />
  </>
);
```

#### Cancelation

It's also a common use case to abort the response message while it's still streaming back from the AI provider. You can do this by calling the `stop` function returned by the `useCompletion` hook.

```tsx
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

```tsx
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

```tsx
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

---
title: Object Generation
description: Learn how to use the useObject hook.
---

## Object Generation

<Note>`useObject` is an experimental feature and only available in React.</Note>

The [`useObject`](/docs/reference/ai-sdk-ui/use-object) hook allows you to create interfaces that represent a structured JSON object that is being streamed.

In this guide, you will learn how to use the `useObject` hook in your application to generate UIs for structured data on the fly.

### Example

The example shows a small notfications demo app that generates fake notifications in real-time.

#### Schema

It is helpful to set up the schema in a separate file that is imported on both the client and server.

```ts filename='app/api/notifications/schema.ts'
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

#### Client

The client uses [`useObject`](/docs/reference/ai-sdk-ui/use-object) to stream the object generation process.

The results are partial and are displayed as they are received.
Please note the code for handling `undefined` values in the JSX.

```tsx filename='app/page.tsx'
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

#### Server

On the server, we use [`streamObject`](/docs/reference/ai-sdk-core/stream-object) to stream the object generation process.

```typescript filename='app/api/notifications/route.ts'
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

### Event Callbacks

`useObject` provides optional event callbacks that you can use to handle life-cycle events.

- `onFinish`: Called when the object generation is completed.
- `onError`: Called when an error occurs during the fetch request.

These callbacks can be used to trigger additional actions, such as logging, analytics, or custom UI updates.

```tsx filename='app/page.tsx' highlight="10-20"
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

---
title: Storing Messages
description: Welcome to the Vercel AI SDK documentation!
---

## Storing Messages

The ability to store message history is essential for chatbot use cases.
The Vercel AI SDK simplifies the process of storing chat history through the `onFinish` callback of the `streamText` function.

`onFinish` is called after the model's response and all tool executions have completed.
It provides the final text, tool calls, tool results, and usage information,
making it an ideal place to e.g. store the chat history in a database.

### Implementing Persistent Chat History

To implement persistent chat storage, you can utilize the `onFinish` callback on the `streamText` function.
This callback is triggered upon the completion of the model's response and all tool executions,
making it an ideal place to handle the storage of each interaction.

#### API Route Example

```tsx highlight="13-16"
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

#### Server Action Example

```tsx highlight="10-13"
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

---
title: Streaming Data
description: Welcome to the Vercel AI SDK documentation!
---

## Streaming Data

Depending on your use case, you may want to stream additional data alongside the model's response.
This can be achieved with [`StreamData`](/docs/reference/stream-helpers/stream-data).

### What is StreamData

The `StreamData` class allows you to stream arbitrary data to the client alongside your LLM response.
This can be particularly useful in applications that need to augment AI responses with metadata, auxiliary information,
or custom data structures that are relevant to the ongoing interaction.

### How To Use StreamData

To use `StreamData`, create a `StreamData` value on the server,
append some data, and then include it in `toDataStreamResponse`.

You need to call `close()` on the `StreamData` object to ensure the data is sent to the client.
This can best be done in the `onFinish` callback of `streamText`.

On the client, the [`useChat`](/docs/reference/ai-sdk-ui/use-chat) hook returns `data`, which will contain the additional data.

#### On the server

<Note>
  While this example uses Next.js (App Router), `StreamData` is compatible with
  any framework.
</Note>

```tsx
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

#### On the client

On the client, you can destructure `data` from the `useChat` hook which stores all `StreamData` in an array. The `data` is of the type `JSONValue[]`.

```tsx
const { data } = useChat();
```

Future versions of the AI SDK will support each `Message` having a `data` object attached to it.

---
title: Error Handling
description: Learn how to handle errors in the AI SDK UI
---

## Error Handling

#### useChat: Keep Last Message on Error

`useChat` has a `keepLastMessageOnError` option that defaults to `false`.
This option can be enabled to keep the last message on error.
We will make this the default behavior in the next major release,
and recommend enabling it.

#### Error Helper Object

Each AI SDK UI hook also returns an [error](/docs/reference/ai-sdk-ui/use-chat#error) object that you can use to render the error in your UI.
You can use the error object to show an error message, disable the submit button, or show a retry button.

<Note>
  We recommend showing a generic error message to the user, such as "Something
  went wrong." This is a good practice to avoid leaking information from the
  server.
</Note>

```tsx file="app/page.tsx" highlight="7,17-24,30"
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

##### Alternative: replace last message

Alternatively you can write a custom submit handler that replaces the last message when an error is present.

```tsx file="app/page.tsx" highlight="15-21,33"
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

#### Error Handling Callback

Errors can be processed by passing an [`onError`](/docs/reference/ai-sdk-ui/use-chat#on-error) callback function as an option to the [`useChat`](/docs/reference/ai-sdk-ui/use-chat), [`useCompletion`](/docs/reference/ai-sdk-ui/use-completion) or [`useAssistant`](/docs/reference/ai-sdk-ui/use-assistant) hooks.
The callback function receives an error object as an argument.

```tsx file="app/page.tsx" highlight="8-11"
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

#### Injecting Errors for Testing

You might want to create errors for testing.
You can easily do so by throwing an error in your route handler:

```ts file="app/api/chat/route.ts"
export async function POST(req: Request) {
  throw new Error('This is a test error');
}
```

---
title: Stream Protocols
description: Learn more about the supported stream protocols in the Vercel AI SDK.
---

## Stream Protocols

AI SDK UI functions such as `useChat` and `useCompletion` support both text streams and data streams.
The stream protocol defines how the data is streamed to the frontend on top of the HTTP protocol.

This page describes both protocols and how to use them in the backend and frontend.

You can use this information to develop custom backends and frontends for your use case, e.g.,
to provide compatible API endpoints that are implemented in a different language such as Python.

For instance, here's an example using [FastAPI](https://github.com/vercel/ai/tree/main/examples/next-fastapi) as a backend.

### Text Stream Protocol

A text stream contains chunks in plain text, that are streamed to the frontend.
Each chunk is then appended together to form a full text response.

Text streams are supported by `useChat`, `useCompletion`, and `useObject`.
When you use `useChat` or `useCompletion`, you need to enable text streaming
by setting the `streamProtocol` options to `text`.

You can generate text streams with `streamText` in the backend.
When you call `toTextStreamResponse()` on the result object,
a streaming HTTP response is returned.

<Note>
  Text streams only support basic text data. If you need to stream other types
  of data such as tool calls, use data streams.
</Note>

#### Text Stream Example

Here is a Next.js example that uses the text stream protocol:

```tsx filename='app/page.tsx' highlight="7"
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

```ts filename='app/api/completion/route.ts' highlight="15"
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

### Data Stream Protocol

A data stream follows a special protocol that the Vercel AI SDK provides to send information to the frontend.

Each stream part has the format `TYPE_ID:CONTENT_JSON\n`.

<MDXImage
  srcLight="/images/data-stream-protocol.png"
  srcDark="/images/data-stream-protocol.png"
  width={3200}
  height={1712}
/>

<Note>
  When you provide data streams from a custom backend, you need to set the
  `x-vercel-ai-data-stream` header to `v1`.
</Note>

The following stream parts are currently supported:

#### Text Part

The text parts are appended to the message as they are received.

Format: `0:string\n`

Example: `0:"example"\n`

<MDXImage
  srcLight="/images/text-part.png"
  srcDark="/images/text-part.png"
  width={3200}
  height={1148}
/>

#### Data Part

The data parts are parsed as JSON and appended to the message as they are received. The data part is available through `data`.

Format: `2:Array<JSONValue>\n`

Example: `2:[{"key":"object1"},{"anotherKey":"object2"}]\n`

<MDXImage
  srcLight="/images/data-part.png"
  srcDark="/images/data-part.png"
  width={3200}
  height={1148}
/>

#### Error Part

The error parts are appended to the message as they are received.

Format: `3:string\n`

Example: `3:"error message"\n`

<MDXImage
  srcLight="/images/error-part.png"
  srcDark="/images/error-part.png"
  width={3200}
  height={1148}
/>

### Tool Call Streaming Start Part

A part indicating the start of a streaming tool call. This part needs to be sent before any tool call delta for that tool call. Tool call streaming is optional, you can use tool call and tool result parts without it.

Format: `b:{toolCallId:string; toolName:string}\n`

Example: `b:{"toolCallId":"call-456","toolName":"streaming-tool"}\n`

<MDXImage
  srcLight="/images/tool-call-streaming-start-part.png"
  srcDark="/images/tool-call-streaming-start-part.png"
  width={3200}
  height={1148}
/>

#### Tool Call Delta Part

A part representing a delta update for a streaming tool call.

Format: `c:{toolCallId:string; argsTextDelta:string}\n`

Example: `c:{"toolCallId":"call-456","argsTextDelta":"partial arg"}\n`

<MDXImage
  srcLight="/images/tool-call-delta-part.png"
  srcDark="/images/tool-call-delta-part.png"
  width={3200}
  height={1148}
/>

#### Tool Call Part

A part representing a tool call. When there are streamed tool calls, the tool call part needs to come after the tool call streaming is finished.

Format: `9:{toolCallId:string; toolName:string; args:object}\n`

Example: `9:{"toolCallId":"call-123","toolName":"my-tool","args":{"some":"argument"}}\n`

<MDXImage
  srcLight="/images/tool-call-part.png"
  srcDark="/images/tool-call-part.png"
  width={3200}
  height={1148}
/>

#### Tool Result Part

A part representing a tool result. The result part needs to be sent after the tool call part for that tool call.

Format: `a:{toolCallId:string; result:object}\n`

Example: `a:{"toolCallId":"call-123","result":"tool output"}\n`

<MDXImage
  srcLight="/images/tool-result-part.png"
  srcDark="/images/tool-result-part.png"
  width={3200}
  height={1148}
/>

#### Finish Message Part

A part indicating the completion of a message with additional metadata, such as [`FinishReason`](/docs/reference/ai-sdk-ui/use-chat#on-finish-finish-reason) and [`Usage`](/docs/reference/ai-sdk-ui/use-chat#on-finish-usage). This part needs to be the last part in the stream.

Format: `d:{finishReason:'stop' | 'length' | 'content-filter' | 'tool-calls' | 'error' | 'other' | 'unknown';usage:{promptTokens:number; completionTokens:number;}}\n`

Example: `d:{"finishReason":"stop","usage":{"promptTokens":10,"completionTokens":20}}\n`

<MDXImage
  srcLight="/images/finish-message-part.png"
  srcDark="/images/finish-message-part.png"
  width={3200}
  height={1148}
/>

The data stream protocol is supported
by `useChat` and `useCompletion` on the frontend and used by default.
`useCompletion` only supports the `text` and `data` stream parts.

On the backend, you can use `toDataStreamResponse()` from the `streamText` result object to return a streaming HTTP response.

#### Data Stream Example

Here is a Next.js example that uses the data stream protocol:

```tsx filename='app/page.tsx' highlight="7"
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

```ts filename='app/api/completion/route.ts' highlight="15"
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