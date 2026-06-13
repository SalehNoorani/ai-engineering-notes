# Routing Pattern

> **Key Takeaway:** Routing is an agentic pattern where an initial LLM call (or a programmatic classifier) inspects an incoming request and dynamically directs it to the most appropriate downstream path, prompt, or tool. This enables systems to handle diverse inputs efficiently.

---

## What Is It?

Routing acts as the traffic controller or switchboard of an AI system. Instead of using a single, massive prompt to handle every possible user scenario, a lightweight router model analyzes the intent of the input. Based on this analysis, the request is routed to a specialized LLM instance, a dedicated prompt chain, or a specific database query.

---

## Why Is It Useful?

* **Cost and Efficiency Optimization:** You can use a small, fast, and cheap model (e.g., GPT-4o-mini or Claude Haiku) as the router, and reserve larger, expensive models only for complex tasks.
* **Separation of Concerns:** Developers can write highly optimized, narrow prompts for specific tasks without worrying about cross-contamination from other use cases.
* **Scalability:** New features or specialized paths can be added to the system simply by updating the routing options, without breaking existing logic.

---

## Basic Workflow

1. **Input Classification:** The user submits a request to the system.
2. **Intent Analysis:** The router (LLM or classifier code) determines the category or intent of the request.
3. **Decision & Dispatch:** The system matches the detected intent against predefined paths.
4. **Specialized Execution:** The request is sent to the designated downstream handler (e.g., Path A, Path B, or Tool C).
5. **Response Delivery:** The specialized handler processes the request and returns the final output.

---

## Example Use Cases

* **Comprehensive Customer Service:**
  * Router detects intent: "Billing Query", "Technical Support", or "Refund Request".
  * Billing queries go to an LLM with access to payment APIs.
  * Technical support goes to a prompt chain optimized for troubleshooting steps.
* **Multi-Language Gateways:**
  * Router detects the input language.
  * Routes the request to a persona or model fine-tuned for that specific language and cultural context.
* **Code/Text Separation:**
  * Router detects if the user is asking for code generation or creative writing.
  * Routes code queries to a model like DeepSeek-Coder/Codellama, and writing queries to a creative writing pipeline.

---

## Advantages

* **High Accuracy:** Specialized prompts and models always perform better than general-purpose, overloaded prompts.
* **Lower Latency:** Decreases token consumption per run since downstream models don't need to carry massive, multi-purpose instructions.

---

## Limitations

* **Router Dependency:** The accuracy of the entire system relies heavily on the router. If the router misclassifies an input, the downstream system will fail completely.
* **Overhead:** Adds an extra network jump/LLM call at the very beginning of the interaction, slightly increasing initial latency.

---

## Example Prompt Structure (Conceptual)

### Router Prompt
```text
You are an intent classification router. Analyze the following user query and classify it into exactly one of these categories:
- [BUG_REPORT] if they are reporting a software glitch.
- [FEATURE_REQUEST] if they are suggesting a new capability.
- [GENERAL_INQUIRY] for questions about pricing or documentation.

Return ONLY the category name inside brackets.

User Query: {{user_input}}
```

### Downstream Logic (Pseudocode)

```python
if routing_output == "[BUG_REPORT]":
    execute_chain(bug_triage_prompt, model="gpt-4o")
elif routing_output == "[FEATURE_REQUEST]":
    execute_chain(product_backlog_prompt, model="gpt-4o-mini")
else:
    execute_chain(general_faq_prompt, model="gpt-4o-mini")
```

---

## Related Concepts

* Prompt Chaining: Routing is frequently used as the first step in a chain to determine which chain should be executed.

* Tool Calling (Function Calling): A specific type of routing where the LLM selects a programmatic function/tool to execute based on user arguments.