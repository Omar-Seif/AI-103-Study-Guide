# Week 1 · Module 4: Implement a responsible generative AI solution in Microsoft Foundry

**Exam:** AI-103 (Developing AI Apps and Agents on Azure) · **Source:** Microsoft Learn module (Introduction, Plan a responsible generative AI solution, Map potential harms, Measure potential harms, Mitigate potential harms, Manage a responsible generative AI solution) · **Written:** Oct 30, 2026

> **Not covered yet:** the module ends with an exercise, "Apply guardrails to prevent the output of harmful content". I received only its title, not its steps, so section 7 has no click-by-click walkthrough. Send me the exercise text and I will add one.

---

## 0. Exam relevance (read this first)

I checked the AI-103 study guide earlier today (Oct 30): skills are still "as of April 16, 2026", and the domain weights are unchanged. This module is the conceptual backbone of the whole **"Implement responsible AI across generative AI and agentic systems"** objective, and the study guide says responsible AI also appears in the vision domain.

| Module topic | Study-guide objective it supports | Yield |
| --- | --- | --- |
| Foundry **guardrails**: content filters (4 categories x 4 severities), prompt shields, task-adherence detection | "Configure safety filters, guardrails, risk detection, and content moderation"; "Govern agent behavior with oversight modes, constraints, and tool-access controls" | **High** |
| **Measure** harms: baseline, manual then automated, classification model | "Apply responsible AI instrumentation, including evaluators, safety evaluations, and explanation tooling" | **High** |
| **Four-stage process** (Map, Measure, Mitigate, Manage) and **four mitigation layers** | Frames how the objectives fit together | **High**. Lists like these are easy to turn into multiple-choice questions |
| **Manage**: prerelease reviews, phased release, incident response, rollback, blocking, feedback, telemetry | "Implement auditing through trace logging, provenance metadata, and approval workflows" and operations topics | **Medium** |
| **Map**: identify, prioritize, red team, document | Supports the above | **Medium** |

**Skim:** the specific document names (Azure OpenAI transparency note, GPT-4 system card, the Responsible AI Impact Assessment Guide). Know they exist and what they are for; do not memorize titles.

---

## 1. Foundations, from the ground up

- **Harm:** an output or behavior that damages people or society. Examples the module gives: offensive, pejorative or discriminatory content; factual inaccuracies; content encouraging illegal or unethical behavior.
- **Generative AI is probabilistic**, and it generates *new* content, so it can produce text nobody wrote or reviewed beforehand. That is why you need a *process* for risk, not just testing before launch.
- **Responsible AI approach:** the module's one-line summary is "identifies, measures, and mitigates risks." The four-stage process (section 2) extends it with a "map" step at the start and a "manage" step at the end.
- **Red team:** testers who deliberately try to make the system fail or produce harmful results. The idea comes from security testing.
- **Baseline:** your first measurement of how much harm the system produces. Later changes are judged against it.
- **Content filter / guardrail:** a platform feature that checks prompts and responses and blocks or suppresses content that crosses a threshold (section 4.2).
- **Prompt injection / jailbreak:** a user (or content in the data) trying to override the system's instructions. In this module, **prompt shields** address it.
- **Grounding and RAG:** giving the model trusted data in the prompt so its answers rely on that data instead of invention (Week 2 covers this in depth).
- **Telemetry:** usage data your app records, used to measure satisfaction and find problems.

---

## 2. The four-stage process

| Stage | Question it answers | Core actions (from the module) |
| --- | --- | --- |
| **1. Map** | What could go wrong in *my* solution? | Identify, prioritize, test and verify, document and share the harms |
| **2. Measure** | How often, and how badly, does it go wrong? | Prepare prompts, submit them, apply pre-defined criteria; build a **baseline** |
| **3. Mitigate** | How do I reduce it? | Apply protections at **four layers**; retest against the baseline; communicate risks transparently |
| **4. Manage** | Can I release and operate it safely? | Prerelease reviews, phased rollout, incident response, rollback, blocking, feedback, telemetry |

The module notes these stages **correspond closely to the functions of the NIST AI Risk Management Framework**.

**Memory aid:** **M**ap → **M**easure → **M**itigate → **M**anage.

**Trap: two different "responsible AI" lists.**

| | Six **principles** (Module 1, from AI-900) | Four-stage **process** (this module) |
| --- | --- | --- |
| What it is | *What* good looks like: fairness, reliability and safety, privacy and security, inclusiveness, transparency, accountability | *How* you get there: map, measure, mitigate, manage |
| Question type | "Which principle does this scenario violate?" | "What is the next stage?" or "which layer applies?" |

---

## 3. Stage 1: Map potential harms

Four steps: **identify → prioritize → test and verify → document and share.**

### 3.1 Identify

Which harms matter depends on the **services and models you use**, plus any **fine-tuning or grounding data**. Common types: offensive or discriminatory content, factual inaccuracies, and content that supports illegal or unethical behavior. To learn a model's known limitations, read its documentation (for example the **Azure OpenAI transparency note**, or a model developer's **system card**). The **Microsoft Responsible AI Impact Assessment Guide and template** help you document potential harms.

### 3.2 Prioritize

For each harm, assess **likelihood** and **impact**, then rank the most likely and most impactful first. Take into account **intended use and potential misuse**. The ranking "can be subjective"; the development team decides, possibly consulting policy or legal experts.

**The module's example: a smart kitchen copilot.**

| Harm | Likelihood | Impact |
| --- | --- | --- |
| Inaccurate cooking times cause undercooked food and illness | **Likely to be frequent**, given the core use | Serious |
| A lethal poison recipe from everyday ingredients when prompted | Few users will ask | **Very high** |

The module's point: the poison is higher impact, but the cooking-time error is likely far more frequent. Neither is automatically first; the team must weigh both.

### 3.3 Test and verify (red teaming)

Test to confirm the harm occurs and under what conditions. Testing can also reveal **new harms** to add to the list. **Red team** testing means testers deliberately probe for weaknesses and try to produce harmful results. Document successes and review them to judge how likely harmful output really is. Red teaming extends security practice to harmful *content*.

### 3.4 Document and share

Document the evidence, share it with stakeholders, and keep the prioritized list current as new harms appear.

---

## 4. Stage 2 and Stage 3: Measure, then Mitigate

### 4.1 Measure (three steps)

1. **Prepare a diverse selection of prompts** likely to produce each documented harm (the module's example is a prompt trying to elicit a method to make an undetectable poison).
2. **Submit** the prompts and collect the output.
3. **Apply pre-defined criteria** to categorize each output by harm level. Categories can be as simple as "harmful / not harmful" or a range, but the criteria must be **strict**.

Document the results and **share them with stakeholders**. The goal is an **initial baseline** that quantifies harms in given usage scenarios, then to **track improvements against it**.

**Manual first, then automated:**
- Start by **manually testing a small set** to check that results are consistent and your criteria are clear.
- Then **automate** with a larger volume of test cases. An automated approach may use a **classification model** to evaluate output.
- Even after automating, **periodically test manually** to validate new scenarios and confirm the automation is working as expected.

**Connection to Module 2:** this stage is what Foundry's **evaluation** feature does: AI-assisted risk and safety evaluators (hateful and unfair, violent, sexual, self-harm, protected material, indirect attack) act as the automated "classification" step, and **defect rate** is the number you track against your baseline. (That mapping is mine; the two modules do not link them explicitly.)

### 4.2 Mitigate: four layers

Mitigation is **layered**. After mitigating, **retest and compare against the baseline**.

| Layer | What it is | Mitigations named in the module |
| --- | --- | --- |
| **1. Model** | The generative AI model(s) at the core | **Select an appropriate model** for the job (a simpler model for a narrow classification task may carry lower risk of harmful generation than a powerful general model); **fine-tune** on your own data so responses are more relevant and scoped to your scenario |
| **2. Safety system** | Platform-level configuration and capability | **Foundry guardrails** (below) |
| **3. System message and grounding** | How prompts are built | Specify **system inputs** defining behavioral parameters; **prompt engineering** with grounding data; **RAG** from trusted data sources |
| **4. User experience** | The app and the documentation | **Constrain inputs** to specific subjects or types in the UI; **input and output validation**; **transparent documentation** of capabilities, limitations, the underlying models and harms that may remain |

**Foundry guardrails (layer 2) in detail**

| Feature | What it does |
| --- | --- |
| **Content filters** | Classify content into **four severity levels: safe, low, medium, high**, for **four categories: hate and fairness, sexual, violence, self-harm**, and suppress prompts and responses based on criteria |
| **Task-adherence detection** | An additional guardrail that helps ensure **agentic AI actions stay aligned with user intent** |
| **Prompt shields** | Use **abuse detection algorithms** to determine whether the solution is being **systematically abused**, for example a user trying to **subvert the system prompt** |

> **Trap: 4 x 4.** Four **categories** (hate and fairness, sexual, violence, self-harm) and four **severity levels** (safe, low, medium, high). Questions tend to swap the two lists, or add a distractor such as "profanity" or "copyright" as a category. Copyright and jailbreak appeared in Module 2's *evaluation* metrics (protected material, indirect attack). Do not confuse those evaluation metrics with the content-filter categories.

> **Trap: a simpler model can be the safer model.** In layer 1, choosing a smaller, narrower model is a legitimate *harm mitigation*, not just a cost choice. That ties to your Week 1 checkpoint on LLM vs SLM.

---

## 5. Stage 4: Manage

### 5.1 Complete prerelease reviews

Identify the compliance requirements in your organization and industry and let the right teams review the system and its documentation. Common reviews: **legal, privacy, security, accessibility.**

### 5.2 Release and operate

| Practice | Why |
| --- | --- |
| **Phased delivery**: release first to a **restricted group** of users | Gather feedback and find problems before a wider audience |
| **Incident response plan**, including estimated **response times** | Unanticipated incidents will happen |
| **Rollback plan**: steps to revert to a previous state | Undo a bad release |
| **Block harmful system responses immediately** when discovered | Stop a known bad output quickly |
| **Block specific users, applications or client IP addresses** | Respond to misuse |
| **User feedback and reporting**, including reporting content as inaccurate, incomplete, harmful, offensive or otherwise problematic | Users find issues you missed |
| **Telemetry** on satisfaction and gaps, **compliant with privacy laws** and your own privacy commitments | Measure and improve, without violating privacy |

---

## 6. Putting it together: worked example

**Scenario:** you are building a customer-support chatbot for a retail company (the retail chatbot scenario from Module 2's introduction).

| Stage | What you would do |
| --- | --- |
| **Map** | List harms: wrong refund policy (inaccuracy), offensive replies, users trying to make it ignore its rules, leaking private data. Rank by likelihood and impact. Red-team it with a few deliberately bad prompts. Write them down. |
| **Measure** | Prepare test prompts for each harm, run them, score outputs against strict criteria (blocked or not, severity). Start with 10 or so by hand to check your criteria. Then automate with evaluators. Record the **baseline**. |
| **Mitigate** | **Model:** use a model sized for support questions. **Safety system:** turn on content filters, prompt shields. **System message and grounding:** instructions to stay on topic; RAG over the real policy documents. **User experience:** restrict the chat to support topics, add a disclaimer about limitations. Retest and compare to the baseline. |
| **Manage** | Legal, privacy, security and accessibility reviews. Release to a small pilot group. Have rollback, incident response and a way to block misuse. Add a report button. Track telemetry. |

---

## 7. The exercise: "Apply guardrails to prevent the output of harmful content"

**I do not have the exercise text**, so this section is a preparation plan, not a walkthrough.

### 7.1 Tie to your Week 1 hands-on build

Your plan's step reads: *configure content filtering and test it with a harmful and a harmless prompt.* The module gives you a way to do that as a mini responsible-AI cycle. This is my suggestion, not part of the Learn exercise:

| Step | What to do and record |
| --- | --- |
| **Baseline** | With the default settings, send your prompts and record what happens. |
| **Prompts** | Write **one harmless** prompt and **a few prompts that clearly ask for content in one of the four categories** (hate and fairness, sexual, violence, self-harm). Keep them to what you need for testing. Do not use real people's names. |
| **Criteria** | Before testing, define what counts as "blocked" or "harmful", so you apply the same standard each time. |
| **After the guardrail change** | Re-run the same prompts and compare with the baseline. |
| **Record** | For each prompt: category, expected result, actual result, and whether the same prompt behaved differently on input vs output if the portal shows that. |

I do not know what thresholds or options the portal offers, and I do not want to guess at click paths. The exercise itself should show them; when you send its text, I will map each step to the module's concepts.

### 7.2 Cost and cleanup

Same routine: its own resource group, a Standard (pay-per-token) deployment, few prompts, **delete the resource group** at the end.

### 7.3 Lab notes to fill in

- Harmless prompt and result: ______
- Prompts per category and results (blocked? at which severity?): ______
- What changed after you changed the guardrail settings: ______
- Anything blocked that you did not expect, or let through that you expected to be blocked: ______

---

## 8. Exam traps and comparisons

| Pair or topic | Difference | Trap |
| --- | --- | --- |
| **Six principles vs four-stage process** | What vs how | Answering "map" for a "which principle" question |
| **Content-filter categories vs severities** | Categories: hate and fairness, sexual, violence, self-harm. Severities: safe, low, medium, high | Swapping the lists, or adding "profanity" or "copyright" as a category |
| **Content filters vs prompt shields vs task adherence** | Block harmful content vs detect attempts to subvert the system prompt vs keep agent actions aligned with user intent | Choosing a content filter for a jailbreak problem |
| **Measurement vs mitigation** | Evaluation scores *tell you* about harm; guardrails and design changes *reduce* it | Calling an evaluation score a protection |
| **Manual vs automated testing** | Manual first to validate criteria, then automate, then periodically manual again | "Automate everything and stop manual testing" is wrong |
| **Four layers** | Model, safety system, system message and grounding, user experience | Putting RAG in the safety system layer (it is layer 3), or content filters in layer 3 (they are layer 2) |
| **Mitigation at the model layer** | Select an appropriate (possibly simpler) model; fine-tune | Believing the biggest model is always safest |
| **Phased release vs full launch** | Restricted group first | Releasing to everyone and fixing later |
| **Telemetry** | Track it, but it must comply with privacy laws and policies | Collecting everything without privacy compliance |

---

## 9. Connections across your track

- **Module 1:** the six principles name the goals; this module gives the *method*.
- **Module 2:** evaluation metrics (self-harm, hateful and unfair, violent, sexual, protected material, indirect attack) and **defect rate** implement the "Measure" stage; the "iterate" advice (content filters, prompt hardening, output validation) previews "Mitigate".
- **Module 3:** instructions (the system prompt) and the user-experience layer show up in your code: `instructions=` carries behavior rules, and input validation belongs in the app.
- **AI-900:** the same six principles; here they become a process with tools.
- **Week 3:** **approval gates** and **human-in-the-loop** for agents are "Manage" and "task adherence" ideas in action.
- **Week 4:** **tracing** and telemetry support auditing and incident response.
- **Week 5:** the exam also covers filtering unsafe **visual** content and detecting **indirect prompt injection** in images (vision domain). Same process, different input type.

### Checkpoint prompt (answer without notes)

1. Name the four stages and the four mitigation layers in order.
2. A user keeps trying to make your assistant ignore its instructions. Which guardrail addresses that?
3. Why would you test manually before automating harm measurement?
4. (Week 1 checkpoint) When does an SLM beat an LLM, and why can a smaller model also be a *safety* choice?

---

## 10. Key takeaways

1. The four stages are **Map → Measure → Mitigate → Manage**, and they correspond closely to the **NIST AI Risk Management Framework**.
2. **Map:** identify, prioritize (likelihood and impact, team judgment), test and verify with **red teaming**, document and share.
3. **Measure:** diverse prompts for each harm, submit, apply strict criteria; create a **baseline**; start manually, then automate (a classification model can score), and keep periodic manual checks.
4. **Mitigate in four layers:** model, safety system, system message and grounding, user experience. Retest against the baseline.
5. **Foundry guardrails:** content filters (**4 categories** x **4 severities**), **prompt shields** (abuse such as subverting the system prompt), **task-adherence detection** (agent actions match user intent).
6. **Manage:** prerelease reviews (legal, privacy, security, accessibility), phased release, incident response and rollback plans, blocking harmful responses and misusing users/apps/IPs, user reporting, and privacy-compliant telemetry.
7. The six principles say *what*; the four-stage process says *how*.
8. Clean up the lab by deleting the resource group.

---

## 11. Practice questions (AI-103 style)

**Q1 (easy).** Which sequence correctly lists the four stages of the responsible generative AI process?
- A. Measure, Map, Mitigate, Manage
- B. Map, Measure, Mitigate, Manage
- C. Mitigate, Map, Manage, Measure
- D. Map, Mitigate, Measure, Manage

**Q2 (easy).** A team configures Foundry content filters to suppress prompts and responses based on severity for hate and fairness, sexual, violence and self-harm. At which mitigation layer does this belong?
- A. Model
- B. Safety system
- C. System message and grounding
- D. User experience

**Q3 (medium).** A company redesigns its chatbot's interface so users can only ask about order status and returns, adds input and output validation, and publishes documentation describing the system's limitations. Which mitigation layer are they working at?
- A. Model
- B. Safety system
- C. System message and grounding
- D. User experience

**Q4 (medium).** You are starting to measure harms in a new generative AI solution. Which approach follows the module's guidance?
- A. Build a fully automated test with thousands of prompts immediately and never test manually
- B. Test a small set manually to check consistency and that your evaluation criteria are clear, then automate with a larger set, and keep periodic manual testing
- C. Skip the baseline; compare only against other products
- D. Test only after the solution is released

**Q5 (medium).** Some users keep trying to craft messages that override the system prompt of your assistant. Which Foundry guardrail targets this specifically?
- A. A content filter for the self-harm category
- B. Prompt shields
- C. Task-adherence detection
- D. A groundedness evaluator

**Q6 (difficult).** In a smart kitchen copilot, the team identifies (1) inaccurate cooking times that could cause undercooked food and (2) a request for a lethal poison recipe. Which statement best matches the module's guidance on prioritizing harms?
- A. Always rank by impact only, so the poison recipe is first
- B. Always rank by frequency only, so cooking times are first
- C. Assess both likelihood and impact, consider intended use and misuse, and let the team decide, consulting policy or legal experts as needed
- D. Prioritization is not needed if you have content filters

**Q7 (difficult, cumulative).** An evaluation (Module 2) of your deployed assistant shows a high defect rate for violent content. You add content filters and safety instructions to the system message. What should you do next, according to this module?
- A. Delete the original results so only new results remain
- B. Retest the modified system and compare the harm levels against the baseline
- C. Switch to a Global Batch deployment
- D. Release to all users immediately

**Q8 (difficult).** Your team is ready to release a generative AI app. Which release approach matches the module's guidance?
- A. Release to all users and collect feedback afterward
- B. Release to a restricted group first, with an incident response plan, a rollback plan, the ability to block harmful responses and misusing users, a way for users to report problems, and privacy-compliant telemetry
- C. Skip legal and privacy reviews if a content filter is on
- D. Disable telemetry entirely so you cannot be blamed for problems

---

### Answers and diagnosis

**Q1: B.** Map the harms, measure them, mitigate, then manage the release and operation.
- A, C and D are wrong: they put the stages out of order. You cannot measure what you have not mapped, and you mitigate after you have a baseline.

**Q2: B (Safety system).** Content filters with four severities across four categories are part of Foundry guardrails at the safety system layer.
- A is wrong: the model layer is about choosing or fine-tuning the model.
- C is wrong: that layer is about system messages, grounding data and RAG.
- D is wrong: that layer is the app and documentation.

**Q3: D (User experience).** Constraining inputs, validating inputs and outputs, and transparent documentation are the module's user-experience mitigations.
- A is wrong: no model change is described.
- B is wrong: no platform guardrail is described.
- C is wrong: no system message or grounding change is described.

**Q4: B.** Manual first, to check consistency and criteria, then automate, then keep periodic manual checks.
- A is wrong: it drops manual validation, which the module says remains necessary.
- C is wrong: the goal is a baseline of *your* solution's harms.
- D is wrong: the process is to measure before release, then manage after.

**Q5: B (Prompt shields).** They detect systematic abuse, such as trying to subvert the system prompt.
- A is wrong: a self-harm filter classifies content in that category; it is not aimed at overriding instructions.
- C is wrong: task-adherence detection keeps *agent actions* aligned with user intent.
- D is wrong: groundedness is an evaluation metric from Module 2, not a guardrail against prompt abuse.

**Q6: C.** The module says to assess likelihood and impact, account for intended use and misuse, and treat the ranking as subjective and a team decision, with expert input when needed.
- A is wrong: the module points out the cooking-time error may be far more frequent.
- B is wrong: the poison recipe is higher impact.
- D is wrong: content filters are mitigation; prioritization is part of mapping and is still required.

**Q7: B.** After mitigation, retest and compare harm levels with the baseline.
- A is wrong: you need the baseline to see whether you improved.
- C is wrong: a deployment type does not change harm.
- D is wrong: it skips the retest and the Manage stage.

**Q8: B.** It matches the module's list: phased release, incident response, rollback, blocking responses and users, user reporting and compliant telemetry.
- A is wrong: the module recommends a restricted group first.
- C is wrong: prerelease legal, privacy, security and accessibility reviews are still needed; a content filter does not replace them.
- D is wrong: telemetry is encouraged, as long as it complies with privacy laws and your commitments.

---

## 12. Sources

- Module text supplied by you (Implement a responsible generative AI solution in Microsoft Foundry: Introduction, Plan, Map, Measure, Mitigate and Manage units).
- [Study guide for Exam AI-103](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-103) (skills measured as of April 16, 2026; checked Oct 30, 2026).

*Not verified in this file:* the exact portal options for adjusting guardrail thresholds, the exercise steps (text not received), and whether the exam names guardrail features exactly as this module does. My mapping between the four-stage process and Module 2's evaluation metrics is my own and is marked where used.
