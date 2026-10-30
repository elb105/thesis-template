# Supervisor setup

For the tutor, not the student. Commands use the [GitHub CLI](https://cli.github.com) (`gh auth login` once) and are bash:
in Git Bash, replace the example values and paste each block whole. Section 3 also has a PowerShell version;
section 4 needs Git Bash.

## 1. Naming convention

`<type>-<startyear>-<b105login>-<topic>`, e.g. `tfg-2026-jgarcia-ble-glucose-monitor`, `tfm-2027-mlopez-lora-mesh`.

| Field | Rule |
|---|---|
| `type` | `tfg` or `tfm` (`phd` if ever needed). Groups the repositories in the org list. |
| `startyear` | Calendar year the repository is created. Known on day one, unaffected if the defense slips, no "2025/26" ambiguity. |
| `b105login` | The student's B105 lab login, lowercase. Already ASCII and unique: no accents, compound surnames or collisions to handle. Drop any `-` or `.` so `-` only separates fields. |
| `topic` | 2–4 English words in kebab-case. Whole name ≤ 50 characters. |

GitHub topics: `tfg` or `tfm`, plus `b105`, `teaching`, `thesis`. Filter with `org:elb105 topic:tfm`.
The template itself has `template`, `teaching`, `b105`.

## 2. One-time: publish the template

From this folder, on `main`:

```bash
gh repo create elb105/thesis-template --public --source . --push --description "Template for B105 TFG/TFM repositories"
gh repo edit elb105/thesis-template --template --add-topic template,teaching,b105
```

## 3. New student repository

The commands fill in the README and `CITATION.cff` placeholders with the values below (one commit per file on `main`).
The template has only `main`; `develop` is then created from it, so both branches share one history.

First create the variables 
```bash
REPO=tfm-2026-jgarcia-lora-mesh
TYPE=tfm
STUDENT=github-username
TITLE="Thesis title"
NAME="Juan"
SURNAME="García López"
TUTORS="Name Surname, Name Surname"
DEGREE="Máster Universitario en Ingeniería de Telecomunicación"
YEAR="2026-2027"
```
then create the repo
```bash
gh repo create elb105/$REPO --private --template elb105/thesis-template --description "${TYPE^^}: $TITLE. B105 Electronic Systems Lab, Information Processing and Telecommunications Center, Universidad Politécnica de Madrid"
# GitHub needs a few seconds to copy the template: this waits until main exists.
until SHA=$(gh api repos/elb105/$REPO/commits/main --jq .sha 2>/dev/null); do sleep 2; done
[ $TYPE = tfg ] && KIND="Bachelor's thesis (TFG)" || KIND="Master's thesis (TFM)"
for FILE in README.md CITATION.cff; do
  TEXT=$(gh api repos/elb105/$REPO/contents/$FILE --jq .content | base64 -d)
  TEXT=${TEXT//"<Thesis title>"/"$TITLE"}
  TEXT=${TEXT//"<Name Surname> ([@github-user](https://github.com/github-user))"/"$NAME $SURNAME ([@$STUDENT](https://github.com/$STUDENT))"}
  TEXT=${TEXT//"<Name Surname>, <Name Surname>"/"$TUTORS"}
  TEXT=${TEXT//"<TFG / TFM>"/"${TYPE^^}"}
  TEXT=${TEXT//"<Degree name>"/"$DEGREE"}
  TEXT=${TEXT//"<academic year>"/"$YEAR"}
  TEXT=${TEXT//"<Name>"/"$NAME"}
  TEXT=${TEXT//"<Surname(s)>"/"$SURNAME"}
  TEXT=${TEXT//"<repo>"/"$REPO"}
  TEXT=${TEXT//"<Bachelor's thesis (TFG) | Master's thesis (TFM)>"/"$KIND"}
  BLOB=$(gh api repos/elb105/$REPO/contents/$FILE --jq .sha)
  SHA=$(gh api -X PUT repos/elb105/$REPO/contents/$FILE -f message="Fill in $FILE" -f sha=$BLOB -f content="$(base64 -w0 <<<"$TEXT")" --jq .commit.sha)
done
gh api repos/elb105/$REPO/git/refs -f ref=refs/heads/develop -f sha=$SHA
gh repo edit elb105/$REPO --default-branch develop --add-topic $TYPE,b105,teaching,thesis
gh api -X PUT repos/elb105/$REPO/collaborators/$STUDENT -f permission=push
# Rookies team: the student joins it, and every rookie can read (not change) this repository.
gh api -X PUT orgs/elb105/teams/b105-rookies/memberships/$STUDENT -f role=member
gh api -X PUT orgs/elb105/teams/b105-rookies/repos/elb105/$REPO -f permission=pull
```

PowerShell: variables need `$`, and the topic list needs quotes (unquoted commas split it into separate arguments).
Run the lines in the same window, since the variables only live there.
First set the variables:
```powershell
$REPO = "tfm-2026-jgarcia-lora-mesh"; $TYPE = "tfm"; $STUDENT = "github-username"; $TITLE = "Thesis title"
$NAME = "Juan"; $SURNAME = "García López"; $TUTORS = "Name Surname, Name Surname"
$DEGREE = "Máster Universitario en Ingeniería de Telecomunicación"; $YEAR = "2026-2027"
```
Then create the repo
```powershell
gh repo create elb105/$REPO --private --template elb105/thesis-template --description "$($TYPE.ToUpper()): $TITLE. B105 Electronic Systems Lab, Information Processing and Telecommunications Center, Universidad Politécnica de Madrid"
do { Start-Sleep 2; $SHA = gh api repos/elb105/$REPO/commits/main --jq .sha 2>$null } until ($LASTEXITCODE -eq 0)
$KIND = if ($TYPE -eq "tfg") { "Bachelor's thesis (TFG)" } else { "Master's thesis (TFM)" }
foreach ($FILE in "README.md", "CITATION.cff") {
  $TEXT = [Text.Encoding]::UTF8.GetString([Convert]::FromBase64String((gh api repos/elb105/$REPO/contents/$FILE --jq .content) -join ""))
  $TEXT = $TEXT.Replace("<Thesis title>", $TITLE)
  $TEXT = $TEXT.Replace("<Name Surname> ([@github-user](https://github.com/github-user))", "$NAME $SURNAME ([@$STUDENT](https://github.com/$STUDENT))")
  $TEXT = $TEXT.Replace("<Name Surname>, <Name Surname>", $TUTORS)
  $TEXT = $TEXT.Replace("<TFG / TFM>", $TYPE.ToUpper())
  $TEXT = $TEXT.Replace("<Degree name>", $DEGREE)
  $TEXT = $TEXT.Replace("<academic year>", $YEAR)
  $TEXT = $TEXT.Replace("<Name>", $NAME)
  $TEXT = $TEXT.Replace("<Surname(s)>", $SURNAME)
  $TEXT = $TEXT.Replace("<repo>", $REPO)
  $TEXT = $TEXT.Replace("<Bachelor's thesis (TFG) | Master's thesis (TFM)>", $KIND)
  $BLOB = gh api repos/elb105/$REPO/contents/$FILE --jq .sha
  $SHA = gh api -X PUT repos/elb105/$REPO/contents/$FILE -f "message=Fill in $FILE" -f "sha=$BLOB" -f "content=$([Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes($TEXT)))" --jq .commit.sha
}
gh api repos/elb105/$REPO/git/refs -f ref=refs/heads/develop -f sha=$SHA
gh repo edit elb105/$REPO --default-branch develop --add-topic "$TYPE,b105,teaching,thesis"
gh api -X PUT repos/elb105/$REPO/collaborators/$STUDENT -f permission=push
gh api -X PUT orgs/elb105/teams/b105-rookies/memberships/$STUDENT -f role=member
gh api -X PUT orgs/elb105/teams/b105-rookies/repos/elb105/$REPO -f permission=pull
```

The `b105-rookies` team has read access to every thesis repository, so rookies can follow a previous student's
`docs/handover.md` and test each other's before the defense. The student keeps write access to their own repository.
If the student isn't an `elb105` member yet, joining the team sends them an invitation to the organization.
When a student leaves the lab, remove them from the team.

## 4. When the student is ready (Level 2)

Until now the student can push anything to any branch (Level 1). These two GitHub **rulesets** switch on Level 2:

| Branch | After this step |
|---|---|
| `develop` | No direct pushes: changes arrive through a pull request, which the student can merge without approval. |
| `main` | No direct pushes: pull requests need **your** approval before they can be merged. |
| both | Can't be deleted or force-pushed (protects the history). |

You (repo and org admin) can bypass both rules, so you are never locked out.

In Git Bash, set the repository name, then paste each block whole, from `gh api` to `EOF`.
Each block sends one rule definition (the JSON) to GitHub; the two differ only in the branch and the number of approvals.

```bash
REPO=tfm-2026-jgarcia-lora-mesh
```

**Rule 1: `develop` needs a pull request (0 approvals)**

```bash
gh api -X POST repos/elb105/$REPO/rulesets --input - <<'EOF'
{
  "name": "develop: pull request required",
  "target": "branch",
  "enforcement": "active",
  "conditions": {"ref_name": {"include": ["refs/heads/develop"], "exclude": []}},
  "rules": [
    {"type": "pull_request", "parameters": {
      "required_approving_review_count": 0,
      "dismiss_stale_reviews_on_push": false,
      "require_code_owner_review": false,
      "require_last_push_approval": false,
      "required_review_thread_resolution": false}},
    {"type": "deletion"},
    {"type": "non_fast_forward"}
  ],
  "bypass_actors": [
    {"actor_id": 5, "actor_type": "RepositoryRole", "bypass_mode": "always"},
    {"actor_id": 1, "actor_type": "OrganizationAdmin", "bypass_mode": "always"}
  ]
}
EOF
```

**Rule 2: `main` needs a pull request with 1 approval**

```bash
gh api -X POST repos/elb105/$REPO/rulesets --input - <<'EOF'
{
  "name": "main: pull request and approval required",
  "target": "branch",
  "enforcement": "active",
  "conditions": {"ref_name": {"include": ["refs/heads/main"], "exclude": []}},
  "rules": [
    {"type": "pull_request", "parameters": {
      "required_approving_review_count": 1,
      "dismiss_stale_reviews_on_push": false,
      "require_code_owner_review": false,
      "require_last_push_approval": false,
      "required_review_thread_resolution": false}},
    {"type": "deletion"},
    {"type": "non_fast_forward"}
  ],
  "bypass_actors": [
    {"actor_id": 5, "actor_type": "RepositoryRole", "bypass_mode": "always"},
    {"actor_id": 1, "actor_type": "OrganizationAdmin", "bypass_mode": "always"}
  ]
}
EOF
```

**Check or undo:** on github.com, the repository's Settings → Rules → Rulesets shows both rules; you can switch them off
or delete them there.

## 5. Licence options

Chosen at publication, per part. Root `LICENSE` holds the code licence (GitHub detects it), `hardware/LICENSE` the
hardware one, and the README "License" section lists all of them.

| Part | Permissive | Weak copyleft | Strong copyleft |
|---|---|---|---|
| Code / firmware | MIT, Apache-2.0 (adds a patent grant) | LGPL-3.0, MPL-2.0 (libraries, drivers) | GPL-3.0 |
| Hardware releases | CERN-OHL-P-2.0 | CERN-OHL-W-2.0 | CERN-OHL-S-2.0 |
| Docs, notes, figures | CC BY 4.0 | | CC BY-SA 4.0 |

- Default suggestion: MIT + CERN-OHL-P-2.0 + CC BY 4.0. Choose the copyleft column if derivatives must stay open.
- The report PDF deposited in Archivo Digital UPM usually carries CC BY-NC-ND 4.0; the copy here can match it.
- Each hardware release includes its Altium design files, so CERN-OHL applies in full: with `-W` or `-S`, anyone
  who distributes a modified board must publish their modified sources.
- Get a licence text: `gh api licenses/mit --jq .body > LICENSE` (also `apache-2.0`, `gpl-3.0`, `lgpl-3.0`, `mpl-2.0`, `cc-by-4.0`, `cc-by-sa-4.0`).
  CERN-OHL texts: <https://spdx.org/licenses/CERN-OHL-P-2.0.html> (and `-W-`, `-S-`).
- A thesis inside a funded or industrial project may have IP terms that override all of this.

## 6. Pre-publication checklist

- [ ] **Secrets**: `gitleaks git -v` from the repository root scans the full history ([gitleaks](https://github.com/gitleaks/gitleaks), `winget install gitleaks`).
- [ ] **Large files**: list the 20 biggest files in the history:
  ```bash
  git rev-list --objects --all | git cat-file --batch-check='%(objecttype) %(objectsize) %(rest)' | awk '$1=="blob"' | sort -k2 -n | tail -20
  ```
- [ ] **Third-party files**: no datasheets, NDA documents, paywalled papers, or vendor code with an incompatible licence.
  Check the hardware `-source.zip` files too: component libraries and 3D models must allow redistribution.
- [ ] **Notes**: read `notes/` (meetings especially): factual, no personal data, no candid comments about people.
- [ ] **Links**: Overleaf links are read-only.
- [ ] **Handover**: `docs/handover.md` complete, tested by another rookie, no personal contact data.
- [ ] **README** complete: title, abstract, status "Defended", report link.
- [ ] **CITATION.cff** complete with `license:` set; GitHub shows "Cite this repository" without errors.
- [ ] **Licence** chosen (section 5) and IP terms allow publication.
- [ ] **Final tag**: `develop` merged into `main`, `v1.0-defense` tagged on `main` and pushed.

If something sensitive is in the history: treat secrets as leaked and rotate them first. Then either rewrite the
history with [git filter-repo](https://github.com/newren/git-filter-repo) and force-push, or (simpler) publish a new
repository with a single commit of the final state.

## 7. Publish at the defense

```bash
gh repo edit elb105/$REPO --visibility public --accept-visibility-change-consequences
```

## 8. Zenodo DOI (optional, before archiving)

Zenodo archives a GitHub release and gives it a citable DOI. Zenodo records are permanent: everything in the release
(notes included) cannot be withdrawn later. The official thesis deposit is still Archivo Digital UPM.

1. Once for the lab: an org owner approves the Zenodo app for `elb105` (Organization settings → Third-party access).
   Optionally create a "B105" community on Zenodo to collect all the theses.
2. On zenodo.org → Account → GitHub, switch on `elb105/$REPO` (the repository must already be public).
3. Create a release from the final tag. A plain tag is not enough:
   ```bash
   gh release create v1.0-defense --repo elb105/$REPO --verify-tag --title "<Thesis title>" --notes "Version defended on YYYY-MM-DD."
   ```
4. Zenodo takes its metadata from `CITATION.cff`, so `license:` must be set. When the DOI appears, add it as `doi:` in
   `CITATION.cff` and as a badge in the README, and commit to `main`.

## 9. Archive after the defense

```bash
gh repo archive elb105/$REPO --yes
```

Makes the repository read-only: no pushes, issues, pull requests or comments, and a banner says it is archived.
It stays visible, clonable and forkable, and `gh repo unarchive` reverses it. Archived repositories cannot create
releases, so do the Zenodo step first.
