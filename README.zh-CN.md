# Lecture-Grounded Tutorial Explainer

[English](README.md) | [简体中文](README.zh-CN.md)

一个可复用的 ChatGPT Skill，用用户自己的 Lecture / 课件作为主要依据来讲解 Tutorial、Worksheet 和 Problem Sheet。它强调**直接展示相关课件图、先讲概念再做题、逐步推导，并在每道题后提炼可复用的方法**。

## 为什么做这个 Skill

常见的 Problem Sheet 讲解有两个问题：

1. 直接跳到答案，用户并没有真正学会对应的 Lecture 概念；
2. 使用通用教材的讲法，和老师课件里的 notation、定义、组织方式对不上。

这个 Skill 使用 source-first 工作流：

```text
Problem Sheet
    -> 判断这题考什么
    -> 找到对应 Lecture / source 页面
    -> 直接展示相关原始页面
    -> 解释概念
    -> 逐步做题
    -> 总结可复用方法 / 考试结论
```

## 核心行为

- **以 Lecture 为依据：** 用户提供的课程资料是讲解的主要依据。
- **需要时优先展示原图：** 不只说“见第 X 页”，而是尽量直接显示最相关的课件页面。
- **默认可从零开始：** 可以假设用户没有听过、看过这节 Lecture。
- **保持课件一致：** 尽量保留课程原本的 terminology、notation、结构和 convention。
- **围绕题目讲：** 每张展示出来的 slide / page 都要说明它为什么和当前问题有关。
- **强调迁移：** 每道题最后总结方法、判断规则、常见坑或 exam shortcut。
- **支持连续学习：** 用户说“继续”时，从当前进度继续，不无意义地从头重讲。

## 示例 Prompt

```text
继续 Lecture 04-05 Problem Sheet。
默认我没有听过 Lecture，每遇到新概念先把对应课件页直接展示出来，再讲概念和做题。
```

```text
讲这个 tutorial，默认我一点 lecture 都没听过。
先把对应课件图直接贴出来，再解释概念和做题。
```

```text
按照老师 Lecture 里的 notation 和 definition 来讲。
除非我要求比较不同方法，否则不要静默换成通用教材的另一套讲法。
```

## 默认讲解流程

遇到一个新概念时，默认按下面顺序：

1. 这道题在考什么
2. 展示对应 Lecture / source 页面
3. 解释这张页面在讲什么
4. 说明做题真正要抓什么
5. 逐步完成题目
6. 给出最终答案
7. 总结以后遇到同类题怎么做

Skill 会根据题型调整讲法，包括：

- 概念 / classification 判断题；
- calculation 计算题；
- convolution / system response；
- Fourier / transform；
- proof / derivation；
- sketch / graph。

## 仓库结构

```text
.
├── README.md
├── README.zh-CN.md
├── SKILL.md
├── agents/
│   └── openai.yaml
└── .gitignore
```

- `SKILL.md`：Skill 的核心行为规范，也是主要 source of truth。
- `agents/openai.yaml`：ChatGPT 侧的 Skill 元数据。
- 仓库**不包含任何具体课程资料**。

## 安装

把安装所需文件打包，使 `SKILL.md` 和 `agents/` 位于 `skill.zip` 根目录，然后在 ChatGPT 的 Skills 页面上传 ZIP。

macOS / Linux：

```bash
zip -r skill.zip SKILL.md agents/
```

PowerShell：

```powershell
Compress-Archive -Path SKILL.md,agents -DestinationPath skill.zip
```

生成的 `skill.zip` 属于 build artifact，因此仓库默认通过 `.gitignore` 忽略它。

## 设计原则

### 1. Minimum sufficient visuals

只展示**足够讲清概念的最少页面**，不要机械地连续贴很多页 Lecture。通常一个新概念展示 1-3 页已经足够。

### 2. Inline 必须是真的“看得见”

如果当前运行环境支持 inline page rendering，那么 Lecture 页面应该直接显示在聊天正文中。

仅显示：

- `page-14.png` 文件卡片；
- `sandbox:/...png` 链接；
- “请看第 14 页”；

都不能视为和真正 inline 图片等价。

### 3. Source 优先于外部知识

如果用户提供的资料没有支持某个结论，应明确说明。

只有当用户要求：

- 扩展；
- 验证；
- 比较；
- 补充缺失背景；

时，才加入外部知识，并明确区分“课件内容”和“外部补充”。

### 4. 教会方法，而不是只交答案

目标不是替用户把 Worksheet 做完，而是让用户做完这一题以后，能够独立处理下一道同类题。

## 适用范围与限制

- Skill 本身不包含课程内容，需要用户提供 Lecture、Tutorial、Problem Sheet、教材等资料。
- Lecture 页面能否直接 inline 显示，取决于当前 ChatGPT / runtime 的能力。
- Skill 的目标是忠实地从用户提供的 source 教学，而不是静默用另一套 notation 或方法覆盖老师的讲法。
- 具体工具名只是当前实现细节；核心目标始终是：找到相关 source、尽可能清晰展示，并据此教学。

## 开发与迭代

修改行为时，以 `SKILL.md` 为主要 source of truth。

推荐的迭代流程：

```text
拿真实 Tutorial / Problem Sheet 测试
    -> 记录实际失败点
    -> 修改 SKILL.md
    -> validate
    -> 重新打包
    -> 重新安装并测试
```

不要提交自动生成的 `skill.zip`。需要安装或发布新版本时再重新打包即可。
