# PR ที่ดี
**Title** — imperative mood, concise, scope ชัด
```
feat(auth): add PKCE flow for OAuth2 authorization
fix(api): prevent race condition in concurrent token refresh
refactor(sap): extract SAPClient retry logic into decorator
```

**Description template**
```
## What
Brief summary of the change — 1-3 sentences.

## Why
Problem being solved or business context.

## How
Key technical decisions, trade-offs ที่เลือก, อะไรที่ไม่ทำและทำไม.

## Testing
- [ ] Unit tests added/updated
- [ ] Manual test steps (ถ้า non-trivial)
- [ ] Edge cases covered

## Notes / Breaking Changes
Migration steps, env vars ใหม่, deprecations.

```

# Commit
## Commit Message Format (Conventional Commits)
```
<type>(<scope>): <short summary>

[optional body]

[optional footer]
```

**Types:**
```
feat      – new feature
fix       – bug fix
refactor  – no behavior change
perf      – performance improvement
test      – tests only
chore     – build, deps, tooling
docs      – documentation
ci        – CI/CD config
revert    – revert prior commit
```

**ตัวอย่าง:**

```
feat(auth): add device fingerprint to session binding

Store device hash in httpOnly cookie alongside access token.
Prevents session hijacking when token is leaked without device context.

Closes #PRJ-214
```

```
fix(sap): retry on 503 only, not all 5xx

Previous logic retried on all 5xx including 501/505 which are
non-recoverable. Wastes retry budget and delays error surfacing.
```

**หลักการ:**

- Summary บรรทัดแรก **≤ 72 chars**, imperative mood ("add" ไม่ใช่ "added")
- Body อธิบาย **why** ไม่ใช่ what — code อ่าน what ได้อยู่แล้ว
- Footer สำหรับ `Closes #`, `BREAKING CHANGE:`, `Co-authored-by:`
- 1 commit = 1 logical change — ถ้า `git diff` มี 3 เรื่อง ให้ `git add -p` แยก