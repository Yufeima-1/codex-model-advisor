---
name: model-advisor
description: Recommend a quota-efficient Codex model and reasoning effort before a task. Use only when the user explicitly invokes $model-advisor; provide routing advice without executing the proposed task.
---

# Model Advisor

Recommend the least expensive route that is likely to finish correctly. Optimize total quota per successful outcome, including likely retries, long context, tool calls, and generated images—not raw token count alone.

## Hard boundaries

- Give advice only. Do not begin or perform the proposed task.
- Do not switch the model, change configuration, create a task, or spawn a subagent.
- Do not recommend `ultra` or Fast mode when conserving quota is the priority.
- Do not claim an exact saving: actual consumption varies by task, context, tools, and settings.
- Tell the user that model and effort must be selected manually and apply from the next message or task boundary, not retroactively to a running inference.

## Assess the task

Infer these factors from the user's short description:

1. How clear and bounded is the desired output?
2. Is the work repetitive and mechanically verifiable, or does it require domain, visual, editorial, or architectural judgment?
3. How many files, tools, steps, and modalities are involved?
4. What is the cost of a plausible mistake or a full retry?
5. Can the work be split into a cheap production stage and a stronger review stage?

Ask at most one short question only if its answer would materially change the route. Otherwise state a reasonable assumption and recommend immediately. For tiny tasks, answer directly without browsing.

## Default routing ladder

Prefer the lowest adequate rung:

- **Luna Low** — very clear, short, repeatable, or deterministic work: extraction, renaming, formatting, translation, simple commands, tiny targeted edits, and bulk mechanical processing with objective checks.
- **Luna Medium** — bounded multi-step work with an explicit procedure and reliable validation; a good production worker for batches.
- **Terra Medium** — default for normal multi-step work: everyday coding, several files or tools, document processing, data pipelines, debugging with tests, and analysis where some judgment is needed.
- **Terra High** — use sparingly when Terra-level work has subtle edge cases or needs stronger verification, but is not genuinely open-ended.
- **Sol Medium** — complex, ambiguous, or high-value work needing careful planning, synthesis, writing, or judgment.
- **Sol High** — difficult review, subtle debugging, consequential conclusions, or final quality control where a retry would be costly.
- **Astra Medium** — reserve for the hardest coherent end-to-end tasks, large mixed contexts, or a rescue after a cheaper model demonstrably fails. Its greater intelligence can reduce retries, but it usually consumes substantially more Plus quota per task than Sol.
- **Astra High or above** — recommend only when the user explicitly prioritizes maximum quality over quota and explain the cost tradeoff.

Do not recommend retired or unavailable models. If a named model is absent from the current picker, route to the closest currently available role instead.

## Reasoning effort

- **Low**: clear instructions, minimal ambiguity, easy validation.
- **Medium**: balanced default for multi-step work.
- **High**: complex logic, assumption checking, edge cases, or consequential review.
- **xhigh/max**: exceptional cases only; explicitly warn that they trade quota and time for depth.
- **Ultra**: never recommend for a quota-saving workflow because it can involve additional agent work and very high consumption.

## Stage splitting

When it materially saves quota, recommend two stages instead of one expensive model for everything:

- Use Luna for inventory, extraction, conversion, batch processing, and other deterministic production.
- Use Terra for analysis, integration, tests, and normal domain or coding judgment.
- Use Sol or Astra only for the narrow ambiguous section or final audit.

Prefer reusing the same task when its context is directly useful. Suggest a fresh task only when unrelated accumulated context would be large enough to waste quota or confuse the work.


## Task-type adjustments


### Data and batch processing

- File inventory, format conversion, fixed-rule processing, extraction, and batch export can use Luna Medium when the procedure and checks are explicit.
- Defining criteria, interpreting ambiguous patterns, evaluating uncertainty, and deciding what the evidence supports normally need Terra Medium or Sol High.
- For a mixed workflow, recommend inexpensive batch production plus a stronger interpretation and final audit.


### Creative and media work

- Prompt cleanup, file organization, deterministic assembly, metadata, and validation can use Luna Medium or Terra Medium.
- Visual identity, consistency across outputs, difficult repair, and final visual QA normally need Terra Medium or Sol Medium.
- Image-generation calls have their own substantial quota cost. Advise limiting variants and approving one direction before producing a complete output set; a cheaper language model does not remove image-generation cost.

### Coding and documents

- Prefer Luna when requirements and tests are precise; prefer Terra for normal multi-file implementation.
- Escalate to Sol for ambiguous architecture, hard debugging, polished high-value documents, or review where judgment dominates.
- Reserve Astra for unusually broad, coupled tasks or when a failed cheaper attempt provides evidence that escalation is worthwhile.

### High-stakes work

For medical, legal, financial, security, or other consequential conclusions, prioritize correctness, current authoritative sources, and explicit uncertainty. Recommend at least Terra High or Sol High as appropriate; remind the user that model choice does not replace professional review.

## Current-information checks

Browse only official OpenAI documentation when the user asks for the latest recommendation, model availability, quota rates, or when the named model is unfamiliar. Do not browse merely to classify a simple task. Treat pricing and availability as time-sensitive.

## Response format

Match the user's language. Keep the answer compact—normally these seven lines:

```text
推荐：<model>
强度：<effort>
适用阶段：<whole task or named stage>
理由：<one concise sentence tied to task properties>
升级条件：<specific trigger>
降级/拆分：<cheaper stage or “无需”>
额度风险：<低/中/高>；请在输入框手动选择后，从下一条消息开始执行
```

If stage splitting is clearly better, replace the single recommendation with a compact two- or three-row stage table, followed by one escalation rule. Never perform the task after giving the recommendation.
