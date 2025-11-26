# Exercise: Using Local LLMs with Ollama - Complete Guide

## **Exercise Overview**

### **Objective:**
Convert your existing web summarization project from using OpenAI's cloud API to using a local LLM running with Ollama.

### **What You'll Learn:**
- How to call local LLMs from Python code
- The differences between cloud APIs and local model execution
- How to modify existing AI applications to work offline

---

## **Prerequisites Setup**

### **1. Environment Preparation**
```bash
# Navigate to your project directory
cd LM-engineering

# Activate your environment
conda activate LMS  # OR: source venv/bin/activate (Mac) OR venv\Scripts\activate (PC)

# Start JupyterLab
jupyterlab
```

### **2. Verify Ollama Installation**
- Go to `http://localhost:11434` in your browser
- You should see: **"Ollama is running"**

#### **Troubleshooting:**
If Ollama isn't running:
```bash
# Start Ollama server
ollama serve
```

---

## **Two Methods to Call Local LLMs**

### **Method 1: Direct HTTP Requests (Understanding the Basics)**

#### **Step-by-Step Implementation:**

```python
import requests
import json

# Configuration
OLLAMA_URL = "http://localhost:11434/api/chat"
MODEL_NAME = "llama3.2"  # or any model you have installed

# Create the message format (same as OpenAI)
messages = [
    {
        "role": "user",
        "content": "Describe some of the business applications of generative AI."
    }
]

# Prepare the request payload
payload = {
    "model": MODEL_NAME,
    "messages": messages,
    "stream": False  # Get complete response at once
}

# Make the API call
response = requests.post(OLLAMA_URL, json=payload)

# Extract the response
if response.status_code == 200:
    result = response.json()
    ai_response = result['message']['content']
    print(ai_response)
else:
    print(f"Error: {response.status_code}")
```

#### **What's Happening Here:**
- **Local Web Server:** Ollama runs a web server on your computer (`localhost:11434`)
- **Same Message Format:** Uses the exact same `role`/`content` structure as OpenAI
- **HTTP POST Request:** Your Python code talks to the local model via web requests

### **Method 2: Using Ollama Python Package (Simplified)**

```python
import ollama

# Much simpler approach - one line call
response = ollama.chat(
    model='llama3.2',
    messages=[
        {
            'role': 'user',
            'content': 'Describe some of the business applications of generative AI.'
        }
    ]
)

print(response['message']['content'])
```

---

## **Exercise: Convert Web Summarizer to Use Local LLM**

### **Your Task:**
Take the web summarization code from Day 1 and modify it to use Ollama instead of OpenAI.

### **Current OpenAI Code (What You Need to Change):**
```python
# OLD CODE - OpenAI version
response = openai.chat.completions.create(
    model="gpt-4o-mini",
    messages=messages
)
summary = response.choices[0].message.content
```

### **New Local LLM Code Options:**

#### **Option A: Using Ollama Python Package (Recommended)**
```python
import ollama

def summarize_with_ollama(messages):
    """Replace OpenAI call with Ollama"""
    response = ollama.chat(
        model='llama3.2',  # Make sure this model is installed
        messages=messages
    )
    return response['message']['content']
```

#### **Option B: Using Direct HTTP Requests**
```python
import requests
import json

def summarize_with_ollama_http(messages):
    """Alternative using direct HTTP calls"""
    payload = {
        "model": "llama3.2",
        "messages": messages,
        "stream": False
    }
    
    response = requests.post(
        "http://localhost:11434/api/chat",
        json=payload
    )
    
    if response.status_code == 200:
        return response.json()['message']['content']
    else:
        return f"Error: {response.status_code}"
```

### **Complete Integration Example:**
```python
def summarize_website_local(url):
    """Modified version using local Ollama"""
    
    # Step 1: Scrape website (same as before)
    website = scrape_website(url)  # Your existing scraping function
    
    # Step 2: Create messages (same format as before)
    messages = [
        {
            "role": "system", 
            "content": "You are an assistant that analyzes website contents and provides short summaries."
        },
        {
            "role": "user",
            "content": f"Please summarize this website: {website.content}"
        }
    ]
    
    # Step 3: Call LOCAL model instead of OpenAI
    summary = summarize_with_ollama(messages)
    
    return summary
```

---

## **Key Benefits of Using Local Models**

### **Advantages:**
1. **💰 No API Costs:** Completely free to use
2. **🔒 Data Privacy:** Your data never leaves your computer
3. **🌐 Offline Capability:** Works without internet connection
4. **⚡ No Rate Limits:** Use as much as you want

### **Disadvantages:**
1. **🤖 Lower Performance:** Local models are smaller and less powerful than frontier models like GPT-4
2. **💻 Hardware Requirements:** Needs sufficient RAM and processing power
3. **📚 Limited Context:** Smaller context windows than cloud models

### **Real-World Use Cases for Local LLMs:**
- **Confidential Documents:** Summarizing internal company documents that can't be sent to the cloud
- **Personal Data:** Processing emails, notes, or personal information privately
- **Development & Testing:** Rapid prototyping without incurring API costs
- **Offline Applications:** Tools that need to work in environments without internet

---

## **Expected Results & Comparison**

### **OpenAI (Cloud) vs Ollama (Local):**

| **Aspect** | **OpenAI GPT-4** | **Local Llama 3.2** |
|------------|------------------|---------------------|
| **Cost** | Pay per request | Free |
| **Privacy** | Data sent to cloud | 100% local |
| **Performance** | State-of-the-art | Good, but limited |
| **Speed** | Fast (powerful servers) | Depends on your hardware |
| **Setup** | API key required | Local installation |

### **What to Expect:**
- Local summaries will be **good but not as sophisticated** as GPT-4
- Responses might be **shorter or less nuanced**
- **Still very useful** for many business applications
- **Perfectly adequate** for internal tools and prototypes

---

## **Troubleshooting Tips**

### **Common Issues & Solutions:**

1. **"Model not found" error:**
   ```bash
   # Download the model first
   ollama pull llama3.2
   ```

2. **Ollama not running:**
   ```bash
   # Start the service
   ollama serve
   ```

3. **Port already in use:**
   - Check if another instance is running
   - Restart your computer if needed

4. **Out of memory:**
   - Try a smaller model
   - Close other memory-intensive applications

---

## **Extension Opportunities**

### **Try Different Models:**
```python
# Experiment with other available models
models_to_try = [
    "llama3.2",
    "mistral",
    "qwen2.5",
    "gemma2"
]

for model in models_to_try:
    print(f"Testing {model}...")
    # Your summarization code here
```

### **Compare Results:**
- Run the same website through both OpenAI and local models
- Compare the quality, length, and usefulness of summaries
- Document the differences for your specific use case

---

## **Summary of Key Points**

1. **Local LLMs** provide a free, private alternative to cloud APIs
2. **Ollama** makes it easy to run models locally with a simple API
3. **Two calling methods:** Direct HTTP requests or Ollama Python package
4. **Same message format** as OpenAI - easy to switch between platforms
5. **Trade-offs:** Privacy and cost savings vs. slightly lower quality
6. **Perfect for:** Confidential data, prototyping, and offline applications
7. **Your exercise:** Convert the web summarizer to use local models instead of OpenAI

### **Next Steps:**
1. Complete the conversion exercise
2. Test with multiple websites
3. Compare results with the original OpenAI version
4. Consider when you'd choose local vs cloud models in real projects

This exercise gives you practical experience with the growing ecosystem of open-source LLMs and prepares you for building applications that prioritize privacy and cost-efficiency!
