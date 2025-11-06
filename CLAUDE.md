# Claude AI Instructions for Reading Digest Project

## Project Purpose
This repository tracks technical reading progress for two O'Reilly books, using an **active learning approach** where questions are generated before reading chapters to guide focused learning.

## Directory Structure
```
reading-digest/
├── AI-Engineering_Chip-Huyen/
│   ├── INFO.md                    # Book metadata and table of contents
│   ├── resources/ch*.txt          # Chapter content
│   ├── questions.log              # Pre-reading questions
│   ├── reading.log                # Reading progress and notes
│   └── .cursorrules              # AI instructions for this book
│
└── Building-Applications-with-AI-Agents_Michael-Albada/
    ├── INFO.md                    # Book metadata and table of contents
    ├── resources/ch*.txt          # Chapter content
    ├── questions.log              # Pre-reading questions
    ├── reading.log                # Reading progress and notes
    └── .cursorrules              # AI instructions for this book
```

## Workflows

### 1. Generate Pre-Reading Questions
**Purpose**: Create focused questions before reading to enable active learning

**Command**:
```
Generate questions for chapter [X] of [book name]
```

**Process**:
1. Read `INFO.md` to understand chapter structure and subsections
2. Read `resources/ch[X].txt` to understand chapter content
3. Generate 10 questions per subsection focusing on:
   - Infrastructure and system design
   - Practical implementation details
   - Backend engineering considerations
   - Scalability and performance
   - Cost and operational concerns
4. Append to or update `questions.log`

**Target Audience**: Backend/infra engineer with basic ML background

### 2. Track Reading Progress
**Purpose**: Log insights, answers, and progress while reading

**Command**:
```
Add reading notes for chapter [X], subsection [Y]
```

**Process**:
1. Update `reading.log` with:
   - Date and chapter/section read
   - Key insights and answers found
   - Additional questions that arose
   - Implementation ideas or patterns learned

### 3. Review and Summarize
**Purpose**: Consolidate learning and identify gaps

**Command**:
```
Summarize key takeaways from chapter [X]
```

**Process**:
1. Review `questions.log` and `reading.log`
2. Identify which questions were answered
3. Highlight remaining gaps or areas for deeper research
4. Generate summary of actionable insights

## Book-Specific Focus

### AI Engineering (Chip Huyen)
- Foundation models and training infrastructure
- Evaluation methodologies and metrics
- Prompt engineering and optimization
- RAG and agent architectures
- Fine-tuning and dataset engineering
- Inference optimization
- Production deployment and monitoring

### Building Applications with AI Agents (Michael Albada)
- Agent system architecture
- Tool design and integration
- Memory systems and state management
- Orchestration patterns
- Multi-agent coordination
- User experience design
- Production best practices

## Key Principles

1. **Active Learning**: Always generate questions before reading to guide focus
2. **Practical Focus**: Prioritize infrastructure, system design, and implementation details
3. **Backend Perspective**: Frame questions from a backend/infra engineer's viewpoint
4. **Implementation-Oriented**: Ask "how" and "what infrastructure is needed" rather than "why"
5. **Cost-Aware**: Consider operational costs, scalability, and real-world constraints

## Example Questions (Good vs Bad)

### ✅ Good Questions
- "What GPU requirements are needed for training a 70B parameter model?"
- "How do I implement a context window that preserves state across sessions?"
- "What database schemas support efficient vector similarity search?"
- "How do I estimate API costs for a production agent handling 10K requests/day?"

### ❌ Avoid These
- "Why is AI important?" (too vague)
- "What is a neural network?" (too basic, outside scope)
- "Explain transformers in detail" (too theoretical, not practical)
- Questions without implementation or infrastructure focus

## Usage Tips

1. **Before Reading**: Generate questions to know what to look for
2. **During Reading**: Take notes in `reading.log` with answers and insights
3. **After Reading**: Review questions to ensure comprehension
4. **Iterate**: Add follow-up questions as needed based on new understanding

---

Each book directory has its own `.cursorrules` file with specific instructions for that book's content and focus areas.
