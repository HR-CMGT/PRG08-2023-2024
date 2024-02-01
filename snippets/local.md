# Models

In de online community verschijnen dagelijks nieuwe models. Om hiermee te werken zal je het moeten downloaden, of hosten. Elk model kan een specifiek doel hebben, bv:
- Geitje (goed in Nederlands)
- Tolkien (goed in fantasy stories)
- CodeLLama (goed in programmeren)

<br>

## Lokaal LLM

Als je zeker wil weten dat je data privé blijft, op je eigen machine, dan kan je een open source taalmodel installeren. Op MacOS/Linux kan je [OLLama](https://ollama.ai) installeren. OLLama heeft een webserver ingebouwd, en werkt met [langchain](https://js.langchain.com/docs/use_cases/question_answering/local_retrieval_qa). Voor windows heb je python en [OpenLLM]() nodig.

 > *🚨 Voor het draaien van een lokaal LLM heb je een krachtige laptop nodig met een snelle GPU en minimaal 16GB RAM. Zelfs dan moet je opletten dat je alleen kleine modellen download (1B of 7B versies). Professionele modellen zoals [Falcon](https://huggingface.co/tiiuae/falcon-180B) hebben 400GB RAM nodig en zijn dus niet werkbaar op een laptop.*

<br>

## Huggingface hosting

[Huggingface biedt hosting en javascript endpoints voor elk LLM model](https://huggingface.co/blog/inference-endpoints-llm).

<br>

## Links

- [Download LLMs met OpenLLM](https://github.com/bentoml/OpenLLM), inclusief webserver en [Langchain integratie](https://python.langchain.com/docs/integrations/llms/openllm)
- [HuggingFace tutorial](https://www.markhneedham.com/blog/2023/06/23/hugging-face-run-llm-model-locally-laptop/) en [6 ways to run a local LLM on your laptop](https://semaphoreci.com/blog/local-llm).
- [Huggingface models in Langchain](https://python.langchain.com/docs/integrations/llms/huggingface_pipelines)
