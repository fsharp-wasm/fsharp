# AGENTS.md

This file provides guidance to AI coding agents and to contributors working in this repository.

## fsharp-wasm fork policy

This repository is the fsharp-wasm organisation's fork of **[dotnet/fsharp](https://github.com/dotnet/fsharp)**. It exists so that [fsharp-wasm](https://github.com/fsharp-wasm/fsharp-wasm) can pin an exact revision of the F# compiler (the front end that parses and type-checks the programs fsharp-wasm compiles), and so that our changes stay mergeable with upstream. Read this before you touch a branch.

### Branches

| branch | what it is | rules |
|---|---|---|
| `main` | a mirror of upstream's `main` | only ever fast-forwarded from upstream. **Never commit here.** |
| `develop` | **our branch**: upstream plus the changes below | what fsharp-wasm pins (the submodule points at a commit of this branch). Merge `main` into it to take upstream's changes. |

Those are the only two long-lived branches. A change that belongs upstream is a short-lived topic branch cut from `main` for a pull request to [dotnet/fsharp](https://github.com/dotnet/fsharp); delete it when it is merged or closed. Nothing else is kept.

### Taking upstream's changes

```bash
git remote add upstream https://github.com/dotnet/fsharp.git      # once
git fetch upstream
git checkout main && git merge --ff-only upstream/main && git push origin main
git checkout develop && git merge main      # a merge, not a rebase: the commits that fsharp-wasm pins keep their SHAs
# resolve conflicts, build, run the tests, then
git push origin develop
```

After a merge, bump the submodule pin in fsharp-wasm (and in the repositories that pin this fork) to the new `develop` commit, in a commit of its own.

### What we change

- **Computation-expression enhancements**: `if!`, `for!` and `while!` desugared in the checker (`CheckComputationExpressions.fs`) — they exist only in this fork.
- **A build that runs outside Microsoft's infrastructure**: net10 adaptations of FSharp.Core and the build tasks, a GitHub Actions build, and NuGet packaging.
- The directories fsharp-wasm does not need (Visual Studio integration, the legacy `fsc`) were removed; do not bring them back.

Keep our changes small and isolated, and say in the commit message why a change is ours and not upstream's. Do not rewrite upstream's history, and never rewrite `main`. Upstream's licence (MIT) and headers stay as they are; the fork's own files are under the same licence.

### Where this is going

Far in the future, **not now**: slim this compiler down to a pure language core and let it evolve — with features from [Lean](https://lean-lang.org/) such as type classes by dictionary passing, stronger do-notation, compile-time evaluation and termination checking — into a language of our own. Nothing in this repository works towards that yet; it is written down so that nobody mistakes the fork for the plan. Until then the rule above holds: small changes, mergeable with upstream.

---

Upstream's own guidance for agents is in [.github/copilot-instructions.md](.github/copilot-instructions.md).
