# Week 2

In deze oefening gaan we het basis webproject opzetten dat je nodig hebt voor beide inleveropdrachten. Voor inleveropdracht 1 gaan we de Azure OpenAI API aanroepen met de Langchain library. 

> *Voor een uitleg van deze les en het werken met de `.env` file, zie de slides van week 2.*

<br><br><br>

## Het webproject opzetten

- Maak een nieuw project aan met de volgende structuur:

```
SERVER
├── .env
├── .gitignore
├── server.js
CLIENT
├── index.html
├── script.js
└── style.css
```

### Client

Voor nu plaats je alleen een "hello world" in de index.html. In `script.js` plaats je `console.log("hello world")`.

### Server

- Installeer [NodeJS 20.11 LTS](https://nodejs.org/en). 

#### Werken met de `.env` file

- Maak een `.env` file aan, plaats hierin de API keys uit de les.
- Let op dat je de `.env` file toevoegt aan je `.gitignore`, zodat deze niet in je github repository terecht komt.

#### .env

```sh
OPENAI_API_TYPE=___
OPENAI_API_VERSION=___
OPENAI_API_BASE=___
AZURE_OPENAI_API_KEY=___
DEPLOYMENT_NAME=___
ENGINE_NAME=___
INSTANCE_NAME=___
```

> *🚨 Deel de API keys en de ENV file niet met anderen. Plaats de API keys niet online.*

#### API keys lezen

Met *Node 20.11 LTS* kan je de `.env` file uitlezen als je deze meegeeft in je aanroep.

```sh
node --env-file=.env server.js
```
Test of het inlezen van je `.env` is gelukt:
```js
console.log(process.env.AZURE_OPENAI_API_KEY)
```


<br><br><br>

## Langchain

[Langchain](https://js.langchain.com/docs/get_started/introduction) is de API die we in `server.js` gaan gebruiken om te werken met Azure OpenAI (ChatGPT). 

```sh
npm install langchain
npm install @langchain/openai
```
Om de OpenAI API's te kunnen aanroepen heb je de API keys uit je `.env` file nodig. Vervolgens kan je een vraag stellen aan het chat model. Test of dit werkt! 
```js
import { ChatOpenAI } from "@langchain/openai"

const model = new ChatOpenAI({
    azureOpenAIApiKey: process.env.AZURE_OPENAI_API_KEY, 
    azureOpenAIApiVersion: process.env.OPENAI_API_VERSION, 
    azureOpenAIApiInstanceName: process.env.INSTANCE_NAME, 
    azureOpenAIApiDeploymentName: process.env.MODEL_CHAT, 
})

const joke = await model.invoke("Tell me a Javascript joke!")
console.log(joke.content)
```

Om de code geschikt te maken voor de volgende stap kan je de `invoke` call alvast in een `async function` plaatsen.

<br>

### Eigen OpenAI KEY gebruiken

Als je zelf een key hebt voor OpenAI mag je die ook gebruiken. Het aanmaken van het model ziet er dan iets anders uit. Afhankelijk van je abonnement kan je hier `gpt-3.5-turbo` of `gpt-4` gebruiken. [Je kan hier een eigen key aanvragen](https://platform.openai.com/docs/introduction).

```js
import { ChatOpenAI } from "@langchain/openai"

const model = new ChatOpenAI({
    modelName: "gpt-3.5-turbo",
    openAIApiKey: process.env.OPEN_AI_KEY
})
```

<br>

### Troubleshooting

📃 De documentatie van Langchain en OpenAI verandert regelmatig. Als de voorbeeldcode uit deze repository een warning geeft moet je de officiële documentatie nalezen.

<br><br><br>


## Express

We gaan de langchain code geschikt maken om vanuit een web client op te vragen.

In PRG06 heb je geleerd te werken met node express. Dit ga je gebruiken om de OpenAI code vanuit je frontend te kunnen aanroepen.

- Maak een express aanroep die een `request` via `GET` of `POST` kan ontvangen.
- Roep vervolgens je langchain chat functie aan.
- Het resultaat geef je terug als JSON in de `response`.

```js
import cors from 'cors'
import express from 'express'
import bodyParser from 'body-parser'

const app = express()
app.use(cors())
app.use(bodyParser.json())
app.use(bodyParser.urlencoded({ extended: false }))

// voorbeeld GET request
app.get('/asksomething', async(req, res) => {
    // roep hier je langchain function aan 
    // gebruik de GET / POST data uit het request als prompt
    // geef het chat result terug als JSON
    let chat = await myLangchainFunction() 
    res.json(chat)
})

app.listen(3000, () => console.log(`app luistert naar port 3000!`))
```
<br>

#### Werken met nodemon 👺

Je kan `nodemon` gebruiken zodat je express server automatisch herstart als je een wijziging hebt gemaakt. 
```sh
npm install nodemon
```
Het nodemon commando kan je het beste in je `scripts` plaatsen in je `package.json`:

```json
"scripts": {
    "mycoolproject" : "nodemon server.js"
},
```
```sh
npm run mycoolproject
```

#### Server testen

- Gebruik [Hoppscotch](https://hoppscotch.io), de VS Code extension [Thunder Client](https://www.thunderclient.com) of [Postman](https://www.postman.com/downloads/) om je server calls te testen.


 
<br><br><br>

### HTML Frontend

- In de client map ga je een user interface bouwen met HTML + CSS. De gebruiker kan hier een vraag stellen in een invoerveld.
- In de frontend javascript gebruik je `fetch()` met `GET` of `POST` om je server aan te roepen met de invoer uit het invoerveld. Zie hiervoor de lessen PRG3 en PRG6.
- ⚠️ ***Belangrijk***: zorg dat je interface disabled is zo lang er nog geen antwoord terug is gekomen. De submit button is disabled. Toon via een `loading spinner` dat de app bezig is.
- Het resultaat toon je vervolgens weer aan de gebruiker in de user interface. Enable de submit button en verwijder de loading spinner.
- Om de UI logica te testen kan je node server ook een fake message teruggeven, in plaats van telkens een call naar OpenAI te doen.

<br><br><br>

### Expert level: live zetten

Voor de lessen en inleveropdrachten kan je jouw frontend en backend lokaal draaien op je eigen computer. Voor het expert level kan je jouw project live zetten op je HR studenthosting. Er zijn ook online node hosting providers te vinden zoals `vercel.com`, `netlify.com`, `codesandbox.com`, `github codespaces`, `huggingface spaces`, `stackblitz.com`, `deno.com`, `amazon serverless webservices`, etc.

<br><Br><br>

## Links

- [LangChain](https://js.langchain.com/docs/get_started/quickstart)
- [Langchain Azure instellingen](https://js.langchain.com/docs/integrations/chat/azure)
- [Node Express Hello World](https://expressjs.com/en/starter/hello-world.html)
- [JSON teruggeven vanuit Express](https://expressjs.com/en/5x/api.html#res.json)
- [Voorbeeld fetch met POST](https://jasonwatmore.com/post/2021/09/05/fetch-http-post-request-examples)

In de lessen benaderen we Azure OpenAI via LangChain. Je kan hier meer lezen over Azure en OpenAI.

- [Eigen OpenAI key aanvragen](https://platform.openai.com/docs/introduction)
- [OpenAI API](https://platform.openai.com/docs/introduction)
- [Azure REST API](https://learn.microsoft.com/en-gb/azure/ai-services/openai/reference)