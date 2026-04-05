---
layout: post
title:  "Your AI Agents Are Hiding Their Uncertainty From You"
date:   2026-12-12 12:00:00 +0000
categories: ai longform
---

It's a Tuesday morning and your AI financial advisor pings you. It's been monitoring your pension, analysing how your retirement funds are performing, cross-referencing your age and goals against thousands of possible approaches.

"I think we should move some of your retirement savings into faster-growing funds. Based on your timeline, now's a good moment. Shall I make the switch?"

It sounds confident. One tap to approve.

This is the future we're building, and it's closer than most people think. Autonomous agents that manage your money, monitor your health, run your operations - handling the complexity so you don't have to.

But today, ask three exceptional financial advisors to look at your situation and you won't get one answer. You'll get a debate. The growth-minded advisor thinks you're far enough from retirement to take more risk now for a bigger payoff later. The cautious advisor sees the same numbers and thinks your current funds are solid - why gamble with your retirement for a marginal gain? The third thinks the timing is wrong and wants to wait six months.

They disagree - not because any of them is wrong, but because the situation is genuinely ambiguous and the right call depends on which risks you care more about.

A great human advisor would bring you that disagreement. They'd walk you through both sides, explain why people they respect see it differently, and help you decide based on your own priorities.

Your AI agent just picked one answer and asked you to approve it. The confidence you saw wasn't earned - it was an artefact of how language models generate text. The uncertainty was real. The agent just hid it from you.

Now scale that to every decision an autonomous agent is making on your behalf - across your finances, your calendar, your business operations - and you start to see the problem.

---

## The Agentic Moment - and Its Blind Spot

The conversation in AI right now is about agents. Not chatbots that answer questions, but autonomous systems that take action: booking travel, writing and shipping code, managing workflows, making decisions across multi-step processes without waiting for you at every turn.

The promise is real. But there's a blind spot hiding inside it.

When one of these agents hits a genuinely ambiguous decision - one where the right answer depends on which trade-offs you prioritise - it does the same thing a simple chatbot does. It picks one answer. It sounds confident. It moves on. The difference is that the agent isn't just advising you anymore. It's acting for you. And it's acting on a certainty it doesn't actually have.

So how do you build an agent that's honest about uncertainty?

The obvious answer is: give it a second opinion. And that's what the engineers and researchers building these systems have reached for:

- There's **ensemble voting**, where you ask multiple models the same question and go with the majority - which works for factual questions but flattens exactly the kind of structured disagreement that makes ambiguous decisions interesting.
- There's **collaborative refinement**, where models critique and improve each other's answers across rounds - genuinely useful for catching errors, but designed to converge on a single best response, counterproductive when you have a situation where the honest response is, "smart people would see this differently". 
- And there's **chain-of-thought self-critique**, where a single model argues with itself across turns - which can catch things it missed on the first pass, but is still bounded by one model's training, one set of assumptions, one perspective in the room.

None of these approaches treat disagreement as useful information. They treat it as a problem to be resolved on the way to a single confident answer.

But what if the disagreement *is* the answer?

---

## The Antiphon Method

Over the last few months, I've been building and iterating an agentic system that handles a deceptively simple version of this problem: deciding when and how to care for houseplants. The domain is modest. The stakes are low. But the architecture I discovered - through significant experimentation and failure - addresses something fundamental about how any autonomous agent should handle decisions under genuine uncertainty.

I call it the Antiphon Method, after Antiphon of Rhamnus, who composed paired opposing speeches for the Athenian courts in the 5th century BCE. The Athenians understood something we've largely forgotten: the quality of a decision depends on how thoroughly the opposing positions are explored before a verdict is reached.

The core idea is this: instead of asking one agent for an answer, you create a structured debate between two agents who hold genuinely different philosophies - and a third who moderates, judges, and tracks whether the winning philosophy was actually right.

In my plant care system, the two debaters are Vera and Hugo.

Vera is an interventionist. She believes early action saves plants. When the sensors say something is off, you act - quickly, decisively, before a small problem becomes a big one. She's the gardener who waters on a schedule, moves a plant at the first sign of stress, and sleeps better knowing she did something rather than nothing.

Hugo is a conservationist. He believes plants are resilient organisms that evolved to handle environmental variation, and that the biggest killer of houseplants isn't neglect - it's well-meaning owners who can't stop fiddling. He's the gardener who trusts the plant, watches the trend over days rather than reacting to a single reading, and knows that most "crises" resolve themselves.

These aren't positions I invented. They represent a genuine and persistent disagreement among experienced gardeners. Talk to enough people who are serious about plants and you'll find them somewhere on this axis. The disagreement is real, it's informed, and - crucially - different situations genuinely call for different approaches.

When my system detects an ambiguous anomaly - a sensor reading that could mean something or could mean nothing - it doesn't ask one agent to decide. It convenes what I call Parliament. Vera and Hugo both receive identical data: the sensor readings, the plant's history, an expert diagnosis. Then they debate.

They argue independently at first - neither sees the other's opening position, so neither is anchored by the other's framing. Then they respond to each other directly, challenging reasoning, citing evidence, calling on an expert witness when they need a specific factual question answered. The expert's testimony goes to both sides simultaneously - no information advantage.

A moderator - a more capable model that's been watching the whole exchange - controls the flow. It asks pointed questions when the argument stalls. It spots when Vera and Hugo are actually saying the same thing in different language. And when it's heard enough, it delivers a verdict: what to do, when to check back, how to measure whether it was the right call.

But here's where it gets really interesting. The system doesn't just make a decision and move on. It tracks what happens next. Did the recommendation get followed? Did the plant improve? Did the situation resolve on its own? Every debate becomes a data point in a growing record of which philosophy - intervention or patience - actually produces better results, for which plants, in which situations.

Over time, the system doesn't just make individual decisions. It learns which *approach to deciding* works best.

---

## Five Principles From Building This

Building, operating, and iterating this system over months produced five principles that I believe apply far beyond plant care - to any agent or agentic system handling decisions where reasonable people would disagree.

### 1. Agents in Opposition Need Permission to Agree

The most counterintuitive finding. The biggest threat to useful debate isn't that the agents fail to disagree. It's that they **disagree when they shouldn't**.

In early operation, Vera and Hugo would open with nearly identical recommendations - "act within 48 hours" versus "act within 72 hours" - then spend four to six rounds manufacturing a disagreement that didn't exist. The debate format is a powerful signal. Language models respond to it eagerly. Without an explicit way to converge, the system produces theatre on questions where both agents would, if asked independently, say roughly the same thing.

I had to give them *permission to agree*. Explicit framing that convergence is a sign of strength, not a failure of the exercise. A moderator with the awareness to detect functional agreement even when it's wrapped in different language, and the authority to end debates early when positions have merged.

**The broader principle:** Any agentic system that uses structured opposition - red teams, multi-perspective review, checks and balances between agents - needs a first-class mechanism for saying "actually, this looks fine." Without it, you generate noise, not insight. And as agentic workflows scale, artificial disagreement on easy decisions burns compute and attention that should be spent on the hard ones.

### 2. Define Agents by What They Believe, Not What They Oppose

Hugo was originally defined as "the agent that opposes the interventionist." His identity was reactive - he existed to say no to whatever Vera said. The result was mechanical contrarianism. Opposition for its own sake, with no coherent reasoning behind it.

Redefining Hugo by what he *stands for* - resilience, species-specific tolerance, seasonal awareness, the real cost of over-intervention - changed everything. An agent defined by opposition will always find something to oppose; that's its entire job description. An agent defined by values can recognise when a situation genuinely calls for the other side's approach and say so.

This is what makes concessions meaningful. When Hugo - who *consistently*, across every debate, argues for patience - suddenly says "no, Vera's right, this is urgent, act now" - that signal carries real weight. The consistency of his position is what gives the concession meaning.

**The broader principle:** As we build more complex multi-agent systems - agents that review each other's work, specialise in different perspectives, collaborate on complex tasks - how we define agent identity becomes a design decision with real consequences. The lazy default is to define agents by their role in the workflow: "you're the critic," "you're the safety checker." The better approach is to give each agent a positive framework of values and priorities that it brings to every interaction. Identity by values, not by opposition.

### 3. Track Philosophies, Not Just Decisions

Most agentic systems optimise for the quality of individual outputs and throw away the reasoning. The Antiphon Method takes a different approach: every debate produces a structured record of which philosophical position prevailed, what was recommended, when to check the outcome, and what actually happened.

Over weeks and months, this builds something I hadn't expected: a dataset that evaluates *decision-making approaches*, not just individual choices. I can ask: "For Monsteras, does early intervention or patience produce better outcomes?" and get an evidence-based answer drawn from dozens of past debates.

I call this *verdict archaeology* - digging through the structured record of past decisions to learn which approach works in which context. It's only possible because the philosophical axis is stable across every debate. Vera always represents intervention. Hugo always represents patience. The variable being tested isn't this decision or that decision - it's the philosophy itself.

**The broader principle:** If your agents are making repeated decisions in the same domain - and in any production system, they are - you should be tracking not just *what* they decided but *which approach to deciding* they used, and how that approach performed over time. This is the difference between an agentic system that makes decisions and one that learns which decision-making philosophy actually works.

### 4. Give Each Agent Its Own Memory

A more technical finding, but one with real practical implications. When I fed the full debate transcript back to each agent every turn - the obvious approach - something odd happened. The agents treated their own previous words as someone else's summary. They'd rephrase instead of building on their arguments. Concessions became shallow - acknowledged on the surface, disconnected from the agent's actual reasoning underneath.

The fix was giving each agent its own conversation history, accumulated turn by turn. When Vera references her earlier argument, she's drawing on genuine first-person memory - not reading back a transcript. When Hugo concedes a point, the concession connects to his real chain of reasoning, the position he actually held and the evidence that changed his mind.

**The broader principle:** In any system where multiple agents interact over several turns, how you handle memory is an architecture decision with outsized consequences. Shared transcripts produce surface-level engagement. Independent, accumulating histories produce genuine multi-turn reasoning. As agentic systems grow more sophisticated - maintaining state across longer interactions and more complex workflows - this distinction will matter more, not less.

### 5. Reserve Agents for Genuine Ambiguity

My system's most reliable components use no language model at all. Threshold-based monitoring - "alert if humidity drops below 40%" - is a simple rule. It doesn't need an agent. The Antiphon debate runs *only* when those rules surface a situation where the right response is genuinely unclear.

This architecture - rules at the foundation, agents at the reasoning layer - is something I've advocated throughout my career implementing technology in large organisations. The most effective systems aren't the ones that use the most sophisticated tool for every task. They're the ones that match the tool to the problem.

**The broader principle:** The current excitement about agents creates a temptation to make everything agentic. But the most robust systems will be the ones that know when *not* to deploy an agent - when a rule, a lookup table, or a simple conditional is more reliable and cheaper. Save the agentic intelligence for the decisions that genuinely need it. Everything else should be boring on purpose.

---

## Where This Applies

The Antiphon Method works in any domain with three properties: **genuine schools of thought** (defensible positions representing different value trade-offs), **repeated decisions** (enough volume to learn which approach works where), and **observable outcomes** (some way to check whether the recommendation turned out to be right).

Take the financial scenario from the opening. A growth-oriented agent and a preservation-oriented agent debate whether now is the right moment to move your retirement savings into faster-growing funds. You don't just receive a recommendation, you see a structured disagreement complete with a clear verdict but also visibility into what the alternative was and why. Over time, the system learns which philosophy produces better outcomes for people with similar timelines and goals. You make a better decision because the trade-offs are visible, not hidden behind a confident-sounding "now is a good time to move your savings".

In content moderation, a free-expression advocate and a harm-reduction advocate debate borderline posts. The reasoning is auditable. The verdict is transparent. And the platform accumulates structured data on which approach better serves its community across different content types - which is something most moderation systems today are remarkably bad at tracking.

In clinical decision support, an active-treatment advocate and a watchful-waiting advocate debate borderline cases - not replacing the clinician's judgment, but enriching it with a structured exploration of trade-offs that a single-output system would collapse into one recommendation. Over hundreds of cases, patterns emerge about which approach tends to produce better outcomes for which presentations.

In legal and regulatory analysis, a strict-interpretation advocate and a purposive-interpretation advocate debate ambiguous compliance questions. The debate itself creates an audit trail showing that both perspectives were rigorously considered - valuable in any regulated industry where "we thought about it" isn't enough; you need to show *how* you thought about it.

In every one of these cases, the method does something that a single confident-sounding agent cannot: it makes the disagreement visible, shows you the trade-offs, and builds a growing body of evidence about which approach to deciding actually produces the best results.

---

## The Deeper Principle

I've spent over two decades building technology - first as a software engineer, then as a product designer, then as a management consultant leading enterprise transformations at McKinsey. The thread running through all of that work is a conviction that the best technology enhances human capabilities without creating new dependencies.

The Antiphon Method is a direct expression of that philosophy.
It doesn't replace human judgment - it enriches it.
It doesn't eliminate uncertainty - it makes uncertainty visible.
It doesn't hand you a single confident answer - it shows you a structured disagreement and tells you which approach has worked in the past.

We're building increasingly powerful autonomous agents and giving them increasing autonomy over decisions that matter. The question everyone is asking is: "What can agents do?" The question almost nobody is asking is: "What should an agent do when it isn't sure?"

The Antiphon Method is one answer: when reasonable experts would disagree, your agents should disagree too - visibly, structurally, and with a mechanism for learning which perspective was right.

---

*Tey Bannerman is a former McKinsey Partner and Head of McKinsey Design Europe. He advises organisations globally on AI strategy and implementation, and has spoken at events including World AI Cannes Festival, Web Summit, and Design Matters. The Antiphon Method was developed through his Herbert Project - a production agentic system for plant care that became an unexpected laboratory for honest decision-making under uncertainty. [teybannerman.com](https://teybannerman.com)*