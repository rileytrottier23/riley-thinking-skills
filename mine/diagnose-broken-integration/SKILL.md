---
name: diagnose-broken-integration
description: >-
  Diagnose an automation, integration, or credential that has broken or is
  silently failing — CI/workflow failures, expired tokens and 401s, syncs that
  stopped mirroring, deploys that report success while doing nothing, connectors
  that quietly went stale. Use this whenever someone reports that something
  "stopped working", forwards a failure notification or a screenshot of one,
  asks why a build/job/sync is failing, or asks you to fix an expired key or
  secret — even if they don't name a system and even if the primary system looks
  unreachable from here. Also use it when a monitor or health check is green but
  the thing it watches seems broken. Reach for this before concluding you lack
  access to investigate: the method is largely about getting a real answer
  through the connectors you do have.
---

# Diagnosing a broken integration

The failure mode this skill exists to prevent is confident guessing. Most of
these investigations end with a plausible story that was never tested, or with
"I can't access that system" when a usable path existed one connector over.

Two commitments carry most of the value: **get to primary evidence rather than
someone's summary of it**, and **prove the failure live before naming a cause**.

## 1. Get the primary source

A screenshot or forwarded alert is a lead, not evidence. It is cropped, it lacks
identifiers, and it has already been through one lossy retelling.

Go find the original. A GitHub Actions failure email carries the run URL, run ID,
exact duration, commit SHA, and timestamps that the screenshot cut off. Search
the user's mail, the CI run page, the log — whatever holds the untouched record.

Extract specifics, because they discriminate between causes later:

- **Identifiers** — run ID, job name, commit SHA, resource name
- **Timing** — when it started failing, and whether prior runs passed
- **Duration** — a job that dies in 4 seconds failed at auth or config; one that
  dies in 10 minutes hit a timeout or a real test failure

## 2. Probe access, and learn *which* door is closed

Try the direct route: CLI, API, clone. When it fails, the useful output is not
"no access" but the specific reason — tool not installed, not authenticated,
session not scoped to this resource, resource private. Each implies a different
workaround.

Timebox this. Two or three attempts is plenty. You are mapping the boundary, not
trying to break through it.

If access is denied for a legitimate security reason, the answer is to ask the
user to grant it — never to look for a way around the control. Routing around a
*blocker* means using another tool the user already authorised. It never means
circumventing a protection.

## 3. Route around — the move most often skipped

Ask: **which system I can reach holds a copy, mirror, or reflection of what I
need?**

This is where investigations are won. Concretely:

- A deploy environment synced from a repo contains the repo's files — including
  CI config that lives in `.github/`
- A notification email contains run metadata the API would have given you
- A connector's agent can read its own filesystem and run commands inside the
  environment that actually holds the credential
- A calendar, ticket, or chat log can date when something changed

The repo being unreachable does not mean the workflow file is unreachable.

## 4. Verify live — never infer

The gap between "the token is probably expired" and "the token returns HTTP 401"
is the whole diagnosis. Everything before a live check is hypothesis.

**Have the environment that owns the credential test it.** Never route a secret
through the conversation, and never ask the user to paste one.

Ask for **status codes, headers, prefixes, and lengths** — never values:

```
curl -s -o /dev/null -w "status=%{http_code}\n" -H "Authorization: Bearer $TOKEN" https://api.example.com/whoami
printf 'prefix=%s len=%s\n' "${TOKEN:0:8}" "${#TOKEN}"
```

Eight characters of a forty-character token is a safe fingerprint and enough to
tell whether a value changed. A full dump is a credential leak — do not ask for
`od -c`, `cat`, or `echo` of a secret. If one lands in the transcript anyway,
say so immediately and treat it as burned regardless of whether it still works.

**A zero exit code is not proof of success.** Tools crash and report success:
a package install can die partway through and still exit 0, leaving a directory
half-populated; a sync can skip its push and exit clean. So verify the *effect*,
not the status — does the artifact exist, did the remote SHA move, is the binary
on disk? When a step passes but the next one fails on something the passing step
was supposed to produce, suspect the passing step first.

Prefer **local, offline checks** when they exist — they cost nothing and handle
no secrets. GitHub tokens, for instance, carry a CRC32 checksum in their last six
characters, so a mistyped value can be ruled in or out without a network call:

```python
import zlib
ALPHA = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz"
def b62(n, w=6):
    s = ""
    while n: n, r = divmod(n, 62); s = ALPHA[r] + s
    return s.rjust(w, "0")
body = token[4:]                      # strip ghp_ / gho_ / ghs_
assert b62(zlib.crc32(body[:-6].encode())) == body[-6:]
```

## 5. Enumerate every candidate — names lie

Do not assume the obviously-named credential is the one in use. Environments
accumulate near-duplicates: `GITHUB_TOKEN` beside `GITHUB_PERSONAL_ACCESS_TOKEN`,
one injected read-only by a platform connector and one editable by the user.

Two habits close this gap fast:

- **List every credential in the environment**, not just the one you were pointed at
- **Grep the consumer** for which variable it actually reads, and quote the line.
  What the script reads settles it; what the name suggests does not.

Ask directly whether a field is **editable**. Platform-managed or linked config
is often read-only, and a user can spend a long time "updating" a value that
never changes. A value that survives a full restart unchanged means either the
environment is stale *or* the write never landed — those look identical from
outside, so distinguish them by comparing prefixes before and after.

## 6. Discriminate hypotheses with timeline evidence

Usually two or three causes fit. History eliminates most of them cheaply:

- **First failure after a long run of passes** → something changed externally.
  Credential expiry, upstream API change, rotation.
- **Failed on every run since it was created** → misconfiguration. It never worked.
- **Failure count versus schedule** — one failure email across seven scheduled
  runs is very different from seven.

When nothing in the repository changed but the job broke anyway, suspect the
**environment underneath it**. Runner images, default language versions, and base
images move on the vendor's schedule, not yours. Anything unpinned is a version
someone else controls: check for a missing `setup-node`/`setup-python` step, an
unpinned base image, or a `@latest` action. The signature is a long gap between
the last success and the first failure, with an empty diff across it.

## 7. Find the silent damage

The alarm is not the injury. A failing health check usually means something else
has been quietly broken for longer, and *that* is what the user cares about.

Ask: what depended on this? How long has it been down? What is missing as a
result — commits that never mirrored, jobs that never ran, data that never synced?

Watch for the inverse too: **a monitor that watches a copy will eventually lie.**
If a check validates a duplicate of a credential rather than the one in use, it
can report green while the real thing is dead. A green monitor over a broken
system is worse than no monitor, because it suppresses investigation. When you
find one, say so plainly — the user believes it is watching something it isn't.

### Expect a backlog when the reporting channel comes back

Broken plumbing hides more than its own failure. If a job only runs when a sync
fires, and the sync was dead, then *nothing downstream ran either* — and none of
it reported. Restoring the channel does not create those failures, it reveals
them.

So when a fix is followed by a burst of new alerts, resist reading them as
regressions you caused. Check whether the failing thing ever ran during the
outage. A job whose last success predates the outage and which has not run since
was already broken; it just had no way to say so.

Tell the user this before they see the alerts, or the fix will look like the
cause. Then work the backlog as separate incidents rather than folding them into
the original diagnosis — they usually have unrelated root causes.

## 8. Working through an intermediary agent

When another agent is your only route into an environment, treat it as an
unreliable narrator. It will hallucinate causes, assert things it cannot know,
and contradict its own earlier findings.

- **Demand raw output.** "Paste exactly what the command printed, no summarising."
  Its conclusions are commentary; the bytes are evidence.
- **Challenge unverifiable claims.** If it reports on state it cannot read —
  write-only secrets, another system's config — ask how it knows and invite a
  retraction. They often retract.
- **Watch for self-contradiction** across turns and resolve it before proceeding.
- **Put it on a leash.** State exactly what is authorised: "read-only from here",
  or "I am authorising one write, this specific command, nothing else." These
  agents take destructive initiative — force-pushing, overwriting secrets,
  dispatching jobs — and report it only afterwards.
- **Ask for a write log**: "list every write action you took this session."
  Do this before you trust any picture of current state.

If something destructive already happened, **preserve before analysing**. Capture
the prior state into a safe ref or copy first — orphaned git commits are
recoverable only until garbage collection. Then assess the damage.

Suspecting the agent is not the same as convicting it. When you think it caused
something, check the record before saying so — `git log` on the file, the write
log you asked for, timestamps. Being wrong about blame in either direction costs
you: accusing wrongly wastes the user's trust in your other findings, and
excusing wrongly leaves a real hazard in place.

## 9. Report

Lead with the finding in one line. Then:

- **Separate verified from inferred.** Say which claims rest on a live check and
  which are reasoning. Name what you could not check, and why.
- **Give the silent damage its own paragraph.** It usually matters more than the
  alarm.
- **Flag design flaws you noticed**, not just the immediate cause — a credential
  copied by hand into two places will drift; a weekly check has a week-long
  detection window; a commit message claiming more than the code does.
- **Say plainly what only the user can do.** Minting credentials and clicking
  through browser settings are theirs.
- **Correct yourself out loud** when new evidence overturns something you said
  earlier. Leaving a wrong claim standing is worse than having made it.
- **Calibrate a partial fix honestly.** If a change improved things without
  resolving them, say exactly that — "this fixed two steps and the failure moved
  to a third" — rather than presenting progress as a solution. Overstating a fix
  is the same error as overstating a cause; it just costs the user later instead
  of now.

## 10. Guiding the fix

When remediation is the user's to perform, resolve the branching questions first —
what device they are on, which variant they want — since those change the
instructions. Then **one step per message**, waiting for confirmation.

- **Flag one-shot and irreversible moments loudly.** A token shown exactly once,
  a destructive confirm, a value that cannot be recovered.
- **Name the specific field.** "The editable secret, not the read-only one" beats
  "update the token" when two similarly-named fields exist.
- **Warn about the failure that looks like a different failure** — trailing
  whitespace producing a 401 identical to expiry; a change that needs a restart
  to take effect.
- **Recommend least privilege.** If a task needs one scope, say so. Broad scopes
  on long-lived credentials turn a small leak into a large one.
- **Verify the step landed before moving on**, through the same live channel used
  to diagnose. Confirming each step in isolation localises the next failure
  instead of compounding it.
