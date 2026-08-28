<p align="center">
  <img src="logo.svg" alt="Exploitmatic solutions" width="72">
</p>

# Exploitmatic solutions

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)
[![validate](https://img.shields.io/github/actions/workflow/status/exploitmatic/solutions/validate.yml?branch=main)](https://github.com/exploitmatic/solutions/actions/workflows/validate.yml)

Verified attack techniques written down as data. This repository is the
community corpus for the [Exploitmatic](https://github.com/exploitmatic/exploitmatic)
runtime: plain text `.txt` files that replay a known attack against a target
and prove the result with asserts.

A solution is data, never code. It is bytes in, bytes out, and a set of tests
over the response. There is no payload source in this repository, no template
engine, and no arbitrary-code supply chain. The runtime never executes
anything from a solution; it replays steps and reports what the asserts found.

## What a solution looks like

```text
id: openssl/heartbleed-mem-leak
summary: CVE-2014-0160 Heartbleed
ref: CVE-2014-0160

tls clienthello-heartbeat
  send hex "1603030125010001210303..."
  receive until "0e000000"
  receive 65536 bytes
  expect "0e000000" in the response

tls malformed-heartbeat
  send hex "1803030003014000"
  receive 70000 bytes
  expect the response to match "18030[123]40"
```

Header lines at the top name the attack. Each block after that is one step: an
identity and a name, then English directives, then an `expect`, the pass or
fail test. The runtime prints one line per step and a result: verified
when the attack worked, not verified when it did not.

## Run one

Install the runtime, then point it at a target you own or are authorized to
test:

```sh
exploitmatic solutions/http/log4j-jndi-rce.txt http://10.0.0.5:8080
```

Useful flags: `-param`, `-flag-path`, `-timeout`, `-oob-host`, `-oob-port`.
See the runtime docs for the full reference.

## Repository layout

```text
solutions/   the verified corpus, one folder per protocol family
examples/    examples and demos (host scope + protocol demos)
```

## Verified corpus

The seed corpus. Every entry was verified against a vulnerable replica, and
where the check is behavioral it was falsified on a fixed build. Some entries
drive the attack through a `process` step (a helper script) because the
protocol needs many round trips; their catalog family is the protocol they
exercise.

| id | family | CVE | what it proves |
|---|---|---|---|
| `openssl/heartbleed-mem-leak` | tls | CVE-2014-0160 | TLS heartbeat over-read on OpenSSL 1.0.1f, falsified on 1.0.1g |
| `log4j/jndi-rce` | http | CVE-2021-44228 | JNDI lookup RCE via header, OOB LDAP bind assert |
| `tpl/jinja2-ssti-rce` | http | - | Jinja2 SSTI to RCE to flag read |
| `wp/batch-route-confusion-sqli-rce` | http | CVE-2026-63030, CVE-2026-60137 | REST batch route confusion to SQLi; needs a live WordPress lab |
| `keycloak/cve-2026-18963-reset-credentials-bypass` | http | CVE-2026-18963 | Keycloak reset-credentials email-gate bypass to account takeover; verified 26.7.1, falsified 26.7.2 |
| `nextjs/cve-2026-75604-rce` | http | CVE-2026-75604 | Next.js Windows cache-traversal to Server Action key disclosure and forged-action RCE; verified 16.2.11, falsified 16.3.3 |
| `https/cve-2021-42013-apache-rce-tls` | https | CVE-2021-42013, CVE-2021-41773 | Apache 2.4.49 path traversal to CGI RCE over TLS |
| `smtp/cve-2020-7247-opensmtpd-rce` | smtp | CVE-2020-7247 | OpenSMTPD 6.6.1p1 `MAIL FROM` command injection RCE, OOB wget |
| `ftp/cve-2011-2523-vsftpd-backdoor` | ftp | CVE-2011-2523 | vsftpd 2.3.4 backdoor root shell on port 6200 |
| `dns/cve-2015-5477-bind-tkey-dos` | dns | CVE-2015-5477 | BIND 9.9.7-P1 TKEY assertion crash |
| `snmp/cve-2018-18066-net-snmp-null-deref-dos` | snmp | CVE-2018-18066 | net-snmp 5.7.3 `snmp_oid_compare` NULL deref crash |
| `tcp/cve-2011-4971-memcached-dos` | tcp | CVE-2011-4971 | memcached negative length integer underflow crash |
| `ldap/cve-2015-6908-openldap-ber-dos` | ldap | CVE-2015-6908 | OpenLDAP 2.4.42 `ber_get_next` assertion crash |
| `ssh/cve-2018-15473-username-enum` | ssh (process) | CVE-2018-15473 | OpenSSH 7.7 username enumeration |
| `websocket/cve-2021-32640-ws-redos` | websocket | CVE-2021-32640 | ws 7.4.5 ReDoS stall |
| `smb/cve-2017-7494-rce` | smb (process) | CVE-2017-7494 | Samba 4.6.3 `is_known_pipename` arbitrary module load |
| `mysql/cve-2012-2122-auth-bypass` | mysql (process) | CVE-2012-2122 | MySQL 5.5.23 `check_scramble` auth bypass |
| `process/cve-2014-6271-shellshock` | process | CVE-2014-6271 | Bash 4.3 env function RCE |
| `file/cve-2016-3714-imagetragick` | file | CVE-2016-3714 | ImageMagick 6.8.9-9 MVG `url()` RCE |
| `telnet/cve-2011-4862-encryption-keyid-dos` | telnet | CVE-2011-4862 | inetutils 1.8 telnetd KEYID overflow crash |

## Contributing

Contribute new solution files -> this repository: open a pull request with
a verified `.txt` file. Solutions are authored by hand or by an LLM, then
verified against a vulnerable replica before they land. See
[CONTRIBUTING.md](CONTRIBUTING.md) for the standard, and [AGENTS.md](AGENTS.md)
for the format rules that apply when an LLM writes a solution.

## Scope and ethics

This repository contains working exploit logic in data form. Use it only
against systems you own or are explicitly authorized to test. Do not run
these solutions against live, third-party, or internet-reachable targets. The
authors are not responsible for misuse.

## License

Exploitmatic runtime is AGPLv3 (community use, copyleft). Solution corpus
is Apache 2.0 (open contributions). This repository is the corpus: every
file here is Apache-2.0. See [LICENSE](LICENSE).
