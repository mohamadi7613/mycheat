


# LLM


### install

```bash
pip install openai
```

## Openai useage

```py
from openai import OpenAI

load_dotenv(override=True)                   # load .env varianbles 
api_key = os.getenv('OPENAI_API_KEY')       # read from .env file
client  = OpenAI()               # by dfault reads from a variable called "api_key"
message = "Write a js function"         # user type message
response = client.chat.completions.create(         # create a prompt
    model="gpt-4o-mini", 
    messages=[                                    # list of messages
        {"role":"user", "content":message},             
        {"role":"system", "content": "you are a web developer"}
    ],
    response_format={"type":"json_object"}           # optional ( in system prompt you can insist on it)
)
print(response.choices[0].message.content)
```

## Anthropic usage

```py
from anthropic import Anthropic

client = Anthropic(api_key="YOUR_API_KEY")

with client.messages.stream(
    model="claude-3-opus",  
    max_tokens=300,                 # max tokens Claude is allowed to generate in this response
    messages=[
        {"role": "developer", "content": "Explain how AI works"}
    ],
) as stream:
    for event in stream:
        if event.type == "message_delta":
            print(event.delta, end="")   # incremental output
```

## Genai usage

```py
from google import genai

client = genai.Client()

response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Explain how AI works in a few words"
)
print(response.text)
```


### tyeps of messages
+ types of roles:
1. system prompt        ---> what task they are performing ---> sets role/behavior ("You are a helpful assistant")
2. user prompt          ---> conversation with user         ---> actual question/request

```py
[
    {"role": "system", "content": "you are a developer"},
    {"role": "user", "content": "write a js function"},
]
```

3. assistant prompt
+ use case: coversation 2 ai with each other

```py
[
    {"role": "system", "content": "you are a developer"},
    {"role": "user", "content": "write a js function"},
    {"role": "assistant", "content": "prior AI response, useful in multi-turn conversations"}, # Assistant message: (optional)
    {"role": "assistant", "content": "another user message"}, # follow-up question
]
```


## gradio

```py
# Opens a web app in your browser with a text box → type a name → see the greeting.
import gradio as gr                # a minimal web server in the background

def greet(name): # Define a Python function
    return "Hello " + name + "!"

demo = gr.Interface(fn=greet, inputs="text", outputs="text")
# Create a Gradio interface
iface = gr.Interface(
    fn=greet,              # function to wrap
    inputs="text",         # type of input widget
    outputs="text"         # type of output widget
)
iface.launch()    # Launch the interface in a browser
iface.launch(share=True)    # share in the network
```


