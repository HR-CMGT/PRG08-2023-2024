# Week 3

## Langchain en Language models

Deze week ga je je langchain kennis verder uitbreiden om betere LLM calls te kunnen maken. 

Je kan zelf bepalen welke onderwerpen voor jou belangrijk zijn. In de beoordeling ga je aantonen hoe ver je gekomen bent. Bekijk de cursushandleiding om te zien hoe je punten verdient.

Inhoud:

- Verschil taalmodel (LLM) en chat model (ChatLLM)
- Prompt engineering
- System instructions en chat roles
- Chat history
- Fake llm
- Koppeling met externe services
- Streaming
- Documentatie
- Andere LLM's en LLM api's

<Br><br><br>

## LLM en ChatLLM

Een taalmodel is getraind om het volgende woord in een zin te voorspellen. Een chatmodel doet dit ook, maar is doorgetraind om te begrijpen hoe een conversatie tussen een persoon en een AI assistent kan verlopen. Een chat model kan een chat historie begrijpen en toepassen. Het hangt af van het doel van jouw applicatie welk model beter geschikt is.

<Br><br><br>

## Prompt engineering

Dit houdt in dat je de vraag van de eindgebruiker uitbreidt met extra taalinstructies, om zo een beter resultaat terug te krijgen van het model. Bijvoorbeeld:

```js
let userQestion = `Tell me how to take care of my banana plant`
let promptTemplate = `Answer the following question: ${userQuestion} as short as you can.`
let res = await model.invoke(promptTemplate)
```

- [Prompt Engineering](https://platform.openai.com/docs/guides/prompt-engineering)
- [Langchain Functies](https://js.langchain.com/docs/modules/model_io/prompts/quick_start) om prompt templates te schrijven.*

<br><bR><br>

## System instructions en chat roles

Een chat model maakt onderscheid in de rol van degene die het bericht stuurt:

- `system` : Dit is een systeem instructie waarmee je kan aangeven hoe het chat model zich moet gedragen.
- `human` : Dit is het bericht van de eindgebruiker.
- `ai` (of `assistant`) : Hiermee kan je aangeven hoe het chat model zelf heeft gereageerd (of zou moeten reageren) op een vraag.

In dit voorbeeld sturen we een heel gesprek en system instructions naar het taalmodel, zodat het zal antwoorden in dezelfde toon:

```js
const res = await model.invoke([
    ["system", "You are a grumpy assistant who always answers with very few words. You end every sentence with complaining that the question was too easy."],
    ["human", "What is the solar system?"],
    ["assistant", "What a simple question! It's just the sun and a few planets!"],
    ["human", "What is the the biggest star in our solar system?"]
])
console.log(res.content)
```

Langchain biedt classes voor `HumanMessage`, `AIMessage` en `SystemMessage` zodat je hier minder snel typfouten in maakt:

```js
import { HumanMessage, SystemMessage } from "langchain/chat_models/messages";

const messages = [
  new SystemMessage("You're a helpful assistant"),
  new HumanMessage("What is the purpose of studying AI?"),
];
```

<Br><br><br>

## Chat history

De chat history is belangrijk omdat het taalmodel zelf niet onthoudt wat het eerder tegen je gezegd heeft. Zonder chat history kunnen vragen zoals `Wat bedoel je daarmee` niet goed beantwoord worden. Je moet dus bij elk nieuw chatbericht de hele chat history mee sturen. 

In dit code voorbeeld maken we een array met de history. Vragen van de gebruiker, en antwoorden van de chatbot worden met de juiste `role` toegevoegd.

```js
let messages = [
    ["system", "You are a neanderthal assistant. End every sentence with a different random grunt"],
    ["human", "How is the solar system composed?"],
]
const chat1 = await model.invoke(messages)
console.log(chat1.content)
```
Het antwoord van de ai voeg je toe aan de `messages` array met de juiste rol, gevolgd door een nieuwe vraag. Je geeft de hele array door aan het taalmodel.
```js
messages.push(
    ["ai", chat1.content],
    ["human", "Can you explain that a bit more?"]
)
const chat2 = await model.invoke(messages)
console.log(chat2.content)
```

> *🚨 Langchain biedt een [Chat Memory](
https://js.langchain.com/docs/modules/memory/types/buffer_memory_chat) class om automatisch de chat history bij te houden!*

<br><br><br>

### Fake LLM

Als je onderdelen van de app aan het testen bent die niet met het taalmodel te maken hebben, is het beter om in `server.js` de [Langchain Fake LLM](https://js.langchain.com/docs/integrations/chat/fake) aan te roepen in plaats van OpenAI, omdat je dan tokens bespaart.

```js
import { FakeListChatModel } from "@langchain/core/utils/testing"

const chat = new FakeListChatModel({
    responses: ["Maybe later.", "Not right now"],
})

const res1 = await chat.invoke("Tell me a JavasSript joke!")
console.log(res.content)
```
[Documentatie Fake LLM](https://js.langchain.com/docs/integrations/chat/fake)


<Br><br><br>

## Koppeling met externe services

Het kan heel interessant zijn om externe services zoals Spotify, Openweather, News, etc. te koppelen aan een taalmodel. Je kan dan bijvoorbeeld de hele user interface in taal uitvoeren, of je kan het LLM laten klagen over het weer. 

*Pseudo code voorbeeld:*
```js
let weather = await fetch("http://api.openweathermap.org/forecast")
let temperature = weather.today.temp
let chatresult = model.invoke(`Complain about the temperature of ${temperature} degrees`)
```
💬 Je kan `text-to-speech` gebruiken om een chat uit te spreken. [De browser heeft dit ingebouwd!](./snippets/speech.md)

<Br><br><br>

## Streaming

Het kan tijd kosten voordat je een volledig antwoord van een LLM terug krijgt. Vooral bij hele lange antwoorden *(zoals de samenvatting van een boek)* kan het lijken of je user interface niet meer reageert. Om een betere user experience te creeëren kan je streaming gebruiken. Je krijgt dan woord-voor-woord een antwoord terug.

```js
const stream = await model.stream("Write an introduction for a book about a colony of tiny hamsters.")
for await (const chunk of stream) {
    console.log(chunk.content)
}
```
### Streaming in node express

Deze stream werkt in de `node` omgeving, maar nu moet je de response ook als stream terug sturen naar de browser. Dit kan met een `readablestream` en `fetch`.

[Tutorial readablestream en fetch](https://www.loginradius.com/blog/engineering/guest-post/http-streaming-with-nodejs-and-fetch-api/)




<Br><br><br>

## Documentatie

- [LangChain](https://js.langchain.com/docs/get_started/quickstart)
- [Prompt Engineering](https://platform.openai.com/docs/guides/prompt-engineering)
- [Annuleren van een OpenAI call](https://js.langchain.com/docs/modules/model_io/llms/cancelling_requests)
- [Tokens bijhouden](https://js.langchain.com/docs/modules/model_io/llms/token_usage_tracking)
- [Omgaan met errors](https://js.langchain.com/docs/modules/model_io/llms/dealing_with_api_errors)

<Br><br><br>

## Andere LLM's en LLM API's

Met de [Langchain library](https://js.langchain.com/docs/integrations/platforms) kan je naast OpenAI ook connectie maken met andere [LLM's](https://js.langchain.com/docs/integrations/llms/) en [ChatLLM's](https://js.langchain.com/docs/integrations/chat/).

Voor de meeste van deze API's heb je een API key nodig van de desbetreffende provider. Je kan op eigen gelegenheid testen waar je een API key kan aanmaken, en vervolgens dit taalmodel gebruiken in Langchain.

Populaire LLM API's zijn *LLama API, Mistral API, Anthropic Claude*.