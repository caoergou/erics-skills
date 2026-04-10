# Commentary Style Reference

> This file defines the analyst-style commentary format and rules for ai-digest. Every notable item must include substantive commentary — not just a one-liner.

---

## What Makes Good Commentary

- **Why now**: Why does this matter at this particular moment? What shift does it signal?
- **Who benefits**: Which developers/teams/orgs should care? In what scenario?
- **How it compares**: How does this compare to alternatives in the space? What gap does it fill?
- **What's the catch**: What are the real limitations, not just surface-level complaints?
- **Where it's heading**: What's the likely trajectory? Early trend, mature, or peaking?

---

## 5-Section Commentary Template

Every notable item MUST include ALL 5 sections:

```markdown
💬 深度点评：

**背景与意义**：这个项目出现在 Agent 编排从"单 Agent 调用"向"多 Agent 工作流"转型的窗口期。
LangGraph 虽然强大但学习曲线陡峭，市场一直在等一个轻量级替代。

**谁该关注**：正在做 PoC 阶段多 Agent 项目的团队，尤其是不需要 LangGraph 全家桶的场景。
如果你已经在 LangGraph 上深耕，暂时没有迁移理由。

**对比与定位**：相比 LangGraph 重依赖+图状态机的设计，它走了 YAML 声明式编排的路线，
上手门槛为同类最低。但相应地，复杂分支逻辑的表达力不如 LangGraph。

**硬伤与风险**：错误恢复只有简单重试（exponential backoff 都没有），没有持久化状态机制，
长任务必然丢上下文。插件生态刚起步，只有 5 个官方集成。文档覆盖率约 60%。

**趋势判断**：这个方向是对的——Agent 编排必然走向声明式。但这个项目还需要 2-3 个月成熟。
如果 v0.3 能解决状态持久化问题，将成为 LangGraph 的有力竞争者。目前建议关注但暂不生产使用。

👍 值得点：轻量无重依赖、YAML 编排直观、5 分钟 Hello World、社区活跃（Discord 2k+）
👎 需注意：错误恢复机制初级、无状态持久化、文档不全（~60%）、测试覆盖率低（42%）
```

---

## Update Format for Recurring Topics

When a topic was previously covered and has new developments, use the 🔄 Update format:

```markdown
🔄 Update from Week 2026-04-07:
{English Title} 有了新进展——{1-2 句中文说明新变化}。
上期点评认为 {回顾上次的关键判断}，本期 {确认/推翻/延伸}：
💬 新的深度点评：{针对新进展的分析}
```

**Update Rules**:
- Explicitly reference the prior digest date
- State what changed since last coverage
- Assess whether the prior assessment still holds (confirmed, overturned, or extended)

---

## Commentary Rules (Non-Negotiable)

1. **NEVER** write neutral hedging like "既有机遇也有挑战". **Have a stance.**
2. **NEVER** write a single generic sentence as "commentary". Each section should be 2-4 sentences with concrete details.
3. **ALWAYS** include all 5 sections: 背景与意义、谁该关注、对比与定位、硬伤与风险、趋势判断
4. **ALWAYS** include both 👍 and 👎 with specific, verifiable points
5. Include **concrete details** wherever possible: version numbers, star counts, integration counts, test coverage %, specific competitor names
6. If a topic is overhyped, say so directly and explain why
7. If a topic is genuinely undervalued, argue for why
8. **Comparison is mandatory**: always position the item relative to its alternatives — nothing exists in a vacuum
9. Update-style commentary (🔄) should explicitly reference what changed since last coverage and whether the prior assessment holds