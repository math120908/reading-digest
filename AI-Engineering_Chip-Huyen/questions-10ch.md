# Active Learning Questions for AI Engineering - 10 Key Questions Per Chapter
*From the perspective of a backend/infra engineer with basic ML knowledge*

---

## CHAPTER 1: Introduction to Building AI Applications with Foundation Models

1. What is a token, and how does tokenization affect storage, processing requirements, and model efficiency?
2. What is self-supervision, and how does it enable scaling up of language models with lower data labeling costs?
3. What is the "model as a service" approach, and what are its infrastructure implications (APIs, versioning, monitoring)?
4. What are the three key factors that created ideal conditions for AI engineering growth (capabilities, investments, low barrier)?
5. What are the three layers of the AI application stack (application, model, infrastructure), and what are the responsibilities of each?
6. What is the difference between prompt engineering, RAG, and finetuning from an implementation and resource perspective?
7. What is inference optimization, and why is it even more critical for foundation models than traditional ML?
8. Why is evaluation more important and challenging with foundation models than traditional ML from an infrastructure perspective?
9. What are typical inference latency targets for AI applications, and what techniques exist to achieve them (quantization, distillation, parallelism)?
10. What are the key maintenance challenges for AI applications (model updates, API changes, regulations), and how do I design systems to handle them?

---

## CHAPTER 2: Understanding Foundation Models

1. What types of data sources are used to train foundation models, and what are the storage/infrastructure implications at scale?
2. How do model size parameters (e.g., 7B, 13B, 70B) translate to actual memory and compute requirements (GPU/TPU needs)?
3. How does distributed training work across multiple machines, and what are the networking and synchronization requirements?
4. What is the difference between pre-training and post-training (finetuning), and what are the resource implications of each?
5. What is RLHF (Reinforcement Learning from Human Feedback), and what additional infrastructure does it require?
6. What are temperature, top-p, and top-k sampling parameters, and how do they affect response generation and resource usage?
7. Why are foundation model outputs non-deterministic, and what are the implications for system design (caching, testing, error handling)?
8. How do I handle the probabilistic nature of AI in systems that require consistent outputs or compliance?
9. What data preprocessing steps are required before training (tokenization, deduplication, quality control), and how do they affect infrastructure?
10. What is the typical ratio of compute to data throughput needed for efficient training, and how do I monitor it?

---

## CHAPTER 3: Evaluation Methodology

1. Why is evaluating foundation models harder than traditional ML models from an infrastructure perspective (open-ended outputs, capabilities)?
2. What is perplexity, how is it calculated, and what infrastructure is needed to compute it at scale?
3. What is functional correctness evaluation, and how do I build automated test harnesses (like HumanEval for code generation)?
4. What is the difference between lexical similarity (BLEU, ROUGE) and semantic similarity, and when should I use each?
5. What does "AI as a judge" mean, what are the infrastructure requirements, and how much does it cost at scale?
6. What are common biases in AI judges (self-bias, position bias, verbosity bias), and how do I mitigate them in my evaluation pipeline?
7. What is comparative evaluation, how does it differ from pointwise evaluation, and what infrastructure is needed (Elo, Bradley-Terry)?
8. How many pairwise comparisons are needed to establish a reliable ranking of N models, and what database schema should I use?
9. How do I design an evaluation system that can scale with increasing model capabilities and handle multimodal outputs?
10. What investments in evaluation infrastructure should I prioritize for long-term success (automated pipelines, reference datasets, monitoring)?

---

## Notes
- These are the **10 most critical questions per chapter**
- Focus on high-level infrastructure, system design, and practical implementation
- Use these questions to guide your initial reading, then dive deeper with questions-100.md
- Look for specific answers, examples, and implementation details while reading

