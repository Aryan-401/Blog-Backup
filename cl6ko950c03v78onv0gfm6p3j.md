## What is AI — Working with OpenAI's Models

Artificial Intelligence is often understood as a complicated forte only for those indulged in the field. OpenAI aims to change that with their AI models, which they have made available to the public. In this article, we would go through the setup process and implement a few simple applications in a few lines of code!

## Signing up for OpenAI

The first step to any API service is to [sign up](https://beta.openai.com/signup) for their service. After signing up, log back in and view your API key using [this](https://beta.openai.com/account/api-keys) link and copy your client secret and paste it into your `.env` file in the following format

```.env
OPENAI_API_KEY=key
```

![openai.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1658050607078/78ncAxC63.png align="left")

## Writing your first script

Now that we have our own API keys, we can work with OpenAI's models. there are a bunch to work with, so I would suggest going through their Engine list using the following function:

```python
import openai
from dotenv import load_dotenv
from os import getenv
load_dotenv()
openai.api_key = getenv("OPENAI_API_KEY")
openai.Engine.list()
```
Output:

```json
{
  "data": [
    {
      "created": null,
      "id": "text-davinci-002",
      "object": "engine",
      "owner": "openai",
      "permissions": null,
      "ready": true
    },
    {
      "created": null,
      "id": "text-ada-001",
      "object": "engine",
      "owner": "openai",
      "permissions": null,
      "ready": true
    },
    
    {
      "created": null,
      "id": "babbage-search-document",
      "object": "engine",
      "owner": "openai-dev",
      "permissions": null,
      "ready": true
    },
...
...
...
    {
      "created": null,
      "id": "text-babbage-001",
      "object": "engine",
      "owner": "openai",
      "permissions": null,
      "ready": true
    },

  ],
  "object": "list"
}

```

Although there are about 50 models trained for all your needs, we will be using only a few in this tutorial.

**Note: These APIs are not a free service, and you will be charged for each API call depending on the service and how many tokens are utilized. Open AI does provide $18.00 of credit for trials.**

## Completing a prompt:

Write or copy the following code into your script
```python
import openai
from dotenv import load_dotenv
from os import getenv

prompt = "Maybe I just need sleep."

load_dotenv()
openai.api_key = getenv("OPENAI_API_KEY")

response = openai.Completion.create(
  model="text-davinci-002",
  prompt=prompt,
  temperature=0.3,
  max_tokens=30,
  top_p=1.0,
  frequency_penalty=0.5,
  presence_penalty=0.2
)
print(response)

print(prompt + "..." + response.choices[0].text)
```
The following code will utilize OpenAI's DaVinci Engine on the Prompt "Maybe I just need sleep." It will automatically generate an output with what it seems appropriate

Output:
```
{
  "choices": [
    {
      "finish_reason": "stop",
      "index": 0,
      "logprobs": null,
      "text": ".\n\n\n\nI'm not sure.\n\n\n\nI'm not sure.\n\n\n\nMaybe I just need sleep."
    }
  ],
  "created": 1658052132,
  "id": "cmpl-5UvOGBy3igAZfFuyXofhCoWrQE40w",
  "model": "text-davinci-002",
  "object": "text_completion",
  "usage": {
    "completion_tokens": 30,
    "prompt_tokens": 5,
    "total_tokens": 35
  }
}
Maybe I just need sleep....



I'm not sure.



I'm not sure.



Maybe I just need sleep.
```

We can see that this prompt took up 35 tokens from our credit, this can also be verified on the OpenAI dashboard [here](https://beta.openai.com/account/usage)

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1658052426915/l65MrU5Wb.png align="left")

## Playing with the API

Open AI can also write unique ads, we will only be showcasing the response variable from now along with the output it generates.


>Prompt:  "Create an ad campaign targeted at orphans from the following prompt:\nPrompt: A Phone where you can only call your mother"

>Response:
"A phone where you can only call your mother"

>Orphans need love too. And with this new phone, they can stay connected to the one person who loves them the most: their mother. With this new phone, they can call their mother anytime, anywhere. So don't forget about the orphans when you're shopping for your new phone. They need your love too.


Here is OpenAI trying to tell a joke:

>Prompt: "Tell me a programming joke"

>Response:
Why do programmers always have to write code? Because without code, there would be nothing to debug!


So AI jokes might not be my cup of tea, but at least I can enjoy an AI-generated poem.

>Prompt:  "Write a poem on cotton in 30 words or less: "

>Response: 
Cotton is a soft, fluffy material
that is often used to make clothing.
It is also used to stuff pillows and plush toys.

I don't know how I feel about AI writing totally unique pieces of text, but it is something special.

Lets make a simple chat bot

>Prompt: 
"I am a highly intelligent question answering bot. If you ask me a question that is rooted in truth, I will give you the answer. If you ask me a question that is nonsense, trickery, or has no clear answer, I will respond with "Unknown".

>Q: What is human life expectancy in the United States?
>A: Human life expectancy in the United States is 78 years.

>Q: Who was president of the United States in 1955?
>A: Dwight D. Eisenhower was president of the United States in 1955.

>Q: Which party did he belong to?
>A: He belonged to the Republican Party.

>Q: What is the square root of banana?
>A: Unknown

>Q: How does a telescope work?
>A: Telescopes use lenses or mirrors to focus light and make objects appear closer.

>Q: How many squigs are in a bonk?
>A: Unknown

>Q: Where is the Taj Mahal?
>A:"

>Response:

>The Taj Mahal is located in Agra, India.

Convert Python code into C++ code

> Prompt: "Convert the following python code into C++\narr = [1,7,5,3]\narr.sort()\nprint(arr)"

>```python
arr = [1,7,5,3]
arr.sort()
print(arr)
```

> Response: 
>```c++
std::vector<int> arr = {1,7,5,3};
std::sort(arr.begin(), arr.end());
for(int i : arr) {
    std::cout << i << " ";
}
```

Your Imagination is the limit for this revolutionizing piece of tech. Try out different prompts and be to explore what the API can do, and, more importantly, what it can't do. Make sure to look at OpenAI's other projects, such as Dall-e, a next-generation AI image generator!