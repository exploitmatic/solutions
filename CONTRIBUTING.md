# Contributing

Thanks for contributing a solution. The bar for merging is that the file is
correct and the claim it makes is true: it was verified against a vulnerable
replica, and where the check is behavioral, falsified on a fixed build.

## What counts as a contribution

- A new solution in `solutions/<family>/` that follows the conventions below.
- A fix to an existing solution: wrong bytes, a weak assert, a missing `ref`.
- Documentation: this README, CONTRIBUTING, or a comment that is
  wrong or unclear.

## The verification standard

A solution in the corpus must prove its claim. That means:

- **Verified.** You ran it against a vulnerable replica (a container built for
  the purpose) and it exited 0 with the right result.
- **Falsified where possible.** If the check is behavioral (a crash, a leaked
  byte, a callback), run the same solution against a fixed build and confirm
  it does not pass. This is what separates a check from a claim.
- **Reproducible.** Say in the PR what the replica was: image, version, ports,
  and any prep steps (for example `flush hosts` before a MySQL auth bypass,
  or a writable guest share for SambaCry).

If you cannot run a vulnerable replica, mark the solution clearly as
"unverified" in the PR description and do not place it in the corpus until it
is verified. Host-scope examples and protocol demos live in `examples/`.

## File conventions

- One solution per file, named `<vendor>-<cve>-<slug>.txt`, in the folder of
  its protocol family. Examples: `tls/openssl-heartbleed-mem-leak.txt`,
  `http/log4j-jndi-rce.txt`.
- The `id` line is `<vendor>/<slug>` and must be unique in the repository.
- Header lines: `id`, `summary`, then one `ref:` per CVE or advisory.
- Every step ends with an `assert`. Add a `#` comment to each step so the
  corpus reads cleanly to humans and to LLMs: a comment is what makes a
  byte dump legible. Comment lines are stripped by the runtime, so they
  never affect the run.
- No placeholder targets, no live internet hosts, no credentials.

## Validate before you push

Run the grammar checker from the runtime repository over your file:

```sh
cargo run -p exploitmatic -- check path/to/your-solution.txt
```

`exploitmatic check` validates the file against the formal ANTLR grammar plus
the runtime's semantic rules. CI runs the same check over every `.txt` file
in the repository, so a file that fails it will not merge.

## Pull request checklist

- [ ] One logical change per PR.
- [ ] File passes `exploitmatic check`.
- [ ] `id` is unique and follows `<vendor>/<slug>`.
- [ ] `summary` and step comments read cleanly.
- [ ] `ref:` lines list every CVE.
- [ ] PR description states the replica used and the verification result.

## Code of conduct

Behave per [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md).
