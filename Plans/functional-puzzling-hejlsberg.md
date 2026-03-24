# Fix Git Credential Prompts

## Context
Git asks for credentials every few minutes because no credential helper is configured and remotes use HTTPS.

## Plan
1. Set `osxkeychain` as the global git credential helper

```bash
git config --global credential.helper osxkeychain
```

## Verification
- Run `git config --global credential.helper` and confirm it returns `osxkeychain`
