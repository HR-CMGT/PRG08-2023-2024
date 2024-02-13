# Function calling

Je kan een LLM gebruiken om functies in je code aan te roepen. Het LLM is dan eigenlijk de interface tussen de gebruiker en de code. Je kan bijvoorbeeld een game besturen via tekst, of het weer opvragen door te praten tegen je LLM.

#### 🚫 Azure

> *Function calling (a.k.a. "tools") werkt in ChatGPT 3.5 met de laatste update. Omdat Azure een versie achter loopt werkt het nog niet met de Azure OpenAI keys.*

<br><br><br>

```js
import { ChatOpenAI } from "@langchain/openai";


//
// voorbeeld functie die het weerbericht ophaalt. hier zou je een fetch in kunnen plaatsen.
//
global.getCurrentWeather = (location) => {
    if (location.toLowerCase().includes("tokyo")) {
        return { location, temperature: "10 c" };
    } else if (location.toLowerCase().includes("san francisco")) {
        return { location, temperature: "72 f" };
    } else {
        return { location, temperature: "22 c" };
    }
}

//
// geef de functiebeschrijving mee bij het aanmaken van het model. versie moet 1106 of hoger zijn.
//
const chat = new ChatOpenAI({
    modelName: "gpt-3.5-turbo-1106",
    maxTokens: 128,
    openAIApiKey: process.env.OPENAI_API_KEY,
    response_format: { type: "json_object" },
}).bind({
    tools: [
        {
            type: "function",
            function: {
                name: "getCurrentWeather",
                description: "Get the current weather in a given location",
                parameters: {
                    type: "object",
                    properties: {
                        location: {
                            type: "string",
                            description: "The city and state, e.g. San Francisco, CA",
                        },
                    },
                    required: ["location"],
                },
            },
        },
    ],
    tool_choice: "auto", 
});

//
// het taalmodel begrijpt dat de getweather functie drie keer aangeroepen moet worden met als parameter de locatie
//
const res = await chat.invoke([
    ["human", "What's the weather like in San Francisco, Tokyo, and Paris?"],
]);

// check welke functies volgens het llm aangeroepen moeten worden
console.log(res.additional_kwargs.tool_calls);
```
<br>

## Functies aanroepen

Het chatmodel geeft alleen aan welke functies je met welke parameters moet aanroepen. Je voert de functies uit door de namen en parameters uit `additional_kwargs_tool_calls` te halen:

```js
for(let tool of res.additional_kwargs.tool_calls) {
    
    const fn = tool.function.name                          // de functie
    const args = JSON.parse(tool.function.arguments)       // de parameters

    if (fn in global && typeof global[fn] === "function" && args) {
        let res_weather = global[fn](args.location);
        console.log(`The weather in ${res_weather.location} is ${res_weather.temperature}`)
    } else {
        console.log("could not find " + fn);
    }
}
```

<br>

## Links

- [OpenAI Tools and function calling](https://platform.openai.com/docs/guides/function-calling)
- [OpenAI LangChain Function Calling](https://js.langchain.com/docs/integrations/chat/openai)