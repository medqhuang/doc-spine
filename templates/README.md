# <PROJECT_NAME>

<One-sentence project description. What is this project? What problem does it solve? What's the deliverable?>

```
CLAUDE.md / README.md / HOWTO.md     entry files (AI / human / architecture)
docs/                                 ROADMAP / CONVENTIONS / (optional) <REGISTRY>.yaml / _archive
<library/>                            (optional) reusable code library, if applicable
instances/<ACTIVE_INSTANCE>/         ★ STATE.md / docs/TASKS.md / reports/
instances/<frozen_baseline>/         (optional) frozen baseline, .aiignore enforced
```

30-second orient:

- Current work → [`instances/<ACTIVE_INSTANCE>/STATE.md`](instances/<ACTIVE_INSTANCE>/STATE.md) ★
- How to use this architecture → [`HOWTO.md`](HOWTO.md)
- AI rules (auto-loaded) → [`CLAUDE.md`](CLAUDE.md)
- Other docs (ROADMAP / CONVENTIONS / etc.) → see directory tree above

---
*Bootstrapped from [anti-entropy-docs](https://github.com/<YOUR_USER>/anti-entropy-docs) on YYYY-MM-DD.*
