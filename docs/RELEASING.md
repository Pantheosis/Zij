# Releasing

For whoever maintains `Pantheosis/Zij`. Users never need this page; builders have
`BUILD_NOTES.md`.

## What this repository carries

The app, its tests, and four documents: this one, `BUILD_NOTES.md`,
`REFERENCES.md` and `HOSTILE_PASS_CHECKLIST.md`. Nothing else — no build
logs, no change notes, no audit reports, no working notes. Comments in the
source that cite `process/tae_docs/…` are pointing at the maintainer's
private notes repository, which is where all of that lives; it is not
public and is not meant to be. **Before adding a file to this repository,
ask whether it should be readable by anyone, forever.** If the answer is
anything but a clear yes, it belongs in the private notes instead.

## Where things live

- **Source and releases:** this repository. A release's binaries sit beside the exact source
  they were built from (the tag), which the AGPL requires and which lets anyone check a binary
  against its code.
- **The texts** are not in the repository and never will be: they are copyrighted translations,
  worked from privately. Nothing in a release or a workflow may copy them.

## The routine

Every change reaches `main` by pull request; `main` has a ruleset (no force-push, no deletion,
PR required, the `pytest` check required, no bypass). Merge when the check is green.

1. **Test a build without releasing.** Actions → *Build Desktop App* → *Run workflow* → `main`.
   About 25 minutes later the run page has two artifacts (`Zij-Windows`,
   `-macOS`), kept three days, no release made. GitHub wraps an artifact in its own zip on
   download, so the Windows one unzips to `Zij-windows.zip`; unzip
   again. The build is byte-for-byte what the tag would produce from the same commit.
2. **Set the version.** `APP_VERSION` in `engine.py` (section 0) is the tag without its `v`
   and without `-dev` — set it to `1.0.0` and merge that before tagging `v1.0.0`, then put it
   back to `<next>-dev`. It is written into every saved record and every exported analysis, and
   it is printed on the Sources page's citation-key line — the one place a page carries it.
3. **Release.** On `main`, at the commit you tested:

   ```bash
   git tag -a v1.0.0 -m "Zīj v1.0.0"
   git push origin v1.0.0
   ```

   The workflow builds both platforms and creates the GitHub release `v1.0.0` with the two zips
   attached and auto-generated notes; edit the notes on the release page. A push to `main`
   builds nothing — only a `v*` tag or a manual run does.
4. **Version numbers.** Zīj's line begins at v1.0.0. A minor bump for new rules and wording, a
   major one when what the app computes on every chart changes. A released number is never
   reused, even if the release is later withdrawn: people downloaded it.
5. **Undoing a release.** `gh release delete v1.0.0 --yes && git push origin :refs/tags/v1.0.0`
   removes the release, its assets and the tag; the source commit stays.

## The suite in CI

`tests.yml` runs the full suite on every PR and push to `main` (about 25 minutes on the 2-core
runner; the stuck-job guard is 45). Seven tests skip in CI because they read the private corpus;
they run locally. A run that ends *cancelled* rather than *failed* hit the guard, not a test.
