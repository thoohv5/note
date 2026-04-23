---
title: Tool
date: 2026-04-07
  - 理论
  - AI
type: note
status: complete
---

## Tool

```python
from langchain.agents import create_agent
from langchain.chat_models import ChatAnthropic
from langchain.tools import tool
from pydantic import BaseModel, Field
from typing import Literal

## ------------------------
## 1. 定义 Pydantic 输入结构
## ------------------------
class WeatherInput(BaseModel):
    location: str = Field(description="City name or coordinates")
    units: Literal["celsius", "fahrenheit"] = Field(
        default="celsius", description="Temperature unit preference"
    )
    include_forecast: bool = Field(
        default=False, description="Include 5-day forecast"
    )

## ------------------------
## 2. 定义 Tool
## ------------------------
@tool(args_schema=WeatherInput)
def get_weather(location: str, units: str = "celsius", include_forecast: bool = False) -> str:
    """Get current weather and optional forecast."""
    temp = 22 if units == "celsius" else 72
    result = f"Current weather in {location}: {temp} degrees {units[0].upper()}"
    if include_forecast:
        result += "\nNext 5 days: Sunny"
    return result

## ------------------------
## 3. 初始化模型（Claude Sonnet）
## ------------------------
llm = ChatAnthropic(
    model="claude-3-sonnet-20240229",
    temperature=0
)

## ------------------------
## 4. 创建 Agent（重点）
## ------------------------
agent = create_agent(
    model=llm,
    tools=[get_weather],
    system_prompt="You are a helpful weather assistant."
)

## ------------------------
## 5. 调用 Agent
## ------------------------
result = agent.invoke({
    "messages": [
        {"role": "user", "content": "What's the weather in San Francisco today? And give me forecast."}
    ]
})

print(result)

```