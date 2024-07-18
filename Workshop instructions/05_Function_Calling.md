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


### Updating Prompt Instructions to provide context

First update the Prompt Instructions. 
- In this Prompt Instructions explain the goal of the assistant
- Explain the information that needs to be gathered
- Which function to all if all information is gathered

```text title="Prompt Instructions"
You are an AI assistant that helps people find hotels. 
In the conversation with the user, your goal is to retrieve the required fields for the function search_hotels.
 
Below is a list of hotels you can reference from:
You are an AI assistant that helps people find hotels. 
In the conversation with the user, your goal is to retrieve the required fields for the function search_hotels.

Once you have retrieved the information use get_hotels to get the hotel name and return the name to the user.
 
Below is a list of hotels you can reference from:
[
    {
        "name": "Hotel Le Meurice",
        "location": "Paris, France",
        "price": 250,
        "features": "free wifi, breakfast included, pet friendly"
    },
    {
        "name": "W Barcelona",
        "location": "Barcelona, Spain",
        "price": 220,
        "features": "beachfront, free wifi, pool"
    },
    {hotel
        "name": "Hotel Pulitzer",
        "location": "Amsterdam, Netherlands",
        "price": 210,
        "features": "canal view, free wifi, breakfast included"
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

We can create a parallel function to find local attractions in the area. 

First update the prompt instructions with local attractions in the Netherlands:

```
Once you have retrieved the information use tourist activities to get a list of activities a person can engage in once in the select country.
 
#Tourist Activities
Zaanse Schans:
Located around 15 kilometers north of Amsterdam, Zaanse Schans is an open-air museum resembling a traditional Dutch village. Visitors can stroll among green wooden houses, a shipyard, and even witness live demonstrations at iconic windmills. Only five windmills remain, but they offer a glimpse into the past.
Recommended tour: Zaanse Schans Windmills Half-Day Tour

Keukenhof
Keukenhof
Keukenhof (Garden of Europe):
Bursting with colorful blooms, Keukenhof is a must-visit for flower enthusiasts. Known as the Garden of Europe, it showcases tulips, daffodils, and other floral varieties. The vibrant displays stretch as far as the eye can see.
Explore this riot of color during springtime!
Amsterdam Canals:
Similar to Venice, Amsterdam boasts a picturesque canal system. Take a boat tour or stroll along the banks to appreciate the idyllic waterways.
Book a cruise: Amsterdam Evening Canal Cruise

```

Create a new function referencing the local tourist attractions in the location:

```json title="Tools"
{
  "name": "tourist_activities",
  "description": "Determine activities a tourist can take part in",
  "parameters": {
    "type": "object",
    "properties": {
      "location": {
        "type": "string",
        "description": "The tourist location"
      }
    },
    "required": [
      "location"
    ]
  }
}
```

## Bringing it all together

For our Contoso Outdoor Company, we can create a function to search through the database and return a specific product based on the user needs. 

First we will need to update the system instructions:

```
```

Then we can create a function to search through the products available on Contoso catalog and return a product similiar to the user requirements.

```
```

Congratulations! You have now completed the 4th part of the lab and you learnt how to interact with llms using Function Calling. Click next to wrap up the lab.
