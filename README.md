# PiesP community defaults

These files are intended for the public `PiesP/.github` repository.

`CONTRIBUTING.md` and `SUPPORT.md` provide default guidance when a PiesP
repository has no corresponding local file. A repository's own file takes
precedence. Local issue-template configurations do not combine with central
templates, so this repository does not include an `ISSUE_TEMPLATE` folder.

[`MAINTENANCE_POLICY.md`](MAINTENANCE_POLICY.md) records common operating
principles and the reasons for product exceptions. It is a reference, not a
settings sync or GitHub enforcement mechanism. Each project retains its own CI,
CODEOWNERS, Dependabot rules, formatter, security policy, release permissions,
Windows safety rules, and game compatibility checks.

The maintainer's read-only `check.py` inventory and detailed `policy.json` stay
in the local workspace. They are not part of this repository. Run the
inventory there to compare declared settings with the current GitHub state;
review any difference before an authorized settings change. The command does
not apply settings, dispatch workflows, publish releases, or change branches.
From that workspace, maintainers can use:

```bash
python3 .codex/maintenance/check.py --offline --output /tmp/maintenance-offline.json
python3 .codex/maintenance/check.py --output /tmp/maintenance-live.json
```

The live command reads GitHub through `gh api`; an unavailable query remains
unverified. Neither command is supplied by this repository.

GitHub's [default community file rules](https://docs.github.com/en/communities/setting-up-your-project-for-healthy-contributions/creating-a-default-community-health-file)
require a public `.github` repository and explain file precedence. These
defaults do not appear in individual repository clones.
