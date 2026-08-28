# AGENTS.md

You are an AI agent contributing to the Exploitmatic solutions repository.
This file tells you what a solution is and how to write one that will pass
review. Read it before you create or edit any `.txt` file.

## What a solution is

A solution is a plain text `.txt` file, not code. The runtime replays its
steps against a target and runs each step's `assert`. You never write
executable payload logic; you write bytes, expectations, and the words that
explain them.

## The format, in brief

- Header lines first: `id: <vendor>/<slug>`, `summary: ...`, and one `ref:`
  line per CVE or advisory.
- Each step is a block: an identity and a name on the first line, then
  English sentence directives below it.
- A step's identity is the protocol it speaks. The implemented set is:
  `http`, `https`, `websocket`, `tls`, `tcp`, `ssh`, `ftp`, `smtp`, `dns`,
  `ldap`, `redis`, `mysql`, `postgresql`, `smb`, `telnet`, `snmp`,
  `process`, `file`.
- Asserts read as `expect ...`: `expect "<text>" in the response`,
  `expect the response to match "<regex>"`, `expect the target to call
  back "<marker>"`, `expect a flag`, `expect no response`.
- Values run to the end of the line. `#` starts a comment.
- Byte protocols take `send hex "<hex>"`, or `send hex of "text"` for
  readable payloads (`\r`, `\n`, `\t`, `\"`, `\\`, and `\xHH` escapes
  are supported).
- `capture` reads as English: `capture the <shape> "<name>" [of
  <source>] [base64] as <var>` names a page shape (`form field`,
  `attribute`, `json field`, `query parameter`, `cookie`, and on
  byte-family steps `value after` a hex marker, hex-decoded); the regex
  form `capture <var>: "<regex>"` is still available. Later steps
  reference `{var}`. A step's `vars`/`set`/`capture` apply to later steps
  only.
- The complete contract is the formal grammar (`grammar/Exm.g4`,
  `grammar/ExmLex.g4`) and the format reference in the runtime docs.

## Rules that apply to every file

1. `id` is required and unique; `summary` is required.
2. At least one step; every step carries an `expect`.
3. Identity must be one of the implemented set above.
4. Hex fields must be valid hex; `recv`/`port`/`timeout` are numbers; `port`
   is 1 to 65535.
5. No placeholder hosts, no live internet targets, no credentials.
6. Every step should carry a `#` comment so
   a reader can follow the exchange without decoding bytes by hand.

## Before you submit

- Validate your file with the runtime's checker:
  `cd <runtime>/tools/exmcheck && go run . your-file.txt`.
- State the replica and verification result in the PR description (see
  CONTRIBUTING.md for the standard).
- Keep prose direct. No em dashes.
