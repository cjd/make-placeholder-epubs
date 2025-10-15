# Book Barcode Scanner & EPUB Generator

This project is a PHP-based web application that allows you to scan a book's barcode (ISBN) using your device's camera, fetch its metadata from various online sources, and generate a basic EPUB file.

My family has a lot of books collected over time and so I wanted to track all the physical books we have.  Although there are a few good self-hosted physical book management out there, but none are quite as user-friendly/nice to look at as some of the ebook management applications.  In particular I found [Booklore](https://github.com/booklore-app/booklore) which was close to perfect - but it couldn't handle bulk importing of physical books. There were some some references online to using placeholder epubs to represent physical books - but nothing which described how to do this (or a way to do it in bulk).  
What I really wanted was a way to scan the ISBN on each book, and have something take care of looking up the details, finding the metadata and creating the placeholder on my behalf.  
The result of that is this app.  
To integrate this into Booklore I set the bookdrop path to point to the epub directory where the placeholders are saved.

## Features

* **Barcode Scanning:** Uses your device's camera to scan ISBN barcodes directly in the browser.
* **Metadata Fetching:** Retrieves book information (title, subtitle, author, description, etc.) from Hardcover, Google Books, and Open Library.
* **Manual Search:** If an ISBN is not found, you can search for the book manually by title and author. The app will query both the Hardcover and Open Library APIs.
* **EPUB Generation:** Creates a simple `.epub` file with the fetched metadata and a cover image.
* **Web Interface:** Provides a user-friendly interface for scanning and generating EPUBs.

## Recent Changes

* **Enhanced Manual Search:** The manual search functionality now queries both the Hardcover and Open Library APIs, increasing the chances of finding your book.
* **Scrollable Book Selection:** When multiple book options are found, the selection dialog is now scrollable to accommodate a large number of results.
* **Subtitle Support:** The application now fetches, displays, and includes subtitles in the generated EPUB file.

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
   DEBUG_LOG_FILE=debug.log
   EPUB_DIR=epubs/
   ISBN_LIST_FILE=processed_isbns.txt
   ```

   Replace `"your_hardcover_bearer_token"` with your actual Hardcover API bearer token.

### How to get a Hardcover API Token

1. Go to your **Account Settings** on the Hardcover website.
2. Click on the **Hardcover API** link.
3. Your API token will be displayed at the top of the page.

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

4. **Generate the EPUB:**

   * A confirmation modal will appear with the fetched metadata.
   * Click the "Generate EPUB" button to create the `.epub` file.
   * The generated file will be saved in the `epubs/` directory.

## Dependencies

This application requires the following PHP extensions to be enabled:

* `zip`: For creating the EPUB file (which is a zip archive).
* `gd`: For processing and converting cover images.
* `fileinfo`: For detecting MIME types of downloaded cover images.
* `curl`: For making HTTP requests to the book metadata APIs.