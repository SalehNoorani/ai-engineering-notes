
# Evaluator-Optimizer Pattern

> **Key Takeaway:** Evaluator-Optimizer is an iterative agentic pattern where one LLM instance (the Optimizer) generates a response, and a separate, highly specialized LLM instance (the Evaluator) reviews it against specific criteria to provide feedback. This loop continues until the output meets the required quality threshold, mimicking a human draft-and-edit workflow.

---

## What Is It?

The Evaluator-Optimizer pattern (often referred to as the Self-Correction or Critique loop) structures the workflow into a formal feedback loop. Instead of trusting the first output of an LLM, the system introduces a formal quality control checkpoint. The Evaluator acts as a strict editor or judge, analyzing the draft for errors, formatting issues, or logical flaws, and sends structured feedback back to the Optimizer to refine the output.

---

## Why Is It Useful?

* **Near-Human Quality:** By separating the generation mindset from the evaluation mindset, the system achieves significantly higher accuracy, especially in complex reasoning, coding, and writing tasks.
* **Objective Guardrails:** You can embed strict evaluation criteria (e.g., brand guidelines, safety alignment, or unit test results) directly into the Evaluator prompt.
* **Deterministic Termination:** The loop can be configured to stop automatically once a specific score is achieved or after a maximum number of attempts, preventing infinite loops.

---

## Basic Workflow



```text
       ┌────────────────────────────────────────┐
       │                                        │
       ▼                                        │ (Feedback / Improvements)
User Request ──► Optimizer (Generates/Refines) ──┴──► Evaluator (Checks Quality)
                                                            │
                                                            └──► [Passed] ──► Final Output

```

1. **Initial Draft:** The Optimizer receives the user prompt and generates the first version of the asset.
2. **Evaluation Check:** The Evaluator reviews the draft alongside the original user prompt and scores it against explicit metrics.
3. **Condition Gate:** * If the draft **passes** the criteria, it is immediately released as the final output.
* If the draft **fails**, the Evaluator generates constructive, specific feedback detailing what needs to be fixed.


4. **Iterative Refinement:** The Optimizer takes the previous draft and the Evaluator's feedback, generates a new version, and sends it back to step 2.

---

## Example Use Cases

* **Rigorous Code Generation:**
* **Optimizer:** Writes a Python function based on user specifications.
* **Evaluator:** Runs the code against a test harness or checks it for security vulnerabilities. If tests fail, it sends the stack trace back to the Optimizer to fix the bugs.


* **Academic or Technical Translation:**
* **Optimizer:** Translates a technical document into another language.
* **Evaluator:** Reviews the translation specifically checking for accurate domain terminology and tone consistency, rejecting drafts that use loose or informal language.


* **Legal Contract Review:**
* **Optimizer:** Drafts a non-disclosure agreement (NDA).
* **Evaluator:** Checks the draft against a checklist of mandatory legal clauses and regulatory compliance rules.



---

## Advantages

* Drastically reduces hallucinations and edge-case logical errors.
* Delivers highly polished, publication-ready text and production-grade code.
* Provides clear visibility into the system's reasoning process through the logged feedback history.

---

## Limitations

* **High Latency:** Because it involves multiple sequential iterations (often 2 to 4 rounds), it is unsuitable for real-time, low-latency user experiences.
* **Compounding Costs:** A single user request can easily multiply into a dozen LLM calls behind the scenes, heavily consuming token budgets.
* **Diminishing Returns:** After 3 or 4 iterations, the model often begins to over-correct, change correct facts, or plateau in overall quality.

---

## Example Implementation Structure (Conceptual Python)

```python
def evaluator_optimizer_workflow(user_goal):
    current_draft = call_llm(optimizer_prompt, inputs={"goal": user_goal})
    max_iterations = 3
    
    for attempt in range(max_iterations):
        # Evaluator returns a JSON structure: {"status": "PASS"/"FAIL", "feedback": "..."}
        evaluation = call_llm(evaluator_prompt, inputs={"draft": current_draft, "goal": user_goal})
        
        if evaluation["status"] == "PASS":
            return current_draft
        
        # Refine the draft using feedback if it fails
        current_draft = call_llm(
            refinement_prompt, 
            inputs={"prev_draft": current_draft, "feedback": evaluation["feedback"]}
        )
        
    return current_draft  # Return best available effort if max iterations reached

```

---

## Related Concepts

* **Reflection:** Evaluator-Optimizer is the formal, multi-model implementation of the basic Reflection pattern.
* **Orchestrator-Workers:** An orchestrator can deploy an Evaluator-Optimizer loop inside a specific worker sub-task to guarantee quality before synthesis.
