PART 1 — Context Is Not Memory
Runtime target: ~8 min Thumbnail template: 2 (diagram-hero, yellow card)

Cold open
There's one distinction that fixes more agent bugs than anything else I know, and almost everyone gets it wrong.
Context is what the model sees right now. Memory is the system you build to decide what goes into that context. The model has no memory of its own.
Once that clicks, the question we argue about — should I use Redis, or a vector database, or a managed memory layer — stops being confusing. Because you'll know exactly which job each one is doing.

Section 1 — The distinction
Context is the tokens the model sees this turn. It's finite, and it's ephemeral. Gone next call unless you put it back.
Memory is the system you build to fetch the right things into that finite context. Conversation history, user preferences, past decisions.
So every "memory" feature you've ever shipped is really context engineering. You're choosing what to retrieve and what to inject.
Now, here's why I want you to hold that distinction tightly in an interview, because it's the thing that turns a vague answer into a design.
When someone says "design an agent with memory," there are three questions sitting underneath that, and they are not the same question.
What is the context? Meaning, what actually needs to be in front of the model on this turn for it to do its job. Not everything you know about the user. The specific things.
What do we need to store? Meaning, of everything flowing through this system, what's worth keeping past the turn it happened in. Most of it isn't.
Where does it live? Meaning, which store, with what access pattern.
And each of those questions sets a different thing.
The first sets your token budget, which sets your cost per turn and — I'll show you this in the next video linked above and below in description. — your accuracy.
The second sets your write path, which sets your storage growth and whether you need a retention policy. In a regulated environment that's a compliance question.
The third sets latency, availability, and failure behaviour. A vector search sitting on the critical path adds tens to hundreds of milliseconds to every single turn.

Section 2 — The question I want the interviewer to hear me ask
And then there's the question I'd actually steer toward, deliberately, because it's the one that separates a design from a diagram.
What happens when the memory store is down?
Because you have to pick. Do you fail the request, or do you serve the user a version of the agent that has amnesia and doesn't mention it?
There's no free answer there. Fail-closed is honest and annoying. Fail-open is smooth and quietly wrong.
Let's name the tradeoff and then refuse to answer it in the abstract, because it isn't an abstract question. Let me give you two agents.
Agent one — a banking assistant.
The user says: "Yeah, go ahead and send it."
In memory: that transfer already went through twenty minutes ago.
Memory is down. The agent doesn't see it. So what happens?
It sends the transfer. Again. And the confirmation it shows the user looks exactly like the first one did.
That's the part that matters. The user has no way to tell it fired twice. Nothing on their screen is different.
So: fail closed. "I can't see your account activity right now — give it a minute, or call us." Annoying. Correct.
Agent two — a writing assistant.
The user says: "Give me the next scene."
In memory: writes in British English, and the main character is called Mara.
Memory is down. The agent doesn't see it. So what happens?
It writes the scene in American spelling and calls her Maria.
And the writer notices in about two seconds, because they're reading every word. They type "it's Mara, British spelling," and it's fixed. Total cost, five seconds.
So: fail open, with a signal. "Heads up, I can't see your past drafts, so I might not match your usual style."
Same outage. Opposite answers. And the thing that flipped it wasn't regulation or risk appetite.
It's whether the user can see the mistake.
The writer catches it instantly. The banking customer never finds out.
So the rule: if a missing memory makes the output wrong in a way the user can't detect, fail closed. If it only makes it worse in a way they'll spot and correct, fail open and say so.
And one more thing, because in practice it's not one answer per product — it's one answer per memory. Take a support agent. "Has this customer already been refunded" — fail closed, they can't see that. "Prefers email over chat" — fail open, they'll just tell you again. Same agent, same outage, two different behaviours.
If you notice this is classic system design. You're asking about the read path, the write path, and the failure mode of a dependency. Exactly the questions you'd ask about a cache sitting in front of a database.

Section 3 — The five types, and where each one lives
{excalidraw — five boxes, each labelled with its store}
Working, or in-context. The user asks "and what about the return flight?" The outbound booking is three turns up, still in the window. The model resolves it instantly. And notice what you did to make that work: nothing. No store, no fetch, no key. It came free with the API call.
Short-term, or session. Same sentence — "and what about the return flight?" — except now it's the next morning, and they're on their phone.
The phone has never seen any of those turns. The laptop's session is gone. As far as the API is concerned, that message arrives with nothing in front of it.
So unless you wrote those turns down somewhere and read them back, the agent has no idea what flight they mean.
Same sentence, same model, same question. One works for free. The other one is a system you have to build. That's the line between working and short-term — not how old the turns are, but whether the window still holds them or you have to rebuild it.
Episodic. A timestamped log of past events. Usually a vector database and SQL together. This is audit and personalization. Something like: February eleventh, 2:03pm, user escalated ticket 4471 to a human after two failed refund attempts. Later, someone asks "has this user escalated before?" and you can actually answer.
Semantic. Extracted facts and preferences, in a vector database or Mem0 storage. Persistent personalization. "User manages a team of eight, prefers Terraform over CloudFormation, is on the enterprise plan." No timestamp needed on the fact itself — it's the current truth about them.
Procedural. Learned how-to. A four-step "reset a stuck deployment" runbook the agent has refined over past runs and now follows. That lives as a prompt or a skill, not as a row in a database.

Section 4 — Evidence that the split is real
Let's look at how the managed services are shaped.
Amazon's Bedrock AgentCore Memory ships with named built-in strategies — a semantic one that extracts standalone facts, a summary one scoped to a session, a user-preference one that tracks choices and style, plus a custom strategy if you need to override the extraction yourself.
Different jobs, different pipelines, shipped in a product.
So on a whiteboard you'd partition the same way.
{capture: https://aws.amazon.com/blogs/machine-learning/amazon-bedrock-agentcore-memory-building-context-aware-agents/}

Close
So: context is the finite window. Memory is the system that fills it. Five types, and only one of them is free.
Next video, the part that surprises people — the window being finite is not the interesting constraint. The interesting constraint is that the model doesn't read all of it equally well.
[cross-link: Part 2]

Vertical lift — Part 1
The fail-open question (40s). Strong, because it's a question rather than a fact, and questions get comments.
Here's a question I ask in every agent design review, and about half the time nobody has an answer. What does your agent do when the memory store is down? You get two options. Fail the request — honest, annoying. Or serve the user an agent with amnesia that doesn't mention it — smooth, and quietly wrong. Most teams have accidentally picked the second one without deciding to. What I'd do is fail open, but say so. The agent tells the user "I don't have your history right now." That one sentence is the difference between a degraded experience and a broken one. And this isn't an AI question. It's the same question you'd ask about a cache in front of a database.
