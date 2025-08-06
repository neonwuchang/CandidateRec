# Candidate Recommendation Engine [🔗](https://huggingface.co/spaces/stargazingjellyfish/CandidateRecEngine)

This project helps users rank candidate resumes based on their semantic similarity to a given job description. Optionally, it uses an LLM to generate a brief summary explaining why the candidate might be a good fit.

Live demo: [Click here](https://huggingface.co/spaces/stargazingjellyfish/CandidateRecEngine)

### 📚 Definitions, Acronyms, Abbreviations

- LLM: Large Language Model
- UI: User Interface
- API: Application Programming Interface

### 📚 General Description

#### ⚙️ Product Functionality / Features

- Accepts job description via text input (max 1000 words)
- Accepts candidate resumes via file upload (initially .txt format)
- Generates semantic embeddings using SentenceTransformers
- Calculates cosine similarity between job description and each resume
- Displays top 5 most relevant candidates with name/ID and similarity score
- Optionally generates AI summaries for top 3 candidates using a Groq-hosted LLM
- Includes prompt injection defense: if a resume or job description attempts to instruct the model, it halts with an alert
- Runs as a Gradio app on Hugging Face Spaces, with public link 

#### ⚠️ Constraints

- Job descriptions are limited to 1000 words (to control LLM input tokens size)
- Resume upload limited to .txt files (expandable to PDF)
- Groq API is subject to free-tier rate limits and latency (suitable for proof-of-concept, but can easily switch to LLM/provider of choice)

#### ⚠️ Assumptions

- Resume file names are formatted with a single extension and contain candidate name/ID, e.g. john_doe_resume.txt

## ⚙️ Behind the Scenes: Design and Engineering Decisions

### 📚 Overall Approach

The goal was to rapidly prototype a minimum viable product that’s lightweight and user-friendly, and then add features incrementally.

### 🛠️ Tool and Library Choices

- Gradio: Chosen for its minimal-code, web-ready interface, ideal for prototyping and demos without the overhead of full-stack development.
- SentenceTransformers (all-MiniLM-L6-v2): Selected for its ability to create sentence embeddings efficiently; SentenceTransformers provides many options of which all-MiniLM provides a good tradeoff between performance and speed.
- Scikit-learn: Common library for cosine similarity computation.
- Groq API (LLaMA-3.3-70b): Enables AI-summarizationvia LLMs without requiring local resources; chosen for low cost and clean code integrations. LLaMA-3.3-70b offers high context length, good performance, and cost efficiency via free tier.

### 📚 Designing for Robustness and Simplicity

- Input Constraints: Job descriptions are capped at 1000 words to prevent token bloat in LLM requests.
- File Handling: Only .txt files are supported, reducing complexity in file parsing and increasing predictability.
- Fallback and Error Handling: API and encoding failures are handled gracefully with simple error alerts to users; detailed error messages are logged but not exposed to users to avoid leaking system details.
- AI-generated Summary: It is limited to top 3 candidates and offered as an optional feature to manage cost and API usage.
- LLM Prompt: The LLM is prompted to act as a recruiter, respond in bullet points (boosts readabiloty and improves user experience), and reject any attempts at prompt manipulation (e.g., prompts trying to manipulate the model to give a higher recommendation).

### 📚 Prioritization and Performance

The app computes similarity for all resumes but returns only the top 5. Additionally, Ai-summary is generated only for the top 3, striking a balance between performance and cost management.

### 👤 User-Centric Design

- Job description textbox with clear placeholder and word limit.
- Upload interface supporting multiple files.
- Summary section visually formatted for readability.
- Readable output: candidates ranked in a table, summaries shown in a scrollable, formatted section
- AI-generated summaries use bullet points for improved scanning and comprehension
