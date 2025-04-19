[Skip to content](https://google.github.io/adk-docs/agents/llm-agents/#llm-agent)

# LLM Agent [¶](https://google.github.io/adk-docs/agents/llm-agents/\#llm-agent "Permanent link")

The `LlmAgent` (often aliased simply as `Agent`) is a core component in ADK, acting as the "thinking" part of your application. It leverages the power of a Large Language Model (LLM) for reasoning, understanding natural language, making decisions, generating responses, and interacting with tools.

Unlike deterministic [Workflow Agents](https://google.github.io/adk-docs/agents/workflow-agents/) that follow predefined execution paths, `LlmAgent` behavior is non-deterministic. It uses the LLM to interpret instructions and context, deciding dynamically how to proceed, which tools to use (if any), or whether to transfer control to another agent.

Building an effective `LlmAgent` involves defining its identity, clearly guiding its behavior through instructions, and equipping it with the necessary tools and capabilities.

## Defining the Agent's Identity and Purpose [¶](https://google.github.io/adk-docs/agents/llm-agents/\#defining-the-agents-identity-and-purpose "Permanent link")

First, you need to establish what the agent _is_ and what it's _for_.

- **`name` (Required):** Every agent needs a unique string identifier. This `name` is crucial for internal operations, especially in multi-agent systems where agents need to refer to or delegate tasks to each other. Choose a descriptive name that reflects the agent's function (e.g., `customer_support_router`, `billing_inquiry_agent`). Avoid reserved names like `user`.

- **`description` (Optional, Recommended for Multi-Agent):** Provide a concise summary of the agent's capabilities. This description is primarily used by _other_ LLM agents to determine if they should route a task to this agent. Make it specific enough to differentiate it from peers (e.g., "Handles inquiries about current billing statements," not just "Billing agent").

- **`model` (Required):** Specify the underlying LLM that will power this agent's reasoning. This is a string identifier like `"gemini-2.0-flash"`. The choice of model impacts the agent's capabilities, cost, and performance. See the [Models](https://google.github.io/adk-docs/agents/models/) page for available options and considerations.


```md-code__content
# Example: Defining the basic identity
capital_agent = LlmAgent(
    model="gemini-2.0-flash",
    name="capital_agent",
    description="Answers user questions about the capital city of a given country."
    # instruction and tools will be added next
)

```

## Guiding the Agent: Instructions ( `instruction`) [¶](https://google.github.io/adk-docs/agents/llm-agents/\#guiding-the-agent-instructions-instruction "Permanent link")

The `instruction` parameter is arguably the most critical for shaping an `LlmAgent`'s behavior. It's a string (or a function returning a string) that tells the agent:

- Its core task or goal.
- Its personality or persona (e.g., "You are a helpful assistant," "You are a witty pirate").
- Constraints on its behavior (e.g., "Only answer questions about X," "Never reveal Y").
- How and when to use its `tools`. You should explain the purpose of each tool and the circumstances under which it should be called, supplementing any descriptions within the tool itself.
- The desired format for its output (e.g., "Respond in JSON," "Provide a bulleted list").

**Tips for Effective Instructions:**

- **Be Clear and Specific:** Avoid ambiguity. Clearly state the desired actions and outcomes.
- **Use Markdown:** Improve readability for complex instructions using headings, lists, etc.
- **Provide Examples (Few-Shot):** For complex tasks or specific output formats, include examples directly in the instruction.
- **Guide Tool Use:** Don't just list tools; explain _when_ and _why_ the agent should use them.

```md-code__content
# Example: Adding instructions
capital_agent = LlmAgent(
    model="gemini-2.0-flash",
    name="capital_agent",
    description="Answers user questions about the capital city of a given country.",
    instruction="""You are an agent that provides the capital city of a country.
When a user asks for the capital of a country:
1. Identify the country name from the user's query.
2. Use the `get_capital_city` tool to find the capital.
3. Respond clearly to the user, stating the capital city.
Example Query: "What's the capital of France?"
Example Response: "The capital of France is Paris."
""",
    # tools will be added next
)

```

_(Note: For instructions that apply to_ all _agents in a system, consider using `global_instruction` on the root agent, detailed further in the [Multi-Agents](https://google.github.io/adk-docs/agents/multi-agents/) section.)_

## Equipping the Agent: Tools ( `tools`) [¶](https://google.github.io/adk-docs/agents/llm-agents/\#equipping-the-agent-tools-tools "Permanent link")

Tools give your `LlmAgent` capabilities beyond the LLM's built-in knowledge or reasoning. They allow the agent to interact with the outside world, perform calculations, fetch real-time data, or execute specific actions.

- **`tools` (Optional):** Provide a list of tools the agent can use. Each item in the list can be:
  - A Python function (automatically wrapped as a `FunctionTool`).
  - An instance of a class inheriting from `BaseTool`.
  - An instance of another agent ( `AgentTool`, enabling agent-to-agent delegation - see [Multi-Agents](https://google.github.io/adk-docs/agents/multi-agents/)).

The LLM uses the function/tool names, descriptions (from docstrings or the `description` field), and parameter schemas to decide which tool to call based on the conversation and its instructions.

```md-code__content
# Define a tool function
def get_capital_city(country: str) -> str:
  """Retrieves the capital city for a given country."""
  # Replace with actual logic (e.g., API call, database lookup)
  capitals = {"france": "Paris", "japan": "Tokyo", "canada": "Ottawa"}
  return capitals.get(country.lower(), f"Sorry, I don't know the capital of {country}.")

# Add the tool to the agent
capital_agent = LlmAgent(
    model="gemini-2.0-flash",
    name="capital_agent",
    description="Answers user questions about the capital city of a given country.",
    instruction="""You are an agent that provides the capital city of a country... (previous instruction text)""",
    tools=[get_capital_city] # Provide the function directly
)

```

Learn more about Tools in the [Tools](https://google.github.io/adk-docs/tools/) section.

## Advanced Configuration & Control [¶](https://google.github.io/adk-docs/agents/llm-agents/\#advanced-configuration-control "Permanent link")

Beyond the core parameters, `LlmAgent` offers several options for finer control:

### Fine-Tuning LLM Generation ( `generate_content_config`) [¶](https://google.github.io/adk-docs/agents/llm-agents/\#fine-tuning-llm-generation-generate_content_config "Permanent link")

You can adjust how the underlying LLM generates responses using `generate_content_config`.

- **`generate_content_config` (Optional):** Pass an instance of `google.genai.types.GenerateContentConfig` to control parameters like `temperature` (randomness), `max_output_tokens` (response length), `top_p`, `top_k`, and safety settings.

[Content continues but truncated due to length...]
