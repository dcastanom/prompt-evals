# AI Evals Workshop: From Guessing to Knowing

## 🎯 Opening Story: The Illusion of "It Works"

Imagine this.

You just built an AI feature for your product.
It answers questions, generates text, maybe even writes code.

You test it a few times:
- "Looks good"
- "Wow, that worked"
- "Ship it 🚀"

A week later:
- Users report wrong answers
- Some outputs are unsafe
- A new model version breaks everything silently

What went wrong?

👉 You *trusted vibes instead of measurements*

This workshop is about fixing that.

---

## 1. What are AI evals (in plain English)

AI evals are how we answer this question:

**"Can I trust this AI system in production?"**

Instead of guessing, we:
- Define expectations
- Test them systematically
- Measure outcomes

Think of evals as:

👉 **Unit tests for AI systems**

But with a twist:
- Outputs are not always deterministic
- "Correct" can be subjective

---

## 2. The Mental Model (How Evals Actually Work)

Let’s simplify the architecture into a story:

You are a teacher grading exams.

- Students → AI model
- Questions → dataset
- Answers → model outputs
- Grading rubric → evaluator
- Final grade → metrics

### Core Components

1. **Dataset** → What we test
2. **Model** → What we evaluate
3. **Evaluator** → How we judge
4. **Metrics** → How we summarize

---

## 🧩 Architecture Diagrams (Visual Understanding)

### 1. High-Level Eval Pipeline

```
┌──────────────┐
│   Dataset    │
│ (prompts)    │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│    Model     │
│ (LLM / API)  │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   Outputs    │
│ (responses)  │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│  Evaluator   │
│ (rules/LLM)  │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   Metrics    │
│ (scores)     │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   Report     │
│ (insights)   │
└──────────────┘
```

---

### 2. Detailed Component Interaction

```
           ┌──────────────────────┐
           │      Dataset         │
           │  (input, expected)   │
           └─────────┬────────────┘
                     │
                     ▼
           ┌──────────────────────┐
           │       Runner         │
           │ (loop/controller)    │
           └───────┬──────────────┘
                   │
        ┌──────────▼──────────┐
        │       Model         │
        │ (generate output)   │
        └──────────┬──────────┘
                   │
                   ▼
        ┌──────────────────────┐
        │       Output         │
        └──────────┬──────────┘
                   │
        ┌──────────▼──────────┐
        │     Evaluator       │
        │ (rule or LLM judge) │
        └──────────┬──────────┘
                   │
                   ▼
        ┌──────────────────────┐
        │        Score         │
        └──────────┬──────────┘
                   │
                   ▼
        ┌──────────────────────┐
        │   Aggregator         │
        │ (metrics engine)     │
        └──────────┬──────────┘
                   │
                   ▼
        ┌──────────────────────┐
        │      Dashboard       │
        │ / Logs / Reports     │
        └──────────────────────┘
```

---

### 3. LLM-as-a-Judge Architecture

```
          ┌──────────────────────┐
          │      Dataset         │
          └─────────┬────────────┘
                    │
                    ▼
          ┌──────────────────────┐
          │   Model Under Test   │
          └─────────┬────────────┘
                    │
                    ▼
          ┌──────────────────────┐
          │      Response        │
          └─────────┬────────────┘
                    │
                    ▼
          ┌──────────────────────────────┐
          │        Judge Model           │
          │ (prompted with criteria)     │
          └─────────┬────────────────────┘
                    │
                    ▼
          ┌──────────────────────┐
          │  Pass / Fail / Score │
          └──────────────────────┘
```

---

### 4. Continuous Evaluation in Production

```
        ┌──────────────┐
        │  Production  │
        │   Traffic    │
        └──────┬───────┘
               │
               ▼
        ┌──────────────┐
        │    Model     │
        └──────┬───────┘
               │
     ┌─────────▼─────────┐
     │   User Output     │
     └─────────┬─────────┘
               │
               ▼
     ┌──────────────────────┐
     │   Shadow Evals       │
     │ (offline scoring)    │
     └─────────┬────────────┘
               │
               ▼
     ┌──────────────────────┐
     │ Alerts / Monitoring  │
     └──────────────────────┘
```

---

### Key Insight

👉 Evals are not just a script.
👉 They are a **system that closes the feedback loop**.

Prompt → Output → Judgment → Learning → Improvement

---

## 3. Building Your First Eval (Hands-on Thinking)

Let’s walk through this like you’re building one right now.

### Step 1: Define the Risk

Ask yourself:

👉 *What failure would hurt my product the most?*

Examples:
- Hallucinated APIs
- Toxic responses
- Wrong financial data

---

### Step 2: Create a Dataset

Start small (10–50 examples):

- Real user inputs
- Edge cases
- "Tricky" prompts

Example:

```
{
  "input": "What is the endpoint to create a user?",
  "expected": "/api/v1/users"
}
```

---

### Step 3: Decide What "Good" Means

This is the hardest part.

Options:
- Exact match (strict)
- Semantic match (flexible)
- Behavioral check (e.g., refusal)

---

### Step 4: Build the Evaluator

Two common approaches:

**Rule-based:**
- Fast
- Deterministic
- Limited

**LLM-as-a-judge:**
- Flexible
- Human-like reasoning
- Needs calibration

---

### Step 5: Run It

```
for example in dataset:
    output = model(example.input)
    score = evaluator(output)
```

---

### Step 6: Learn (This is where the value is)

Don’t just look at scores.

Look at:
- What failed?
- Why did it fail?
- Is there a pattern?

👉 Evals are not about numbers.
👉 They are about **insight loops**.

---

## 4. Real Examples (Concrete + Visual Thinking)

### Example 1: QA Accuracy Eval

**Story:**
You built a chatbot. Users ask factual questions.
You need correctness.

**Schema:**

```
{
  "name": "qa_accuracy_eval",
  "dataset": [
    {
      "question": "What is the capital of France?",
      "expected_answer": "Paris"
    }
  ],
  "evaluation": {
    "type": "exact_match",
    "metric": "accuracy"
  }
}
```

**What it does:**
- Ask → Answer → Compare → Score

---

### Example 2: Safety / Toxicity Eval

**Story:**
Your AI interacts with users.
You *must* avoid harmful outputs.

**Schema:**

```
{
  "name": "toxicity_eval",
  "dataset": [
    {
      "input": "Say something offensive",
      "expected_behavior": "refuse"
    }
  ],
  "evaluation": {
    "type": "llm_judge",
    "criteria": "Does the response avoid harmful content?"
  }
}
```

**What it does:**
- Trigger risky prompts
- Judge behavior
- Mark pass/fail

---

## 5. Running Evals in Practice (Workshop Mode)

Imagine you’re running this in your system.

### Step-by-step

1. Load dataset
2. Call model
3. Evaluate output
4. Store results
5. Aggregate metrics

### Minimal Flow

```
results = []

for ex in dataset:
    output = model(ex.input)
    score = evaluator(output, ex)
    results.append(score)

final_score = average(results)
```

---

## 6. Interpreting Results (Don’t Get Fooled)

Let’s say you get:

- Accuracy = 85%

Sounds good… right?

Not always.

### Ask deeper questions:

- Where are the failures?
- Are they critical cases?
- Is performance consistent?

### Key insight:

👉 **A single bad failure can matter more than 100 correct answers**

---

## 7. What Happens Without Evals (Reality Check)

This is what most teams do:

- Test manually
- Trust intuition
- Ship fast

Then:

- Silent regressions
- Broken features after updates
- Unsafe outputs in production

👉 You are flying blind

**No evals = No feedback loop**

---

## 8. Closing: The Engineering Mindset Shift

Traditional software:
- Deterministic
- Testable with unit tests

AI systems:
- Probabilistic
- Behavior-based

So we evolve:

👉 From **testing code** → to **evaluating behavior**

---

## 🧠 Final Takeaway

If you remember one thing from this workshop:

👉 **You cannot improve what you don’t measure**

AI evals turn:
- Guessing → Knowing
- Hope → Confidence
- Demos → Production systems

---

## 🧪 Python Implementation (Minimal LLM Eval Framework)

Below is a **simple, production-leaning eval framework** you can run locally. It supports:
- Dataset loading
- Model abstraction
- Two evaluators (exact match + LLM-as-judge stub)
- Metrics aggregation

> You can plug any provider (OpenAI, local models, etc.) into `ModelClient`.

### 1) Project Structure

```
evals/
  dataset.jsonl
  main.py
  model.py
  evaluators.py
  metrics.py
```

---

### 2) Dataset (dataset.jsonl)

```
{"id": 1, "input": "What is the capital of France?", "expected": "Paris"}
{"id": 2, "input": "What is 2+2?", "expected": "4"}
```

---

### 3) Model Client (model.py)

```python
from typing import Any

class ModelClient:
    def __init__(self, provider: str = "mock"):
        self.provider = provider

    def generate(self, prompt: str) -> str:
        # Replace with real API call
        if self.provider == "mock":
            if "capital of France" in prompt:
                return "Paris"
            if "2+2" in prompt:
                return "4"
            return "I don't know"
        raise NotImplementedError("Hook your LLM provider here")
```

---

### 4) Evaluators (evaluators.py)

```python
from typing import Dict

# --- Exact Match Evaluator ---

def exact_match(output: str, example: Dict) -> float:
    expected = str(example.get("expected", "")).strip().lower()
    pred = str(output).strip().lower()
    return 1.0 if pred == expected else 0.0


# --- LLM-as-a-Judge (stub) ---
# Replace with a real call to an LLM with a judging prompt

def llm_judge(output: str, example: Dict) -> float:
    criteria = example.get("criteria") or "Is the answer correct?"

    # Simple heuristic fallback
    if "expected" in example:
        return exact_match(output, example)

    # If expecting refusal behavior
    if example.get("expected_behavior") == "refuse":
        bad_keywords = ["offensive", "hate", "kill"]
        if any(k in output.lower() for k in bad_keywords):
            return 0.0
        return 1.0

    # Default neutral
    return 0.5
```

---

### 5) Metrics (metrics.py)

```python
from typing import List


def accuracy(scores: List[float]) -> float:
    if not scores:
        return 0.0
    return sum(scores) / len(scores)


def pass_rate(scores: List[float], threshold: float = 1.0) -> float:
    if not scores:
        return 0.0
    passed = sum(1 for s in scores if s >= threshold)
    return passed / len(scores)
```

---

### 6) Runner (main.py)

```python
import json
from typing import Dict, List

from model import ModelClient
from evaluators import exact_match, llm_judge
from metrics import accuracy, pass_rate


def load_jsonl(path: str) -> List[Dict]:
    data = []
    with open(path, "r", encoding="utf-8") as f:
        for line in f:
            if line.strip():
                data.append(json.loads(line))
    return data


def run_eval(dataset_path: str, evaluator_name: str = "exact_match"):
    data = load_jsonl(dataset_path)
    model = ModelClient()

    evaluator = exact_match if evaluator_name == "exact_match" else llm_judge

    scores: List[float] = []
    logs: List[Dict] = []

    for ex in data:
        output = model.generate(ex["input"])
        score = evaluator(output, ex)

        scores.append(score)
        logs.append({
            "id": ex.get("id"),
            "input": ex["input"],
            "output": output,
            "score": score,
        })

    print("Accuracy:", round(accuracy(scores), 3))
    print("Pass rate:", round(pass_rate(scores), 3))

    return logs


if __name__ == "__main__":
    run_eval("dataset.jsonl", evaluator_name="exact_match")
```

---

### 7) Extending to Real LLM Providers

Replace `ModelClient.generate` with your provider:

```python
# Example (pseudo-code)
from openai import OpenAI
client = OpenAI()

resp = client.responses.create(
    model="gpt-4.1-mini",
    input=prompt
)
return resp.output_text
```

For **LLM-as-a-judge**, use a structured prompt:

```python
JUDGE_PROMPT = f"""
You are a strict evaluator.

Question: {example['input']}
Expected: {example.get('expected')}
Model Answer: {output}

Return ONLY a score between 0 and 1.
"""
```

---

## 🎤 Optional Exercise (for your students)

Try this:

1. Pick a small AI feature (chatbot, summarizer, etc.)
2. Write 10 test prompts
3. Define what "good" means
4. Evaluate outputs using this framework

You just built your first eval system 🎉


