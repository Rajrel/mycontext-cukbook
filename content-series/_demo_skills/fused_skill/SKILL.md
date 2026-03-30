---
name: code_security_check_v3
description: Security code review fused with root_cause_analyzer reasoning structure
input_schema:
  language: str
  depth: str
  focus_area: str
pattern: root_cause_analyzer
---

Review the following {language} code with {depth} depth.
Focus specifically on: {focus_area}.

Apply systematic root cause analysis to each vulnerability found:
identify the immediate cause, contributing factors, and root cause.
