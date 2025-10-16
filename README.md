# Book Barcode Scanner & EPUB Generator

This project is a PHP-based web application that allows you to scan a book's barcode (ISBN) using your device's camera, fetch its metadata from various online sources, and generate a basic EPUB file.

My family has a lot of books collected over time and so I wanted to track all the physical books we have.  Although there are a few good self-hosted physical book management out there, but none are quite as user-friendly/nice to look at as some of the ebook management applications.  In particular I found [Booklore](https://github.com/booklore-app/booklore) which was close to perfect - but it couldn't handle bulk importing of physical books. There were some some references online to using placeholder epubs to represent physical books - but nothing which described how to do this (or a way to do it in bulk).  
What I really wanted was a way to scan the ISBN on each book, and have something take care of looking up the details, finding the metadata and creating the placeholder on my behalf.  
The result of that is this app.  
To integrate this into Booklore I set the bookdrop path to point to the epub directory where the placeholders are saved.  
Note that since this is just creating placeholder epub files you are not limited to Booklore, but it should work just as well with Calibre or other ebook managers.

## Features

* **Barcode Scanning:** Uses your device's camera to scan ISBN barcodes directly in the browser.
* **Metadata Fetching:** Retrieves book information (title, subtitle, author, description, etc.) from Hardcover, Google Books, and Open Library.
* **Manual Search:** If an ISBN is not found, you can search for the book manually by title and author. The app will query both the Hardcover and Open Library APIs.
* **Gemini Vision Search:** You can also take a photo of the book cover and Gemini will try to determine the title/author from that image.  If no match is found after that you can generate a book using the cover photo+title+author (good for books that don't exist in any of the metadata sources)
* **EPUB Generation:** Creates a simple `.epub` file with the fetched metadata and a cover image.
* **Web Interface:** Provides a user-friendly interface for scanning and generating EPUBs.

## Recent Changes

* **Scan Book Cover:** The application can now use your device's camera to scan a book's cover.
* **Gemini Vision Integration:** The "Scan Book Cover" feature uses the Gemini API to identify the book's title and author from the cover image.
* **Multiple Metadata Options:** When you scan a book cover, you will be presented with multiple metadata options to choose from, including the option suggested by Gemini Vision.
* **Captured Image Preview:** After capturing a book cover, the application will display the captured image, allowing you to verify it before proceeding.

## Configuration

Before running the application, you need to create a `.env` file in the root of the project to store your API credentials.

1. **Create the .env file:**

   ```bash
   touch .env
    ```

2. **Add the configuration variables:**

   Open the `.env` file and add the following lines:

   ```.env
   HARDCOVER_BEARER_TOKEN="your_hardcover_bearer_token"
   GEMINI_API_KEY="your_gemini_api_key"
   DEBUG_LOG_FILE=debug.log
   EPUB_DIR=epubs/
   ISBN_LIST_FILE=processed_isbns.txt
   ```

   Replace `"your_hardcover_bearer_token"` with your actual Hardcover API bearer token.
   Replace `"your_gemini_api_key"` with your actual Gemini API key.

### How to get a Hardcover API Token

1. Go to your **Account Settings** on the Hardcover website.
2. Click on the **Hardcover API** link.
3. Your API token will be displayed at the top of the page.

### How to get a Gemini API Key

1. Go to [Google AI Studio](https://aistudio.google.com/).
2. Sign in with your Google account.
3. Click on the "Get API key" button.
4. Your API key will be displayed.

## Usage

1. **Start the PHP Server:**

   You can use PHP's built-in web server to run the application. Open your terminal in the project's root directory and run the following command:

   ```bash
   php -S localhost:8000
   ```

2. **Open the Application:**

   Open your web browser and navigate to `http://localhost:8000`.

3. **Scan a Barcode:**

   * Allow the browser to access your camera.
   * Point your camera at a book's barcode.
   * The application will automatically detect the ISBN and fetch the book's metadata.

4. **Scan a Book Cover:**

   * Click the "Scan Cover" button.
   * Allow the browser to access your camera.
   * Position the book's cover in the frame and click "Capture & Analyze".
   * The application will display the captured image and a list of metadata options.
   * Select the desired metadata option.

5. **Generate the EPUB:**

   * A confirmation modal will appear with the fetched metadata.
   * Click the "Generate EPUB" button to create the `.epub` file.
   * The generated file will be saved in the `epubs/` directory.

## Dependencies

This application requires the following PHP extensions to be enabled:

* `zip`: For creating the EPUB file (which is a zip archive).
* `gd`: For processing and converting cover images.
* `fileinfo`: For detecting MIME types of downloaded cover images.
* `curl`: For making HTTP requests to the book metadata APIs.
