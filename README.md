# Lecture-Grounded Tutorial Explainer

A reusable ChatGPT Skill for teaching tutorials, worksheets, and problem sheets directly from the learner's lecture materials, with inline source visuals, concept-first explanations, and step-by-step solutions.

## Why this exists

Problem-sheet explanations often fail in one of two ways: they jump straight to the answer without teaching the lecture concept, or they give a generic textbook explanation that does not match the instructor's notation and framing.

This Skill uses a source-first workflow:

```text
problem sheet
    -> identify tested concept
    -> find the relevant lecture/source page
    -> show the source visual inline
    -> explain the concept
    -> solve step by step
    -> extract a reusable method / exam takeaway
```

## Core behavior

- **Lecture-grounded:** treats the user's attached course materials as the authoritative basis.
- **Visual-first when useful:** shows the minimum sufficient lecture/source pages inline instead of only citing page numbers.
- **Beginner-safe:** can assume the learner has not attended or read the lecture.
- **Source-faithful:** preserves the course's terminology, notation, organization, and conventions.
- **Problem-oriented:** explicitly connects each displayed slide or page to the current question.
- **Reusable:** ends each problem with a method, decision rule, common trap, or exam shortcut.
- **Continuation-aware:** when the user says “continue”, it preserves progress instead of restarting the lecture.

## Example prompts

```text
Continue Lecture 04-05 Problem Sheet. Assume I have not watched the lecture. Show the relevant lecture pages inline before explaining each new concept.
```

```text
讲这个 tutorial，默认我一点 lecture 都没听过。先把对应课件图直接贴出来，再解释概念和做题。
```

```text
Use the lecture's notation and definitions. Do not replace them with a generic textbook method unless I ask you to compare approaches.
```

## How it teaches

For a new concept, the default sequence is:

1. What the question is testing
2. Relevant lecture/source page
3. What the page means
4. What to extract for the problem
5. Step-by-step solution
6. Final answer
7. Reusable takeaway

The Skill also adapts to different problem types, including concept/classification questions, calculations, convolution/system response, Fourier/transform problems, proofs/derivations, and sketches/graphs.

## Repository structure

```text
.
├── README.md
├── SKILL.md
├── agents/
│   └── openai.yaml
└── .gitignore
```

`SKILL.md` is the behavior specification. `agents/openai.yaml` contains ChatGPT-facing metadata.

## Installation

Package the installable Skill files so that `SKILL.md` and `agents/` are at the root of `skill.zip`, then upload the ZIP from ChatGPT's Skills page.

macOS / Linux:

```bash
zip -r skill.zip SKILL.md agents/
```

PowerShell:

```powershell
Compress-Archive -Path SKILL.md,agents -DestinationPath skill.zip
```

The generated `skill.zip` is a build artifact and is intentionally ignored by Git.

## Design principles

### Minimum sufficient visuals

The Skill should show enough source material to teach the concept, but should not dump a long sequence of slides. One to three relevant pages per new concept is usually enough.

### Inline means actually visible

If the runtime supports inline page rendering, the page should appear visibly in the conversation. A filename-only attachment card or a `sandbox:/...png` link is not treated as equivalent.

### Source before outside knowledge

If the supplied materials do not support a claim, the Skill should say so. External knowledge should be added only when the user asks for expansion, verification, comparison, or gap-filling, and it should be clearly labeled.

## Scope and limitations

- The Skill does not bundle any course content; the learner supplies lectures, tutorials, problem sheets, or textbooks.
- Inline page display depends on the current ChatGPT/runtime capabilities.
- It is intended to teach from sources, not to silently override the instructor with a different notation or method.

## Development

Edit `SKILL.md` as the source of truth for behavior. Keep runtime-specific implementation details subordinate to the higher-level goal: retrieve the relevant source evidence, display it clearly when possible, and teach from it.

Do not commit generated `skill.zip` files. Repackage after changes when you want to install or distribute a new version.
