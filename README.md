# 📚 AI-Powered Internal Policy Assistant (RAG-lite)

## 📌 Overview
A no-code automation workflow that answers employee questions using **only** a company's internal policy document — powered by **Claude (Anthropic)**. The assistant reads a list of questions from Google Sheets, answers each one strictly from the provided policy document, and explicitly refuses to answer anything not covered in it.

## 🎯 Problem Solved
New employees often ask repetitive questions about company policies (shipping, returns, working hours, etc.), taking up managers' and support teams' time. This project solves that by:
- Providing instant, accurate answers grounded strictly in official company documentation
- Preventing AI hallucination — the assistant will not guess or invent policy details
- Scaling to handle any volume of questions with zero manual lookup
- Reducing risk of employees giving customers incorrect policy information

## 🛠️ Tools Used
- **n8n** – Workflow automation platform
- **Claude (Anthropic API)** – Generative AI model (claude-sonnet-5)
- **Google Sheets** – Data source (employee questions)

## ⚙️ Workflow
```
[Manual Trigger] 
      ↓
[Read employee questions from Google Sheets] 
      ↓
[Send each question + the full policy document to Claude via a grounded prompt]
      ↓
[Receive an answer strictly based on the policy document, or a clear "not covered" response]
```

## 🧠 Prompt Design — RAG-lite Approach
This project introduces **Retrieval-Augmented Generation (RAG)** concepts in a simplified form suitable for short documents. Instead of using a vector database to retrieve relevant chunks from a large knowledge base (traditional RAG), the entire policy document is injected directly into the prompt as static context — appropriate for a single-page policy guide.

Key prompt elements:
- **A defined role** (internal support assistant restricted to one company's policies)
- **The word "ONLY"** used explicitly to instruct the model to ignore its general knowledge and rely solely on the provided document
- **A dynamic variable** for the employee's question (`{{ $json['employee_question'] }}`)
- **An explicit fallback instruction**: if the answer isn't in the document, the model must respond with a fixed, predictable message instead of guessing

## 📊 Sample Output & Validation
Tested with 5 employee questions, including 4 questions directly covered by the policy document and 1 question deliberately **not covered** (competitor price matching). Results:
- All 4 covered questions were answered accurately and concisely, matching the source document exactly
- The uncovered question correctly triggered the fallback response: *"This isn't covered in our policy document — please check with your manager."*

This confirms the system reliably distinguishes between "known" and "unknown" information — the core requirement of any trustworthy AI knowledge assistant.

## 💡 Key Takeaways
- RAG doesn't always require a vector database — for small, static documents, direct context injection is a valid and much simpler approach
- Explicit "ground truth only" instructions (like the word "ONLY") are critical to prevent hallucination in knowledge-based assistants
- A well-designed fallback response is as important as the correct answers — it defines how the system fails safely
- This pattern (document + constrained prompt + fallback) is the foundation for more advanced RAG systems using vector search for larger knowledge bases

## 🚀 Future Improvements
- Scale to a full vector-database RAG setup for larger, multi-document knowledge bases
- Add a Slack or Teams trigger so employees can ask questions directly from chat
- Log unanswered questions to help identify gaps in the policy documentation
