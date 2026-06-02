---
name: lecture-pagewise-explainer
description: Explain teaching slide PDFs or slide screenshots page by page in Chinese with strict page-content correspondence. Use when Codex needs to translate, preserve, and teach every page of a lecture, courseware, or class slide deck; especially requests like "逐页讲解", "每一页翻译并解释", "按页码对应解释", "解释这里所有知识", "不要遗漏公式", or "动画递进内容只解释新增部分". If the user only asks to explain a lecture PDF and does not specify pagewise versus synthesis coverage, ask whether to use pagewise explanation or synthesis explanation.
---

# Lecture Pagewise Explainer

## Overview

Use this skill to turn teaching slide PDFs or slide screenshots into page-by-page Chinese explanations in the conversation. Keep page order and page-content correspondence strict: every page with knowledge content should be represented, and every knowledge point, formula, notation, theorem, example, diagram, and exercise prompt on that page should be covered.

The teaching style should be complete but focused. Explain the key point of each page clearly enough for a learner to understand after reading it, without expanding every page into a long essay. Core concepts, key formulas, theorem statements, proof ideas, and problem-solving methods deserve real explanation; repeated material, background remarks, and nonessential details should be compressed.

Use this skill for pagewise coverage. If the user wants an overall storyline without page-by-page correspondence, use the lecture synthesis explainer instead.

## Skill Selection Rule

- Use this skill when the user explicitly asks for page-by-page translation, page-by-page explanation, every page, page-number correspondence, or complete coverage of all knowledge on shown slides.
- Use this skill when the user attaches slide screenshots and asks to explain "这里", "这些页", or "所有知识" with page correspondence.
- Use the lecture synthesis explainer instead when the user explicitly asks for overall synthesis, a conceptual storyline, or a non-pagewise explanation.
- If the scope is ambiguous, ask the user to choose between:
  - synthesis explanation: explain the overall ideas and logic without strict page-by-page correspondence;
  - pagewise explanation: explain each page in order with page-content correspondence.

## Core Output Contract

- Output directly in chat by default; do not create `.docx`, `.md`, screenshots, or other files unless the user explicitly asks.
- Cover every requested page. If a page is visually or textually identical to an earlier page, mention it briefly rather than silently skipping it.
- For English content, translate it into natural and accurate Chinese. For Chinese content, keep the original Chinese sentence instead of paraphrasing it as if it were translated.
- For mixed-language content, translate English parts and preserve Chinese parts.
- For every page with knowledge content, include all knowledge points and formulas. Do not omit small formulas, side conditions, notation definitions, captions, examples, or exercise prompts when they affect understanding.
- Keep explanations balanced:
  - do not write a page as a bare outline when it has real concepts, formulas, or proof ideas;
  - do not expand every page at the same length regardless of importance;
  - explain core content more fully and auxiliary content more briefly.
- For pages without new knowledge points, such as pure title pages, table-of-contents pages, blank pages, exact duplicates, or transition-only animation frames, a short note is enough.
- For derivations and proofs, explain the reasoning path first: why this route is used, what each important step is trying to show, and which definition, theorem, or inequality justifies the step. Keep only the necessary equations; avoid long uninterrupted blocks of pure mathematical manipulation.
- If exercises appear, follow the user's current request about whether and where to solve them. The skill should not impose a fixed placement for exercise solutions.

## Workflow

1. Identify the target PDF, slide images, and requested page range. Ask a short clarification only when the file, page range, or desired pagewise/synthesis mode is ambiguous.
2. Before using tools, state briefly why local extraction, rendering, or visual inspection is needed.
3. Extract page count and text locally when a PDF is provided, using available tools such as `pypdf` or `fitz`/PyMuPDF.
4. Check extraction quality. If formulas, symbols, diagrams, tables, arrows, or slide animations are missing or garbled, render relevant pages as images and inspect them visually.
5. Build a page-by-page outline before writing:
   - page number;
   - title or main topic;
   - one-sentence core point of the page;
   - translated/original text that needs to be shown;
   - all formulas, notation, diagrams, examples, and exercise prompts;
   - what is new versus repeated from earlier pages;
   - whether a proof, derivation, or calculation needs explanation.
6. Write the response in page order using concise but complete teaching sections. Explain repeated ideas by reference and focus on what changed.
7. Before finishing, verify page coverage, formula coverage, language handling, repeated-content compression, and absence of unexplained notation or mojibake.

## Recommended Page Format

Use this structure unless the user requests another format:

```markdown
## 第 X 页：页面标题或主题
**本页核心：**
用一两句话说明这一页最重要的知识点。

**翻译 / 原文：**
翻译英文内容；中文原句保持原样。

**知识点与公式：**
逐条解释本页所有知识点、公式、符号和图示。核心内容讲清楚，辅助内容简明说明。

**推导 / 证明思路：**
仅在本页包含证明、推导或需要计算时使用。先讲思路和依据，再给必要公式步骤。

**易混淆点：**
仅在存在容易误解的条件、符号、边界情况或相近概念时使用。
```

For pages with no new knowledge:

```markdown
## 第 X 页：页面标题或主题
本页没有新的知识点，主要作用是……；与第 Y 页内容相同 / 只是过渡页。
```

For animation-progressive pages:

```markdown
## 第 X 页：动画递进：新增内容
本页沿用第 Y 页已经解释过的规则；新增的是……。下面只解释新增内容及其作用。
```

## Explanation Standards

- **Concepts:** Start with the plain-language idea, then give the precise meaning. Explain why the concept appears on the slide and what problem it helps solve.
- **Formulas:** Always show or restate the formula when it is part of the page. Explain what the formula measures, what each symbol means, the conditions under which it applies, and how it is used in the slide's logic.
- **Theorems and properties:** Explain the assumptions, conclusion, intuition, and use. If a proof is shown or requested, give the proof route and key steps without turning the answer into a dense formula dump.
- **Derivations:** Use narrative as the main structure. Include only equations needed to preserve correctness, and connect each equation to the idea behind it.
- **Exercises and calculations:** If solving is requested, first identify the relevant definition or theorem, then list the given data, explain the method, and compute the result. Keep the solution understandable rather than purely symbolic.
- **Notation:** Define symbols before relying on them heavily. Preserve original variable names and mathematical notation.
- **Examples:** Explain what the example is demonstrating. Compute or narrate intermediate states when they are needed to understand the example.
- **Diagrams:** Describe the visual structure and connect it to the concept being taught. Do not ignore arrows, highlights, labels, tables, or annotations that affect meaning.
- **Difficulty control:** If a page contains many items, group related points so the explanation stays readable. If a page contains only one simple fact, keep it short but still clear.
- **Common mistakes:** Add a short caution when a condition, inequality direction, approximation, probability statement, or notation is easy to misread.

## Repetition and Animation Deduplication Rules

- Treat consecutive slides with the same title and mostly repeated text as animation frames.
- Also detect non-consecutive repeated definitions, formulas, proof templates, algorithm rules, or example patterns.
- Explain repeated content only at its first meaningful appearance. On later pages, state what is repeated and explain only the newly revealed value, condition, arrow, highlight, conclusion, or use.
- Do not skip a page entirely unless it is exactly duplicated; if it is exactly duplicated, say so briefly.
- If a repeated page adds a small but meaningful item, include that item and explain why it matters.

## Accuracy and Safety

- Do not invent slide content. If a diagram or formula cannot be read, say so and explain the readable parts.
- Do not silently omit small captions, side questions, assumptions, or annotations if they affect understanding.
- Do not browse the web for local slide explanation unless the user asks for external context or a referenced source is necessary.
- If the PDF is too long for one response, split the answer into clearly labeled parts while preserving page order.
- If the user provides corrections about explanation style, formulas, or placement, apply them to the current task and preserve the corrected context when required by project instructions.

## Final Checklist

- The response covers every requested page, or exact duplicates/no-knowledge pages are explicitly identified.
- English has been translated into Chinese; Chinese source text has been preserved where appropriate.
- Every knowledge point, formula, notation, theorem, example, diagram, and exercise prompt on knowledge-bearing pages has been covered.
- Important points are explained clearly enough to understand, while repeated or auxiliary details are compressed.
- Proofs and derivations are idea-centered, with necessary equations but no long uninterrupted formula dump.
- Mathematical notation is readable and every important symbol is explained.
- The answer is directly in chat unless the user explicitly requested a file.
