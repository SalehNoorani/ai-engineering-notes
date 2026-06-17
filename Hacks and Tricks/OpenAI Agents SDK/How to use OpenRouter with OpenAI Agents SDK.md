# How to use OpenRouter with OpenAI Agents SDK
I struggled with this for half an hour, so I'll write this here for future reference.


```python
# Import these modules at the top
from dotenv import load_dotenv
import os
from openai import AsyncOpenAI
from agents import Agent, Runner, set_default_openai_client, set_tracing_disabled
from agents.models.openai_chatcompletions import OpenAIChatCompletionsModel

# Prohibit the agent from sending trace requests to OpenAI
set_tracing_disabled(True)

# Define the OpenRouter Client
openrouter_client = AsyncOpenAI(
    base_url="https://openrouter.ai/api/v1",
    api_key=os.getenv("OPENROUTER_API_KEY"),
)

# Choose the model you want. You can make multiple ones to use in different agents.
openrouter_model_for_chat = OpenAIChatCompletionsModel(
    model="deepseek/deepseek-v4-flash",
    openai_client=openrouter_client
)

openrouter_model_for_coding = OpenAIChatCompletionsModel(
    model="anthropic/claude-opus-4.8",
    openai_client=openrouter_client
)

# Create an agent or a tool or whatever you need.
agent = Agent(name="Jokester", instructions="You are a joke teller", model=openrouter_model_for_chat)

# If you're in Jupyter, just await the Runner.run
result = await Runner.run(agent, "Tell a joke about Autonomous AI Agents")
print(result.final_output)

# If you're in a script, you need to async define the function.
async def main():
    result = await Runner.run(
        agent, 
        "Tell a joke about Autonomous AI Agents"
    )
    print(result.final_output)
if __name__ == "__main__":
    asyncio.run(main())

```