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

> [!TIP]
> Instructions are similar to system messages in the chat playground.

```text title="Prompt Instructions"
You are an AI assistant that helps people find hotels. 
In the conversation with the user, your goal is to retrieve the required fields for the function search_hotels.
 
Below is a list of hotels you can reference from:
You are an AI assistant that helps people find hotels. 
In the conversation with the user, your goal is to retrieve the required fields for the function search_hotels.
```

### Add your Function to your Tools

A function has three main parameters: name, description, and parameters.

* Name:a name for your function
* Description: The model is to determine when and how to call the function so it's important to give a meaningful description of what the function does.
* Parameters: is a JSON schema object that describes the parameters that the function accepts.

Below is an example of a sample function:

```json title="function"
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

## Best Practices for Function Calling

- Define **Clear Function Parameters:** Ensure that each function has a clear and concise description, along with well-defined parameters. This helps the model understand when and how to call the function, reducing ambiguity and improving the acuracy of the model's responses.

- **Error Handling:** Include robust error handling in your functions to manage unexpected inputs or failures gracefully.

- **Security Considerations:** Be mindful of security implications, especially when dealing with sensitive data or actions. Ensure that your functions are secure and do not expose vulnerabilities.

- **Iterative Testing:** Continuously test and refine your functions to ensure they work correctly with the model. This helps identify and fix issues early.

- **Parallel Function Calls:** When using parallel function calls, ensure that the functions are independent and do not interfere with each other.

### Parallel function calling with multiple functions

We can create a parallel function to find local attractions in the area. 

First update the prompt instructions with local attractions in the Netherlands:

```
Once you have retrieved the information use tourist_activities to get a list of activities a person can engage in once in the select country.

```

Create a new function referencing the local tourist attractions in the location:

```json title="function"
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

For the Contoso Outdoor Company, we can create a function to search through the database and return a specific product based on the user query. 

First we will need to update the system instructions with a description and sample catalog for the different products:

```
You are an AI assistant that helps people find products based on their searches on the contoso database.

In the conversation with the user, your goal is to retrieve the required fields for the function get_products and respond with the most appropriate product based on the user needs.

## Documents​
The following documentation should be used in the response. The response should specifically include the product id.

{
  "catalog": {
    "Tents": [
      {
        "id": "1",
        "title": "TrailMaster X4 Tent",
        "description": "Crafted from durable polyester, this tent boasts a spacious interior perfect for four occupants. It ensures your dryness under drizzly skies thanks to its water-resistant construction, and the accompanying rainfly adds an extra layer of weather protection.",
        "url": "/products/trailmaster-x4-tent"
      },
      {
        "id": "8",
        "title": "Alpine Explorer Tent",
        "description": "This robust, 8-person, 3-season marvel is from the responsible hands of the AlpineGear brand. Promising an enviable setup that is as straightforward as counting sheep, your camping experience is transformed into a breezy pastime.",
        "url": "/products/alpine-explorer-tent"
      },
      {
        "id": "3",
        "title": "SkyView 2-Person Tent",
        "description": "This tent offers a spacious interior that houses two people comfortably, with room to spare. Crafted from durable waterproof materials to shield you from the elements, it is the fortress you need in the wild.",
        "url": "/products/skyview-2-person-tent"
      }
    ],
    "Backpacks": [
      {
        "id": "2",
        "title": "Adventurer Pro Backpack",
        "description": "Uniquely designed with ergonomic comfort in mind, this backpack ensures a steadfast journey no matter the mileage. It boasts a generous 40L capacity wrapped up in durable nylon fabric ensuring its long-lasting performance on even the most rugged pursuits.",
        "url": "/products/adventurer-pro-backpack"
      },
      {
        "id": "9",
        "title": "SummitClimber Backpack",
        "description": "Your reliable partner for every exhilarating journey. With a generous 60-liter capacity and multiple compartments and pockets, packing is a breeze. Every feature points to comfort and convenience; the ergonomic design and adjustable hip belt ensure a pleasantly personalized fit, while padded shoulder straps protect you from the burden of carrying.",
        "url": "/products/summitclimber-backpack"
      },
      {
        "id": "16",
        "title": "TrailLite Daypack",
        "description": "Built for comfort and efficiency, this lightweight and durable backpack offers a spacious main compartment, multiple pockets, and organization-friendly features all in one sleek package.",
        "url": "/products/traillite-daypack"
      }
    ],
    "Hiking Clothing": [
      {
        "id": "4",
        "title": "Summit Breeze Jacket",
        "description": "This lightweight jacket is your perfect companion for outdoor adventures. Sporting a trail-ready, windproof design and a water-resistant fabric, it's ready to withstand any weather.",
        "url": "/products/summit-breeze-jacket"
      },
      {
        "id": "5",
        "title": "TrailBlaze Hiking Pants",
        "description": "Crafted from high-quality nylon fabric, these dapper troopers are lightweight and fast-drying, with a water-resistant armor that laughs off light rain. Their breathable design whisks away sweat while their articulated knees grant you the flexibility of a mountain goat.",
        "url": "/products/trailblaze-hiking-pants"
      },
      {
        "id": "6",
        "title": "RainGuard Hiking Jacket",
        "description": "The ultimate solution for weatherproof comfort during your outdoor undertakings! Designed with waterproof, breathable fabric, this jacket promises an outdoor experience that's as dry as it is comfortable.",
        "url": "/products/rainguard-hiking-jacket"
      }
    ]
  }
}
```

Then we can create a function to search through the products available on Contoso catalog and return a product similiar to the user requirements.

```
{
  "name": "find_products",
  "description": "Finds products based on a user query. This function uses the Azure Cognitive Search API to find products in the search index based on the provided query. The response includes the most relevant products from the index.",
  "parameters": {
    "type": "object",
    "properties": {
      "query": {
        "type": "string",
        "description": "An optimal search query to find products in the search index using the Azure Cognitive Search API"
      },
      "category": {
        "type": "string",
        "description": "The category of the products to search for, e.g. 'electronics', 'books', etc."
      }
    },
    "required": [
      "query"
    ]
  }
}
```

You can test your functions by asking a question to your model:

```
I need warm waterproof jacket ​to go on a hike.
```

Congratulations! You have now completed the 4th part of the lab and you learnt how to interact with llms using Function Calling. Click next to wrap up the lab.
