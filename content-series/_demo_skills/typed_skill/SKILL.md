---
name: code_security_check_v2
description: Review code for security vulnerabilities with configurable depth
input_schema:
  language: str
  depth: str
  focus_area: str
---

Review the following {language} code with {depth} depth.
Focus specifically on: {focus_area}.

For each vulnerability found:
1. Name the vulnerability type (OWASP category)
2. Show the vulnerable line(s)
3. Explain the risk
4. Provide a concrete fix
