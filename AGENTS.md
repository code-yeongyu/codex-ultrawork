# Repository Conventions

Conventions for human contributors and AI agents working on this repository.

## Style

- Terse technical prose. No emojis in commits, issues, PR comments, or code.
- Python: PEP 484 type hints. Hook script stays in standard-library only — no pip dependencies.
- Tabs for indentation in JSON and Markdown tables. Spaces (4) for Python.
- Double quotes for JSON strings.

## Layout

- `hooks/ultrawork-detector.py` — the only behavior. Pure stdlib. Reads JSON on stdin, writes the directive to stdout when the keyword matches, exits 0 otherwise.
- `hooks/hooks.json` — registers the script under `UserPromptSubmit`.
- `.codex-plugin/plugin.json` — Codex plugin manifest. Marketplace metadata lives here, not in `package.json`.

## Constraints

- Never let the hook block a turn — exit code is always 0.
- Never make a network call from the hook.
- Keep the directive in `ULTRAWORK_DIRECTIVE` self-contained inside the Python file. The hook is a single artifact a reviewer can read top-to-bottom.
- When editing `ULTRAWORK_DIRECTIVE`, apply the `prompt-engineering` skill's entropy gate: every edit must reduce uncertainty per token. Re-measure character count before committing.

## Commands

```bash
# smoke test the hook
PAYLOAD='{"cwd":"/tmp","hook_event_name":"UserPromptSubmit","model":"gpt-5.5","permission_mode":"default","session_id":"x","transcript_path":"","turn_id":"y","prompt":"please ultrawork"}'
echo "$PAYLOAD" | python3 hooks/ultrawork-detector.py | head -3

# pattern boundary check (must be empty)
echo '{"hook_event_name":"UserPromptSubmit","prompt":"refactor ulw_helper.ts"}' | python3 hooks/ultrawork-detector.py | wc -c
```
