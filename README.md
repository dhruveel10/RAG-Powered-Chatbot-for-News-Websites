# News AI Chatbot - Full Stack Application

A modern, intelligent chat application that combines a React frontend with a powerful RAG-powered backend to deliver real-time news analysis and conversational AI capabilities. The system features intelligent session management, semantic search, and responsive design across all devices.

## Live Demo

**[View Live Application](https://frontend-self-sigma-40.vercel.app/)**

Experience the full application with real-time chat, intelligent responses, and seamless session management.

## Architecture Overview

### RAG Pipeline Implementation
1. **News Ingestion**: ~50 news articles processed and chunked (5-10 chunks per article)
2. **Embeddings**: Generated using Jina Embeddings API
3. **Vector Storage**: Pinecone vector database for semantic search
4. **Retrieval**: Top-k (k=10) passages retrieved for each query
5. **Generation**: Google Gemini API for final answer synthesis

### Data Flow
```
User Query → Embedding → Vector Search (Pinecone) → Context Retrieval → Gemini API → Response
```

## Key Features

### Frontend Features
- **Responsive Design**: Seamless experience on mobile and desktop devices
- **Dark/Light Mode**: Toggle between themes for comfortable viewing
- **Real-Time Chat**: Instant message delivery with typing indicators
- **Session Management**: Easy access to chat history via sidebar navigation
- **Interactive UI**: Modern, clean interface with intuitive navigation
- **Cross-Platform**: Optimized for all screen sizes and devices

### Backend Capabilities
- **RAG Pipeline**: Retrieval-Augmented Generation for intelligent responses
- **Semantic Search**: Jina embeddings for high-quality vector representations
- **Vector Database**: Pinecone integration for fast similarity search
- **Smart Caching**: Redis for active sessions, MySQL for persistence
- **Real-Time Streaming**: Character-by-character response generation

### Session Intelligence
- **24-Hour Active Sessions**: Stored in Redis with automatic TTL management
- **Persistent Storage**: Inactive chats archived in MySQL
- **Seamless Restoration**: One-click reactivation of inactive sessions
- **Automatic Migration**: Sessions move between Redis and MySQL intelligently

## Tech Stack

### Frontend
- **React**: Modern frontend framework
- **Vercel**: Production hosting and deployment
- **Responsive CSS**: Mobile-first design approach
- **Real-time UI**: Socket.IO integration for live updates

### Backend
- **Node.js & Express**: Server framework
- **Socket.IO**: Real-time bidirectional communication
- **Jina Embeddings**: Advanced text vectorization
- **Pinecone**: Vector database for semantic search
- **Google Gemini**: LLM for response generation
- **Redis**: In-memory session caching
- **MySQL**: Persistent data storage

## Getting Started

### Quick Start - Frontend Only
1. Visit the live application: **[https://frontend-self-sigma-40.vercel.app/](https://frontend-self-sigma-40.vercel.app/)**
2. Start chatting immediately - no setup required!

### Full Development Setup

#### Prerequisites
- Node.js (v14 or higher)
- Redis server
- MySQL server
- API keys for Pinecone, Google Gemini, and Jina AI

#### Frontend Setup
```bash
# Clone and setup frontend
git clone https://github.com/dhruveel10/frontend.git
cd frontend
npm install

# Configure environment
echo "REACT_APP_API_BASE=http://localhost:3005" > .env

# Start development server
npm start
```

#### Backend Setup
```bash
# Clone and setup backend
git clone https://github.com/dhruveel10/backend.git
cd backend
npm install

# Configure environment
cat > .env << EOF
PORT=3005
FRONTEND_URL=http://localhost:3000

PINECONE_API_KEY=your_pinecone_api_key
GEMINI_API_KEY=your_gemini_api_key
JINA_API_KEY=your_jina_api_key

REDIS_HOST=localhost
REDIS_PORT=6379

DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_password
DB_NAME=news_ai_chatbot
EOF

# Setup database
mysql -u root -p -e "CREATE DATABASE news_ai_chatbot;"

# Start services
redis-server &
npm start
```

## API Endpoints

### REST Endpoints
- `GET /api/sessions/:sessionId/history` - Retrieve session chat history
- `DELETE /api/sessions/:sessionId` - Clear/reset session
- `POST /api/chat` - Send chat message (alternative to Socket.IO)
- `GET /api/health` - Health check endpoint
- `GET /api/stats` - System statistics
- `GET /api/sessions` - List all active and inactive sessions
- `GET /api/sessions/:sessionId/exists` - Check if session exists in Redis or MySQL
- `GET /api/sessions/stored` - Get all stored sessions from MySQL
- `GET /api/sessions/:sessionId/stored-history` - Get session history from MySQL
- `GET /api/sessions/status` - Detailed session status across Redis and MySQL
- `POST /api/sessions/cleanup` - Trigger manual cleanup of empty sessions
- `DELETE /api/sessions/:sessionId/redis` - Delete session from Redis only

### Socket.IO Events
- `connection` - Client connection established
- `join-session` - Join specific session room
- `send-message` - Send chat message
- `receive-message` - Receive bot response
- `typing` - Typing indicators
- `error` - Error handling

## Caching & Performance Strategy

### Redis Configuration & TTL Management

#### Session TTL Configuration
```javascript
// 24-hour TTL for active sessions
const SESSION_TTL = 3600 * 24; // 24 hours in seconds

// Configure Redis TTL for session data
await redis.expire(`session:${sessionId}`, SESSION_TTL);
```

#### Session Lifecycle Management
```javascript
// Active sessions in Redis with 24-hour TTL
await redis.lPush(sessionKey, JSON.stringify(messageData));
await redis.expire(sessionKey, 3600 * 24);

// Restoration from MySQL when session expires
const restoreResult = await sessionService.restoreSessionFromStorage(
  sessionId, 
  sessionStorageService, 
  limit
);
```

### Performance Optimizations

#### Vector Search Caching
- **Embedding Cache**: Frequently queried embeddings cached for performance
- **Search Results**: Top-k results optimized through Pinecone
- **Context Caching**: Retrieved contexts processed efficiently

#### Database Connection Pooling
```javascript
// MySQL connection pool configuration
const pool = mysql.createPool({
  connectionLimit: 10,
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
  waitForConnections: true,
  queueLimit: 0
});
```

## Session Lifecycle Management

### Active Session Flow
1. **Creation**: New session created with UUID in Redis
2. **Activity**: Chat messages stored in Redis with 24-hour TTL
3. **Real-time Updates**: Socket.IO enables instant message delivery
4. **TTL Refresh**: Session activity resets TTL to 24 hours

### Session Migration Flow
1. **TTL Expiration**: Sessions expire after 24 hours in Redis
2. **MySQL Storage**: All messages automatically stored in MySQL during active session
3. **Inactive State**: After Redis expiration, chats remain accessible in MySQL
4. **Reactivation**: When users access inactive sessions, they automatically move back to Redis as new entries
5. **Seamless Restoration**: Users can continue conversations without data loss

## Real-Time Features

### Socket.IO Implementation
```javascript
// Real-time message handling with streaming
io.on('connection', (socket) => {
  socket.on('send-message', async (data) => {
    const response = await chatService.processMessage(message, sessionId);
    
    // Stream response word by word
    const words = response.response.split(' ');
    for (let i = 0; i < words.length; i++) {
      socket.emit('message-stream', {
        text: currentText,
        isComplete: i === words.length - 1,
        sessionId: currentSessionId
      });
      await new Promise(resolve => setTimeout(resolve, 50));
    }
  });
});
```

### Connection Monitoring
- **Server Status**: Frontend automatically detects server availability
- **Reconnection Logic**: Automatic reconnection on connection loss
- **Fallback Mode**: REST API fallback when Socket.IO unavailable

## Database Schema

### Redis Schema
```
session:{sessionId} -> List<{
  id: string,
  text: string,
  isUser: boolean,
  timestamp: string,
  sources: array
}>

session_title:{sessionId} -> string (24h TTL)
```

### MySQL Schema
```sql
CREATE TABLE chat_messages (
  id INT AUTO_INCREMENT PRIMARY KEY,
  session_id VARCHAR(255) NOT NULL,
  message TEXT NOT NULL,
  role ENUM('user', 'bot') NOT NULL,
  timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  INDEX idx_session_id (session_id),
  INDEX idx_timestamp (timestamp)
);
```

## Deployment Architecture

### Local Development with ngrok
- **Backend**: Runs locally on port 3005
- **ngrok Tunnel**: Exposes local server to internet
- **Frontend**: Deployed on Vercel, connects via ngrok URL
- **Databases**: Local Redis and MySQL instances

### Production Considerations
For production deployment:
- Deploy backend to cloud service (Render, Railway, etc.)
- Use managed Redis (Redis Cloud, AWS ElastiCache)
- Use managed MySQL (PlanetScale, AWS RDS)
- Update CORS settings for production domains

## Configuration Options

### Cache TTL Configuration
```javascript
const CACHE_CONFIG = {
  SESSION_TTL: 24 * 60 * 60,        // 24 hours
  MAINTENANCE_INTERVAL: 4 * 60 * 60 * 1000  // 4 hours
};
```

### RAG Pipeline Configuration
```javascript
const RAG_CONFIG = {
  TOP_K_RESULTS: 10,                // Number of retrieved passages
  JINA_MODEL: 'jina-embeddings-v3', // Embedding model
  GEMINI_MODEL: 'gemini-1.5-flash'  // Generation model
};
```

## Technical Highlights

### Vector Database Implementation
- Uses Pinecone for scalable vector storage (`news-chatbot-rag` index)
- Implements similarity search with top-10 results
- Supports metadata filtering and source-based queries
- 1024-dimensional Jina embeddings with fallback support

### LLM Integration
- Google Gemini API integration for response generation
- Fallback mechanisms for API failures
- Conversation context management through session history
- Specialized news analysis and question-answering prompts

### Session Management
- Dual-storage architecture (Redis + MySQL)
- Automatic session cleanup and maintenance
- Session restoration capabilities
- UUID-based session identification

## API Documentation

### Chat Message Format
```javascript
// Request
{
  "message": "What's happening in the tech industry?",
  "sessionId": "optional-session-id"
}

// Response
{
  "response": "Based on recent news...",
  "sources": [
    {
      "title": "Article Title",
      "source": "Source Name",
      "url": "https://...",
      "score": 0.85
    }
  ],
  "sessionId": "uuid-session-id"
}
```

### Session History Format
```javascript
{
  "sessionId": "uuid",
  "history": [
    {
      "id": "message-uuid",
      "text": "User or bot message",
      "isUser": true/false,
      "timestamp": "2023-...",
      "sources": []
    }
  ],
  "exists": true,
  "source": "redis|mysql",
  "restored": false
}
```

## Project Structure

### Frontend Structure
```
frontend/
├── src/
│   ├── components/     # Reusable UI components
│   ├── pages/         # Main application pages
│   ├── hooks/         # Custom React hooks
│   ├── utils/         # Helper functions
│   └── styles/        # CSS and styling files
├── public/            # Static assets
└── package.json       # Dependencies and scripts
```

### Backend Structure  
```
backend/
├── services/          # Core business logic
│   ├── sessionService.js        # Redis session management
│   ├── sessionStorageService.js # MySQL persistence
│   ├── vectorService.js         # Pinecone integration
│   ├── llmService.js            # Gemini API
│   └── jinaEmbeddingService.js  # Jina embeddings
├── chatbot.js         # Main server file
└── database.js        # Database utilities
```

## Contributing

1. Fork the repositories
2. Create feature branches (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branches (`git push origin feature/amazing-feature`)
5. Open Pull Requests

## Notes

- Frontend is production-ready and deployed on Vercel
- Backend runs locally with ngrok tunnel for development
- Both services work together to provide the complete experience
- Database setup is automatic on first run

---

*A modern full-stack application demonstrating advanced web development practices with real-time communication, intelligent session management, and responsive design.*
