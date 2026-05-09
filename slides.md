---
theme: default
title: GitHub Actions 
info: |
  ## GitHub Actions — a 5-minute introduction
  CI/CD, workflow anatomy, and limitations for informatics students.
class: text-center
transition: slide-left
mdc: true
duration: 5min
colorSchema: dark
fonts:
  sans: Inter
  mono: JetBrains Mono
---

# GitHub Actions


<div class="abs-bl m-6 text-sm opacity-60">
  Anna Romankiewicz, MC Bachelor
<br> 13.05.2026
</div>

<div class="abs-br m-6 text-xl">
  <carbon:logo-github />
</div>

<style>
:root {
  --slidev-theme-primary: #2da44e;
}
h1 {
  background: linear-gradient(90deg, #2da44e 0%, #8957e5 100%);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  font-weight: 800;
}
</style>

<!--
Welcome — five minutes, nine slides. We motivate (CI/CD), tease use cases, define the vocabulary, see the pieces connect, then walk a real YAML file. Close with the rough edges. Roughly 30 seconds per slide; the YAML walkthrough takes the most.
-->

---
transition: fade-out
---

# Why CI/CD?


- **Continuous Integration** 
- **Continuous Deployment / Delivery** 


<div class="flex justify-center mt-12">

```mermaid {scale: 1.1, theme: 'dark'}
flowchart LR
  A[Code] --> B[Build]
  B --> C[Test]
  C --> D[Deploy]
  D --> E[Monitor]
  E -.feedback.-> A
```

</div>

<!--
CI/CD is the loop you want around every codebase. CI = automatically build and test on every commit. CD = automatically ship the green builds. It eliminates the "works on my machine" excuse, prevents broken `main`, and replaces stressful manual deploys with boring automation. That loop is exactly what we'll automate. ~35 seconds.
-->

---

# What can you automate?

<br>
<br>

<div class="grid grid-cols-2 gap-4 mt-8">

<div class="p-3 rounded border border-green-500/30 bg-green-500/5">
  <div class="text-xl"><carbon:test-tool /></div>
  <div class="font-bold mt-1">Tests</div>
  <div class="text-xs opacity-70">On every Push and Pull Request</div>
</div>

<div class="p-3 rounded border border-green-500/30 bg-green-500/5">
  <div class="text-xl"><carbon:build-tool /></div>
  <div class="font-bold mt-1">Builds & Artifacts</div>
  <div class="text-xs opacity-70">Binaries, Bundles etc.</div>
</div>

<div class="p-3 rounded border border-purple-500/30 bg-purple-500/5">
  <div class="text-xl"><carbon:deploy /></div>
  <div class="font-bold mt-1">Deploys</div>
  <div class="text-xs opacity-70">GitHub Pages, AWS, other servers …</div>
</div>

<div class="p-3 rounded border border-purple-500/30 bg-purple-500/5">
  <div class="text-xl"><carbon:bot /></div>
  <div class="font-bold mt-1">Repo Automation</div>
  <div class="text-xs opacity-70">Label Pull Requests, Cron Jobs etc.</div>
</div>

</div>

<!--
Four buckets in 20 seconds: tests & linting on every PR, builds & artifacts (binaries, Docker images), deploys (Pages, cloud), and repo automation (label PRs, cron jobs, stale-issue bots). Anything you can do in a shell, you can do here.
-->

---

# Components of GitHub Actions Workflow
<br>
<br>

<div class="grid grid-cols-2 gap-4 mt-8">

<div class="p-3 rounded border border-green-500/30 bg-green-500/5">
  <div class="font-bold text-green-400">Event</div>
  <div class="text-xs opacity-80 mt-1">An activity that triggers a workflow run.</div>
  <div class="mt-2 text-xs"><code>on: [push, pull_request]</code></div>
</div>

<div class="p-3 rounded border border-green-500/30 bg-green-500/5">
  <div class="font-bold text-green-400">Workflow</div>
  <div class="text-xs opacity-80 mt-1">The YAML file in <code>.github/workflows/</code> defining what runs.</div>
  <div class="mt-2 text-xs"><code>name: CI</code></div>
</div>

<div class="p-3 rounded border border-purple-500/30 bg-purple-500/5">
  <div class="font-bold text-purple-400">Job</div>
  <div class="text-xs opacity-80 mt-1">A set of steps executed on the same runner.</div>
  <div class="mt-2 text-xs"><code>jobs: { test: {...} }</code></div>
</div>

<div class="p-3 rounded border border-purple-500/30 bg-purple-500/5">
  <div class="font-bold text-purple-400">Step</div>
  <div class="text-xs opacity-80 mt-1">One unit — a <code>run:</code> shell command or a <code>uses:</code> action.</div>
  <div class="mt-2 text-xs"><code>- run: npm test</code></div>
</div>

</div>

<!--
Four terms describe the *structure* of every workflow. An event triggers a workflow; a workflow groups jobs; jobs are sequences of steps; a step is either a shell command or a reusable action. Read these once — we'll see all four in YAML in two slides. ~35 seconds.
-->

---

# Components of GitHub Actions Workflow
<br>
<br>

<div class="grid grid-cols-3 gap-4 mt-8">

<div class="p-3 rounded border border-green-500/30 bg-green-500/5">
  <div class="font-bold text-green-400">Action</div>
  <div class="text-xs opacity-80 mt-1">Pre-built reusable code that a step can call.</div>
  <div class="mt-2 text-xs"><code>uses: actions/checkout@v4</code></div>
</div>

<div class="p-3 rounded border border-green-500/30 bg-green-500/5">
  <div class="font-bold text-green-400">Runner</div>
  <div class="text-xs opacity-80 mt-1">The VM (Linux / macOS / Windows) that executes a job. Self-hosted runners also possible.</div>
  <div class="mt-2 text-xs"><code>runs-on: ubuntu-latest</code></div>
</div>

<div class="p-3 rounded border border-purple-500/30 bg-purple-500/5">
  <div class="font-bold text-purple-400">Marketplace</div>
  <div class="text-xs opacity-80 mt-1">Where Actions live. Reference one with <code>uses:</code>.</div>
  <div class="mt-2 text-xs"><code>super-linter/super-linter@v6</code></div>
</div>

</div>

<div class="mt-6 p-3 rounded border border-amber-500/30 bg-amber-500/5">
  <div class="text-xs"><strong class="text-amber-400">Secrets</strong> &nbsp;—&nbsp; <code v-pre>${{ secrets.X }}</code> keeps tokens out of YAML; values come from repo settings, never from the file itself.</div>
</div>

<!--
Three more terms cover *where* it runs. Actions are reusable building blocks. Runners are the machines — GitHub gives you fresh Linux, macOS, or Windows VMs free for public repos; self-host if you need GPUs or your university's network. The Marketplace is the package registry where every `uses:` line resolves to community code. And secrets — never paste tokens, reference them by name. ~35 seconds.
-->

---

# Workflow Example
<br>

<div class="text-center text-xs opacity-60 -mt-2">An event triggers a workflow → jobs executed on independent runners </div>
<br>

<div class="relative w-full mt-2" style="min-height: 380px">

<div v-click.hide="1" class="absolute inset-0 flex justify-center">

```mermaid {scale: 0.8, theme: 'dark'}
flowchart LR
  E([Event]) --> Tj
  Dj -.needs.-> Tj
  subgraph WF[Workflow · .github/workflows/ci.yml]
    direction LR
    subgraph TR[Runner 1 · ubuntu-latest]
      direction TB
      Tj[Job: test]
      Tj --> Tc[Step 1: checkout]
      Tc --> Tn[Step 2: setup-node]
      Tn --> Tt[Step 3: npm test]
    end
    subgraph DR[Runner 2 · ubuntu-latest]
      direction TB
      Dj[Job: deploy]
      Dj --> Db[Step 1: build]
      Db --> Du[Step 2: upload]
    end
  end
  classDef evt fill:#8957e5,stroke:#8957e5,color:#fff;
  class E evt;
```

</div>

<div v-click="[1,2]" class="absolute inset-0 flex justify-center">

```mermaid {scale: 0.8, theme: 'dark'}
flowchart LR
  E([Event]) --> Tj
  Dj -.needs.-> Tj
  subgraph WF[Workflow · .github/workflows/ci.yml]
    direction LR
    subgraph TR[Runner 1 · ubuntu-latest]
      direction TB
      Tj[Job: test]
      Tj --> Tc[Step 1: checkout]
      Tc --> Tn[Step 2: setup-node]
      Tn --> Tt[Step 3: npm test]
    end
    subgraph DR[Runner 2 · ubuntu-latest]
      direction TB
      Dj[Job: deploy]
      Dj --> Db[Step 1: build]
      Db --> Du[Step 2: upload]
    end
  end
  classDef evt fill:#8957e5,stroke:#8957e5,color:#fff;
  classDef hl  fill:#2da44e,stroke:#2da44e,color:#fff;
  classDef dim fill:#1f2428,stroke:#30363d,color:#8b949e;
  class E evt;
  class Tj,Tc,Tn,Tt hl;
  class Dj,Db,Du dim;
```

</div>

<div v-click="2" class="absolute inset-0 flex justify-center">

```mermaid {scale: 0.8, theme: 'dark'}
flowchart LR
  E([Event]) --> Tj
  Dj -.needs.-> Tj
  subgraph WF[Workflow · .github/workflows/ci.yml]
    direction LR
    subgraph TR[Runner 1 · ubuntu-latest]
      direction TB
      Tj[Job: test]
      Tj --> Tc[Step 1: checkout]
      Tc --> Tn[Step 2: setup-node]
      Tn --> Tt[Step 3: npm test]
    end
    subgraph DR[Runner 2 · ubuntu-latest]
      direction TB
      Dj[Job: deploy]
      Dj --> Db[Step 1: build]
      Db --> Du[Step 2: upload]
    end
  end
  classDef evt  fill:#8957e5,stroke:#8957e5,color:#fff;
  classDef hl   fill:#2da44e,stroke:#2da44e,color:#fff;
  classDef done fill:#1a3d1f,stroke:#2da44e,color:#9be9a8;
  class E evt;
  class Tj,Tc,Tn,Tt done;
  class Dj,Db,Du hl;
```

</div>

</div>

<!--
The five terms in one picture. An event triggers a workflow, which spins up runners — each runner executes one job, a sequence of steps. Important: in this example the deploy job *needs* test, so they're NOT parallel. [click] First the test runner finishes. [click] Then the deploy runner starts. Drop the `needs:` and they'd run side-by-side. ~40 seconds.
-->

---
level: 2
---

# Workflow Definition

<br>

````md magic-move {lines: true}
```yaml
name: CI                       # workflow name shown in the Actions tab
on: [push, pull_request]       # events: any push, any PR
```

```yaml
name: CI                       # workflow name shown in the Actions tab
on: [push, pull_request]       # events: any push, any PR

jobs:
  test:                        # job ID (must be unique)
    runs-on: ubuntu-latest     # which runner OS to use
```

```yaml
name: CI                       # workflow name shown in the Actions tab
on: [push, pull_request]       # events: any push, any PR

jobs:
  test:                        # job ID (must be unique)
    runs-on: ubuntu-latest     # which runner OS to use
    steps:
      - uses: actions/checkout@v4   # action: clone the repo
```

```yaml
name: CI                       # workflow name shown in the Actions tab
on: [push, pull_request]       # events: any push, any PR

jobs:
  test:                        # job ID (must be unique)
    runs-on: ubuntu-latest     # which runner OS to use
    steps:
      - uses: actions/checkout@v4   # action: clone the repo
      - uses: actions/setup-node@v4 # action with parameters
        with:
          node-version: 20          # input passed to the action
```

```yaml
name: CI                       # workflow name shown in the Actions tab
on: [push, pull_request]       # events: any push, any PR

jobs:
  test:                        # job ID (must be unique)
    runs-on: ubuntu-latest     # which runner OS to use
    steps:
      - uses: actions/checkout@v4   # action: clone the repo
      - uses: actions/setup-node@v4 # action with parameters
        with:
          node-version: 20          # input passed to the action
      - run: npm ci                 # shell: install deps
      - run: npm test               # shell: run tests
```

```yaml
name: CI
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm ci
      - run: npm test

  deploy:                          # second job (same as the diagram)
    needs: test                    # wait for test to pass — that's the dotted arrow
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm run build         # build the artifact
      - uses: actions/upload-artifact@v4

```
````

<!--
The centerpiece. Comments do most of the work — narrate the additions:

1. `name` and `on:` — *when* does it run?
2. Add a job and `runs-on:` — *where* does it run?
3. Add `steps:` with `actions/checkout@v4` — first step almost always grabs your code.
4. Add `actions/setup-node@v4` with `with:` — parameterized action.
5. Add `run:` — your own shell commands.
6. Add a second job `deploy` with `needs: test` — that's the dotted "needs" arrow in the previous diagram. Without it, both jobs would run in parallel.

Push this to `.github/workflows/ci.yml` and the next PR runs it. ~90 seconds.
-->


---

# Limitations

<div class="limitations">
<br>

- 💸 **Minutes cost money on private repos** — 2000 free/month, then per-minute billing
- ⏱️ **Job Timeout** — max. 6 hours
- 🔒 **Reliance on GitHub** — workflows don't run outside GitHub
- 🐛 **Painful debugging** — no local step-through
- ⚠️ **Supply-chain risk** — `uses: some-action@v1` follows a moving tag
- 📚 **Learning Curve** — YAML syntax, familiar with CI/CD concepts

</div>

<style scoped>
.limitations li { margin: 0.6rem 0; }
</style>

<!--
Be honest about the trade-offs. Minutes — free for public repos, metered for private; at scale the bill shows up. Vendor lock-in — workflows are GitHub-specific; switching to GitLab or Jenkins means a rewrite. Debugging is the biggest daily frustration: no breakpoint, you commit-push-watch-repeat. The supply-chain point matters: `@v1` is a moving tag — for security-sensitive workflows pin to a commit SHA. (Worth knowing if there's time: YAML has no type checking, and every job pays a 10–30 s cold-start tax.) ~30 seconds.
-->

---
layout: center
class: text-center
---

# That's it.

When it shines: anything already on GitHub, especially open source.

<div class="mt-8 text-sm opacity-70">
  📚 <a href="https://docs.github.com/actions" target="_blank">docs.github.com/actions</a>
  ·
  🛒 <a href="https://github.com/marketplace?type=actions" target="_blank">Marketplace</a>
  ·
  🧪 <a href="https://github.com/nektos/act" target="_blank">nektos/act</a> (run locally)
</div>

<div class="mt-16 text-xs opacity-40">
  Questions?
</div>

<!--
Five minutes, one workflow file, and a sense of when GitHub Actions is the right tool: when your code already lives on GitHub, especially for open source where it's free. Three links to take home — official docs, the Marketplace, and `act` for running workflows locally. Open the floor.
-->
