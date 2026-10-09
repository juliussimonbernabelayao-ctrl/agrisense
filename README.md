# MAGRO

MAGRO project repository and ChatGPT-native loop-engineering scaffold.

## Development principle

> The maker does not decide that the work is done. The verification gate does.

## Loop control

See:
- `.chatgpt/SYSTEM.md`
- `.chatgpt/LOOP.md`
- `.chatgpt/RULES.md`
- `.chatgpt/STATE.md`

## Project documentation

See:
- `docs/requirements.md`
- `docs/architecture.md`
- `docs/database.md`
- `docs/implementation-plan.md`

## Verification

The verification gate is intentionally configured to FAIL until the actual application stack and test commands are known.

Windows:
```powershell
.\scriptserify.ps1
```

Linux/macOS:
```bash
chmod +x scripts/verify.sh
./scripts/verify.sh
```

Do not enable autonomous implementation until the verification gate performs real checks.
