# Exercise 1: Exploring the Building Blocks of the Application

### Estimated Duration: 15 minutes

## Lab Scenario

In this exercise, you will review the source code of creative writer application and familiarize yourself with the technologies used. You will learn about the FastAPI framework, how real-time data streaming is handled, and how monitoring and observability tools like OpenTelemetry are integrated. By the end of this exercise, you'll have a solid understanding of the code structure and the different tech stacks that power the application

## Lab Objectives

After you complete this exercise, you will understand:

- Navigate to source code directory
- Know the technology stacks used
- Review source code files

### Task1: Navigate to Source Code Directory

1. In your LabVM desktop, open **Visual Studio Code**.

1. On **Visual Studio Code** pane, select **Open Folder** under **file** menu from top menu.

1. Navigate to `C:\contoso\contoso-creative-writer` directory, click on **Select folder**.

1. Once you have the **contoso-creative-writer** directory opened, ensure you have the source code files from the **explorer pane**

### Task2: Know the Technology Stacks used

In this task, you will familiarize yourself with the various technologies that power the Contoso Creative Writer application. The app leverages a combination of powerful tools and frameworks to help you write well-researched, product-specific articles. As you explore the code, you will learn about the following key technology stacks:

- **FastAPI Framework**

  - ***What It Is:*** FastAPI is a modern, fast (high-performance) web framework for building APIs with Python 3.7+ based on standard Python type hints.

  - ***Why It’s Used:*** FastAPI is chosen for its performance and ease of use. It allows you to quickly create a RESTful API for interacting with the various agents involved in the article-writing process.

  - ***Reference :*** [FastAPI](https://fastapi.tiangolo.com/)

- **Prompty**

  - ***What It Is:*** Prompty is a library that allows you to manage, create, and evaluate prompts for language models like GPT-3. It simplifies the process of interacting with AI models by providing better prompt management tools.
    
  - ***Why It’s Used:*** Prompty helps in organizing and refining prompts to guide AI agents effectively, ensuring that each step in the article creation process (research, writing, editing) is powered by accurate and relevant inputs.

  - ***Reference :*** [Prompty](https://prompty.ai/)

- **Bing Search API**

  - ***What It Is:*** The Bing Search API allows you to programmatically access Bing search results. It’s useful for gathering information from the web, such as research materials related to the article topic.

  - ***Why It’s Used:*** In the Contoso Creative Writer app, the Bing Search API is used by the research agent to gather relevant information about the topic provided by the user.

  - ***Reference :*** [Bing Search API](https://www.microsoft.com/en-us/bing/apis)

- **Azure OpenAI**

  - ***What It Is:*** Azure OpenAI provides access to OpenAI's powerful models, including GPT-3, which can be used for a variety of tasks such as natural language understanding, generation, and more.

  - ***Why It’s Used:*** In this app, Azure OpenAI powers the various AI agents that handle tasks like research, product matching, article writing, and editing.

  - ***Reference :*** [Azure OpenAI](https://azure.microsoft.com/en-us/products/ai-services/openai-service)

- **Azure AI Search**

  - ***What It Is:*** Azure AI Search (formerly known as Azure Cognitive Search) is a cloud search service with built-in AI capabilities to help you extract insights from data, such as searching for related products using semantic similarity.

  - ***Why It’s Used:*** The product agent uses Azure AI Search to find products related to the research topic by performing a semantic similarity search in a vector store, improving the relevance of product recommendations.

  - ***Reference :*** [Azure AI Search](https://learn.microsoft.com/en-us/azure/search/search-what-is-azure-search)

### Task3: Review source code files [Read-Only]

1. From the explorer menu of **visual Studio Code**, navigate to `/src/api/main.py` file and review the codes.

1. The first part of the file imports various libraries and modules used throughout the application.

   ```python
   import os
   from pathlib import Path
   from fastapi import FastAPI
   from dotenv import load_dotenv
   from prompty.tracer import trace
   from prompty.core import PromptyStream, AsyncPromptyStream
   from fastapi.responses import StreamingResponse
   from fastapi.middleware.cors import CORSMiddleware
   from opentelemetry.instrumentation.fastapi import FastAPIInstrumentor
   from tracing import init_tracing
   from orchestrator import Task, create
   ```
   
   >**os and Path:** Used for file path handling and environment variable management.

   >**FastAPI:** The web framework used to build the API.

   >**dotenv:** Loads environment variables from a .env file.

   >**prompty:** A library for managing and evaluating prompts with OpenAI.

   >**StreamingResponse:** Used for streaming data from the server to the client.

   >**CORSMiddleware:** Middleware to handle Cross-Origin Resource Sharing (CORS).

   >**FastAPIInstrumentor:** Part of OpenTelemetry for instrumenting the FastAPI app with tracing.

   >**tracing and orchestrator:** Custom modules for tracing setup and task orchestration.

1. The next part of the file focus on loading environment variables and initializing **FastAPI** application.

   ```python
   base = Path(__file__).resolve().parent
   load_dotenv()
   tracer = init_tracing()
   app = FastAPI()
   ```
   >**load_dotenv():** This function loads the environment variables from a .env file into the Python environment. It ensures that sensitive information like API keys and database credentials are stored securely and can be accessed within the app.

   >**init_tracing():** Initializes the tracing system for monitoring and debugging, ensuring that requests and interactions can be traced across services.

   >**FastAPI():** This line initializes the FastAPI app, which will handle HTTP requests and route them to the appropriate functions.

1. In the next part, there is a setup of  CORS (Cross-Origin Resource Sharing) middleware.

   ```python
   code_space = os.getenv("CODESPACE_NAME")
   app_insights = os.getenv("APPINSIGHTS_CONNECTIONSTRING")

   if code_space: 
       origin_8000= f"https://{code_space}-8000.app.github.dev"
       origin_5173 = f"https://{code_space}-5173.app.github.dev"
       ingestion_endpoint = app_insights.split(';')[1].split('=')[1]
    
       origins = [origin_8000, origin_5173, os.getenv("API_SERVICE_ACA_URI"), os.getenv("WEB_SERVICE_ACA_URI"), ingestion_endpoint]
   else:
       origins = [
           o.strip()
           for o in Path(Path(__file__).parent / "origins.txt").read_text().splitlines()
       ]
       origins = ['*']
   ```

   >**CORS Middleware** is used to allow or restrict cross-origin requests (requests coming from other domains).

1. The next part creates the API endpoint for handling article creation in the application.

   ```python
   @app.post("/api/article")
   @trace
   async def create_article(task: Task):
       return StreamingResponse(
           PromptyStream(
               "create_article", create(task.research, task.products, task.assignment)
           ),
           media_type="text/event-stream",
       )
   ```

   >**POST /api/article:** This endpoint accepts a POST request to create an article based on the task details. It uses the PromptyStream to handle real-time streaming of the article creation process.



