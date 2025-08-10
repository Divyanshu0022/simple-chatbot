# 🤖 Chatbot Project — Step-by-Step Workflow
<img width="1920" height="1034" alt="Screenshot (422)" src="https://github.com/user-attachments/assets/1769fc73-c551-4cc1-bbc2-80510cb68367" />

This project builds a chatbot using **LiteLLM** and **Gradio**, evolving from a simple Q&A bot to a context-aware assistant with a modern UI and format handling.

---

## 🧠 Step 1: The Core Problem — A Simple Q&A Bot

**Problem:** We want a bot that answers a user's question.

**Solution:** Create a function that sends the user's message to an AI model and returns the response.

```python
# A very simple, memoryless function
def get_answer(message):
    messages = [{"role": "user", "content": message}]
    response = litellm.completion(model="gemini/gemini-pro", messages=messages)
    return response.choices[0].message.content
```

---

## 🧠 Step 2: The Next Challenge — Adding Memory

**Problem:** The bot forgets previous messages. It can't resolve references like "its" or "that."

**Solution:** Send the entire conversation history along with the new message.

```python
# Our function now needs two parameters
def chat_with_memory(message, history):
    # Combine history and new message to maintain context
    pass
```

---

## 🎨 Step 3: The UI Problem — Making It Usable

**Problem:** Command-line interfaces aren't user-friendly.

**Solution:** Use Gradio's `ChatInterface` to create a browser-based chat UI.

```python
import gradio as gr

# Connect Gradio to our chat function
gr.ChatInterface(fn=chat).launch()
```

Gradio expects the function to accept two parameters: `message` and `history`.

---

## 🔄 Step 4: The Format Problem — Bridging Gradio and AI

**Problem:** Format mismatch between Gradio and AI APIs.

- Gradio history: `[[user_msg, bot_reply], ...]`
- AI expects: `[{"role": "user", "content": ...}, {"role": "assistant", "content": ...}]`

**Modern Solution:** Add `type="messages"` to Gradio interface.

```python
# The magic switch that solves our format problem
gr.ChatInterface(fn=chat, type="messages").launch()
```

This tells Gradio to auto-convert history into AI-ready format.

---

## 🧩 Step 5: The Final Workflow — Putting It All Together

Define the final chat function with full context and streaming response:

```python
def chat(message, history):
    # 1. System prompt
    system_prompt = [{"role": "system", "content": "You are a helpful assistant."}]
    
    # 2. Combine system prompt, history, and new message
    messages = system_prompt + history + [{"role": "user", "content": message}]
    
    # 3. Stream response from LiteLLM
    stream = litellm.completion(model="gemini/gemini-pro", messages=messages, stream=True)
    response = ""
    for chunk in stream:
        response += chunk.choices[0].delta.content or ''
        yield response
```

---

## 🧠 Bonus: Multi-Shot Prompting

We also use **multi-shot prompts** to guide the AI's behavior:

- Define how it should respond
- Include examples
- Handle edge cases like specific keywords or formats

This makes the bot more reliable and tailored to your use case.

---

