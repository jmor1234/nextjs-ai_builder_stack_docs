# Exa Docs

## What is Exa?

Exa finds the exact content you're looking for on the web using embeddings-based search. Exa has three core functionalities, surfaced through our API endpoints:

1. **Search for pages**: Find any pages on the web using a natural language query. If you still need it, Exa supports also supports Google-style keyword search.

2. **Get contents from pages**: Obtain clean, up-to-date, parsed HTML from Exa search results. Contents can be semantically targeted using our 'highlights' feature.

3. **Find similar pages**: Based on a link, find and return pages that are similar in meaning.

Exa's precise content retrieval supercharges RAG pipelines, automates hours of research, and creates high-quality datasets for your niche use-case.

## How Exa Search Works

### Describing how Exa search works

Exa is a novel search engine that utilizes the latest advancements in AI language processing to return the best possible results.

### Introducing neural searches via 'next-link prediction'

At Exa, we've built our very own index of high quality web content, and have trained a model to query this index powered by the same embeddings-based technology that makes modern LLMs so powerful.

By using embeddings, we move beyond keyword searches to use 'next-link prediction', understanding the semantic content of queries and indexed documents. This method predicts which web links are most relevant based on the semantic meaning, not just direct word matches.

By doing this, our model anticipates the most relevant links by understanding complex queries, including indirect or thematic relationships. This approach is especially effective for exploratory searches, where precise terms may be unknown, or where queries demand many, often semantically dense, layered filters.

### Combining neural and keyword - the best of both worlds through Exa Auto Search

Sometimes keyword search is the best way to query the web - for instance, you may have a specific word or piece of jargon that you want to match explicitly with results (often the case with proper nouns like place-names). In these cases, semantic searches are not the most useful.

To ensure our engine is comprehensive, we have built keyword search in parallel to our novel neural search capability. This means Exa is an 'all-in-one' search solution, no matter what your query needs are.

Lastly, we surface both query archetypes through 'Auto Search', to give users the best of both worlds - we have built a small categorization model that understands your query, our search infrastructure and therefore routes your particular query to the best matched search type.

See [here](#) for the way we set Auto Search in a simple Python example. Type has options neural, keyword or auto.

## The Exa Index

We spend a lot of time and energy creating a high quality, curated index. There are many types of content, and we're constantly discovering new things to search for as well. If there's anything you want to be more highly covered, just reach out to hello@exa.ai. See the following table for a high level overview of what is available in our index:

| Category | Availability in Exa Index | Description | Example prompt link |
|----------|---------------------------|-------------|---------------------|
| Research papers | Very High | Offer semantic search over a very vast index of papers, enabling sophisticated, multi-layer and complex filtering for use cases | If you're looking for the most helpful academic paper on "embeddings for document retrieval", check this out (pdf: |
| Personal pages | Very High | Excels at finding personal pages, which are often extremely hard/impossible to find on services like Google | Here is a link to the best life coach for when you're unhappy at work: |
| Wikipedia | Very High | Covers all of Wikipedia, providing comprehensive access to this vast knowledge base via semantic search | Here is a Wikipedia page about a Roman emperor: |
| News | Very High | Includes a wide, robust index of web news sources, providing coverage of current events | Here is news about war in the Middle East: |
| LinkedIn personal profiles | Coming Soon | Will provide extensive coverage of LinkedIn personal profiles, allowing for detailed professional information searches | (Best-practice example TBC) |
| LinkedIn company pages | Coming Soon | Will offer comprehensive access to LinkedIn company pages, enabling in-depth research on businesses and organization | (Best-practice example TBC) |
| Company home-pages | Very High | Wide index of companies covered; also available are curated, customized company datasets - reach out to learn more | Here is the homepage of a company working on making space travel cheaper: |
| GitHub repos | High | Indexes open source code (which the Exa team use frequently!) | Here's a Github repo if you want to convert OpenAPI specs to Rust code: |
| Blogs | High | Excels at finding high quality reading material, particularly useful for niche topics | If you're a huge fan of Japandi decor, you'd love this blog: |
| Places and things | High | Covers a wide range of entities including hospitals, schools, restaurants, appliances, and electronics | Here is a high-rated Italian restaurant in downtown Chicago: |
| Legal and policy sources | High | Strong coverage of legal and policy information, (e.g., including sources like CPUC, Justia, Findlaw, etc.) | Here is a common law case in california on marital property rights: |
| Financial information | High | Includes sources like Yahoo Finance, with some results containing detailed financial data | Here is a source on Apple's revenue growth rate over the past years: |
| Government and international organization sources | High | Includes content from sources like the IMF and CDC amongst others | Here is a recent World Health Organization site on global vaccination rates: |
| Events | Moderate | Reasonable coverage of events in major municipalities, suggesting room for improvement | Here is an AI hackathon in SF: |
| Jobs | Moderate | Can find some job listings | If you're looking for a software engineering job at a small startup working on an important mission, check out |

# FAQs

### What is Exa?

Exa is a new search engine offering both proprietary neural search and industry-standard keyword search. It excels in finding precise web content, retrieving clean/rich web content, and can even identify similar pages based on input URLs. These technologies make Exa ideal for enhancing RAG pipelines, automating research, and creating niche datasets.

### What's different about Exa's Neural Search?

Exa uses a transformer-based model to understand your query and return the most relevant links. Exa has embedded large portions of the web, allowing you to make extremely specific and complex queries, and get only the highest quality results.

### How is Neural Search different from Google?

Google search is mostly keyword-based, matching query words to webpage words. For example, a Google search for "companies working on AI for finance" typically returns links like "Top 10 companies developing AI for financial services". In contrast, Exa's neural search understands meaning, returning actual company URLs. Additionally, Exa's results are not influenced by SEO, unlike Google/other engines, which can be affected by optimized content. This allows Exa to provide more precise and relevant results based on the query's intent rather than by keywords alone.

### How is Exa different from LLMs?

Exa is a new search engine built from the ground up. LLMs are models built to predict the next piece of text. Exa predicts specific links on the web given their relevance to a query. LLMs have intelligence, and are getting smarter over time as new models are trained. Exa connects these intelligences to the web.

### How can Exa be used in an LLM?

Exa enhances LLMs by supplying high-quality, relevant web content, minimizing hallucination and outdated responses. An LLM can take a user's query, use Exa to find pertinent web content, and generate answers based on reliable, up-to-date information.

### How does Exa compare to other search APIs?

Exa.ai offers unique capabilities:

- **Neural Search Technology**: Uses transformers for semantic understanding, handling complex queries based on meaning.
- **Natural Language Queries**: Processes and understands natural language queries for more accurate results.
- **Instant Content Retrieval**: Instantly returns clean and parsed content for any page in its index.
- **Large-scale Searches**: Capable of returning thousands of results for automatic processing, ideal for batch use cases.
- **Content Highlights**: Extracts relevant excerpts or highlights from retrieved content for targeted information.
- **Optimized for AI Applications**: Specifically designed for enhancing AI models, chatbots, and research automation.
- **Auto Search**: Automatically selects the best search type (neural or keyword) based on the query for optimal results.

### How often is the index updated?

We update our index every two minutes, and are constantly adding batches of new links. We target the highest quality web pages. Our clients oftentimes request specific domains to be more deeply covered - if there is a use-case we can unlock by additional domain coverage in our index, please contact us.

### What's our roadmap?

- The ability to create arbitrary custom datasets by powerfully searching over our index
- Support arbitrary non-neural filters
- Build a (much) larger index
- Solve search. No, really.

### How does similarity search work?

When you search using a URL, Exa crawls the URL, parses the main content from the HTML, and searches the index with that parsed content.

The model chooses webpages which it predicts are talked about in similar ways to the prompt URL. That means the model considers a range of factors about the page, including the text style, the domain, and the main ideas inside the text.

Similarity search is natural extension for a neural search engine like Exa, and something that's difficult with keyword search engines like google.

### What security measures does Exa take?

We have robust policies and everything we do is either in standard cloud services, or built in house (e.g., we have our own vector database that we serve in house, our own GPU cluster, our own query model and our own SERP solution). In addition to this, we can offer unique security arrangements like zero data retention as part of a custom enterprise agreement just chat to us!

# Getting Started with TypeScript

Doing your first Exa search with our TypeScript SDK

## Exa TypeScript SDK Documentation

1. **Create an account and grab an API key**
   First generate and grab an API key for Exa here:

   [Get API Key](https://exa.ai/api-key)

2. **Install the SDK**
   ```shell
   npm install exa-js
   ```

3. **Instantiate the client**
   ```typescript
   import Exa from 'exa-js';

   const exa = new Exa(process.env.EXA_API_KEY);
   ```

4. **Make a search using the searchAndContents method**
   ```typescript
   const result = await exa.searchAndContents(
     "hottest AI startups",
     {
       type: "neural",
       useAutoprompt: true,
       numResults: 10,
       text: true,
     }
   );
   console.log(result);
   ```

   Example output:
   ```json
   {
     "autopromptString": "A top AI startup to watch is:",
     "results": [
       {
         "score": 0.20460350811481476,
         "title": "OpenAI",
         "id": "https://openai.com/",
         "url": "https://openai.com/",
         "publishedDate": "2021-06-18",
         "author": "OpenAI",
         "text": "Creating safe AGI that benefits all of humanity Pioneering research on the path to AGI Transforming work and creativity with AI Join us in shaping the future of technology Safety & responsibility Our work to create safe and beneficial AI requires a deep understanding of the potential risks and benefits, as well as careful consideration of the impact. Learn about safety Research We research generative models and how to align them with human values. Learn about our research GPT-4 March 14, 2023 Forecasting potential misuses of language models for disinformation campaigns and how to reduce risk January 11, 2023 Point-E: A system for generating 3D point clouds from complex prompts December 16, 2022 Introducing Whisper September 21, 2022 Products Our API platform offers our latest models and guides for safety best practices. Explore our products New and improved embedding model December 15, 2022 DALLÂ·E now available without waitlist September 28, 2022 New and improved content moderation tooling August 10, 2022 New GPT-3 capabilities: Edit & insert March 15, 2022 Careers at OpenAI Developing safe and beneficial AI requires people from a wide range of disciplines and backgrounds. View careers I encourage my team to keep learning. Ideas in different topics or fields can often inspire new ideas and broaden the potential solution space. Lilian WengApplied AI at OpenAI"
       },
       // ... (other results omitted for brevity)
     ],
     "origQuery": "A top AI startup to watch is:",
     "requestId": "26f30c5ab664c2faf3be3cc608b4509a"
   }
   ```
# TypeScript SDK Specification

## Getting started

### Installing the exa-js SDK

npm:
```bash
npm install exa-js
```

pnpm:
```bash
pnpm install exa-js
```

and then instantiate an Exa client:

```typescript
import Exa from 'exa-js';

const exa = new Exa(process.env.EXA_API_KEY);
```

## search Method

Perform an Exa search given an input query and retrieve a list of relevant results as links.

### Input Example

```typescript
const result = await exa.search(
  "hottest AI startups",
  {
    useAutoprompt: true,
    numResults: 2
  }
);
```

### Input Parameters

| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| query | string | The input query string. | Required |
| numResults | number | Number of search results to return. | 10 |
| includeDomains | string[] | List of domains to include in the search. | undefined |
| excludeDomains | string[] | List of domains to exclude in the search. | undefined |
| startCrawlDate | string | Results will only include links crawled after this date. | undefined |
| endCrawlDate | string | Results will only include links crawled before this date. | undefined |
| startPublishedDate | string | Results will only include links with a published date after this date. | undefined |
| endPublishedDate | string | Results will only include links with a published date before this date. | undefined |
| useAutoprompt | boolean | If true, convert query to a query best suited for Exa. | false |
| type | string | The type of search, "keyword" or "neural". | "auto" |
| category | string | A data category to focus on when searching, with higher comprehensivity and data cleanliness. Available categories: "company", "research paper", "news", "github", "tweet", "movie", "song", "personal site", "pdf". | undefined |

### Returns Example

```json
{
  "autopromptString": "Here is a link to one of the hottest AI startups:",
  "results": [
    {
      "score": 0.17025552690029144,
      "title": "Adept: Useful General Intelligence",
      "id": "https://www.adept.ai/",
      "url": "https://www.adept.ai/",
      "publishedDate": "2000-01-01",
      "author": null
    },
    {
      "score": 0.1700288951396942,
      "title": "Home | Tenyx, Inc.",
      "id": "https://www.tenyx.com/",
      "url": "https://www.tenyx.com/",
      "publishedDate": "2019-09-10",
      "author": null
    }
  ]
}
```

### Return Parameters

#### SearchResponse

| Field | Type | Description |
|-------|------|-------------|
| results | Result[] | List of Result objects |
| autopromptString? | string | Exa query created by autoprompt functionality |

#### Result Object

| Field | Type | Description |
|-------|------|-------------|
| url | string | URL of the search result |
| id | string | Temporary ID for the document |
| title | string \| null | Title of the search result |
| score? | number | Similarity score between query/url and result |
| publishedDate? | string | Estimated creation date |
| author? | string | Author of the content, if available |

## searchAndContents Method

Perform an Exa search given an input query and retrieve a list of relevant results as links, optionally including the full text and/or highlights of the content.

### Input Example

```typescript
// Search with full text content
const resultWithText = await exa.searchAndContents(
  "AI in healthcare",
  {
    text: true,
    numResults: 2
  }
);

// Search with highlights
const resultWithHighlights = await exa.searchAndContents(
  "AI in healthcare",
  {
    highlights: true,
    numResults: 2
  }
);

// Search with both text and highlights
const resultWithTextAndHighlights = await exa.searchAndContents(
  "AI in healthcare",
  {
    text: true,
    highlights: true,
    numResults: 2
  }
);
```

### Input Parameters

| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| query | string | The input query string. | undefined |
| text | boolean | If provided, includes the full text of the content in the results. | undefined |
| highlights | boolean | If provided, includes highlights of the content in the results. | undefined |
| numResults | number | Number of search results to return. | 10 |
| includeDomains | string[] | List of domains to include in the search. | undefined |
| excludeDomains | string[] | List of domains to exclude in the search. | undefined |
| startCrawlDate | string | Results will only include links crawled after this date. | undefined |
| endCrawlDate | string | Results will only include links crawled before this date. | undefined |
| startPublishedDate | string | Results will only include links with a published date after this date. | undefined |
| endPublishedDate | string | Results will only include links with a published date before this date. | undefined |
| useAutoprompt | boolean | If true, convert query to a query best suited for Exa. | false |
| type | string | The type of search, "keyword" or "neural". | "auto" |
| category | string | A data category to focus on when searching, with higher comprehensivity and data cleanliness. Available categories: "company", "research paper", "news", "github", "tweet", "movie", "song", "personal site", "pdf". | undefined |

### Returns Example

```json
{
  "results": [
    {
      "score": 0.20826785266399384,
      "title": "2023 AI Trends in Health Care",
      "id": "https://aibusiness.com/verticals/2023-ai-trends-in-health-care-",
      "url": "https://aibusiness.com/verticals/2023-ai-trends-in-health-care-",
      "publishedDate": "2022-12-29",
      "author": "Wylie Wong",
      "text": "While the health care industry was initially slow to [... TRUNCATED FOR BREVITY ...]",
      "highlights": [
        "But to do so, many health care institutions would like to share data, so they can build a more comprehensive dataset to use to train an AI model. Traditionally, they would have to move the data to one central repository. However, with federated or swarm learning, the data does not have to move. Instead, the AI model goes to each individual health care facility and trains on the data, he said. This way, health care providers can maintain security and governance over their data."
      ],
      "highlightScores": [
        0.5566554069519043
      ]
    },
    {
      "score": 0.20796334743499756,
      "title": "AI in healthcare: Innovative use cases and applications",
      "id": "https://www.leewayhertz.com/ai-use-cases-in-healthcare",
      "url": "https://www.leewayhertz.com/ai-use-cases-in-healthcare",
      "publishedDate": "2023-02-13",
      "author": "Akash Takyar",
      "text": "The integration of AI in healthcare is not [... TRUNCATED FOR BREVITY ...]",
      "highlights": [
        "The ability of AI to analyze large amounts of medical data and identify patterns has led to more accurate and timely diagnoses. This has been especially helpful in identifying complex medical conditions, which may be difficult to detect using traditional methods. Here are some examples of successful implementation of AI in healthcare. IBM Watson Health: IBM Watson Health is an AI-powered system used in healthcare to improve patient care and outcomes. The system uses natural language processing and machine learning to analyze large amounts of data and provide personalized treatment plans for patients."
      ],
      "highlightScores": [
        0.6563674807548523
      ]
    }
  ]
}
```

### Return Parameters

#### SearchResponse

| Field | Type | Description |
|-------|------|-------------|
| results | SearchResult<T>[] | List of SearchResult objects |
| autopromptString? | string | Exa query created by autoprompt functionality |

#### SearchResult

Extends the Result object from the search method with additional fields based on T:

| Field | Type | Description |
|-------|------|-------------|
| text? | string | Text of the search result page (if requested) |
| highlights? | string[] | Highlights of the search result (if requested) |
| highlightScores? | number[] | Scores of the highlights (if requested) |

Note: The actual fields present in the SearchResult<T> object depend on the options provided in the searchAndContents call.

## findSimilar Method

Find a list of similar results based on a webpage's URL.

### Input Example

```typescript
const similarResults = await exa.findSimilar(
  "https://www.example.com",
  {
    numResults: 2,
    excludeSourceDomain: true
  }
);
```

### Input Parameters

| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| url | string | The URL of the webpage to find similar results for. | Required |
| numResults | number | Number of similar results to return. | undefined |
| includeDomains | string[] | List of domains to include in the search. | undefined |
| excludeDomains | string[] | List of domains to exclude from the search. | undefined |
| startCrawlDate | string | Results will only include links crawled after this date. | undefined |
| endCrawlDate | string | Results will only include links crawled before this date. | undefined |
| startPublishedDate | string | Results will only include links with a published date after this date. | undefined |
| endPublishedDate | string | Results will only include links with a published date before this date. | undefined |
| excludeSourceDomain | boolean | If true, excludes results from the same domain as the input URL. | undefined |
| category | string | A data category to focus on when searching, with higher comprehensivity and data cleanliness. | undefined |

### Returns Example

```json
{
  "results": [
    {
      "score": 0.8777582049369812,
      "title": "Play New Free Online Games Every Day",
      "id": "https://www.minigames.com/new-games",
      "url": "https://www.minigames.com/new-games",
      "publishedDate": "2000-01-01",
      "author": null
    },
    {
      "score": 0.87653648853302,
      "title": "Play The best Online Games",
      "id": "https://www.minigames.com/",
      "url": "https://www.minigames.com/",
      "publishedDate": "2000-01-01",
      "author": null
    }
  ]
}
```

### Return Parameters

#### SearchResponse

| Field | Type | Description |
|-------|------|-------------|
| results | Result[] | List of Result objects |
| autopromptString? | string | Exa query created by autoprompt functionality |

#### Result Object

| Field | Type | Description |
|-------|------|-------------|
| url | string | URL of the search result |
| id | string | Temporary ID for the document |
| title | string | Title of the search result |
| score? | number | Similarity score between query/url and result |
| publishedDate? | string | Estimated creation date |
| author? | string | Author of the content, if available |

## findSimilarAndContents Method

Find a list of similar results based on a webpage's URL, optionally including the text content or highlights of each result.

### Input Example

```typescript
// Find similar with full text content
const similarWithText = await exa.findSimilarAndContents(
  "https://www.example.com/article",
  {
    text: true,
    numResults: 2
  }
);

// Find similar with highlights
const similarWithHighlights = await exa.findSimilarAndContents(
  "https://www.example.com/article",
  {
    highlights: true,
    numResults: 2
  }
);

// Find similar with both text and highlights
const similarWithTextAndHighlights = await exa.findSimilarAndContents(
  "https://www.example.com/article",
  {
    text: true,
    highlights: true,
    numResults: 2,
    excludeSourceDomain: true
  }
);
```

### Input Parameters

| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| url | string | The URL of the webpage to find similar results for. | Required |
| text | boolean | If provided, includes the full text of the content in the results. | undefined |
| highlights | boolean | If provided, includes highlights of the content in the results. | undefined |
| numResults | number | Number of similar results to return. | undefined |
| includeDomains | string[] | List of domains to include in the search. | undefined |
| excludeDomains | string[] | List of domains to exclude from the search. | undefined |
| startCrawlDate | string | Results will only include links crawled after this date. | undefined |
| endCrawlDate | string | Results will only include links crawled before this date. | undefined |
| startPublishedDate | string | Results will only include links with a published date after this date. | undefined |
| endPublishedDate | string | Results will only include links with a published date before this date. | undefined |
| excludeSourceDomain | boolean | If true, excludes results from the same domain as the input URL. | undefined |
| category | string | A data category to focus on when searching, with higher comprehensivity and data cleanliness. | undefined |

### Returns Example

```json
{
  "results": [
    {
      "score": 0.8777582049369812,
      "title": "Similar Article: AI and Machine Learning",
      "id": "https://www.similarsite.com/ai-ml-article",
      "url": "https://www.similarsite.com/ai-ml-article",
      "publishedDate": "2023-05-15",
      "author": "Jane Doe",
      "text": "Artificial Intelligence (AI) and Machine Learning (ML) are revolutionizing various industries. [... TRUNCATED FOR BREVITY ...]",
      "highlights": [
        "AI and ML are transforming how businesses operate, enabling more efficient processes and data-driven decision making.",
        "The future of AI looks promising, with potential applications in healthcare, finance, and autonomous vehicles."
      ],
      "highlightScores": [
        0.95,
        0.89
      ]
    },
    {
      "score": 0.87653648853302,
      "title": "The Impact of AI on Modern Technology",
      "id": "https://www.techblog.com/ai-impact",
      "url": "https://www.techblog.com/ai-impact",
      "publishedDate": "2023-06-01",
      "author": "John Smith",
      "text": "In recent years, artificial intelligence has made significant strides in various technological domains. [... TRUNCATED FOR BREVITY ...]",
      "highlights": [
        "AI is not just a buzzword; it's a transformative technology that's reshaping industries and creating new opportunities.",
        "As AI continues to evolve, ethical considerations and responsible development become increasingly important."
      ],
      "highlightScores": [
        0.92,
        0.88
      ]
    }
  ]
}
```

### Return Parameters

#### SearchResponse

| Field | Type | Description |
|-------|------|-------------|
| results | SearchResult<T>[] | List of SearchResult objects |
| autopromptString? | string | Exa query created by autoprompt functionality |

#### SearchResult

Extends the Result object with additional fields based on the requested content:

| Field | Type | Description |
|-------|------|-------------|
| url | string | URL of the search result |
| id | string | Temporary ID for the document |
| title | string | Title of the search result |
| score? | number | Similarity score between query/url and result |
| publishedDate? | string | Estimated creation date |
| author? | string | Author of the content, if available |
| text? | string | Text of the search result page (if requested) |
| highlights? | string[] | Highlights of the search result (if requested) |
| highlightScores? | number[] | Scores of the highlights (if requested) |

Note: The actual fields present in the SearchResult<T> object depend on the options provided in the findSimilarAndContents call.

## getContents Method

Retrieves contents of documents based on a list of document IDs.

### Input Example

```typescript
// Get contents for a single ID
const singleContent = await exa.getContents("https://www.example.com/article");

// Get contents for multiple IDs
const multipleContents = await exa.getContents([
  "https://www.example.com/article1",
  "https://www.example.com/article2"
]);

// Get contents with specific options
const contentsWithOptions = await exa.getContents(
  ["https://www.example.com/article1", "https://www.example.com/article2"],
  {
    text: { maxCharacters: 1000 },
    highlights: { query: "AI", numSentences: 2 }
  }
);
```

### Input Parameters

| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| ids | string | A single ID, an array of IDs, or an array of SearchResults. | Required |
| text | boolean | If provided, includes the full text of the content in the results. | undefined |
| highlights | boolean | If provided, includes highlights of the content in the results. | undefined |

### Returns Example

```json
{
  "results": [
    {
      "id": "https://www.example.com/article1",
      "url": "https://www.example.com/article1",
      "title": "The Future of Artificial Intelligence",
      "publishedDate": "2023-06-15",
      "author": "Jane Doe",
      "text": "Artificial Intelligence (AI) has made significant strides in recent years. [... TRUNCATED FOR BREVITY ...]",
      "highlights": [
        "AI is revolutionizing industries from healthcare to finance, enabling more efficient processes and data-driven decision making.",
        "As AI continues to evolve, ethical considerations and responsible development become increasingly important."
      ],
      "highlightScores": [
        0.95,
        0.92
      ]
    },
    {
      "id": "https://www.example.com/article2",
      "url": "https://www.example.com/article2",
      "title": "Machine Learning Applications in Business",
      "publishedDate": "2023-06-20",
      "author": "John Smith",
      "text": "Machine Learning (ML) is transforming how businesses operate and make decisions. [... TRUNCATED FOR BREVITY ...]",
      "highlights": [
        "Machine Learning algorithms can analyze vast amounts of data to identify patterns and make predictions.",
        "Businesses are leveraging ML for customer segmentation, demand forecasting, and fraud detection."
      ],
      "highlightScores": [
        0.93,
        0.90
      ]
    }
  ]
}
```

### Return Parameters

#### SearchResponse

| Field | Type | Description |
|-------|------|-------------|
| results | SearchResult<T>[] | List of SearchResult objects |

#### SearchResult

The fields in the SearchResult<T> object depend on the options provided in the getContents call:

| Field | Type | Description |
|-------|------|-------------|
| id | string | Temporary ID for the document |
| url | string | URL of the search result |
| title | string | Title of the search result |
| publishedDate? | string | Estimated creation date |
| author? | string | Author of the content, if available |
| text? | string | Text of the search result page (if requested) |
| highlights? | string[] | Highlights of the search result (if requested) |
| highlightScores? | number[] | Scores of the highlights (if requested) |

Note: The actual fields present in the SearchResult<T> object depend on the options provided in the getContents call. If neither text nor highlights is specified, the method defaults to including the full text content.

# Getting Started with RAG in TypeScript

## How to Use Exa with TypeScript

### 1. Create an account and grab an API key

First, generate and grab an API key for Exa:

[Get API Key](https://exa.ai/signup)

### 2. Install the SDK

Install the Exa TypeScript SDK using npm:

```bash
npm install exa-js
```

### 3. Instantiate the client

Create a new TypeScript file (e.g., `exa-example.ts`) and instantiate the Exa client:

```typescript
import Exa from 'exa-js';

const exa = new Exa(process.env.EXA_API_KEY);
```

Make sure to set the `EXA_API_KEY` environment variable with your API key.

### 4. Make a search using the searchAndContents method

Here's an example of how to use the `searchAndContents` method:

```typescript
async function exampleSearch() {
  try {
    const result = await exa.searchAndContents(
      "hottest AI startups",
      {
        type: "neural",
        useAutoprompt: true,
        numResults: 10,
        text: true,
      }
    );

    console.log(JSON.stringify(result, null, 2));
  } catch (error) {
    console.error("Error:", error);
  }
}

exampleSearch();
```

### 5. Set up OpenAI and pass the Exa query results to perform RAG

First, install the OpenAI library:

```bash
npm install openai
```

Now, set up the OpenAI client and use it to summarize Exa search results, creating a RAG system:

```typescript
import OpenAI from 'openai';

const openai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY,
});

async function performRAG() {
  try {
    const exaResult = await exa.searchAndContents(
      "hottest AI startups",
      {
        type: "neural",
        useAutoprompt: true,
        numResults: 10,
        text: true,
      }
    );

    const systemPrompt = "You are a helpful AI assistant. Summarize the given search results about AI startups.";
    const userMessage = "Please provide a brief summary of the top AI startups based on the search results.";

    const response = await openai.chat.completions.create({
      model: "gpt-3.5-turbo",
      messages: [
        { role: "system", content: systemPrompt },
        { role: "user", content: `Search results: ${JSON.stringify(exaResult)}\n\n${userMessage}` }
      ]
    });

    console.log(response.choices[0].message.content);
  } catch (error) {
    console.error("Error:", error);
  }
}

performRAG();
```

Make sure to set the `OPENAI_API_KEY` environment variable with your OpenAI API key.

This completes the guide, demonstrating how to set up Exa and OpenAI, use Exa's search capabilities, and then use OpenAI to summarize the results in a RAG system. The combination of Exa's powerful search capabilities with OpenAI's language model allows for the creation of a system that can retrieve relevant, up-to-date information and generate insightful summaries based on that information.

# Exa Prompting Guide

## Why Prompting Matters

Exa's neural search excels at understanding natural language descriptions of web content. For optimal results, frame queries as if you're describing a link to someone.

## Quick Solutions

- Use Autoprompt: Set `use_autoprompt=true` for automatic query optimization.
- Use Auto Search: Use `type="auto"` for intelligent routing to auto vs keyword search, especially where neural is not returning useful results.

## Prompting Tips

- **Phrase as Statements**: "Here's a great article about X:" works better than "What is X?"
- **Add Context**: Include modifiers like "funny", "academic", or specific websites to narrow results.
- **End with a Colon**: Many effective prompts end with ":", mimicking natural link sharing.

### Examples

> Bad: "best restaurants in SF"
> Good: "Here is the best restaurant in SF:"

> Bad: "What's the best way to learn cooking?"
> Good: "This is the best tutorial on how to get started with cooking:"

Remember, Exa is continually improving. These guidelines help leverage its current strengths while we work on making prompting even more intuitive.

## Exa's Capabilities Explained

This page explains some of the available feature functionalities of Exa and some unique ways you might use Exa for your use-case

### Exa's Capabilities

#### Search Types

##### Auto Search (prev. Magic Search)

Where you would use it:
When you want optimal results without manually choosing between neural and keyword search. When you might not know ahead of time what the best search type is. Note Auto Search is the default search type - when unspecified, Auto Search is used.

```typescript
const result = await exa.search("hottest AI startups", { type: "auto" });
```

##### Neural Search

Description: Uses Exa's embeddings-based index and query model to perform complex queries and provide semantically relevant results.

Where you would use it: For exploratory searches or when looking for conceptually related content rather than exact keyword matches. To find hard to find, specific results from the web.

```typescript
const result = await exa.search("Here is a startup building innovative solutions for climate change:", { type: "neural" });
```

##### Keyword Search

Description: Traditional search method that matches specific words or phrases.

Where you would use it: When doing simple, broad searches where the user can refine results manually. Good for general browsing and finding exact matches. Good for matching proper nouns or terms of art that are rarely used in other contexts. When neural search fails to return what you are looking for.

Note: For keyword search, domain filtering is limited to ~5-10 usably. This is because domain filtering on keyword search works by post-pending `site:example.com OR ...` and on for particular domains. Using more than 15-20 domains then therefore runs into query length maximums, breaking the domain filter. Neural search supports infinite domain filtering.

```typescript
const result = await exa.search("Paris", { type: "keyword" });
```

##### Phrase Filter Search

Description: Apply keyword filters atop of a neural search before returning results

Where you would use it: When you want the power of Neural Search but also need to specify and filter on some key phrase. Often helpful when filtering on a piece of jargon where a specific match is crucial.

```typescript
const result = await exa.search(query, { type: 'neural', includeText: 'Some_key_phrase_to_fiter_on' });
```

See a worked example [here](link_to_example)

#### Large-scale Searches

Description: Exa searches that return a large number of search results.

Where you would use it: When desiring comprehensive, semantically relevant data for batch use cases, e.g., for enrichment of CRMs or full topic scraping.

```typescript
const result = await exa.search("Companies selling sonar technology", { num_results: 1000 });
```

Note: High return results cost more and higher result caps (e.g., 1000 returns) are restricted to Enterprise/Custom plans only. Get in touch if you are interested in learning more.

#### Content Retrieval

##### Contents Retrieval

Description: Instantly retrieves whole, cleaned and parsed webpage contents from search results.

Where you would use it: When you need the full text of webpages for analysis, summarization, or other post-processing.

```typescript
const result = await exa.searchAndContents("latest advancements in quantum computing", { text: true });
```

##### Highlights Retrieval

Description: Extracts relevant excerpts or highlights from retrieved content.

Where you would use it: When you want quick or targeted outputs from the most relevant parts of a search entity without wanting to handle the full text.

```typescript
const result = await exa.searchAndContents("AI ethics", { highlights: true });
```

#### Prompt Engineering

Prompt engineering is crucial for getting the most out of Exa's capabilities. The right prompt can dramatically improve the relevance and usefulness of your search results. This is especially important for neural search and advanced features like writing continuation.

##### Writing continuation queries

Description: Prompt crafted by post-pending 'Here is a great resource to continue writing this piece of writing:'. Useful for research writing or any other citation-based text generation after passing to an LLM.

Where you would use it: When you're in the middle of writing a piece and need to find relevant sources to continue or expand your content. This is particularly useful for academic writing, content creation, or any scenario where you need to find information that logically follows from what you've already written.

```typescript
const current_text = `
The impact of climate change on global agriculture has been significant. 
Rising temperatures and changing precipitation patterns have led to shifts 
in crop yields and growing seasons. Some regions have experienced increased 
drought stress, while
`;
const continuation_query = current_text + " If you found the above interesting, here's another useful resource to read:";
const result = await exa.search(continuation_query, { type: "neural", use_autoprompt: false });
```

##### Long queries

Description: Utilizing Exa's long query window to perform matches against semantically rich content.

Where you would use it: When you need to find content that matches complex, detailed descriptions or when you want to find content similar to a large piece of text. This is particularly useful for finding niche content or when you're looking for very specific information.

```typescript
const long_query = `
Abstract: In this study, we investigate the potential of quantum-enhanced machine learning algorithms 
for drug discovery applications. We present a novel quantum-classical hybrid approach that leverages 
quantum annealing for feature selection and a quantum-inspired tensor network for model training. 
Our results demonstrate a 30% improvement in prediction accuracy for binding affinity in protein-ligand 
interactions compared to classical machine learning methods. Furthermore, we show a significant 
reduction in computational time for large-scale molecular dynamics simulations. These findings 
suggest that quantum machine learning techniques could accelerate the drug discovery process 
and potentially lead to more efficient identification of promising drug candidates.
`;
const result = await exa.search(long_query, { type: "neural", use_autoprompt: false });
```

##### Use Autoprompt (incl. Autodate)

Description: Automatically optimizes your query for Exa's neural search.

Where you would use it: When you want to leverage Exa's neural search capabilities without manually crafting the perfect prompt. It's particularly useful for general-purpose queries or when you're not sure how to phrase your query for optimal results.

```typescript
const result = await exa.search("AI startups in healthcare", { use_autoprompt: true });
```

Note: `use_autoprompt` is set to `false` in some examples above where manual prompt engineering is demonstrated. For most general use cases, leaving it as `true` (the default) will yield good results.

Using autoprompt will also automatically fetch date information as a filter to apply onto searches. For instance, the query:

"Here is the latest news from Russia in the last 7 days"

On July 15 2024, will produce results with an autoDate response attribute:

```json
{
  "autopromptString": "\"Here is the latest news from Russia:",
  "autoDate": "2024-07-08T17:18:57.152Z",
  "results": ...
}
```

Note the date is no longer in the query, but rather is applied as a strict filter as though you had applied it as a date.

# Examples

## Exa Researcher - JavaScript

Example project using the Exa JS SDK

### What this doc covers
- Using Exa's Auto Search to pick the best search setting for each query (keyword or neural)
- Using `searchAndContents()` through Exa's JavaScript SDK

In this example, we will build Exa Researcher, a JavaScript app that, given a research topic, automatically searches for relevant sources with Exa's Auto Search and synthesizes the information into a reliable research report.

Fastest setup: Interact with the code in your browser with this [Replit template](https://replit.com/@ExaAI/Exa-Researcher).

Alternatively, this interactive notebook was made with the Deno Javascript kernel for Jupyter so you can easily run it locally. Check out the plain JS version if you prefer a regular Javascript file you can run with NodeJS, or want to skip to the final result. If you'd like to run this notebook locally, [Installing Deno](https://deno.land/#installation) and [connecting Deno to Jupyter](https://github.com/deno-jupyter/deno-jupyter) is fast and easy.

To play with this code, first we need a Exa API key and an OpenAI API key.

### Setup

Let's import the Exa and OpenAI SDKs and put in our API keys to create a client object for each. Make sure to pick the right imports for your runtime and paste or load your API keys.

```typescript
// Deno imports
import Exa from 'npm:exa-js';
import OpenAI from 'npm:openai';

// NodeJS imports
//import Exa from 'exa-js';
//import OpenAI from 'openai';

// Replit imports
//const Exa = require("exa-js").default;
//const OpenAI = require("openai");

const EXA_API_KEY = "" // insert or load your API key here
const OPENAI_API_KEY = ""// insert or load your API key here

const exa = new Exa(EXA_API_KEY);
const openai = new OpenAI({ apiKey: OPENAI_API_KEY });
```

Since we'll be making several calls to the OpenAI API to get a completion from GPT-3.5 Turbo, let's make a simple utility function so we can pass in the system and user messages directly, and get the LLM's response back as a string.

```typescript
async function getLLMResponse({system = 'You are a helpful assistant.', user = '', temperature = 1, model = 'gpt-3.5-turbo'}){
    const completion = await openai.chat.completions.create({
        model,
        temperature,
        messages: [
            {'role': 'system', 'content': system},
            {'role': 'user', 'content': user},
        ]
    });
    return completion.choices[0].message.content;
}
```

Okay, great! Now let's starting building Exa Researcher.

### Exa Auto Search

The researcher should be able to automatically generate research reports for all kinds of different topics. Here's two to start:

```typescript
const SAMA_TOPIC = 'Sam Altman';
const ART_TOPIC = 'renaissance art';
```

The first thing our researcher has to do is decide what kind of search to do for the given topic.

Exa offers two kinds of search: neural and keyword search. Here's how we decide:

- Neural search is preferred when the query is broad and complex because it lets us retrieve high quality, semantically relevant data. Neural search is especially suitable when a topic is well-known and popularly discussed on the Internet, allowing the machine learning model to retrieve contents which are more likely recommended by real humans.
- Keyword search is useful when the topic is specific, local or obscure. If the query is a specific person's name, and identifier, or acronym, such that relevant results will contain the query itself, keyword search may do well. And if the machine learning model doesn't know about the topic, but relevant documents can be found by directly matching the search query, keyword search may be necessary.

Conveniently, Exa's Auto Search feature (on by default) will automatically decide whether to use keyword or neural search for each query. For example, if a query is a specific person's name, Exa would decide to use keyword search.

Now, we'll create a helper function to generate search queries for our topic.

```typescript
async function generateSearchQueries(topic, n){
    const userPrompt = `I'm writing a research report on ${topic} and need help coming up with diverse search queries.
Please generate a list of ${n} search queries that would be useful for writing a research report on ${topic}. These queries can be in various formats, from simple keywords to more complex phrases. Do not add any formatting or numbering to the queries.`;

    const completion = await getLLMResponse({
        system: 'The user will ask you to help generate some search queries. Respond with only the suggested queries in plain text with no extra formatting, each on its own line.',
        user: userPrompt,
        temperature: 1
    });
    return completion.split('\n').filter(s => s.trim().length > 0).slice(0, n);
}
```

Next, let's write another function that actually calls the Exa API to perform searches using Auto Search.

```typescript
async function getSearchResults(queries, linksPerQuery=2){
    let results = [];
    for (const query of queries){
        const searchResponse = await exa.searchAndContents(query, { 
            numResults: linksPerQuery, 
            useAutoprompt: false 
        });
        results.push(...searchResponse.results);
    }
    return results;
}
```

### Writing a report with GPT-4

The final step is to instruct the LLM to synthesize the content into a research report, including citations of the original links. We can do that by pairing the content and the URLs and writing them into the prompt.

```typescript
async function synthesizeReport(topic, searchContents, contentSlice = 750){
    const inputData = searchContents.map(item => `--START ITEM--\nURL: ${item.url}\nCONTENT: ${item.text.slice(0, contentSlice)}\n--END ITEM--\n`).join('');
    return await getLLMResponse({
        system: 'You are a helpful research assistant. Write a report according to the user\'s instructions.',
        user: 'Input Data:\n' + inputData + `Write a two paragraph research report about ${topic} based on the provided information. Include as many sources as possible. Provide citations in the text using footnote notation ([#]). First provide the report, followed by a single "References" section that lists all the URLs used, in the format [#] <url>.`,
        //model: 'gpt-4' //want a better report? use gpt-4 (but it costs more)
    });
}
```

### All Together Now

Now, let's just wrap everything into one Researcher function that strings together all the functions we've written. Given a user's research topic, the Researcher will generate search queries, feed those queries to Exa Auto Search, and finally use an LLM to synthesize the retrieved information. Three simple steps!

```typescript
async function researcher(topic){
    console.log(`Starting research on topic: "${topic}"`);
    
    const searchQueries = await generateSearchQueries(topic, 3);
    console.log("Generated search queries:", searchQueries);
    
    const searchResults = await getSearchResults(searchQueries);
    console.log(`Found ${searchResults.length} search results. Here's the first one:`, searchResults[0]);
    
    console.log("Synthesizing report...");
    const report = await synthesizeReport(topic, searchResults);
    
    return report;
}
```

In just a couple lines of code, we've used Exa to go from a research topic to a valuable essay with up-to-date sources.

```typescript
async function runExamples() {
    console.log("Researching Sam Altman:");
    const samaReport = await researcher(SAMA_TOPIC);
    console.log(samaReport);

    console.log("\n\nResearching Renaissance Art:");
    const artReport = await researcher(ART_TOPIC);
    console.log(artReport);
}

// To use the researcher on the examples, simply call the runExamples() function:
runExamples();

// Or, to research a specific topic:
researcher("llama antibodies").then(console.log);
```

## Exa-powered Writing Assistant 

### Demo overview

#### High-level overview
This demo showcases a real-time writing assistant that uses Exa's search capabilities to provide relevant information and citations as a user writes. The system combines Exa's neural search with Anthropic's Claude AI model to generate contextually appropriate content and citations.

#### Exa prompting and query style
The Exa search is performed using a unique query style that appends the user's input with a prompt for continuation. Here's the relevant code snippet:

```javascript
let exaQuery = conversationState.length > 1000 
    ? (conversationState.slice(-1000))+"\n\nIf you found the above interesting, here's another useful resource to read:"
    : conversationState+"\n\nIf you found the above interesting, here's another useful resource to read:"

let exaReturnedResults = await exa.searchAndContents(
    exaQuery,
    {
        type: "neural",
        useAutoprompt: false,
        numResults: 10,
        highlights: {
            numSentences: 1,
            highlightsPerUrl: 1
        }
    }
)
```

Key aspects of this query style:

- **Continuation prompt**: The crucial post-pend "A helpful source to read so you can continue writing the above:"
  - This prompt is designed to find sources that can logically continue the user's writing when passed to an LLM to generate content.
  - It leverages Exa's ability to understand context and find semantically relevant results.
  - By framing the query as a request for continuation, it aligns with how people naturally share helpful links.
- **Length limitation**: It caps the query at 1000 characters to maintain relevance and continue writing just based on the last section of the text.

Note: This prompt is not a hard and fast rule for this use-case - we encourage experimentation with query styles to get the best results for your specific use case. For instance, you could further constrain down to just research papers.

#### Prompting Claude with Exa results
The Claude AI model is prompted with a carefully crafted system message and passed the above formatted Exa results. Here is an example system prompt:

```typescript
const systemPrompt = `You are an essay-completion bot that continues/completes a sentence given some input stub of an essay/prose. You only complete 1-2 SHORT sentence MAX. If you get an input of a half sentence or similar, DO NOT repeat any of the preceding text of the prose. THIS MEANS DO NOT INCLUDE THE STARTS OF INCOMPLETE SENTENCES IN YOUR RESPONSE. This is also the case when there is a spelling, punctuation, capitalization or other error in the starter stub - e.g.:

USER INPUT: pokemon is a
YOUR CORRECT OUTPUT: Japanese franchise created by Satoshi Tajiri.
NEVER/INCORRECT: Pokémon is a Japanese franchise created by Satoshi Tajiri.

USER INPUT: Once upon a time there
YOUR CORRECT OUTPUT: was a princess.
NEVER/INCORRECT: Once upon a time, there was a princess.

USER INPUT: Colonial england was a
YOUR CORRECT OUTPUT: time of great change and upheaval.
NEVER/INCORRECT: Colonial England was a time of great change and upheaval.

USER INPUT: The fog in san francisco
YOUR CORRECT OUTPUT: is a defining characteristic of the city's climate.
NEVER/INCORRECT: The fog in San Francisco is a defining characteristic of the city's climate.

USER INPUT: The fog in san francisco
YOUR CORRECT OUTPUT: is a defining characteristic of the city's climate.
NEVER/INCORRECT: The fog in San Francisco is a defining characteristic of the city's climate.

 Once you have made one citation, stop generating. BE PITHY. Where there is a full sentence fed in, 
 you should continue on the next sentence as a generally good flowing essay would. You have a 
 specialty in including content that is cited. Given the following two items, (1) citation context and 
 (2) current essay writing, continue on the essay or prose inputting in-line citations in
 parentheses with the author's name, right after that followed by the relevant URL in square brackets. 
 THEN put a parentheses around all of the above. If you cannot find an author (sometimes it is empty), use the generic name 'Source'. 
 ample citation for you to follow the structure of: ((AUTHOR_X, 2021)[URL_X]). 
 If there are more than 3 author names to include, use the first author name plus 'et al'`
```

This prompt ensures that:

- Claude will only do completions, not parrot back the user query like in a typical chat based scenario. Note the inclusion of multiple examples that demonstrate Claude should not reply back with the stub even if there are errors, like spelling or grammar, in the input text (which we found to be a common issue)
- We define the citation style and formatting. We also tell the bot went to collapse authors into 'et al' style citations, as some webpages have many authors

Once again, experimenting with this prompt is crucial to getting best results for your particular use case.

### Conclusion
This demo illustrates the power of combining Exa's advanced search capabilities with generative AI to create a writing assistant. By leveraging Exa's neural search and content retrieval features, the system can provide relevant, up-to-date information to any AI model, resulting in contextually appropriate content generation with citations.

- This approach showcases how Exa can be integrated into AI-powered applications to enhance user experiences and productivity.


