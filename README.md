# Gemini MCP Server

An MCP Server that provides access to the Gemini Suite, including the latest **Gemini 3 Flash** model.

## ✨ Features

- **Gemini 3 Flash** - Latest model with frontier intelligence (December 2025)
- **Gemini 3 Pro** - Advanced reasoning capabilities
- **Gemini 2.5 Pro/Flash** - Balanced performance
- **Image Generation** - Text-to-image and image editing
- **Embeddings** - 1536-dimensional vectors
- **Batch Processing** - 50% cost reduction for large-scale tasks
- **File Upload** - Multi-file support with 1M+ token context
- **Conversation Memory** - Persistent chat sessions

## 📊 Supported Models

| Model | ID | Context | Best For |
|-------|-----|---------|----------|
| **Gemini 3 Flash** | `gemini-3-flash` | 1M tokens | Fast, cost-effective, agentic coding |
| Gemini 3 Pro | `gemini-3-pro-preview` | 1M tokens | Complex reasoning |
| Gemini 2.5 Pro | `gemini-2.5-pro` | 2M tokens | Deep analysis |
| Gemini 2.5 Flash | `gemini-2.5-flash` | 1M tokens | General use |
| Gemini 2.0 Flash | `gemini-2.0-flash-exp` | 1M tokens | Legacy support |

**Pricing (Gemini 3 Flash):** $0.50/1M input tokens, $3/1M output tokens

## 🚀 Quick Start

### Authentication Options

This fork supports **two authentication methods**:

#### Option A: OAuth via Gemini CLI (Recommended - No API Key Needed!)

If you already use [Gemini CLI](https://github.com/google-gemini/gemini-cli), just login once:

```bash
npx @google/gemini-cli login
```

The MCP server will automatically use your OAuth credentials from `~/.gemini/oauth_creds.json`. No API key required!

**Benefits:**
- No API key management
- Uses your Google account
- Automatic token refresh
- Same auth as Gemini CLI

#### Option B: API Key

Get your API key from [Google AI Studio](https://makersuite.google.com/app/apikey)

### Installation

#### Option 1: From GitHub Fork (with OAuth support)

```bash
# Clone the repository
git clone https://github.com/AndrewMoryakov/gemini-mcp.git
cd gemini-mcp

# Install and build
npm install
npm run build

# Add to Claude Code (no API key needed if you use OAuth!)
claude mcp add gemini -s user -- node /path/to/gemini-mcp/build/index.js

# Or with API key:
claude mcp add gemini -s user --env GEMINI_API_KEY=YOUR_KEY_HERE -- node /path/to/gemini-mcp/build/index.js
```

#### Option 2: NPX with API Key

```bash
claude mcp add gemini -s user --env GEMINI_API_KEY=YOUR_KEY_HERE -- npx -y @mintmcqueen/gemini-mcp@latest
```

#### Option 3: Global Install with API Key

```bash
npm install -g @mintmcqueen/gemini-mcp
claude mcp add gemini -s user --env GEMINI_API_KEY=YOUR_KEY_HERE -- gemini-mcp
```

After any installation method, **restart Claude Code** and you're ready to use Gemini.

### Shell Environment (Alternative for API Key)
- **File:** `~/.zshrc` or `~/.bashrc`
- **Format:** `export GEMINI_API_KEY="your-key-here"`

## Usage

### MCP Tools

The server provides the following tools:

#### `chat`
Send a message to Gemini with optional file attachments.

Parameters:
- `message` (required): The message to send
- `model` (optional): Model to use (default: `gemini-3-flash`)
  - `gemini-3-flash` - Fastest, recommended for most tasks
  - `gemini-3-pro-preview` - Most capable
  - `gemini-2.5-pro` - Large context (2M tokens)
  - `gemini-2.5-flash` - Balanced performance
  - `gemini-2.0-flash-exp` - Legacy
- `fileUris` (optional): Array of file URIs from uploaded files
- `temperature` (optional): Controls randomness (0.0-2.0, default: 1.0)
- `maxTokens` (optional): Maximum response tokens (up to 500,000)
- `conversationId` (optional): Continue an existing conversation

**Example:**
```javascript
chat({
  message: "Analyze this code for security issues",
  model: "gemini-3-flash",
  fileUris: ["files/abc123"],
  maxTokens: 8000
})
```

#### `upload_file`
Upload a single file to Gemini for analysis.

Parameters:
- `filePath` (required): Absolute path to the file
- `displayName` (optional): Custom name for the file
- `mimeType` (optional): MIME type (auto-detected if not provided)

**Example:**
```javascript
upload_file({ filePath: "/path/to/document.pdf" })
// Returns: { uri: "files/abc123", state: "ACTIVE" }
```

#### `upload_multiple_files`
Upload multiple files efficiently with parallel processing.

Parameters:
- `filePaths` (required): Array of absolute file paths
- `maxConcurrent` (optional): Parallel uploads (default: 5, max: 10)
- `waitForProcessing` (optional): Wait for ACTIVE state (default: true)

**Example:**
```javascript
upload_multiple_files({
  filePaths: ["/path/to/file1.pdf", "/path/to/file2.md"],
  maxConcurrent: 5
})
// Returns: { successful: [...], failed: [], totalRequested: 2 }
```

#### `start_conversation`
Start a new conversation session for multi-turn chat.

Parameters:
- `id` (optional): Custom conversation ID

#### `clear_conversation`
Clear a conversation session.

Parameters:
- `id` (required): Conversation ID to clear

#### `generate_images`
Generate images from text prompts or edit existing images using Gemini image models.

Parameters:
- `prompt` (required): Text description or editing instructions
- `aspectRatio` (optional): `1:1`, `2:3`, `3:2`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`, `16:9`, `21:9` (default: `1:1`)
- `numImages` (optional): Number of images, 1-4 (default: 1)
- `inputImageUri` (optional): File URI for image editing
- `outputDir` (optional): Save directory (default: `./generated-images`)
- `temperature` (optional): Randomness (0.0-2.0, default: 1.0)

**Text-to-Image Example:**
```javascript
generate_images({
  prompt: "A photorealistic coffee cup on a wooden table",
  aspectRatio: "16:9",
  numImages: 2
})
```

**Image Editing Example:**
```javascript
// First, upload the image
upload_file({ filePath: "./photo.jpg" })
// Returns: { uri: "files/abc123" }

// Then edit it
generate_images({
  prompt: "Add a wizard hat to the subject",
  inputImageUri: "files/abc123"
})
```

### Batch API Tools

Process large-scale tasks asynchronously at **50% cost** with ~24 hour turnaround.

#### Content Generation

**Simple (Automated):**
```javascript
batch_process({
  inputFile: "prompts.csv",  // CSV, JSON, TXT, or MD
  model: "gemini-2.5-flash"
})
```

**Advanced (Manual Control):**
```javascript
// 1. Convert to JSONL
batch_ingest_content({ inputFile: "prompts.csv" })

// 2. Upload JSONL
upload_file({ filePath: "prompts.jsonl" })

// 3. Create batch job
batch_create({
  inputFileUri: "files/abc123",
  model: "gemini-2.5-flash"
})

// 4. Monitor progress
batch_get_status({
  batchName: "batches/xyz789",
  autoPoll: true
})

// 5. Download results
batch_download_results({ batchName: "batches/xyz789" })
```

#### Embeddings

```javascript
batch_process_embeddings({
  inputFile: "documents.txt",
  taskType: "RETRIEVAL_DOCUMENT"  // Optional - will prompt if not provided
})
```

**Task Types:**
- `SEMANTIC_SIMILARITY` - Compare text similarity
- `CLASSIFICATION` - Categorize content
- `CLUSTERING` - Group similar items
- `RETRIEVAL_DOCUMENT` - Build search indexes
- `RETRIEVAL_QUERY` - Search queries
- `CODE_RETRIEVAL_QUERY` - Code search
- `QUESTION_ANSWERING` - Q&A systems
- `FACT_VERIFICATION` - Fact-checking

#### Job Management

```javascript
batch_cancel({ batchName: "batches/xyz789" })
batch_delete({ batchName: "batches/xyz789" })
```

### MCP Resources

#### `gemini://models/available`
Information about available Gemini models and their capabilities.

#### `gemini://conversations/active`
List of active conversation sessions with metadata.

## 🔧 Development

```bash
npm run build        # Build TypeScript
npm run watch        # Watch mode
npm run dev          # Build + auto-restart
npm run inspector    # Debug with MCP Inspector
```

### Troubleshooting

**Connection Failures:**
1. Verify your API key is correct
2. Check that the command path is correct (for local installs)
3. Restart Claude Code after configuration changes

**Rate Limits:**
- Free tier: 60 requests/minute, 1,000 requests/day
- Paid tier: Higher limits based on plan

## 🔒 Security

- API keys are never logged or echoed
- Files created with 600 permissions (user read/write only)
- Masked input during key entry
- Real API validation before storage

## 🤝 Contributing

Contributions are welcome! This package is designed to be production-ready with:
- Full TypeScript types
- Comprehensive error handling
- Automatic retry logic
- Real API validation

## 📄 License

MIT - see LICENSE file

## 🙋 Support

- **MCP Protocol**: https://modelcontextprotocol.io
- **Gemini API Docs**: https://ai.google.dev/docs
- **Gemini 3 Flash Announcement**: https://blog.google/products/gemini/gemini-3-flash/

---

**Fork maintained by:** [@AndrewMoryakov](https://github.com/AndrewMoryakov)
**Original by:** [@MintMcQueen](https://github.com/MintMcQueen/gemini-mcp)
