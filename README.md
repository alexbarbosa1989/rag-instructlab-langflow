# rag-instructlab-langflow
Basic RAG test using Langflow and Instructlab

## Requirements

This test make a basic RAG implementation using:
- [Langflow 1.1.0](https://github.com/langflow-ai/langflow) as IDE/App builder.
- [Instructlab 0.21.0 + granite-7b-lab-Q4_K_M.gguf](https://github.com/instructlab/instructlab) as LLM agent.
- [ChromaDB 1.9.4](https://github.com/chroma-core/chroma) for Vector data storage.
- [Ollama 0.4.4 + mxbai-embed-large](https://ollama.com/blog/embedding-models) model for Vector embedding process in the Chroma Database
- Python 3.11

For this test, it is required to have running the Langflow and the Instructlab instaces. For installation process I strongly recommend folow each project setup process described in their respective GitHub project repository:
- Langflow istanllation: https://github.com/langflow-ai/langflow?tab=readme-ov-file#-quickstart
- Instructlab: https://github.com/instructlab/instructlab#-getting-started
- Ollama Embedding Models: https://ollama.com/blog/embedding-models
- Chroma doesn't require any installation process. The files are automatically created in the path that is set in the Langflow schema.

## Starting the environment:

### Langflow 
- Open a terminal session and activate the python environment for Langflow:
~~~
$ source venv-langflow/bin/activat
~~~
- Start the Langflow instance:
~~~
$ langflow run
~~~
This acction will show the URL access point for the running instance:
~~~
 Access http://127.0.0.1:7860           
~~~

### Instructlab
- In a different termina session activate the python environment for Instructlab:
~~~
$ source venv/bin/activate
~~~
- [Download the model](https://github.com/instructlab/instructlab?tab=readme-ov-file#-download-the-model) (If haven't already downloaded the Granite model):
~~~
ilab model download --repository instructlab/granite-7b-lab-GGUF --filename granite-7b-lab-Q4_K_M.gguf --hf-token <your-huggingface-token>
~~~
- Serve the model:
~~~
$ ilab model serve
~~~
This acction will show also the URL access point for the running instance:
~~~
... instructlab.model.backends.llama_cpp:233: After application startup complete see http://127.0.0.1:8000/docs for API.
~~~

### Ollama Embedding
- [Download Ollama](https://ollama.com/download):
~~~
$ curl -fsSL https://ollama.com/install.sh | sh
~~~
- Download the embedding model:
~~~
$ ollama pull mxbai-embed-large
~~~

## Test the Granite Model without any traninig

- Having the granite-lab model served in instructlab, in a separated terminal session start a chat session:
~~~
$ source venv/bin/activate
~~~
~~~
$ ilab model chat
~~~
- Then ask for recent information on a particular topic. Current test is asking for Bucaramanga city information:
~~~
>>> who is the current mayor of Bucaramanga?
~~~
- Since I'm ask directly to the served model without any previous training, it will provide unaccurated information (an allucination):
~~~
╭───────────────────────────────────────────────────────────────────── granite-7b-lab-Q4_K_M.gguf ─────────────────────────────────────────────────────────────────────╮
│ The current mayor of Bucaramanga is Gabriel Gomez. He was inaugurated on June 21, 2016, and is serving his second term as the head of the city's government. As the  │
│ mayor, Gomez leads the Municipal Administration of Bucaramanga (AMBU), which is responsible for managing the city's affairs and ensuring the well-being of its       │
│ residents.                                                                                                                                                           │
│ ...
~~~

Therefore, here is where RAG can be used for this purpsose.

## Build RAG in LangFlow

### Import the RAG project in Langflow
Access the LangFlow builder URL `http://127.0.0.1:7860` in a browser and import the `BasicRAG-URL.json` file which contains the built flow for the RAG test. 
There are two main flows:
1. For Vector Database population:
   ![Screenshot From 2024-11-26 11-57-05](https://github.com/user-attachments/assets/a3aae1ec-aca6-4c9f-9a92-d769432f1a75)

2. For Model interaction:
   ![Screenshot From 2024-11-26 11-59-26](https://github.com/user-attachments/assets/783bbe31-f16e-42eb-950d-dd1e8ef379f1)

### Populate the vector database
This is the first step that should be executed. This is the workflow:
- Define an URL that will be used as base content. In this case I set the [Wikipedia entry](https://en.wikipedia.org/wiki/Bucaramanga) for Bucaramanga City. This initial data sosurce could be also a local document, such as a PDF, text file, etc., if don't want to use online data.
- Set a Recursive Character Text Splitter to separate the document (web page) data into a set of chunks
