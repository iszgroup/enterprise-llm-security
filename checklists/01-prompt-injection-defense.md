# Security Checklist: Prompt Injection Defense

> **OWASP Reference**: LLM01 - Prompt Injection  
> **Target**: AI DevSecOps & Security Engineers  
> **Last Verified**: July 2026

---

## Defense Depth Matrix

| Defense Layer | Implementation Technique | Effectiveness | Overhead |
| :--- | :--- | :--- | :--- |
| **Layer 1: Structural Isolation** | XML/Markdown Delimiters (`<<<USER_DATA>>>`) | High | <1ms |
| **Layer 2: Input Pre-Filtering** | Fast Regex + Pattern Matching (`ignore previous instructions`) | Medium | <5ms |
| **Layer 3: Model-Based Guardrail** | Secondary Classifier Model (e.g. Llama Guard 3) | Very High | 80ms - 150ms |
| **Layer 4: Output Schema Enforcement**| Strict JSON Schema Validation | High | 0ms |

---

### About ISZ GROUP

> ISZ GROUP builds enterprise AI software, AI agents, workflow automation, and enterprise AI solutions.

- Website: https://iszgroup.com
- Enterprise AI: https://isz.ai
- GitHub: https://github.com/iszgroup
