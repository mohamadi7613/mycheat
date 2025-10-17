
# Prompt engineering

## list of LLMs

1. LLaMA family (LLaMA 2, LLaMA 3)    Meta
2. Gemmeni                       Google

## ollama 

+  Ollama  ---> Local LLM runner

```bash
ollama run mistral
```


## Prompt Patterns

```text
Explain this code in simple terms.         # 1. Instruction
You are a senior data engineer            # 2. Role-based
Solve step by step: What’s 15% of 240?      # 3. Chain of Thought
Return the answer as a markdown table.      # 5. Format control (.md, .json)
Summarize this in 3 sentences / 5 bullet points.    # 5. Summarization
Classify the sentiment: "I love this pizza". exampels: text: "" -> semantic: "+" # 4. Few-shot prompting
Translation → “Translate this to Spanish, keeping code unchanged.”

Code generation → “Write a Python function to fetch API data and save as CSV.”

Code explanation → “Explain what this regex does in plain English.”

Comparison → “Compare Flask vs Django in a table.”

Brainstorming → “List 10 app ideas combining AI + education.”

```
### shots prompting
+ give a language model a few examples of input–output pairs inside the prompt

1. Zero-shot → No examples given, just instructions.

Few-shot → A handful of examples given. (like 3 examples)

One-shot → Exactly one example given.


### types of prompts


1. system prompt        ---> what task they are performing ---> sets role/behavior ("You are a helpful assistant")
2. user prompt          ---> conversation with user         ---> actual question/request

### token + context window
1. A token is a chunk of text (word or a part of word like chat+bot)
2. context window = all your previous conversations in a single interaction


### advanced options
Temperature → Higher = more creative, lower = more deterministic.

Max tokens → Controls output length.




### Pitfalls
Hallucinations (confidently wrong answers) → always verify.

Prompt sensitivity → tiny rewording can change output.

Context limits → long conversations may truncate earlier parts.




# cursor

### project management steps

1. Master Plan              ---> big picture roadmap
2. PRD                      ---> detailed spec for one feature/release
3. Feature Document         ---> smaller, lightweight write-up for design/dev handoff



#### 1. master plan

+ high-level, long-term roadmap, major steps
+ use as `Initial Context`


#### 2. PRD (Prod Req Doc)

+ Make it clear and actionable for engineers and designers. 
+ formula:
```text
You are a product manager. Write a Product Requirements Document (PRD) for adding a "real-time collaboration" feature to our note-taking app. 
Include:
- Purpose
- Goals & success metrics
- User stories
- Functional requirements
- Non-functional requirements (performance, security, etc.)
- Dependencies
- Acceptance criteria
```

+ PRD for a To-Do app
    1. Purpose: Let users set deadlines on tasks.
    2. Requirement: Each task should have an optional “due date” field.
    3. Acceptance criteria:
        - User can add/edit/remove a due date.
        - Overdue tasks appear in red.
        - Due dates sync across devices.

+ E-Commerce Website: Guest Checkout:
    1. Purpose: Increase conversion rate by allowing purchases without requiring account creation.
    2. Functional Requirements:
        - Guest users can add items to cart and checkout.
        - Collect email for receipts and shipping updates.
        - Offer optional “create account” after purchase.
    6. Non-Functional Requirements:
        - Checkout time < 2 minutes on average.
        - System must handle 10,000 concurrent guest checkouts.
    9. Acceptance Criteria:
        - A/B test shows ≥ 15% improvement in completed checkouts.
        - No increase in cart abandonment rates.

#### 3. Feature Document 







































































