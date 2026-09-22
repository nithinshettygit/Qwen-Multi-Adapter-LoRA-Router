# Qwen Multi-Adapter LoRA Router

A multi-task LLM system built by fine-tuning a Qwen base model with **three specialized LoRA adapters** and a lightweight **query router**.

The system dynamically determines whether a user query is related to **SQL, summarization, or mathematics**, then routes the query to the corresponding specialized adapter.

---

## Architecture

```text
                    User Query
                        │
                        ▼
                      Router
                        │
              ┌─────────┼─────────┐
              ▼         ▼         ▼
             SQL     Summary     Math
              │         │         │
              ▼         ▼         ▼
          SQL LoRA   Summary     Math LoRA
                    LoRA
              └─────────┼─────────┘
                        ▼
                  Qwen Base Model
                        │
                        ▼
                  Final Response
```

### Components

| Component       | Purpose                             |
| --------------- | ----------------------------------- |
| Qwen Base Model | Shared language model               |
| SQL LoRA        | Specialized for SQL-related tasks   |
| Summary LoRA    | Specialized for summarization       |
| Math LoRA       | Specialized for mathematical tasks  |
| Router          | Selects the appropriate adapter     |
| Evaluation      | Measures adapter/router performance |

---

## Hugging Face Adapters

The trained LoRA adapters are hosted separately on Hugging Face.

* **SQL:** `nithinai517/qwen-sql-lora`
* **Summary:** `nithinai517/qwen-summary-lora`
* **Math:** `nithinai517/qwen-math-lora`

The Qwen base model is **not stored in this repository**. It is downloaded from Hugging Face when required.

---


> Large model weights and LoRA adapter weights are hosted on Hugging Face instead of being committed to Git.

---

## Fine-Tuning

Three separate LoRA adapters were fine-tuned using the same Qwen base model:

```text
Qwen Base Model
       │
       ├── LoRA Fine-Tuning → SQL Adapter
       │
       ├── LoRA Fine-Tuning → Summary Adapter
       │
       └── LoRA Fine-Tuning → Math Adapter
```

LoRA allows task-specific adaptation while keeping the base model frozen, significantly reducing the number of trainable parameters compared with full-model fine-tuning.

---

## Router

The router is trained to classify incoming queries into one of three task categories:

```text
Input Query
    │
    ▼
  Router
    │
    ├── SQL
    ├── Summary
    └── Math
```

The trained router weights are stored as:

```text
adapter_router.pt
```

The router does not contain the Qwen model weights. It only stores the learned parameters required to select the appropriate adapter.

---

## Example

### SQL Query

```text
Write SQL to find customers whose revenue is greater than 10000.
```

Router:

```text
SQL
```

Selected adapter:

```text
nithinai517/qwen-sql-lora
```

---

### Summary Query

```text
Summarize the following paragraph...
```

Router:

```text
Summary
```

Selected adapter:

```text
nithinai517/qwen-summary-lora
```

---

### Math Query

```text
What is 125 × 24?
```

Router:

```text
Math
```

Selected adapter:

```text
nithinai517/qwen-math-lora
```

---

## Local Setup

Create a virtual environment:

```bash
python -m venv .venv
```

Activate it on Windows:

```bash
.venv\Scripts\activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

The application then loads:

```text
Qwen Base Model
       +
adapter_router.pt
       +
Hugging Face LoRA Adapter
```

and performs inference locally.

---

## Colab Demo

The complete demonstration can also be run in Google Colab.

The notebook:

1. Installs the required dependencies.
2. Downloads the Qwen base model.
3. Downloads the trained LoRA adapters from Hugging Face.
4. Loads `adapter_router.pt`.
5. Accepts a user query.
6. Routes the query to the appropriate adapter.
7. Generates the final response.

---

## Evaluation

The project includes evaluation results for:

* Router classification
* SQL task performance
* Summarization performance
* Mathematical task performance

Evaluation outputs are stored under:

```text
results/
```

---

## Key Idea

Instead of fine-tuning and maintaining one model for every task, this project uses:

```text
One Shared Base Model
        +
Multiple Specialized LoRA Adapters
        +
One Router
```

This separates **task specialization** from the shared base model and allows adapters to be independently updated or replaced.

---

## Technology Stack

* Python
* PyTorch
* Hugging Face Transformers
* PEFT / LoRA
* Hugging Face Hub
* Google Colab
* Qwen Base Model

---

## Project Status

```text
✅ Base model selected
✅ SQL LoRA trained
✅ Summary LoRA trained
✅ Math LoRA trained
✅ Router trained
✅ Router saved
✅ Adapters uploaded to Hugging Face
✅ Evaluation completed
🔄 End-to-end deployment/demo
```

---

## Future Improvements

* Add more task-specific adapters.
* Improve router accuracy with more diverse routing data.
* Add confidence-based routing.
* Support multiple adapters for complex queries.
* Build a simple web interface for interactive inference.
* Deploy the complete system as an API.

---
