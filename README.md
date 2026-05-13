# claude-resume-bouncer

A tiny HTTPS bouncer that lets GitHub-markdown links launch `claude-resume://` URLs.

## Why this exists

GitHub's markdown sanitizer strips non-http schemes from `<a href>` values, so a markdown link to `claude-resume://...` renders as inert text. This page is a workaround: link to it over HTTPS, pass the artifact URL as `?url=...`, and the page bounces to the custom scheme.

Used by the [`claude-session-share`](https://github.com/squareup/agents/tree/main/skills/claude-session-share) skill in `squareup/agents` to make session-share links in PR descriptions one-click.

## Use it

```
https://daniel-brestoiu.github.io/claude-resume-bouncer/?url=<https-url-of-jsonl-gz>
```

The URL is validated against a strict allowlist (`https://artifactory.sqprod.co/artifactory/user-submitted-artifacts/claude-sessions/...`) to prevent the page from being used as an arbitrary `claude-resume://` launcher.

If the user has the [`claude-session-share`](https://github.com/squareup/agents/tree/main/skills/claude-session-share) skill and the `ClaudeResume.app` URL handler installed, clicking the page's "Resume in Claude" button launches a new Terminal that fetches the session from Block Artifactory and execs `claude -r <uuid>`. If not installed, the page also surfaces a one-paste install-and-resume command.

## Security posture

- Pure static HTML/JS. No backend.
- The allowlist check is the only "policy" — it refuses to bounce anything outside Block's Artifactory `user-submitted-artifacts/claude-sessions/` path.
- The artifact URL is exposed in the GitHub Pages access log (and in the browser address bar) but contains no secrets — it's a Block-internal Artifactory path that requires WARP + SSO to actually fetch from.
- Anyone in the world can hit this page, but the resulting `claude-resume://` launch only works on a machine with the URL handler installed AND access to the linked Artifactory path.

## Why a personal repo

This is an unofficial dogfood experiment for the `claude-session-share` skill. If the pattern proves useful, the natural home is `squareup/agents` GitHub Pages or an internal redirector with SSO — both filed as follow-ups in the skill PR.
