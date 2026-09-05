# GenAI Fundamentals

## For .NET Full Stack Developers

> **Goal:** Understand the basic GenAI concepts in simple language and be able to explain them confidently in interviews.

---

# 1. What is Generative AI?

## Simple Definition

**Generative AI is a type of Artificial Intelligence that can create new content based on the instructions or input provided by the user.**

It can generate:

- Text
- Code
- Images
- Audio
- Video
- SQL queries
- Test cases
- Documentation
- Summaries
- Emails

### Example

If we give AI this prompt:

```text
Create unit test cases for this .NET Core API.
```

The AI can generate:

```csharp
[Fact]
public async Task GetUser_ShouldReturnUser_WhenUserExists()
{
    // Arrange

    // Act

    // Assert
}
```

This is called **Generative AI** because the system is generating new content.

---

## Traditional Software vs Generative AI

### Traditional Software

```text
Input
  ↓
Business Logic
  ↓
Output
```

Example:

```text
GET /api/users/10
       ↓
 UserService
       ↓
  Database
       ↓
 User JSON
```

The developer explicitly defines the logic.

---

### Generative AI

```text
Prompt / Input
      ↓
   AI Model
      ↓
Generated Content
```

Example:

```text
"Create test cases for User API"
             ↓
          AI Model
             ↓
Test cases + Edge cases + Code
```

The AI generates the output based on patterns learned during training.

---

# 2. What is an LLM?

## Full Form

**LLM = Large Language Model**

Examples:

- GPT
- Gemini
- Claude
- Llama

---

## Simple Definition

An **LLM is a large AI model trained on huge amounts of data that can understand and generate human-like text and code.**

You can think of an LLM as the **brain behind many Generative AI applications**.

---

## Simple Example

Suppose we ask:

```text
What is the capital of India?
```

The LLM generates:

```text
New Delhi
```

Or we ask:

```text
Create a C# method to add two numbers.
```

It can generate:

```csharp
public int Add(int a, int b)
{
    return a + b;
}
```

---

# LLM from a .NET Developer's Perspective

As a .NET developer, you can think about an LLM as an external service that your application can communicate with through an API.

```text
Your .NET Application
        |
        | Prompt
        ↓
      LLM API
        |
        | Response
        ↓
Your .NET Application
```

For example:

```text
.NET Web API
      ↓
Gemini / GPT / Claude
      ↓
Generated Test Cases
```

This is similar to consuming other external APIs.

For example:

```text
Your Application
      ↓
Payment API
      ↓
Payment Response
```

Similarly:

```text
Your Application
      ↓
LLM API
      ↓
AI Response
```

---

## Why is it called "Large"?

It is called **Large Language Model** because these models are trained using enormous amounts of data and contain a very large number of learned parameters.

You don't need to understand the mathematical details initially.

For interviews, remember:

> **An LLM is a large AI model trained on huge amounts of data to understand and generate human-like text and code.**

---

# 3. What are Tokens?

## Simple Definition

**Tokens are the small pieces of text that an LLM processes.**

An LLM doesn't simply process text exactly the way humans read it.

Text is broken into smaller pieces called **tokens**.

---

## Example

Suppose we provide:

```text
I love programming
```

The tokenizer may represent it approximately as:

```text
"I"
" love"
" programming"
```

These pieces are called tokens.

> The exact tokenization depends on the model and tokenizer.

---

# Token Does NOT Always Mean One Word

This is very important.

A token can be:

- A complete word
- Part of a word
- Punctuation
- A number
- A special text sequence

For example, a long or uncommon word may be split into multiple tokens.

Therefore:

```text
1 word ≠ necessarily 1 token
```

---

# Why are Tokens Important?

Tokens are important mainly because of three reasons.

## 1. API Cost

Many LLM APIs calculate cost based on the number of tokens processed.

Conceptually:

```text
Input Tokens
+
Output Tokens
=
Total Tokens
```

More tokens can mean higher cost.

---

## 2. Context Window

An LLM can process only a certain maximum number of tokens at a time.

That maximum capacity is called the **Context Window**.

---

## 3. Performance

Sending unnecessary information increases the number of tokens.

For example:

```text
100-page document
+
50-page documentation
+
Large source code
+
Previous conversation
```

can consume a large number of tokens.

Therefore, GenAI applications try to provide the model with only the information that is relevant.

This concept becomes very important when learning **RAG (Retrieval-Augmented Generation)**.

---

# 4. What is Context Window?

## Simple Definition

> **Context Window is the maximum amount of information, measured in tokens, that an LLM can process and consider at one time.**

Think of it as the model's **working space**.

---

# Simple Real-Life Example

Imagine you have a small notebook.

You can write only a certain amount of information in that notebook.

Once the notebook is full, you cannot keep adding unlimited information.

Similarly, an LLM has a maximum amount of information it can process within one context.

---

# Context in a Conversation

Consider this conversation:

```text
User:
My name is Nikhil.

AI:
Nice to meet you Nikhil.

User:
What is my name?

AI:
Your name is Nikhil.
```

The AI needs the previous information:

```text
My name is Nikhil
```

to answer the question correctly.

That previous information is part of the **context**.

---

# Context Window in a GenAI Application

The context can contain multiple types of information:

```text
System Instructions
        +
User Prompt
        +
Conversation History
        +
Documents
        +
API Definition
        +
Additional Information
        ↓
   Context Window
        ↓
       LLM
        ↓
     Response
```

All this information consumes tokens.

---

# Example with a .NET API

Suppose we are building a **GenAI Test Case Generator**.

We provide:

```text
Generate test cases for this API.

POST /api/users

Request:
{
    "name": "John",
    "email": "john@test.com",
    "age": 30
}
```

The LLM may need to consider:

```text
Prompt
+
API endpoint
+
HTTP method
+
Request model
+
Business rules
+
Instructions
```

All of this becomes part of the context.

The LLM uses this context to generate the answer.

---

# Context Window Example

Suppose, for simplicity, a model has a context window of:

```text
100 tokens
```

And we send:

```text
80 tokens
```

Then there is limited space remaining for the generated response.

If we attempt to send:

```text
500 tokens
```

we exceed the model's context capacity.

> Modern LLMs can support much larger context windows than this example. The numbers here are only for understanding the concept.

---

# Context Window vs Memory

These concepts are related but not the same.

## Context Window

Context window means:

> **The information available to the model during the current processing context.**

It can include:

```text
Prompt
Conversation
Instructions
Documents
Retrieved information
```

---

## Memory

Memory refers to information that may be retained or provided across interactions, depending on the AI application's architecture and configuration.

So don't think:

```text
Context Window = Permanent Memory
```

They are different concepts.

---

# How These Four Concepts Work Together

This is the most important section.

Imagine our **GenAI Test Case Generator**.

We send:

```text
Generate test cases for this API.

POST /api/users

Request:
{
    "name": "John",
    "email": "john@test.com"
}
```

---

## Step 1 — Generative AI

Generative AI is the overall technology that generates new content.

Output:

```text
Test Cases
```

---

## Step 2 — LLM

The LLM is the AI model that understands the input and generates the response.

```text
Your Application
       ↓
      LLM
       ↓
Generated Test Cases
```

---

## Step 3 — Tokens

The input and output are processed as tokens.

Conceptually:

```text
"Generate test cases for this API"
                ↓
              Tokens
```

The generated response is also represented as tokens.

---

## Step 4 — Context Window

The LLM considers information such as:

```text
Prompt
+
API Definition
+
Request Model
+
Instructions
+
Conversation History
```

This information occupies the model's context window.

---

# Complete Flow

```text
                YOUR .NET APPLICATION
                         |
                         |
                    User Prompt
                         |
                         ↓
                ┌─────────────────┐
                │   Tokenization  │
                │                 │
                │  Text → Tokens  │
                └────────┬────────┘
                         |
                         ↓
                ┌─────────────────┐
                │ Context Window  │
                │                 │
                │ Prompt          │
                │ API Definition  │
                │ Instructions    │
                │ History         │
                └────────┬────────┘
                         |
                         ↓
                ┌─────────────────┐
                │      LLM        │
                │                 │
                │ GPT / Gemini /  │
                │ Claude / Llama  │
                └────────┬────────┘
                         |
                         ↓
                Generated Response
                         |
                         ↓
                   Test Cases
```

---

# Easy Analogy for .NET Developers

| GenAI Concept | Simple Analogy |
|---|---|
| Generative AI | The system that creates new content |
| LLM | The brain/model generating the content |
| Token | Small pieces of text processed by the model |
| Context Window | Working space available to the model |

Another way to remember:

```text
Generative AI = WHAT the system does

LLM = WHO/WHAT does the generation

Tokens = UNITS the model processes

Context Window = INFORMATION available to the model
```

---

# Interview Questions and Answers

## Q1. What is Generative AI?

### Answer

> Generative AI is a type of AI that can generate new content such as text, code, images, audio, video, SQL queries, and test cases based on user instructions or input.

### Example

```text
Prompt:
Generate test cases for my .NET Core API.

Output:
Positive test cases
Negative test cases
Edge cases
Test data
```

---

# Q2. What is an LLM?

### Answer

> LLM stands for Large Language Model. It is an AI model trained on a huge amount of data that can understand and generate human-like text and code. Examples include GPT, Gemini, Claude, and Llama.

---

# Q3. What are Tokens?

### Answer

> Tokens are the small pieces of text that an LLM processes. A token can represent a complete word, part of a word, punctuation, or other text elements. Tokens are important because they affect processing limits, context window, and API cost.

---

# Q4. What is a Context Window?

### Answer

> Context window is the maximum amount of information, measured in tokens, that an LLM can process and consider at one time. It can include the prompt, conversation history, instructions, documents, and other relevant information.

---

# Quick Revision

Before moving to the next topic, remember these four definitions:

### Generative AI

```text
AI that generates new content.
```

### LLM

```text
A large AI model that understands and generates language/code.
```

### Token

```text
A small piece of text processed by an LLM.
```

### Context Window

```text
The maximum amount of information an LLM can consider at one time.
```

---

# Real-World Example

For our **.NET Core GenAI Test Case Generator**:

```text
                    USER
                     |
                     |
      "Generate test cases for this API"
                     |
                     ↓
              .NET Core API
                     |
                     ↓
              Prompt Creation
                     |
                     ↓
                  Tokens
                     |
                     ↓
              Context Window
                     |
                     ↓
                   LLM
             (Gemini / GPT)
                     |
                     ↓
              Generated Tests
                     |
                     ↓
             JSON / UI Response
                     |
                     ↓
                    USER
```

This architecture will become the foundation for our later topics:

```text
GenAI Fundamentals
        ↓
Prompt Engineering
        ↓
Embeddings
        ↓
Vector Database
        ↓
RAG
        ↓
Function Calling / Tools
        ↓
AI Agents
        ↓
GenAI Application
```

---

# Key Interview Takeaways

Remember these five points:

1. **Generative AI generates new content.**
2. **LLM is the model behind many GenAI applications.**
3. **LLMs process text as tokens.**
4. **Tokens affect cost, limits, and context.**
5. **Context window defines how much information the model can consider at one time.**

---

# One-Line Memory Trick

```text
GenAI → Generate
LLM → Brain
Token → Piece of Text
Context Window → Working Space
```