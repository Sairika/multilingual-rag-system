# ChatPDF with Together AI - Multilingual RAG System

## Overview
A multilingual Retrieval-Augmented Generation (RAG) system built with Gradio that allows users to upload PDF documents and interact with them through natural language queries in both English and Bengali. The system uses Together AI's Qwen model to provide contextually relevant answers based on the uploaded document content.

## 🚀 Live Demo
**Try it now**: [https://huggingface.co/spaces/sairika/multilingual-rag-system](https://huggingface.co/spaces/sairika/multilingual-rag-system)

The application is deployed on Hugging Face Spaces and can be accessed directly through your browser. No installation required!

## Features
- **Multilingual Support**: Handles queries in both English and Bengali
- **PDF Text Extraction**: Uses PyPDF2 for robust text extraction from PDF documents
- **Interactive Web Interface**: Gradio-based interface with real-time chat functionality
- **Context-Aware Responses**: Generates answers grounded in the uploaded document content
- **Conversation History**: Maintains chat history throughout the session
- **API Key Validation**: Built-in validation for Together AI API keys

## Setup Guide

### Option 1: Use the Live Demo (Recommended)
Simply visit: [https://huggingface.co/spaces/sairika/multilingual-rag-system](https://huggingface.co/spaces/sairika/multilingual-rag-system)

No installation required! Just bring your Together AI API key and start uploading PDFs.

### Option 2: Local Development

### Prerequisites
- Python 3.7 or higher
- Together AI API key ([Sign up at Together AI](https://api.together.xyz/))

### Installation

1. **Install required packages**
   ```bash
   pip install gradio PyPDF2 together
   ```

2. **Run the application**
   ```bash
   python app.py
   ```

3. **Access the interface**
   - The app will launch with a shareable Gradio link
   - Open the provided URL in your browser
   - Or visit the live demo at: https://huggingface.co/spaces/sairika/multilingual-rag-system

## Tools, Libraries & Packages Used

### Core Dependencies
- **gradio**: Web interface framework for the chat application
- **PyPDF2**: PDF text extraction library
- **together**: Together AI Python SDK for API access
- **io**: Built-in library for handling byte streams
- **os**: Built-in library for file operations
- **textwrap**: Built-in library for text formatting
- **tempfile**: Built-in library for temporary file handling
- **time**: Built-in library for time operations

### AI Model Configuration
- **Model**: `Qwen/Qwen3-Coder-480B-A35B-Instruct-FP8`
- **Provider**: Together AI
- **Max Tokens**: 5,000
- **Temperature**: 0.7
- **Context Handling**: 10,000 characters with intelligent truncation

## Sample Queries and Outputs

### Bengali Queries (Based on HSC Bangla Book)
**Query**: `অনুপেমর ভাষায় সুপুরুষ কাকে বলা হয়েছে?`  
**Expected Output**: Answer based on document content

**Query**: `কাকে অনুপেমর ভাগ্য দেবতা বলে উল্লেখ করা হয়েছে?`  
**Expected Output**: Answer extracted from the PDF context

**Query**: `বিয়ের সময় কল্যাণীর প্রকৃত বয়স কত ছিল?`  
**Expected Output**: Age information from the document

### English Queries
**Query**: `What is the main topic of this document?`  
**Output**: Summary based on the PDF content

**Query**: `Can you explain the key concepts discussed in this document?`  
**Output**: Detailed explanation derived from the uploaded PDF

## Technical Implementation Details

### Text Extraction Method
**Library Used**: PyPDF2  
**Rationale**: 
- Reliable text extraction from various PDF formats
- Handles both simple and complex PDF structures
- Good support for Unicode characters (important for Bengali text)
- Lightweight and easy to integrate

**Formatting Challenges Faced**:
- Page breaks causing text fragmentation
- Inconsistent spacing in extracted text
- Special characters and Bengali Unicode handling
- Scanned PDFs with no extractable text
- **Solutions Implemented**: Error handling for unreadable PDFs, text validation, and user feedback for extraction issues

### Chunking Strategy
**Method**: Context-length based truncation (10,000 characters)  
**Implementation**: 
- If PDF text exceeds 10,000 characters, the system takes the first 5,000 and last 5,000 characters
- Adds a truncation notice in the middle
- Preserves both beginning and end context

**Why This Works for Semantic Retrieval**:
- Maintains document structure and context
- Preserves important information typically found at document start/end
- Prevents model context overflow
- Simple and reliable implementation

### Embedding Model
**Model Used**: Together AI's Qwen/Qwen3-Coder-480B-A35B-Instruct-FP8  
**Choice Rationale**:
- Excellent multilingual capabilities (English and Bengali)
- Large context window suitable for document processing
- Strong instruction-following capabilities
- Optimized for both code and natural language understanding

**How It Captures Meaning**:
- The model processes the entire PDF context as part of the system prompt
- Uses transformer architecture to understand semantic relationships
- Maintains context across the conversation history

### Query-Document Comparison
**Method**: System prompt-based context injection  
**Implementation**:
1. PDF text is embedded in the system prompt as context
2. User queries are processed against this embedded context
3. The model generates responses based on semantic understanding of both query and document

**Storage Setup**: 
- PDF text stored in Gradio state during the session
- Chat history maintained in memory
- No persistent vector database (simplified approach)

**Why This Approach**:
- Simple and reliable implementation
- No need for external vector database setup
- Direct semantic comparison through the language model
- Suitable for the assessment scope

### Meaningful Query-Document Comparison
**Process**:
1. PDF content is preprocessed and validated
2. System prompt instructs the model to answer only based on PDF content
3. Model performs semantic matching between query and document context
4. Responses are grounded in the provided document

**Handling Vague/Missing Context**:
- System prompt includes instructions to state when information is not available
- Model responds with "information not found in document" for out-of-scope queries
- Maintains transparency about document limitations

## Memory Management

### Short-Term Memory
- **Implementation**: Gradio chatbot state maintains conversation history
- **Scope**: Current session only
- **Purpose**: Enables contextual follow-up questions

### Long-Term Memory  
- **Implementation**: PDF document text stored in Gradio state variable
- **Scope**: Persists until new PDF is uploaded
- **Purpose**: Consistent access to document knowledge base

## Error Handling & Validation

### API Key Validation
```python
def validate_api_key(api_key):
    if not api_key or not api_key.strip():
        return "❌ API Key is required"
    if len(api_key.strip()) < 10:
        return "❌ API Key appears to be too short"
    return "✓ API Key format looks valid (not verified with server)"
```

### PDF Processing
- File type validation (PDF only)
- Text extraction error handling
- Empty/unreadable document detection
- User feedback with status messages

### Query Processing
- Input validation for empty queries
- API error handling with user-friendly messages
- Context length management

## Results Quality Assessment

### Relevance
**Current Performance**:
- ✅ Successfully processes various PDF formats
- ✅ Handles both English and Bengali queries effectively  
- ✅ Maintains conversation context
- ✅ Provides document-grounded responses
- ✅ Clear error messaging for edge cases

### Potential Improvements
1. **Better Chunking**: Implement sentence-boundary aware chunking instead of character-based truncation
2. **Vector Database**: Add proper vector storage for better semantic search
3. **Better Embedding Model**: Use specialized multilingual embedding models
4. **Larger Context**: Utilize models with extended context windows for complete document processing
5. **Preprocessing**: Add better text cleaning and formatting

## Usage Instructions

### Using the Live Demo
1. **Visit**: Go to [https://huggingface.co/spaces/sairika/multilingual-rag-system](https://huggingface.co/spaces/sairika/multilingual-rag-system)
2. **Enter API Key**: Input your Together AI API key in the password field
3. **Upload PDF**: Click "Upload PDF" and select your document  
4. **Process Document**: Click "Process PDF" button to extract text
5. **Wait for Confirmation**: Look for the "✅ Successfully processed PDF" message
6. **Start Asking Questions**: Type your questions in English or Bengali
7. **Submit**: Click "Submit Question" or press Enter
8. **Continue Conversation**: The system remembers previous questions in the session

### Local Development
Follow the same steps after running the application locally with `python app.py`

## System Architecture

```
User Input (PDF) → PyPDF2 Extraction → Text Processing → 
Gradio Interface → Together AI API → Qwen Model → 
Response Generation → Chat Interface
```

## Limitations

1. **Context Window**: Limited to 10,000 characters of PDF content
2. **No Persistent Storage**: Document context lost when session ends
3. **Simple Chunking**: Basic truncation method may lose important middle content
4. **No Vector Search**: Relies on model's internal semantic understanding
5. **Session-Based**: No user accounts or conversation persistence

## Future Enhancements

- REST API implementation for programmatic access
- Vector database integration for better document search
- Advanced chunking strategies
- Conversation persistence
- Multi-document support
- Formal evaluation metrics implementation

## Deployment

### Hugging Face Spaces
The application is deployed on Hugging Face Spaces using:
- **Framework**: Gradio
- **Python Version**: 3.9+
- **Deployment**: Automatic from repository
- **Access**: Public, requires user's own Together AI API key
- **Live URL**: https://huggingface.co/spaces/sairika/multilingual-rag-system

### Local Deployment
For local development and testing, follow the installation steps above.

## License
This project is open source and available under the MIT License.

---

**Developed for AI Engineer (Level-1) Technical Assessment**  
*Simple Multilingual RAG System using Together AI and Gradio*
