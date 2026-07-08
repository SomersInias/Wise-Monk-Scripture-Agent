# Wise Monk Scripture Agent (LangChain + LangGraph)
 
A showcase project demonstrating how I implemented the information from the course *Foundation: Introduction to LangGraph - Python*, to build a tool-using AI agent with LangChain and LangGraph, centered around a practical and personal use case: a **Wise Monk Scripture Assistant**. Personally I am fascinated by old scriptures like the Tao Te Ching and the timeless wisdom they contain. This seemed like an interesting project to see how these tools could turn a small library of scripture PDFs into a wise conversational guide that always points back to the original texts.
 
## Overview
 
The Wise Monk Agent helps users:
 
* Load a folder of scripture PDFs into a searchable library
* Ask life and wisdom questions in natural language
* Receive answers **grounded in the actual texts**, with the exact passage quoted
* Get clear references (scripture name + PDF page) for every quote
* Add new scriptures to the library at any time
* Search online for extra background about a scripture or its author
This project showcases how an AI agent accessing tools can act as a sort of wise monk: instead of inventing wisdom, it retrieves and cites the real source material.
 
 
## Features
 
### Tool-Using Agent
 
The agent uses LangChain tool calling to dynamically decide when to:
 
* Load all scripture PDFs from a folder into a vector database
* Add a single new scripture PDF to the existing library
* Retrieve relevant passages from the scriptures
* List which scriptures are currently loaded
* Search the web
* Answer wisdom questions with exact, cited quotes
### Scripture Library (PDF Loading)
 
* Loads every PDF in the `scriptures/` folder using `PyPDFLoader`
* Each page becomes a document, so **page numbers are preserved as metadata**
* Every chunk is tagged with the scripture's name (taken from the filename)
* A separate tool allows adding one new PDF later without re-indexing the whole library
### Retrieval-Augmented Generation (RAG)
 
* Converts the scripture library into a vector database
* Uses OpenAI embeddings (`text-embedding-3-large`)
* Chunks with `RecursiveCharacterTextSplitter` (1000 chars, 150 overlap), a good fit for verse-style texts without a fixed separator
* Enables semantic search over all loaded scriptures at once
* The agent can rephrase a personal question into a searchable theme (e.g. "I'm anxious about my future" → "acceptance of the unknown, non-striving")
### Exact Citations
 
* Retrieved passages are returned together with their scripture name and PDF page number
* The system prompt instructs the agent to:
   * Quote the retrieved passage **exactly**, between quotation marks
   * Reference it clearly, e.g. *(Tao Te Ching, PDF page 12)*
   * Never invent quotes — only quote text that was actually retrieved
   * Honestly say when the retrieved passages don't answer the question
### Wise Monk Persona
 
* Calm, warm and humble tone
* Quotes first, then gently explains what the passage means for the user's situation
* Sometimes asks a reflective question back to deepen the user's own thinking
### Web Search Integration
 
* Uses Tavily Search API
* Agent can search online for background about a scripture's history or author (only when explicitly asked)
### Memory (Conversation History)
 
* Implemented using LangGraph MemorySaver
* Enables:
   * Multi-turn conversations
   * Context awareness
   * Persistent interaction per thread (user)
### Agent Loop (LangGraph)
 
Built using a ReAct-style loop:
 
1. User input
2. Agent reasoning
3. Tool execution (if needed)
4. Continue until final answer
### Observability (LangSmith)
 
* Integrated LangSmith Studio for tracing and debugging
* Enables:
   * Inspection of agent decisions and tool calls
   * Step-by-step execution tracking
   * Easier debugging and evaluation of agent behavior
This highlights how the system can be monitored and improved in a production-like environment.
 
## Example Use Cases
 
* "Ask the monk to load your scriptures folder"
* "I struggle with wanting to control everything in my life. What do the old texts say?"
* "What does the Tao Te Ching say about leadership?"
* "Which scriptures do you have in your library?"
* "Ask it to search online who Laozi was"
* "Add this new scripture PDF to your library"
## How It Works
 
1. User sends a message
2. Agent decides:
   * Respond directly
   * OR call a tool
3. Tool executes (e.g. retrieve passages from the vector database)
4. Result is fed back into the agent
5. Agent answers with exact quotes + references
6. Memory stores the interaction
## Getting Started
 
1. Open `wise_monk_agent.ipynb` in Google Colab (or Jupyter)
2. Upload your scripture PDFs to a folder (e.g. `/content/scriptures`)
3. Run the setup cells and enter your API keys (OpenAI, Tavily, LangSmith)
4. Tell the monk where your scriptures live: *"Hello wise one, my scriptures are in /content/scriptures — please read them."*
5. Ask your questions
## Notes
 
* The vector database is in-memory, so the library needs to be re-loaded when the runtime restarts (swap in Chroma with a persist directory for a persistent version)
* PDF page numbers refer to the page index in the PDF file, which can differ from the printed chapter/verse numbering in the original book
