# Data Generation with AI

## Learning Goals

- Generate realistic mock data for prototypes using AI.

## Introduction

We have seen and used tools like
[json-server](https://www.npmjs.com/package/json-server) which can create a fake
REST API based on the provided JSON data. We can use AI chatbots to create these
JSON data much faster than by writing functions for generating fake data. It’s
still important to know how to use libraries like Faker to generate seed data
since not all environments will allow using chatbots.

There are two main ways of generating data with chatbots:

1. Prompt the chatbot in plain English to generate data.
2. Use a structured JSON schema to generate data.

The first approach is very straightforward and works fairly well for simple
data. But it can result in invalid JSON for more complex queries. It may also
use more tokens for the API calls to the AI model which results in higher costs.

The second approach is almost guaranteed to produce a valid response and can
also be used more easily when interacting with chatbots through APIs.

## Plain Prompt

In this approach, we prompt the chatbot to generate some fields for an entity
and give it context. We can make further prompts to add or remove fields as
necessary.

Prompt:

```
Generate a list of 5 fields for fresh produce on an online grocery shop.
```

Response:

```
Sure! Here's a list of five fields for fresh produce on an online grocery shop:

1. Name: The name of the fresh produce item, such as "Apples," "Broccoli," "Tomatoes," etc.

2. Description: A brief description or details about the fresh produce item, highlighting its variety, taste, and any specific characteristics or benefits.

3. Price: The price of the fresh produce item, indicating the cost per unit (e.g., per pound, per kilogram) or per package.

4. Quantity/Weight: The quantity or weight of the fresh produce item available for purchase. This field specifies the measurement unit, such as pounds, kilograms, bunches, or individual pieces.

5. Availability: Indicates whether the fresh produce item is currently in stock and available for purchase. This field helps customers know if the item is ready for immediate delivery or if there may be a delay in availability.

These fields provide essential information about fresh produce items on an online grocery shop, helping customers make informed decisions about their purchases. Additional fields can be included based on specific requirements, such as nutritional information, origin or source details, organic certification, and any special handling instructions.
```

Prompt:

```
Include an 'id' field.
```

Response

```
Certainly! Here's an updated list with the 'id' field included:

1. ID: A unique identifier for each fresh produce item. This field assigns a distinct identification number or code to easily reference and track the item within the online grocery shop's database.

2. Name: The name of the fresh produce item, such as "Apples," "Broccoli," "Tomatoes," etc.

3. Description: A brief description or details about the fresh produce item, highlighting its variety, taste, and any specific characteristics or benefits.

4. Price: The price of the fresh produce item, indicating the cost per unit (e.g., per pound, per kilogram) or per package.

5. Quantity/Weight: The quantity or weight of the fresh produce item available for purchase. This field specifies the measurement unit, such as pounds, kilograms, bunches, or individual pieces.

6. Availability: Indicates whether the fresh produce item is currently in stock and available for purchase. This field helps customers know if the item is ready for immediate delivery or if there may be a delay in availability.

Including the 'id' field enables easy identification and management of fresh produce items within the online grocery shop's system.
```

Prompt:

```text
Generate a JSON sample of 3 such entities with realistic values.
```

Response:

```json
[
  {
    "id": "123456",
    "name": "Apples",
    "description": "Fresh and crisp apples from local orchards.",
    "price": 2.99,
    "quantity": 1.5,
    "availability": true
  },
  {
    "id": "789012",
    "name": "Broccoli",
    "description": "Organic broccoli with vibrant green florets.",
    "price": 1.49,
    "quantity": 0.75,
    "availability": true
  },
  {
    "id": "345678",
    "name": "Tomatoes",
    "description": "Juicy and ripe tomatoes, perfect for salads and sandwiches.",
    "price": 0.99,
    "quantity": 3,
    "availability": false
  }
]
```

## Data Generation with JSON Schema

We can use JSON schemas to define exactly what our JSON output should look like.
The fields can even have `description` properties to give additional
instructions to the model.

### JSON Schema Syntax

Each property in the schema can be provided an object with a `type` and
`description` key. The type specifies what kind of data to generate for the
field and the description lets us provide additional context or information for
the field. Here’s the general structure of a JSON schema outer object:

```json
json_schema = {
	"type": "object",
	"properties": { ... }
}
```

The above object is what you would start with when creating any JSON schema. The
object referenced by the `properties` key will hold the keys that will be
included in the JSON output.

Here’s the full general structure of a JSON schema:

```js
json_schema = {
	"type": "object",
	"properties": {
		"key_1": {"type": <data type value>, "description": "additional context or instruction for key_1"},
		"key_2": {"type": <data type value>, "description": "additional context or instruction for key_2"}
	}
}
```

For the `data type value`, the following are supported:

- `number`
- `string`
- `boolean`
- `array`
- `object`

### JSON Schema Example

We will be using the following JSON schema for our prompts:

```js
json_schema = {
  type: "object",
  properties: {
    id: { type: "number" },
    name: { type: "string", description: "produce name" },
    description: { type: "string" },
    price: { type: "number", description: "price of produce in USD" },
    quantity: { type: "number" },
    availability: { type: "boolean" },
  },
};
```

Here’s the example from the first method generated using a JSON schema prompt:

```js
Generate JSON sample data for fresh produce on an online grocery store based on the following JSON schema:

json_schema = {
  type: "object",
  properties: {
    id: { type: "number" },
    name: { type: "string", description: "produce name" },
    description: { type: "string" },
    price: { type: "number", description: "price of produce in USD" },
    quantity: { type: "number" },
    availability: { type: "boolean" },
  },
};

```

Response:

```json
[
  {
    "id": 1,
    "name": "Apples",
    "description": "Fresh and crisp apples from local orchards.",
    "price": 2.99,
    "quantity": 1.5,
    "availability": true
  },
  {
    "id": 2,
    "name": "Broccoli",
    "description": "Organic broccoli with vibrant green florets.",
    "price": 1.49,
    "quantity": 0.75,
    "availability": true
  },
  {
    "id": 3,
    "name": "Tomatoes",
    "description": "Juicy and ripe tomatoes, perfect for salads and sandwiches.",
    "price": 0.99,
    "quantity": 3,
    "availability": false
  }
]
```

Let’s add a `description` field to the `id` key in the schema and see how it
affects the output:

```js
Generate JSON sample data for fresh produce on an online grocery store based on the following JSON schema:

json_schema = {
	"type": "object",
	"properties": {
		"id": {"type": "number", "description": "use a random hexadecimal UUID value"},
		"name": {"type": "string", "description": "produce name"},
		"description": {"type": "string"},
		"price": {"type": "number", "description": "price of produce in USD"},
		"quantity": {"type": "number"},
		"availability": {"type": "boolean"},
	}
}
```

Response:

```json
[
  {
    "id": 428148f6-5e54-4e41-a0d5-82b442e3e7c9,
    "name": "Bananas",
    "description": "Fresh and ripe bananas from tropical farms.",
    "price": 0.29,
    "quantity": 1.5,
    "availability": true
  },
  {
    "id": f7407b68-806a-4dc3-bec6-d2f0211b342a,
    "name": "Carrots",
    "description": "Organic carrots with a vibrant orange color.",
    "price": 1.99,
    "quantity": 0.75,
    "availability": true
  },
  {
    "id": c43b7c0a-9fc3-4742-8d53-2878a0e3fd45,
    "name": "Strawberries",
    "description": "Plump and juicy strawberries, perfect for desserts.",
    "price": 2.49,
    "quantity": 2,
    "availability": false
  }
]
```

The chatbot also mentions the modification:

> Please note that the 'id' values in the sample are random hexadecimal UUID
> values. You can generate new UUID values for each item using a UUID generator
> library or function specific to your programming language or framework. The
> other fields contain realistic sample values based on the provided schema.

Using this method makes it much easier to create more complex, nested schemas
and get the desired output with a single prompt. Check out the
[jsonformer repo](https://github.com/1rgs/jsonformer) and
[this article](https://yonom.substack.com/p/native-json-output-from-gpt-4) for
more examples.

## Conclusion

AI chatbots can drastically simplify the generation of realistic mock data for
prototypes. We have looked at two different methods of data generation and
discussed their trade-offs.

The first method is simpler but involves more overhead and is also less flexible
than the second method. For simpler data generation, the first method may be
viable but the second method is considerably more flexible, especially if data
generation needs to be done with API calls.

## Resources

- [Rapid Mock API Creation with ChatGPT and json-server](https://www.builder.io/blog/mock-api-data-with-chatgpt).
- [Native JSON Output from GPT-4](https://yonom.substack.com/p/native-json-output-from-gpt-4).
- [Jsonformer Repository](https://github.com/1rgs/jsonformer).
