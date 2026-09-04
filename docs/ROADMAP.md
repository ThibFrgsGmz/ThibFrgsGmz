# Profile repository roadmap

This repository powers the GitHub profile shown at
[github.com/ThibFrgsGmz](https://github.com/ThibFrgsGmz). It has one product
surface (`README.md`) and one moving part (the snake-animation workflow).

The work is tracked as small, self-contained increments. Each increment should
leave the profile in a working, publishable state.

---

## Increment 1 — Repair the rendering and the automation ✅

Everything in this increment was a defect, not a preference.

**Automation**

- `.github/workflows/main.yml` triggered on pushes to `master`, but the
  repository's default branch is `main`. The push trigger had therefore never
  fired. Now set to `main`.

**README rendering**

- `<img ... width, height="25px">` — a malformed attribute (`width,`) in the
  header image. Split into valid `width` / `height` attributes.
- The stats block opened `<a>` and `<div>` but closed with `</p>`, leaving the
  anchor unclosed and a stray paragraph tag. Now correctly nested.
- The Udemy certificates block used `<tr>` / `<td>` with no enclosing
  `<table>`, so GitHub's sanitizer dropped the row and the cells rendered as
  loose text. Wrapped in `<table>`.
- Badge text read `CONINUOUS LEARNING`. Corrected to `CONTINUOUS`.

**Accessibility**

- Fourteen technology icons carried `alt="Rafa-*"` — alt text left over from
  the profile template this README was adapted from. Replaced with the actual
  technology names.
- Added `alt` text to the three statistics cards.

**Third-party endpoint**

- The streak card pointed at `github-readme-streak-stats.herokuapp.com`. That
  host went away when Heroku retired its free dynos in November 2022; the
  project's own hosted instance is now `streak-stats.demolab.com`. Updated.
  *Not verified from CI — this sandbox's egress proxy only permits GitHub
  hosts. Confirm the card renders once this reaches `main`.*

---

## Increment 2 — Restore and harden the snake animation ▶ next

The `output` branch was last written on **2022-04-16**, and the workflow has
**no runs at all** in its history. The animation embedded in the README has
been frozen for over four years.

Two causes, one fixed and one still open:

1. The dead `master` push trigger — fixed in increment 1.
2. GitHub suspends `schedule:` triggers in public repositories after 60 days
   without repository activity. `main` had been untouched since 2022-07-17, so
   the six-hourly cron was suspended.

Merging increment 1 counts as activity and should re-arm the schedule. Then:

- [ ] Confirm a run appears under Actions and that `output` gets a fresh
      commit. If the schedule stays suspended, re-enable it from the Actions
      tab.
- [ ] Replace `Platane/snk@master` with a released tag. A floating `master`
      ref is both unstable and a supply-chain risk. **Check the action's
      current input names before switching** — the v3 line replaced
      `svg_out_path` with a multi-line `outputs:` input, so this is not a
      drop-in version bump. Deliberately left alone in increment 1 rather than
      changed unverified.
- [ ] Consider bumping `crazy-max/ghaction-github-pages@v2.5.0`, subject to the
      same check.
- [ ] Add a dark-mode variant of the animation and serve both through
      `<picture>` + `prefers-color-scheme`.

## Increment 3 — Content accuracy

- [ ] The technology row is unordered and mixes languages, tools and editors.
      Group it, and drop entries no longer worth advertising.
- [ ] `logos/` holds six SVGs (`C`, `docker`, `jenkins`, `jenkins_with_docker`,
      `python-5`, `travis-ci`) that nothing references. Either use them in
      place of the remote devicon URLs — which also removes a third-party
      request per icon — or remove the directory.
- [ ] The profile advertises space and embedded systems but links no project.
      Add a short "selected work" section.

## Increment 4 — Resilience

- [ ] Every visual element is a third-party request (shields.io,
      github-readme-stats on Vercel, komarev, giphy, devicon). Any outage
      renders the profile as broken-image icons. Self-host what is static.
- [ ] Add a scheduled link-checker workflow so dead endpoints surface as a
      failing run rather than being noticed by a visitor — the streak card in
      increment 1 was dead for years without anyone being alerted.

---

## Conventions

- One increment per branch, one topic per commit.
- Prefer a fix that is verifiable in the repository over one that depends on
  an external service being up.
- When a third-party endpoint cannot be reached from the working environment,
  say so in the change rather than asserting it works.
