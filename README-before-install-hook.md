## Before-install hook (repo-local)

Some repositories require a build/setup step **before** dependency installation can succeed
at historical commits (e.g. a `file:` dependency that points at a generated `dist/` folder).

This evaluator supports an optional, repo-checked-in hook file:

- `repo_evaluator_before_install.txt`

### How it works

During F2P/P2P analysis, after checking out each commit and applying the patch (if any),
the evaluator will:

1. Look for `repo_evaluator_before_install.txt` in:
   - the detected `pkg_path` (project root), then
   - the repo root
2. Run each non-empty, non-comment line (`# ...`) as a shell command in order, with:
   - working directory = `pkg_path`
3. If any command fails, installation/testing is aborted with a clear error.

### Example file

```text
# Build legacy isomorphic workspace so file:../isomorphic/dist exists
cd isomorphic
yarn install
./node_modules/.bin/tsc
cd ..
```

### Environment variables

- `REPO_EVAL_RUN_BEFORE_INSTALL`:
  - default `1`
  - set to `0` / `false` / `no` to disable
- `REPO_EVAL_BEFORE_INSTALL_FILE`:
  - default `repo_evaluator_before_install.txt`
  - override the filename if needed
- `REPO_EVAL_BEFORE_INSTALL_TIMEOUT_SECONDS`:
  - default `180`
  - per-command timeout

