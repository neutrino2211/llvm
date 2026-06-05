# AGENTS.md

This file is the operating guide for human and AI agents working in this repository.

## Mission

Keep this fork current with upstream LLVM IR changes while preserving existing Go API behavior as much as possible.

Primary near-term objective: bring this repo up to date with the latest stable LLVM release.

## Ground Rules

- Make focused, reviewable changes.
- Prefer additive/backward-compatible API changes unless a break is required by LLVM semantics.
- Do not hand-edit generated enum string files when regeneration is possible.
- Avoid destructive git commands (`reset --hard`, `checkout --`, etc.).
- Do not imply official upstream maintainership transfer; this is a community fork continuation.

## Project Facts

- Module: `github.com/llir/llvm`
- Test corpus: git submodule at `testdata` (`github.com/llir/testdata`)
- Parser bridge: `asm` package translates parsed AST into `ir` data structures
- Enum generation entrypoint: `make -C asm/enum`

## Local Setup

```bash
git submodule update --init --recursive
go install ./...
go test -short ./...
```

If enum regeneration is needed:

```bash
make -C tools
go install golang.org/x/tools/cmd/goimports@latest
make -C asm/enum
```

## LLVM Upgrade Runbook

1. Identify current target LLVM version.
2. Identify latest upstream LLVM release tag.
3. Diff upstream parser/IR surface between those releases.
4. Implement required updates in `ir/*` and `asm/*`.
5. Regenerate enums (`make -C asm/enum`) when enum sets changed.
6. Update test inputs/submodule as needed for new LLVM coverage.
7. Run tests and fix regressions.
8. Update docs/changelog (`README.md`, `HISTORY.md`, upgrade notes) to reflect the new LLVM target.

### Helpful commands

Find latest LLVM tag:

```bash
git ls-remote --tags https://github.com/llvm/llvm-project.git 'llvmorg-*' \
  | awk -F/ '{print $3}' \
  | sed 's/\^{}//' \
  | sort -V \
  | tail -1
```

Compare LLVM AsmParser changes between two releases:

```bash
OLD=llvmorg-X.Y.Z
NEW=llvmorg-A.B.C
wget https://github.com/llvm/llvm-project/archive/${OLD}.tar.gz
wget https://github.com/llvm/llvm-project/archive/${NEW}.tar.gz
tar zxf ${OLD}.tar.gz
tar zxf ${NEW}.tar.gz
git diff llvm-project-${OLD}/llvm/lib/AsmParser llvm-project-${NEW}/llvm/lib/AsmParser
```

## Files Usually Involved In LLVM-Version Updates

- `ir/enum/*` (new/changed enum values)
- `asm/enum/*` (regenerated enum helpers)
- `asm/*` (AST-to-IR translation for new syntax)
- `ir/*` (new IR constructs/fields/enums)
- `testdata` submodule pointer
- `README.md` version mapping
- `HISTORY.md` release notes
- `howto_update_llvm.md` (process notes)

## Done Criteria For An Upgrade PR

- All tests pass locally:
  - `go test -short ./...`
  - `go test ./...`
- Regenerated files are included when required.
- Docs state the new LLVM target version.
- The PR description lists major language/IR additions and known limitations.
