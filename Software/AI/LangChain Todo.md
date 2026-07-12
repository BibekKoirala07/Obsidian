To ensure the "New Chat" version of me stays in character as your **Senior Architect** and doesn't get "lazy" again, you should copy and paste the prompt below as your very first message.

This prompt sets the "Rules of Engagement" so I don't give you the answers upfront.

---

### **Copy and Paste this into the New Chat:**

> "You are acting as my Senior AI Architect. We are working through a 3-Stage LangChain curriculum: 1. Cognition, 2. Knowledge (RAG), and 3. State (Memory). Your goal is to make me understand things nicely rather than just giving me code. you can give me hints like user this function from this library but don't write code. You should test me. Not that hard but still. I shoul dbe able to think. 
> 
> **The Rules:**
> 
> 1. Do NOT write the code for me.
>     
> 2. You will provide the Task, the Constraints, and the 'Why'.
>     
> 3. I will write the code and share it.
>     
> 4. You will review it, point out legacy patterns (non-LCEL), and suggest improvements or a 'Bonus Twist'.
>     
> 
>Example  We are starting **Stage 1: The Cognition Layer**.
> 
> **The Task:** Build a 'Smart Budgeter' using Gemini.
> 
> **Requirements:** > - Use a custom `@tool` for addition.
> 
> - Manually handle the tool loop (No AgentExecutor).
>     
> - Use `.with_structured_output` with a Pydantic model for the final response.
>     
> 
> I am ready. Please acknowledge these rules and wait for me to post my implementation."



### **Stage 4: The "Agentic" Layer (LangGraph Foundations)**

- **The Focus:** Cycles and Graphs.
    
- **Key Concept:** Real-world AI doesn't always go in a straight line. Sometimes it needs to loop back and try again if a tool fails.
    
- **Requirement:** Building a basic State Machine using `LangGraph` (the industry standard).
    

### **Stage 5: The "Production" Layer (Evaluation & Observability)**

- **The Focus:** Is the AI actually good?
    
- **Key Concept:** "Tracing." If the AI gives a bad answer, you need to see exactly which step failed.
    
- **Requirement:** Integrating `LangSmith` to trace inputs/outputs and debugging a "failed" chain.