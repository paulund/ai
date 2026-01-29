# 7. Including Related Data

- Reference for: API Developer Skill
- Load when: When implementing optional expansion of related resources in responses.

Allow optional expansion: /posts/100?include=author,category.

Keep base payload lean; only embed when requested.
