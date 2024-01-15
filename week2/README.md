# Week 2

- Introductie taalmodellen
- Werken met OpenAI API
- Webproject opzetten

<br><br><br>

## Taalmodellen

Zie de presentatie

<br><br><br>

## Werken met de OpenAI API

We gaan werken met de Azure variant van de OpenAI API.

- Maak een map genaamd `server`
- Installeer de langchain library via `npm install langchain`
- Maak een `.env` file aan, plaats hierin de API keys uit de les.
- Test of je in `node.js` de keys uit je `.env` file kan lezen met `console.log(process.env.YOUR_KEY)`. Zie de code snippets hieronder voor het werken met `.env` files.
- Als dat werkt kan je testen of je een chat aanroep kan doen met de langchain voorbeeldcode.

<br>

### .env gebruiken

De nieuwste nodeJS heeft auto import via de command line. 
```
node --env-file=.env mijntestje.js
console.log(process.env.OPENAI_API_KEY)
```
Met `dotenv` kan je een `.env` file importeren
```
npm install dotenv
console.log(process.env.OPENAI_API_KEY)
```
> *Langchain heeft *auto import* als je keys exact de goede naam hebben. Als je met [bun](https://bun.sh) werkt heb je geen `dotenv` nodig.*

<br>

### Langchain Chat aanroep

Test of je een chat kan starten met OpenAI via Langchain.

```js
import { ChatOpenAI } from "@langchain/openai"

const model = new ChatOpenAI({
    azureOpenAIApiKey: KEY, 
    azureOpenAIApiVersion: VERSION, 
    azureOpenAIApiInstanceName: INSTANCE, 
    azureOpenAIApiDeploymentName: MODEL, 
})
let messages = [["human", "How is the solar system composed?"]]
const result = await model.invoke(messages)
console.log(result)
```
<br><br><br>

## Webproject opzetten

Als je de aanroep van langchain en het werken met `.env` files werkend hebt, kan je de werkomgeving voor de inleveropdracht gaan opzetten.

- Plaats een client map naast de server map.
- In de client map plaats je je HTML+CSS+JS frontend.

```
SERVER
├── .env
├── server.js
CLIENT
├── index.html
├── script.js
└── style.css
```
<br>

### Node express

We gaan de langchain code geschikt maken om vanuit een web client op te vragen.

In PRG06 heb je geleerd te werken met node express. Bouw naar eigen inzicht een node express server waarmee je jouw langchain functie kan aanroepen.

- Maak een express aanroep die een `request` via `GET` of `POST` kan ontvangen.
- Roep vervolgens je langchain chat functie aan.
- Het resultaat geef je terug als JSON in de `response`.

```js
import cors from 'cors'
import express from 'express'

const app = express()
app.use(cors())

app.get('/', async(req, res) => {
    let chat = await myCoolFunction() // jouw langchain function hier
    res.json(chat)
})

app.listen(3000, () => console.log(`app luistert naar port 3000!`))
```

> *Als je [bun](https://bun.sh) gebruikt kan je `bun --watch server.js` gebruiken om automatisch te herstarten als je server code is aangepast. Je kan ook `nodemon` gebruiken.*
 
<br>

### Frontend in HTML

- In de client map ga je een user interface bouwen met HTML + CSS. De gebruiker kan hier bijvoorbeeld een vraag stellen in een invoerveld.
- In de frontend javascript gebruik je `fetch` met `GET` of `POST` om je server aan te roepen.
- Het resultaat toon je vervolgens weer aan de gebruiker in een chat interface.
- Als je de UI aan het testen bent is het beter om op je server even de `Fake LLM` aan te roepen in plaats van OpenAI, omdat je dan tokens bespaart.

<br><br><br>

## Links

- [Langchain Azure instellingen](https://js.langchain.com/docs/integrations/chat/azure)
- [Langchain Fake LLM](https://js.langchain.com/docs/integrations/chat/fake)
- [Node Express Hello World](https://expressjs.com/en/starter/hello-world.html)
- [JSON teruggeven vanuit Express](https://expressjs.com/en/5x/api.html#res.json)