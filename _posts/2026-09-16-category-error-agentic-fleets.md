---
layout: post
title: "The Category Error at the Heart of Agentic Fleets"
date: 2026-09-16 09:00:00 +0000
---

While the internet is buzzing with talk of ever-newer capabilities of agentic
systems, few people stop to consider what happens when the system fails. Let's
consider a typical scenario for a postmortem analysis in an agentic system:
every node has a "green" status, all metrics are within acceptable limits, and
all unit tests were successful. However, somewhere at the fleet level, an
unauthorized action occurs, three hops from where anyone was looking.

Every safety mechanism that has ever proven effective in production software
decided a question the system could actually answer. Agentic fleets, by
contrast, stake their security on a predicate the system structurally cannot
evaluate — and dress the gap up as an engineering flaw. This is the category
error the whole architecture rests on: treating an underdetermined question of
intent as an engineering problem to be solved with more code.

## What everything that works has in common

Every isolation and security barrier that has proven effective in distributed
systems has one, not immediately obvious characteristic: it controls a predicate
whose truth can be determined.

When a build pipeline compares a compiled artifact against a canonical digest, it
executes a byte-diff. When a gateway validates an incoming request, it checks a
cryptographic signature against a known public key. When a test harness asserts
correctness, it evaluates whether execution satisfied a deterministic assertion.
When a Kerberos Key Distribution Center grants a ticket, it validates known
credentials against an internal database.

In each of these instances, an "oracle" exists because everything needed to
decide the question is already inside the system's field of view. The runtime
contains the input byte string, the key, the test harness, and the output
payload. Because all inputs are entirely contained within the system's
observable state, the predicate can be mechanically evaluated with absolute
certainty.

This constitutes the foundation of Tier 0 security: closed ecosystems wherein
essential decisions derive from decidable premises. From these, hooks can be
established, constrained by rigorous execution limits, and assured to confine
faulty states within specified boundaries. The foundation of this architecture
remains robust because the oracle necessary for security assessment is physically
situated within the pipeline.

## The predicate a fleet actually needs

Transitioning from a deterministic framework to a fleet of agents fundamentally
changes the nature of the question. The runtime environment no longer evaluates
whether a byte string matches a hash. Instead, it asks a more complex set of
questions: Is this instruction valid? Did the principal authorize this subagent
to execute this particular action? Is this peer node's task interpretation
authorized? Should this child process exist?

These are not four distinct questions; they represent different expressions of a
single shape. They are questions about intent, not questions about state.

Treating this as an engineering problem reflects a misunderstanding of the nature
of this predicate. This is not "undecidable" in the classical Turing sense —
where a definitive mathematical truth exists but cannot be computed within finite
time — but rather an underdetermined matter. Legitimacy is not an inherent
characteristic of a text token or tool call; it is a relationship between an
action and the internal, unexpressed intent of a human principal.

That intent resides outside the context window. It is absent from the system
prompt, not captured within the RAG retrieval pipeline, and cannot be derived
from maintained task state. The legitimacy oracle would require an input that the
runtime environment never obtains. You cannot write an algorithm to compute a
result from an input you do not possess.

## Not a new problem — a bigger one

This fundamental barrier is not entirely new; it represents the same structural
vulnerability that makes in-band prompt injection so persistent, amplified across
a distributed network of autonomous nodes.

In a standard language model runtime, control and data channels are mixed inside
a single context window. Every incoming string — whether originating from a
trusted principal's prompt, a database field, or an untrusted web page — is
converted into the same token stream. The model cannot reliably distinguish
between an instruction it must obey and data it must merely inspect, because the
clarifying factor — the principal's actual intent — exists outside the channel.

When extended to an agentic fleet, this vulnerability escalates. "Is this
untrusted text a prompt injection?" at the individual agent level becomes "Should
this subagent be instantiated?" at the fleet scale. If a node processes untrusted
text, condenses it, and leverages that condensed state to create a subagent, the
instruction transition happens without a type boundary. What was inert data
within one context becomes executable authority within the next. Fleet autonomy
does not resolve prompt injection; it provides prompt injection with a
propagation mechanism.

## Why the usual fixes don't ground it

When confronted with this structural limitation, engineering teams typically
implement a comprehensive set of protective strategies. While each approach
offers distinct advantages, none can convert an underdetermined condition into a
decidable one.

- **Secondary LLM classifiers.** Deploying a guardrail model to evaluate a task
  payload essentially assigns one paraphrasing model to oversee another
  paraphrasing model. This introduces no additional ground truth; instead, it
  merely adds computational overhead to an already uncertain semantic assessment.
- **Human-in-the-loop approvals.** Humans lack the intent oracle when reviewing
  complex, high-volume automated steps. Faced with hundreds of natural-language
  delegation requests, human attention decays rapidly, converting the human
  verifier into a rubber stamp. The fleet's operational load exhausts the
  supervisor's capacity.
- **Signed spawn manifests and cryptographic credentials.** While cryptographic
  signatures prevent credential forgery, they provide no protection against a
  confused deputy wielding legitimate authorization. A compromised agent
  performing an unauthorized action does so with a cryptographically valid token.
- **Capability attenuation and scope ceilings.** Limiting a subagent's
  permissions reduces the potential impact of a compromise, yet it does not
  eliminate confusion within permitted boundaries. An agent restricted to a
  single database retains complete authority to damage that database.

These protective measures reduce risk probability and limit expected damage — but
none of them supplies the missing oracle.

## Name the tier you actually built

If an architecture cannot anchor its fundamental safety determinations in
decidable predicates, it should refrain from employing deterministic terminology.

An agentic fleet that depends on semantic assessment, unrestricted propagation
pathways, or maintained task context operates within Tier 1: Risk-Managed. In
Tier 1, safety exhibits inherent probabilistic characteristics and non-stationary
behavior. Because the foundational reasoning infrastructure relies on externally
hosted probabilistic models, a minor adjustment to model weights or an RLHF
modification by an API provider can shift your fleet's safety parameters
substantially without initiating any code modification or repository version
change.

Tier 1 represents a legitimate operational framework for constructing
sophisticated, high-impact systems. The flaw in modern agentic engineering is not
the decision to build Tier 1 fleets; it is the habit of describing Tier 1 fleets
using Tier 0 "safe-by-construction" rhetoric. Claiming deterministic safety for a
system constructed on underdetermined premises creates a dangerous gap between
perceived and actual risk — a gap that stays invisible in product demos and
unbounded in postmortems.

Engineering clarity requires calling things by their true names: you have
developed a system that is risk-managed probabilistically, not one that is secure
by construction.

## Underneath: two harder questions

Behind this category error sit two deeper questions that multiagent design must
eventually confront: whether the cascading effects of a distributed swarm are
even revertible once propagated into shared state, and whether a verifier that
the actor itself can reach or influence can ever be called a verifier at all.
