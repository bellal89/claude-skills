# claude-skills

Claude Code plugin marketplace with personal skills.

## Install

```
/plugin marketplace add bellal89/claude-skills
/plugin install ab@claude-skills
```

## Skills

| Skill | Action |
| --- | --- |
| `/ab:review-pr [PR]` | Review a PR step by step: minimal diff, tests, readability, manual test, `code-review:code-review`, then the PR description |

`ab:review-pr` needs the `code-review` plugin from `claude-plugins-official`.
