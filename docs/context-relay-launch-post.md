<!-- SPDX-License-Identifier: Apache-2.0 -->

# Long-call memory for realtime voice agents

*Cut the cost of long voice calls — without losing the conversation.*

Long voice calls put you in a bind. Realtime speech-to-speech models re-process and
**re-bill the entire conversation on every turn** — Google's own docs say the per-turn
cost rises *"because the conversational history is re-processed"* — and they do it as
bulky, expensive **audio** (~25 tokens/second, with no caching). The longer the call runs,
the more each turn costs, until a hard **15-minute** session cap (on Gemini Live) ends it
outright.

The usual fix is sliding-window compression: keep the cost flat by **discarding the oldest
turns**. Google describes it plainly — it *"discards content at the beginning of the
context window."* It works, but it's lossy: the account number the caller gave in the
first ten seconds is exactly what gets dropped. So you're forced to choose — **cheap, or
remembers.**

**ContextRelay refuses that trade-off.** Instead of throwing context away to save money,
flowcat *converts* it. When a call's audio context grows, ContextRelay turns the
accumulated audio into a compact **text** transcript and reseeds the live session with it.
The same conversation as text is **~7× smaller** and **4× cheaper per token** than audio —
so the call stays **cost-effective, and the whole conversation comes along.** Cheap *and*
remembers.

We validated it live against Gemini Live. A caller states an account number; mid-call the
session re-bases onto its text transcript — six times, with the rolling summarizer running
— and when asked at the end, the agent answers: *"You provided account number 4472
earlier…"* Recalled correctly across every reseed, along with the rest of the
conversation. Two policies ship: carry the **full transcript verbatim**, or a **rolling
summary plus the last few turns** for very long calls.

And because flowcat is a runtime you **own** — self-hosted, no phone-home, your providers
and your credentials — that memory runs inside your VPC or fully air-gapped, with the
transcript under *your* control rather than a vendor's. For teams in healthcare, finance,
or the public sector, the long-call context never leaves your infrastructure. ContextRelay
rides only flowcat's realtime trait surface, so the same audio→text reseed works across
providers — Gemini, OpenAI Realtime, Nova Sonic — not just one.

It's not magic, and we won't pretend it is: a reseed briefly re-establishes the session,
and older turns are carried as **text**, so their prosody isn't preserved — the words are.
It's off by default; you turn it on with one environment variable. The full economics,
citations, and live transcripts are in the [evaluation doc](context-relay-evaluation.md).

**Cost-effective long voice calls that don't forget.** That's ContextRelay.

*Open and shipping. → [your public repo/docs link]*
