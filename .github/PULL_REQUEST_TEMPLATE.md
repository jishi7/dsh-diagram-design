# Pull request

## What changed

<!-- One paragraph. If this touches a diagram type, name the `type-*.md` and the affected examples. -->

## Scope check

This repo is a DSH-only edition. Before adding files, confirm they are not re-introduced from upstream:

- [ ] No file assumes `skills/diagram-design/` is the install root — this repo's root is the bundle
- [ ] No slash-command routing, plugin manifests, marketplace docs, or Pi prompt templates
- [ ] No CI/lint/verify scripts from upstream's maintainer suite
- [ ] `python scripts/self_check.py` passes on every example or template I touched
- [ ] Prompt snippets and docs say `python`, not `python3` (Windows Store alias trap)
- [ ] `SKILL.md` frontmatter still parses as YAML: quote the `description` when it contains `: ` (an unquoted colon silently removes the skill from the DSH catalog)
- [ ] `SKILL.md` description still under 500 characters (the DSH catalog truncates at 500)

## Testing

<!-- How you verified it: `self_check` output, a generated file, or a screenshot. -->
