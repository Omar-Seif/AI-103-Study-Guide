# Week 1 · Module 1: Plan and prepare to develop AI solutions on Azure

**Exam:** AI-103 (Developing AI Apps and Agents on Azure) · **Source:** Microsoft Learn module (introduction, What is AI, Microsoft Foundry, Foundry Tools, Developer tools and SDKs, Responsible AI, Exercise) · **Written:** Oct 30, 2026

---

## 0. Exam relevance (read this first)

I re-checked the official AI-103 study guide today. The skills-measured version is still **April 16, 2026** (page last updated April 14, 2026), and the five domains and weights are unchanged:

| Domain | Weight |
| --- | --- |
| Plan and manage an Azure AI solution | 25-30% |
| Implement generative AI and agentic solutions | 30-35% |
| Computer vision | 10-15% |
| Text analysis | 10-15% |
| Information extraction | 10-15% |

A Microsoft Q&A thread from Aug 4, 2026 says AI-103 is out of beta, but that is a community answer plus an AI-generated reply, not an official announcement. Confirm on the official exam page before you book.

**What this module maps to**

| Module topic | Study-guide objective it supports | Yield |
| --- | --- | --- |
| Foundry resource vs project, endpoints | "Design Azure infrastructure for AI apps and agent-based solutions"; "Configure an application to connect to a Foundry project" | **High.** The vocabulary appears in many later questions |
| Foundry Tools (Language, Speech, Translator, Document Intelligence, Content Understanding) | "Choose an appropriate model for each task, including ... Foundry Tools"; also the vision, text and extraction domains | **High.** Choosing the right tool is a recurring scenario type |
| Developer tools and SDKs | "Integrate generative workflows into applications by using Foundry SDKs and connectors" | **Medium** |
| Responsible AI principles | "Implement responsible AI across generative AI and agentic systems" | **Medium.** The study guide asks you to *configure* safety filters and guardrails; the six principles are the vocabulary behind that. I cannot confirm how often the principle names themselves are tested |
| The exercise (portal click-path) | "Configure model and agent deployments" | **Low for clicks, high for what each step means** |

**Skim:** the Visual Studio vs VS Code comparison and the GitHub Copilot paragraph. Know they exist; the exam is not about them.

---

## 1. Foundations, from the ground up

You told me LLM basics, Python SDKs and authentication feel fuzzy, so every word below is defined before it is used. Skip a block only if it is already solid.

### 1.1 Model, LLM, prompt, token

- A **model** is a program whose behavior was learned from data instead of hand-written rules. You give it input and it returns a prediction.
- A **large language model (LLM)** is a model trained on huge amounts of text. Given text, it predicts what text should come next, which is why it can answer questions, summarize and write code.
- A **prompt** is the input text you send. In chat apps it usually has two parts: **instructions** (a "system" message that sets the role and rules, such as "You are an AI assistant that gives advice about AI software development") and the **user message** (the actual question).
- A **token** is a small chunk of text (a word or part of a word). Models read and write in tokens, and many services charge by the token. This is why "cost per use" depends on how much text you send and receive.

### 1.2 Deployment, endpoint, API, SDK

Think of a restaurant.

| Term | Plain meaning | Restaurant version |
| --- | --- | --- |
| **Model** | The trained brain, listed in a catalog | A recipe in a cookbook |
| **Deployment** | A running copy of that model, set up in *your* project so you can call it | The recipe actually being cooked in your kitchen |
| **Endpoint** | The web address your code sends requests to | The kitchen's order window |
| **API** | The agreed rules for what a request must look like and what comes back | The menu and ordering rules |
| **SDK** | A code library (for example in Python) that wraps the API so you write one function call instead of building web requests by hand | A waiter who takes your order in the right format |
| **Authentication** | Proving who you are before the service answers | Showing your reservation |

Why deployment matters: the model catalog only *describes* models. Nothing answers you until you **deploy** one.

### 1.3 Authentication in two sentences (full deep dive comes in the Entra ID lessons)

- A **key** is a secret string that acts like a password; anyone holding it can call the service.
- **Microsoft Entra ID** (token-based authentication) proves identity by *who or what you are* (a signed-in user or an application identity) and issues short-lived tokens instead of a permanent secret.

This module says the key is fine for learning, but "in most production scenarios you should consider using Microsoft Entra ID authentication based on authenticated user and application identities." Remember that sentence; it is Week 1's checkpoint.

### 1.4 What an agent is (just enough for this module)

An **agent** (the module's definition) combines an LLM with **instructions** that define its responsibilities and **tools** it can use to find knowledge or automate tasks. In Foundry, an agent is a *named AI configuration* that bundles an LLM, instructions and tools.

---

## 2. The five AI capabilities

The module's first unit lists five kinds of capability. Deciding which you need is step one of any project, because it tells you which services to provision.

| Capability | What it does | Example from the module / typical use |
| --- | --- | --- |
| **Generative AI and agents** | LLMs generate original responses; agents add instructions and tools | Chat apps, AI-assisted content creation, task-automating agents |
| **Natural language processing (NLP)** | Understands language in text. Many tasks can now be done by LLMs, but specialized text analysis can still benefit from statistical NLP and task-specific models | Text classification, sentiment analysis, summarization |
| **Computer speech** | Speech to text and text to speech | Voice assistants, transcription, "read aloud", simultaneous translation |
| **Computer vision** | Accepts and interprets images, video and live camera streams; multimodal models can also *generate* images and video | Automated grocery checkout identifying products in a basket |
| **Information extraction** | Pulls key information out of documents, forms, images and recordings | Expense claims app extracting dates, line items and totals from a scanned receipt |

**Exam trap:** NLP vs generative AI. The module says an LLM *can* do many NLP tasks, but that does not make it the best choice. It also says prebuilt tools can be "more cost-effective and predictable" than relying on generative agents alone (section 4).

---

## 3. Microsoft Foundry: the platform

### 3.1 What it is

Microsoft Foundry is Azure's platform for AI development. You *can* provision individual AI resources and build apps against them without Foundry, but the module calls Foundry "the recommended way to build all but the most simple solutions." It gives you two ways in:

- the **Foundry portal**: a web interface (https://ai.azure.com)
- the **Foundry SDK**: code, so you can script and automate (including CI/CD pipelines)

### 3.2 Resource vs project (the most important idea in this module)

```
Azure subscription                       <- where cost and credit attach
  └── Resource group                     <- lifecycle folder (you delete this to clean up)
        └── Foundry resource             <- the Azure resource: compute, storage, AI tools, central user access
              ├── Project  (default)     <- your workspace for one solution
              ├── Project  (optional)    <- a resource can have one or more child projects
              └── Foundry Tools          <- hosted in the Foundry resource
```

- Each **project belongs to a single Foundry resource**, and a Foundry resource can support **one or more** child projects. The first project becomes the **default project**.
- The **resource** is the Azure-level thing (it shows up in your resource group). The **project** is where you manage the assets for one solution.
- The module and the exercise say the resource provides a central place to **manage user access** to projects.

**What lives in a project (module's list):**

| Asset | What it is |
| --- | --- |
| **Models** | Deployments of models from the **Foundry Models** catalog (Microsoft, OpenAI and other providers) |
| **Agents** | Named configurations of an LLM + instructions + tools; developed and consumed through the **Foundry Agent service** via the project endpoint |
| **Tools** | Built-in (web search, code interpreter) or connections to custom and third-party tools through **Model Context Protocol (MCP)**; plus **Foundry Tools** (section 4) |
| **Knowledge** | Data stores agents use to contextualize prompts; **Foundry IQ** gives you a single, central, MCP-based knowledge connection across multiple sources |

### 3.3 The three things you find on the project home page

The exercise has you note a **key**, a **project endpoint** and an **Azure OpenAI endpoint**. They are easy to mix up:

| Item | Used for |
| --- | --- |
| **Project endpoint** | Accessing models directly in Foundry (including OpenAI models) using the **OpenAI Responses API**, and **Foundry-specific APIs** such as the **Foundry Agent service** |
| **Azure OpenAI endpoint** | Accessing models using **OpenAI APIs and SDKs**, including the **Chat Completions API** and the **Responses API** |
| **Key** | **Key-based authentication** to models and tools |
| **Resource endpoint** (on the Manage page, parent resource) | Resource-level functionality such as **Foundry Tools shared across all projects** in the resource |

**Trap to remember:** "Which endpoint do I use for X?" Agents and Foundry-specific APIs go through the **project endpoint**; OpenAI SDK chat apps go through the **Azure OpenAI endpoint**; shared tool services sit at the **resource** level. Both endpoint types can serve the Responses API, so do not choose by that alone.

---

## 4. Foundry Tools (prebuilt AI services)

These are "out-of-the-box prebuilt APIs and models" for common tasks. The module's argument: using them can give a **more cost-effective and predictable** solution than relying on generative agents alone.

| Tool | What it does | Use it when |
| --- | --- | --- |
| **Azure Language** | Entity extraction, sentiment analysis, summarization; also conversational language models and question answering | You need to analyze text for fixed, repeatable outputs |
| **Azure Speech** | Text to speech, speech to text, real-time live speech for conversational apps and agents | Voice in or out |
| **Azure Translator** | Translates text between many languages | Language translation |
| **Azure Document Intelligence** | Prebuilt or custom models to extract fields from invoices, receipts and forms | Structured fields from documents |
| **Azure Content Understanding** | Multimodal analysis: build models to extract data from forms and documents, images, videos and audio streams | Information from mixed content types |

**How you call them:** write a client app that connects to the **tool-specific endpoint** in your Foundry resource, authenticating with the **project key** or **token-based authentication**. Some have a test UI in the portal. They are hosted in the Foundry resource, so they are shared by its projects.

**Names change.** Older material calls these "Azure AI services" or "Cognitive Services". The AI-103 study guide still links to docs under the older names in places. If a service name looks unfamiliar in a practice question, check whether it is an older name for something in this table.

**Content Understanding vs Document Intelligence:** the two overlap for documents (the table reads that way on its own terms). Do not try to separate them yet; Week 5 is where we compare them properly.

---

## 5. Developer tools and SDKs

- **Foundry Toolkit extension for VS Code** (the module's tool): browse and manage project resources (deployed models, agents, connections, vector stores), deploy models from the catalog, test in integrated playgrounds, configure declarative and hosted agents with a visual designer or YAML, and generate integration code.
- **GitHub and GitHub Copilot:** source control, plus an AI coding assistant. Low exam yield.
- **Languages:** C#, Python, Node, TypeScript, Java and others. The study guide says you should have experience developing apps with **Python**, so that is your language.

**Which SDK for which job** (the module lists three families):

| If you need to... | Use |
| --- | --- |
| Connect to a Foundry project and use Foundry-specific assets such as agents and Foundry IQ knowledge stores | **Microsoft Foundry SDK** |
| Build chat apps against Foundry models that support OpenAI syntax | **OpenAI API / OpenAI SDKs** |
| Use a specific Foundry Tool (Language, Speech, Document Intelligence...) | **Foundry Tools SDKs** (or their **REST APIs**) |

---

## 6. Responsible AI: Microsoft's six principles

You met these in AI-900. The module repeats them because AI systems are probabilistic, depend on training data, and sound human, which can lead users to over-trust them.

| Principle | One-line meaning | The module's example or key point |
| --- | --- | --- |
| **Fairness** | Treat all people fairly | A loan-approval model must not include bias based on gender, ethnicity or other factors; tooling alone is not enough, so review training data and check performance for *subsections* of your users throughout development |
| **Reliability and safety** | Perform reliably and safely | Autonomous vehicle or diagnosis system; rigorous testing and deployment management; apply appropriate **thresholds** to confidence scores |
| **Privacy and security** | Be secure and respect privacy | Models rely on large data that may contain personal details; protect data and customer content, including in production |
| **Inclusiveness** | Empower everyone | Benefit all parts of society regardless of physical ability, gender, sexual orientation, ethnicity or other factors; include diverse input in design, development and testing |
| **Transparency** | Be understandable | Tell users the purpose, how it works and limitations; share factors affecting accuracy and **confidence scores**; if personal data is used, say how it is used, retained and who can access it |
| **Accountability** | People are accountable | Developers are responsible for the overall system meeting requirements; work within a governance framework |

**The three confusable pairs (exam favorites in AI-900 style)**

| Pair | Tell them apart |
| --- | --- |
| **Fairness vs Inclusiveness** | Fairness = *not disadvantaging* a group in the system's decisions. Inclusiveness = *designing so everyone can use and benefit*, including people with disabilities |
| **Transparency vs Accountability** | Transparency = users can *understand* the system and its limits. Accountability = a *person or organization is responsible* and governance exists |
| **Reliability and safety vs Privacy and security** | Reliable/safe = it works correctly and does not cause harm. Privacy/security = data is protected and not exposed |

**Connecting the principles to what you build this week (my mapping, not an official one):**

- Content filters on inputs and outputs → mostly **reliability and safety**.
- Entra ID instead of shared keys, and keeping keys out of code → **privacy and security**.
- Model cards describing capabilities and limits → **transparency**.
- Approval gates and logging on agents (Weeks 3 and 4) → **accountability**.

In AI-103 these principles become *configuration work*: the study guide's responsible AI objectives are about safety filters, guardrails, risk detection, evaluators, trace logging, approval workflows and tool-access controls.

---

## 7. The exercise: "Prepare for an AI development project" (about 30 minutes)

### 7.1 Before you start

The exercise lists prerequisites: an active Azure subscription, Visual Studio Code, **Python 3.13.xx** (3.14 exists but some dependencies are not compiled for it; the lab was tested on 3.13.12), Git, and the Azure CLI. This lab itself only uses the portal and the VS Code extension, but later labs need the rest.

**Cost and cleanup, before you click anything:**

- Put the lab in **its own resource group** (the exercise has you create or select one). Name it like `rg-ai103-w1-lab1`.
- When you deploy the model, **look at the deployment type and any pricing shown in the dialog** before accepting the default. I have not verified how this model is billed, so do not rely on my guess. Tell me what the dialog shows.
- Chat only a few times in the playground. Each message costs tokens.
- Your credit is $100 and I still need the **Expiration date** from Azure credits (see the setup file). Please send it.
- **Never paste an API key into this chat or into any file you send me.** Keys are secrets.

### 7.2 Steps and what each one teaches

| # | What you do | Why it matters |
| --- | --- | --- |
| 1 | Open https://ai.azure.com, sign in with the account that holds your credit, close any tip panes, turn on the **New Foundry** toggle | The exercise says to enable it if it is not already enabled |
| 2 | Create a project with a unique name. Expand **Advanced options**: keep the default Foundry resource name (`{project_name}-resource`), pick your subscription, **create or select a resource group**, and pick a region from the **AI Foundry recommended regions** list. **Write the region down.** | This creates *both* a Foundry resource and a project under it (section 3.2). The region matters later for model availability. If your student subscription restricts regions and the picker rejects one, choose one it allows and tell me which |
| 3 | On **Discover → Models**, search for **gpt-5.2**, open its **model card** | A model card describes capabilities and limitations, which helps you decide if the model suits your requirements (a transparency practice). If gpt-5.2 is not listed or not deployable for you, tell me what you see; do not substitute silently |
| 4 | Click **Deploy** with the default settings | A **deployment** is what lets you work with the model in your project (section 1.2) |
| 5 | In the playground, enter instructions: *You are an AI assistant that can provide information and advice about AI software development.* Ask: *Describe three key considerations for working with Large Language Models for AI application development.* | The instructions box is the system message from section 1.1 |
| 6 | Top menu → **Manage** → open the **parent resource** | Shows the resource level vs the project level, and the **resource endpoint** for shared Foundry Tools |
| 7 | Back on **Home**, find the **key, project endpoint and Azure OpenAI endpoint** | These are what client code uses to connect (section 3.3) |
| 8 | In VS Code, install the **Foundry Toolkit** extension, connect to Azure, set your project as the **default project**, view the deployed model, and open the **Model playground** | The same project, reached from your editor |
| 9 | **Clean up:** in https://portal.azure.com open the resource group, click **Delete resource group**, type its name to confirm | Deleting the group removes everything inside. This is the habit from the setup file |

After step 9, check the group is gone, and check Azure credits the next day to confirm usage stayed near $0.

**Soft delete (check, do not assume):** some Azure AI resources are kept for a recovery period after deletion and may hold their name until purged. I have not verified whether your Foundry resource does this. If you try to recreate a project with the same name and it complains, tell me the exact message.

### 7.3 Lab notes to fill in (for your archive)

- Region chosen: ______
- Project name / resource name: ______
- Deployment type shown in the deploy dialog: ______
- Which endpoint did the playground use? (Do not record the key.) ______
- Anything that differed from the exercise text: ______

---

## 8. Exam traps and comparisons

| Pair | Difference | Trap |
| --- | --- | --- |
| **Foundry resource vs project** | Resource = the Azure resource (compute, storage, tools, user access). Project = your workspace for one solution | A resource can have **one or more** projects; "one resource per project is required" is wrong |
| **Project endpoint vs Azure OpenAI endpoint** | Project endpoint: Foundry-specific APIs and agents. Azure OpenAI endpoint: OpenAI APIs and SDKs | Both can serve the Responses API; the *agent service* is the project endpoint |
| **Key vs Entra ID** | Key = a shared secret. Entra ID = identity-based tokens | Keys are fine to learn with; production guidance leans to Entra ID |
| **Model catalog vs deployment** | Catalog lists models. Deployment makes one callable in your project | Nothing answers until you deploy |
| **Foundry Tools vs a generative agent** | Prebuilt, task-specific, cost-effective and predictable vs flexible but costlier and less predictable | "Use an agent for everything" is a tempting distractor when a prebuilt tool fits |
| **Foundry IQ vs a single knowledge store** | Foundry IQ = one central MCP-based connection over several knowledge sources | The module names it as the simplification for multiple sources |
| **Fairness / Inclusiveness; Transparency / Accountability; Reliability-safety / Privacy-security** | See section 6 | These pairs are the classic confusion |

---

## 9. Connections across your track

- **AZ-900:** subscription → resource group → resource is exactly how a Foundry resource sits in Azure. Cost and credit attach to the subscription; the resource group is the delete-everything unit.
- **AI-900:** the six responsible AI principles are the same list. In AI-103 they turn into things you configure (filters, evaluators, approvals).
- **Your own project work:** you already use Python and have built LLM apps. Foundry gives those apps an Azure-hosted model deployment, a project, and identity-based access, which is what AI-103 tests.
- **Next in the plan:** Entra Agent ID, Bicep, content safety and model selection (Weeks' Units 3, 4, 5) all build on the project/resource/endpoint structure above.

---

## 10. Key takeaways

1. Pick the **capability** first (generative, NLP, speech, vision, extraction); it tells you which services you need.
2. A **Foundry resource** is the Azure resource; a **project** is the workspace under it. One resource can have one or more projects, with the first as the default.
3. A project holds **models, agents, tools and knowledge**. Foundry IQ gives one central MCP-based knowledge connection.
4. **Deploy** a model before you can use it. The **project endpoint** serves Foundry-specific APIs and agents; the **Azure OpenAI endpoint** serves OpenAI APIs and SDKs.
5. **Foundry Tools** (Language, Speech, Translator, Document Intelligence, Content Understanding) are prebuilt, cost-effective and predictable; use them before reaching for a generative agent.
6. Three SDK families: **Foundry SDK**, **OpenAI SDK/API**, **Foundry Tools SDKs/REST**.
7. Keys are for learning; production leans toward **Entra ID** identity-based access.
8. Six responsible AI principles: fairness, reliability and safety, privacy and security, inclusiveness, transparency, accountability. Know the three confusable pairs.
9. Clean up the lab by **deleting the resource group**.

---

## 11. Practice questions (AI-103 style)

**Q1 (easy).** A bank's loan-approval model is found to reject applicants from one ethnic group more often than others with similar finances. Which responsible AI principle is most directly violated?
- A. Reliability and safety
- B. Fairness
- C. Inclusiveness
- D. Transparency

**Q2 (easy).** Two teams in your company want separate workspaces for their own AI solutions, but you want them to share the same Foundry resource and its central user-access management. What should you do?
- A. Create a separate Azure subscription for each team
- B. Create a second project in the same Foundry resource
- C. Create a new Foundry resource for each team, because each resource supports only one project
- D. Create one project and have both teams share it

**Q3 (medium).** You want your app to create and run an agent by using the Foundry Agent service. Which endpoint should the app connect to?
- A. The project endpoint
- B. The Azure OpenAI endpoint
- C. The Speech tool endpoint
- D. No endpoint; agents run only in the portal

**Q4 (medium).** An expense-claims app must extract the purchase date, line items and total from scanned receipts. The team wants a cost-effective and predictable solution rather than a general-purpose agent. Which Foundry Tool fits best?
- A. Azure Translator
- B. Azure Speech
- C. Azure Document Intelligence
- D. An agent that uses the web search tool

**Q5 (medium).** An agent needs to draw on several different knowledge sources. You want a single, central connection instead of wiring up each source separately. What should you use in the project?
- A. Foundry IQ
- B. A separate Foundry resource per source
- C. Azure Translator
- D. The code interpreter tool

**Q6 (difficult, cumulative).** You finish a one-hour practice lab in which you created a Foundry resource, a project and a model deployment inside one resource group. You set a budget alert at 100% of your credit, but you want to be sure nothing keeps spending. What is the most reliable action, with the fewest steps?
- A. Rely on the budget alert; it stops resources when the threshold is reached
- B. Delete only the project in the Foundry portal
- C. Delete the resource group
- D. Close Visual Studio Code

---

### Answers and diagnosis

**Q1: B (Fairness).** The model treats a group worse in its decisions. That is the module's own loan-approval example.
- A is wrong: reliability and safety is about working correctly and avoiding harm, such as testing and confidence thresholds.
- C is wrong: inclusiveness is about designing so everyone can use and benefit, including people with disabilities. The scenario is biased *decisions*, not accessibility.
- D is wrong: transparency is about users understanding the system and its limits.

**Q2: B.** A Foundry resource supports one or more child projects, and the resource is the central place to manage user access.
- A is wrong: a separate subscription is unnecessary and splits billing.
- C is wrong: the premise is false; one resource can host multiple projects.
- D is wrong: sharing one project does not give each team its own workspace.

**Q3: A.** Agents in Foundry are developed and consumed through the Foundry Agent service via the **project endpoint**.
- B is wrong: the Azure OpenAI endpoint is for OpenAI APIs and SDKs (such as Chat Completions).
- C is wrong: the Speech tool is a separate Foundry Tool for speech tasks.
- D is wrong: agents are consumed through the service, not only the portal.

**Q4: C.** Document Intelligence uses prebuilt or custom models to extract fields from receipts, invoices and forms. It is also the module's own receipt example for *information extraction*.
- A is wrong: Translator converts text between languages.
- B is wrong: Speech handles audio, not scanned images.
- D is wrong: web search is a built-in agent tool that finds information online; it does not extract fields from your scans, and an agent is the less predictable, more costly route the scenario wants to avoid.
- *Note:* Content Understanding also extracts from documents. It is not offered here on purpose; we compare it with Document Intelligence in Week 5.

**Q5: A.** Foundry IQ creates a single, central, MCP-based knowledge connection across multiple sources.
- B is wrong: separate resources add administration, which is the opposite of the goal.
- C is wrong: Translator translates text.
- D is wrong: the code interpreter is a built-in tool for running code, not a knowledge connection.

**Q6: C.** Deleting the resource group removes everything inside, including the Foundry resource, so nothing remains to bill. This is also how the exercise says to clean up.
- A is wrong: from your setup file, budgets **notify**; they do not stop consumption or delete resources.
- B is wrong: the exercise does not clean up by deleting only the project. The parent Foundry resource lives in the resource group, and I have not verified that deleting the project removes the resource or its model deployment. Delete the group instead.
- D is wrong: closing VS Code only closes your editor. The deployed resources are in Azure.

---

## 12. Sources

- Module text supplied by you (Plan and prepare to develop AI solutions on Azure: introduction, What is AI, Microsoft Foundry, Foundry Tools, Developer tools and SDKs, Responsible AI, Exercise).
- [Study guide for Exam AI-103: Developing AI Apps and Agents on Azure](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-103) (skills measured as of April 16, 2026; rechecked Oct 30, 2026).
- [Microsoft Q&A: Is AI 103 Out of BETA phase?](https://learn.microsoft.com/en-us/answers/questions/5965794/is-ai-103-out-of-beta-phase) (community answer, Aug 4, 2026; not official).

*Not verified in this file:* billing or deployment type of gpt-5.2 in your region, whether a deleted Foundry resource is soft-deleted, and the exact current names of every older "Azure AI services" product.
