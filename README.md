# 🤖 Commitment Tracker Agent (Slack + Linear Integration)

An event-driven, lightweight AI agent designed to automatically detect, extract, and follow up on unresolved commitments from unstructured workplace conversations—turning casual promises into tracked, actionable tasks without manual overhead.

## 🎯 Core Problem
High-friction, everyday commitments often fall through the cracks in fast-moving chat threads (e.g., *"I'll send the doc by Friday"*, *"Going to finish the PR tonight"*)[span_8](start_span)[span_8](end_span)[span_9](start_span)[span_9](end_span). Traditional assistants only summarize or suggest; this agent actively monitors conversation streams, filters out casual banter, and maintains an automated follow-up loop[span_10](start_span)[span_10](end_span)[span_11](start_span)[span_11](end_span)[span_12](start_span)[span_12](end_span).

## 🏗️ Architecture & Two-Stage Filtering Pipeline

To avoid false positives and excessive LLM token overhead, the ingestion pipeline uses a **two-stage intent classification filter** triggered via Slack Events API (Bolt framework)[span_13](start_span)[span_13](end_span)[span_14](start_span)[span_14](end_span)[span_15](start_span)[span_15](end_span)[span_16](start_span)[span_16](end_span):

1. **Stage 1: Lightweight Heuristic / Regex Gate (Cheap Filter)**[span_17](start_span)[span_17](end_span)
   - Listens to incoming Slack messages via webhooks[span_18](start_span)[span_18](end_span).
   - Filters for future-oriented commitment patterns paired with concrete actions and optional time markers[span_19](start_span)[span_19](end_span).
   - Drops obvious non-actionable messages before invoking the LLM.

2. **Stage 2: Contextual LLM Classification**[span_20](start_span)[span_20](end_span)
   - Evaluates candidate messages alongside immediate thread context[span_21](start_span)[span_21](end_span).
   - Strictly classifies messages into `COMMITMENT` (clear action + ownership/timing) vs. `CASUAL` (hedged language like *"I'll look into it sometime"* or *"might check later"*)[span_22](start_span)[span_22](end_span).

3. **Stage 3: State Tracking & Automated Follow-Up**[span_23](start_span)[span_23](end_span)
   - Syncs verified commitments into a structured task schema (`task`, `owner`, `deadline`, `source_thread_url`, `status`) and links them with Linear issues[span_24](start_span)[span_24](end_span)[span_25](start_span)[span_25](end_span).
   - Runs a scheduled diff check against closed tasks and triggers gentle in-thread reminders for unresolved items[span_26](start_span)[span_26](end_span)[span_27](start_span)[span_27](end_span).

## 📋 Initial Task Schema (v0.1)

```json
{
  "commitment_id": "cmt_89234a",
  "owner": "@username",
  "action_item": "Review the numbers and send feedback",
  "deadline": "2026-11-21T18:00:00Z",
  "confidence": "COMMITMENT",
  "source": {
    "platform": "slack",
    "channel_id": "C01234567",
    "thread_ts": "1732123456.000100"
  },
  "linear_issue_id": "ENG-104",
  "status": "OPEN"
}
