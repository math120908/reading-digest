# Tool Use with Claude

## Overview

Tool use (also known as function calling) allows Claude to interact with external tools, APIs, and functions, enabling you to extend Claude's capabilities to perform a wider variety of tasks. When you provide Claude with tool definitions, it can request to use appropriate tools to help complete tasks.

## How Tool Use Works

Claude doesn't call tools directly. Instead, Claude:
1. Analyzes the user's request and available tools
2. Decides when a tool would be helpful
3. Requests to use a tool with specific arguments in a structured format
4. Waits for you to execute the tool and return results
5. Uses the tool results to continue the task

This design gives you complete control over tool execution while leveraging Claude's intelligence to determine when and how tools should be used.

## Key Concepts

### Tool Definitions

Tools are defined using JSON schemas that specify:
- **name**: Unique identifier for the tool
- **description**: What the tool does and when to use it (extremely important for tool selection)
- **input_schema**: JSON Schema defining the expected parameters

Example tool definition:
```json
{
  "name": "get_weather",
  "description": "Get the current weather in a given location. Use this when the user asks about weather conditions, temperature, or forecast.",
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
        "description": "The unit of temperature"
      }
    },
    "required": ["location"]
  }
}
```

### Tool Choice

Control when Claude can use tools with the `tool_choice` parameter:
- **auto** (default): Claude decides whether to use tools
- **any**: Claude must use at least one tool
- **tool**: Claude must use a specific tool
- **none**: Claude cannot use tools (useful for conversational responses)

### Best Practices for Tool Descriptions

**Providing extremely detailed descriptions is by far the most important factor in tool performance.** Your tool descriptions should:
- Be at least 3-4 sentences long (more for complex tools)
- Explain what the tool does
- Specify when it should be used
- Include relevant keywords users might mention
- Clarify any limitations or constraints

**Bad example:** "Gets weather data"

**Good example:** "Get the current weather conditions and forecast for a specific location. Use this when the user asks about weather, temperature, forecast, or current conditions in any city. Returns temperature, conditions, humidity, and wind speed. Requires a valid city name or coordinates."

## Advanced Tool Use Features (Beta)

Anthropic has introduced three advanced features that dramatically improve tool use efficiency:

### 1. Tool Search Tool

**Problem**: Loading hundreds or thousands of tool definitions consumes Claude's context window, leaving less space for actual reasoning.

**Solution**: The Tool Search Tool allows Claude to discover tools on-demand instead of loading all tool definitions upfront. Tools are marked with `defer_loading: true`, and Claude only loads the full definitions when needed.

**Benefits**:
- 85% reduction in token usage
- Maintain access to your full tool library
- Improved performance with large tool catalogs

**Usage**: Add beta header `"advanced-tool-use-2025-11-20"` to your API requests.

### 2. Programmatic Tool Calling

**Problem**: Traditional tool use requires API round-trips for each tool call, with results returned to Claude's context window.

**Solution**: Programmatic Tool Calling allows Claude to write code that calls multiple tools within a code execution environment, processes their outputs, and controls what information enters its context.

**Benefits**:
- Reduced latency (no round-trips for each tool)
- 37% token efficiency gains
- Better handling of large datasets (e.g., spreadsheets with thousands of rows)
- Multi-step workflows executed in code

**Usage**:
- Add beta header `"advanced-tool-use-2025-11-20"`
- Set `allowed_callers` parameter on tools
- Claude writes code using available tools programmatically

**Example use case**: Claude for Excel uses this to read and modify spreadsheets with thousands of rows without overloading the model's context window.

### 3. Tool Use Examples

**Problem**: Complex tools with many parameters can be difficult for Claude to use correctly.

**Solution**: Provide concrete sample tool calls alongside schemas to demonstrate proper usage.

**Benefits**:
- Accuracy improvement from 72% to 90% in parameter handling
- Better understanding of edge cases
- Clearer expectations for tool input/output

**Implementation**: Include example calls in your tool definitions showing typical usage patterns.

## SDK Tool Helpers (Beta)

Tool helpers are available in the Python, TypeScript, and Ruby SDKs, providing:

### Tool Runner
Automated tool handling with:
- Type-safe input validation
- Automatic tool execution
- Result handling and context management
- Support for multi-turn conversations

### Benefits
- Simplified tool creation
- Built-in validation
- Reduced boilerplate code
- Consistent error handling

## Implementation Workflow

### Basic Tool Use Flow

1. **Define Tools**: Create tool definitions with names, descriptions, and schemas
2. **Send Request**: Include tools in your Messages API request
3. **Check Response**: Look for `tool_use` content blocks
4. **Execute Tools**: Run the requested tools with provided arguments
5. **Return Results**: Send tool results back to Claude in a new message
6. **Continue**: Claude uses results to complete the task

### Example API Request Structure

```json
{
  "model": "claude-sonnet-4-5-20250929",
  "max_tokens": 1024,
  "tools": [
    {
      "name": "calculator",
      "description": "Performs basic arithmetic operations...",
      "input_schema": { /* ... */ }
    }
  ],
  "messages": [
    {
      "role": "user",
      "content": "What is 25 * 37?"
    }
  ]
}
```

### Example Response with Tool Use

```json
{
  "content": [
    {
      "type": "text",
      "text": "I'll calculate that for you."
    },
    {
      "type": "tool_use",
      "id": "toolu_123",
      "name": "calculator",
      "input": {
        "operation": "multiply",
        "a": 25,
        "b": 37
      }
    }
  ],
  "stop_reason": "tool_use"
}
```

### Returning Tool Results

```json
{
  "role": "user",
  "content": [
    {
      "type": "tool_result",
      "tool_use_id": "toolu_123",
      "content": "925"
    }
  ]
}
```

## Built-in Tools in Claude Code

Claude Code and the Agent SDK provide several built-in tools:

- **Bash**: Execute shell commands
- **Code Execution**: Run Python, JavaScript, and other code
- **Computer Use**: Interact with computer interfaces (screens, mouse, keyboard)
- **Text Editor**: Read and modify files
- **Web Fetch**: Retrieve content from URLs
- **Web Search**: Search the internet for information
- **Memory Tool**: Store and retrieve information across sessions
- **Tool Search Tool**: Discover tools on-demand from large catalogs

## Model Recommendations

- **Claude Sonnet 4.5** or **Claude Opus 4.5**: Recommended for complex tools and ambiguous queries
  - Better handling of multiple tools
  - Improved ability to seek clarification
  - More accurate parameter selection

- **Earlier models**: Suitable for simpler, well-defined tool use cases

## Token Efficiency Considerations

### Traditional Approach
- All tool definitions loaded into context
- Each tool result returned to context
- Context fills quickly with large tool catalogs

### Optimized Approach (with Advanced Features)
- Use Tool Search Tool for large catalogs (85% reduction)
- Use Programmatic Tool Calling for multi-step workflows (37% improvement)
- Provide examples for complex tools (90% accuracy)

## Common Use Cases

1. **Data Retrieval**: Fetch information from APIs, databases, or search engines
2. **Calculations**: Perform complex mathematical operations
3. **System Integration**: Interact with external systems and services
4. **File Operations**: Read, write, and analyze files
5. **Workflow Automation**: Chain multiple operations together
6. **Customer Service**: Query CRM systems, check order status, etc.
7. **Code Execution**: Run and test code in various languages

## Error Handling

When tools encounter errors:
- Return clear error messages in the tool_result
- Include enough detail for Claude to understand what went wrong
- Claude can retry with different parameters or choose alternative tools

Example error result:
```json
{
  "type": "tool_result",
  "tool_use_id": "toolu_123",
  "content": "Error: Location 'Atlantis' not found. Please provide a valid city name.",
  "is_error": true
}
```

## Best Practices Summary

1. **Write detailed tool descriptions** (3-4+ sentences with keywords)
2. **Use clear parameter names** and descriptions
3. **Provide examples** for complex tools
4. **Set appropriate tool_choice** based on your use case
5. **Handle errors gracefully** with informative messages
6. **Consider token efficiency** with large tool catalogs
7. **Test thoroughly** with representative queries
8. **Use latest models** (Sonnet 4.5 or Opus 4.5) for best results
9. **Leverage advanced features** (Tool Search, Programmatic Calling) when available
10. **Validate tool inputs** before execution

## Resources

- **Implementation Guide**: See the detailed implementation documentation for code examples
- **Anthropic Courses**: Tool use tutorial with six progressive lessons
- **Claude Cookbooks**: Repository of ready-to-implement tool use examples
- **API Reference**: Complete tool use API documentation
- **GitHub Examples**: anthropics/claude-cookbooks and anthropics/courses repositories

## Platform Support

Tool use is available on:
- Anthropic API
- Amazon Bedrock
- Google Cloud Vertex AI
- Azure (via partner integrations)

Advanced features require the beta header: `"advanced-tool-use-2025-11-20"`

## Related Documentation

- How to implement tool use (detailed implementation guide)
- Programmatic tool calling (advanced beta feature)
- Tool search tool (dynamic tool discovery)
- Code execution tool (run code in sandboxed environment)
- Computer use tool (interact with computer interfaces)
- Memory tool (persistent information storage)

---

*Last updated: January 2026*
*Model recommendations: Claude Sonnet 4.5, Claude Opus 4.5*
*Beta features: Tool Search Tool, Programmatic Tool Calling, Tool Use Examples*
