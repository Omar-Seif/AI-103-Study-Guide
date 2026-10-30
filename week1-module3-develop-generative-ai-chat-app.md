# Week 1 · Module 3: Develop a generative AI chat app with Microsoft Foundry

**Exam:** AI-103 (Developing AI Apps and Agents on Azure) · **Source:** Microsoft Learn module (Introduction, Explore with the model playground, Choose an endpoint and SDK, Generate responses with the ChatCompletions API, Generate responses with the Responses API, Exercise) · **Written:** Oct 30, 2026

---

## 0. Exam relevance (read this first)

I checked the AI-103 study guide earlier today (Oct 30): skills are still "as of April 16, 2026", with unchanged domain weights. The study guide says candidates need **Python app-development experience**, and this is the module where you start writing it.

| Module topic | Study-guide objective it supports | Yield |
| --- | --- | --- |
| Endpoint + SDK + authentication choices | "Configure an application to connect to a Foundry project"; "Integrate generative workflows into applications by using Foundry SDKs and connectors" | **High** |
| Entra ID / keyless authentication, keys in Key Vault | "Configure security, including managed identity, private networking, keyless credentials, and role policies" | **High** (my mapping: Entra ID with `DefaultAzureCredential` is the "keyless" pattern) |
| Responses API, `previous_response_id`, instructions, parameters | "Deploy and consume LLMs..."; "Define agent roles, goals, **conversation-tracking approach**, and tool schemas"; "Tune generation behavior..." | **High** |
| ChatCompletions API and manual history | Same family of objectives; also code maintenance scenarios | **Medium-high** |
| Streaming, async | "Optimize and operationalize generative AI systems" (responsiveness) | **Medium** |
| Context window contents and token cost | "Manage quotas, scaling, rate limits, and cost footprints" | **Medium-high** |
| Model playground and Code button | "Deploy and consume LLMs" | **Medium** |

**Skim:** nothing here is fluff, but do not memorize the exact `api_version` strings or the example model names in the snippets. Learn the *pattern*.

**Exam style to expect:** questions that show a few lines of Python and ask what is wrong, what is missing, or which client, endpoint or parameter to use. Practice reading the snippets in section 7.

---

## 1. Foundations, from the ground up

You said Python SDKs and authentication feel fuzzy, so this block defines every word the module takes for granted.

### 1.1 Calling a model from code

- Your code sends a **request** over the internet to a model **endpoint** (a web address) and gets a **response**. The data travels as **JSON**, a text format of labeled values like `{"role": "user", "content": "Hi"}`.
- Writing raw web requests is tedious, so you use an **SDK** (a code library). **`pip install`** downloads a library from **PyPI** (Python's package store).
- A **virtual environment** (venv) is a private folder of installed libraries for one project, so projects do not clash. The exercise's terminal prefix **`(.venv)`** tells you it is active.
- A **`.env` file** stores configuration values (like the endpoint and deployment name) outside your code. A **requirements.txt** lists the packages to install in one go (`pip install -r requirements.txt`).
- **Environment variables** are named settings the operating system gives your program. Some SDKs read specific ones automatically (section 3.3).

### 1.2 Authentication, a bit deeper than Module 1

- **API key:** a secret string; whoever holds it can call the service.
- **Microsoft Entra ID:** your program proves *which identity it runs as* and receives a **token** (a signed proof, accepted for a limited time) that it presents instead of a key. The module's wording: production apps "should use Microsoft Entra ID authentication, which requires the application to be running in the context of a specific identity."
- **`az login`** signs you into Azure from the command line (Azure CLI). Then code running on your computer can use *your* signed-in identity.
- **`DefaultAzureCredential`** (from the `azure-identity` package) is a helper that finds a usable identity for you. *Beyond the module (background knowledge, verify later):* it tries several sources in order, such as environment variables, a managed identity when running in Azure, and your `az login` session. That is why the same code works on your laptop and in Azure without changing it.
- **Bearer token:** the access token presented with each request. `get_bearer_token_provider(...)` creates a function that fetches fresh tokens when needed. The string `"https://ai.azure.com/.default"` in the exercise names the scope the token is requested for.

### 1.3 Stateless vs stateful

A model call, by default, **remembers nothing**. If you ask "Who founded it?" in a fresh request, the model has no idea what "it" is. To hold a conversation, *something* must send the earlier context back. There are two ways in this module:

| | **You keep the history** | **The service keeps it** |
| --- | --- | --- |
| How | Your code appends every user and assistant message and resends the whole list each turn | You send only the new input plus a pointer (`previous_response_id`) to the earlier response |
| API | ChatCompletions (always); Responses (optional "manual chaining") | Responses |

### 1.4 Streaming and async, in plain words

- **Streaming:** the reply arrives in small pieces ("deltas") as it is produced, so the user sees text appear instead of waiting for the full answer.
- **Async (asynchronous) code:** while your program waits for the network, it can do other work. Python's `async`/`await` is how you write that. It helps when you run **several requests at once**.

---

## 2. Exploring with the model playground

Before writing code, test in the **Model playground** (left navigation): send prompts to deployed models, adjust settings such as **temperature** and **max tokens**, add **system messages**, and try different models.

**The Code button** in the chat pane shows sample code that reproduces your session. You choose:

| Choice | Options (from the module) |
| --- | --- |
| **API** | Responses API, or another such as ChatCompletions |
| **Language** | Your preferred programming language |
| **SDK** | Which SDK sample you want |

The samples are **pre-populated with your project endpoint, model deployment name and current settings**.

**Workflow:** 1) explore in the playground, 2) generate code with the Code tab, 3) develop the app from it, 4) iterate and return to the playground.

---

## 3. Choosing an endpoint, SDK and authentication

Four decisions, per the module: **endpoint**, **client SDK**, **authentication**, **chat API**.

### 3.1 Two endpoints on every project

| | **Project endpoint** | **Azure OpenAI endpoint** |
| --- | --- | --- |
| Format | `https://{resource-name}.services.ai.azure.com/api/projects/<project-name>` | `https://{resource-name}.openai.azure.com/openai/v1` |
| Typical SDK | **Foundry SDK** (`azure-ai-projects`) | **OpenAI SDK** (`openai`) |
| Found on | The project's **Overview** page in https://ai.azure.com | The same page |

Tell them apart by the URL: **`.services.ai.azure.com/api/projects`** is the project; **`.openai.azure.com/openai/v1`** is the OpenAI-compatible endpoint.

### 3.2 Foundry SDK (`azure-ai-projects`) with the project endpoint

- Available as **Azure AI Projects** for **Python, .NET and JavaScript**. Each SDK is developed independently, so features may be at different stages.
- Install with `pip install azure-ai-projects`. It brings along **`openai`** and **`azure-identity`**. You still import the OpenAI package yourself for chatting, because the Foundry SDK's chat client "is derived from the OpenAI SDK".
- **`AIProjectClient`** gives Foundry-native operations with no OpenAI equivalent:
  - Retrieve resource **connections**
  - Access **project configuration**
  - Enable **tracing**
  - Manage **datasets and indexes**
- To chat, call **`project_client.get_openai_client(...)`** to get an OpenAI-compatible client.

### 3.3 OpenAI SDK with the Azure OpenAI endpoint

- Install `pip install openai azure-identity` (`azure-identity` is needed for Entra ID tokens).
- **Three ways to authenticate:**

| Method | How | Notes |
| --- | --- | --- |
| **Entra ID (recommended)** | `OpenAI(base_url=..., api_key=token_provider)` where `token_provider` comes from `get_bearer_token_provider(DefaultAzureCredential(), "https://ai.azure.com/.default")` | Passing the token provider *as* `api_key` is the pattern to recognize |
| **API key** | `api_key=os.getenv("AZURE_OPENAI_API_KEY")` | The module's warning: store keys in **Azure Key Vault** and **never include them directly in your code** |
| **Environment variables** | Set `OPENAI_BASE_URL` and `OPENAI_API_KEY`, then `OpenAI()` picks them up | Convenient; it is still a key |

- **`AzureOpenAI` client:** use the normal `OpenAI` client with the `/openai/v1` endpoint in general. Use `AzureOpenAI` only if you need functionality from a **specific Azure OpenAI API version**; then you must give `azure_endpoint` and `api_version`.
- The OpenAI client handles model inference: the **Responses API**, **chat completions and image generation**, and Foundry direct models.

> **A typo to avoid copying:** in the module's `AzureOpenAI` snippet there is no comma after the `azure_endpoint = "..."` line, so it is a syntax error as printed. The exam tests ideas, not typos, but do not paste it into a lab.

### 3.4 Which SDK for which job

| Use the **Foundry SDK** (`AIProjectClient`) when you need... | Use the **OpenAI SDK** when you need... |
| --- | --- |
| **Foundry Agent Service** (build and manage agents) | **Full OpenAI API compatibility** for existing code and tooling |
| **Tool invocation and approval workflows** | **Portability** between OpenAI and Azure OpenAI deployments |
| **Cloud evaluations** | **Chat Completions, Responses and Images** APIs |
| **Tracing and observability** | Minimal dependency on Foundry-specific concepts |
| **Foundry direct models** (non-Azure OpenAI models in the catalog) | Straightforward model **inference** |
| **Project metadata, connections and governance** | (It does **not** give agents or evaluations) |

Microsoft recommends the Foundry SDK for apps with agents, evaluations or other Foundry-specific features. You can use **both together**: the Foundry SDK for project features and the OpenAI SDK for model inference.

**Something the module states inconsistently:** it says the OpenAI SDK connects to the **Azure OpenAI endpoint**, but its closing paragraph says "both SDKs work with your Foundry project endpoint", and the Responses unit says the Responses API works through "the Foundry SDK or AzureOpenAI client to connect to a project endpoint". Module 1 also said the project endpoint serves the Responses API. The safe exam pairing is: **Foundry SDK → project endpoint; OpenAI SDK → Azure OpenAI (v1) endpoint**, and use the **project endpoint + Foundry SDK** whenever agents, evaluations or tracing are mentioned.

---

## 4. ChatCompletions API

- The widely used OpenAI chat API. The Responses API is recommended for new projects, but ChatCompletions is useful for **code maintenance** and **cross-platform compatibility** (well established across many models and platforms).
- **A prompt is a list of message objects** (JSON), each with a **role**: `system` (instructions), `user` (input) and `assistant` (the model's earlier answers).

```python
completion = openai_client.chat.completions.create(
    model="gpt-4o",          # your model DEPLOYMENT name
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "When was Microsoft founded?"}
    ]
)
print(completion.choices[0].message.content)   # the answer
```

- **Not stateful.** To keep context, *you* append each user message and each assistant reply to the list and **submit the entire conversation history on every turn**.
- Read the answer from **`completion.choices[0].message.content`**.

---

## 5. Responses API (the recommended one)

It **brings together ChatCompletions and Assistants** patterns in one unified, **stateful** API. It also works with **Foundry direct models** (for example Phi, DeepSeek), not just Azure OpenAI models. The module says it "replaces the older ChatCompletions API for most scenarios."

### 5.1 The basic call

```python
response = openai_client.responses.create(
    model="gpt-4.1",                                  # deployment name
    instructions="You are a helpful AI assistant.",   # = the system prompt
    input="What is Microsoft Foundry?",               # = the user message
    temperature=0.8,                                  # 0.0 to 2.0, higher = more varied
    max_output_tokens=200                             # cap on the response length
)
print(response.output_text)
```

| ChatCompletions | Responses |
| --- | --- |
| `chat.completions.create(...)` | `responses.create(...)` |
| `messages=[...]` with system and user roles | `instructions=` + `input=` |
| `completion.choices[0].message.content` | `response.output_text` |
| `max_tokens`-style length cap (not shown in the module) | `max_output_tokens` |
| Stateless | Stateful (`previous_response_id`) |

### 5.2 The response object

`output_text` (the text), `id` (unique identifier), `status` (for example `completed`), `usage` (input, output and total tokens), `model`.

The other parameter named: **`top_p`**, an alternative to temperature for controlling randomness.

### 5.3 Keeping the conversation (the exercise's core lesson)

```python
last_response_id = None
while True:
    ...
    response = openai_client.responses.create(
        model=model_deployment,
        instructions="...",
        input=input_text,
        previous_response_id=last_response_id   # link to the earlier turn
    )
    last_response_id = response.id               # remember it for next time
```

- **`previous_response_id`** links responses so context carries across calls. The first turn has `None`.
- **`openai_client.responses.retrieve(response_id)`** fetches an earlier response, because the API keeps response history. You can pass the ID of *any* earlier response to redirect a conversation or resume an older thread.
- **Manual chaining (alternative):** build the history list yourself and pass it as `input`. Use it to **customize which messages are included**, **prune the conversation to manage token limits**, or **store and restore history from a database**.

### 5.4 The context window trap

> **"The SDK helps you manage state, but it doesn't automatically make token usage cheaper."**

On **every request**, everything in the active context window is **concatenated, tokenized and sent to the model together**:

1. System instructions (instructions, safety rules)
2. Your current prompt
3. Conversation history (earlier user and assistant messages)
4. Tool schemas (functions, OpenAPI specs, MCP tools)
5. Tool outputs (search results, code interpreter output, files)
6. Retrieved memory or documents (memory stores, RAG, file search)

So a long conversation costs more per turn even when you use `previous_response_id`. **Tie to your credits:** every extra turn in your lab resends the growing context. Keep lab chats short.

### 5.5 Responsiveness: streaming and async

**Streaming:** `stream=True`, then loop over events. The two events the exercise uses:

```python
for event in stream:
    if event.type == "response.output_text.delta":
        print(event.delta, end="")                 # a new chunk of text
    elif event.type == "response.completed":
        last_response_id = event.response.id       # get the ID when the stream ends
```

If you track history while streaming, you get the response ID from the **`response.completed`** event.

**Async:** use `AsyncOpenAI` and `await` each call.

- The module is explicit: a single `await` wrapped in `asyncio.run()` takes **about the same time** as the synchronous client. The benefit appears when you run **several requests concurrently**, for example with **`asyncio.gather()`**.
- Async streaming uses `async for event in stream`.
- In the exercise, the async file imports from **`azure.identity.aio`** and closes the credential at the end with **`await credential.close()`**.

| Problem | Tool |
| --- | --- |
| A long answer makes the app look frozen | **Streaming** |
| You need to run many model calls at the same time | **Async** with `asyncio.gather()` |
| Neither makes tokens cheaper | The module does not claim cost savings for either |

---

## 6. The exercise: "Create a generative AI chat app" (about 45 minutes)

### 6.1 Before you start

**You need on your computer:** VS Code, **Python 3.13.xx** (3.14 exists but some dependencies are not yet compiled for it; the lab was tested on 3.13.12), Git, **Azure CLI**, and an active Azure subscription. If an install step fails, paste me the **exact error text** and what operating system you use.

**Cost and cleanup:**
- Same routine: own resource group, **check the deployment type and price** in the Deploy dialog and avoid a Provisioned type, keep prompts few, and **delete the resource group** when you finish. You can reuse the project and the **gpt-5.2** deployment from Module 2 if they still exist, or create new ones.
- Each message costs tokens, and the later turns resend context (section 5.4). I have not verified the price, so the exercise cost is unknown to me; it should be small if you chat briefly.
- **Do not put a key in `.env` or paste one here.** This exercise uses **Entra ID**, so no key is needed.

### 6.2 Steps and what each one teaches

| # | What you do | What it teaches |
| --- | --- | --- |
| 1 | Create a Foundry project and **deploy gpt-5.2** with default settings (or reuse yours) | Module 1 and 2 recap |
| 2 | On the project **Home** page, copy the **Azure OpenAI endpoint**, **not** the project endpoint | Which endpoint pairs with the OpenAI SDK (section 3.1) |
| 3 | In VS Code, **clone** `https://github.com/microsoftlearning/mslearn-ai-studio`, install the **Python extension**, select an interpreter, and create a **venv** from Python 3.13 | Isolated environment |
| 4 | In `labfiles/foundry-chat/python/chat-app`, open a terminal and confirm the **`(.venv)`** prefix. Run `pip install -r requirements.txt` | Installing dependencies (OpenAI SDK, Azure Identity) |
| 5 | Edit **`.env`**: set the **Azure OpenAI endpoint** and **`MODEL_DEPLOYMENT`** to the **exact deployment name** | Config outside code; deployment name in the `model` parameter |
| 6 | In **`chat-app.py`**, add the imports and the **Entra ID client** (token provider as `api_key`); add a **ChatCompletions** call in the loop | Section 4 and 3.3 |
| 7 | Run `az login`, then `python chat-app.py`; ask *Tell me about the ELIZA chatbot.* | Authentication through your signed-in identity |
| 8 | Replace the call with the **Responses API** (`instructions=` and `input=`). Ask the ELIZA question, then *How does it compare to modern LLMs?* **Expect the model to not know what "it" means** | Responses calls that are not linked to an earlier response carry no context; the API only tracks the conversation when you link responses |
| 9 | Add **`last_response_id = None`** before the loop, pass **`previous_response_id`**, and store **`response.id`**. Re-run; the follow-up now works | Conversation tracking (section 5.3) |
| 10 | Switch to **`stream=True`** and handle **`response.output_text.delta`** and **`response.completed`** | Streaming (section 5.5) |
| 11 | In **`chat-async.py`**, import **`asyncio`, `AsyncOpenAI`, and `azure.identity.aio`**; build `AsyncOpenAI`; `await async_client.responses.create(...)`; **`await credential.close()`** in `finally`. Ask *Tell me about the Turing test.* | Async (section 5.5) |
| 12 | **Delete the resource group** | Cleanup |

### 6.3 If something breaks: first checks

| Symptom | Check first |
| --- | --- |
| Authentication error | Did you run **`az login`** in that terminal and pick the right subscription? If you have subscriptions in multiple tenants, the exercise says to add `--tenant`. If you get a permission-type error (401 or 403) while signed in, tell me the exact message: it is probably a missing role assignment on the resource, and I will confirm the exact role name before telling you what to assign |
| Errors about the model or deployment | Is `MODEL_DEPLOYMENT` the **exact deployment name** from the portal (not just the catalog name)? |
| Wrong endpoint | Did you copy the **Azure OpenAI endpoint**, not the project endpoint? |
| Package or install errors | Is the **`(.venv)`** prefix showing, and is it built from **Python 3.13**? |
| Scripts will not run on your computer | The exercise notes you may need to **enable running scripts** on your system |
| Follow-up answer says it does not know what "it" is | Expected at step 8; fixed at step 9 with `previous_response_id` |

### 6.4 Connection to your Week 1 build

Your plan's hands-on step says: *call both models from Python using Entra ID.* This exercise already does it for **gpt-5.2**. To finish the step, point **`MODEL_DEPLOYMENT`** at your **gpt-5-mini** deployment (same code) and run the same prompts. Then add your 10-task comparison on quality, speed and cost. I can write that script with you when you are ready (see the options at the end of my message).

### 6.5 Lab notes to fill in

- Python version and venv created: ______
- Which endpoint and which authentication method you used: ______
- ChatCompletions vs Responses: what was different in the code? ______
- What happened to the follow-up question before and after `previous_response_id`? ______
- Did streaming change when you *saw* text, and did it change total time? ______
- Errors you hit and how you fixed them: ______

---

## 7. Reading code on the exam: annotated pattern

```python
# 1) Credentials: use whoever is signed in (az login) or the Azure-assigned identity
token_provider = get_bearer_token_provider(
    DefaultAzureCredential(), "https://ai.azure.com/.default")

# 2) Client for the AZURE OPENAI endpoint, authenticated with Entra ID (no key in code)
openai_client = OpenAI(
    base_url=azure_openai_endpoint,     # .../openai/v1
    api_key=token_provider)             # a token provider goes in api_key

# 3) The call: model = DEPLOYMENT name; instructions = system prompt; input = user message
response = openai_client.responses.create(
    model=model_deployment,
    instructions="You are a helpful AI assistant ...",
    input=input_text,
    previous_response_id=last_response_id)   # None on the first turn

# 4) Read the result and remember the ID for the next turn
print(response.output_text)
last_response_id = response.id
```

Quick diagnostic questions the exam may ask about code like this: *Which endpoint is it?* (Azure OpenAI.) *Which SDK?* (OpenAI.) *How does it authenticate?* (Entra ID token provider.) *Why does it keep context?* (`previous_response_id`.) *What would you change to see partial text?* (`stream=True`.)

---

## 8. Exam traps and comparisons

| Pair or topic | Difference | Trap |
| --- | --- | --- |
| **Foundry SDK vs OpenAI SDK** | Foundry: agents, evaluations, tracing, connections. OpenAI: inference and compatibility | Choosing the OpenAI SDK when the scenario needs agents, evaluations or tracing |
| **Project endpoint vs Azure OpenAI endpoint** | `.services.ai.azure.com/api/projects` vs `.openai.azure.com/openai/v1` | Mixing them; the exercise explicitly warns to use the Azure OpenAI endpoint |
| **ChatCompletions vs Responses** | Stateless, you resend history, `messages` list vs stateful, `previous_response_id`, `instructions` + `input` | "ChatCompletions remembers the conversation" is false |
| **`previous_response_id` vs manual chaining** | Pointer to server-held history vs you build the list | Manual chaining is how you **prune** or store history in a **database** |
| **State management vs cost** | The SDK manages state but does **not** reduce tokens | "Chaining responses lowers the token bill" is false |
| **Streaming vs async** | Streaming shows partial output sooner; async overlaps concurrent requests | A single awaited call is not faster than the sync client |
| **Entra ID vs key vs environment variables** | Identity-based (recommended) vs secret in Key Vault, never in code vs convenient key storage | Hard-coding a key, or thinking environment variables stop it being a secret |
| **`OpenAI` vs `AzureOpenAI` client** | Default for the v1 endpoint vs only when you need a specific API version | Using `AzureOpenAI` by default |
| **Deployment name vs model name** | The `model` parameter takes the deployment name | Using the catalog name after renaming |
| **`temperature` vs `top_p`** | Both control randomness; top_p is an alternative to temperature | Thinking `max_output_tokens` controls creativity (it limits length) |

---

## 9. Connections across your track

- **Module 1:** the two endpoints, the three keys and the Foundry SDK families come back as real code.
- **Module 2:** the **deployment name** goes in `model`; **temperature, max tokens and top-p** from the playground are the parameters here; **evaluation** and **tracing** are the reasons to choose the Foundry SDK.
- **Setup file:** each message sends context again, so conversations cost tokens as they grow; delete the resource group at the end.
- **Week 1 course Unit 3 (Entra Agent ID):** the Entra ID pattern in this exercise is the application-level version of that idea. The lesson goes further with *agent identities*.
- **Weeks 3 and 4:** agents add **tools**, **tool outputs** and **approval workflows**, which appear in the context window list in section 5.4. Tracing (`AIProjectClient`) comes back in Week 4.
- **AI-900 / responsible AI:** keeping keys out of code is a *privacy and security* practice.

### Checkpoint prompt (answer without notes)

1. Why use Entra ID instead of API keys in a production app?
2. A teammate says "`previous_response_id` makes our conversation cheaper because the server remembers it." What do you tell them?
3. When would you choose the Foundry SDK over the OpenAI SDK?

---

## 10. Key takeaways

1. Prototype in the **playground**, then use the **Code** button for starter code (API, language and SDK choices, pre-filled with your endpoint and deployment name).
2. Every project has a **project endpoint** (Foundry SDK) and an **Azure OpenAI endpoint** (OpenAI SDK).
3. Use the **Foundry SDK** (`azure-ai-projects`, `AIProjectClient`) for agents, evaluations, tracing, connections and datasets/indexes. Use the **OpenAI SDK** for compatible inference. You can combine them.
4. **Entra ID** is the recommended authentication for production (`DefaultAzureCredential` + `get_bearer_token_provider`, with `az login` locally). Keys belong in **Key Vault**, never in code.
5. The **Responses API** is recommended for new projects: `instructions` + `input`, read `output_text`, and keep context with **`previous_response_id`**. **ChatCompletions** uses a `messages` list and needs you to resend the full history.
6. **Context costs tokens every request** (instructions, history, tool schemas, tool outputs, retrieved docs). The SDK manages state; it does not make tokens cheaper.
7. **Streaming** (`stream=True`, `response.output_text.delta`, `response.completed`) fixes a frozen-looking app. **Async** helps when requests run concurrently.
8. The `model` parameter takes the **deployment name**.
9. Clean up by deleting the resource group.

---

## 11. Practice questions (AI-103 style)

**Q1 (easy).** A team is starting a new chat application on a Foundry project and wants the API Microsoft recommends for most new development, with built-in conversation tracking. Which API should they use?
- A. The ChatCompletions API
- B. The Responses API
- C. A custom REST wrapper that stores all messages in a database
- D. The Azure Machine Learning SDK

**Q2 (medium).** An application must create and manage agents with the Foundry Agent Service, enforce tool approval workflows and trace its behavior. Which client library fits best?
- A. The OpenAI SDK against the Azure OpenAI endpoint
- B. The Foundry SDK (`azure-ai-projects`) with `AIProjectClient`
- C. The `AzureOpenAI` client with a pinned API version
- D. An HTTP client that sends raw JSON with the project key

**Q3 (medium).** Your Responses API chat app answers the first question but, on the follow-up "How does it compare to modern LLMs?", asks what "it" refers to. What should you change?
- A. Raise `max_output_tokens`
- B. Pass `previous_response_id` using the ID of the last response
- C. Set `temperature=0`
- D. Add `stream=True`

**Q4 (medium).** You maintain an app that uses the ChatCompletions API and need the model to remember earlier turns. What must your code do?
- A. Nothing; ChatCompletions tracks the conversation for you
- B. Pass `previous_response_id`
- C. Append each user and assistant message to the `messages` list and submit the full history on every turn
- D. Put the conversation in the `instructions` parameter

**Q5 (medium).** A production app uses the OpenAI SDK with the Azure OpenAI endpoint. Security policy says the app must not contain secrets. Which approach complies?
- A. Hard-code the API key in a constant
- B. Commit a `.env` file with the key to the repository
- C. Use `DefaultAzureCredential` with `get_bearer_token_provider` and pass the token provider as `api_key`
- D. Email the key to each developer and paste it into their scripts

**Q6 (difficult).** A colleague claims, "Using `previous_response_id` makes multi-turn conversations cheaper, because the service holds the history instead of us." Which statement is correct?
- A. They are right; only the newest input is billed
- B. They are wrong; instructions, history, tool schemas, tool outputs and retrieved documents are still all tokenized and sent on every request, so state management does not make tokens cheaper
- C. They are right, but only when streaming is enabled
- D. They are wrong; `previous_response_id` makes each request cost twice as much

**Q7 (difficult, cumulative).** In a Microsoft Foundry project you deployed a model from the catalog under the custom deployment name `support-bot-prod`. Your code calls `responses.create(model="gpt-5.2", ...)` and fails. What is the most likely fix?
- A. Set `model="support-bot-prod"`, the deployment name
- B. Change the resource group name to match the model
- C. Switch to the ChatCompletions API
- D. Increase `max_output_tokens`

**Q8 (difficult).** Users complain that your chat app looks frozen while the model writes long answers. They want to see the text appear gradually. You change the code to use `AsyncOpenAI` with a single `await` around `asyncio.run()`. What will happen?
- A. Text will now appear gradually
- B. The call will complete in about the same time as before; to show partial output you need `stream=True` and to handle the delta events
- C. Token costs will drop
- D. The conversation will be tracked automatically

---

### Answers and diagnosis

**Q1: B.** Microsoft recommends the Responses API for most new development, and it is stateful (`previous_response_id`).
- A is wrong: it is well established, but the module keeps it mainly for code maintenance and cross-platform compatibility; it has no stateful response tracking.
- C is wrong: custom storage is "manual chaining", an option for special needs; it is not what Microsoft recommends first.
- D is wrong: the Azure Machine Learning SDK is not the chat API for Foundry apps in this module.

**Q2: B.** The module says use the Foundry SDK for Foundry Agent Service, tool invocation and approval workflows, cloud evaluations and tracing.
- A is wrong: the OpenAI SDK "doesn't provide Foundry-specific features like agents or evaluations."
- C is wrong: `AzureOpenAI` is for needing a specific Azure OpenAI API version, not for agents.
- D is wrong: raw requests with a key skip both the SDK and the recommended authentication.

**Q3: B.** `previous_response_id` links responses so context carries across calls. That is exactly what the exercise demonstrates.
- A is wrong: that only limits response length.
- C is wrong: temperature changes randomness, not memory.
- D is wrong: streaming changes how output arrives, not whether context is kept.

**Q4: C.** ChatCompletions is not stateful; you track the history yourself and submit the whole conversation each turn.
- A is wrong: the module says ChatCompletions "doesn't provide a stateful response tracking feature."
- B is wrong: `previous_response_id` belongs to the Responses API.
- D is wrong: `instructions` is a Responses parameter for the system prompt, and putting the whole conversation there is not the pattern shown.

**Q5: C.** Entra ID token-based authentication has no secret in the code, and is the module's recommended approach.
- A is wrong: the module says never include keys directly in code.
- B is wrong: a committed `.env` still exposes the key in source control; the module says store keys in Azure Key Vault if you must use them.
- D is wrong: distributing keys by email multiplies the secret, the opposite of the policy.

**Q6: B.** "The SDK helps you manage state, but it doesn't automatically make token usage cheaper." All of that context is concatenated, tokenized and sent to the model on each request.
- A is wrong: it assumes only new input counts, which the module contradicts.
- C is wrong: streaming changes delivery, not token counts, and the module makes no such link.
- D is wrong: the module does not say it doubles the cost; it says state tracking does not reduce it.

**Q7: A.** The `model` parameter takes the **deployment name** so the request routes to your deployment (from Module 2 and this module).
- B is wrong: the resource group plays no part in routing requests.
- C is wrong: both chat APIs use the deployment name in `model`.
- D is wrong: max tokens affects response length only.

**Q8: B.** The module states a single `await` wrapped in `asyncio.run()` takes about the same time as the synchronous client. For partial output you need **streaming**.
- A is wrong: async alone does not stream.
- C is wrong: neither async nor streaming is described as reducing cost.
- D is wrong: tracking comes from `previous_response_id`, not from async.

---

## 12. Sources

- Module text supplied by you (Develop a generative AI chat app with Microsoft Foundry: Introduction, Explore with the model playground, Choose an endpoint and SDK, ChatCompletions API, Responses API, Exercise).
- [Study guide for Exam AI-103](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-103) (skills measured as of April 16, 2026; checked Oct 30, 2026).

*Not verified in this file:* the cost of the exercise on your subscription, the exact Azure role name you would need if Entra ID returns a permission error, how `DefaultAzureCredential` orders its credential sources (from general knowledge, marked above), and whether the Foundry SDK can also serve the Responses API through the project endpoint, since the module words this inconsistently. Code in the module may contain typos (such as the missing comma in the `AzureOpenAI` snippet).
