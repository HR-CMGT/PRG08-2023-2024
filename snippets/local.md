# Lokaal LLM

Als je zeker wil weten dat je data privé blijft, op je eigen machine, dan kan je een open source taalmodel installeren. Je data blijft dan altijd binnen je eigen ecosysteem. Voor javascript is de meest eenvoudige oplossing om [OLLama](https://ollama.ai) te installeren. OLLama heeft een webserver ingebouwd, en je kan [langchain local LLM](https://js.langchain.com/docs/use_cases/question_answering/local_retrieval_qa) gebruiken om embeddings te maken. Ollama werkt *(nog)* niet op windows 😭.

 > *🚨 Voor het draaien van een lokaal LLM heb je een krachtige laptop nodig met een snelle GPU en minimaal 16GB RAM.*

#### Python

Op windows heb je python nodig om een lokaal LLM te draaien.

- [Download LLMs met OpenLLM](https://github.com/bentoml/OpenLLM), inclusief webserver en [Langchain integratie](https://python.langchain.com/docs/integrations/llms/openllm)
- [HuggingFace tutorial](https://www.markhneedham.com/blog/2023/06/23/hugging-face-run-llm-model-locally-laptop/) en [6 ways to run a local LLM on your laptop](https://semaphoreci.com/blog/local-llm).
- [Huggingface models in Langchain](https://python.langchain.com/docs/integrations/llms/huggingface_pipelines)
