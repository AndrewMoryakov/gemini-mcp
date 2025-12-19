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
| **Gemini 3 Flash** | `gemini-3-flash-preview` | 1M tokens | Fast, cost-effective, agentic coding |
| Gemini 3 Pro | `gemini-3-pro-preview` | 1M tokens | Complex reasoning |
| Gemini 2.5 Pro | `gemini-2.5-pro` | 2M tokens | Deep analysis |
| Gemini 2.5 Flash | `gemini-2.5-flash` | 1M tokens | General use |
| Gemini 2.0 Flash | `gemini-2.0-flash-exp` | 1M tokens | Legacy support |

**Pricing (Gemini 3 Flash):** $0.50/1M input tokens, $3/1M output tokens

## 🚀 Quick Start (5 minutes)

### Step 1: Choose Your Auth Method

| Method | Best For | Difficulty |
|--------|----------|------------|
| **API Key** | Most users, quick setup | ⭐ Easy |
| **gcloud OAuth** | Pro subscription, Google Cloud users | ⭐⭐ Medium |

---

### 🔑 Option A: API Key (Recommended for most users)

**Step 1.1:** Get your API key
1. Go to [Google AI Studio](https://aistudio.google.com/apikey)
2. Sign in with Google
3. Click "Create API Key"
4. Copy the key

**Step 1.2:** Install MCP server
```bash
claude mcp add gemini -s user --env GEMINI_API_KEY=YOUR_KEY_HERE -- npx -y @mintmcqueen/gemini-mcp@latest
```

**Step 1.3:** Restart Claude Code

**Step 1.4:** Test it works
```
Ask Claude: "Use Gemini to say hello"
```

✅ **Done!** You can now use Gemini in Claude Code.

---

### 🔐 Option B: gcloud OAuth (For Pro/Paid Users)

Use this if you have Gemini Pro subscription and want OAuth instead of API key.

**Step 1.1:** Install gcloud SDK

<details>
<summary>Windows</summary>

1. Download installer: https://cloud.google.com/sdk/docs/install
2. Run `GoogleCloudSDKInstaller.exe`
3. Follow the wizard (keep defaults)
4. Open new terminal after installation
</details>

<details>
<summary>macOS</summary>

```bash
brew install google-cloud-sdk
```
</details>

<details>
<summary>Linux</summary>

```bash
curl https://sdk.cloud.google.com | bash
exec -l $SHELL
```
</details>

**Step 1.2:** Authenticate with Google
```bash
gcloud auth application-default login --scopes="https://www.googleapis.com/auth/cloud-platform"
```
- Browser opens → Sign in with your Google account (with Pro subscription)
- Click "Allow"

**Step 1.3:** Clone and build MCP server
```bash
git clone https://github.com/AndrewMoryakov/gemini-mcp.git
cd gemini-mcp
npm install
npm run build
```

**Step 1.4:** Add to Claude Code

Windows:
```bash
claude mcp add gemini -s user -- node C:/path/to/gemini-mcp/build/index.js
```

macOS/Linux:
```bash
claude mcp add gemini -s user -- node /path/to/gemini-mcp/build/index.js
```

**Step 1.5:** Restart Claude Code

**Step 1.6:** Test it works
```
Ask Claude: "Use Gemini to say hello"
```

✅ **Done!** OAuth credentials are used automatically.

---

### 🔧 Troubleshooting Quick Start

| Problem | Solution |
|---------|----------|
| "API key not valid" | Check key is correct, no extra spaces |
| "UNAUTHENTICATED" | Run `gcloud auth application-default login` again |
| "Tool not found" | Restart Claude Code |
| Nothing happens | Check `claude mcp list` shows "gemini" |

## 🤖 For AI Agents (Claude)

This section explains how to use Gemini tools effectively.

### When to Use Gemini

| Use Case | Tool | Example |
|----------|------|---------|
| Analyze large files (>100KB) | `upload_file` → `chat` | "Upload this 500KB log and find errors" |
| Cross-reference multiple docs | `upload_multiple_files` → `chat` | "Compare these 5 specs for conflicts" |
| Generate images | `generate_images` | "Create a diagram of this architecture" |
| Long analysis (>1M tokens) | `chat` with conversation | Use Gemini's 1M context window |
| Batch processing | `batch_process` | Process 100 prompts at 50% cost |

### Basic Workflow

```
1. Upload files (if needed):
   upload_file({ filePath: "/path/to/file.pdf" })
   → Returns: { uri: "files/abc123" }

2. Chat with Gemini:
   chat({
     message: "Analyze this document for security issues",
     fileUris: ["files/abc123"]
   })
   → Returns: Analysis text

3. For multi-turn conversations:
   start_conversation({ id: "analysis-session" })
   chat({ message: "First question", conversationId: "analysis-session" })
   chat({ message: "Follow-up", conversationId: "analysis-session" })
   clear_conversation({ id: "analysis-session" })
```

### Model Selection Guide

```
For most tasks:        → gemini-3-flash-preview (default, fast, cheap)
For complex reasoning: → gemini-2.5-pro (2M context, deep analysis)
For image generation:  → gemini-3-pro-image-preview (default)

Change session default:
set_default_model({ category: "chat", model: "gemini-2.5-pro" })
```

### Best Practices

1. **Upload first, then chat** — Don't include file content in message
2. **Use conversations** for multi-turn analysis — Maintains context
3. **Check defaults** with `get_default_models()` before starting
4. **Clean up** files after batch processing with `cleanup_all_files()`

---

## Usage

### MCP Tools

The server provides the following tools:

#### `chat`
Send a message to Gemini with optional file attachments.

Parameters:
- `message` (required): The message to send
- `model` (optional): Model to use (default: `gemini-3-flash-preview`)
  - `gemini-3-flash-preview` - Fastest, recommended for most tasks
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
  model: "gemini-3-flash-preview",
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

#### `set_default_model`
Change the default model for a category during the current session.

Parameters:
- `category` (required): `chat`, `batch`, `image`, or `embedding`
- `model` (required): Model ID (e.g., `gemini-2.5-pro`)

**Example:**
```javascript
set_default_model({
  category: "chat",
  model: "gemini-2.5-pro"
})
// All subsequent chat calls will use gemini-2.5-pro by default
```

#### `get_default_models`
Show current default models for all categories (config + session overrides).

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

## ⚙️ Model Configuration

Models can be configured dynamically without code changes.

### Option 1: Edit `models.config.json`

Located in the package root directory:

```json
{
  "chat": {
    "models": ["gemini-3-flash-preview", "gemini-3-pro-preview", "gemini-2.5-pro"],
    "default": "gemini-3-flash-preview"
  },
  "batch": {
    "models": ["gemini-2.5-flash", "gemini-2.5-pro"],
    "default": "gemini-2.5-flash"
  },
  "image": {
    "models": ["gemini-3-pro-image-preview", "gemini-2.5-flash-image"],
    "default": "gemini-3-pro-image-preview"
  },
  "embedding": {
    "models": ["gemini-embedding-001"],
    "default": "gemini-embedding-001"
  }
}
```

### Option 2: Custom Config Path

Set the `GEMINI_MODELS_CONFIG` environment variable:

```bash
export GEMINI_MODELS_CONFIG="/path/to/my-models.json"
```

### Adding New Models

When Google releases new models (e.g., `gemini-4-flash`):

1. Add to the appropriate `models` array
2. Optionally set as `default`
3. Restart Claude Code

No code changes or rebuilds required!

## 🔧 Development

```bash
npm run build        # Build TypeScript
npm run watch        # Watch mode
npm run dev          # Build + auto-restart
npm run inspector    # Debug with MCP Inspector
```

### Troubleshooting

**Authentication Failures:**

| Error | Cause | Solution |
|-------|-------|----------|
| `UNAUTHENTICATED` | No valid credentials | Run `gcloud auth application-default login` or set `GEMINI_API_KEY` |
| `ACCESS_TOKEN_SCOPE_INSUFFICIENT` | OAuth scopes missing | Use gcloud ADC instead of Gemini CLI |
| `restricted_client` | OAuth client can't request scope | Use gcloud ADC (Option A) |
| `API key not valid` | Invalid or expired API key | Get new key from [AI Studio](https://aistudio.google.com/apikey) |

**Authentication Priority:**
1. `GEMINI_API_KEY` environment variable (if set)
2. gcloud ADC (`application_default_credentials.json`)
3. Gemini CLI OAuth (`oauth_creds.json`)

**Connection Failures:**
1. Verify credentials are valid
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
