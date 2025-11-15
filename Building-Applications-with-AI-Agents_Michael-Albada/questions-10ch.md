# Active Learning Questions for Building Applications with AI Agents - 10 Key Questions Per Chapter
*From the perspective of a backend/infra engineer with basic ML knowledge*

---

## CHAPTER 1: Introduction to Agents

1. What is an "agentic system," and what are all the infrastructure components I need to support it (model, tools, memory, orchestration)?
2. What is the Model Context Protocol (MCP), and how does it enable agents to communicate with tools differently from traditional APIs?
3. What are the seven practical types of agents, and what infrastructure does each type require (conversational, research, analytics, developer, etc.)?
4. How do I choose between commercial models (OpenAI, Anthropic) and open-weight models (Llama, Mistral) for agent systems?
5. What is the difference between synchronous and asynchronous agent operations, and what infrastructure supports parallel task execution?
6. When should I use simple code versus a deterministic workflow versus RAG versus a full autonomous agent?
7. What does scalability mean for agent systems, and what infrastructure patterns support it (autoscaling, distributed architectures, message queues)?
8. What is LangGraph, and what are its strengths and trade-offs compared to other frameworks (AutoGen, CrewAI, OpenAI SDK)?
9. How do I balance experimentation with standardization when multiple teams are building agents across an organization?
10. What are the typical API integrations needed for enterprise AI agent applications (CRM, ticketing, databases, SIEM systems)?

---

## CHAPTER 2: Designing Agent Systems

1. What are the three core components of an agent system (model, tools, memory), and how do I design the architecture to separate these concerns?
2. What is a StateGraph in LangGraph, and how does it manage agent state across workflows?
3. What are local tools, API-based tools, and MCP-based tools, and when should I use each for my agent?
4. What is short-term versus long-term memory in agents, and what database options are best (PostgreSQL, vector stores)?
5. What is orchestration in agent systems, and how do I design orchestrators that compose multiple tools into complex workflows?
6. What is the speed/accuracy trade-off in agents, and how do I scale with GPU resources (dynamic allocation, priority queuing)?
7. What is fault tolerance in agents, and what patterns should I implement (retries, circuit breakers, timeouts, redundancy)?
8. When should I use single-agent versus multiagent architectures, and what are the challenges (coordination, token consumption)?
9. What metrics should I track for agent systems (tool recall, parameter accuracy, confirmation quality, response time)?
10. How do I implement phased rollouts and real-world testing for agent systems (canary deployments, gradual traffic increases)?

---

## CHAPTER 3: User Experience Design for Agentic Systems

1. What are the four main interaction modalities for agents (text, GUI, voice, video), and what are the infrastructure requirements for each?
2. What is the problem of discoverability in text interfaces, and how do I mitigate it (capability reminders, dynamic suggestions)?
3. What is generative UI, and what backend capabilities are needed to dynamically create interface elements?
4. What is the OpenAI Realtime Voice API, and how do I implement streaming audio with graceful handling of interruptions?
5. How do I design agent systems that transition seamlessly between modalities (voice → text → GUI) with state preservation?
6. What is the autonomy slider, and how do I implement the three levels (Manual, Ask, Agent) in my agent APIs?
7. What is the difference between synchronous and asynchronous agent experiences, and when should I design for each?
8. What is context retention, and how do I implement hybrid context management (client-side for short-term, server-side for long-term)?
9. How do I implement confidence scores for agent outputs, and what UX patterns communicate uncertainty effectively?
10. What does it mean for an agent to "fail gracefully," and what backend patterns support graceful degradation?

---

## Notes
- These are the **10 most critical questions per chapter**
- Focus on high-level infrastructure, system design, and practical implementation
- Use these questions to guide your initial reading, then dive deeper with questions-100.md
- Look for specific answers, examples, and implementation details while reading

