# Contributing

Thanks for contributing to this `llir/llvm` community fork.

This repository continues maintenance work in fork form; it is not an official upstream handover.

## Before you start

- Check open issues and existing PRs for overlap.
- For large or behavior-changing work, open an issue first to align on approach.

## Development setup

```bash
git submodule update --init --recursive
go install ./...
go test -short ./...
```

For full local validation:

```bash
go test ./...
```

If you changed enum definitions in `ir/enum`, regenerate:

```bash
make -C tools
go install golang.org/x/tools/cmd/goimports@latest
make -C asm/enum
```

## Pull request expectations

- Keep changes focused and easy to review.
- Include tests for behavioral changes.
- Update docs (`README.md`, `HISTORY.md`, `howto_update_llvm.md`) when needed.
- Mention LLVM version impact clearly for parser/IR changes.

## Commit guidance

- Use descriptive commit messages.
- Prefer small commits that separate refactors from functional changes.

## Code of conduct

Be respectful and constructive. We prioritize a welcoming contributor experience.
