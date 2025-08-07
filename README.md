# Candidate Recommendation Engine 🔗

This project helps users rank candidate resumes based on their semantic similarity to a given job description. Optionally, it uses an LLM to generate a brief summary explaining why the candidate might be a good fit and what are the gaps.

Live demo: [Click here](https://27a65edcc8092fbc63.gradio.live/)

### 📚 Definitions, Acronyms, Abbreviations

- LLM: Large Language Model
- UI: User Interface
- API: Application Programming Interface

### 📚 General Description

#### ⚙️ Product Functionality / Features

- Accepts job description via text input (max 1000 words)
- Accepts candidate resumes via file upload (initially .txt format, now updated to .pdf)
- Generates semantic embeddings using SentenceTransformers
- Calculates cosine similarity between job description and each resume
- Displays top 5 most relevant candidates with name/ID and similarity score
- Optionally generates AI summaries for top 3 candidates using a Groq-hosted LLM
- Includes prompt injection defense: if a resume or job description attempts to instruct the model, it halts with an alert
- Runs as a Gradio app on Hugging Face Spaces, with public link 

#### ⚠️ Constraints

- Job descriptions are limited to 1000 words (to control LLM input tokens size)
- Resume upload limited to .pdf
- Groq API is subject to free-tier rate limits and latency (suitable for proof-of-concept, but can easily switch to LLM/provider of choice)

#### ⚠️ Assumptions

<!--- Old Constraint: Resume file names are formatted with a single extension and contain candidate name/ID, e.g. john_doe_resume.txt -->
- Candidate name is conatined within the first line of the resume

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
- File Handling: Only .pdf files (a common resume file type) are supported, reducing complexity in file parsing and increasing predictability.
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

## 📊 Output Samples

### 🎯 Use Case 1:

Input: 
- 6 resumes submitted
- AI summary option enabled

Output:
- Top 5 resumes get outputted, in descending order of similarity score (as expected, keyword stuffer is ranked highest)
- AI summary for top 3 is generated, providing a balanced view of candidates' skills and skill gaps
- * LLM is able to detect the keyword stuffing and mentions the high cosine similarity may not be sufficient, suggesting a deepr look into the candidate's experiences. 

<img width="3355" height="2065" alt="Case 1 Output: Generic Use case" src="https://github.com/user-attachments/assets/719d9f2d-a5db-4ef3-90b6-04a042ef7cec" />

### 🎯 Use Case 2:

Input:
- 2 resumes submitted
- AI-summary option not enabled

Output:
- 2/2 resumes ranked
- No AI summary
- * Contrast between Cases 1 and 2 shows the advantage of an AI summary

  <img width="3730" height="2040" alt="Case 2 Output: No AI Summary" src="https://github.com/user-attachments/assets/0d9c42d9-fd6b-4f03-9327-c08b08deebe0" />

### 🎯 Use Case 3:

Input: 
- Resumes with instructions for LLMs submitted

Output:
- No summarization is done, and an alert message is returned by the model

<img width="1544" height="1029" alt="Case 3 Output: Malicious Resume" src="https://github.com/user-attachments/assets/e2be4a4c-03d2-4828-bd51-b08f8d2dcbb5" />

### 🎯 Use Case 4 (Updated App):

Input: 
- Resumes in pdf format

Output:
- Correct candidate names and similarity scores

<img width="3777" height="2035" alt="image" src="https://github.com/user-attachments/assets/03e72dd0-620e-4714-b73d-b361344523c3" />


