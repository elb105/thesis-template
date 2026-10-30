<!-- Replace every <placeholder> and delete this comment. -->

# <Thesis title>

| | |
|---|---|
| **Student** | <Name Surname> ([@github-user](https://github.com/github-user)) |
| **Tutor(s)** | <Name Surname>, <Name Surname> |
| **Degree** | <TFG / TFM> — <Degree name>, ETSI Telecomunicación, Universidad Politécnica de Madrid |
| **Lab** | B105 Electronic Systems Lab |
| **Year** | <academic year> |
| **Status** | 🟡 In progress <!-- 🟢 Defended on YYYY-MM-DD --> |
| **Builds on** | <link to the previous repository, or "—"> |
| **Report** | <link to the PDF in docs/ or to a read-only Overleaf link> |

## First steps (delete this section when done)

1. **Accept the invitations** GitHub emailed you: this repository and the `elb105` organization.
2. **Install Git and clone** this repository: [CONTRIBUTING.md → Setup](CONTRIBUTING.md#setup-once).
3. **Make your first commit**: check the details at the top of this README and write the abstract,
   then commit and push as in [Level 1](CONTRIBUTING.md#level-1-commit-and-push-to-develop-first-weeks).
4. **Read [This repository will be public](#this-repository-will-be-public)** before you add any file.
5. **Continuing someone's project?** Put their repository in *Builds on*, read their `docs/handover.md`
   and spend your first week getting it running. Other theses are at
   [github.com/orgs/elb105/repositories?q=topic:thesis](https://github.com/orgs/elb105/repositories?q=topic:thesis).
6. **Start your [handover sheet](docs/handover.md)**: fill in the header now and update it after every meeting with your tutor.
7. **Optional**: open [`notes/`](notes/) in Obsidian for meeting notes, a logbook and literature notes.
   Designing a board? Read [`hardware/README.md`](hardware/README.md) first.

Stuck? Ask your tutor or another rookie.

## Abstract

<One paragraph: problem, what you built, main result.>

## Repository map

| Folder | Contents |
|---|---|
| [`src/`](src/) | Firmware and software |
| [`hardware/`](hardware/) | Hardware releases: design files, PDF schematics, Gerbers, BOM, 3D views |
| [`docs/`](docs/) | Report, figures, presentation, [handover sheet](docs/handover.md) |
| [`notes/`](notes/) | Optional notes: meetings, logbook, literature (Obsidian vault) |

New to Git? Start with [CONTRIBUTING.md](CONTRIBUTING.md).
Tutors: repository setup, rulesets and publication steps are in [docs/supervisor-setup.md](docs/supervisor-setup.md).

## This repository will be public

At the defense this repository becomes public, **including its full history**. Never commit:

- passwords, API keys, tokens or Wi-Fi credentials (keep them in a git-ignored `.env` or `secrets.h`);
- unpublished project data or results that belong to someone else;
- datasheets or documents under NDA, or copyrighted papers (link to them instead);
- personal data, yours or anyone else's;
- candid comments about people: keep notes factual.

Git keeps every version: deleting a file later does **not** remove it from the history.
If something slips in, tell your tutor straight away.

## How to cite

See [CITATION.cff](CITATION.cff), or "Cite this repository" in the GitHub sidebar.

## License

To be chosen before publication. Code, hardware and documentation may use different licences.
