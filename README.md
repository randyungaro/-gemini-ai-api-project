# Gemini API Server

This project provides a simple Express.js server that interacts with the Google Gemini API to perform various generative AI tasks, 
including text generation, image analysis, document analysis, and audio transcription/analysis.

## Features

- **Text Generation:** Generate text based on a given prompt.
- **Image Analysis:** Describe or analyze images uploaded by the user.
- **Document Analysis:** Analyze various document types (e.g., PDFs, text files) and extract information.
- **Audio Transcription/Analysis:** Transcribe or analyze audio files.

## Technologies Used

- **Node.js:** JavaScript runtime.
- **Express.js:** Web framework for Node.js.
- **`@google/generative-ai`:** Google's official Node.js client library for the Gemini API.
- **`dotenv`:** To load environment variables from a `.env` file.
- **`multer`:** Middleware for handling `multipart/form-data`, primarily used for uploading files.
- **`fs`:** Node.js built-in module for file system operations.

## Setup and Installation

### Prerequisites

- Node.js (v14 or higher recommended)
- A Google Cloud Project with the Gemini API enabled.
- An API Key for the Gemini API.

### Steps

1.  **Clone the repository:**

    ```bash
    git clone https://github.com/randyungaro/-gemini-ai-api-project
   
    ```

2.  **Install dependencies:**

    ```bash
    npm install
    ```

3.  **Create a `.env` file:**
    In the root of your project, create a file named `.env` and add your Gemini API key:

    ```
    GEMINI_API_KEY=YOUR_GEMINI_API_KEY
    ```

    Replace `YOUR_GEMINI_API_KEY` with your actual API key obtained from the Google Cloud Console.

4.  **Create an `uploads` directory:**
    The server uses `multer` to store uploaded files temporarily in an `uploads/` directory. Create this directory in the root of your project:

    ```bash
    mkdir uploads
    ```

## Running the Server

To start the server, run the following command:

```bash
node server.js
```

The server will start on `http://localhost:3000`. You should see the following message in your console:

```
Gemini API server is running at http://localhost:3000
```

## API Endpoints

All endpoints respond with a JSON object containing either the `output` from the Gemini API or an `error` message.

### 1. Generate Text

-   **Endpoint:** `POST /generate-text`
-   **Description:** Generates text based on a provided prompt.
-   **Request Body (JSON):**
    ```json
    {
        "prompt": "Write a short story about a futuristic city."
    }
    ```
-   **Example Response (Success):**
    ```json
    {
        "output": "In the gleaming spires of Neo-Veridia, where hovercars hummed..."
    }
    ```
-   **Example Response (Error):**
    ```json
    {
        "error": "API key not valid. Please pass a valid API key."
    }
    ```

### 2. Generate from Image

-   **Endpoint:** `POST /generate-from-image`
-   **Description:** Describes or analyzes an uploaded image.
-   **Request Body (`multipart/form-data`):**
    -   `image`: The image file to upload.
    -   `prompt` (optional): A text prompt to guide the image analysis (e.g., "What is in this picture?"). If not provided, it defaults to "Describe the image".
-   **Example using `curl`:**

    ```bash
    curl -X POST -F "image=@/path/to/your/image.jpg" -F "prompt=What is this a picture of?" http://localhost:3000/generate-from-image
    ```
-   **Example Response (Success):**
    ```json
    {
        "output": "The image shows a bustling market street in an old town, with vendors selling fresh produce and crafts."
    }
    ```

### 3. Generate from Document

-   **Endpoint:** `POST /generate-from-document`
-   **Description:** Analyzes an uploaded document.
-   **Request Body (`multipart/form-data`):**
    -   `document`: The document file to upload (e.g., PDF, TXT).
-   **Example using `curl`:**

    ```bash
    curl -X POST -F "document=@/path/to/your/document.pdf" http://localhost:3000/generate-from-document
    ```
-   **Example Response (Success):**
    ```json
    {
        "output": "This document appears to be a technical specification for a new drone model, detailing its features and performance metrics."
    }
    ```

### 4. Generate from Audio

-   **Endpoint:** `POST /generate-from-audio`
-   **Description:** Transcribes or analyzes an uploaded audio file.
-   **Request Body (`multipart/form-data`):**
    -   `audio`: The audio file to upload (e.g., MP3, WAV).
-   **Example using `curl`:**

    ```bash
    curl -X POST -F "audio=@/path/to/your/audio.mp3" http://localhost:3000/generate-from-audio
    ```
-   **Example Response (Success):**
    ```json
    {
        "output": "The audio contains a clear speech discussing the benefits of renewable energy."
    }
    ```

## Error Handling

The server includes basic error handling. If an error occurs during the API call, it will return a `500` status code with a JSON object containing the `error` message.

## Important Notes

-   **MIME Types:** For image, document, and audio uploads, ensure the `mimeType` passed to the Gemini API is correct. The current `imageToGenerativePart` function assumes `image/jpeg`. You might need to extend this to dynamically detect MIME types or handle different image formats. For documents and audio, `req.file.mimetype` is used, which `multer` provides.
-   **File Deletion:** Uploaded files are temporarily stored in the `uploads/` directory and are deleted after processing using `fs.unlinkSync(req.file.path)` in a `finally` block to ensure cleanup even if an error occurs.
-   **API Key Security:** Never expose your `GEMINI_API_KEY` directly in client-side code. Always use a backend server to make calls to the Gemini API.
-   **Model:** The code uses `models/gemini-1.5-flash`. You can change this to other available Gemini models as needed.

## Contributing

Feel free to fork the repository and submit pull requests.

## License

MIT License
