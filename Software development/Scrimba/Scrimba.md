### Intro to AI engineering
![[Pasted image 20251126194630.png]]

The LLM is the type of algorithm that uses training data  to recognize patterns and make predictions or decisions. 
```js
import OpenAI from 'openai'

const openai = new OpenAI({
	dangerouslyAllowBrowser: true
})

const messages = [
{
role: 'system',
content: 'You are a helpful assistant that explains things in language a 10-year-old can understand. Your answers are always less than 100 words.'
},
{
role: 'user',
content: 'What is Quantum Computing?'
}
]

const response = await openai.chat.completions.create({
model: 'gpt-4',
messages: messages,
max_tokens: 16 // default: inf
})
```

temperature means what does it means to be creative(0 to 2).

Few shot approach. It just means providing one or more examples in user or system object. That is it. It is more expensive and less performance. but we do get the output exactly we.

Stop sequence. We can use max token for that or tell model to stop once you see certain pattern. 

frequency and presence penalties. 
High presence penalties means talking about same topic over one chat while low presence penalties means talking about different topic. High frequency penalties means decreasing the probability of using same words or phrase in one chat. opposite for low frequency penalties.  

Fine tuning
Just giving a standard, pre-trained model a specific data set to enhance its performance on a particular tasks. 
Why would we need fine tuning. We can save money by decreasing the prompt token size by giving the model data we may need to give in prompt. It saves token. We can teach the model how it can respond. We can make the AI to return certain format every time. We can reduce the cost by fine-tuning the cheap model and fine-tuning it instead of expensive model. However fine-tuning should be used as last resort. First try good prompt and use few shot approach. It is quite tedious to do it. We need plenty of data. We need at least about 50 data for fine-tuning. write in ` openai.files.create({ purpose: "", file: ... }) `

for image, we get blob from Dalle E. 
### Deployment
We need to create Cloudflare workers to handle API request securely. shift API request from client side to server side. and set up cloudflare AI gateway to make the app robust.  

### Open source Models
In the world of AI, OS means three things. One is Dataset(usually closed source), code and models. It is called Open source if models are open source. 

Inference just means getting result/prediction from pre-trained models. and models are stored around Hugging Face. 
![[Pasted image 20251201101112.png]]

The code for some of them could be :
```js

```
We just call Hugging Face and call the library and expect result. 

With Transformers
We will download the 
![[Pasted image 20251201153945.png]]
We need to filter the models so that it will be compatible with `transformer.js`

First we need to load the model in browser with the pipeline function 
```js
import { pipeline } from ""

const detector = await pipelile("", "")
```
And don't worry, model get cached in browser and we don't need to make external API calls. 
### Embeddings and Vector Database
Vector Embeddings
Embeddings just means a language that AI understands. 

So How to create embedding for a text, sentence, or even paragraph ? We do it through the embedding model of ChatGPT. 
```ts
const embeddings = await openai.embeddings.create({
	model: "",
	input: "" 
})
```

Normally we put the embedding in vector database with content like paragraph, sentence and other things. 

Semantic Search - searching by its meaning and understanding user's intent rather than keyword. 

we can put everything in vector database and can perform query using similar search. One of the popular algorithm used to measure similarity between two vectors is called cosine similarity. But supabase makes it easy and fast as sql to do that. 

Query search using similar search # I need to reread this. 

create a conversational response using OPENAI # I have to do it again. 

Creating text chunks from documents - meaning some docs have more than let's say 8000 words right ? so what if we put embedding in it as a whole ? we might lose the context right ? So we need to chunk it.  or some docs may have html tags and so many error right ? So chunking helps to solve it at least partially. 
```ts
import { CharacterTextSplitter } from "langchain"

const splitter = new CharacterTextSplitter({
	seperator: " ",
	chunkSize: 150,
	chunkOverlap: 15
})

const output = await splitter.createDocuments([text])
console.log("output", output)
```
But with this splitting technique, we mayn't get optimum splitting. may be paragraph might get caught. So we normally use `RecursiveCharacterTextSplitter` to iterate over document and do it in such a way that it becomes better
```ts
import { RecursiveCharacterTextSplitter } from "langchain"

const splitter = new RecursiveCharacterTextSplitter({
	chunkSize: 150,
	chunkOverlap: 15,
})

const output = await splitter.createDocuments([text])
console.log("output", output)
```

Normally, we would get the document, split that document into chunks and use OPENAI to get embedding for those chunks and save those chunks with its embedding in supabase. 

AI chat bot proof of concept


RAG(Retrieval Augmented Generation)
The things that we did is RAG. It is simple. For R, We retrieve lot of information from data source and 
See it later

Do solo Project: PopChoice
### Agents
The idea of agents is that it can pull together multiple tools and can iterate on the tasks until the task is complete. 
![[Pasted image 20251202141139.png]]

![[Pasted image 20251122205145.png]]
In the system prompt, We need to teach how to do this thing. 

```ts
export async function getCurrentWeather() {

const weather = {

temperature: "72",

unit: "F",

forecast: "sunny"

}

return JSON.stringify(weather)

}

  

export async function getLocation() {

return "New York City, NY"

}

import OpenAI from "openai"

import { getCurrentWeather, getLocation } from "./tools"

  

export const openai = new OpenAI({

apiKey: process.env.OPENAI_API_KEY,

dangerouslyAllowBrowser: true

})

  

const availableFunctions = {

getCurrentWeather,

getLocation

}

  

const systemPrompt = `

You cycle through Thought, Action, PAUSE, Observation. At the end of the loop you output a final Answer. Your final answer should be highly specific to the observations you have from running

the actions.

1. Thought: Describe your thoughts about the question you have been asked.

2. Action: run one of the actions available to you - then return PAUSE.

3. PAUSE

4. Observation: will be the result of running those actions.

  

Available actions:

- getCurrentWeather:

E.g. getCurrentWeather: Salt Lake City

Returns the current weather of the location specified.

- getLocation:

E.g. getLocation: null

Returns user's location details. No arguments needed.

  

Example session:

Question: Please give me some ideas for activities to do this afternoon.

Thought: I should look up the user's location so I can give location-specific activity ideas.

Action: getLocation: null

PAUSE

  

You will be called again with something like this:

Observation: "New York City, NY"

  

Then you loop again:

Thought: To get even more specific activity ideas, I should get the current weather at the user's location.

Action: getCurrentWeather: New York City

PAUSE

  

You'll then be called again with something like this:

Observation: { location: "New York City, NY", forecast: ["sunny"] }

  

You then output:

Answer: <Suggested activities based on sunny weather that are highly specific to New York City and surrounding areas.>

`

  

async function agent(query) {

const messages = [

{ role: "system", content: systemPrompt },

{ role: "user", content: query }

]

const MAX_ITERATIONS = 5

const actionRegex = /^Action: (\w+): (.*)$/

for (let i = 0; i < MAX_ITERATIONS; i++) {

console.log(`Iteration #${i + 1}`)

const response = await openai.chat.completions.create({

model: "gpt-3.5-turbo",

messages

})

  

const responseText = response.choices[0].message.content

console.log(responseText)

messages.push({ role: "assistant", content: responseText })

const responseLines = responseText.split("\n")

  

const foundActionStr = responseLines.find(str => actionRegex.test(str))

if (foundActionStr) {

const actions = actionRegex["exec"](foundActionStr)

const [_, action, actionArg] = actions

if (!availableFunctions.hasOwnProperty(action)) {

throw new Error(`Unknown action: ${action}: ${actionArg}`)

}

console.log(`Calling function ${action} with argument ${actionArg}`)

const observation = await availableFunctions[action](actionArg)

messages.push({ role: "assistant", content: `Observation: ${observation}` })

} else {

console.log("Agent finished with task")

return responseText

}

}

}

  

// console.log(await agent("What are some activity ideas that I can do this afternoon based on my location and weather?"))
```


With OpenAI - calling functions manually
```ts
import OpenAI from "openai"

import { getCurrentWeather, getLocation, tools } from "./tools"

  

export const openai = new OpenAI({

apiKey: process.env.OPENAI_API_KEY,

dangerouslyAllowBrowser: true

})

  

const availableFunctions = {

getCurrentWeather,

getLocation

}

  

async function agent(query) {

const messages = [

{ role: "system", content: "You are a helpful AI agent. Give highly specific answers based on the information you're provided. Prefer to gather information with the tools provided to you rather than giving basic, generic answers." },

{ role: "user", content: query }

]

  

const MAX_ITERATIONS = 5

  

for (let i = 0; i < MAX_ITERATIONS; i++) {

console.log(`Iteration #${i + 1}`)

const response = await openai.chat.completions.create({

model: "gpt-3.5-turbo-1106",

messages,

tools

})

  

const { finish_reason: finishReason, message } = response.choices[0]

const { tool_calls: toolCalls } = message

console.log(toolCalls)

messages.push(message)

if (finishReason === "stop") {

console.log(message.content)

console.log("AGENT ENDING")

return

} else if (finishReason === "tool_calls") {

for (const toolCall of toolCalls) {

const functionName = toolCall.function.name

const functionToCall = availableFunctions[functionName]

const functionArgs = JSON.parse(toolCall.function.arguments)

const functionResponse = await functionToCall(functionArgs)

console.log(functionResponse)

messages.push({

tool_call_id: toolCall.id,

role: "tool",

name: functionName,

content: functionResponse

})

}

}

}

}

  

await agent("What's the current weather in my current location?")

  

/**

import OpenAI from "openai"

import { getCurrentWeather, getLocation, tools } from "./tools"

  

export const openai = new OpenAI({

apiKey: process.env.OPENAI_API_KEY,

dangerouslyAllowBrowser: true

})

  

const availableFunctions = {

getCurrentWeather,

getLocation

}

  

async function agent(query) {

const messages = [

{ role: "system", content: "You are a helpful AI agent. Give highly specific answers based on the information you're provided. Prefer to gather information with the tools provided to you rather than giving basic, generic answers." },

{ role: "user", content: query }

]

  

const MAX_ITERATIONS = 5

  

for (let i = 0; i < MAX_ITERATIONS; i++) {

console.log(`Iteration #${i + 1}`)

const response = await openai.chat.completions.create({

model: "gpt-3.5-turbo-1106",

messages,

tools

})

  

const { finish_reason: finishReason, message } = response.choices[0]

const { tool_calls: toolCalls } = message

console.log(toolCalls)

messages.push(message)

if (finishReason === "stop") {

console.log(message.content)

console.log("AGENT ENDING")

return

} else if (finishReason === "tool_calls") {

for (const toolCall of toolCalls) {

const functionName = toolCall.function.name

const functionToCall = availableFunctions[functionName]

const functionArgs = JSON.parse(toolCall.function.arguments)

const functionResponse = await functionToCall(functionArgs)

console.log(functionResponse)

messages.push({

tool_call_id: toolCall.id,

role: "tool",

name: functionName,

content: functionResponse

})

}

}

}

}

  

await agent("What's the current weather in my current location?")

  

/**
```

With OPENAI - calling functions automatically
```js
import OpenAI from "openai"

import { getCurrentWeather, getLocation, functions } from "./tools"

  

export const openai = new OpenAI({

apiKey: process.env.OPENAI_API_KEY,

dangerouslyAllowBrowser: true

})

  

const availableFunctions = {

getCurrentWeather,

getLocation

}

  

async function agent(query) {

const messages = [

{ role: "system", content: "You are a helpful AI agent. Give highly specific answers based on the information you're provided. Prefer to gather information with the tools provided to you rather than giving basic, generic answers." },

{ role: "user", content: query }

]

  

const runner = openai.beta.chat.completions.runFunctions({

model: "gpt-3.5-turbo-1106",

messages,

functions

}).on("message", (message) => console.log(message))

const finalContent = await runner.finalContent()

console.log(finalContent)

}

  

await agent("What's the current weather in my current location?")

  

/**
```

`ipapi`
### Context Engineering
Well, AI lose context after messaging it for long time. 

Slide the messages array sent back to AI. Like just send last 10 messages only and things like that. 

```ts
const contextMessages = messages.slice(-10)
```


### Vercel AI SDK

### MCP
It's an open protocol that allows AI models to securely and flexibly connect to tools, APIs and other resources. We need a way to manage how models access tools and data. It solves the limitation of LLMs and is different from APIs. 

The Protocol in MCP tells how models should receive and use context.
![[Pasted image 20251201202542.png]]
Context are important

It solves two major issues. Lack of latest data and limited context windows . MCP is just like USB C for AI. If I want my LLM Model to access A weather API, A local folder of PDFs, Google Search or calendar, We can just plug those in the models using MCP.

![[Pasted image 20251208203828.png]]
MCP follows Client Server Architecture. If we use the host LLM app with IDE like Claude, IDE and others, MCP Client resides inside the host app, we can connect one or more MCP server to those. 


MCP is the new standard for building APIs. and it works very differently from traditional APIs. Lets have a look at core MCP Concept and Architecture. 
![[Pasted image 20251208204352.png]]
From High level, We can see that there is Host and MCP client lives insides the Host app and talks one to one with server. And Server provides tools, resources and prompt that is context and all the communication happens at transport layer. For MCP Server development, first we need to understand 3 core primitives.
![[Pasted image 20251208204653.png]]
Resources are similar to HTTP GET Requests. They helps us to expose data or content from an MCP server to an LLM. They are just mean to retrieve and return data and don't perform complex calculation.(not process it). Their job is to make context available to the model and nothing more. Where does that data comes from ? 
![[Pasted image 20251208204952.png]]
In short, resources is any structured data source that your MCP server want to make accessible to connected LLM client. 

Let's talk about Tools. Tools are like HTTP POST Requests. They perform action. They are the executable functions that your server make it accessible for the LLM to "take action". Like it could make the function to send the email(Like automate the process of composing and sending an email).
![[Pasted image 20251208205535.png]]
Let's understand with example. Let's say we are creating the Agent to book the hotel rooms. So we can create a set of MCP tool to ![[Pasted image 20251208205700.png]]So This tools gives you access to outside world and even perform actions also. 

Here are the difference. ![[Pasted image 20251208205747.png]]
Now Let's talk about Prompt. They are reusable prompt template(Need to look more here, I immediately went to Transport)

Now let's talk about MCP Transport
There are two built-in MCP transport. ![[Pasted image 20251208205940.png]]

We can create MCP Server in any language. In `Js`, We should use MCP SDK. 

### Multi-Modality
It is about getting image from text or querying about image and getting answer about the query of image. Urls exists just for 1 hour. We can provide `respons_format` to `b64_json`. 






Agents can pulls multiple tools and work on a tasks until the tasks is complete.  On simple level it means using LLM to gather real time information and loop over a task, solving one piece at a time. On complex terms it could be driving the autonomous car and driving it until the car drives the destination. 

It just means AI can take action on its own. Digital assistant that can do work. 

The term ReAct = Reasoning and Act which is main theme of Agentic AI. 

It does its thing in three steps
In first step, we use LLM to get the logic or make. Not to generate content like normal way. 
In second step, we write the code in such a way such that LLM can perform the logic/action that it made. At first LLM will tell what function it want to call 

![[Pasted image 20251122205350.png]]

SERP API = A API for performing online searches
Wikipedia article scanner
This things are needed as tools for agents