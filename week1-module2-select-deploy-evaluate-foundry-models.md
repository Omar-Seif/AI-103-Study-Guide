# Week 1 · Module 2: Select, deploy, and evaluate Microsoft Foundry models

**Exam:** AI-103 (Developing AI Apps and Agents on Azure) · **Source:** Microsoft Learn module (Introduction, Explore the model catalog, Select models using benchmarks, Deploy models to endpoints, Evaluate model performance, Exercise) · **Written:** Oct 30, 2026

---

## 0. Exam relevance (read this first)

I checked the AI-103 study guide earlier today (Oct 30): skills are still "as of April 16, 2026" and the domain weights are unchanged. This module is one of the **highest-yield** ones in Week 1.

| Module topic | Study-guide objective it supports | Yield |
| --- | --- | --- |
| Model catalog, LLM vs SLM, model types | "Choose an appropriate model for each task, including large language models (LLMs), small language models, multimodal models, and Foundry Tools" | **High** |
| Benchmarks (quality, safety, cost, performance) | Same objective, plus "Manage ... cost footprints for model and agent workloads" | **High** |
| Deployment types (Standard, Provisioned, Batch; Global, Data Zone, Regional) | "Choose appropriate deployment options"; "Configure model and agent deployments"; "Manage quotas, scaling, rate limits, and cost footprints" | **High**. Scenario questions pick a deployment type from a requirement |
| Playground parameters (temperature, max tokens, top-p) | "Tune generation behavior, such as prompt engineering and adjusting model parameters" | **Medium** |
| Evaluation (manual, AI-assisted, NLP metrics, evaluator library) | "Evaluate models and apps, including detecting fabrications, relevance, quality, and safety"; "Apply responsible AI instrumentation, including evaluators, safety evaluations" | **High** |
| Iterating on results (prompting, RAG, fine-tuning, content filters) | "Optimize and operationalize generative AI systems"; "Configure safety filters, guardrails..." | **Medium-high** |

**Skim or ignore:** the specific model names in the module (GPT-5, Mistral Large, Llama 3 70B, Phi-4, Claude Opus 4.6, GPT-image-1, Sora 2 and so on) are *examples*. Model lineups change, and I would not memorize names. Learn the categories.

---

## 1. Foundations, from the ground up

Every term below is used later. Skim what you already know.

### 1.1 Benchmarks and indexes

- A **benchmark** is a standard test: a fixed set of questions with known answers, run against many models, so scores are comparable.
- A **dataset** is the collection of test cases. Each benchmark in this module is named after its dataset (for example MMLU-Pro).
- An **index** is a single summary number combining several scores. The **Quality index** averages accuracy across several benchmark datasets. The module says scores are normalized to a range from **zero to one**, higher is better.

### 1.2 Speed words

| Word | Meaning | Everyday version |
| --- | --- | --- |
| **Latency** | Time between sending a request and getting the response | How long the order takes |
| **Throughput** | How much work gets done per second | How many orders the kitchen handles per second |
| **Streaming** | The reply arrives token by token as it is generated | Dishes arriving one at a time |
| **Time to first token (TTFT)** | Wait until the first piece of the reply arrives (streaming) | Wait until the first dish appears |
| **Percentiles (P50, P90, P95, P99)** | "P90 = 2 s" means 90% of requests finish faster than 2 s | A "worst typical case" view; P99 shows the slow tail that an average hides |

### 1.3 Generation parameters (from the playground)

- **Temperature:** creativity vs consistency. Lower is more consistent; higher is more varied.
- **Max tokens:** the limit on response length.
- **Top-p (nucleus sampling):** another way to control how varied the output is. The module lists it without a deeper definition, so know it as a generation-tuning parameter.
- **System message / instructions:** text that sets context, tone and rules for every user input.
- **Context window:** how many tokens the model can keep in a conversation and produce as output (the comparison page shows this).

### 1.4 Deployment words

- **Pay-per-token:** you are billed for the tokens you send and receive (see tokens in Module 1).
- **Provisioned throughput unit (PTU):** capacity you *reserve* in advance, so throughput is predictable.
- **Asynchronous (batch):** you submit a big job and collect results later, instead of waiting for each answer.
- **Data residency:** where your data is processed and stored (a region, or a wider "data zone" such as US, EU or APAC).
- **Quota:** the cap on how much capacity your subscription may use.

### 1.5 Evaluation words

- **Ground truth:** the known correct answer for a test case.
- **Precision and recall:** precision = of the words (or items) the model produced, how many were right; recall = of the right ones that should have appeared, how many did it produce.
- **AI-assisted evaluation ("AI as a judge"):** you pick a GPT model that reads your model's answers and scores them against criteria.
- **Defect rate:** the percentage of responses that cross a bad threshold (details in section 5).

---

## 2. Exploring the model catalog

The catalog ("Foundry Models") lists **over 1,900 models** from providers such as Microsoft, Anthropic, OpenAI, Meta and Hugging Face.

### 2.1 Two billing categories

| Category | Billing and terms | Marketplace? |
| --- | --- | --- |
| **Foundry Models sold directly by Azure** | Billed directly through your Azure subscription. Includes Azure OpenAI models plus models from Microsoft and other providers | **Not required** (for example GPT-4o-mini) |
| **Foundry Models from partners and community** | Provided by trusted partners and the community, each with **its own licensing and pricing** | **Common**: you review the terms and select **Agree and Proceed** |

### 2.2 How you find models

Search by keyword, then filter by:

| Filter | Examples |
| --- | --- |
| **Collection** | Models provided directly in Azure; models in the Hugging Face repository |
| **Capabilities** | Reasoning, tool calling (API and function integration), multimodal (text, images, audio) |
| **Source** | Azure OpenAI, Microsoft, Cohere, Mistral, Meta, Anthropic... |
| **Inference tasks** | Text generation, summarization, translation, image generation, speech synthesis... |
| **Fine-tuning methods** | Techniques supported for fine-tuning |
| **Industry** | Models trained on industry-specific datasets, which "often outperform general-purpose models in their respective domains" |

Each **model card** shows provider, capabilities, benchmark metrics, responsible AI considerations and deployment options.

### 2.3 Model types

**Language models by size**

| | LLM | SLM |
| --- | --- | --- |
| Examples (from the module) | GPT-5, Mistral Large, Llama 3 70B | Phi-4, Mistral OSS models, Llama 3 8B |
| Best for | Deep reasoning, complex content generation, extensive context understanding | Common NLP tasks where **speed and cost matter more** than the hardest reasoning |
| Cost | More computational resources | Efficient and cost-effective |
| Hardware | Needs more | Can run on **lower-end hardware or edge devices** |

> This is half of your Week 1 checkpoint: *when does an SLM beat an LLM?* Try to answer it from memory before looking back.

**Chat completion vs reasoning models:** most models are **chat completion** models that generate coherent, contextual text and power conversational apps. **Reasoning** models give stronger problem-solving on complex tasks such as mathematics, coding, science, strategy and logistics; they break a problem down and can show their reasoning.

**Specialized models**

| Type | What it does | Typical use (from the module) |
| --- | --- | --- |
| **Embedding** | Converts text into numbers (vectors) so you can search by *meaning* | Semantic search, recommendations, **RAG** |
| **Image generation** | Creates images from text | Marketing materials, illustrations, mockups |
| **Video generation** | Creates video from text | Video content |
| **Image analysis (multimodal)** | Accepts text plus images and answers in text | Analyzing images with prompts |
| **Text to speech** | Synthesizes speech | Spoken output |
| **Speech to text** | Transcribes audio | Transcription |

**Regional and domain-specific models:** optimized for particular languages, regions or industries (for example medical literature or legal documents).

**Exam trap:** picking the biggest model by default. The exam rewards matching the model to the *task and constraints*. A simple summarization with tight cost and latency targets points to an SLM; a multi-step reasoning task points to an LLM or reasoning model; semantic search needs an **embedding** model, not a chat model.

---

## 3. Selecting models with benchmarks

### 3.1 Where to find them

1. **Model leaderboard** (from the catalog): comparative rankings, shown by quality, safety, estimated cost and throughput.
2. **Benchmarks tab** on a model card: that model's scores with comparison charts against similar models.

### 3.2 Quality

The **Quality index** averages accuracy across datasets that measure reasoning, knowledge, question answering, mathematics and coding. **Higher is better.**

| Dataset | What it measures |
| --- | --- |
| Arena-Hard | Adversarial question answering |
| BIG-Bench Hard | Reasoning |
| GPQA | Graduate-level multi-discipline questions |
| HumanEval+ and MBPP+ | Code generation |
| MATH | Mathematical reasoning |
| MMLU-Pro | General knowledge |
| IFEval | Instruction following |

### 3.3 Safety (watch the direction of each metric)

| Benchmark | Measures | Good direction |
| --- | --- | --- |
| **HarmBench** | How well a model resists generating unsafe content. Reported as **Attack Success Rate (ASR)**. Covers standard harmful behaviors (cybercrime, illegal activities, general harm), contextually harmful behaviors (misinformation, harassment, bullying) and copyright violations | **Lower ASR = safer** |
| **ToxiGen** | How well a model *detects* adversarial and implicit hate speech (references to minority groups) | **Higher F1 = better** |
| **WMDP** (Weapons of Mass Destruction Proxy) | Model knowledge in biosecurity, cybersecurity and chemical security | **Higher = MORE knowledge of potentially dangerous capabilities** |

> **Trap:** the three metrics point in different directions. Lower ASR is good. Higher F1 is good. A higher WMDP score is *not* "better"; it means the model knows more about dangerous domains.

### 3.4 Cost

- **Cost per input tokens:** price for processing **1 million** input tokens (what you send).
- **Cost per output tokens:** price for **1 million** output tokens (what the model writes).
- **Estimated cost:** combines the two using a typical **3:1 ratio** (three input tokens per output token). Lower is more cost-effective.

Cost benchmarks display pricing for **serverless API deployments and Azure OpenAI models**.

### 3.5 Performance

| Group | Metrics |
| --- | --- |
| **Latency** | Mean, P50 (median), P90, P95, P99, and time to first token (TTFT) |
| **Throughput** | Generated tokens per second (GTPS, output only), total tokens per second (TTPS, input plus output), time between tokens |

The leaderboard summarizes performance with **mean TTFT (lower is better)** and **mean generated tokens per second (higher is better)**. Interactive apps need low latency and high throughput. For **batch processing** where speed matters less, prioritize other factors such as cost.

### 3.6 The comparison tools

| Tool | What it gives you |
| --- | --- |
| **Leaderboard** | Sort by quality, safety, estimated cost, throughput |
| **Scenario leaderboards** | Rankings for specific cases: reasoning, coding, math, question answering, groundedness. **If your app fits a scenario, start here** instead of the overall quality index |
| **Trade-off charts** | Two metrics at once (quality vs cost, throughput or safety). **Models nearer the top-right corner do well on both** |
| **Side-by-side comparison** | Pick **two or three** models and compare benchmarks, model details (context window, training data, languages), supported endpoints and feature support (function calling, structured output, vision) |

**Trade-off thinking:** a model that is slightly less accurate but much faster or cheaper may serve you better. Benchmarks use public datasets, so they tell you which models are *worth testing*, not which will be best on *your* data. That is what evaluation (section 5) is for.

---

## 4. Deploying models to endpoints

### 4.1 Deployment types

The module groups them by **where the data and processing can be** (Global, Data Zone, Regional) and **how you pay or reserve** (Standard, Provisioned, Batch).

| | **Standard** (pay-per-token) | **Provisioned** (reserved PTUs) | **Batch** (async, within 24 h) |
| --- | --- | --- | --- |
| **Global** (any Azure region) | **Global Standard**: general workloads, **highest quota** | **Global Provisioned**: predictable high throughput | **Global Batch**: **50% discount**, large asynchronous jobs |
| **Data Zone** (stays within a data zone: US, EU or APAC) | **Data Zone Standard**: use when data zone compliance is required | **Data Zone Provisioned** | **Data Zone Batch** |
| **Regional** (single region) | **Regional Standard**: regional data residency, or low-volume scenarios | **Regional Provisioned** | *(the module lists none)* |

There is also a **Developer** deployment: any Azure region, pay-per-token, **for fine-tuned model evaluation only**.

Facts to remember:

- Each model shows which deployment types it supports.
- The portal automatically chooses the best option for your environment and the model.
- **Global Standard in Foundry resources should be used whenever possible for maximum capabilities.**

**Decision shortcuts**

| Requirement in the question | Pick |
| --- | --- |
| General workload, flexible, highest quota | **Global Standard** |
| Data must stay in a geography such as the EU or US | **Data Zone** (Standard if pay-per-token) |
| Data must stay in one region, or low volume | **Regional Standard** |
| Predictable, high, steady throughput (reserved capacity) | **Provisioned** (PTU) |
| Large non-urgent job, results within 24 h, lowest cost | **Batch** (Global Batch: 50% discount) |
| Evaluating a fine-tuned model only | **Developer** |

**Connection to your setup file:** "Provisioned = reserved capacity billed whether or not you send requests; Standard = pay-per-token." This is the pay-per-use vs provisioned trap from the setup file's practice question. It is the same idea with the real product names. **For your credits, Provisioned is the one to avoid in practice labs.**

### 4.2 How to deploy

1. **Discover → Models**, open the **model card** and review the specs and supported deployment types.
2. Select **Deploy**, then choose **Default settings** (quick, recommended) or **Custom settings**.
3. If the model needs an **Azure Marketplace subscription** (common for partner and community models), review the terms and select **Agree and Proceed**.
4. Set:
   - **Deployment name:** defaults to the model name. You can change it, which lets you create meaningfully named deployments of the same model.
   - **Deployment type:** chosen automatically from the model and your environment.
5. **Managed compute deployments** also need a **virtual machine SKU** (you need **Azure Machine Learning compute quota** for that SKU) and an **instance count** (load distribution and redundancy).
6. Select **Deploy**. You land in the **playground**. Verify the deployment status shows **Succeeded**.

### 4.3 Managing deployments

Go to **Build → Models** to see your deployments. A deployment's details page shows configuration and status, the **endpoint URL**, **authentication keys or tokens**, monitoring and usage metrics, and options to **adjust settings or delete** the deployment.

### 4.4 The three things your code needs

| Piece | Notes |
| --- | --- |
| **Endpoint URL** | Foundry supports **project endpoints** (Foundry-specific functionality) and **OpenAI v1 endpoints** (broad compatibility with OpenAI model APIs) |
| **Authentication** | A key or token. Or use **Microsoft Entra ID** and present a token based on the application's identity. **Entra ID is recommended for production** |
| **Deployment name** | Goes in the **`model` parameter** of API requests, which routes the request to *your* deployment |

> **Trap:** the `model` parameter takes the **deployment name**, not necessarily the catalog model name. If you rename the deployment, the code must use the new name.

### 4.5 The playground

Test without code: try simple questions, multi-step reasoning, specific formats, and **edge cases** that might expose limits. Adjust the **system message**, **temperature**, **max tokens** and **top-p**. The **Code tab** shows Python, C# and JavaScript samples with authentication, endpoint and request formatting that you can copy. The playground is your environment for **prompt engineering** before integration.

---

## 5. Evaluating model performance

### 5.1 Why

Quality assurance (find problems before production), user satisfaction, continuous improvement, and compliance and safety verification.

### 5.2 Manual evaluation

| Approach | What it is |
| --- | --- |
| **Interactive testing** | Explore the playground with varied prompts; note wrong facts, tone problems, ignored instructions. You can test models **side by side**, with synchronized system instructions and prompts |
| **Structured review** | A set of test cases; humans rate on relevance, informativeness, engagement, accuracy and safety, usually on a scale such as 1 to 5 |
| **User studies** | Feedback from real or representative users; shows confusing phrasing, missing context and unmet expectations |

Manual evaluation captures subjective things (user satisfaction, contextual fit, brand alignment) that metrics cannot.

### 5.3 Automated metrics, group 1: AI-assisted (you choose a GPT model as the judge)

**Generation quality**

| Metric | Checks |
| --- | --- |
| **Groundedness** | Whether responses are based on the provided context rather than speculation. **Groundedness Pro** gives a binary grounded / not grounded result, useful for factual-accuracy requirements |
| **Relevance** | Whether the response addresses the user's request |
| **Coherence** | Whether the response flows logically with consistent ideas |
| **Fluency** | Linguistic correctness and natural language quality |

**Risk and safety**

| Metric | Flags |
| --- | --- |
| Self-harm content | Responses discussing or encouraging self-harm |
| Hateful and unfair content | Bias, discrimination, hateful statements |
| Violent content | Containing or promoting violence |
| Sexual content | Inappropriate sexual content |
| Protected material | Possible copyright or proprietary content reproduction |
| Indirect attack (jailbreak) | Vulnerability to manipulation attempts |

**Defect rate**

- For the four **content harm** metrics: the percentage of responses exceeding a severity threshold (typically **Medium**).
- For **protected material** and **indirect attack**: (true instances ÷ total instances) × 100.

### 5.4 Automated metrics, group 2: NLP metrics (no evaluator model, often need ground truth)

| Metric | Idea | Typical task |
| --- | --- | --- |
| **F1-score** | Shared words between generated and ground-truth answers, balancing precision and recall | Text classification, information retrieval |
| **BLEU** | Compares n-grams (word sequences) between generated and reference texts | Machine translation |
| **METEOR** | Extends BLEU by accounting for synonyms, stemming and paraphrasing | Translation, with more flexible matching |
| **ROUGE** | Emphasizes **recall** over precision | **Summarization**, where covering key points matters |
| **GLEU** | A BLEU variant for **sentence-level** evaluation | Sentence-level evaluation |

NLP metrics work when you have definitive correct answers or reference texts. They are **less suitable for open-ended generation** where many answers are valid.

> **Trap:** "no ground-truth answers available" points to AI-assisted metrics. "Reference answers available and the task has a definite answer" points to NLP metrics.

### 5.5 Comprehensive evaluations in the portal

1. **Pick a target:** **Model** (a deployment with prompts you specify; outputs are generated during the evaluation), **Agent** (an agent's responses), or **Dataset** (outputs already present in your data).
2. **Provide a dataset:** upload a **CSV or JSONL** file, use an **existing dataset** in the project, or **generate a synthetic dataset** (you give a topic prompt, the number of rows and the resource that generates it; you can upload files to improve relevance).
3. **Choose metrics, field mappings and the system prompt.**
4. **Run the job.** It processes **asynchronously**, row by row; it may take some time.
5. **Review results:** aggregate scores plus per-prompt detail.

**Evaluator library** (project **Evaluation** page → **Evaluator library** tab): view Microsoft-curated evaluators (quality, safety, performance), check details and parameters, see annotation prompts for quality evaluators, see definitions and severity levels for safety evaluators, and manage **custom evaluators** with **version management** (compare, restore, collaborate).

### 5.6 Iterating

| If... | Try (in roughly this order of complexity) |
| --- | --- |
| **Scores are low** | **Prompt engineering** (refine instructions and system messages) → **try a different model** → **RAG integration** (ground responses in your data) → **fine-tuning** (if supported). Each step can grow in complexity and cost |
| **Safety metrics show concerns** | **Content filters** (Azure AI Content Safety), **prompt hardening** (safety instructions in system messages), **output validation** (check responses before showing them) |

Establish **evaluation benchmarks early**, then re-run after changes so quality does not regress.

---

## 6. The exercise: "Explore and compare models" (about 45 minutes)

### 6.1 How it ties to your Week 1 build

Your plan's hands-on step reads: *deploy one larger and one smaller model, compare them on 10 sample tasks for quality, speed and cost, write down when you would route to each.* This exercise gives you the first version: **gpt-5.2 (larger) vs gpt-5-mini (smaller)**. Later, on your own, you add the 10 tasks and call both from Python with Entra ID.

### 6.2 Before you start: cost and cleanup

- The exercise needs an Azure subscription with permission to create AI resources.
- You can reuse your Module 1 project if you have not deleted it yet, or create a new one. Either way, **delete the resource group when you finish**.
- **Before each Deploy, look at the deployment type and any price shown.** Per this module, Standard deployments are pay-per-token and Provisioned deployments are reserved PTUs. **Do not accept a Provisioned type** for these labs. I have not verified the price of gpt-5.2 or gpt-5-mini, or what the default type will be for you.
- **The evaluation is the most token-heavy step.** In order, the exercise generates 45 questions with gpt-5.2, gets 45 answers from gpt-5.2, and then runs AI-judge evaluators over each answer. I have not verified the cost of that. Check the leaderboard's cost numbers first. Fewer rows (for example 10 to 15) would cost less but give weaker results; that is your call.
- Write down the **deployment names**, because the exercise says you need to identify them later.
- **Never paste keys into this chat.**

### 6.3 Steps and what each one teaches

| # | What you do | What it teaches |
| --- | --- | --- |
| 1 | Create (or reuse) a Foundry project: Advanced options, default resource name, your subscription, a resource group, a recommended region | Resource vs project (Module 1) |
| 2 | **Discover → Models**, search **gpt-5.2**, open the model card, read **Details**, open **Benchmarks** | Model cards and the benchmarks tab (sections 2 and 3) |
| 3 | In the catalog, select **View leaderboard**; note the top quality models; use the **Trade-off chart** with **Benchmark Cost**, then **Throughput**, then **Safety**, comparing **gpt-5.2 and gpt-5-mini** | Trade-off charts: top-right is good on both axes |
| 4 | Select both models and use **Compare models**: performance, input/output formats, context, endpoints (and whether an agent can use the model), supported features | Side-by-side comparison |
| 5 | Deploy **gpt-5.2** with default settings; note its **deployment name** | Deployment (section 4) |
| 6 | In the playground **Model list → Browse more models**, deploy **gpt-5-mini**; note its name | A second deployment |
| 7 | With gpt-5-mini selected, use **Compare models** and pick the gpt-5.2 deployment. In both chat panes, enter the **fox, chicken and grain river-crossing** prompt, then the follow-up **"Explain your reasoning."** Compare accuracy, reasoning quality and style | Side-by-side manual evaluation (section 5.2) |
| 8 | **Evaluations** tab → **Create** → target **Model**, only **gpt-5.2** selected | Evaluation target (section 5.5) |
| 9 | Data: **Synthetic generation**, model gpt-5.2, **45 rows**, prompt *Create various travel related questions, and include some content safety and security tests* | Synthetic dataset |
| 10 | Configure models: developer prompt *You are a helpful travel assistant that provides accurate, detailed, and practical travel advice to help users plan their trips.* | The system prompt is part of the evaluation |
| 11 | Criteria: they use an AI model as a judge. **Remove all criteria under Agents and Safety**, leave the rest | AI-assisted generation-quality metrics (section 5.3) |
| 12 | Review, name it `travel-assistant-eval`, **Submit**, wait | Asynchronous job |
| 13 | Open the run, review scores, scroll right for per-row detail. **Analyze results** → gpt-5.2 → **Start analysis** to see failures clustered by cause and AI suggestions | Iterating on results (section 5.6) |
| 14 | **Delete the resource group** (type its name to confirm) | Cleanup |

**What to expect:** the exercise says you may see some failures, mostly because the model says it cannot help with questions that include safety and security tests. The point is to *judge whether that behavior is what you want*, not to chase a perfect score.

### 6.4 Lab notes to fill in

- Deployment names and deployment types shown: gpt-5.2 ______ / gpt-5-mini ______
- Benchmark readings: quality, estimated cost, throughput (higher/lower for each model): ______
- Fox/chicken/grain: which model was right? Which explained better? Which was faster? ______
- Evaluation: which evaluators ran, pass rate, what the failure clusters were: ______
- One thing in the results you would change in the prompt: ______
- Anything that differed from the exercise text: ______

---

## 7. Exam traps and comparisons

| Pair or topic | Difference | Trap |
| --- | --- | --- |
| **Sold directly by Azure vs partners/community** | Azure-sold: billed through your Azure subscription, no Marketplace. Partner/community: own licensing and pricing, often Marketplace terms | Assuming all catalog models bill the same way |
| **LLM vs SLM** | LLM: deep reasoning, more resources. SLM: faster, cheaper, can run on edge or lower-end hardware | "Always choose the largest model" |
| **Chat completion vs reasoning vs embedding** | Conversational text, hard multi-step problems, and vectors for semantic search | Using a chat model for similarity search |
| **HarmBench ASR vs ToxiGen F1 vs WMDP** | Lower ASR good; higher F1 good; higher WMDP = more dangerous knowledge | Treating "higher" as better on all three |
| **Estimated cost** | Combines input and output at a **3:1** ratio | Comparing only input price |
| **P50 vs P99; TTFT vs GTPS** | Median vs slow tail; first-token wait vs output speed | Looking only at the mean |
| **Standard vs Provisioned vs Batch** | Pay-per-token; reserved PTUs; async within 24 h at 50% off | "Provisioned is cheaper for low usage" |
| **Global vs Data Zone vs Regional** | Any region; stays in a zone (US, EU, APAC); single region | Global when compliance demands residency |
| **Deployment name vs model name** | The `model` parameter takes the deployment name | Passing the catalog name after renaming |
| **Developer deployment** | Fine-tuned model evaluation only | Using it as a production option |
| **AI-assisted vs NLP metrics** | AI judge, no ground truth needed vs mathematical, usually needs references | Using BLEU/ROUGE on open-ended chat |
| **BLEU / METEOR / ROUGE / GLEU / F1** | Translation; translation with synonyms; summarization (recall); sentence-level BLEU; word overlap | Mixing up which fits which task |
| **Groundedness vs relevance** | Based on the provided context vs addresses the question | An answer can be relevant but ungrounded |
| **Content safety metrics vs content filters** | Metrics *measure* problems in evaluation; filters (Azure AI Content Safety) *block* in operation | Calling an evaluation score a protection |
| **Fix order for low scores** | Prompting → different model → RAG → fine-tuning (growing complexity and cost) | Jumping to fine-tuning first |

---

## 8. Connections across your track

- **Setup file:** provisioned = reserved and billed regardless of use; Standard = per token; budgets notify, they do not stop spending. The deployment types here are where that idea appears with real names.
- **Module 1:** the **deployment** and **endpoint** words from the Foundry module are now fully used; the **project endpoint** in section 4.4 is the same project endpoint.
- **AI-900:** responsible AI becomes measurable here (safety benchmarks, defect rates).
- **Week 2 (RAG):** embedding models appear in section 2, and RAG shows up as a fix for low groundedness.
- **Week 4:** evaluation and tracing return with agent evaluation. This module already lists **Agent** as an evaluation target.
- **Week 1 course Unit 5** (model selection, LLMs vs SLMs): this module is the Learn companion for it.

### Checkpoint prompt for you (do it without notes)

1. When does an SLM beat an LLM?
2. Why use Entra ID instead of keys? (partly from Module 1 and this module's section 4.4)
3. Which deployment type would you pick for a nightly summarization job that must finish within 24 hours at the lowest cost, and why?

---

## 9. Key takeaways

1. The catalog has 1,900+ models. Azure-sold models bill through your subscription; partner and community models have their own licensing and often need Marketplace terms.
2. Match the model to the task: LLM for deep reasoning, SLM for speed and cost, embedding for semantic search, and reasoning models for hard problems.
3. Benchmarks cover quality (index 0 to 1, higher is better), safety (lower ASR good, higher F1 good, higher WMDP means more dangerous knowledge), cost (per 1M tokens, 3:1 estimate) and performance (latency percentiles, TTFT, tokens per second).
4. Use scenario leaderboards, trade-off charts (top-right is best) and side-by-side comparison (two or three models), but benchmarks use public data, so test on your own.
5. Deployment types combine scope (Global, Data Zone, Regional) with billing mode (Standard per token, Provisioned PTU, Batch async at 50% off). Global Standard is the default recommendation. Developer is for fine-tuned model evaluation only.
6. Your code needs the endpoint, authentication (Entra ID recommended for production) and the **deployment name** in the `model` parameter.
7. Evaluate with manual review, AI-assisted metrics (quality and safety) and NLP metrics (need ground truth). ROUGE suits summarization; BLEU and METEOR suit translation.
8. Fix low quality in order of effort: prompt, model, RAG, fine-tuning. Fix safety issues with content filters, prompt hardening and output validation, and re-run evaluations after every change.
9. Clean up by deleting the resource group.

---

## 10. Practice questions (AI-103 style)

**Q1 (easy).** A healthcare company must ensure its model's data is processed only within the EU, and it wants to pay per token with no reserved capacity. Which deployment type fits best?
- A. Global Standard
- B. Data Zone Standard
- C. Global Provisioned
- D. Global Batch

**Q2 (easy).** A company needs to summarize 20,000 archived documents once a month. Results can arrive any time within 24 hours, there is no data residency requirement, and the goal is the lowest cost. Which deployment type is the best fit?
- A. Global Standard
- B. Global Batch
- C. Regional Provisioned
- D. Developer

**Q3 (medium).** You compare two models for a customer-facing chatbot on the leaderboard's safety benchmark. On HarmBench, Model X has an Attack Success Rate of 4% and Model Y has 19%. What is the correct conclusion?
- A. Model Y is safer because a higher ASR means stronger protection
- B. Model X is safer because a lower ASR means it resists unsafe content better
- C. The ASR reflects cost, not safety
- D. The ASR only applies to image generation models

**Q4 (medium).** You are evaluating a summarization feature and have reference summaries written by experts. Which metric is best suited to check how well the generated summaries cover the key points?
- A. BLEU
- B. GLEU
- C. ROUGE
- D. Coherence

**Q5 (medium).** You deployed a model from the catalog and changed the deployment name to `support-bot-prod`. Your code sends requests with `model="gpt-5-mini"` and the request fails. What should the `model` parameter contain?
- A. The deployment name, `support-bot-prod`
- B. The catalog model name in every case
- C. The project name
- D. The resource group name

**Q6 (difficult).** A team has deployed a model for a travel assistant. They have no labeled test data and no reference answers, but they need a systematic quality score on many varied questions. Which approach is best?
- A. Upload 45 expected answers and score with BLEU
- B. Generate a synthetic dataset and run AI-assisted quality evaluators such as relevance and coherence with a GPT model as the judge
- C. Rely only on user studies
- D. Run the HarmBench benchmark against the deployment

**Q7 (difficult, cumulative).** An evaluation of your company chatbot shows low groundedness: it answers from its general knowledge and includes statements that are not in your company's documents. Which improvement from this module targets the problem most directly?
- A. Change the deployment to Global Batch
- B. Add retrieval-augmented generation (RAG) integration to ground responses in your data
- C. Increase the max tokens parameter
- D. Choose a model with a lower HarmBench ASR

---

### Answers and diagnosis

**Q1: B (Data Zone Standard).** Data Zone deployments keep data within a data zone such as the EU, and Standard is pay-per-token.
- A is wrong: Global Standard can use any Azure region, so it does not guarantee EU-only processing.
- C is wrong: Global Provisioned is also global, and it is reserved (PTU), not pay-per-token.
- D is wrong: Global Batch is global and for asynchronous jobs.

**Q2: B (Global Batch).** Large asynchronous jobs within 24 hours at a 50% discount, with no residency requirement.
- A is wrong: it works, but at full pay-per-token price.
- C is wrong: Regional Provisioned reserves capacity (PTU), which means paying for reserved throughput.
- D is wrong: Developer deployments are for fine-tuned model evaluation only.
- *Note:* Data Zone Batch exists too; it is not offered because the scenario has no residency need.

**Q3: B.** Attack Success Rate counts how often attacks succeed, so lower is safer and more robust.
- A is wrong: it reverses the direction.
- C is wrong: cost benchmarks are separate (price per 1M tokens).
- D is wrong: HarmBench tests whether models generate unsafe content, including cybercrime, harassment and copyright violations, so it is about language models.

**Q4: C (ROUGE).** ROUGE emphasizes recall, which suits summarization where covering the key points matters, and the scenario has reference summaries.
- A is wrong: BLEU compares n-grams and is used mainly for machine translation.
- B is wrong: GLEU is a sentence-level BLEU variant.
- D is wrong: coherence is an AI-assisted metric about logical flow; it does not use the reference summaries and does not measure coverage of key points.

**Q5: A.** The `model` parameter takes the **deployment name** so the request routes to your deployment.
- B is wrong: after renaming, the catalog name is not what routes the request.
- C is wrong: the project name identifies the workspace, not the deployment.
- D is wrong: the resource group is a container and has no role in request routing.

**Q6: B.** There is no labeled data, so generate synthetic questions and use AI-assisted evaluators, which do not need ground truth.
- A is wrong: you have no expected answers to upload, and BLEU needs reference text and suits translation.
- C is wrong: user studies are valuable but do not give a systematic, scalable score on many varied questions.
- D is wrong: HarmBench is a safety benchmark for comparing models in the catalog, not a quality evaluation of your assistant on its own task.

**Q7: B.** RAG grounds responses in your data, and groundedness measures whether answers rely on the supplied context rather than speculation.
- A is wrong: Batch is a billing and delivery mode and does not change answer content.
- C is wrong: max tokens only limits response length.
- D is wrong: a lower ASR means better resistance to unsafe prompts; it does not make answers grounded in your documents.

---

## 11. Sources

- Module text supplied by you (Select, deploy, and evaluate Microsoft Foundry models: Introduction, Explore the model catalog, Select models using benchmarks, Deploy models to endpoints, Evaluate model performance, Exercise).
- [Study guide for Exam AI-103](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-103) (skills measured as of April 16, 2026; checked Oct 30, 2026).

*Not verified in this file:* the price or default deployment type of gpt-5.2 and gpt-5-mini in your region, how much the evaluation job will cost, whether your student subscription has enough quota, and the definition of top-p beyond what the module says. Model names in the module are examples and may change.
