# Codex Model Advisor

一个面向 Codex 用户的模型选择 Skill。简要描述准备执行的任务，它会从任务复杂度、可验证性、上下文规模、出错成本和返工风险等方面进行理性分析，然后推荐更节省额度的模型与推理强度。

它只负责给出建议，不会自动执行原任务、切换模型、更改配置或创建子代理。

## 使用方法

安装后在 Codex 中输入：

    $model-advisor 我准备处理一组科研数据并制作论文图片，请推荐模型和推理强度。

## 安装

在 Codex 中调用 $skill-installer：

    $skill-installer 请从这个 GitHub 仓库安装 model-advisor：https://github.com/Yufeima-1/codex-model-advisor

也可以下载仓库，将整个文件夹放进个人 Skills 目录。若没有立即出现，请重启 Codex。该 Skill 只会在显式输入 $model-advisor 时触发。

## 设计原则

- 优先选择能够可靠完成任务的最低成本模型。
- 计算上下文、工具调用、图像生成和返工在内的完整任务成本。
- 必要时拆成低成本执行阶段和高质量审核阶段。
- 不把更高推理强度简单等同于更高效率。
- 模型价格、额度和可用性变化时，只核对 OpenAI 官方资料。

---

## English

Codex Model Advisor recommends a quota-efficient Codex model and reasoning level before you start a task. It assesses task clarity, complexity, verifiability, context size, error cost, and retry risk before making a recommendation.

It provides routing advice only. It does not execute the proposed task, switch models, modify configuration, or create subagents.

### Usage

After installation, invoke it explicitly in Codex:

    $model-advisor I need to analyze a scientific dataset and prepare publication figures. Which model and reasoning level should I use?

### Installation

Use $skill-installer in Codex:

    $skill-installer Install model-advisor from this repository: https://github.com/Yufeima-1/codex-model-advisor

You may also download the repository and place the complete folder in your personal Skills directory. Restart Codex if the Skill does not appear immediately.

### Design principles

- Prefer the least expensive model likely to complete the task reliably.
- Consider total task cost, including context, tool calls, image generation, and retries.
- Split work into a lower-cost execution stage and a stronger review stage when useful.
- Do not assume that a higher reasoning level is automatically more efficient.
- Treat model pricing, quotas, and availability as time-sensitive.
