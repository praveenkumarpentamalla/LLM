Of course! Here are clear, structured notes from the transcript, with a special explanation about using VS Code instead of JupyterLab.

---

### **First LM Experiment: Web Scraping & Summarization**

#### **1. Core Concepts: How to Talk to an LLM**

*   **Two Types of Prompts:** Modern LLMs expect two distinct types of instructions to understand a task fully.
    *   **System Prompt:** Sets the context, role, and rules for the AI.
        *   **Purpose:** Tells the AI *what its job is* and *how it should behave*.
        *   **Example:** "You are an assistant that analyzes the contents of a website and provides a short summary, ignoring texts that might be navigation related. Respond in markdown."
    *   **User Prompt:** The actual task or question from the user.
        *   **Purpose:** Tells the AI *what specific task to perform right now*.
        *   **Example:** "You are looking at a website titled [Title]. The contents are: [Website Text]. Please provide a short summary."

*   **How the LLM Works:**
    *   **Simple Explanation:** The LLM's job is to predict the most likely text that should come next in the "conversation" you've set up with the system and user prompts.
    *   **Real-World Analogy:** Imagine you tell a new employee (System Prompt): "You are a helpful customer service rep. Always be polite and concise." Then a customer asks (User Prompt): "My order is late, what should I do?" The employee uses the context from their job description to generate the most appropriate response.

#### **2. The Message Format: Structuring the Request**

*   **The Standard:** OpenAI created a standard format for conversations, which has been widely adopted by other AI models.
*   **The Structure:** The conversation is a **list of dictionaries** in Python.
    *   Each dictionary in the list represents one "message" in the conversation.
    *   Each dictionary has two keys:
        1.  `"role"`: Who is speaking (e.g., `"system"`, `"user"`).
        2.  `"content"`: What they are saying (the actual text of the prompt).

*   **Example in Code:**
    ```python
    messages = [
        {"role": "system", "content": "You are a helpful summarizer."},
        {"role": "user", "content": "Please summarize this text: ..."}
    ]
    ```

#### **3. Step-by-Step: Building the Summarization Tool**

##### **Step 1: Define the Prompts**
*   **System Prompt:** Fixed instruction telling the AI to be a summarizer that ignores navigation text and uses markdown.
*   **User Prompt Function:** A function that takes a website's text and title, and plugs them into a template to create the user's specific request.

##### **Step 2: Create the Messages List**
*   A function (`messages_for`) takes a website object, uses the `system_prompt` and the `user_prompt_for` function, and builds the correctly formatted list of message dictionaries.

##### **Step 3: Call the OpenAI API**
*   The core API call is made using the `openai` Python library.
    ```python
    response = openai.chat.completions.create(
        model="gpt-4o-mini",  # The AI model to use
        messages=messages      # The list of messages we built
    )
    ```
*   **`gpt-4o-mini`:** A smaller, faster, and cheaper version of OpenAI's powerful GPT-4 model, perfect for tasks like this that cost "fractions of a cent."

##### **Step 4: Extract the Response**
*   The AI's response is nested inside the returned object. The standard way to get the text is:
    ```python
    summary = response.choices[0].message.content
    ```

#### **4. Practical Application & Results**

*   The tool was successfully used to summarize:
    1.  The instructor's personal website.
    2.  **CNN.com** (capturing major news events like the 2024 elections).
    3.  **Anthropic's website** (the creators of the Claude AI).
*   **Markdown Output:** By instructing the AI to respond in markdown, the summaries are returned with formatting (like headings and bold text), which can be displayed nicely.

---

### **Expanding the Project: Ideas & Improvements**

#### **Business Applications of Summarization**
*   Summarizing news articles or financial reports.
*   Generating cover letters from resumes.
*   Condensing long documents or meeting transcripts.
*   Creating briefs from competitor websites.

#### **Technical Challenge: Dynamic Websites**
*   **The Problem:** The simple web scraper used (`BeautifulSoup`) only gets the initial HTML. Many modern websites use JavaScript to load content dynamically, which this scraper will miss.
*   **The Solution:** Use tools like **Selenium** or **Playwright**.
    *   These tools control a real web browser, allowing them to fully render a page and access all the dynamically loaded content, just like a human user would see it.
    *   A Selenium-based solution for this project is already available in the course's `community_contributions` folder.

#### **Contributing to the Project**
*   If you improve the code (e.g., add Selenium support), you are encouraged to share it.
*   **Process:**
    1.  **Clear your outputs** (Kernel -> Restart Kernel and Clear Outputs).
    2.  **Submit a Pull Request (PR)** to have your code merged into the main project repository for others to use.
    3.  Instructions for submitting a PR are provided in the course materials.

---

### **💻 Important Note: JupyterLab vs. VS Code**

The transcript shows the instructor using **JupyterLab**, but you are using **VS Code**. Here’s what that means for you:

*   **The Code is the Same:** The Python code for defining functions, calling the OpenAI API, and handling data is **identical**. You can copy and paste the code logic directly into your VS Code environment.
*   **The Difference is the "Notebook" Interface:**
    *   **JupyterLab** uses "notebooks" (`.ipynb` files) which let you run code in small, separate blocks (cells). This is great for experimentation.
    *   **VS Code** is a traditional code editor where you typically write a full Python script (`.py` file) and run it all at once, or use its interactive Python terminal for piece-by-piece execution.

*   **How to Adapt in VS Code:**
    1.  You can create a regular Python file (e.g., `summarizer.py`) and write all the code there.
    2.  Alternatively, you can use the **Jupyter extension** within VS Code to create and run `.ipynb` notebook files, giving you the same cell-based experience as JupyterLab.
    3.  When running a full script, you won't see the nice Markdown rendering by default. To see formatted output, you might need to print the summary and view it in a terminal that supports basic markdown, or save the output to a file.

---

### **Summary of Key Points**

*   LLMs use **system prompts** (for context) and **user prompts** (for the task).
*   Conversations are structured as a **list of message dictionaries** with `role` and `content`.
*   The **OpenAI API** call is simple: `openai.chat.completions.create(model="gpt-4o-mini", messages=messages)`.
*   **Web scraping + summarization** is a powerful, real-world business application.
*   For JavaScript-heavy websites, use **Selenium or Playwright** instead of simple HTML parsers.
*   The core logic is **environment-agnostic** and will work in both JupyterLab and VS Code.



## **Complete Web Scraping & Summarization Script**

### **1. First, create a `requirements.txt` file:**

```txt
openai
beautifulsoup4
requests
python-dotenv
markdown
```

### **2. Main Python script (`web_summarizer.py`):**

```python
import openai
import requests
from bs4 import BeautifulSoup
import os
from dotenv import load_dotenv
import markdown
from typing import Optional

# Load environment variables from .env file
load_dotenv()

# Set your OpenAI API key
openai.api_key = os.getenv("OPENAI_API_KEY")

class WebsiteScraper:
    """Handles scraping website content using BeautifulSoup"""
    
    def __init__(self, url: str):
        self.url = url
        self.title = ""
        self.content = ""
    
    def scrape(self) -> bool:
        """Scrape the website and extract main content"""
        try:
            headers = {
                'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
            }
            response = requests.get(self.url, headers=headers, timeout=10)
            response.raise_for_status()
            
            soup = BeautifulSoup(response.content, 'html.parser')
            
            # Get page title
            self.title = soup.title.string if soup.title else "No Title"
            
            # Remove script and style elements
            for script in soup(["script", "style", "nav", "header", "footer"]):
                script.decompose()
            
            # Get text content
            self.content = soup.get_text(separator=' ', strip=True)
            
            # Limit content length to avoid token limits
            self.content = self.content[:8000]  # Adjust as needed
            
            return True
            
        except Exception as e:
            print(f"Error scraping website: {e}")
            return False

def create_system_prompt() -> str:
    """Define the system prompt that sets the AI's role"""
    return """You are an assistant that analyzes the contents of a website and provides a short summary, 
    ignoring texts that might be navigation related. Focus on the main content, key information, 
    and important announcements. Respond in markdown format with clear headings and bullet points."""

def create_user_prompt(website_title: str, website_content: str) -> str:
    """Create the user prompt with website content"""
    return f"""You are looking at a website titled: {website_title}

The contents of this website is as follows:

{website_content}

Please provide a short summary of the website in markdown. If it includes news or announcements, 
summarize these too. Focus on the most important information that a visitor would want to know."""

def create_messages(website_title: str, website_content: str) -> list:
    """Create the messages list in OpenAI format"""
    return [
        {"role": "system", "content": create_system_prompt()},
        {"role": "user", "content": create_user_prompt(website_title, website_content)}
    ]

def summarize_website(url: str) -> Optional[str]:
    """
    Main function to summarize a website
    Returns: Summary text or None if error
    """
    print(f"🔄 Scraping website: {url}")
    
    # Step 1: Scrape the website
    scraper = WebsiteScraper(url)
    if not scraper.scrape():
        return None
    
    print(f"✅ Successfully scraped: {scraper.title}")
    
    # Step 2: Create messages for OpenAI
    messages = create_messages(scraper.title, scraper.content)
    
    # Step 3: Call OpenAI API
    try:
        print("🤖 Calling OpenAI API...")
        response = openai.chat.completions.create(
            model="gpt-4o-mini",  # You can use "gpt-4" for better results
            messages=messages,
            max_tokens=500,
            temperature=0.3
        )
        
        # Step 4: Extract the response
        summary = response.choices[0].message.content
        return summary
        
    except Exception as e:
        print(f"Error calling OpenAI API: {e}")
        return None

def display_summary(summary: str):
    """Display the summary in a formatted way"""
    print("\n" + "="*80)
    print("📝 WEBSITE SUMMARY")
    print("="*80)
    print(summary)
    print("="*80)

def save_summary_to_file(summary: str, filename: str = "website_summary.md"):
    """Save the summary to a markdown file"""
    with open(filename, 'w', encoding='utf-8') as f:
        f.write(summary)
    print(f"💾 Summary saved to: {filename}")

def main():
    """Main function to run the summarizer"""
    print("🌐 Website Summarization Tool")
    print("=" * 50)
    
    # Example URLs to test
    test_urls = [
        "https://example.com",
        # Add your own URLs here
    ]
    
    while True:
        print("\nOptions:")
        print("1. Enter a URL to summarize")
        print("2. Use test URLs")
        print("3. Exit")
        
        choice = input("\nEnter your choice (1-3): ").strip()
        
        if choice == "1":
            url = input("Enter the URL to summarize: ").strip()
            if not url.startswith(('http://', 'https://')):
                url = 'https://' + url
            
            summary = summarize_website(url)
            if summary:
                display_summary(summary)
                
                # Ask to save
                save_choice = input("\nSave summary to file? (y/n): ").lower()
                if save_choice == 'y':
                    save_summary_to_file(summary)
        
        elif choice == "2":
            print("\nTesting with example URLs...")
            for url in test_urls:
                print(f"\n--- Testing: {url} ---")
                summary = summarize_website(url)
                if summary:
                    display_summary(summary)
                    print("\n" + "-"*50)
        
        elif choice == "3":
            print("Goodbye!")
            break
        
        else:
            print("Invalid choice. Please try again.")

# Alternative: Simple one-url version
def quick_summarize(url: str):
    """Quick function to summarize a single URL"""
    summary = summarize_website(url)
    if summary:
        display_summary(summary)
        return summary
    else:
        print("Failed to generate summary.")
        return None

if __name__ == "__main__":
    main()
```

### **3. Environment configuration (`.env` file):**

```env
OPENAI_API_KEY=your_openai_api_key_here
```

### **4. Selenium version for dynamic websites (`selenium_summarizer.py`):**

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.by import By
from web_summarizer import create_messages, summarize_website, display_summary
import time

class SeleniumScraper:
    """Handles scraping JavaScript-heavy websites using Selenium"""
    
    def __init__(self, url: str):
        self.url = url
        self.title = ""
        self.content = ""
        
    def setup_driver(self):
        """Setup Chrome driver with options"""
        chrome_options = Options()
        chrome_options.add_argument("--headless")  # Run in background
        chrome_options.add_argument("--no-sandbox")
        chrome_options.add_argument("--disable-dev-shm-usage")
        
        self.driver = webdriver.Chrome(options=chrome_options)
    
    def scrape(self) -> bool:
        """Scrape website using Selenium"""
        try:
            self.setup_driver()
            self.driver.get(self.url)
            
            # Wait for page to load
            time.sleep(3)
            
            # Get page title
            self.title = self.driver.title
            
            # Get main content - you might need to adjust selectors based on the website
            try:
                # Try to get main content area
                main_content = self.driver.find_element(By.TAG_NAME, "main")
                self.content = main_content.text
            except:
                # Fallback to body text
                body = self.driver.find_element(By.TAG_NAME, "body")
                self.content = body.text
            
            # Limit content length
            self.content = self.content[:8000]
            
            self.driver.quit()
            return True
            
        except Exception as e:
            print(f"Error with Selenium scraping: {e}")
            if hasattr(self, 'driver'):
                self.driver.quit()
            return False

def summarize_with_selenium(url: str):
    """Summarize a website using Selenium for JavaScript-heavy sites"""
    print(f"🔧 Using Selenium for: {url}")
    
    scraper = SeleniumScraper(url)
    if not scraper.scrape():
        return None
    
    # Use the same OpenAI logic from the main script
    messages = create_messages(scraper.title, scraper.content)
    
    try:
        import openai
        response = openai.chat.completions.create(
            model="gpt-4o-mini",
            messages=messages,
            max_tokens=500
        )
        return response.choices[0].message.content
    except Exception as e:
        print(f"Error: {e}")
        return None

# Example usage
if __name__ == "__main__":
    # Test with a JavaScript-heavy site
    url = "https://example.com"  # Replace with actual JS-heavy site
    summary = summarize_with_selenium(url)
    if summary:
        display_summary(summary)
```

## **How to Run This in VS Code:**

### **Setup:**
1. **Install requirements:**
   ```bash
   pip install -r requirements.txt
   ```

2. **Get OpenAI API key:**
   - Go to [OpenAI Platform](https://platform.openai.com/)
   - Create an account and get your API key
   - Add it to your `.env` file

3. **For Selenium version:**
   ```bash
   pip install selenium webdriver-manager
   ```
   - You'll also need Chrome browser installed

### **Running the Code:**

**Option 1: Run the main script**
```python
# In VS Code, you can run this directly
if __name__ == "__main__":
    main()
```

**Option 2: Use the quick function**
```python
# In VS Code's Python interactive window or a separate script
from web_summarizer import quick_summarize

summary = quick_summarize("https://example.com")
```

**Option 3: Import and use in your own code**
```python
from web_summarizer import summarize_website, display_summary

# Summarize multiple websites
urls = [
    "https://news.ycombinator.com",
    "https://github.com",
    "https://stackoverflow.com"
]

for url in urls:
    print(f"\nSummarizing: {url}")
    summary = summarize_website(url)
    if summary:
        display_summary(summary)
```

## **Key Features of This Code:**

- ✅ **Complete implementation** of all concepts from the transcript
- ✅ **Error handling** for network issues and API errors
- ✅ **Configurable** via environment variables
- ✅ **Both BeautifulSoup and Selenium** versions
- ✅ **File output** option to save summaries
- ✅ **Well-structured** with classes and functions
- ✅ **VS Code compatible** - runs as a standard Python script
- ✅ **Type hints** for better code clarity

