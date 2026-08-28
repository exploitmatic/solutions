# Security

## This repository

This repository contains working exploit logic in data form. The `.txt`
files replay real attacks: memory disclosure, RCE, denial of service. Two
rules apply to everyone who clones it.

1. Use the solutions only against systems you own or are explicitly
   authorized to test.
2. Never run them against live, third-party, or internet-reachable targets.

The runtime is a security testing tool. Results come from asserts over
responses, not from anything a solution executes; still, the same
authorization rules apply.

## Reporting a vulnerability

If you find a security issue in this repository (the docs, the CI, or a
solution that misleads its reader), report it privately: use the GitHub
Security Advisory workflow on this repository or contact the maintainers
directly. Do not post exploit details publicly before they are fixed. Issues
in the runtime itself belong in the runtime repository.
