---
title: 用Openrouter配置OpenAI模型的structured output
category: Computer
tag:
    - LLM
    - Openrouter
---

我用的是Openrouter的中转，但是我用OpenAI官网platform的代码跑不通：

```python
from openai import OpenAI
from pydantic import BaseModel

client = OpenAI()

class CalendarEvent(BaseModel):
    name: str
    date: str
    participants: list[str]

response = client.responses.parse(
    model="gpt-4o-2024-08-06",
    input=[
        {"role": "system", "content": "Extract the event information."},
        {
            "role": "user",
            "content": "Alice and Bob are going to a science fair on Friday.",
        },
    ],
    text_format=CalendarEvent,
)

event = response.output_parsed
```

后来查了一下发现Openrouter的API版本有点落后了，换成下面这样就可以了(beta版)

```python
response = client.beta.chat.completions.parse(
    model="gpt-4o-mini",
    messages=[
        {"role": "system", "content": "Extract the event information."},
        {
            "role": "user",
            "content": "Alice and Bob are going to a science fair on Friday.",
        },
    ],
    response_format=CalendarEvent,
)

event = response.choices[0].message.parsed
```