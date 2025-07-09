# BharatGPT MSME Bot (Prototype)

This project is a prototype of a Hindi-language AI assistant designed to provide information and assistance to Micro, Small, and Medium Enterprises (MSMEs) in India. It leverages a Retrieval-Augmented Generation (RAG) architecture using LangChain, a local Large Language Model (LLM) (OpenHathi-7B), and FastAPI for a web interface with Text-to-Speech (TTS) capabilities.

## Features

* **Hindi-centric QA:** Answers questions related to MSME schemes, registration, and basic marketing in Hindi.
* **Retrieval-Augmented Generation (RAG):** Uses a vector database (ChromaDB) to retrieve relevant information before generating responses, ensuring factual accuracy based on provided context.
* **Local LLM Inference:** Employs a quantized GGUF model (OpenHathi-7B) for local execution, reducing reliance on external API costs and improving data privacy for sensitive queries.
* **Text-to-Speech (TTS):** Converts bot responses into audio using `gTTS` (Google Text-to-Speech) for an enhanced user experience.
* **FastAPI Web Interface:** Provides a simple, interactive web application to chat with the bot and demonstrate a campaign generation feature.
* **Campaign Generation (Prototype):** A dedicated form to generate marketing campaign text based on user-provided product details.
* **Google Colab Compatibility:** Designed to run easily within a Google Colab environment, leveraging its free resources (including optional GPU support).

## Technology Stack

* **Framework:** FastAPI
* **LLM Integration:** LangChain
* **Vector Database:** ChromaDB
* **Embeddings:** HuggingFace Embeddings (`sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2`)
* **Large Language Model (LLM):** OpenHathi-7B-Hi-v0.1-Base GGUF (from SarvamAI)
* **Text-to-Speech (TTS):** gTTS (Google Text-to-Speech)
* **Deployment (Local/Prototype):** Uvicorn, ngrok (for public access from Colab)
* **Python Libraries:** `langchain`, `langchain-community`, `chromadb`, `fastapi`, `uvicorn`, `gTTS`, `transformers`, `accelerate`, `llama-cpp-python`, `pyngrok`, `nest_asyncio`

## Setup and Running the Project (Google Colab)

This project is optimized for execution in Google Colab. Follow these steps to get your bot up and running:

1.  **Open Google Colab:** Go to [colab.research.google.com](https://colab.research.google.com/) and create a new notebook.
2.  **Organize Cells:** Copy and paste the code from each of the provided "Cells" (Cell 1, Cell 2, Cell 3, Cell 4) into separate code cells in your Colab notebook.
3.  **Enable GPU Runtime (Optional but Recommended for Speed):**
    * Go to `Runtime` -> `Change runtime type`.
    * Select `T4 GPU` (or a similar GPU if available) as the Hardware accelerator.
    * Click `Save`.
4.  **Get Ngrok Auth Token:**
    * Go to [ngrok.com](https://ngrok.com/) and sign up for a free account.
    * Navigate to your dashboard and find your "Authtoken". Copy this token.
    * In your Colab notebook, click on the "Secrets" (key icon) tab in the left sidebar.
    * Click "Add new secret".
        * For **Name**, type: `NGROK_AUTH_TOKEN`
        * For **Value**, paste your copied ngrok token.
    * Ensure "Notebook access" is toggled **on**.
    * **Crucially: After adding the secret, restart your Colab runtime** (`Runtime` -> `Restart runtime`). This ensures the environment variable is loaded.
5.  **Run Cells Sequentially:**
    * Execute **Cell 1 (Install Dependencies)**.
    * Execute **Cell 2 (Download LLM Model & Setup Directories)**. You should see messages confirming model download/existence and directory creation.
    * Execute **Cell 3 (Define FastAPI App, LangChain Components, and Endpoints)**. This will define the core logic and initialize LangChain. You should see "LangChain components initialized successfully."
    * Execute **Cell 4 (Run FastAPI with Ngrok)**.
        * **Important:** This cell will *block* and run indefinitely as it starts your web server.
        * Wait for the output to show:
            ```
            Public URL: NgrokTunnel: https://<your-ngrok-id>.ngrok-free.app -> "http://localhost:8000"
            ...
            INFO:     Application startup complete.
            ```
6.  **Access Your Bot:**
    * Once you see the `Public URL` from ngrok and the "Application startup complete." message, copy the `https://` URL.
    * Paste this URL into a new tab in your web browser.
    * You should now see the "BharatGPT MSME Bot (Prototype)" interface!

## How to Interact with the Bot

**1. Chat Functionality:**

* In the main chat interface, type your questions related to MSMEs in Hindi.
* **Example Questions (based on included data):**
    * `मुद्रा योजना क्या है?` (What is Mudra Yojana?)
    * `पंजीकरण कैसे करें?` (How to register?)
    * `स्टैंड-अप इंडिया योजना में कितना ऋण मिलता है?` (How much loan is available in Stand-Up India scheme?)
    * `मार्केटिंग अभियान कैसे बनाएं?` (How to create a marketing campaign?)
* Press "पूछें" (Ask) to get a response. The bot will reply in text and play an audio response.

**2. Campaign Generation (Prototype):**

* Below the chat, there's a section for "अभियान बनाएं" (Create Campaign).
* Fill in the fields:
    * `उत्पाद का नाम` (Product Name): e.g., `ऑर्गेनिक सब्जियां`
    * `छूट प्रतिशत` (Discount Percentage): e.g., `10%`
    * `वैधता` (Validity): e.g., `31 जुलाई तक`
* Click "अभियान टेक्स्ट जनरेट करें" (Generate Campaign Text) to get a generated marketing message.

## Project Structure (Conceptual)

While running in a single Colab notebook, conceptually, the project consists of:

* **`main.py` (simulated by Colab cells):** Contains the FastAPI application, API endpoints, and calls to LangChain components.
* **`models/`:** Directory to store the downloaded GGUF LLM model.
* **`static/`:** Directory to store generated audio files from gTTS.
* **`chroma_db/`:** Directory to store the persistent Chroma vector database.

## Troubleshooting

* **`ERR_NGROK_8012 / Connection refused`:** This means your FastAPI server is not running or not accessible on port 8000. Ensure Cell 4 is running and shows "Application startup complete." in its output.
* **`NameError: name 'app' is not defined`:** You have run the `uvicorn.run(app, ...)` command before the `app = FastAPI()` line (in Cell 3) has been executed. Always run cells in order.
* **`RuntimeError: Directory 'static' does not exist`:** The `static` directory was not created before FastAPI tried to mount it. Ensure `os.makedirs("static", exist_ok=True)` in Cell 2 (or early Cell 3) has run.
* **Bot not replying / Very slow replies:**
    * Check Colab output for errors during LLM inference or RAG retrieval.
    * Consider enabling GPU runtime and ensuring `llama-cpp-python` is installed with CUDA support (`n_gpu_layers=-1`).
    * The dummy LLM fallback will give very basic answers if the main LLM fails to load.
* **`DeprecationWarning: on_event is deprecated`:** This is just a warning and does not prevent the app from running. It's addressed by using `lifespan` in the provided code.

## Future Enhancements

* **Expand Knowledge Base:** Add more diverse and comprehensive data on MSME schemes, policies, and business advice.
* **Advanced RAG:** Implement more sophisticated retrieval strategies or multi-query approaches.
* **Chat History:** Maintain conversational history for better context.
* **User Interface:** Improve the frontend UI/UX.
* **Error Handling:** More robust error handling and user feedback.
* **Streaming Responses:** Implement streaming for faster perceived LLM responses.
* **Production Deployment:** Explore deployment options beyond ngrok for a permanent solution (e.g., Hugging Face Spaces, Render, AWS, GCP).
