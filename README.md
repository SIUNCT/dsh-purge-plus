<p align="center">
  <img src="docs/banner.svg" alt="dsh-purge" width="720">
</p>

<h1 align="center">⚔️ DeepSeek Harness cybersecurity red-team tool (dsh-purge)</h1>

<p align="center"><strong>Version 1.4.4 · fork</strong> · targets DSH <strong>0.2.1-rc.2</strong></p>

> **This is a fork, not upstream.** Original project: [YuJunZhiXue/dsh-purge](https://github.com/YuJunZhiXue/dsh-purge) by **小杨** ([@YuJunZhiXue](https://github.com/YuJunZhiXue)), MIT licence (`Copyright (c) 2026 小杨`, kept verbatim here).
> This branch is maintained by **[@JeffKeeping](https://github.com/JeffKeeping)** and only adds **0.2.1-rc.2 compatibility, a stronger prompt kernel, a rebuilt UI, and one patch-anchor fix**. It does not change what the plugin does.
> What changed against the previous release (1.2.0), item by item: **[CHANGELOG.md](CHANGELOG.md)**.
> 1.4.0 borrows the approval tier, audit trail, strict JSON boundary and revision guard from [Minglink/dsh-infinite-gen-5](https://github.com/Minglink/dsh-infinite-gen-5); scope and boundaries in [NOTICE.md](NOTICE.md).
> **1.4.1 fixes the unknown prompt variable {{cwd}} crash on install**: 0.2.1 registers provider/model only, so this plugin now registers cwd itself and stops emitting the variable from config.
> **1.4.2 removes the input-box rewind button and all of its machinery** (backend routes, `/rewind` command, 3 patches): 82 patches -> 79.
> **1.4.3 restores steer delivery**: reverts the two 1.4.2 patches that forced every prompt to followup (#85/#86) and adds #90, which latches wakes during the running phase so turn-tail messages are no longer stranded.
> **1.4.4 fixes two silent failures**: `hostFs` now exposes the fd-based sync methods (without them no `.bak` backup could be created and CLI shims were misreported as `binary`), and `repairOfficialCliAfterUnpack` now awaits (an unhandled rejection used to terminate the host).
> Changes and attribution: [NOTICE.md](NOTICE.md). Preconditions, no-warranty and legal terms: [DISCLAIMER.md](DISCLAIMER.md).
> The upstream **non-commercial** term carries over: no paid resale, no gray- or black-market profit.

<p align="center">
  <em>DeepSeek Harness jailbreak: every model, swap prompts per model. Default prompt for 小码酱. Please star ⭐ If you build on this, see Attribution required.</em>
</p>

<p align="center">
  <a href="https://github.com/YuJunZhiXue/dsh-purge/stargazers"><img src="https://img.shields.io/github/stars/YuJunZhiXue/dsh-purge?logo=github&label=Stars" alt="GitHub stars"></a>
  <a href="https://github.com/YuJunZhiXue/dsh-purge/blob/master/LICENSE"><img src="https://img.shields.io/badge/license-MIT-65a30d?style=flat" alt="MIT license"></a>
  <a href="https://awesome-dsh-plugin.com"><img src="https://awesome-dsh-plugin.com/badge.svg" alt="awesome · DSH plugin"></a>
  <a href="https://deepseek.stream/plugins/dsh-purge"><img src="https://img.shields.io/badge/Hub-dsh--purge-1a73e8" alt="DeepSeek Harness Hub"></a>
  <a href="https://www.deepseek.com/harness/"><img src="https://img.shields.io/badge/dsh-0.2.1--rc.2-blue" alt="DSH"></a>
  <br>
  <img src="https://img.shields.io/badge/Node.js-%3E%3D18-339933?logo=node.js&logoColor=fff" alt="Node.js">
  <img src="https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=000" alt="JavaScript">
</p>

<p align="center">
  <a href="https://dsh-plugin.org/plugins/yujunzhixue/dsh-purge"><img src="https://dsh-plugin.org/badges/listed.svg" alt="Listed on dsh-plugin.org"></a>
</p>

<p align="center">
  <a href="README.md">English</a> | <a href="README.zh-CN.md">中文</a>
</p>

> **Upstream status.** The original author has paused upstream development (no new releases, issues / PRs not taken). This fork is maintained only so the official 0.2.1-rc.2 build installs and its UI works; no long-term tracking is promised. When upstream resumes, prefer it.

> **Official Web and the official desktop app are both supported.** Official `dsh web` uses profile `web`. The official desktop EXE (DeepSeek Harness **0.2.1-rc.2**) uses profile `desktop`. Install and Apply them separately. This release targets dsh 0.2, including 0.2.1-rc.2. Older dsh builds need the matching older plugin. Community Desktop is not maintained.
>
> Adding the plugin to a profile does not patch the host. Open **Settings → Purge** → **Apply**. A successful Apply restarts; an unfinished Apply does not. After the restart, start a new chat - that is when the inject enters the session.
>
> Node.js **≥18**. Windows no-flash needs **Node ≥22**. Hub one-click and the commands below both install the npm package `dsh-purge`.


## What this is

**dsh-purge** is a cybersecurity red-team plugin for the official [DeepSeek Harness](https://www.deepseek.com/harness/) already installed on your machine. Official **Web** (`dsh web`) and the official **desktop EXE** are both supported. Install and Apply them separately.

Harness does more than forward a prompt. It rewrites prompts, approval gates, sandbox policy, and session migration at runtime. Asking only “will the model answer” treats a host-layer block as a model refusal. This plugin separates those two layers on your own install, for controlled evaluation and for seeing which default policy is too tight.

| You get | What it does |
|---|---|
| **Settings → Purge** | The only surface: last row of the settings rail, with patches, prompt, rule sets, skills, and the drill console |
| **Clean** | Grouped patch status, Apply / Restore / Uninstall, prompt editor, multiple rule sets |
| **Drill** | Built into the stable release. After authorization: assets, skills, and the local environment. Only for a host you manage, an offline target, or a written authorized exercise |
| **Host policy** | Default copy, permission policy, and tool limits. Official capabilities stay. No second invented identity |
| **On start** | Checks and reapplies. After npm overwrites `node_modules`, you do not hand-edit files |

No hardcoded drive letters. It looks at `$DSH_HOME`, `.dsh` next to the dsh launcher, then `~/.dsh`. It does not patch the Harness source tree. **Apply** on the **Clean** page of **dsh-purge** is what writes the changes. Identity comes from the encrypted prompt shipped in the plugin, the text the prompt box shows by default. The host prompt file is not read.

It only touches the official `@deepseek-ai` packages and local config on the user's machine. It is not a public scanner and not an attack kit for third-party sites. The repo does not ship malware, unauthorized-exploit scripts, or payloads aimed at the public internet.

---

## Non-profit public project. Commercial sale, paid resale, and profit from illegal or gray-market activity are forbidden. For technical reference only.

## Attribution required

**If you borrow this project's name, ideas, code, or prompts, you must credit the author and name the source repository:** [YuJunZhiXue/dsh-purge](https://github.com/YuJunZhiXue/dsh-purge).

Using them without attribution, hiding the source, or passing them off as your own will be pursued.

---
