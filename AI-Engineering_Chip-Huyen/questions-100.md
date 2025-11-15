# Active Learning Questions for AI Engineering - Chapters 1, 2 & 3
*From the perspective of a backend/infra engineer with basic ML knowledge*

---

## CHAPTER 1: Introduction to Building AI Applications with Foundation Models

### 1.1 The Rise of AI Engineering / From Language Models to Large Language Models
1. What is a language model, and how does it encode statistical information about languages?
2. What is a token, and how does tokenization affect storage and processing requirements?
3. What is the difference between masked language models (like BERT) and autoregressive language models?
4. What is self-supervision, and how does it differ from supervised learning in terms of data requirements?
5. How does self-supervision enable scaling up of language models, and what infrastructure is needed?
6. What are the cost implications of using self-supervised training versus traditional supervised training?
7. How do vocabulary size and tokenization methods affect model efficiency and infrastructure needs?
8. What is the relationship between model size (number of parameters) and compute/storage requirements?
9. How do end-of-sequence (EOS) and beginning-of-sequence (BOS) tokens work in language model training?
10. What infrastructure is needed to store and process training data for language models at scale?

### 1.2 From Large Language Models to Foundation Models
1. What is the difference between a language model and a foundation model?
2. What is a multimodal model, and what additional infrastructure is needed compared to text-only models?
3. How does natural language supervision differ from traditional supervised learning for training multimodal models?
4. What is CLIP, and how does it create joint embeddings for text and images?
5. What storage and compute requirements are needed for training multimodal models on image-text pairs?
6. How do foundation models transition from task-specific to general-purpose capabilities?
7. What are the infrastructure implications of supporting multiple modalities (text, image, audio, video)?
8. How do embedding models differ from generative models in terms of compute and storage?
9. What is the Super-NaturalInstructions benchmark, and what does it tell us about foundation model capabilities?
10. What database and storage systems are needed to manage multimodal training data at scale?

### 1.3 From Foundation Models to AI Engineering
1. What is AI engineering, and how does it differ from traditional ML engineering?
2. What is the "model as a service" approach, and what are its infrastructure implications?
3. What are the three key factors that created ideal conditions for AI engineering growth?
4. How do foundation model APIs work, and what are typical rate limits and pricing structures?
5. What infrastructure do I need to integrate foundation model APIs into existing backend systems?
6. What is the difference between prompt engineering, RAG, and finetuning from an implementation perspective?
7. How do I handle versioning and monitoring when using third-party model APIs?
8. What are the latency and reliability considerations when depending on external model providers?
9. How do I design systems to be model-agnostic so I can switch providers if needed?
10. What are the typical costs per API call for major foundation model providers, and how do I forecast expenses?

### 1.4 Foundation Model Use Cases
1. What are the eight common use case categories for foundation models across consumer and enterprise applications?
2. How do coding use cases (like GitHub Copilot) differ in infrastructure needs from other use cases?
3. What are the infrastructure requirements for image and video production applications?
4. What backend systems are needed to support conversational bots and customer support agents?
5. How do information aggregation use cases (summarization, talk-to-your-docs) work architecturally?
6. What database and search infrastructure is needed for data organization use cases?
7. How do workflow automation use cases (agents) require different infrastructure than simple chatbots?
8. What are the typical API integrations needed for enterprise AI applications (CRM, ticketing, databases)?
9. How do internal-facing applications differ from external-facing applications in terms of deployment and risk?
10. What monitoring and observability are needed for AI applications in production?

### 1.5 Planning AI Applications
1. What are the key questions to ask before building an AI application (risks, opportunities, defensibility)?
2. How do I evaluate whether AI is critical or complementary to my application?
3. What is the difference between reactive and proactive AI features, and how does this affect infrastructure design?
4. What is human-in-the-loop, and how do I implement it in my system architecture?
5. What is the Crawl-Walk-Run framework for gradually increasing AI automation?
6. How do I set measurable goals and usefulness thresholds for AI applications?
7. What quality metrics, latency metrics, and cost metrics should I track for AI applications?
8. What is the "last mile challenge" in AI product development, and how do I plan for it?
9. How do I design evaluation pipelines to measure progress toward my goals?
10. What is the buy-or-build decision for AI applications, and what factors should I consider?

### 1.6 The AI Engineering Stack - Three Layers
1. What are the three layers of the AI application stack (application, model, infrastructure)?
2. What responsibilities fall under the application development layer?
3. What responsibilities fall under the model development layer?
4. What responsibilities fall under the infrastructure layer?
5. How has the distribution of open source repositories changed across these layers since 2023?
6. What tooling is needed for each layer of the stack?
7. How do I design systems that separate concerns between the three layers?
8. What APIs and interfaces should exist between the layers for modularity?
9. How do I monitor and optimize performance at each layer independently?
10. What are the typical team structures for managing the different layers?

### 1.7 AI Engineering Versus ML Engineering - Model Development
1. How does model development differ between traditional ML and AI engineering?
2. What is the difference between pre-training, finetuning, and post-training from a resource perspective?
3. What infrastructure is needed for dataset engineering (data curation, annotation, processing)?
4. How do data annotation requirements differ between close-ended and open-ended tasks?
5. What is inference optimization, and why is it even more critical for foundation models?
6. What are typical inference latency targets for AI applications, and how do I achieve them?
7. How do I handle the sequential token generation of autoregressive models to reduce latency?
8. What techniques exist for inference optimization (quantization, distillation, parallelism)?
9. How do I balance model quality against inference cost and latency?
10. What monitoring should I implement to track inference performance in production?

### 1.8 AI Engineering Versus ML Engineering - Application Development
1. Why is evaluation more important and challenging with foundation models than traditional ML?
2. What is prompt engineering, and what infrastructure is needed to experiment with prompts at scale?
3. What is context construction, and how do I implement efficient context management?
4. How do I version and track prompts across different model versions and deployments?
5. What A/B testing infrastructure is needed to compare different prompts or models?
6. What is the impact of prompt engineering on model performance (as seen in Gemini vs ChatGPT example)?
7. What AI interface options exist (web apps, browser extensions, chatbots, plug-ins, voice)?
8. How do I design APIs that allow AI to be integrated as plug-ins into existing products?
9. What feedback mechanisms should I implement to collect user feedback on AI outputs?
10. How do I extract and analyze natural language feedback from users efficiently?

### 1.9 AI Engineering Versus Full-Stack Engineering
1. How is AI engineering becoming closer to full-stack development?
2. Why is JavaScript support (LangChain.js, Transformers.js) becoming more important in AI tooling?
3. What is the advantage of starting with product development before investing in data and models?
4. How does the iterative development workflow differ between traditional ML and AI engineering?
5. What frontend frameworks and tools are commonly used for AI application interfaces?
6. How do I design rapid prototyping workflows to get from idea to demo quickly?
7. What is the role of AI engineers in product decisions compared to traditional ML engineers?
8. How do I implement feedback loops that allow quick iteration based on user input?
9. What deployment strategies (staging, canary, blue-green) work best for AI applications?
10. How do I handle the fast pace of change in AI when planning infrastructure investments?

### 1.10 Maintenance and Evolution
1. What are the key maintenance challenges for AI applications (model updates, API changes, regulations)?
2. How do I design systems to handle model provider API changes without major refactoring?
3. What versioning strategies should I use for models, prompts, and evaluation datasets?
4. How do I monitor and respond to changes in model performance or pricing from providers?
5. What is the cost-benefit analysis process for swapping between different models or providers?
6. How do I handle regulatory changes (GDPR, compute restrictions, IP concerns) in my architecture?
7. What disaster recovery and fallback mechanisms should I have if a model provider goes down?
8. How do I track and control costs as model usage scales in production?
9. What infrastructure is needed to support continuous evaluation and monitoring?
10. How do I balance stability and reliability with keeping up with rapid AI advancements?

---

## CHAPTER 2: Understanding Foundation Models

### 2.1 Training Data
1. What types of data sources are used to train foundation models, and how does data quality impact model performance?
2. How much training data is typically needed for foundation models, and what are the storage/infrastructure implications?
3. What are the common data preprocessing steps required before training, and how do they affect infrastructure requirements?
4. What are the legal and licensing considerations when collecting training data that I need to be aware of as an infrastructure engineer?
5. How is training data stored and accessed during the training process - what database or file systems are typically used?
6. What is data contamination, and how does it affect model evaluation from a systems perspective?
7. What are the bandwidth and I/O requirements for feeding training data to models during training?
8. How do different tokenization methods affect storage and processing requirements?
9. What is the typical ratio of compute to data throughput needed for efficient training?
10. How do companies handle data versioning and lineage for foundation model training data?

### 2.2 Modeling
1. What are the key architectural components of transformer models that I need to understand for infrastructure planning?
2. How do model size parameters (e.g., 7B, 13B, 70B) translate to actual memory and compute requirements?
3. What is the difference between dense and sparse models from an infrastructure perspective?
4. How do different model architectures (decoder-only, encoder-decoder) affect deployment requirements?
5. What are the GPU/TPU requirements for training models of different sizes?
6. How does distributed training work across multiple machines, and what are the networking requirements?
7. What is gradient checkpointing, and how does it help with memory constraints?
8. What are the key differences between training from scratch versus continued pre-training from an infrastructure standpoint?
9. How do model parallelism and data parallelism strategies affect cluster design?
10. What are the typical training times and costs for models of different scales?

### 2.3 Post-Training
1. What is the difference between pre-training and post-training (finetuning), and what are the resource implications of each?
2. What is supervised finetuning (SFT), and how do the compute requirements compare to pre-training?
3. What is RLHF (Reinforcement Learning from Human Feedback), and what additional infrastructure does it require?
4. How is human feedback collected and stored for RLHF workflows?
5. What are reward models, and what are their inference requirements?
6. What is the typical data volume needed for finetuning versus pre-training?
7. How does post-training affect model performance metrics like latency and throughput?
8. What is alignment, and why is it important from a production deployment perspective?
9. How do you version and manage multiple finetuned variants of the same base model?
10. What are the storage implications of maintaining multiple checkpoints during post-training?

### 2.4 Sampling
1. What is sampling in the context of language models, and when does it happen in the inference pipeline?
2. What are temperature, top-p, and top-k parameters, and how do they affect response generation?
3. How do different sampling parameters affect inference latency and computational cost?
4. What is the difference between greedy decoding and sampling, and which is more resource-intensive?
5. How can I expose sampling parameters to end users through APIs without causing system instability?
6. What is beam search, and what are its memory and compute implications?
7. How do sampling strategies affect the reproducibility of model outputs?
8. What sampling parameter defaults should I set for production systems, and why?
9. How do I cache or optimize repeated sampling operations for similar prompts?
10. What monitoring metrics should I track related to sampling behavior in production?

### 2.5 The Probabilistic Nature of AI
1. Why are foundation model outputs non-deterministic, and what are the implications for system design?
2. How do I handle the probabilistic nature of AI in systems that require consistent outputs?
3. What are logprobs (log probabilities), and how can they be used for system monitoring?
4. How can I make model outputs more deterministic when needed (e.g., for testing or compliance)?
5. What is the relationship between randomness and creativity in model outputs?
6. How should I design retry logic and error handling given the probabilistic nature of responses?
7. What are the implications of probabilistic outputs for caching strategies?
8. How do I explain to stakeholders that the same input might produce different outputs?
9. What testing strategies work best for systems with probabilistic components?
10. How does the probabilistic nature affect idempotency in API design?

---

## CHAPTER 3: Evaluation Methodology

### 3.1 Challenges of Evaluating Foundation Models
1. Why is evaluating foundation models harder than evaluating traditional ML models from an infrastructure perspective?
2. What are the key challenges in creating automated evaluation pipelines for open-ended model outputs?
3. How do I design an evaluation system that can scale with increasing model capabilities?
4. What is benchmark saturation, and why does it matter for long-term evaluation strategies?
5. How much compute and time should I budget for continuous model evaluation in production?
6. What types of evaluation failures should I design my monitoring systems to detect?
7. How do I balance the cost of thorough evaluation against the speed of iteration?
8. What is the difference between offline evaluation and online evaluation, and when should I use each?
9. How do I design evaluation workflows that can handle multimodal outputs (text, images, code)?
10. What data storage and retrieval systems are needed to maintain evaluation datasets and results over time?

### 3.2 Understanding Language Modeling Metrics
1. What is perplexity, and why is it useful as a high-level metric for model quality?
2. How is perplexity calculated, and what infrastructure is needed to compute it at scale?
3. What is cross-entropy, and how does it relate to perplexity from a practical standpoint?
4. What are typical perplexity values for good models, and how do I interpret them?
5. Do I need access to model logprobs to calculate perplexity, and do all APIs expose this?
6. How can perplexity be used to detect data contamination or memorization in models?
7. What is bits-per-byte (BPB), and when is it more useful than perplexity?
8. How does perplexity change after post-training (SFT/RLHF), and what does this mean for evaluation?
9. Can I use perplexity as a real-time monitoring metric in production, or is it too expensive to compute?
10. How do different tokenization schemes affect perplexity comparisons across models?

### 3.3 Exact Evaluation (Functional Correctness & Similarity Measurements)
1. What is functional correctness evaluation, and for what types of tasks can it be automated?
2. How do I build automated test harnesses for evaluating code generation outputs (like for HumanEval)?
3. What is the pass@k metric, and how should I implement it in my evaluation pipeline?
4. What are reference-based metrics, and what data storage is needed to maintain reference datasets?
5. What is the difference between lexical similarity (BLEU, ROUGE) and semantic similarity, and when should I use each?
6. How do I compute embeddings for semantic similarity at scale - what models and infrastructure do I need?
7. What is cosine similarity, and how computationally expensive is it to calculate for large batches?
8. How do exact match evaluations handle edge cases like formatting differences or whitespace?
9. What are the storage and compute requirements for maintaining embedding databases for similarity search?
10. How do I version and update reference datasets without invalidating historical evaluation results?

### 3.4 AI as a Judge
1. What does "AI as a judge" mean, and what are the infrastructure requirements for implementing it?
2. What models can serve as judges, and do I need different models than the ones being evaluated?
3. How much does it cost to use AI judges (like GPT-4) for evaluation at scale?
4. What latency do AI judge calls add to my evaluation pipeline?
5. How do I design prompts for AI judges to get consistent and reliable evaluations?
6. What is self-evaluation, and can it reduce costs compared to using separate judge models?
7. What are common biases in AI judges (self-bias, position bias, verbosity bias), and how do I mitigate them?
8. How do I cache or batch AI judge evaluations to reduce API costs?
9. What monitoring should I implement to detect when AI judge behavior changes over time?
10. Should I use AI judges in the critical path of serving user requests, or only for offline evaluation?

### 3.5 Ranking Models with Comparative Evaluation
1. What is comparative evaluation, and how does it differ from pointwise evaluation?
2. How do I implement a pairwise comparison system where users can vote on model outputs?
3. What is the Elo rating system, and how is it used to rank models based on comparisons?
4. What is the Bradley-Terry algorithm, and why might it be preferred over Elo for AI evaluation?
5. How many pairwise comparisons are needed to establish a reliable ranking of N models?
6. What infrastructure is needed to collect, store, and analyze comparative evaluation data at scale?
7. How do I handle ties in comparative evaluation systems?
8. What is LMSYS Chatbot Arena, and what can I learn from their infrastructure for comparative evaluation?
9. How do I design an A/B testing system for comparative evaluation in production?
10. What database schema should I use to efficiently store and query pairwise comparison results?

### 3.6 Challenges of Comparative Evaluation
1. What are the scalability bottlenecks of comparative evaluation as the number of models increases?
2. How do I handle the challenge of evaluating new models against existing ones in a comparative framework?
3. What is the transitivity assumption in ranking algorithms, and does it hold for AI models?
4. How do I maintain quality control when crowdsourcing comparative evaluations?
5. What strategies can I use to detect and filter out low-quality or malicious evaluation data?
6. How do I balance between standardized test prompts and diverse real-world usage patterns?
7. What are the infrastructure requirements for running multiple models simultaneously for comparison?
8. How do I convert comparative rankings (win rates) into absolute performance metrics?
9. What is the cost-benefit trade-off of comparative evaluation versus traditional benchmarks?
10. How do I design an evaluation system that combines both comparative and absolute evaluation methods?

### 3.7 The Future of Comparative Evaluation
1. What are specialized judges (reward models, preference models), and how do they differ from general AI judges?
2. What is a preference model, and what infrastructure is needed to train and serve one?
3. How can small, specialized judge models be more efficient than large general-purpose models?
4. What are the benefits of comparative evaluation that make it worth the additional complexity?
5. How might comparative evaluation evolve as models surpass human performance?
6. What role will comparative evaluation play in production monitoring versus offline evaluation?
7. How can I integrate user feedback into comparative evaluation without disrupting their workflow?
8. What are the privacy and data security implications of using user data for comparative evaluation?
9. How do I design systems that can handle both preference-based and correctness-based evaluation?
10. What investments in evaluation infrastructure should I prioritize for long-term success?

---

## Notes
- These questions are designed to guide active reading
- Look for specific answers, examples, and implementation details while reading
- Note which questions are fully answered, partially answered, or not covered
- Add your own follow-up questions as you discover new concepts

