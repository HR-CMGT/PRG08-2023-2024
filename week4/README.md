# Week 4

In deze oefening gaan we vragen over een document beantwoorden met een taalmodel. Je werkt in drie losse projecten:

- Voorbereiding: tekst inladen, embedden, model opslaan
- Server: model inladen, vragen beantwoorden met LLM
- Client: vragen sturen vanuit de user interface

<br>

### Inhoud

- Werken met grote hoeveelheden tekst
- Tekst omzetten naar vectors
- Tekstbestand inlezen
- Vragen beantwoorden
- Vector stores
- Privacy en copyright

<br><br><br>

## Tekst omzetten naar vectors

In het college heb je gezien waarom teksten als `vectordata` worden gebruikt in taalmodellen. Jouw tekst moet je dus omzetten naar vectordata. Dit noemen we `embedding`. Om tekst te `embedden` heb je een taalmodel nodig dat *niet* als chat model is getraind. OpenAI gebruikt hier het `ada` model voor. 

We gebruiken het `embedding` (ada) model om vectordata te maken. We hebben het `chat` model (chatgpt) uit de vorige les ook nodig om vragen te stellen. De waarden voor de keys staan in de `.env` file.

```js
import { OpenAIEmbeddings, ChatOpenAI } from "@langchain/openai"

const chatmodel = new ChatOpenAI({
    temperature: 0.3,
    azureOpenAIApiKey: process.env...,
    azureOpenAIApiVersion: process.env...,
    azureOpenAIApiInstanceName: process.env...,
    azureOpenAIApiDeploymentName: process.env..., // hier vul je het `chatgpt` model in
})

const embeddings = new OpenAIEmbeddings({
    temperature: 0.1,
    azureOpenAIApiKey: process.env...,
    azureOpenAIApiVersion: process.env...,
    azureOpenAIApiInstanceName: process.env...,
    azureOpenAIApiDeploymentName: process.env...,  // hier vul je het 'ada' model in
})
```
#### Hello world test

```js
const vectordata = await embeddings.embedQuery("Hello world")
console.log(vectordata)
console.log(`Created ${vectordata.length} vectors`)
```
[Langchain documentatie voor Azure OpenAI embedding](https://js.langchain.com/docs/integrations/text_embedding/azure_openai).

<br>

## Tekstbestand inlezen

Langchain heeft [verschillende opties om tekstbestanden, PDF files, JSON files, CSV files, enz. in te lezen](https://js.langchain.com/docs/modules/data_connection/document_loaders/). In dit voorbeeld lezen we een `.txt` file. Voor grote teksten is het nodig om deze op te splitsen in kleinere chunks. Dit moet je doen bij bestanden van tientallen tot honderden pagina's. 

```js
import { RecursiveCharacterTextSplitter } from "langchain/text_splitter"
import { TextLoader } from "langchain/document_loaders/fs/text"
import { MemoryVectorStore } from "langchain/vectorstores/memory"

const loader = new TextLoader("./myfile.txt")
const data = await loader.load()
const textSplitter = new RecursiveCharacterTextSplitter({chunkSize: 1500, chunkOverlap: 10})
const splitDocs = await textSplitter.splitDocuments(data)
```


Je kan nu de ingeladen tekstdata omzetten naar vectordata. In dit voorbeeld slaan we de vectordata op in het geheugen.
```js
const vectorStore = await MemoryVectorStore.fromDocuments(splitDocs, embeddings)
```
Vanaf dit moment kan je chatten met je eigen document! In dit voorbeeld wordt de chat history automatisch bijgehouden door langchain.
```js
const memory = new BufferMemory({ memoryKey: "chat_history", returnMessages: true })
const chain = ConversationalRetrievalQAChain.fromLLM(model, vectorStore.asRetriever(), { memory })
const answer = await chain.call({ question: "Waar gaat deze tekst over?" })
console.log(answer)
```


<br><br><br>

## Vector stores

Het doen van prompts (genereren van tokens) kost geld. Een simpele prompt kost bijna niets, maar het maken van een embedding kan sneller oplopen, vooral als je honderden pagina's wil embedden. Om die reden wil je een gemaakte embedding kunnen opslaan. Bekijk [hier een lijst van vectorstores](https://js.langchain.com/docs/integrations/vectorstores) die je via langchain kan gebruiken. In het algemeen kan je dit onderscheid maken:

- *Lokaal bestand*. De vectordata wordt als lokaal bestand binnen je project opgeslagen.
- *Vector database*. Dit is een "echte" database op je systeem, zoals ChromaDB, MongoDB.
- *Cloud database*. Je vectordata staat in de cloud, waardoor het vanuit meerdere projecten online toegankelijk is. Sommige cloud services, zoals [pinecone](https://www.pinecone.io) bieden 1 gratis online vectorstore.

### FAISS

Voor deze les werken we met [FAISS - Facebook Vector Store](https://js.langchain.com/docs/integrations/vectorstores/faiss). Hiermee sla je de vector embeddings lokaal op in een een folder van je project. Let op dat de documentatie van FAISS recent is aangepast.

#### Opdracht

- Maak een embedding van een tekstbestand en sla deze op met [FAISS](https://js.langchain.com/docs/integrations/vectorstores/faiss)
- Maak een nieuwe `.js` file waarin de [FAISS](https://js.langchain.com/docs/integrations/vectorstores/faiss) data wordt ingeladen. Gebruik deze file om vragen over het document te stellen met het `chatgpt` model.



<br><br><br>

## Privacy en copyright

Bij het maken van embeddings verstuur je een document naar OpenAI en/of Microsoft. De kans bestaat hierbij dat jouw data wordt gebruikt om de taalmodellen van OpenAI te verbeteren. Het is daarom belangrijk dat je geen data verstuurt die auteursrechtelijk beschermd is (niet van jouzelf), gevoelige data bevat, of waarvan je simpelweg niet wil dat derden hier eventueel beschikking over krijgen.

De meest veilige oplossing voor dit probleem is om een open source taalmodel op je eigen machine of webserver te installeren. Je data blijft dan altijd binnen je eigen ecosysteem. Voor javascript is de meest eenvoudige oplossing om [OLLama]() te installeren. OLLama heeft een webserver ingebouwd, en je kan [langchain local LLM](https://js.langchain.com/docs/use_cases/question_answering/local_retrieval_qa) gebruiken om embeddings te maken. Ollama werkt *(nog)* niet op windows 😭.

#### Python

Voor python zijn meer opties om lokale LLMs te draaien:

- [Download LLMs met OpenLLM](https://github.com/bentoml/OpenLLM), inclusief webserver en [Langchain integratie](https://python.langchain.com/docs/integrations/llms/openllm)
- [HuggingFace tutorial](https://www.markhneedham.com/blog/2023/06/23/hugging-face-run-llm-model-locally-laptop/) en [6 ways to run a local LLM on your laptop](https://semaphoreci.com/blog/local-llm).
- [Huggingface models in Langchain](https://python.langchain.com/docs/integrations/llms/huggingface_pipelines)

 > *🚨 Voor het draaien van een lokaal LLM heb je een krachtige laptop nodig met een snelle GPU en minimaal 16GB RAM.*

<br><br><br>

 ## Links

- [Langchain Azure OpenAI Text Embedding](https://js.langchain.com/docs/integrations/text_embedding/azure_openai)
- [Langchain document loaders](https://js.langchain.com/docs/modules/data_connection/document_loaders/)
- [FAISS - Facebook Vector Store](https://js.langchain.com/docs/integrations/vectorstores/faiss)