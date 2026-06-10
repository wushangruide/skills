# skills

Claude Code skills collection by [@wushangruide](https://github.com/wushangruide).

## Skills

### research-ideation

Systematic iterative research idea generation with multi-role expert review. Designed for researchers facing problems like insufficient innovation, blocked research directions, or need for structured brainstorming.

**Features:**
- Iterative generation loop with knowledge accumulation across rounds
- Four-role review panel: Domain Expert, Cross-Domain Generalist, Devil's Advocate, Mock Reviewer
- Weighted multi-dimensional scoring with configurable threshold
- Literature search integration for novelty checking
- Structured output: per-idea markdown files, structured log (JSON), and summary

**Install:**
```bash
claude plugins install https://github.com/wushangruide/skills/raw/main/research-ideation.skill
```

**Trigger phrases:** "generate research ideas", "improve innovation", "brainstorm systematically", "need novel directions for my paper"

---

## Usage

After installation, the skill auto-triggers when you describe a research problem and ask for systematic idea exploration. You can also invoke it explicitly:

```
/skill research-ideation
```

## License

MIT
