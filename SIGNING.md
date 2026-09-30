# Signing a Station Ops release

The workflow that signs an approved Windows build, collects the matching
Android, Linux and macOS packages, and stages one cross-platform draft is
`.github/workflows/sign-and-stage.yml`. It is reviewed alongside the code it
signs, in the private source repository, and deployed here.

## Why signing is not in the private repository

GitHub's documentation: **"Users with GitHub Free plans can only configure
environments for public repositories."**

`KeystoneCore` is on the Free plan and `station-apps` is private. An
`environment:` named in a private workflow there is decorative — no required
reviewer, no approval, no gate. A signing key placed behind it would sit in a
job that checks out an arbitrary commit and runs its scripts, with nothing
stopping the run.

`station-ops-releases` is public, so environments and their protection rules
work. The key goes there, behind a required reviewer, and the source stays
private.

## What must be configured, before this can run

On **KeystoneCore/station-ops-releases** — Settings → Environments → New
environment, named exactly `release-signing`:

| Setting | Value | Why |
|---|---|---|
| **Required reviewers** | `geoffreygarrett` | **This is the gate.** Up to six may be listed; one approval proceeds |
| Prevent self-review | **leave off** | see the note below |
| Deployment branches and tags | Selected — `main` only | the default branch. An arbitrary ref cannot deploy to this environment |
| Environment secret | `EDDSA_PRIVATE_KEY` | the base64 Ed25519 seed |
| Environment secret | `SOURCE_READ_TOKEN` | read-only on the private repository's Actions artifacts |

`SOURCE_READ_TOKEN` is a fine-grained token on `KeystoneCore/station-apps`
with **Actions: read** and **Metadata: read**, and nothing else. It cannot
write anywhere and cannot read the private repository's contents.

### What a single-member organisation can and cannot get from this

`KeystoneCore` has one member. GitHub allows a reviewer to approve a run they
triggered — *"Optionally, to prevent users from approving workflow runs that
they triggered, select **Prevent self-review**"* — so the gate does function,
but it is worth being honest about what it is.

**It is a deliberate pause, not a second pair of eyes.** What it actually
prevents:

- a run reaching the key without a person seeing which ref and which build it
  is for;
- an automated, scheduled or mistaken dispatch signing anything;
- a signing job starting at all if the environment is missing or unprotected,
  because the `guard` job refuses before the key is in scope.

What it does not prevent is the one person approving their own mistake. That
needs a second maintainer, and listing one later is a change to this setting
and nothing else.

**Do not turn on Prevent self-review while there is one member** — it would
make the environment unapprovable and the signing job permanently stuck.

### The gate existing is not the gate having worked

`guard` confirms a required reviewer is *configured*. That is not the same as
an approval having been *exercised*, and the difference is the whole point —
a misconfigured environment, a reviewer who cannot actually approve, or a
deployment-branch rule that silently blocks the run all look identical from
outside until somebody tries.

So before any production key exists, prove the pause:

**Actions → Sign and stage a Station Ops release → Run workflow**

| Input | Value |
|---|---|
| `dry_run` | **true** |
| everything else | leave empty |

Expected, in order:

1. **`guard` succeeds** — it reads this repository's own environment
   configuration and finds a required reviewer. It holds no secrets.
2. **`sign` does not start.** The run shows *Waiting* and names the
   environment. **This is the thing being proved.** If it starts immediately,
   the environment has no required reviewer and the gate is not real.
3. You approve it.
4. **`sign` runs its first step and stops**, reporting that the gate was
   reached and who approved it. It reads no secret, fetches no artifact and
   touches no release. The run is **green**.

If step 2 does not happen, stop and fix the environment. Nothing else in this
document is worth doing until a run visibly waits.

Only after that: provision `EDDSA_PRIVATE_KEY` and `SOURCE_READ_TOKEN`. A key
created before the pause has been seen to work has nowhere safe to live.

### What approval means here, exactly

`KeystoneCore` has one member, so the reviewer and the person dispatching are
the same. **This is a deliberate confirmation, not an independent review.**

Saying that plainly matters because the two are easy to conflate. What a
single-reviewer gate actually gives:

- a run cannot reach the signing key without a person stopping, reading which
  commit and which version it is for, and choosing to continue;
- nothing scheduled, automated or mistakenly dispatched can sign anything;
- the job refuses outright when the environment is missing or unprotected.

What it does not give: a second pair of eyes on the decision. One person can
still approve their own mistake. That needs a second maintainer listed as a
reviewer, which is a change to this one setting and nothing else.

## The key-bearing job's execution boundary

Stated in full, because "we pinned the signing script" was not enough: the job
that held the key also ran other scripts from the same checkout, any of which
could have read it.

**The signing job runs nothing from the candidate application.**

| | |
|---|---|
| Checks out | this repository only. The private source is never on disk |
| Runs | only commands written in the workflow file itself. No repository script, no build, no `dart`, no `flutter` |
| Takes from the candidate | the matching platform artifacts, as **files**. They are checked, hashed, packaged and uploaded. Nothing in them is executed |
| Inputs | validated against a shape, then passed through `env:`. No `${{ }}` appears inside any `run:` block |
| Permissions | `{}` at the top; `contents: read` for the guard; `contents: write` for signing. Nothing else |
| Secrets | only in the signing job, only after approval |

Before it signs, it confirms: the run concluded successfully, its head SHA is
the approved commit, the workflow was `Manual platform builds`, exactly one
installer is present, its filename matches the version and source, **its
SHA-256 matches the digest the operator approved**, the bundle executable is
x64, and `WinSparkle.dll` is present.

After signing it verifies the signature against the public half derived from
the same seed, so a signature that would fail on a station fails on the runner.

## What stays in the private repository

`manual-publish.yml` keeps only the `feed` step, which regenerates the channel
documents from what is published. It holds `RELEASES_TOKEN` (write releases on
the public repository) and no signing key. Dispatching its `stage` step now
fails with a pointer here.
