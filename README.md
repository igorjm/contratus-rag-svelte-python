# Contratus AI - Contract Query System

A modern system for semantic processing and querying of contracts, using Python, Pinecone, and SvelteKit. It allows semantic search and natural language questions about contracts.

## Project Structure

```
├── api_pinecone.py       # Main API with FastAPI
├── api_upload.py         # API for contract uploads
├── llm_router.py         # Router for LLM-based questions
├── pinecone_utils.py     # Pinecone utilities
├── processar_contrato.py # Contract processing
├── shared.py             # Shared functions and models
├── contratos/            # Directory to store contracts
├── frontend/             # SvelteKit application
├── diagrama_modulos.md   # Diagram of module relationships
└── .env                  # Environment variables
```

For a detailed view of how the backend modules are related, refer to the file [diagrama_modulos.md](diagrama_modulos.md).

- **Backend (Python + FastAPI)**

  - `processar_contrato.py`: Automatic processing of PDFs and embedding generation
  - `api_pinecone.py`: REST API for semantic contract search using Pinecone
  - `api_upload.py`: API for uploading and automatically processing new contracts
  - `pinecone_utils.py`: Utility library for interacting with Pinecone
  - `llm_router.py`: Router for processing questions with LLM
  - `shared.py`: Shared functions across modules

- **Frontend (SvelteKit)**
  - Modern and responsive interface with Tailwind CSS and DaisyUI
  - Semantic search in natural language
  - Contract visualization
  - Question mode for natural language queries

## Backend Code Explanation

### 1. `pinecone_utils.py`

**Purpose**: Utility library for interacting with Pinecone (vector database).

**Key functionalities**:

- Connecting to Pinecone
- Generating embeddings using OpenAI (model `text-embedding-3-small`)
- Document indexing
- Semantic document search
- Listing documents in the index

This file serves as a library of helper functions and does not need to be executed directly.

### 2. `processar_contrato.py`

**Purpose**: Processing PDF contracts and indexing them in Pinecone.

**Key functionalities**:

- Loads PDF files using LangChain
- Splits documents into smaller chunks
- Generates embeddings for each chunk
- Indexes the chunks in Pinecone with metadata
- Can process a single contract or all contracts in a folder

This file can be executed directly to process contracts:

- To process all contracts: `python processar_contrato.py`
- To process a specific contract: `python processar_contrato.py path/to/contract.pdf`

### 3. `api_pinecone.py`

**Purpose**: REST API for semantic contract search using FastAPI.

**Key functionalities**:

- Endpoint to list all contracts
- Endpoint for semantic search with natural language queries
- Endpoint to list unique files in the index
- Manages connection to Pinecone
- Integrates with the LLM router for natural language questions

This file starts a web server on port 8000 when executed: `python api_pinecone.py`

### 4. `api_upload.py`

**Purpose**: REST API for uploading and automatically processing new contracts.

**Key functionalities**:

- Endpoint for uploading PDF files
- Asynchronous processing of uploaded contracts
- Endpoint to list available contracts in the folder

This file starts a web server on port 8001 when executed: `python api_upload.py`

### 5. `llm_router.py`

**Purpose**: Router for processing questions using LLM (Large Language Model).

**Key functionalities**:

- Endpoint to answer questions about contracts using the LLM
- Integrates with semantic search to provide context to the LLM
- Formats responses with source citations

This file is imported by `api_pinecone.py` and does not need to be executed directly.

### 6. `shared.py`

**Purpose**: Shared functions and models across different modules.

**Key functionalities**:

- Shared function for contract search
- Data models for API responses

This file is imported by other modules and does not need to be executed directly.

## Requirements

- Python 3.8+
- Node.js 18+
- Pinecone account (https://www.pinecone.io/)
- OpenAI API key (https://platform.openai.com/)

## Setup

1. **Backend**

```bash
# Install dependencies
pip install -r requirements.txt

# Configure environment variables (.env)
OPENAI_API_KEY=your_openai_api_key
PINECONE_API_KEY=your_pinecone_api_key
PINECONE_HOST=your_pinecone_host
PINECONE_INDEX_NAME=brito-ai
OPENAI_MODEL=gpt-4o-mini  # Optional, default is gpt-4o-mini

# Process existing contracts
python processar_contrato.py

# Process a specific contract
python processar_contrato.py contratos\EDUARD ROCHA FONTENELE.pdf

# Start the semantic search API with Uvicorn
uvicorn api_pinecone:app --host 127.0.0.1 --port 8000 --reload

# Start the upload API (in another terminal window)
uvicorn api_upload:app --host 127.0.0.1 --port 8001 --reload
```

**Important Note:** The vector index in Pinecone (`brito-ai`) must be manually created through the Pinecone dashboard, using the `text-embedding-3-small` model from OpenAI with a dimension of 1536.

2. **Frontend**

```bash
# Navigate to the frontend directory
cd frontend

# Install dependencies
npm install

# Start in development mode
npm run dev
```

## Recommended Execution Order

1. **Initial contract processing** (if necessary):

   ```
   python processar_contrato.py
   ```

   This step is optional if the contracts have already been processed and indexed in Pinecone.

2. **Start the semantic search API**:

   ```
   uvicorn api_pinecone:app --host 127.0.0.1 --port 8000 --reload
   ```

   This API is essential for the frontend to function, as it provides semantic search and question mode with LLM.

3. **Start the upload API** (optional, if you want to allow new contract uploads):

   ```
   uvicorn api_upload:app --host 127.0.0.1 --port 8001 --reload
   ```

   This step is optional if there is no need to upload new contracts.

4. **Start the frontend**:
   ```
   cd frontend
   npm install (if dependencies are not yet installed)
   npm run dev
   ```
   This will start the SvelteKit development server on port 5173.

After these steps, you can access the application at `http://localhost:5173` and perform semantic queries on contracts already indexed in Pinecone.

## Usage

### Contract Processing

1. Place the PDF contract files in the `contratos/` folder.
2. Run `python processar_contrato.py` to process all contracts in the folder.
3. Alternatively, process a specific contract: `python processar_contrato.py path/to/contract.pdf`.

### Uploading New Contracts

1. Start the upload API: `uvicorn api_upload:app --host 127.0.0.1 --port 8001 --reload`.
2. Upload new contracts via the POST `/upload/contrato` endpoint.
3. Uploaded contracts will be processed automatically in the background.

### Contract Querying

1. Access the frontend at `http://localhost:5173`.
2. Use the search bar to query contracts in natural language.
3. View results sorted by relevance.
4. Alternatively, use the API directly via the GET `/contratos/busca?q=your query` endpoint.

### Question Mode (LLM)

1. Access the frontend at `http://localhost:5173`.
2. Enable the "Question Mode" switch next to the search bar.
3. Type your question in natural language (e.g., "What is the rent amount Eduardo pays?" or "Do we have invoice information?").
4. Click "Search" to get a detailed response based on relevant contracts.

Alternatively, use the API directly via the POST `/llm/ask` endpoint with a JSON payload:

```json
{
  "question": "Your question here",
  "max_results": 3
}
```

The system uses the model configured in the `OPENAI_MODEL` environment variable (default: gpt-4o-mini) to generate detailed responses based on contracts found in the semantic search.

## Technologies

- **Backend**:
  - Python
  - FastAPI
  - Uvicorn (ASGI server)
  - Pinecone (vector database)
  - OpenAI Embeddings (model `text-embedding-3-small`)
  - OpenAI Chat Completions (default model: gpt-4o-mini)
  - LangChain (document processing)
- **Frontend**:
  - SvelteKit
  - Tailwind CSS
  - DaisyUI

## API Documentation

### Semantic Search API (`api_pinecone.py`)

**Port**: 8000

| Endpoint              | Method | Description                                     | Parameters                                                                        |
| --------------------- | ------ | ----------------------------------------------- | --------------------------------------------------------------------------------- |
| `/`                   | GET    | Checks the API status and Pinecone connection   | -                                                                                 |
| `/contratos/lista`    | GET    | Lists all available contracts                   | `skip`: number of records to skip<br>`limit`: maximum number of records to return |
| `/contratos/busca`    | GET    | Performs a semantic search on contracts         | `q`: search query<br>`limit`: maximum number of results                           |
| `/contratos/arquivos` | GET    | Lists all unique file names in the index        | -                                                                                 |
| `/llm/ask`            | POST   | Answers questions about contracts using the LLM | Body JSON: `{"question": "string", "max_results": int}`                           |

### Upload API (`api_upload.py`)

**Port**: 8001

| Endpoint           | Method | Description                                               | Parameters                  |
| ------------------ | ------ | --------------------------------------------------------- | --------------------------- |
| `/upload/contrato` | POST   | Uploads a new PDF contract and processes it automatically | Form Data: `file`: PDF file |
| `/contratos/lista` | GET    | Lists all contracts available in the contracts folder     | -                           |

## Starting with Uvicorn

Uvicorn is a high-performance ASGI (Asynchronous Server Gateway Interface) server recommended for FastAPI applications. To start the APIs with Uvicorn, follow the commands below:

### Semantic Search API

```bash
uvicorn api_pinecone:app --host 127.0.0.1 --port 8000 --reload
```

Important options:

- `--host 127.0.0.1`: Restricts access to localhost only.
- `--port 8000`: Sets port 8000 for the API.
- `--reload`: Enables auto-reload mode (useful for development).

### Upload API

```bash
uvicorn api_upload:app --host 127.0.0.1 --port 8001 --reload
```

For production, remove the `--reload` flag and consider using `--host 0.0.0.0` if the API needs to be accessed from other devices on the network.

## Recent Updates

- **Question Mode Fix (LLM)**: Resolved an issue affecting question mode after processing new contracts. The solution involved modifying `llm_router.py` to directly access the `buscar_documentos` function from the `pinecone_utils.py` module, bypassing format incompatibility with the `buscar_contratos` function.

- **Improved Error Handling**: Implemented more robust error handling across the application, with clearer messages and detailed logs for easier debugging.

- **Frontend Optimization**: Enhanced error handling in the frontend to display clearer messages to users.

- **New Contract Processing**: Added and processed new contracts (`contrato_joao_silva.pdf` and `contrato_maria_oliveira.pdf`).

- **Documentation Update**: Improved documentation with detailed instructions for initialization and system usage.
