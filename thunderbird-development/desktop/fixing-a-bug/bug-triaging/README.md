---
description: Tutorial on how to dive into triaging bugs.
---

How to efficiently triage bugs and contribute to a clean and organized Bugzilla

# Expectations
The best way to optimize your time and drive a productive triaging session is to set limited expectations, and only tackle a limited amount of bugs per day. It’s easy to feel overwhelmed after triaging 10 bugs (some of which may be difficult) and seeing that in the meantime 50 more bugs have been reported.

Try to manage your expectations and accept that what you are doing, as small as it can look, is important and vital for the success of Thunderbird.

# Requirements
Not all of these are hard requirements, but some nice to have configurations that will make it easier to triage complex reports.

Have the ability to test on all the Operating Systems (bare metal or VMs), and should not be blocked by technical limitations as much as possible.
Run all currently supported versions (Daily, Beta, ESR).
Ability to run [moz-regression](https://mozilla.github.io/mozregression/) and familiarity with the tool.

# Rules of the Road

* **Always be respectful** and polite even when faced with a difficult reporter, because a) this is a customer facing environment, b) being empathetic is healthy for both you and the reporter. Doesn’t mean you have to engage with impolite reporters or accept abuse (see 3rd point below). *“Thank you”*, and *“please”*, and similar ideas go a long way to encouraging users to engage and return.
* **Acknowledge the issue**, e.g.: *“I’m sorry you are having some issues, could you please provide me with the following information so that I can look into it…”*
* **Don’t accept harassment** from a reporter, if they violate our community rules mark the comment as abuse and close the bug as invalid.
  * If the bug is a real issue, recreate it in a new bug. Allowing abuse in Bugzilla sets a bad precedent and is unhealthy for everyone involved and the entire community.
  * *E.g.: “You guys are idiots. This is disgusting. The Thunderbird Team/you are stupid”.*
* Do not drive users against developers, and do not argue with other team members in a bug. The MZLA team is to always have a united front on Bugzilla.
  * *E.g. Triager finds the regression and pulls in the developer. Developer says that it was intended that this feature would work differently (or is now unsupported).*
  * *Another core developer joins the conversation and publicly disagrees with the previous developer’s statement.*
  * ***Don’t argue in the bug - take it to another internal forum to discuss, come back with a united front on how to proceed.***
* **Don’t promote your own point of view.** A triager should be unbiased - is it a bug? Is it reproducible? That’s it.
* **Do not try to create a solution to the bug.** Leave the ideation and implementation of a solution to the developers, designers, and product management as there are likely many considerations that go into a resolution.
* **Stay neutral and take a step back when things are hard to handle.** We have 20 million users, lots of bugs and lots of negativity will come in. A bug report comes from an emotional reaction, likely a negative experience. You need to detach from the emotional aspect of any bug and not escalate emotions, but also not take it as a personal attack.

# How to triage
* **Don’t triage bugs that you have no idea what they are.** Stick to your area of expertise!
  * If you’re in the back-end, don’t try to triage front-end bugs, and vice versa.
  * No need to add noise to bugs if you have no clue what the topic or context is.
* **Stick to your strengths** and to the areas you worked on. Participate in the bugs that you know what the reporter is talking about and you have historical knowledge of that area.
* **Point users to KB articles** so they learn to use them, and hopefully are then less likely to report things that are not bugs and more likely to make good bug reports. 
  * Examples: [Troubleshoot Mode](https://support.mozilla.org/en-US/kb/cannot-receive-messages), [not receiving messages](https://support.mozilla.org/en-US/kb/cannot-receive-messages) - in general items under https://support.mozilla.org/en-US/products/thunderbird/fix-slowness-crashing-error-messages-and-other-problems.  (If an existing article is inaccurate, please ping Roland, Heather, or Wayne to get to get it updated) 
* Try to first reproduce the issue reported.
* **Try to add all the flags and data points in one go** in order to limit email noise for all the people CC’ed on those bugs.
* **Don’t let your time go to waste**. If you have read a bug but are unable to confirm or add significant info (you can’t reproduce, etc) - at a minimum: assign a component, a better summary, or NI or CC someone who can take a next step.

