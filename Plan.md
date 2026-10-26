# AI-103 Six-Week Study Plan

Oct 11, 2026 · @Omar

## Context and assumptions

This plan prepares for Microsoft exam AI-103 (Azure AI Apps and Agents Developer Associate) in six weeks, from Monday October 12 to about Sunday November 22, 2026, using a build-first approach.

**Who it is for:** an AI engineering undergraduate who holds the IBM Data Science, AZ-900, and AI-900 certificates, works full-time, and wants to become an AI/LLM engineer in Egypt and the Gulf.

**Resources:**

- Udemy course: *AI-103: Azure AI App and Agent Developer - Complete Course* by Luke Ginn, 82 lectures, about 32.5 hours, with a companion practice-tests course.
- Microsoft Learn learning paths: Develop generative AI apps in Azure; Develop AI agents on Azure; Extract insights from visual data on Azure; plus the Azure Language and Speech modules.
- $100 of Azure credit for hands-on labs.

**Assumptions to confirm:**

- About 10 to 12 study hours per week, roughly 66 hours in total. The plan splits into about 26 hours of course, 15 of labs, 10 of Learn gap-filling, and 12 to 15 of practice and review.
- The Azure credit does not expire before about November 22. Check the expiry date in the Azure portal.
- Content details come from lecture titles and Learn module descriptions, not the lecture content itself. Verify against the official AI-103 skills outline on Microsoft Learn before relying on any item.
- The exam was in beta in mid-2026. Confirm its current status, duration, and question count on Microsoft Learn before booking.

## Exam map

The course covers the largest domain well and the three smallest thinly, so Learn and your own labs fill the rest. Weights are from the April 16, 2026 skills outline as reported by study guides; one source lists the generative AI domain higher, so check the official outline.

| Exam domain | Weight | Course coverage | Main gap-filler | Plan weeks |
| --- | --- | --- | --- | --- |
| Implement generative AI and agentic solutions | 30–35% | Strong: RAG, grounding, multi-agent, tools, MCP, human-in-the-loop, memory, evaluation, tracing | Learn: Foundry IQ, optimize model performance, agent modules | 2, 3, 4 |
| Plan and manage an Azure AI solution | 25–30% | Partial: model selection, responsible AI, Entra Agent ID, Key Vault, Bicep, telemetry, caching | Learn: select/deploy/evaluate models; own labs for quotas, cost, CI/CD | 1, 6 |
| Computer vision | 10–15% | Thin: one multimodal unit and short code walkthroughs | Learn: vision path, Content Understanding | 5 |
| Text analysis | 10–15% | Thin: one NLP preprocessing unit | Learn: Azure Language modules | 5 |
| Information extraction | 10–15% | Thin: Document Intelligence and Content Understanding as agent tools | Learn: Document Intelligence, AI Search knowledge mining | 2, 5 |

Responsible AI is not a single topic: it appears across all five domains, so keep notes on content safety and safeguards every week.

## Weekly rhythm and rules

The course is the spine, Learn fills its gaps, and every week ends with something you built and can explain.

**The loop each week:**

1. Watch the course units at 1.25x speed, with the quiz after each.
2. Do the lab yourself from the instructions before watching its code walkthrough. Struggling first is where the learning happens.
3. Read the matching Learn module for its knowledge check and anything the course skipped.
4. Write a short note per unit in your own words: what it is, when to use it, and one exam-style trap.
5. Close the week with the checkpoint questions at the end of its section.

**Skip or trim:**

- Course Lab 6 and its walkthrough (Multi-Agent Routing), both marked outdated. Use the new Part A to D walkthroughs.
- The duplicate "Repeat due to Udemy bug" lecture, and the second copy of the Bicep special unit.
- Learn modules not obviously on the skills outline: Microsoft 365 integration, Sora video generation, and Voice Live. Check the official outline first.

**Cost safety for the $100 credit:**

- Set Azure Cost Management budget alerts at roughly $20, $50, and $80 before creating anything.
- Delete the resource group after each lab; the course's Bicep unit shows how to deploy and tear down repeatably.
- Check current pricing before creating Cosmos DB, Azure Redis, AI Search, or Microsoft Fabric. Use free or serverless tiers where they exist, and watch the lecture on Fabric without running its lab.
- Use small, cheap model deployments for practice.
- If credit runs low in week 5 or 6, run fewer labs and rely on walkthroughs, then prioritize the plan and manage domain.

## Week 1 (Oct 12–18): Foundry, identity, responsible AI, model selection

By the end of week 1 you can set up a Foundry project, deploy a model, call it from Python with Entra ID instead of keys, and explain safety filters and model choice. This week covers most of the plan and manage domain's foundations.

**Course (about 6 hours of lectures plus quizzes):**

- Unit 1: What Is an AI Agent?
- Unit 2: Microsoft Foundry, the agent development platform
- Lab 1: Microsoft Foundry intro, then Code Walkthrough 1
- Special Unit: Bicep deployment, tips, tricks and advice
- Unit 3: Entra Agent ID, agent identity deep dive
- Lab 2: Foundry Agent intro, then Code Walkthrough 2
- Unit 4: Responsible AI and safety for agents, Lab 3, and Code Walkthrough 3
- Unit 5: Model selection, LLMs vs SLMs for agent tasks, Lab 4, and Code Walkthrough 4
- Client walkthrough Unit 1 (first agent call) and Unit 2 (content safety input and output filtering)

**Learn (about 3.5 hours):**

- Plan and prepare to develop AI solutions on Azure
- Select, deploy, and evaluate Microsoft Foundry models
- Implement a responsible generative AI solution in Microsoft Foundry
- Develop a generative AI chat app with Microsoft Foundry

**Hands-on build:**

1. Set budget alerts, then create a Foundry project with Bicep.
2. Deploy one larger and one smaller model, and call both from Python using Entra ID authentication.
3. Configure content filtering and test it with a harmful and a harmless prompt.
4. Compare the two models on 10 sample tasks for quality, speed, and cost, and write down when you would route to each.
5. Delete everything with the teardown step.

**Concepts to master:** what a Foundry project is, model deployments and endpoints, keys vs Entra ID authentication, what Entra Agent ID adds, content safety filtering on inputs and outputs, and how to choose a model for a task.

**Checkpoint:** explain without notes why you would use Entra ID instead of API keys, and when an SLM beats an LLM. Score the quizzes at 80% or better.

## Week 2 (Oct 19–25): RAG, grounding, AI Search, system instructions

By the end of week 2 you can build a retrieval-augmented app on Azure AI Search that answers with citations, and you can say how you would improve its retrieval quality. This week covers the biggest part of the generative AI domain.

**Course (about 4 hours):**

- Unit 6: Agentic RAG, dynamic retrieval for agents, Lab 5, and Code Walkthrough 5
- Unit 7: Advanced grounding with Fabric and Cosmos DB. Watch the theory only; do not run a Fabric lab because of cost.
- Unit 8: Context management and system instructions
- Client walkthroughs: Unit 3 (dynamic prompt construction), Unit 5 (model parameters such as temperature), Units 6 to 8 (AI Search parts 1 to 3: basic search, vector search with semantic ranking, and agentic RAG)

**Learn (about 4.5 hours):**

- Optimize generative AI model performance with Microsoft Foundry (prompt engineering, RAG, fine-tuning, and when to combine them)
- Build knowledge-enhanced AI agents with Foundry IQ
- Create a knowledge mining solution with Azure AI Search

**Hands-on build (a document question-answering app):**

1. Choose 10 to 20 of your own documents, such as notes or course PDFs.
2. Create an AI Search index on the free tier if it fits your data, with chunking and embeddings.
3. Try keyword, vector, and hybrid search with semantic ranking, and compare results on the same questions.
4. Return answers with source citations.
5. Write 15 test questions with expected answers and score the app on how often it retrieves the right passage.
6. Delete the search service when done.

**Concepts to master:** what grounding is and why it reduces hallucination, how chunking and embeddings affect retrieval, keyword vs vector vs hybrid search, semantic ranking, what makes retrieval "agentic", how system instructions shape responses, and what temperature and similar parameters do.

**Checkpoint:** from a failing question in your test set, diagnose whether the problem is chunking, retrieval, or the prompt, and say what you would change.

## Week 3 (Oct 26–Nov 1): tool calling, MCP, human-in-the-loop

By the end of week 3 you can build an agent that calls a REST API tool and an MCP server, and pauses for human approval before sensitive actions. You already have agent experience, so spend your effort on the Azure-specific services and terms.

**Course (about 4.5 hours):**

- Unit 10: Tool calling, connecting agents to APIs, Lab 7 (tool calling and basic MCP), and Code Walkthrough 7
- Unit 11: Model Context Protocol, standardized tool connectivity, Lab 8 (more about MCP servers), and Code Walkthrough 8
- Unit 12: Human-in-the-loop, approval gates
- Client walkthroughs: Unit 9 (tool calling with REST API integration) and Unit 10 (MCP)

**Learn (about 4 hours):**

- Develop generative AI apps that use tools
- Develop AI agents with Microsoft Foundry and Visual Studio Code
- Integrate custom tools into your agent
- Integrate MCP tools with Azure AI agents

**Hands-on build (an approval-gated agent):**

1. Deploy a Foundry agent through the Foundry Agent Service.
2. Give it one custom REST tool, such as a public weather or currency API.
3. Connect it to one MCP server and note how MCP tools are discovered compared with custom tools.
4. Add an approval step before any action that changes data, and test both the approved and rejected paths.
5. Write down the tool schema you gave the model and one case where the model chose the wrong tool.

**Concepts to master:** what the Foundry Agent Service does vs building an agent in code, how tools are described to the model, built-in vs custom vs MCP tools, why MCP standardizes tool access, and where to place human approval gates.

**Checkpoint:** describe in one paragraph when to use a built-in tool, a custom function tool, or an MCP server, and what could go wrong if an agent can act without approval.

## Week 4 (Nov 2–8): multi-agent orchestration, tracing, evaluation

By the end of week 4 you can build a multi-agent solution with the Microsoft Agent Framework, trace it with OpenTelemetry, query the telemetry with KQL, and judge it with evaluation metrics. This finishes the generative AI and agents domain.

**Course (about 6 hours):**

- Unit 9: Multi-agent orchestration with the Microsoft Agent Framework, plus Code Walkthrough 6 Parts A to D (sequential, concurrent, Magentic, and group chat patterns). Skip the outdated Lab 6.
- Unit 13: Foundry Trace, OpenTelemetry debugging
- Unit 14: Analyzing telemetry with KQL and Application Insights
- Lab 9: Foundry Trace (parts 1 and 2) and Code Walkthrough 9
- Unit 15: Performance evaluation, metrics that matter
- Client walkthroughs: Unit 11 (handoff pattern), Unit 12 (Magentic manager pattern), Unit 13 (sequential vs concurrent), Unit 14 (OpenTelemetry debugging)

**Learn (about 4.5 hours):**

- Develop an AI agent with Microsoft Agent Framework
- Orchestrate a multi-agent solution using the Microsoft Agent Framework
- Build agent-driven workflows using Microsoft Foundry
- Discover Azure AI Agents with A2A

**Hands-on build (a two-to-three agent workflow):**

1. Build a task, such as research then summarize then review, using one pattern from the course (handoff or sequential).
2. Rebuild it with a different pattern (concurrent or Magentic) and note the trade-offs in latency, cost, and control.
3. Turn on tracing, run 10 requests, and open the traces in Application Insights.
4. Write two KQL queries: one for slowest requests and one for failures.
5. Evaluate the outputs on a small test set and record what the metrics miss.

**Concepts to master:** the four orchestration patterns and when each fits, what the Agent Framework and Foundry workflows each provide, what A2A is for, OpenTelemetry traces and spans, basic KQL, and the difference between measuring performance and measuring answer quality.

**Checkpoint:** given a scenario, choose an orchestration pattern and justify it, then say which trace data you would check first when an agent is slow.

## Week 5 (Nov 9–15): memory, vision, text analysis, information extraction

By the end of week 5 you can add memory to an agent and use vision, language, Document Intelligence, and Content Understanding services directly, not only as agent tools. These three domains are 10 to 15 percent each, so this is the week to lean on Learn more than the course.

**Course (about 4.5 hours):**

- Unit 16: Agent memory, short-term and long-term state, Lab 10, and Code Walkthrough 10
- Unit 17: Multimodal integration (vision, speech, Document Intelligence)
- Unit 18: Input transformation, NLP preprocessing for agents
- Server walkthroughs: Unit 1 (native vision), Unit 2 (vision as a tool), Unit 3 (Document Intelligence as a tool), Unit 6 (Content Understanding as a tool). Units 4 and 5 on speech are lower priority.
- Client walkthroughs: Unit 15 (short-term memory with Redis), Unit 16 (long-term memory with Cosmos DB), Unit 17 (multimodal), Unit 18 (NLP preprocessing)

**Learn (about 7 hours, the core of this week):**

- Develop a vision-enabled generative AI application
- Analyze images with Content Understanding
- Create a multimodal analysis solution with Azure Content Understanding
- Create an Azure Content Understanding client application
- Extract data with Azure Document Intelligence (prebuilt and custom models)
- Analyze text with Azure Language in Foundry Tools
- Optional, if time remains: the Azure Language MCP server module and the speech modules. Check the skills outline first.

**Hands-on build (a document extraction pipeline):**

1. Collect 10 sample receipts or invoices, such as your receipt detection project's images.
2. Extract fields with a prebuilt Document Intelligence model, and with Content Understanding for comparison.
3. Run Azure Language on text from the documents for language detection, key phrases, entities, and personal information redaction.
4. Send an image to a vision-enabled model and compare its answer to the extracted data.
5. Add short-term memory to your week 4 agent using the cheapest option; if Redis or Cosmos DB costs too much, watch the walkthroughs instead of running them.

**Concepts to master:** short-term vs long-term agent memory and where each is stored, when to use Document Intelligence vs Content Understanding vs a multimodal model, prebuilt vs custom extraction models, and the Azure Language features for text analysis.

**Checkpoint:** given a scenario (scanned forms, product photos, support emails), pick the right service and say what could fail.

## Week 6 (Nov 16–22): production readiness, gaps, practice tests, exam

By the end of week 6 you have closed your weakest domain, scored well on timed practice tests, and booked the exam. This week is lighter on new content and heavier on practice.

**Course (about 3 hours):**

- Unit 19: Caching for latency with Azure Redis
- Unit 20: Complete agent solution, integration and exam readiness
- Client walkthroughs: Unit 19 (secure secret management with Azure Key Vault) and Unit 20 (complete production-ready agent integration)

**Gap-filling (about 3 hours, self-study from Microsoft documentation):**

- Model deployment options, quotas, and rate limits in Foundry
- Cost management for AI workloads, including token cost and budget alerts
- CI/CD for AI projects: deploying with Bicep or pipelines, and keeping secrets out of code
- Monitoring and security: Key Vault, managed identities, network and access controls

Neither the course titles nor the Learn paths you listed show much on these, so check the official skills outline for the exact wording of each objective.

**Practice and review (about 6 hours):**

1. Take the first practice test untimed on day 1 of the week, then sort every miss by exam domain.
2. Spend two sessions on your lowest-scoring domain: read the Learn module, redo the related lab, and write two exam-style questions of your own.
3. Take the second practice test timed, in one sitting.
4. Use Microsoft's official practice assessment and exam sandbox if available, to get used to the question formats.
5. Book the exam once the readiness checklist below is met. Pick a date within 1 to 2 weeks, not months away.

**Final-week rules:** no new labs after day 5, delete all Azure resources you no longer need, and sleep before the exam.

## Readiness checklist and exam-day notes

Book the exam when you can tick most of these, with the first two being non-negotiable. The score thresholds are my suggestion, not an official rule.

- [ ] You scored about 80% or better on a timed practice test, and no single domain is below about 70%.
- [ ] You built the RAG app and the agent (tools, MCP, approval gate) without following a walkthrough.
- [ ] You can explain Entra ID authentication, content safety, and model selection without notes.
- [ ] You can choose between Document Intelligence, Content Understanding, Azure Language, and a multimodal model for a given scenario.
- [ ] You can name the four multi-agent patterns and when each fits.
- [ ] You have read the official skills outline line by line and found no objective you cannot explain.
- [ ] You deleted unused Azure resources and checked your remaining credit.

**Exam-day notes (verify on Microsoft Learn before booking):**

- Delivery is through Pearson VUE, online or at a test center. One source says 120 minutes, English only, with a 700 out of 1000 pass mark and 40 to 60 questions. Treat these as unconfirmed.
- The exam was in beta in mid-2026. Confirm its current status and any change in format.
- Test your computer and room setup in advance if you take it online.
- Mark hard questions and return to them; scenario questions often hinge on one requirement such as cost, security, or latency.
- If you do not pass, the score report shows weak domains. Use it to plan a short retake, after checking the retake waiting period on Microsoft's site.
- One source says the certification renews annually through a free Microsoft Learn assessment. Confirm this.

## Starter prompt for a new chat

Paste this into a new chat together with the plan above, so the new chat has the context it needs.

```text
I am preparing for Microsoft exam AI-103 (Azure AI Apps and Agents Developer Associate) over six weeks, starting October 12, 2026. I am an AI engineering undergraduate who holds the IBM Data Science, AZ-900, and AI-900 certificates and works full-time, with about 10 to 12 study hours per week and $100 of Azure credit. I want to become an AI/LLM engineer in Egypt and the Gulf.

My resources are the Udemy course "AI-103: Azure AI App and Agent Developer - Complete Course" by Luke Ginn, three Microsoft Learn learning paths, and hands-on labs in Azure. My six-week plan is attached.

How I want you to help:
- Before teaching a topic, ask me a few questions to find my level, then teach from the foundations up and do not assume I know the underlying concepts.
- When suggesting what to do next, offer a few options with a short question instead of choosing for me.
- Tie each concept to a real implementation or lab, and point out exam-style traps.
- Quiz me at the end of each week using the checkpoint in the plan, and diagnose why I miss questions.
- Check anything about the exam or Azure services against current Microsoft Learn, because AI-103 was in beta in 2026 and details may have changed.

Start with Week 1. Ask me your calibration questions first.
```

# Azure Setup for AI-103: Subscription, Credits, Budgets, Resource Groups

**Exam:** AI-103 (Azure AI Apps and Agents Developer) · **Plan position:** Week 1, "Setup first" · **Written:** Oct 11, 2026
**Goal of this file:** by the end you can sign in, see your $100 credit and what is eating it, set up spending alerts, create a practice resource group, and delete it safely. No prior portal experience assumed.

---

## 0. Exam relevance (read this first)

I checked the current AI-103 study guide on Microsoft Learn (skills measured as of **April 16, 2026**; guide last updated April 2026).

| What this file covers | Where it touches the exam | Yield |
| --- | --- | --- |
| Subscriptions, resource groups, regions | "Design Azure infrastructure for AI apps and agent-based solutions" and "Configure model and agent deployments" (Plan and manage, 25-30%) | Medium. You need the vocabulary, not portal click-paths |
| Budgets and cost control | "Manage quotas, scaling, rate limits, and cost footprints for model and agent workloads" | Low-medium. The exam asks about cost *of model usage* (tokens, quotas), not how to click through Cost Management |
| Deleting and recreating environments | "Integrate Foundry projects with CI/CD pipelines" (Bicep comes later) | Low now, higher once we reach Bicep |

**Honest summary:** this file is mostly about keeping your $100 safe and getting you ready to do the labs. It is not heavy exam content. The exam does not test portal click-paths for budgets. Do not memorize the clicks; understand the ideas.

The study guide also says the exam can include **Preview** features if they are commonly used, so it is worth rechecking the skills outline before booking.

---

## 1. The ideas, from the ground up

You already met these in AZ-900. Here they are again in the order they matter for doing real work.

### The hierarchy

```
Your Microsoft Entra tenant      <- "the organization" (your sign-in identity lives here)
  └── Subscription               <- the billing container. Costs and credits attach HERE
        └── Resource group       <- a folder of related things
              └── Resources      <- the actual services (a model deployment, a search service, ...)
```

**Analogy:** the subscription is your bank account (and the $100 sits in it). A resource group is a labeled box of receipts for one project. Resources are the individual purchases inside the box. Throw the box away and everything in it goes with it.

### Plain definitions

- **Subscription:** where Azure charges accumulate. Your $100 credit is attached to a subscription. If the credit runs out, what happens next depends on the offer (see section 3).
- **Resource group:** a container for resources that share a lifecycle, meaning you create, update and delete them together. Per Microsoft, it holds *metadata* about the resources, and the region you pick for the group is where that metadata is stored. Resources inside a group can be in different regions.
- **Resource:** one actual service instance. In this track: a Foundry project, a model deployment, an AI Search service, a Key Vault.
- **Region:** the physical location (for example "Sweden Central"). Some models and services exist only in some regions. This matters in Week 1 when you deploy a model.
- **Credit:** prepaid money that Azure subtracts usage from. It is not a spending limit you control; it is a balance.

### The most important idea in this file

> **Resources cost money while they exist, not only while you use them.**

Some resources bill per use (per token sent to a model). Others bill **per hour just for existing**, even if nobody touches them. Forgetting one of those is how credit disappears overnight. A real example from Microsoft's own Q&A forum: a student reported the whole $100 gone in under 24 hours because of a single large Premium SSD managed disk. You will not create disks in this track, but the lesson applies to anything you leave running.

---

## 2. Step-by-step: sign in and find your subscription

**Step 1. Sign in to the portal.**
Open https://portal.azure.com in a browser. Sign in with **the exact account the $100 credit was given to**. This matters: if you have several Microsoft accounts (personal, school), signing in with the wrong one shows an empty account and no credit.

**Step 2. Open your subscription list.**
In the search bar at the top, type **Subscriptions** and open it. You should see at least one subscription. Click it.

**Step 3. Note three things** from its overview page and write them down:
1. The subscription name and ID
2. The offer or subscription type, if shown
3. The status (it should say *Active*)

**If you see no subscription**, or it says *Disabled*, stop and tell me exactly what the screen says. That is the first thing to fix, before any lab.

---

## 3. Step-by-step: find out where your $100 came from and see the balance

The place you check credit depends on **how you got the $100**. I do not know your offer, so here is the one I could verify, plus what to do if it is something else.

### Case A: Azure for Students (the most common source of a $100 credit)

Facts, from Microsoft Learn and Microsoft Q&A:

- You get **$100 credit for 12 months** and some free services. Usage beyond the free quantities is deducted from the credit.
- When the credit runs out **or** 12 months pass, Azure **disables the subscription**. To keep using Azure you must upgrade to pay-as-you-go, which Microsoft says is done by contacting Azure support. Do **not** upgrade unless you accept paying real money.
- Because the subscription is disabled rather than billed, the credit also works as a safety net against real charges. That is a reason students use it. (It also means a runaway resource can end your whole lab budget fast.)

**To see your remaining credit:** use the **Microsoft Azure Sponsorships portal**: https://www.microsoftazuresponsorships.com/ (if the link does not load, search for "Microsoft Azure Sponsorships portal"). Sign in with your student account. The **Balance** page shows used and remaining credit, and the **expiry date is below the credit chart**. Choose **Usage** in the left menu, then your subscription, to see *which service* is spending: columns show Service Name and Spend.

### Case B: some other source (free account, Visual Studio, employer, event voucher)

I have not verified the steps for your offer type, so I will not guess. Tell me the offer name shown on your subscription (step 3 above) or how you claimed the credit, and I will check the right place.

### Do this now (5 minutes)

- [ ] Find your remaining credit and **write down the expiry date**. The study plan assumes it lasts past about Nov 22. If it expires sooner, tell me and we will compress the labs.
- [ ] Open **Usage** and note what is currently spending (it should be about $0).

---

## 4. Step-by-step: budgets and alerts

### What a budget is, and what it is NOT

From Microsoft's Cost Management documentation:

- A budget sends **notifications** when spending crosses a threshold.
- **"Resources aren't affected, and your consumption isn't stopped."** A budget does not stop anything. It only emails you.
- Cost data usually arrives **8 to 24 hours late**, and budgets are evaluated **every 24 hours**. So an alert can reach you a day after the money was spent.

> **Trap:** a budget alert is a smoke detector, not a sprinkler. The thing that actually protects your credit is **deleting resources when you finish**. Alerts only tell you that you forgot.

Two more cautions from the docs:
- A **brand-new subscription can take up to 48 hours** before Cost Management features work.
- The docs list supported account types for budgets; **I could not confirm that Azure for Students appears on that list**. If **Budgets** is missing or errors for you, do not worry. Use the Sponsorships portal (section 3) as your meter and check it after every lab.

### Create a budget (if available to you)

1. In the portal search bar type **Subscriptions**, open it, click your subscription.
2. In the left menu click **Budgets**, then **Add**.
3. Check that the **scope** is your subscription. Leave filters empty.
4. Give it a name, such as `ai103-credit-guard`.
5. Reset period: **Monthly**. Set an expiration date well in the future (for example 12 months).
6. Budget amount: **$100**.
7. Click **Next** and add alert conditions. A budget needs at least one threshold and one email, and can have up to **five** thresholds and five emails.
   - Type **Actual**: **20%**, **50%**, **80%** (this equals the $20 / $50 / $80 alerts in your study plan)
   - Type **Forecasted**: **100%** (warns you early if your trend will blow past the budget)
   - Email: your own address
8. Create it. Add `azure-noreply@microsoft.com` to your safe senders so alerts do not land in junk.

**One limitation to know:** a monthly budget starts counting from zero each month, and your labs will cross from October into November. The alerts therefore watch *monthly* spend. The **Sponsorships balance page is your true running total**. Check it weekly.

---

## 5. Step-by-step: create a practice resource group

Resource groups themselves cost nothing, so this is a safe first action. Creating one shows you the whole pattern.

1. In the portal search bar type **Resource groups** and open it.
2. Click **Create**.
3. **Subscription:** pick yours. **Resource group:** name it `rg-practice-001`. **Region:** pick one near you, for example a European region. (Some student subscriptions limit which regions are allowed; if your region is rejected, pick one that is offered and tell me which.)
4. Click **Review + create**, then **Create**. It takes a few seconds.
5. Click the bell icon at the top, then **Go to resource group**. It is empty, and that is fine.

**Naming habit for the whole track:** one resource group per lab, named like `rg-ai103-w1-foundry`. That way "delete the lab" means deleting one group.

---

## 6. Step-by-step: the teardown routine (the habit that protects your credit)

When you finish any lab:

1. Search **Resource groups**, open the group for that lab.
2. Click **Delete resource group**.
3. Azure asks you to type the group's name to confirm, then confirm the delete. **Everything inside the group is deleted.**
4. Wait for the notification (bell icon) saying the deletion succeeded.
5. Back in **Resource groups**, check the group is gone.

**Now delete `rg-practice-001`** to practice the full create-then-delete cycle on something free.

Two things to be aware of for the real labs (we will check these during the Foundry lab, not now):
- Some AI services are **soft-deleted**: billing stops, but the name may stay reserved for a while and you may need to **purge** it to reuse the name. I will show you where when it happens.
- Your plan's Bicep unit automates this create-and-delete cycle. Doing it by hand first means Bicep will make sense when we reach it.

---

## 7. Cost-safety checklist for the whole track

Before **every** lab:
- [ ] Check the credit balance (Sponsorships portal).
- [ ] Put the lab in its own resource group.
- [ ] Look up the price of any service you have not used before **on its Azure pricing page**. I have not verified current prices, so do not trust my memory or the plan's guesses.

After **every** lab:
- [ ] Delete the resource group.
- [ ] Check it is gone.
- [ ] Next day, glance at **Usage** to confirm spending stopped.

Services to look up before creating, because they can bill continuously (the study plan names these too): **Azure AI Search** (check whether a free tier fits), **Cosmos DB**, **Azure Redis**, **Microsoft Fabric**, and any **provisioned throughput** model deployment (reserved capacity that you pay for whether or not you call it, as opposed to pay-per-token). If in doubt, ask me before creating it.

---

## 8. Exam traps and comparisons

| Pair | Difference | Trap |
| --- | --- | --- |
| **Subscription vs resource group** | Subscription = billing and access boundary. Resource group = lifecycle container inside it | Cost and credit belong to the subscription, not the group. But you can create a budget scoped to a group |
| **Budget vs credit** | Budget = *notification* you configure. Credit = *prepaid balance* | Neither one stops spending by itself on a normal subscription. Delete resources to stop spending |
| **Alert vs limit** | Alert tells you. A limit blocks | "Budget stops the deployment at 100%" is a wrong answer on any Azure exam |
| **Actual vs forecasted alert** | Actual = already spent. Forecast = predicted to exceed | If a question says "warn *before* the overspend happens", the answer is forecast |
| **Pay-per-use vs provisioned** | Per-use bills when you call. Provisioned reserves capacity and bills for it regardless | The classic "looks cheap but bills while idle" distractor. This returns in model deployment questions |
| **Region of the group vs region of resources** | Group region = where its *metadata* lives. Resources can be elsewhere | A "resource group must be in the same region as its resources" option is wrong |

---

## 9. Connections across your track

- **AZ-900:** the hierarchy in section 1 (tenant, subscription, resource group, resource) and Cost Management are AZ-900 topics. Same ideas, now with your hands on them.
- **AI-900:** responsible AI, which you studied there, becomes a configuration task in AI-103 (content filters). That is Week 1, option C.
- **Your real work this term:** every lab in the 6-week plan will run inside a subscription you now understand, with a resource group you delete afterward. The $100 is the budget for the whole exam prep, so this routine is what lets you finish all six weeks.

---

## 10. Key takeaways

1. Credit and cost belong to the **subscription**. Resource groups are folders you can delete in one go.
2. Resources cost money **while they exist**. Deleting the resource group is the only reliable way to stop spending.
3. Budgets **notify, they do not stop**, and the data is 8-24 hours late.
4. Where you check your credit depends on your offer. For Azure for Students it is the **Azure Sponsorships portal**.
5. Azure for Students: $100, 12 months, subscription **disabled** when either runs out. Do not upgrade to pay-as-you-go unless you accept real charges.
6. Per-lab resource group + teardown + check the balance is the routine for the whole track.
7. For the exam, the ideas matter (hierarchy, cost footprint, per-use vs provisioned), not the click-paths.

---

## 11. Practice questions (AI-103 style)

*Foundation level: the real exam tests these ideas lightly, so I mixed in concepts that will recur in the cost, quota and deployment questions.*

**Q1 (easy).** A team creates a Foundry project and several resources in one resource group for a two-week proof of concept. When the proof of concept ends, they want to remove everything and stop all charges with the fewest steps. What should they do?
- A. Delete each model deployment, then pause the subscription
- B. Delete the resource group
- C. Set a budget of $0 on the resource group
- D. Move the resources to a different region

**Q2 (medium).** An administrator creates a budget on the subscription with an alert at 100% of $500 per month. A developer then leaves an expensive resource running and spending accelerates. What will the budget do when the threshold is reached?
- A. Automatically stop all resources in the subscription
- B. Delete the resources that caused the overspend
- C. Send a notification, and resources keep running unless you set up separate automation
- D. Prevent new deployments until the next month

**Q3 (medium).** A finance lead wants to be warned *before* monthly spending exceeds the budget, based on the current trend. Which budget alert should be configured?
- A. An actual-cost alert at 100%
- B. A forecasted-cost alert
- C. A resource lock on the resource group
- D. A policy that denies deployments

**Q4 (difficult, cumulative).** You deploy a model with **provisioned throughput** for a chatbot used only during business hours. After a month, your cost is far higher than the number of requests suggests. Which explanation is most likely?
- A. Provisioned deployments bill only per token, and token prices rose
- B. Provisioned capacity is reserved and billed whether or not requests arrive
- C. Resource groups charge a monthly fee for each deployment
- D. Budgets automatically increase capacity when spending is low

---

### Answers and diagnosis

**Q1: B.** A resource group is the lifecycle container. Deleting it deletes everything inside, which also stops the charges.
- A is wrong: there is no "pause the subscription" step that removes resources, and you would keep paying for anything not deleted.
- C is wrong: a budget only notifies. It cannot remove anything, and it would not stop cost.
- D is wrong: moving resources changes where they live, not what they cost.

**Q2: C.** Per Microsoft, a budget notification does not affect resources and does not stop consumption. Stopping things requires separate automation (for example an action group tied to the budget).
- A and B are wrong for the same reason: budgets do not take actions on their own.
- D is wrong: budgets do not gate deployments. A *policy* can, but that is a different feature.

**Q3: B.** Forecasted alerts use predicted cost and fire when the projection will exceed the threshold, so they warn early.
- A is wrong: actual alerts fire only after the money is already spent.
- C is wrong: locks prevent deleting or modifying resources and have nothing to do with spend.
- D is wrong: a deny policy blocks deployments but does not warn about spending trends.

**Q4: B.** Provisioned throughput reserves capacity, and you pay for the reservation regardless of traffic. Pay-per-token is the other model, billed on usage. (Check current billing details on the pricing page; the concept is what the exam tests.)
- A is wrong: provisioned billing is not per token, and that is the whole point of the contrast.
- C is wrong: resource groups themselves are free containers.
- D is wrong: budgets never change capacity.

---

## 12. Sources

- Study guide for Exam AI-103: Developing AI Apps and Agents on Azure (skills measured as of April 16, 2026): https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-103
- Tutorial: Create and manage budgets (Microsoft Cost Management): https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/tutorial-acm-create-budgets
- Manage resource groups in the Azure portal: https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-portal
- Why is my Azure for Students subscription disabled (credit balance and Sponsorships portal): https://learn.microsoft.com/en-us/azure/cost-management-billing/manage/azurestudents-subscription-disabled
- Microsoft Q&A, charges on a student account (Premium SSD example): https://learn.microsoft.com/en-us/answers/questions/1285224/charges-gradually-increasing-on-the-student-accoun

*Not verified in this file: the exact current prices of any service, your specific offer's rules (if it is not Azure for Students), and whether Budgets is available on your offer type.*
