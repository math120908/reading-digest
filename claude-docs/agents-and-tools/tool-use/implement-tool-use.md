# How to Implement Tool Use

## Overview

Tool use (also known as function calling) allows Claude to interact with external tools and functions, extending its capabilities beyond text generation. When you provide Claude with tool definitions, it can intelligently decide when to use them, construct properly formatted tool requests, and process the results to continue the conversation.

## Key Concepts

### What is Tool Use?

Tool use enables Claude to:
- Call external APIs and services
- Execute code in a sandboxed environment
- Query databases and retrieve information
- Interact with your application's business logic
- Perform calculations and data transformations

### How It Works

The tool use workflow follows a multi-step process:

1. **Define tools**: You provide tool definitions with names, descriptions, and input schemas
2. **User prompt**: Include a user prompt that might require these tools
3. **Claude assesses**: Claude determines if any tools can help with the query
4. **Tool use request**: If yes, Claude constructs a properly formatted tool use request
5. **Execute tool**: Your application executes the requested tool
6. **Return result**: You return the tool result to Claude
7. **Continue**: Claude processes the result and responds to the user

## Defining Tools

### Tool Definition Structure

Each tool definition includes three required components:

```json
{
  "name": "get_weather",
  "description": "Get the current weather in a given location",
  "input_schema": {
    "type": "object",
    "properties": {
      "location": {
        "type": "string",
        "description": "The city and state, e.g. San Francisco, CA"
      },
      "unit": {
        "type": "string",
        "enum": ["celsius", "fahrenheit"],
        "description": "The unit of temperature, either 'celsius' or 'fahrenheit'"
      }
    },
    "required": ["location"]
  }
}
```

### Tool Definition Components

#### 1. Name
- A unique identifier for the tool
- Should be descriptive and follow naming conventions (e.g., `get_weather`, `calculate_total`)
- Used by Claude to reference the tool in requests

#### 2. Description
- Clear explanation of what the tool does
- Helps Claude understand when to use the tool
- Should include:
  - What the tool accomplishes
  - When it should be used
  - Any important constraints or limitations

#### 3. Input Schema
- Follows **JSON Schema draft 2020-12** specification
- Defines the expected structure of tool inputs
- Components:
  - `type`: Usually "object"
  - `properties`: Defines each parameter with its type and description
  - `required`: Array of mandatory parameter names
  - `additionalProperties`: Set to `false` for strict validation

### Schema Validation

**Structured Outputs** provides guaranteed schema validation for tool inputs:

```json
{
  "name": "create_user",
  "description": "Create a new user account",
  "input_schema": {
    "type": "object",
    "properties": {
      "username": { "type": "string" },
      "email": { "type": "string" },
      "age": { "type": "integer" }
    },
    "required": ["username", "email"],
    "additionalProperties": false
  },
  "strict": true
}
```

Adding `strict: true` ensures:
- Tool inputs always match your schema exactly
- No type mismatches
- No missing required fields
- No extra undeclared fields

## The Tool Use Flow

### 1. Making a Request with Tools

**Python Example:**
```python
import anthropic

client = anthropic.Anthropic(api_key="your_api_key")

response = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    tools=[
        {
            "name": "get_weather",
            "description": "Get the current weather in a given location",
            "input_schema": {
                "type": "object",
                "properties": {
                    "location": {
                        "type": "string",
                        "description": "The city and state, e.g. San Francisco, CA"
                    }
                },
                "required": ["location"]
            }
        }
    ],
    messages=[
        {"role": "user", "content": "What's the weather like in San Francisco?"}
    ]
)
```

**TypeScript Example:**
```typescript
import Anthropic from '@anthropic-ai/sdk';

const client = new Anthropic({
  apiKey: process.env.ANTHROPIC_API_KEY,
});

const response = await client.messages.create({
  model: 'claude-sonnet-4-5-20250929',
  max_tokens: 1024,
  tools: [
    {
      name: 'get_weather',
      description: 'Get the current weather in a given location',
      input_schema: {
        type: 'object',
        properties: {
          location: {
            type: 'string',
            description: 'The city and state, e.g. San Francisco, CA',
          },
        },
        required: ['location'],
      },
    },
  ],
  messages: [
    { role: 'user', content: "What's the weather like in San Francisco?" },
  ],
});
```

### 2. Processing Claude's Response

When Claude decides to use a tool, the response will have:
- `stop_reason`: `"tool_use"`
- One or more `tool_use` content blocks

**Response Structure:**
```json
{
  "id": "msg_123",
  "type": "message",
  "role": "assistant",
  "content": [
    {
      "type": "text",
      "text": "Let me check the weather for you."
    },
    {
      "type": "tool_use",
      "id": "toolu_456",
      "name": "get_weather",
      "input": {
        "location": "San Francisco, CA"
      }
    }
  ],
  "stop_reason": "tool_use"
}
```

### 3. Executing the Tool

Extract the tool use block and execute your actual tool:

**Python:**
```python
# Check if Claude wants to use a tool
if response.stop_reason == "tool_use":
    tool_use = next(block for block in response.content if block.type == "tool_use")
    tool_name = tool_use.name
    tool_input = tool_use.input

    # Execute your tool
    if tool_name == "get_weather":
        tool_result = get_weather(tool_input["location"])
```

**TypeScript:**
```typescript
// Check if Claude wants to use a tool
if (response.stop_reason === 'tool_use') {
  const toolUse = response.content.find(block => block.type === 'tool_use');
  const toolName = toolUse.name;
  const toolInput = toolUse.input;

  // Execute your tool
  if (toolName === 'get_weather') {
    const toolResult = await getWeather(toolInput.location);
  }
}
```

### 4. Returning Tool Results

Send the tool result back to Claude in a new message:

**Python:**
```python
# Continue the conversation with the tool result
follow_up_response = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    tools=tools,  # Include tools again
    messages=[
        {"role": "user", "content": "What's the weather like in San Francisco?"},
        {"role": "assistant", "content": response.content},  # Claude's tool use request
        {
            "role": "user",
            "content": [
                {
                    "type": "tool_result",
                    "tool_use_id": tool_use.id,
                    "content": str(tool_result)
                }
            ]
        }
    ]
)
```

**TypeScript:**
```typescript
// Continue the conversation with the tool result
const followUpResponse = await client.messages.create({
  model: 'claude-sonnet-4-5-20250929',
  max_tokens: 1024,
  tools: tools,  // Include tools again
  messages: [
    { role: 'user', content: "What's the weather like in San Francisco?" },
    { role: 'assistant', content: response.content },  // Claude's tool use request
    {
      role: 'user',
      content: [
        {
          type: 'tool_result',
          tool_use_id: toolUse.id,
          content: JSON.stringify(toolResult),
        },
      ],
    },
  ],
});
```

## Tool Result Format

### Successful Tool Result

```json
{
  "type": "tool_result",
  "tool_use_id": "toolu_456",
  "content": "Temperature: 72°F, Conditions: Sunny"
}
```

### Error Tool Result

When a tool encounters an error:

```json
{
  "type": "tool_result",
  "tool_use_id": "toolu_456",
  "content": "Error: Location not found",
  "is_error": true
}
```

## Tool Choice Parameter

Control how Claude uses tools with the `tool_choice` parameter:

### Options

#### 1. `auto` (default)
Claude decides whether to use any tools:

```python
response = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    tools=tools,
    tool_choice={"type": "auto"},
    messages=messages
)
```

#### 2. `any`
Claude must use one of the provided tools (but can choose which):

```python
response = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    tools=tools,
    tool_choice={"type": "any"},
    messages=messages
)
```

#### 3. `tool`
Force Claude to use a specific tool:

```python
response = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    tools=tools,
    tool_choice={"type": "tool", "name": "get_weather"},
    messages=messages
)
```

## The Agentic Loop

The "agentic loop" refers to the repetition of Claude requesting tools and your application responding with results:

```python
def agentic_loop(client, user_message, tools, max_iterations=10):
    messages = [{"role": "user", "content": user_message}]

    for i in range(max_iterations):
        response = client.messages.create(
            model="claude-sonnet-4-5-20250929",
            max_tokens=4096,
            tools=tools,
            messages=messages
        )

        # Add assistant's response to messages
        messages.append({"role": "assistant", "content": response.content})

        # Check if we're done
        if response.stop_reason != "tool_use":
            return response

        # Execute all tool uses
        tool_results = []
        for block in response.content:
            if block.type == "tool_use":
                result = execute_tool(block.name, block.input)
                tool_results.append({
                    "type": "tool_result",
                    "tool_use_id": block.id,
                    "content": result
                })

        # Add tool results to messages
        messages.append({"role": "user", "content": tool_results})

    raise Exception("Max iterations reached")
```

## Error Handling

### Best Practices

#### 1. Catch Tool Execution Errors

```python
try:
    tool_result = execute_tool(tool_name, tool_input)
    is_error = False
except Exception as e:
    tool_result = f"Error executing tool: {str(e)}"
    is_error = True

# Return error to Claude
tool_result_block = {
    "type": "tool_result",
    "tool_use_id": tool_use.id,
    "content": tool_result,
    "is_error": is_error
}
```

#### 2. Implement Retry Logic with Exponential Backoff

```python
import time
import random

def make_request_with_retry(client, **kwargs):
    max_retries = 5
    base_delay = 1

    for attempt in range(max_retries):
        try:
            return client.messages.create(**kwargs)
        except anthropic.RateLimitError as e:
            if attempt == max_retries - 1:
                raise

            # Check retry-after header
            retry_after = getattr(e, 'retry_after', None)
            if retry_after:
                delay = float(retry_after)
            else:
                # Exponential backoff with jitter
                delay = base_delay * (2 ** attempt) + random.uniform(0, 1)

            time.sleep(delay)
        except anthropic.APIError as e:
            # Handle other API errors
            raise
```

#### 3. Handle Rate Limits (429 Errors)

Key strategies:
- Respect the `retry-after` header in 429 responses
- Implement exponential backoff with jitter
- Use connection pooling
- Consider request caching
- Monitor rate limit headers

#### 4. Validate Tool Inputs

```python
def validate_tool_input(tool_name, tool_input, schema):
    """Validate tool input against schema before execution"""
    import jsonschema

    try:
        jsonschema.validate(instance=tool_input, schema=schema)
        return True, None
    except jsonschema.ValidationError as e:
        return False, str(e)

# Use in tool execution
is_valid, error = validate_tool_input(tool_name, tool_input, tool_schema)
if not is_valid:
    return {
        "type": "tool_result",
        "tool_use_id": tool_use_id,
        "content": f"Invalid input: {error}",
        "is_error": True
    }
```

## SDK Tool Helpers

The Python and TypeScript SDKs provide tool helpers to simplify tool creation and execution.

### Key Features

1. **Type-safe input validation**: Automatic validation using schemas
2. **Tool runner**: Automated tool handling in conversations
3. **Error handling**: Automatic error catching and reporting
4. **Simplified API**: Less boilerplate code

### Tool Runner

The tool runner automates the agentic loop:

**Python Example:**
```python
from anthropic import Anthropic

client = Anthropic()

# Define tools with helper
tools = [...]

# Use tool runner
for message in client.messages.stream(
    model="claude-sonnet-4-5-20250929",
    max_tokens=4096,
    tools=tools,
    messages=[{"role": "user", "content": "What's the weather in SF?"}]
).tool_runner(tool_executor_fn):
    print(message)
```

The tool runner:
- Checks if Claude requested a tool use
- Calls your tool executor function
- Sends results back to Claude automatically
- Yields the next message from Claude
- Handles errors with `is_error: true`

### Benefits

- Reduces boilerplate code
- Handles orchestration automatically (execution, context, retries)
- You just consume the stream
- Built-in error handling

## Advanced Features

### Programmatic Tool Calling (PTC)

Programmatic Tool Calling allows Claude to write code that calls your tools programmatically within a code execution environment, rather than through individual API round-trips.

#### How It Works

1. Claude writes code that calls multiple tools
2. Code executes in a secure sandbox
3. Only final code output enters Claude's context
4. Tool results from programmatic calls are NOT added to context

#### Benefits

- **Token reduction**: Up to 85.6% reduction demonstrated in examples
- **Reduced overhead**: Multiple tool calls in one execution
- **Better orchestration**: Claude can process outputs and control flow
- **Context efficiency**: Only final results consume context

#### Example Flow

Instead of:
```
User → Claude → Tool A → Claude → Tool B → Claude → Response
```

With PTC:
```
User → Claude → [Code: Tool A + Tool B + processing] → Response
```

### Code Execution Tool

The code execution tool allows Claude to:
- Run Bash commands in a secure sandbox
- Manipulate files
- Write and execute code
- Perform system operations

#### Security

The sandbox is built on OS-level primitives:
- **Linux**: bubblewrap
- **macOS**: seatbelt

Restrictions:
- **Filesystem isolation**: Read/write only in working directory
- **Network isolation**: Internet access through proxy only
- **Process isolation**: Limited resource access

## Best Practices

### 1. Write Clear Tool Descriptions

Good:
```json
{
  "name": "get_customer_info",
  "description": "Retrieves customer information from the database using their customer ID. Returns name, email, account status, and purchase history. Use this when you need to look up customer details."
}
```

Bad:
```json
{
  "name": "get_customer_info",
  "description": "Gets customer info"
}
```

### 2. Use Appropriate Models

- **Claude Opus 4.5**: Best for complex tool scenarios and ambiguous queries
- **Claude Sonnet 4.5**: Good balance of performance and cost
- **Claude Haiku 4.5**: Fast and cost-effective for simple tools

### 3. Provide Examples in Descriptions

```json
{
  "name": "search_products",
  "description": "Search for products in the catalog. Supports filters for category, price range, and availability. Example: To find available laptops under $1000, use category='laptops', max_price=1000, available=true"
}
```

### 4. Keep Tool Inputs Simple

- Minimize required parameters
- Use clear, descriptive parameter names
- Provide default values when possible
- Include examples in descriptions

### 5. Handle Errors Gracefully

- Return clear error messages
- Use `is_error: true` for failures
- Provide actionable information
- Log errors for debugging

### 6. Optimize for Context

- Keep tool results concise
- Return only necessary information
- Consider using programmatic tool calling for multi-step operations
- Use context compaction when available

### 7. Test Tool Definitions

- Verify schema validation works
- Test with edge cases
- Check error handling
- Monitor tool usage patterns

### 8. Implement Timeouts

```python
import signal

def timeout_handler(signum, frame):
    raise TimeoutError("Tool execution timed out")

def execute_tool_with_timeout(tool_fn, timeout=30):
    signal.signal(signal.SIGALRM, timeout_handler)
    signal.alarm(timeout)
    try:
        result = tool_fn()
        signal.alarm(0)  # Cancel alarm
        return result
    except TimeoutError as e:
        return {"error": str(e), "is_error": True}
```

### 9. Use Connection Pooling

For tools that make external API calls:

```python
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

# Create session with retry logic
session = requests.Session()
retry = Retry(
    total=3,
    backoff_factor=0.3,
    status_forcelist=[500, 502, 503, 504]
)
adapter = HTTPAdapter(max_retries=retry, pool_connections=10, pool_maxsize=100)
session.mount('http://', adapter)
session.mount('https://', adapter)

def call_external_api(url, params):
    return session.get(url, params=params)
```

### 10. Monitor and Log

```python
import logging

logger = logging.getLogger(__name__)

def execute_tool(tool_name, tool_input):
    logger.info(f"Executing tool: {tool_name}")
    logger.debug(f"Input: {tool_input}")

    start_time = time.time()
    try:
        result = tool_functions[tool_name](tool_input)
        duration = time.time() - start_time
        logger.info(f"Tool {tool_name} completed in {duration:.2f}s")
        return result
    except Exception as e:
        logger.error(f"Tool {tool_name} failed: {str(e)}", exc_info=True)
        raise
```

## Complete Example

Here's a complete working example with multiple tools, error handling, and the agentic loop:

```python
import anthropic
import json
from typing import List, Dict, Any

# Initialize client
client = anthropic.Anthropic(api_key="your_api_key")

# Define tools
tools = [
    {
        "name": "get_weather",
        "description": "Get current weather for a location",
        "input_schema": {
            "type": "object",
            "properties": {
                "location": {
                    "type": "string",
                    "description": "City and state, e.g. San Francisco, CA"
                }
            },
            "required": ["location"]
        }
    },
    {
        "name": "get_time",
        "description": "Get current time for a timezone",
        "input_schema": {
            "type": "object",
            "properties": {
                "timezone": {
                    "type": "string",
                    "description": "Timezone name, e.g. America/New_York"
                }
            },
            "required": ["timezone"]
        }
    }
]

# Tool implementations
def get_weather(location: str) -> str:
    # Simulate API call
    return f"Weather in {location}: 72°F, Sunny"

def get_time(timezone: str) -> str:
    from datetime import datetime
    import pytz
    tz = pytz.timezone(timezone)
    time = datetime.now(tz)
    return f"Current time in {timezone}: {time.strftime('%Y-%m-%d %H:%M:%S')}"

# Tool executor
def execute_tool(tool_name: str, tool_input: Dict[str, Any]) -> str:
    try:
        if tool_name == "get_weather":
            return get_weather(tool_input["location"])
        elif tool_name == "get_time":
            return get_time(tool_input["timezone"])
        else:
            return f"Unknown tool: {tool_name}"
    except Exception as e:
        raise Exception(f"Tool execution failed: {str(e)}")

# Agentic loop
def process_tool_call(user_message: str, max_iterations: int = 10) -> str:
    messages = [{"role": "user", "content": user_message}]

    for iteration in range(max_iterations):
        # Make API request
        response = client.messages.create(
            model="claude-sonnet-4-5-20250929",
            max_tokens=4096,
            tools=tools,
            messages=messages
        )

        print(f"\nIteration {iteration + 1}")
        print(f"Stop reason: {response.stop_reason}")

        # Add assistant message to history
        messages.append({"role": "assistant", "content": response.content})

        # Check if we're done
        if response.stop_reason == "end_turn":
            # Extract final text response
            final_response = next(
                (block.text for block in response.content if hasattr(block, "text")),
                None
            )
            return final_response

        # Process tool uses
        if response.stop_reason == "tool_use":
            tool_results = []

            for block in response.content:
                if block.type == "tool_use":
                    print(f"Tool call: {block.name}")
                    print(f"Input: {block.input}")

                    try:
                        # Execute tool
                        result = execute_tool(block.name, block.input)
                        print(f"Result: {result}")

                        tool_results.append({
                            "type": "tool_result",
                            "tool_use_id": block.id,
                            "content": result
                        })
                    except Exception as e:
                        print(f"Error: {str(e)}")
                        tool_results.append({
                            "type": "tool_result",
                            "tool_use_id": block.id,
                            "content": str(e),
                            "is_error": True
                        })

            # Add tool results to messages
            messages.append({"role": "user", "content": tool_results})
        else:
            # Unexpected stop reason
            raise Exception(f"Unexpected stop reason: {response.stop_reason}")

    raise Exception("Max iterations reached without completion")

# Example usage
if __name__ == "__main__":
    user_query = "What's the weather in San Francisco and what time is it there?"
    result = process_tool_call(user_query)
    print(f"\nFinal response: {result}")
```

## Common Patterns

### Pattern 1: Single Tool Call

Simple query that needs one tool:
```
User: "What's the weather in Boston?"
→ Claude: [tool_use: get_weather]
→ App: [executes, returns result]
→ Claude: "The weather in Boston is..."
```

### Pattern 2: Sequential Tool Calls

Multiple tools needed in sequence:
```
User: "Get customer info and their recent orders"
→ Claude: [tool_use: get_customer_info]
→ App: [returns customer data]
→ Claude: [tool_use: get_orders]
→ App: [returns order data]
→ Claude: "Here's the information..."
```

### Pattern 3: Conditional Tool Use

Tool use based on previous results:
```
User: "Check if item is in stock and if so, get price"
→ Claude: [tool_use: check_stock]
→ App: [returns: in stock]
→ Claude: [tool_use: get_price]
→ App: [returns: $29.99]
→ Claude: "Yes, it's in stock for $29.99"
```

### Pattern 4: Parallel Tool Calls

Multiple independent tools:
```
User: "Compare weather in NYC and LA"
→ Claude: [tool_use: get_weather(NYC), tool_use: get_weather(LA)]
→ App: [returns both results]
→ Claude: "NYC: 65°F, LA: 78°F..."
```

## Troubleshooting

### Issue: Claude isn't using tools

**Solutions:**
- Make tool descriptions more specific
- Include relevant keywords in descriptions
- Use `tool_choice: "any"` to force tool use
- Ensure user query actually needs the tool
- Check that tool is appropriate for the task

### Issue: Invalid tool inputs

**Solutions:**
- Add `strict: true` for schema validation
- Provide clearer parameter descriptions
- Include examples in descriptions
- Validate schema follows JSON Schema draft 2020-12

### Issue: Too many tool calls

**Solutions:**
- Use programmatic tool calling
- Combine related operations into single tools
- Provide more context in tool results
- Implement automatic context compaction

### Issue: Rate limit errors (429)

**Solutions:**
- Implement exponential backoff with jitter
- Respect `retry-after` header
- Use connection pooling
- Consider caching tool results
- Upgrade to higher rate limit tier

### Issue: Tool execution timeouts

**Solutions:**
- Implement timeouts on tool execution
- Break complex operations into smaller tools
- Use async execution where possible
- Optimize tool implementations
- Return partial results when possible

## Resources

### Official Documentation
- [Tool Use Overview](https://platform.claude.com/docs/en/agents-and-tools/tool-use/overview)
- [Programmatic Tool Calling](https://platform.claude.com/docs/en/agents-and-tools/tool-use/programmatic-tool-calling)
- [Code Execution Tool](https://platform.claude.com/docs/en/agents-and-tools/tool-use/code-execution-tool)
- [Structured Outputs](https://platform.claude.com/docs/en/build-with-claude/structured-outputs)
- [Messages API Reference](https://platform.claude.com/docs/en/api/messages)

### Cookbooks and Examples
- [Claude Cookbook](https://platform.claude.com/cookbook)
- [GitHub: claude-cookbooks](https://github.com/anthropics/claude-cookbooks)
- [Calculator Tool Example](https://github.com/anthropics/anthropic-cookbook/blob/main/tool_use/calculator_tool.ipynb)
- [Tool Choice Cookbook](https://platform.claude.com/cookbook/tool-use-tool-choice)
- [Programmatic Tool Calling Cookbook](https://platform.claude.com/cookbook/tool-use-programmatic-tool-calling-ptc)

### SDK Documentation
- [Agent SDK Overview](https://platform.claude.com/docs/en/agent-sdk/overview)
- [TypeScript SDK Reference](https://platform.claude.com/docs/en/agent-sdk/typescript)
- [Custom Tools](https://docs.claude.com/en/api/agent-sdk/custom-tools)

### Learning Resources
- [Anthropic Academy](https://www.anthropic.com/learn/build-with-claude)
- [Advanced Tool Use Blog Post](https://www.anthropic.com/engineering/advanced-tool-use)
- [Building Agents with Claude SDK](https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk)
- [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices)

### Community Resources
- [Beginners Guide to Tool Use (Medium)](https://medium.com/@judeaugustinej/beginners-guide-to-tools-usage-in-claude-39d910ff76da)
- [Mastering Claude Tool API (SparkCo)](https://sparkco.ai/blog/mastering-claude-tool-api-a-deep-dive-for-developers)
- [Claude Function Calling (Composio)](https://composio.dev/blog/claude-function-calling-tools)

### AWS Bedrock Documentation
- [Tool Use on Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/model-parameters-anthropic-claude-messages-tool-use.html)

---

## Summary

Tool use is a powerful feature that extends Claude's capabilities by allowing it to interact with external systems and functions. Key takeaways:

1. **Define clear tools** with comprehensive descriptions and schemas
2. **Implement the agentic loop** to handle multi-turn tool use conversations
3. **Handle errors gracefully** with proper error messages and retry logic
4. **Use SDK helpers** to simplify implementation and reduce boilerplate
5. **Consider advanced features** like programmatic tool calling for complex workflows
6. **Follow best practices** for production deployments
7. **Monitor and optimize** tool usage for performance and cost

With proper implementation, tool use enables sophisticated AI agents that can autonomously accomplish complex tasks by leveraging your application's capabilities.
