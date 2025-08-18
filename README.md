# Financial Analysis Chatbot

A full-stack intelligent chatbot application that enables users to ask questions about public limited companies and receive AI-powered responses with interactive visualizations. The system processes financial documents (earnings calls, investor presentations, financial results) and provides semantic search capabilities with visual outputs for financial trends and metrics.

## 🚀 Features

- **Intelligent Document Processing**: Automated PDF parsing and text extraction
- **Semantic Search**: Vector-based similarity search using embeddings
- **AI-Powered Responses**: Natural language processing with Ollama LLM
- **Interactive Visualizations**: Dynamic charts and graphs for financial data
- **Modern UI/UX**: Clean, responsive interface with dark/light mode
- **Session Management**: Conversation history and context preservation
- **Real-time Analysis**: Live financial trend analysis and comparisons

## 🏗️ Architecture

### Backend
- **Vector Database**: Pinecone for semantic document storage and retrieval
- **LLM**: Ollama (llama3.1:8b) for natural language processing and analysis
- **Embeddings**: paraphrase-MiniLM-L3-v2 for text embedding generation
- **API**: Express.js REST API with CORS support
- **Document Processing**: PDF parsing with automatic text chunking

### Frontend
- **Framework**: React with modern hooks
- **Styling**: Tailwind CSS with responsive design
- **Charts**: Recharts for interactive data visualizations
- **Icons**: Lucide React for modern iconography
- **Features**: Dark mode, markdown parsing, loading states

## 📋 Prerequisites

- Node.js (v16 or higher)
- Python 3.8+
- Ollama installed and running
- Pinecone account and API key

## 🛠️ Installation

### 1. Clone the Repository
```bash
git clone https://github.com/dhruveel10/financial-analysis-assistant.git
```

### 2. Backend Setup
```bash
cd backend
npm install
```

### 3. Python Dependencies
```bash
pip install sentence-transformers numpy
```

### 4. Environment Configuration
Create a `.env` file in the backend directory:
```env
PINECONE_API_KEY=your_pinecone_api_key_here
PORT=3001
```

### 5. Frontend Setup
```bash
cd frontend
npm install
```

### 6. Ollama Setup
Install and start Ollama:
```bash
# Install Ollama (visit https://ollama.ai for platform-specific instructions)
ollama serve

# Pull the required model
ollama pull llama3.1:8b
```

## 🚀 Usage

### 1. Document Upload and Processing
Place your PDF documents in the `data` directory and run the upload script:
```bash
cd backend
node server.js
```

This will:
- Parse PDF documents
- Generate text embeddings using paraphrase-MiniLM-L3-v2
- Upload vectorized data to Pinecone
- Create searchable document chunks

### 2. Start the Backend Server
```bash
cd backend
npm start
```
The API will be available at `http://localhost:3001`

### 3. Start the Frontend Application
```bash
cd frontend
npm start
```
The web application will be available at `http://localhost:3000`

## 💬 Using the Chatbot

### Sample Questions
- "What are the key financial highlights?"
- "Show me revenue growth trends"
- "Compare profit margins across quarters"
- "Visualize revenue by business division"
- "What are the main business segments?"
- "Analyze the order backlog performance"

### Chart Generation
The system automatically detects when visual outputs would be helpful and generates:
- **Bar Charts**: For comparisons and segment analysis
- **Line Charts**: For trends and time-series data
- **Pie Charts**: For distribution and composition analysis

## 🔧 API Endpoints

### Chat Endpoint
```http
POST /api/chat
Content-Type: application/json

{
  "message": "Show me revenue trends",
  "sessionId": "unique-session-id"
}
```

### Health Check
```http
GET /api/health
```

### Statistics
```http
GET /api/stats
```

## 🎨 Key Features

### Document Processing
- **Automatic PDF Parsing**: Extracts text from financial documents
- **Intelligent Chunking**: Splits large documents into searchable segments
- **Metadata Preservation**: Maintains source document information
- **Batch Processing**: Efficient handling of multiple documents

### AI Capabilities
- **Semantic Search**: Vector-based document retrieval
- **Context Awareness**: Maintains conversation history
- **Chart Intent Detection**: Automatically identifies visualization opportunities
- **Financial Data Extraction**: Specialized parsing for financial metrics

### User Interface
- **Responsive Design**: Works on desktop and mobile devices
- **Dark/Light Mode**: User preference-based theming
- **Interactive Charts**: Hover effects and data tooltips
- **Markdown Support**: Rich text formatting in responses
- **Loading States**: Clear feedback during processing

## 🔍 Technical Highlights

### Vector Database Implementation
- Uses Pinecone for scalable vector storage
- Implements similarity search with configurable top-K results
- Supports metadata filtering and source-based queries
- Optimized chunking strategy for document processing

### LLM Integration
- Ollama integration with multiple model support
- Fallback mechanisms for offline scenarios
- Conversation context management
- Specialized financial analysis prompts

### Chart Generation
- Automatic chart type detection based on query intent
- Dynamic data extraction from document context
- Support for multiple visualization types
- Responsive chart rendering with Recharts

## 🔮 Future Enhancements

- Support for additional document formats (Excel, Word)
- Real-time data integration from financial APIs
- Advanced analytics and forecasting
- Multi-language support
- Export functionality for charts and reports
