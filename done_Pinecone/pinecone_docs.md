# PineCone Vector Database

## Quickstart

Pinecone provides long-term memory for high-performance AI applications. It's a managed, cloud-native vector database with a streamlined API and no infrastructure hassles. Pinecone serves fresh, relevant query results with low latency at the scale of billions of vectors.

This guide shows you how to set up and use a Pinecone vector database in minutes.

1. **Sign up for Pinecone or log in**

   Sign up for a free Pinecone account or log in to your existing account:

   Your API key will display in the next section.

   On the free Starter plan, you get 1 project, 5 serverless indexes in the us-east-1 region of AWS, and up to 2 GB of storage. Although the Starter plan has stricter limits than other plans, you can upgrade whenever you're ready.

2. **Get your API key**

   You need an API key to make API calls to your Pinecone project. Copy your generated key:

   ```
   PINECONE_API_KEY="YOUR_API_KEY"
   ```

   Alternatively, you can get your key from the Pinecone console:

   - Open the Pinecone console.
   - Select your project.
   - Go to API Keys.
   - Copy your API key.

3. **Install a Pinecone SDK**

   Pinecone exposes a simple REST API for interacting with its vector database. You can use the API directly, or you can use one of the official Python, Node.js, Java, or Go SDKs:

   ```bash
   npm install @pinecone-database/pinecone
   ```

4. **Initialize your client connection**

   Using your API key, initialize your client connection to Pinecone:

   ```javascript
   import { Pinecone } from '@pinecone-database/pinecone';

   const pc = new Pinecone({ apiKey: 'YOUR_API_KEY' });
   ```

   When using the API directly, each HTTP request must contain an `Api-Key` header that specifies your API key. You'll see this in all subsequent curl examples.

5. **Create a serverless index**

   In Pinecone, an index is the highest-level organizational unit of data, where you define the dimension of vectors to be stored and the similarity metric to be used when querying them. Normally, you choose a dimension and similarity metric based on the embedding model used to create your vectors. For this quickstart, however, you'll use a configuration that makes it easy to verify your query results.

   Create a serverless index named `docs-quickstart-index` that stores vectors of 2 dimensions and performs nearest-neighbor search using the cosine similarity metric:

   ```javascript
   const indexName = "docs-quickstart-index"

   await pc.createIndex({
     name: indexName,
     dimension: 2,
     metric: 'cosine',
     spec: { 
       serverless: { 
         cloud: 'aws', 
         region: 'us-east-1' 
       }
     } 
   }); 
   ```

   In this Quickstart, you are creating a serverless index in the us-east-1 region of AWS (the default). Pinecone supports multiple cloud providers and regions.

6. **Upsert vectors**

   Within an index, vectors are stored in namespaces, and all upserts, queries, and other data operations always target one namespace:

   *Namespaces are essential for implementing multitenancy when you need to isolate the data of each customer/user.*

   Now that you've created your index, target the `docs-quickstart-index` index and use the upsert operation to write six 2-dimensional vectors into 2 distinct namespaces:

   ```javascript
   const index = pc.index(indexName);

   await index.namespace("ns1").upsert([
     {
       "id": "vec1", 
       "values": [1.0, 1.5]
     },
     {
       "id": "vec2", 
       "values": [2.0, 1.0]
     },
     {
       "id": "vec3", 
       "values": [0.1, 3.0]
     }
   ]);

   await index.namespace("ns2").upsert([
     {
       "id": "vec1", 
       "values": [1.0, -2.5]
     },
     {
       "id": "vec2", 
       "values": [3.0, -2.0]
     },
     {
       "id": "vec3", 
       "values": [0.5, -1.5]
     }
   ]);
   ```

   When upserting larger amounts of data, it is recommended to upsert records in large batches. This should be as large as possible (up to 1000 records) without exceeding the maximum request size of 2MB.

7. **Check the index**

   Pinecone is eventually consistent, so there can be a delay before your upserted vectors are available to query. Use the `describe_index_stats` operation to check if the current vector count matches the number of vectors you upserted:

   ```javascript
   const stats = await index.describeIndexStats();

   console.log(stats)

   // Returns:
   // {
   //   namespaces: { ns1: { recordCount: 3 }, ns2: { recordCount: 3 } },
   //   dimension: 2,
   //   indexFullness: 0.00008,
   //   totalRecordCount: 6
   // }
   ```

8. **Run a similarity search**

   Query each namespace in your index for the 3 vectors that are most similar to an example 2-dimensional vector using the cosine similarity metric you specified for the index:

   ```javascript
   const queryResponse1 = await index.namespace("ns1").query({
     topK: 3,
     vector: [1.0, 1.5],
     includeValues: true
   });

   const queryResponse2 = await index.namespace("ns2").query({
     topK: 3,
     vector: [1.0,-2.5],
     includeValues: true
   });

   // Returns:
   // {
   //   "matches": [
   //     {
   //       "id": "vec1",
   //       "score": 1,
   //       "values": [
   //         1,
   //         1.5
   //       ]
   //     },
   //     {
   //       "id": "vec2",
   //       "score": 0.868243158,
   //       "values": [
   //         2,
   //         1
   //       ]
   //     },
   //     {
   //       "id": "vec3",
   //       "score": 0.850068152,
   //       "values": [
   //         0.1,
   //         3
   //       ]
   //     }
   //   ],
   //   "namespace": "ns1",
   //   "usage": {
   //     "readUnits": 6
   //   }
   // }
   // {
   //   "matches": [
   //     {
   //       "id": "vec1",
   //       "score": 1,
   //       "values": [
   //         1,
   //         -2.5
   //       ]
   //     },
   //     {
   //       "id": "vec3",
   //       "score": 0.998274386,
   //       "values": [
   //         0.5,
   //         -1.5
   //       ]
   //     },
   //     {
   //       "id": "vec2",
   //       "score": 0.824041963,
   //       "values": [
   //         3,
   //         -2
   //       ]
   //     }
   //   ],
   //   "namespace": "ns2",
   //   "usage": {
   //     "readUnits": 6
   //   }
   // }
   ```

   Your index uses the cosine distance metric, which measures similarity based on the angle between two vectors. Scores range between 1 and -1. The greater the score, the greater the similarity between the vectors.

   In the following graph, the upper and lower right quadrants show the query results from namespaces ns1 and ns2, respectively. In each quadrant, blue represents the most similar vector, green the second most similar, and red the third most similar. In this case, the most similar vectors are identical to the query vectors, with a score of 1, and so the blue arrows represent both the query vectors and the nearest vectors.

   *[Note: An image showing cosine similarity would be inserted here]*

   This is a simple example. As you put more demands on Pinecone, you'll see it returning low-latency, accurate results at huge scales, with indexes of up to billions of vectors.

9. **Clean up**

   When you no longer need the "docs-quickstart-index" index, use the `delete_index` operation to delete it:

   ```javascript
   await pc.deleteIndex(indexName);
   ```

   After you delete an index, you cannot use it again or recover it.

# Key concepts

## Organization
An organization is a group of one or more projects that use the same billing. Organizations allow one or more users to control billing and permissions for all of the projects belonging to the organization.

For more information, see [Understanding organizations](https://docs.pinecone.io/docs/organizations).

## Project
A project belongs to an organization and contains one or more indexes. Each project belongs to exactly one organization, but only users who belong to the project can access the indexes in that project. API keys and Assistants are project-specific.

For more information, see [Understanding projects](https://docs.pinecone.io/docs/projects).

## Index
An index is the highest-level organizational unit of data. It defines the dimension (i.e., number of values in a vector) of the vectors to be stored and the similarity metric to be used when querying them. Normally, you choose a dimension and similarity metric based on the embedding model used to create your vectors.

In Pinecone, there are two types of indexes: serverless and pod-based.

For more information, see [Understanding indexes](https://docs.pinecone.io/docs/indexes).

## Namespace
A namespace is a partition within an index. It divides records in an index into separate groups.

All upserts, queries, and other data operations always target one namespace.

For more information, see [Use namespaces](https://docs.pinecone.io/docs/namespaces).

## Record
A record is a basic unit of data and consists of the following:

- Record ID
- Dense vector
- Metadata (optional)
- Sparse vector (optional)

For more information, see [Upsert data](https://docs.pinecone.io/docs/upsert-data).

## Record ID
A record ID is a record's unique ID. Use ID prefixes to segment your data beyond namespaces.

## Dense vector
A dense vector, also referred to as a vector embedding or simply a vector, is the basic vector type in Pinecone. It is a series of numerical values that represent different dimensions of the data that are essential for understanding patterns, relationships, and underlying structures (i.e., its semantic information). A vector is a type of data representation that is generated by AI models, such as LLMs.

## Metadata
Metadata is additional information that can be attached to vector embeddings to provide more context and enable additional filtering capabilities. For example, the original text of the embeddings can be stored in the metadata.

## Sparse vector
A sparse vector, also referred to as a sparse vector embedding, has a large number of dimensions, but only a small proportion of those values are non-zero. Sparse vectors are often used to represent documents or queries in a way that captures keyword information. Each dimension in a sparse vector typically represents a word from a dictionary, and the non-zero values represent the importance of these words in the document.

For more information, see [Understanding hybrid search](https://docs.pinecone.io/docs/hybrid-search).

## Other concepts
Although not represented in the diagram above, Pinecone also contains the following concepts:

- API key
- User
- Collection
- Pinecone Assistant
- Pinecone Inference

### API key
An API key is a unique token that authenticates and authorizes access to the Pinecone APIs. API keys are project-specific.

### User
A user is a member of organizations and projects. Users are assigned specific roles at the organization and project levels that determine the user's permissions in the Pinecone console.

For more information, see [Manage organization members](https://docs.pinecone.io/docs/manage-org-members) and [Manage project members](https://docs.pinecone.io/docs/manage-project-members).

### Collection
A collection is a static copy of a pod-based index. It is a non-queryable representation of a set of vectors and metadata. A collection can only be created from a pod-based index, but you can create either a serverless index or a pod-based index from a collection.

For more information, see [Understanding collections](https://docs.pinecone.io/docs/collections).

### Pinecone Assistant
Pinecone Assistant is a service that allows you to upload documents, ask questions, and receive responses that reference your documents. This is known as retrieval-augmented generation (RAG).

For more information, see [Understanding Pinecone Assistant](https://docs.pinecone.io/docs/pinecone-assistant).

### Pinecone Inference
Pinecone Inference is an API service that provides access to embedding models hosted on Pinecone's infrastructure.

For more information, see [Understanding Pinecone Inference](https://docs.pinecone.io/docs/pinecone-inference).

# Guides

## Build a RAG chatbot

This page shows you how to build a simple RAG chatbot in TypeScript using Pinecone for the vector database and embedding model, OpenAI for the LLM, and LangChain for the RAG workflow.

To run through this guide in your browser, use the [Build a RAG chatbot](https://colab.research.google.com/github/pinecone-io/examples/blob/master/learn/generation/langchain/rag-chatbot.ipynb) colab notebook.

### How it works

GenAI chatbots built on Large Language Models (LLMs) can answer many questions. However, when the questions concern private data that the LLMs have not been trained on, you can get answers that sound convincing but are factually wrong. This behavior is referred to as "hallucination".

Retrieval augmented generation (RAG) is a framework that prevents hallucination by providing LLMs the knowledge that they are missing, based on private data stored in a vector database like Pinecone.

![RAG overview](path/to/rag_overview_image.png)

### Before you begin

Ensure you have the following:

- A Pinecone account and API key.
- An OpenAI account and API key.

### 1. Set up your environment

Install the Pinecone and LangChain libraries required for this guide:

```bash
npm install \
    @pinecone-database/pinecone \
    langchain-pinecone \
    langchain-openai \
    langchain-text-splitters \
    langchain
```

Set environment variables for your Pinecone and OpenAI API keys:

```bash
export PINECONE_API_KEY="<your Pinecone API key>" # available at app.pinecone.io
export OPENAI_API_KEY="<your OpenAI API key>" # available at platform.openai.com/api-keys
```

### 2. Store knowledge in Pinecone

For this guide, you'll use a document about a fictional product called the WonderVector5000 that LLMs do not have any information about. First, you'll create a Pinecone index. Then you'll use LangChain to chunk the document into smaller segments, create vector embeddings for each segment via Pinecone Inference, and upsert the vector embeddings into your Pinecone index.

Pinecone Inference is an API service that gives you access to embedding models hosted on Pinecone's infrastructure. You can use the Inference API directly or through Langchain's PineconeEmbeddings class, as shown in this guide.

[Browse the document](path/to/document)

Create a serverless index in Pinecone for storing the embeddings of your document, setting the index dimensions and distance metric to match those of the multilingual-e5-large model you'll use to create the embeddings:

```typescript
import { PineconeClient } from '@pinecone-database/pinecone';
import { ServerlessSpec } from '@pinecone-database/pinecone';

const pc = new PineconeClient();
await pc.init({
  apiKey: process.env.PINECONE_API_KEY,
  environment: 'gcp-starter'
});

const indexName = 'docs-rag-chatbot';
const indexList = await pc.listIndexes();

if (!indexList.includes(indexName)) {
  await pc.createIndex({
    name: indexName,
    dimension: 1024,
    metric: 'cosine',
    spec: new ServerlessSpec('aws', 'us-east-1')
  });
}
```

Since your document is in Markdown, chunk the content based on structure to get semantically coherent segments. Then use Pinecone Inference to embed each chunk and upsert the embeddings into your Pinecone index.

```typescript
import { PineconeEmbeddings } from 'langchain-pinecone';
import { PineconeStore } from 'langchain-pinecone';
import { MarkdownHeaderTextSplitter } from 'langchain-text-splitters';

// Chunk the document based on h2 headers.
const markdownDocument = "## Introduction\n\nWelcome to the whimsical world of the WonderVector5000, an astonishing leap into the realms of imaginative technology. This extraordinary device, borne of creative fancy, promises to revolutionize absolutely nothing while dazzling you with its fantastical features. Whether you're a seasoned technophile or just someone looking for a bit of fun, the WonderVector5000 is sure to leave you amused and bemused in equal measure. Let's explore the incredible, albeit entirely fictitious, specifications, setup process, and troubleshooting tips for this marvel of modern nonsense.\n\n## Product overview\n\nThe WonderVector5000 is packed with features that defy logic and physics, each designed to sound impressive while maintaining a delightful air of absurdity:\n\n- Quantum Flibberflabber Engine: The heart of the WonderVector5000, this engine operates on principles of quantum flibberflabber, a phenomenon as mysterious as it is meaningless. It's said to harness the power of improbability to function seamlessly across multiple dimensions.\n\n- Hyperbolic Singularity Matrix: This component compresses infinite possibilities into a singular hyperbolic state, allowing the device to predict outcomes with 0% accuracy, ensuring every use is a new adventure.\n\n- Aetherial Flux Capacitor: Drawing energy from the fictional aether, this flux capacitor provides unlimited power by tapping into the boundless reserves of imaginary energy fields.\n\n- Multi-Dimensional Holo-Interface: Interact with the WonderVector5000 through its holographic interface that projects controls and information in three-and-a-half dimensions, creating a user experience that's simultaneously futuristic and perplexing.\n\n- Neural Fandango Synchronizer: This advanced feature connects directly to the user's brain waves, converting your deepest thoughts into tangible actions—albeit with results that are whimsically unpredictable.\n\n- Chrono-Distortion Field: Manipulate time itself with the WonderVector5000's chrono-distortion field, allowing you to experience moments before they occur or revisit them in a state of temporal flux.\n\n## Use cases\n\nWhile the WonderVector5000 is fundamentally a device of fiction and fun, let's imagine some scenarios where it could hypothetically be applied:\n\n- Time Travel Adventures: Use the Chrono-Distortion Field to visit key moments in history or glimpse into the future. While actual temporal manipulation is impossible, the mere idea sparks endless storytelling possibilities.\n\n- Interdimensional Gaming: Engage with the Multi-Dimensional Holo-Interface for immersive, out-of-this-world gaming experiences. Imagine games that adapt to your thoughts via the Neural Fandango Synchronizer, creating a unique and ever-changing environment.\n\n- Infinite Creativity: Harness the Hyperbolic Singularity Matrix for brainstorming sessions. By compressing infinite possibilities into hyperbolic states, it could theoretically help unlock unprecedented creative ideas.\n\n- Energy Experiments: Explore the concept of limitless power with the Aetherial Flux Capacitor. Though purely fictional, the notion of drawing energy from the aether could inspire innovative thinking in energy research.\n\n## Getting started\n\nSetting up your WonderVector5000 is both simple and absurdly intricate. Follow these steps to unleash the full potential of your new device:\n\n1. Unpack the Device: Remove the WonderVector5000 from its anti-gravitational packaging, ensuring to handle with care to avoid disturbing the delicate balance of its components.\n\n2. Initiate the Quantum Flibberflabber Engine: Locate the translucent lever marked "QFE Start" and pull it gently. You should notice a slight shimmer in the air as the engine engages, indicating that quantum flibberflabber is in effect.\n\n3. Calibrate the Hyperbolic Singularity Matrix: Turn the dials labeled 'Infinity A' and 'Infinity B' until the matrix stabilizes. You'll know it's calibrated correctly when the display shows a single, stable "∞".\n\n4. Engage the Aetherial Flux Capacitor: Insert the EtherKey into the designated slot and turn it clockwise. A faint humming sound should confirm that the aetherial flux capacitor is active.\n\n5. Activate the Multi-Dimensional Holo-Interface: Press the button resembling a floating question mark to activate the holo-interface. The controls should materialize before your eyes, slightly out of phase with reality.\n\n6. Synchronize the Neural Fandango Synchronizer: Place the neural headband on your forehead and think of the word "Wonder". The device will sync with your thoughts, a process that should take just a few moments.\n\n7. Set the Chrono-Distortion Field: Use the temporal sliders to adjust the time settings. Recommended presets include "Past", "Present", and "Future", though feel free to explore other, more abstract temporal states.\n\n## Troubleshooting\n\nEven a device as fantastically designed as the WonderVector5000 can encounter problems. Here are some common issues and their solutions:\n\n- Issue: The Quantum Flibberflabber Engine won't start.\n\n    - Solution: Ensure the anti-gravitational packaging has been completely removed. Check for any residual shards of improbability that might be obstructing the engine.\n\n- Issue: The Hyperbolic Singularity Matrix displays "∞∞".\n\n    - Solution: This indicates a hyper-infinite loop. Reset the dials to zero and then adjust them slowly until the display shows a single, stable infinity symbol.\n\n- Issue: The Aetherial Flux Capacitor isn't engaging.\n\n    - Solution: Verify that the EtherKey is properly inserted and genuine. Counterfeit EtherKeys can often cause malfunctions. Replace with an authenticated EtherKey if necessary.\n\n- Issue: The Multi-Dimensional Holo-Interface shows garbled projections.\n\n    - Solution: Realign the temporal resonators by tapping the holographic screen three times in quick succession. This should stabilize the projections.\n\n- Issue: The Neural Fandango Synchronizer causes headaches.\n\n    - Solution: Ensure the headband is properly positioned and not too tight. Relax and focus on simple, calming thoughts to ease the synchronization process.\n\n- Issue: The Chrono-Distortion Field is stuck in the past.\n\n    - Solution: Increase the temporal flux by 5%. If this fails, perform a hard reset by holding down the "Future" slider for ten seconds.";

const headersToSplitOn = [
  ["##", "Header 2"]
];

const markdownSplitter = new MarkdownHeaderTextSplitter({
  headersToSplitOn: headersToSplitOn,
  stripHeaders: false
});
const mdHeaderSplits = await markdownSplitter.splitText(markdownDocument);

// Initialize a LangChain embedding object.
const modelName = "multilingual-e5-large";
const embeddings = new PineconeEmbeddings({
  model: modelName,
  pineconeApiKey: process.env.PINECONE_API_KEY
});

// Embed each chunk and upsert the embeddings into your Pinecone index.
const docsearch = await PineconeStore.fromDocuments(
  mdHeaderSplits,
  embeddings,
  {
    pineconeIndex: pc.Index(indexName),
    namespace: "wondervector5000"
  }
);

await new Promise(resolve => setTimeout(resolve, 1000));
```

Use Pinecone's list and query operations to look at one of the records:

```typescript
import { PineconeClient } from '@pinecone-database/pinecone';

const pc = new PineconeClient();
await pc.init({
  apiKey: process.env.PINECONE_API_KEY,
  environment: 'gcp-starter'
});

const index = pc.Index("docs-rag-chatbot");
const namespace = "wondervector5000";

const ids = await index.list({ namespace });
for (const id of ids) {
  const query = await index.query({
    id,
    namespace,
    topK: 1,
    includeValues: true,
    includeMetadata: true
  });
  console.log(query);
}

// Response:
// {'matches': [{'id': '8a7e5227-a738-4422-9c25-9a6136825803',
//             'metadata': {'Header 2': 'Introduction',
//                         'text': '## Introduction  \n'
//                                 'Welcome to the whimsical world of the '
//                                 'WonderVector5000, an astonishing leap into '
//                                 'the realms of imaginative technology. This '
//                                 'extraordinary device, borne of creative '
//                                 'fancy, promises to revolutionize '
//                                 'absolutely nothing while dazzling you with '
//                                 "its fantastical features. Whether you're a "
//                                 'seasoned technophile or just someone '
//                                 'looking for a bit of fun, the '
//                                 'WonderVector5000 is sure to leave you '
//                                 "amused and bemused in equal measure. Let's "
//                                 'explore the incredible, albeit entirely '
//                                 'fictitious, specifications, setup process, '
//                                 'and troubleshooting tips for this marvel '
//                                 'of modern nonsense.'},
//             'score': 1.0080868,
//             'values': [-0.00798303168,
//                        0.00551192369,
//                        -0.00463955849,
//                        -0.00585730933,
//                        ...
//                       ]}],
// 'namespace': 'wondervector5000',
// 'usage': {'readUnits': 6}}    
```

### 3. Use the chatbot

Now that your document is stored as embeddings in Pinecone, when you send questions to the LLM, you can add relevant knowledge from your Pinecone index to ensure that the LLM returns an accurate response.

Initialize a LangChain object for chatting with the gpt-3.5-turbo LLM, define a few questions about the WonderVector5000, and then send the questions to the LLM, first with relevant knowledge from Pincone and then without any additional knowledge.

The questions require specific, private knowledge of the product, which the LLM does not have by default.

```typescript
import { RetrievalQAChain } from 'langchain/chains';
import { ChatOpenAI } from 'langchain/chat_models/openai';
import { OpenAIEmbeddings } from 'langchain/embeddings/openai';
import { PineconeStore } from 'langchain-pinecone';

// Initialize a LangChain object for chatting with the LLM
// without knowledge from Pinecone.
const llm = new ChatOpenAI({
  openAIApiKey: process.env.OPENAI_API_KEY,
  modelName: "gpt-3.5-turbo",
  temperature: 0.0
});

// Initialize a LangChain object for retrieving information from Pinecone.
const knowledge = await PineconeStore.fromExistingIndex(
  new OpenAIEmbeddings({ openAIApiKey: process.env.OPENAI_API_KEY }),
  {
    pineconeIndex: pc.Index("docs-rag-chatbot"),
    namespace: "wondervector5000"
  }
);

// Initialize a LangChain object for chatting with the LLM
// with knowledge from Pinecone. 
const qa = RetrievalQAChain.fromLLM(llm, knowledge.asRetriever());

// Define a few questions about the WonderVector5000.
const query1 = `What are the first 3 steps for getting started 
with the WonderVector5000?`;

const query2 = `The Neural Fandango Synchronizer is giving me a 
headache. What do I do?`;

// Send each query to the LLM twice, first with relevant knowledge from Pincone 
// and then without any additional knowledge.
console.log("Query 1\n");
console.log("Chat with knowledge:");
console.log((await qa.call({ query: query1 })).text);
console.log("\nChat without knowledge:");
console.log((await llm.call(query1)).content);
console.log("\nQuery 2\n");
console.log("Chat with knowledge:");
console.log((await qa.call({ query: query2 })).text);
console.log("\nChat without knowledge:");
console.log((await llm.call(query2)).content);


// Response:
//
// Query 1

// Chat with knowledge:
// The first three steps for getting started with the WonderVector5000 are:

// 1. Unpack the Device: Remove the WonderVector5000 from its anti-gravitational packaging.
// 2. Initiate the Quantum Flibberflabber Engine: Locate the translucent lever marked "QFE Start" and pull it gently.
// 3. Calibrate the Hyperbolic Singularity Matrix: Turn the dials labeled 'Infinity A' and 'Infinity B' until the matrix stabilizes.

// Chat without knowledge:
// 1. Unbox the WonderVector5000 and carefully read the user manual provided. Familiarize yourself with the different components of the device and their functions.

// 2. Charge the WonderVector5000 using the provided charging cable. Make sure the device is fully charged before using it for the first time.

// 3. Turn on the WonderVector5000 by pressing the power button. Follow the on-screen instructions to set up the device and connect it to your Wi-Fi network.

// Query 2

// Chat with knowledge:
// Ensure the headband is properly positioned and not too tight. Relax and focus on simple, calming thoughts to ease the synchronization process.

// Chat without knowledge:
// If the Neural Fandango Synchronizer is giving you a headache, it is important to stop using it immediately and give yourself a break. Take some time to rest and relax, drink plenty of water, and consider taking over-the-counter pain medication if needed. If the headache persists or worsens, it may be a good idea to consult a healthcare professional for further advice and guidance. Additionally, it may be helpful to adjust the settings or usage of the Neural Fandango Synchronizer to see if that helps alleviate the headache.
```

For each query, notice that the first response provides very accurate information, matching closely the information in the WonderVector5000 document, while the second response sounds convincing but is generic and inaccurate.

# Authentication

This guide explains how to authenticate API calls to your Pinecone project.

## Overview

All API calls to your Pinecone index authenticate with an API key for the project containing the target index. If you are using a Pinecone SDK, you can initialize a client object, which allows you to provide your API key in one place and use it multiple times. If you are making HTTP requests with a tool like cURL, the HTTP request must include a header that specifies the API key. This topic describes each method.

## Find your Pinecone API key

1. Open the Pinecone console.
2. Select your project.
3. Go to API Keys.
4. Copy your API key.

## Initialize a client

To initialize a client with your API key, use the following code:

```javascript
import { Pinecone } from '@pinecone-database/pinecone';

const pc = new Pinecone({
    apiKey: 'YOUR_API_KEY' 
});
```

Function calls with this client use the authentication information provided at initialization. For example:

```javascript
// Creates an index using the API key stored in the client 'pc'.
await pc.createIndex({
    name: 'docs-auth-index',
    dimension: 1536,
    metric: 'cosine',
    spec: { 
        serverless: { 
            cloud: 'aws', 
            region: 'us-east-1' 
        }
    } 
}) 
```

## Add headers to an HTTP request

When issuing an HTTP request to Pinecone, each request must contain an `Api-Key` header that specifies a valid API key and must be encoded as JSON with the `Content-Type: application/json` header.

```bash
curl -X POST "https://api.pinecone.io/indexes" \
   -H "Content-Type: application/json" \
   -H "Api-Key: $PINECONE_API_KEY" \
   -H "X-Pinecone-API-Version: 2024-07" \
   -d '{
         "name":  "docs-auth-index",
         "dimension": 8,
         "metric": "cosine",
         "spec": {
            "serverless": {
               "cloud":"aws",
               "region": "us-east-1"
            }
         }
      }'
```

# Indexes

An index is the highest-level organizational unit of vector data in Pinecone. It accepts and stores vectors, serves queries over the vectors it contains, and does other vector operations over its contents.

Organizations on the Standard and Enterprise plans can create serverless indexes and pod-based indexes. Organizations on the free Starter plan can create only one starter pod-based index.

## Serverless indexes

Serverless indexes are in general availability on AWS and in public preview on GCP and Azure. Check the serverless limits and restrictions.

With serverless indexes, you don't configure or manage any compute or storage resources. Instead, based on a breakthrough architecture, serverless indexes scale automatically based on usage, and you pay only for the amount of data stored and operations performed, with no minimums. This means that there's no extra cost for having additional indexes.

For more details about how costs are calculated for a serverless index, see Understanding cost.

### Cloud regions

When creating a serverless index, you must choose the cloud and region where you want the index to be hosted. The following table lists the available public clouds and regions and the plans that support them:

| Cloud | Region | Supported plans | Availability phase |
|-------|--------|-----------------|---------------------|
| aws | us-east-1 (Virginia) | Starter, Standard, Enterprise | General Availability |
| aws | us-west-2 (Oregon) | Standard, Enterprise | General Availability |
| aws | eu-west-1 (Ireland) | Standard, Enterprise | General Availability |
| gcp | us-central1 (Iowa) | Standard, Enterprise | Public Preview |
| azure | eastus2 (Virginia) | Standard, Enterprise | Public Preview |

The cloud and region cannot be changed after a serverless index is created.

On the free Starter plan, you can create serverless indexes in the us-east-1 region of AWS only. To create indexes in other regions, upgrade your plan.

## Pod-based indexes

With pod-based indexes, you choose one or more pre-configured units of hardware (pods). Depending on the pod type, pod size, and number of pods used, you get different amounts of storage and higher or lower latency and throughput. Be sure to choose an appropriate pod type and size for your dataset and workload.

### Pod types

Different pod types are priced differently. See Understanding cost for more details.

Once a pod-based index is created, you cannot change its pod type. However, you can create a collection from an index and then create a new index with a different pod type from the collection.

#### s1 pods

These storage-optimized pods provide large storage capacity and lower overall costs with slightly higher query latencies than p1 pods. They are ideal for very large indexes with moderate or relaxed latency requirements.

Each s1 pod has enough capacity for around 5M vectors of 768 dimensions.

#### p1 pods

These performance-optimized pods provide very low query latencies, but hold fewer vectors per pod than s1 pods. They are ideal for applications with low latency requirements (<100ms).

Each p1 pod has enough capacity for around 1M vectors of 768 dimensions.

#### p2 pods

The p2 pod type provides greater query throughput with lower latency. For vectors with fewer than 128 dimension and queries where topK is less than 50, p2 pods support up to 200 QPS per replica and return queries in less than 10ms. This means that query throughput and latency are better than s1 and p1.

Each p2 pod has enough capacity for around 1M vectors of 768 dimensions. However, capacity may vary with dimensionality.

The data ingestion rate for p2 pods is significantly slower than for p1 pods; this rate decreases as the number of dimensions increases. For example, a p2 pod containing vectors with 128 dimensions can upsert up to 300 updates per second; a p2 pod containing vectors with 768 dimensions or more supports upsert of 50 updates per second. Because query latency and throughput for p2 pods vary from p1 pods, test p2 pod performance with your dataset.

The p2 pod type does not support sparse vector values.

### Pod size and performance

Each pod type supports four pod sizes: x1, x2, x4, and x8. Your index storage and compute capacity doubles for each size step. The default pod size is x1. You can increase the size of a pod after index creation.

To learn about changing the pod size of an index, see Configure an index.

### Pod environments

When creating a pod-based index, you must choose the cloud environment where you want the index to be hosted. The project environment can affect your pricing. The following table lists the available cloud regions and the corresponding values of the environment parameter for the create_index operation:

| Cloud | Region | Environment |
|-------|--------|-------------|
| GCP | us-west-1 (N. California) | us-west1-gcp |
| GCP | us-central-1 (Iowa) | us-central1-gcp |
| GCP | us-west-4 (Las Vegas) | us-west4-gcp |
| GCP | us-east-4 (Virginia) | us-east4-gcp |
| GCP | northamerica-northeast-1 | northamerica-northeast1-gcp |
| GCP | asia-northeast-1 (Japan) | asia-northeast1-gcp |
| GCP | asia-southeast-1 (Singapore) | asia-southeast1-gcp |
| GCP | us-east-1 (South Carolina) | us-east1-gcp |
| GCP | eu-west-1 (Belgium) | eu-west1-gcp |
| GCP | eu-west-4 (Netherlands) | eu-west4-gcp |
| AWS | us-east-1 (Virginia) | us-east-1-aws |
| Azure | eastus (Virginia) | eastus-azure |

Contact us if you need a dedicated deployment in other regions.

The environment cannot be changed after the index is created.

## Distance metrics

When creating an index, you can choose from the following similarity metrics. For the most accurate results, choose the similarity metric used to train the embedding model for your vectors. For more information, see Vector Similarity Explained

### euclidean

Querying indexes with this metric returns a similarity score equal to the squared Euclidean distance between the result and query vectors.

This metric calculates the square of the distance between two data points in a plane. It is one of the most commonly used distance metrics. For an example, see our [IT threat detection example](https://docs.pinecone.io/docs/it-threat-detection).

When you use `metric='euclidean'`, the most similar results are those with the lowest similarity score.

### cosine

This is often used to find similarities between different documents. The advantage is that the scores are normalized to [-1,1] range. For an example, see our [generative question answering example](https://docs.pinecone.io/docs/generative-qa).

### dotproduct

This is used to multiply two vectors. You can use it to tell us how similar the two vectors are. The more positive the answer is, the closer the two vectors are in terms of their directions. For an example, see our [semantic search example](https://docs.pinecone.io/docs/semantic-search).

# Pinecone API's

The Pinecone REST APIs let you interact programmatically with your Pinecone account using HTTP requests.

## APIs

Pinecone provides three distinct APIs:

1. **Database API**: Use the Database API to store and query records in the Pinecone vector database. This API is comprised of data plane and control plane endpoints.

   The data plane handles requests to write and read records in indexes. Indexes are partitioned into one or more logical namespaces, and all write and read requests are scoped by namespace.

   For each index, there is a unique URL for performing data plane operations in the form of `https://{index_host}/{operation}`. The Pinecone SDKs construct these URLs for you. However, when using the API directly, you must explicitly specify them.

   You can get index URLs in the Pinecone console or using the describe_index operation. For more details, see [Get an index endpoint](https://docs.pinecone.io/docs/get-an-index-endpoint).

   The control plane handles requests to create and manage indexes and collections.

   The global URL for all control plane operations is `https://api.pinecone.io`. You use this URL regardless of the cloud environment where an index is hosted.

2. **Inference API**: Use the Inference API to generate embeddings for text data such as queries or passages and to rerank items like text documents.

3. **Assistant API**: Use the Assistant API to upload documents, ask questions, and receive responses that reference your documents. This is known as retrieval-augmented generation (RAG).

## Authentication

All HTTP requests to Pinecone APIs must contain an `Api-Key` header that specifies a valid API key and must be encoded as JSON with the `Content-Type: application/json` header.

```javascript
import { Pinecone } from '@pinecone-database/pinecone';

const pc = new Pinecone({
    apiKey: 'YOUR_API_KEY' 
});

// Creates an index using the API key stored in the client 'pc'.
await pc.createIndex({
    name: 'example-index',
    dimension: 1536,
    metric: 'cosine',
    spec: { 
        serverless: { 
            cloud: 'aws', 
            region: 'us-east-1' 
        }
    } 
}) 
```

## Response codes

When Pinecone receives a request to an API endpoint, a number of different HTTP status codes can be returned in the response depending on the original request. In general, codes in the 2xx range indicate success, codes in the 4xx range indicate an error that failed given the information provided, and codes in the 5xx range indicate an error with Pinecone's servers.

| HTTP status code | Description |
|-------------------|-------------|
| 200 - OK | The request was successful. |
| 201 - CREATED | The request was successful and a new resource was created. |
| 202 - NO CONTENT | The request was successful, but there is no content to return. |
| 400 - INVALID ARGUMENT | The request was unsuccessful due to an invalid argument. |
| 401 - UNAUTHENTICATED | The API key is missing or invalid. |
| 403 - QUOTA_EXCEEDED | The request was unsuccessful due to an exceeded quota. |
| 404 - NOT FOUND | The request was unsuccessful because the resource was not found. |
| 409 - ALREADY EXISTS | The request was unsuccessful because the resource already exists. |
| 412 - FAILED PRECONDITIONS | The request was unsuccessful due to preconditions not being met. |
| 422 - UNPROCESSABLE ENTITY | The request was unsuccessful because the server was unable to process the contained instructions. |
| 429 - TOO MANY REQUESTS | The request was rate-limited. |
| 500 - UNKNOWN | An internal server error occurred. |

## API versioning

Pinecone's Database and Inference APIs are versioned to ensure that your applications continue to work as expected as the platform evolves. Versions are named by release date in the format YYYY-MM, for example, 2024-10.

The Pinecone Assistant API is not versioned. Pinecone Assistant in public preview and is not recommended for production usage.

### Release schedule

On a quarterly basis, Pinecone releases a new stable API version as well as a release candidate of the next stable version.

- **Stable**: Each stable version remains unchanged and supported for a minimum of 12 months. Since stable versions are released every 3 months, this means you have at least 9 months to test and migrate your app to the newest stable version before support for the previous version is removed.

- **Release candidate**: The release candidate gives you insight into the upcoming changes in the next stable version. It is available for approximately 3 months before the release of the stable version and can include new features, improvements, and breaking changes.

Below is an example of Pinecone's release schedule:

[Image placeholder for release schedule]

### Specifying an API version

When using the API directly, it is important to specify an API version in your requests. If you don't, requests default to the oldest supported stable version. Once support for that version ends, your requests will default to the next oldest stable version, which could include breaking changes that require you to update your integration.

To specify an API version, set the `X-Pinecone-API-Version` header to the version name.

For example, based on the version support diagram above, if it is currently July 2024 and you want to use the latest stable version to describe an index, you would set `"X-Pinecone-API-Version: 2024-07"`:

```bash
PINECONE_API_KEY = "YOUR_API_KEY"

curl -i -X GET "https://api.pinecone.io/indexes/movie-recommendations" \
    -H "Api-Key: $PINECONE_API_KEY" \
    -H "X-Pinecone-API-Version: 2024-07"
```

If you want to use the release candidate of the next stable version instead, you would set `"X-Pinecone-API-Version: 2024-10"`:

```bash
PINECONE_API_KEY = "YOUR_API_KEY"

curl -i -X GET "https://api.pinecone.io/indexes/movie-recommendations" \
    -H "Api-Key: $PINECONE_API_KEY" \
    -H "X-Pinecone-API-Version: 2024-10"
```

### SDK versions

Official Pinecone SDKs provide convenient access to Pinecone APIs. SDK versions are pinned to specific API versions. When a new API version is released, a new version of the SDK is also released.

The mappings between API versions and SDK versions are as follows:

| SDK | 2024-04 | 2024-07 |
|-----|---------|---------|
| Python SDK | v4.x | v5.x |
| Node.js SDK | v2.x | v3.x |
| Java SDK | v1.x | v2.x |
| Go SDK | v0.x | v1.x |

### Breaking changes

Breaking changes are changes that can potentially break your integration with a Pinecone API. Breaking changes include:

- Removing an entire operation
- Removing or renaming a parameter
- Removing or renaming a response field
- Adding a new required parameter
- Making a previously optional parameter required
- Changing the type of a parameter or response field
- Removing enum values
- Adding a new validation rule to an existing parameter
- Changing authentication or authorization requirements

### Non-breaking changes

Non-breaking changes are additive and should not break your integration. Additive changes include:

- Adding an operation
- Adding an optional parameter
- Adding an optional request header
- Adding a response field
- Adding a response header
- Adding enum values

### Getting updates

To ensure you always know about upcoming API changes, follow the [Release notes](https://docs.pinecone.io/docs/release-notes).

## Upsert vectors

The upsert operation writes vectors into a namespace. If a new value is upserted for an existing vector ID, it will overwrite the previous value.

```javascript
// npm install @pinecone-database/pinecone
import { Pinecone } from '@pinecone-database/pinecone'

const pc = new Pinecone({ apiKey: "YOUR_API_KEY" })
const index = pc.index("example-index")

await index.namespace('example-namespace').upsert([
  {
    id: 'vec1',
    values: [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8],
    sparseValues: {
        indices: [1, 5],
        values: [0.5, 0.5]
    },
    metadata: {'genre': 'drama'},
  },
  {
    id: 'vec2',
    values: [0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9],
    metadata: {'genre': 'action'},
    sparseValues: {
        indices: [5, 6],
        values: [0.4, 0.5]
    }
  }
])
```

Response:

```json
{
  "upsertedCount": 2
}
```

### Authorizations

- Api-Key
  - Type: string
  - Location: header
  - Required: Yes
  - Description: An API Key is required to call Pinecone APIs. Get yours from the console.

### Body

- Content-Type: application/json
- vectors
  - Type: object[]
  - Required: Yes
  - Description: An array containing the vectors to upsert. Recommended batch limit is 100 vectors.
- namespace
  - Type: string
  - Description: The namespace where you upsert vectors.

### Response

- Status: 200
- Content-Type: application/json
- upsertedCount
  - Type: integer
  - Description: The number of vectors upserted.

## Query vectors

The query operation searches a namespace, using a query vector. It retrieves the ids of the most similar items in a namespace, along with their similarity scores.

```javascript
// npm install @pinecone-database/pinecone
import { Pinecone } from '@pinecone-database/pinecone'

const pc = new Pinecone({ apiKey: 'YOUR_API_KEY' })
const index = pc.index("pinecone-index")

const queryResponse = await index.namespace('example-namespace').query({
    vector: [0.3, 0.3, 0.3, 0.3, 0.3, 0.3, 0.3, 0.3],
    filter: {
      'genre': {'$eq': 'documentary'}
    },
    topK: 3,
    includeValues: true
});
```

Response:

```json
{
  "matches":[
    {
      "id": "vec3",
      "score": 0,
      "values": [0.3,0.3,0.3,0.3,0.3,0.3,0.3,0.3]
    },
    {
      "id": "vec2",
      "score": 0.0800000429,
      "values": [0.2, 0.2, 0.2, 0.2, 0.2, 0.2, 0.2, 0.2]
    },
    {
      "id": "vec4",
      "score": 0.0799999237,
      "values": [0.4, 0.4, 0.4, 0.4, 0.4, 0.4, 0.4, 0.4]
    }
  ],
  "namespace": "example-namespace",
  "usage": {"read_units": 6}
}
```

### Authorizations

- Api-Key
  - Type: string
  - Location: header
  - Required: Yes
  - Description: An API Key is required to call Pinecone APIs. Get yours from the console.

### Body

- Content-Type: application/json
- namespace
  - Type: string
  - Description: The namespace to query.
- topK
  - Type: integer
  - Required: Yes
  - Description: The number of results to return for each query.
- filter
  - Type: object
  - Description: The filter to apply. You can use vector metadata to limit your search. See Filter with metadata.
- includeValues
  - Type: boolean
  - Default: false
  - Description: Indicates whether vector values are included in the response.
- includeMetadata
  - Type: boolean
  - Default: false
  - Description: Indicates whether metadata is included in the response as well as the ids.
- vector
  - Type: number[]
  - Description: The query vector. This should be the same length as the dimension of the index being queried. Each query() request can contain only one of the parameters id or vector.
- sparseVector
  - Type: object
  - Description: Vector sparse data. Represented as a list of indices and a list of corresponded values, which must be with the same length.
- id
  - Type: string
  - Description: The unique ID of the vector to be used as a query vector. Each query() request can contain only one of the parameters queries, vector, or id.

### Response

- Status: 200
- Content-Type: application/json
- matches
  - Type: object[]
  - Description: The matches for the vectors.
- namespace
  - Type: string
  - Description: The namespace for the vectors.
- usage
  - Type: object
  - readUnits
    - Type: integer
    - Description: The number of read units consumed by this operation.

## Fetch vectors

The fetch operation looks up and returns vectors, by ID, from a single namespace. The returned vectors include the vector data and/or metadata.

```javascript
// npm install @pinecone-database/pinecone
import { Pinecone } from '@pinecone-database/pinecone'

const pc = new Pinecone({ apiKey: 'YOUR_API_KEY' })
const index = pc.index('example-index')

const fetchResult = await index.namespace('example-namespace').fetch(['id-1', 'id-2']);
```

Example response:

```json
{
  "namespace": "example-namespace",
  "usage": {
    "readUnits": 1
  },
  "vectors": {
    "id-1": {
      "id": "id-1",
      "values": [
        1,
        1.5
      ]
    },
    "id-2": {
      "id": "id-2",
      "values": [
        2,
        1
      ]
    }
  }
}
```

### Authorizations

| Parameter | Type | Location | Required | Description |
|-----------|------|----------|----------|-------------|
| Api-Key | string | header | Yes | An API Key is required to call Pinecone APIs. Get yours from the console. |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| ids | string[] | Yes | The vector IDs to fetch. Does not accept values containing spaces. |
| namespace | string | No | - |

### Response

#### 200 - application/json

- **vectors**: object
  - **vectors.{key}**: object
    - (Child attributes hidden for brevity)
- **namespace**: string
  - The namespace of the vectors.
- **usage**: object
  - **usage.readUnits**: integer
    - The number of read units consumed by this operation.

## Update a vector

The update operation updates a vector in a namespace. If a value is included, it will overwrite the previous value. If a set_metadata is included, the values of the fields specified in it will be added or overwrite the previous value.

```javascript
// npm install @pinecone-database/pinecone
import { Pinecone } from '@pinecone-database/pinecone'

const pc = new Pinecone({ apiKey: "YOUR_API_KEY" })
const index = pc.index("example-index")

await index.namespace('example-namespace').update({
  id: 'id-3',
  values: [4.0, 2.0],
  metadata: {
    genre: "comedy",
  },
});
```

### Response

| Status | Content-Type     | Body |
|--------|------------------|------|
| 200    | application/json | {}   |

### Authorizations

| Name    | Type   | Location | Required | Description                                                           |
|---------|--------|----------|----------|-----------------------------------------------------------------------|
| Api-Key | string | header   | Yes      | An API Key is required to call Pinecone APIs. Get yours from the console. |

### Body

Content-Type: application/json

- `id` (string, required): Vector's unique id.
- `values` (number[]): Vector data.
- `sparseValues` (object): Vector sparse data. Represented as a list of indices and a list of corresponded values, which must be with the same length.
  - `indices` (integer[], required): The indices of the sparse data.
  - `values` (number[], required): The corresponding values of the sparse data, which must be with the same length as the indices.
- `setMetadata` (object): Metadata to set for the vector.
- `namespace` (string): The namespace containing the vector to update.

### Response

| Status | Content-Type     | Description                           |
|--------|------------------|---------------------------------------|
| 200    | application/json | The response for the update operation |


## Delete vectors

The delete operation deletes vectors, by id, from a single namespace.

```python
# pip install pinecone-client[grpc]
from pinecone.grpc import PineconeGRPC as Pinecone

pc = Pinecone(api_key="YOUR_API_KEY")
index = pc.Index("example-index")

index.delete(ids=["id-1", "id-2"], namespace='example-namespace')
```

Response:
```json
{}
```

### Authorizations

| Parameter | Type | Location | Required | Description |
|-----------|------|----------|----------|-------------|
| Api-Key   | string | header | Yes | An API Key is required to call Pinecone APIs. Get yours from the console. |

### Body

Content-Type: `application/json`

- **ids** (string[]): Vectors to delete.

- **deleteAll** (boolean):
  - Default: `false`
  - This indicates that all vectors in the index namespace should be deleted.

- **namespace** (string): The namespace to delete vectors from, if applicable.

- **filter** (object): If specified, the metadata filter here will be used to select the vectors to delete. This is mutually exclusive with specifying ids to delete in the `ids` param or using `delete_all=True`. See [Filter with metadata](). Serverless indexes do not support delete by metadata. Instead, you can use the list operation to fetch the vector IDs based on their common ID prefix and then delete the records by ID.

### Response

**200 - application/json**

The response for the Delete operation.

## List vector IDs

The list operation lists the IDs of vectors in a single namespace of a serverless index. An optional prefix can be passed to limit the results to IDs with a common prefix.

list returns up to 100 IDs at a time by default in sorted order (bitwise "C" collation). If the limit parameter is set, list returns up to that number of IDs instead. Whenever there are additional IDs to return, the response also includes a pagination_token that you can use to get the next batch of IDs. When the response does not include a pagination_token, there are no more IDs to return.

```javascript
// npm install @pinecone-database/pinecone
import { Pinecone } from '@pinecone-database/pinecone';
const pc = new Pinecone();

const index = pc.index('my-index').namespace('example-namespace');

const results = await index.listPaginated({ prefix: 'document1#' });
console.log(results);

// Fetch the next page of results
await index.listPaginated({ prefix: 'document1#', paginationToken: results.pagination.next});
```

Example response:

```json
{
  "vectors": [
    {
      "id": "document1#abb"
    },
    {
      "id": "document1#abc"
    }
  ],
  "pagination": {
    "next": "Tm90aGluZyB0byBzZWUgaGVyZQo="
  },
  "namespace": "example-namespace",
  "usage": {
    "readUnits": 5
  }
}
```

**Note:** list is supported only for serverless indexes.

### API Details

**GET /vectors/list**

#### Authorizations

- **Api-Key**: string (header, required)
  - An API Key is required to call Pinecone APIs. Get yours from the console.

#### Query Parameters

- **prefix**: string
  - The vector IDs to fetch. Does not accept values containing spaces.
- **limit**: integer (default: 100)
  - Max number of IDs to return per page.
- **paginationToken**: string
  - Pagination token to continue a previous listing operation.
- **namespace**: string

#### Response

**200 - application/json**

- **vectors**: object[]
  - **id**: string
- **pagination**: object
  - **next**: string
- **namespace**: string
  - The namespace of the vectors.
- **usage**: object
  - **readUnits**: integer
    - The number of read units consumed by this operation.

## Get index stats

The `describe_index_stats` operation returns statistics about the contents of an index, including the vector count per namespace, the number of dimensions, and the index fullness.

Serverless indexes scale automatically as needed, so index fullness is relevant only for pod-based indexes.

For pod-based indexes, the index fullness result may be inaccurate during pod resizing; to get the status of a pod resizing process, use `describe_index`.

```javascript
// npm install @pinecone-database/pinecone
import { Pinecone } from '@pinecone-database/pinecone'

const pc = new Pinecone({ apiKey: 'YOUR_API_KEY' })
const index = pc.index('pinecone-index')

const stats = await index.describeIndexStats();
```

```json
{
  "dimension": 1024,
  "index_fullness": 0.4,
  "namespaces": {
    "": {
      "vectorCount": 50000
    },
    "example-namespace-2": {
      "vectorCount": 30000
    }
  }
}
```

### Authorizations

| Parameter | Type | Location | Required | Description |
|-----------|------|----------|----------|-------------|
| Api-Key | string | header | required | An API Key is required to call Pinecone APIs. Get yours from the console. |

### Body

- Content-Type: application/json
- `filter` (object): If this parameter is present, the operation only returns statistics for vectors that satisfy the filter. See [Filter with metadata](https://docs.pinecone.io/docs/metadata-filtering).

**Note:** Serverless indexes do not support filtering `describe_index_stats` by metadata.

### Response

**200 - application/json**

- `namespaces` (object): A mapping for each namespace in the index from the namespace name to a summary of its contents. If a metadata filter expression is present, the summary will reflect only vectors matching that expression.
  - `namespaces.{key}` (object): A summary of the contents of a namespace.

- `dimension` (integer): The dimension of the indexed vectors.

- `indexFullness` (number): The fullness of the index, regardless of whether a metadata filter expression was passed. The granularity of this metric is 10%.

  **Note:** Serverless indexes scale automatically as needed, so index fullness is relevant only for pod-based indexes.

  The index fullness result may be inaccurate during pod resizing; to get the status of a pod resizing process, use `describe_index`.

- `totalVectorCount` (integer): The total number of vectors in the index, regardless of whether a metadata filter expression was passed.
