# Week 4

In deze oefening gaan we vragen over een document beantwoorden met een taalmodel. Je werkt in drie losse projecten:

- Voorbereiding: tekst inladen, embedden, model opslaan
- Server: model inladen, vragen beantwoorden met LLM
- Client: vragen sturen vanuit de user interface

<br>

### Inhoud

- Tekstbestand inlezen
- Tekstbestand omzetten naar vectordata
- Vectordata opslaan
- Vectordata inladen en vragen beantwoorden
- Privacy en copyright

<br><br><br>

## Tekstbestand inlezen

Langchain heeft verschillende opties om tekstbestanden, PDF files, .txt files enz. in te lezen. Voor grotere teksten is het nodig om dit op te splitsen in kleinere chunks. Denk aan bestanden van tientallen tot honderden pagina's.

TODO CODE

<br><br><br>

## Tekstbestand omzetten naar vectordata

In het college heb je gezien waarom teksten als `vectordata` worden gebruikt in taalmodellen. Jouw ingeladen tekst moet je dus omzetten naar vectordata. Dit noemen we `embedding`. Om tekst te `embedden` heb je een taalmodel nodig dat *niet* als chat model is getraind. OpenAI gebruikt hier het `ada` model voor. 

TODO VOORBEELD VECTOR

TODO VOORBEELD LOADED TEXT NAAR VECTOR

<br><br><br>

## Vectordata opslaan

Het doen van prompts (genereren van tokens) kost geld. Een simpele prompt kost bijna niets, maar het maken van een embedding kan sneller oplopen, vooral als je honderden pagina's wil embedden. Om die reden wil je een gemaakte embedding kunnen opslaan. Omdat je embedding bestaat uit `vectordata` gebruiken we hier een `vectorstore` voor. Bekijk [hier een lijst van vectorstores](https://js.langchain.com/docs/integrations/vectorstores) die je via langchain kan gebruiken. In het algemeen kan je dit onderscheid maken:

- *Lokaal bestand*. De vectordata wordt als lokaal bestand binnen je project opgeslagen.
- *Vector database*. Dit is een "echte" database op je systeem, zoals ChromaDB, MongoDB.
- *Cloud database*. Je vectordata staat in de cloud, waardoor het vanuit meerdere projecten online toegankelijk is. Sommige cloud services, zoals [pinecone](https://www.pinecone.io) bieden 1 gratis online vectorstore.

Voor deze les werken we met [FAISS - Facebook Vector Store](https://js.langchain.com/docs/integrations/vectorstores/faiss). Hiermee sla je de vector embeddings lokaal op in een een folder van je project. 

***Opdracht***: maak een embedding van een tekstbestand en sla deze op als [FAISS](https://js.langchain.com/docs/integrations/vectorstores/faiss) vectorstore in je project.

<br><br><br>

## Vectordata inladen en vragen beantwoorden

Als het maken van een [FAISS](https://js.langchain.com/docs/integrations/vectorstores/faiss) vectorstore gelukt is kan je dit bestand ook weer inladen. Door deze vectorstore mee te geven bij een ChatLLM (ChatGPT) aanroep, kan het taalmodel hier vragen over beantwoorden.

TODO CODE LLM + EMBEDDING

<br><br><br>

## Privacy en copyright

Bij het maken van embeddings verstuur je een document naar OpenAI en/of Microsoft. De kans bestaat hierbij dat jouw data wordt gebruikt om de taalmodellen van OpenAI te verbeteren. Het is daarom belangrijk dat je geen data verstuurt die auteursrechtelijk beschermd is (niet van jouzelf), gevoelige data bevat, of waarvan je simpelweg niet wil dat derden hier eventueel beschikking over krijgen.

De meest veilige oplossing voor dit probleem is om een open source taalmodel op je eigen machine of webserver te installeren. Je data blijft dan altijd binnen je eigen ecosysteem. Voor javascript is de meest eenvoudige oplossing om [OLLama]() te installeren. OLLama heeft een webserver ingebouwd, en je kan [langchain local LLM](https://js.langchain.com/docs/use_cases/question_answering/local_retrieval_qa) gebruiken. Ollama werkt (nog) niet op windows 😭.

Voor python zijn meer opties: [How to run HuggingFace LLM on your laptop](https://www.markhneedham.com/blog/2023/06/23/hugging-face-run-llm-model-locally-laptop/) en [6 ways to run a local LLM on your laptop](https://semaphoreci.com/blog/local-llm).

 > *🚨 Voor het draaien van een lokaal LLM heb je een krachtige laptop nodig met een snelle GPU en minimaal 16GB RAM.*