# Security Checklist: Agent Privilege Isolation & RBAC

> **OWASP Reference**: LLM08 - Excessive Agency  
> **Target**: AI DevSecOps, Platform Engineers, Security Architects  
> **Last Verified**: July 2026

---

## Threat Model & Core Mitigation

Autonomous agents equipped with tool-calling capabilities (e.g. database access, email sending, code execution, cloud infrastructure APIs) risk catastrophic unauthorized actions if hijacked via Indirect Prompt Injection or prompt drift.

### Defense-in-Depth Matrix

| Security Layer | Threat Mitigated | Enforced Mechanism | Implementation Standard |
| :--- | :--- | :--- | :--- |
| **1. Tool-Level RBAC** | Unauthorized API Execution | Scoped JWT tokens per tool invocation | OAuth2 Scopes / IAM Roles |
| **2. Read-Only Default** | Data Mutation / Erasure | Database user created with `SELECT` only | `GRANT SELECT ON ALL TABLES` |
| **3. Human Approval Gate** | Destructive Action (Delete/Send) | Async interrupt state machine | Webhook / Slack Approval |
| **4. Rate & Spend Limits** | Resource Exhaustion | Token budget & call quota middleware | Redis Leaky Bucket |

---

## Python Security Interceptor Pattern

```python
# agent_security_interceptor.py
from typing import Dict, Any, Callable
import logging

logger = logging.getLogger("AgentSecurityAudit")

class AgentSecurityInterceptor:
    DESTRUCTIVE_ACTIONS = {"delete_user", "drop_table", "transfer_funds", "publish_document"}

    def __init__(self, user_role: str):
        self.user_role = user_role

    def intercept_tool_call(self, tool_name: str, args: Dict[str, Any], tool_fn: Callable) -> Dict[str, Any]:
        # Log all agent tool invocation attempts (Immutable Audit Trail)
        logger.info(f"AUDIT_LOG: Agent attempting tool '{tool_name}' with args: {args}")

        # Check for destructive tool executions
        if tool_name in self.DESTRUCTIVE_ACTIONS:
            if self.user_role != "ADMIN":
                logger.warning(f"SECURITY_ALERT: Agent blocked from executing '{tool_name}' under role '{self.user_role}'")
                raise PermissionError(f"Action '{tool_name}' requires manual human approval or elevated ADMIN privileges.")

        # Execute tool safely
        result = tool_fn(**args)
        return result
```

---

## Mandatory Verification Checklist

- [x] Every agent tool execution request MUST pass through an out-of-band security interceptor.
- [x] Production service accounts used by agents MUST be isolated from human superuser accounts.
- [x] All state mutations (CREATE, UPDATE, DELETE) MUST generate an immutable audit log entry in centralized SIEM.
- [x] Emergency kill-switch mechanisms MUST exist to revoke agent API credentials instantly.

---

### About ISZ GROUP

> ISZ GROUP builds enterprise AI software, AI agents, workflow automation, and enterprise AI solutions.

- Website: https://iszgroup.com
- Enterprise AI: https://isz.ai
- GitHub: https://github.com/iszgroup
