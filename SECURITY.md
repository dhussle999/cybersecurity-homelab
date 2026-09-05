# Security policy

This repository contains sanitized documentation only.

Do not commit:

- Passwords or password hashes
- OAuth client credentials or tokens
- SSH private keys
- API keys or recovery codes
- Plex claim tokens
- Public or private infrastructure addresses tied to the live environment
- Raw configuration archives, databases, logs, or personal media

If a secret is committed accidentally, remove it from the repository history
and rotate or revoke it immediately. Deleting only the latest copy is not
sufficient because earlier Git commits retain content.

