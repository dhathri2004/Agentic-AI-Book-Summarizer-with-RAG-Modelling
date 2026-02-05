```markdown
# Book Summarization and Q&A System

This project implements a comprehensive system for processing book-length documents, generating hierarchical summaries (chunk, chapter, and full book), and enabling question-answering through a vector database.

## Features

- **Multi-format Document Loading**: Supports PDF, EPUB, TXT, and DOCX files.
- **Robust Text Preprocessing**: Cleans raw text by normalizing whitespace, removing page numbers, headers, footers, and reference sections.
- **Chapter Detection**: Intelligently identifies and splits documents into logical chapters.
- **Token-aware Text Chunking**: Utilizes LangChain's `RecursiveCharacterTextSplitter` with `tiktoken` for efficient and context-preserving text segmentation.
- **Hierarchical Summarization**: Generates summaries at multiple levels:
    - **Chunk Summaries**: Concise summaries of individual text chunks.
    - **Chapter Summaries**: Combines chunk summaries to create comprehensive chapter-level summaries.
    - **Book Summaries**: Provides short, medium, and key highlight summaries for the entire book.
- **OpenRouter Integration**: Leverages OpenRouter API for accessing various LLMs for summarization tasks.
- **Vector Database for Q&A**: Stores chapter summaries in a Chroma vector database, enabling semantic search and retrieval for question-answering.

## Project Structure & Workflow

The project follows a sequential workflow:

1.  **Setup and Installation**:
    -   Installs necessary Python libraries: `pdfplumber`, `pymupdf`, `ebooklib`, `python-docx` for document parsing; `langchain`, `tiktoken`, `langchain-text-splitters` for text processing; `openai` for LLM interaction; `chromadb` for vector store.

2.  **Document Loading (`book_loader.py`)**:
    -   `extract_text_from_pdf`: Extracts text from PDF files using `pdfplumber`.
    -   `extract_text_from_pdf_pymupdf`: Extracts text from PDF files using `PyMuPDF` (alternative).
    -   `extract_text_from_epub`: Extracts text from EPUB files.
    -   `extract_text_from_txt`: Extracts text from plain text files.
    -   `extract_text_from_docx`: Extracts text from DOCX files.
    -   `load_book`: A utility function to automatically detect file type and call the appropriate extractor.

3.  **Text Preprocessing (`text_preprocessor.py`)**:
    -   `normalize_whitespace`: Cleans up extra spaces, tabs, and newlines.
    -   `remove_page_numbers`: Removes standalone page numbers.
    -   `remove_headers_and_footers`: Identifies and removes common header/footer patterns.
    -   `remove_references`: Strips out common reference or bibliography sections.
    -   `clean_text`: Orchestrates the full cleaning pipeline.

4.  **Chapter Detection (`chapter_detector.py`)**:
    -   `split_by_chapter`: Uses regex patterns to identify chapter titles and split the document into a dictionary of `{chapter_title: chapter_text}`.

5.  **Text Chunking (`chunker_langchain.py`)**:
    -   `chunk_text_langchain`: Splits chapter texts into smaller, token-aware chunks using LangChain's `RecursiveCharacterTextSplitter` and `tiktoken` to respect model token limits.

6.  **Summarization Agent (`summarization_agent.py`)**:
    -   **Configuration**: Sets up OpenRouter API key and base URL for LLM calls.
    -   `SUMMARY_PROMPT`: Defines the prompt for summarizing individual text chunks.
    -   `create_summarization_agent`: Initializes a `ChatOpenAI` model (configured for OpenRouter) and creates an LCEL (LangChain Expression Language) chain for chunk summarization.
    -   `summarize_chunks`: Iterates through text chunks and generates summaries using the `summarization_chain`.

7.  **Chapter and Book Summarization**:
    -   `CHAPTER_SUMMARY_PROMPT`: Defines the prompt for combining partial summaries into a coherent chapter summary.
    -   `create_chapter_summary_agent`: Initializes another `ChatOpenAI` model and LCEL chain for chapter summarization.
    -   `summarize_chapter`: Uses the `chapter_summary_chain` to produce a single summary for each chapter.
    -   `BOOK_SUMMARY_PROMPT`: Defines the prompt for generating comprehensive book summaries (short, medium, highlights).
    -   `create_book_summary_agent`: Initializes the final `ChatOpenAI` model and LCEL chain for book summarization.
    -   `summarize_book`: Combines all chapter summaries and generates the final multi-faceted book summary.

8.  **Vector Database Integration**:
    -   `chapter_summaries_to_documents`: Converts the generated chapter summaries into `langchain_core.documents.Document` objects.
    -   `store_in_vector_db`: Embeds these documents using `OpenAIEmbeddings` (configured for OpenRouter) and stores them in a Chroma vector database for efficient retrieval.
    -   **Q&A Capability**: Demonstrates how to perform similarity searches against the vector database to answer queries based on chapter summaries.

## Usage

To use this project, follow the code cells in the notebook sequentially. You will need to:

1.  **Install Dependencies**: Run the `pip install` commands.
2.  **Provide a Document**: Upload a PDF, EPUB, TXT, or DOCX file to `/content/` and update the `file_path` variable.
3.  **Set OpenRouter API Key**: Ensure your OpenRouter API key is set as an environment variable or directly in the code where indicated.
4.  **Execute Cells**: Run the cells to load, clean, chunk, summarize, and store the document in the vector database.
5.  **Query the Database**: Use the provided example to query the vector database for information based on chapter summaries.

## Example Output

The final output will include:

-   **Detected Chapters** with their character counts.
-   **Token Counts** for individual chapters.
-   **Chapter-level Summaries** for each identified chapter.
-   **Hierarchical Book Summary** including short, medium, and key highlights.
-   **Vector Database Search Results** for specific queries.

```
