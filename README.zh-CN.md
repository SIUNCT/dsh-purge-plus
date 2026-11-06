<p align="center">
  <img src="docs/banner.svg" alt="dsh-purge" width="720">
</p>

<h1 align="center">⚔️ DeepSeek Harness 网络安全红队工具（dsh-purge）</h1>

<p align="center"><strong>Version 1.4.4 · 本分支 fork</strong> · 适配 DSH <strong>0.2.1-rc.2</strong></p>

> **这是 fork，不是上游。** 原项目：[YuJunZhiXue/dsh-purge](https://github.com/YuJunZhiXue/dsh-purge)，原作者 **小杨**（[@YuJunZhiXue](https://github.com/YuJunZhiXue)），MIT 许可证（`Copyright (c) 2026 小杨`，本仓库原样保留）。
> 本分支由 **[@JeffKeeping](https://github.com/JeffKeeping)** 维护，只做 **0.2.1-rc.2 适配 + 破甲内核升级 + 界面重做 + 一处补丁锚点修复**，不改变插件用途与能力范围。
> 逐条对比上一版（1.2.0）优化与新增了什么：**[CHANGELOG.md](CHANGELOG.md)**。
> 1.4.0 的审批档位 / 审计流水 / 严格 JSON / 修订号借鉴了 [Minglink/dsh-infinite-gen-5](https://github.com/Minglink/dsh-infinite-gen-5)，来源与边界见 [NOTICE.md](NOTICE.md)。
> **1.4.1 修掉「装完就报 unknown prompt variable {{cwd}}」**：0.2.1 只注册 provider / model，本插件已补注册 cwd 并不再让配置发变量。
> **1.4.2 移除输入框的「回退」按钮及其作用**（后端路由、`/rewind` 命令、3 条补丁一起删），补丁总数 82 → 79。
> **1.4.3 恢复「插话发送」**：回滚 1.4.2 里把 prompt() 一律改成 followup 的两个补丁（#85/#86），并新增 #90 真正闩锁 running 态的唤醒，turn 收尾窗口的消息不再滞留。
> **1.4.4 修两处静默失效**：`hostFs` 补上 fd 型同步方法（否则 `.bak` 备份一个都建不出、CLI shim 被误报 `binary`），`repairOfficialCliAfterUnpack` 补 `await`（否则未处理拒绝直接终止宿主）。
> 变更清单与署名要求见 [NOTICE.md](NOTICE.md)；使用前提、无担保与法律声明见 [DISCLAIMER.md](DISCLAIMER.md)。
> 上游的**非商用**条款沿用：禁止商业售卖、付费倒卖与黑灰产牟利。

<p align="center">
  <em>DeepSeek Harness 破甲：让所有模型都能破甲，不同模型可换不同提示词；默认提示词面向国模「小码酱」。Jailbreak for every model — swap prompts per model. 求 Star 收藏 ⭐ 如要二开，请查看署名要求部分</em>
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

> **上游维护状态。** 原作者因工作安排暂停更新上游（不再发版、暂不收 issue / PR）。本分支只为「官方 0.2.1-rc.2 能装上、界面能用」而维护，不承诺长期跟进；上游恢复后请优先回到上游。

> **官方 Web 和官方桌面端都支持。** 官方 `dsh web` 用 profile `web`；官方桌面 EXE（DeepSeek Harness **0.2.1-rc.2**）用 profile `desktop`。两边分开安装、分开点「应用」。当前版本适配 dsh 0.2（含 0.2.1-rc.2）；更早的 dsh 用对应的老版本插件。社区桌面端不维护。
>
> 写进 profile 还不会改宿主。打开 **设置 → Purge** → **应用**。成功会自动重启；补丁没打完不重启。重启后新开一轮对话，注入才进当前会话。
>
> Node.js **≥18**。Windows 无闪窗需要 **Node ≥22**。插件市场一键安装和下面的命令都走 npm 包 `dsh-purge`。



## 介绍

**dsh-purge** 是装在本机官方 [DeepSeek Harness](https://www.deepseek.com/harness/) 上的网络安全红队评测插件。官方 **Web**（`dsh web`）和官方 **桌面端**（EXE）都支持，两边分开装、分开应用。

Harness 不只是把问题交给模型。它还会在运行时改提示词、审批门、沙箱和会话格式。只看「模型答不答」，会把宿主拦截误当成模型拒绝。这个插件在你自己的安装里把这两层分开，用来做受控评测，也用来看默认策略该不该收紧。


| 你会用到             | 它做什么                                            |
| ---------------- | ----------------------------------------------- |
| **设置 → Purge**   | 唯一入口。设置左侧导航最后一行，面板含补丁 / 提示词 / 规则 / 技能 / 演练台 |
| **清洗**           | 分组查看补丁，应用、还原、卸载；编辑提示词和多套规则                      |
| **演练台**          | 正式版内置。授权后查看资产、技能和本机环境。只用于你有权管理的本机、离线靶标或已书面授权的演练 |
| **宿主策略**         | 调整默认文案、权限策略和工具上限。官方能力保留，不另写一套身份                 |
| **启动时**          | 自动再检查并应用。npm 升级盖掉 `node_modules` 之后不用手改文件       |


不写死盘符。按 `$DSH_HOME`、dsh 启动器旁边的 `.dsh`，再退回 `~/.dsh`。不改 Harness 源码仓库，在 **设置 → Purge** 里点「应用」才写入。身份来自插件里的加密提示词，也就是提示词框默认显示的那一份。不读取宿主磁盘上的提示词文件。

本插件只处理使用者本机已安装的官方 `@deepseek-ai` 包和本机配置。它不是公网扫描器，也不是针对第三方站点的攻击套件。仓库内不含木马、未授权渗透脚本或对外攻击载荷。

---

## 📌 非盈利公益项目，严禁任何主体用于商业售卖、付费倒卖或黑灰产牟利，仅供技术参考。

## 署名要求

**借用本项目的名称、思路、代码或提示词，必须署名，并写明来源仓库：** [YuJunZhiXue/dsh-purge](https://github.com/YuJunZhiXue/dsh-purge)。

不署名、隐瞒来源、改头换面据为己有，作者将依法追究责任。

---


```



---

## 还原

- 每个目标文件在应用前备份为 `<文件>.dshpurge.bak`。
- 「还原」或 `/purge revert` 用备份覆盖回去并删除备份；没有备份时去掉 shim 里由本插件写入的行。
- `prompt-inject.md` 是用户文件，还原时保留。
- 「卸载」会先还原（若已应用），再删除注入文件、规则库和插件本身。
- 重复应用是幂等的。

---

## 路径探测

宿主面先判断 `web` / `desktop`（预留 `gui` / `tui`，尚未单独适配时回退 web）。

**Web：**

1. `DSH_HOME` / `DSH_BASE`
2. dsh 启动器旁的 `.dsh`
3. `npm prefix -g` / `npm root -g`
4. 嵌套 `@deepseek-ai/dsh/node_modules/@deepseek-ai`
5. 系统默认 `~/.dsh`

**官方桌面：** 只改当前正在运行的官方桌面安装。点「应用」成功后会自动重启一次。社区桌面端不维护。

找不到目标时提示设置 `DSH_BASE`，不改文件。

---

## 更新

每一版改了什么、安装包在 [Releases](https://github.com/YuJunZhiXue/dsh-purge/releases)。发新版时把 `package.json` 的版本号改掉，中英文说明写进 `release-notes.md`，推到 `master` 就会自动打包。同一版本再推送不会重复发包。

## 说明

- 改动范围是本机 `@deepseek-ai/*` 包里的渲染文案、默认策略和执行逻辑，以及用户目录下的覆盖文件与规则集。
- 升级后原文对不上会显示跳过，这次应用仍算完成，不会乱改。
- 不改动非 `@deepseek-ai` 的第三方插件源仓库（启动时的 CMD 无感会**尽力**修补已装的 doctor / market / 梁神 / mnemon，属运行时补丁）。
- 安装走 npm 包名 `dsh-purge`：`dsh plugin --profile web add dsh-purge` 或 `dsh plugin --profile desktop add dsh-purge`。当前目录已是本仓库时 `add .`。registry 上不去再加 master.tar.gz。插件市场一键安装走同一个 npm 包。

---
