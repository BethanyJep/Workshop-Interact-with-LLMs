# Part 4 - Function Calling

In the previous sections, we have engaged with LLMs through text, image and multimodal prompts. In this section, we will explore another modality - function calling. This allows you to include functions in your requests, and the model will respond with a JSON object containing the function’s arguments. The models are capable of formulating API calls and structuring data outputs based on the functions you provide.

> [!TIP]
> **What is function calling?** GPT-3.5, GPT-4o and GPT-4-turbo models can take user-defined functions as input and generate structured output. The models are capable of formulating API calls and structuring data outputs based on the functions you provide.

The latest versions of gpt-35-turbo and gpt-4 are fine-tuned to work with functions and are able to both determine when and how a function should be called. If one or more functions are included in your request, the model determines if any of the functions should be called based on the context of the prompt. When the model determines that a function should be called, it responds with a JSON object including the arguments for the function.

The models formulate API calls and structure data outputs, all based on the functions you specify. It's important to note that while the models can generate these calls, it's up to you to execute them, ensuring you remain in control.

Working with functions can be broken down into three high-level steps:

- Call the chat completions API with your functions and the user’s input
- Use the model’s response to call your API or function
- Call the chat completions API again, including the response from your function to get a final response


### Updating Prompt to provide context

First update the Prompt. 
- In this Prompt explain the goal of the assistant
- Explain the information that needs to be gathered
- Which function to all if all information is gathered

```text title="Prompt"
You are an AI assistant that helps people find hotels. 
In the conversation with the user, your goal is to retrieve the required fields for the function search_hotels.
 
Below is a list of hotels you can reference from:
[
    {
        "name": "Hotel Le Meurice",
        "location": "Paris, France",
        "price": 250,
        "features": "free wifi, breakfast included, pet friendly"
    },
    {
        "name": "Hotel Artemide",
        "location": "Rome, Italy",
        "price": 200,
        "features": "city center, free wifi, airport shuttle"
    },
    {
        "name": "Hotel Adlon Kempinski",
        "location": "Berlin, Germany",
        "price": 180,
        "features": "free parking, gym, pet friendly"
    },
    {
        "name": "W Barcelona",
        "location": "Barcelona, Spain",
        "price": 220,
        "features": "beachfront, free wifi, pool"
    },
    {
        "name": "Hotel Pulitzer",
        "location": "Amsterdam, Netherlands",
        "price": 210,
        "features": "canal view, free wifi, breakfast included"
    },
    {
        "name": "Hotel Sacher",
        "location": "Vienna, Austria",
        "price": 190,
        "features": "city center, free wifi, spa"
    },
    {
        "name": "Hotel Kings Court",
        "location": "Prague, Czech Republic",
        "price": 170,
        "features": "historic district, free wifi, breakfast included"
    },
    {
        "name": "Bairro Alto Hotel",
        "location": "Lisbon, Portugal",
        "price": 160,
        "features": "free wifi, rooftop bar, pet friendly"
    },
    {
        "name": "Electra Palace Athens",
        "location": "Athens, Greece",
        "price": 230,
        "features": "acropolis view, free wifi, breakfast included"
    },
    {
        "name": "Corinthia Hotel Budapest",
        "location": "Budapest, Hungary",
        "price": 150,
        "features": "thermal baths, free wifi, city center"
    }
]
```

### Add your Function to your Tools

A function has three main parameters: name, description, and parameters.

Description: The model is to determine when and how to call the function so it's important to give a meaningful description of what the function does.

Parameters: is a JSON schema object that describes the parameters that the function accepts.


```json title="Tools"
{
    "name": "search_hotels",
    "description": "Retrieves hotels from the search index based",
    "parameters": {
           "type": "object",             
           "properties": {
                "location": {
                    "type": "string",
                    "description": "The location of the hotel (i.e. Seattle, WA)"
                },
                "price": {
                    "type": "number",
                    "description": "The maximum price for the hotel"
                },
                "features": {
                    "type": "string",
                    "description": "A comma separated list of features (i.e. beachfront, free wifi, etc.)"
                }
            },
           "required": ["location","price","features"]
      }
}
```

### Interacting with the model using function calling

Now let's start a conversation with the agent.

Ask:
```text title="User Message"
I'm looking for a hotel in the Netherlands
```

The agent should start asking you about location, price and hotel features and finally call the function and return the properties in JSON format. 

### Parallel function calling with multiple functions


:::info[Assignment]
Extend the function to ask for how many people the room is.
:::



Congratulations! You have now completed the 4th part of the lab and you learnt how to interact with llms using Function Calling. Click next to wrap up the lab.
