# 🛠️ Tools in LangChain

A beginner-friendly notebook demonstrating how **Tools** work in **LangChain**. This project explores built-in tools, custom tool creation, structured tools, BaseTool implementation, and Toolkits, helping developers understand how Large Language Models (LLMs) interact with external functionalities.

---

## 📖 Overview

Large Language Models are excellent at reasoning and generating text, but they cannot directly perform actions like:

- Searching the web
- Running shell commands
- Querying databases
- Calling APIs
- Performing calculations
- Reading local files

To overcome these limitations, **LangChain Tools** provide a standardized interface that allows an LLM to interact with external systems.

This notebook demonstrates different ways to create and use tools in LangChain, progressing from built-in tools to fully customized implementations.

---

## 🎯 Learning Objectives

After completing this notebook, you'll understand how to:

- Install and configure LangChain tools
- Use built-in LangChain tools
- Execute internet searches using DuckDuckGo
- Run shell commands safely
- Create custom tools using the `@tool` decorator
- Build tools using `StructuredTool`
- Create advanced tools by inheriting from `BaseTool`
- Group multiple tools into reusable Toolkits
- Understand tool schemas and argument validation

---

# 🧠 What are Tools?

A **Tool** is a function that an LLM can call whenever it needs external information or needs to perform an action.

Instead of asking the model to memorize everything, we allow it to use specialized functions whenever required.

For example:

**User**

> What are today's top news headlines?

Without tools:

```
LLM ❌
"I don't have real-time information."
```

With tools:

```
LLM
      │
      ▼
DuckDuckGo Search Tool
      │
      ▼
Latest News
      │
      ▼
Final Response
```

The LLM decides **which tool to call**, provides the required inputs, receives the output, and then generates the final response.

---

# 🏗️ Project Structure

```
tools-in-langchain/
│
├── tools_in_langchain.ipynb
├── README.md
```

---

# ⚙️ Installation

Clone the repository

```bash
git clone https://github.com/Swizknife/tools-in-langchain.git
```

Move into the project

```bash
cd tools-in-langchain
```

Install dependencies

```bash
pip install langchain
pip install langchain-core
pip install langchain-community
pip install langchain_experimental
pip install duckduckgo-search
pip install pydantic
```

Or

```bash
pip install langchain langchain-core langchain-community langchain_experimental duckduckgo-search pydantic
```

---

# 📚 Notebook Contents

## 1. Installing Dependencies

The notebook begins by installing the required LangChain packages.

```python
pip install langchain
pip install langchain-community
pip install duckduckgo-search
```

These libraries provide:

- LangChain core functionality
- Community tools
- Search tools
- Pydantic for schema validation

---

# 2. Built-in Tools

LangChain already provides numerous ready-to-use tools.

Examples include:

- Search Engines
- Python REPL
- File System
- SQL
- Shell
- APIs
- Web Browsing

This notebook demonstrates two of them.

---

## DuckDuckGo Search Tool

The notebook imports

```python
DuckDuckGoSearchRun
```

and performs a live web search.

Example

```python
search_tool.invoke("top news in india today")
```

The tool returns current news directly from DuckDuckGo.

### Why use it?

LLMs have knowledge cutoffs.

Search tools allow them to retrieve:

- Latest news
- Current events
- Weather
- Sports
- Recent technologies

instead of relying solely on pre-trained knowledge.

---

## Shell Tool

The notebook also demonstrates

```python
ShellTool()
```

which executes terminal commands.

Example

```python
shell_tool.invoke("ls")
```

Output

```
sample_data
```

This allows an AI application to interact with the operating system.

Examples include

- Listing files
- Running scripts
- Creating directories
- Managing projects

> **⚠️ Warning:** ShellTool executes system commands. It should only be used in trusted environments because it has no built-in safeguards.

---

# 3. Creating Custom Tools

Not every application requires built-in tools.

Often you'll need your own custom functions.

LangChain allows any Python function to become a Tool.

The notebook demonstrates this step-by-step.

---

## Step 1

Create a normal function

```python
def multiply(a,b):
    return a*b
```

---

## Step 2

Add type hints

```python
def multiply(a:int,b:int)->int:
```

Type hints help LangChain understand

- expected inputs
- output types

---

## Step 3

Convert it into a Tool

```python
@tool
```

Now the function becomes callable by an LLM.

Example

```python
multiply.invoke({
"a":3,
"b":5
})
```

Output

```
15
```

---

# Tool Metadata

Every LangChain tool automatically exposes metadata.

The notebook prints

```python
multiply.name
```

```python
multiply.description
```

```python
multiply.args
```

Output includes

- Tool name
- Description
- Input schema

This metadata helps an LLM understand

- when to use the tool
- what arguments it accepts
- what the tool returns

---

# Tool Schema

LangChain automatically creates a JSON schema.

Example

```python
multiply.args_schema.model_json_schema()
```

Output

```json
{
"type":"object",
"properties":{
"a":{"type":"integer"},
"b":{"type":"integer"}
}
}
```

The schema enables

- validation
- function calling
- structured outputs

---

# 4. StructuredTool

For more complex applications, tools require richer input validation.

The notebook demonstrates

```python
StructuredTool
```

combined with

```python
Pydantic
```

A custom schema is created using

```python
BaseModel
```

```python
class MultiplyInput(BaseModel):
    a:int
    b:int
```

The tool is then built using

```python
StructuredTool.from_function()
```

Advantages include

- Required fields
- Descriptions
- Automatic validation
- Better documentation

This is recommended when tools require multiple parameters.

---

# 5. BaseTool

The notebook also demonstrates creating a tool by extending

```python
BaseTool
```

Instead of decorating a function, a new class is created.

```python
class MultiplyTool(BaseTool):
```

The implementation includes

- name
- description
- args_schema
- _run()

This method provides the greatest flexibility.

Useful when building

- API wrappers
- Database tools
- Search engines
- Enterprise tools
- Long-running tasks

---

# 6. Toolkits

Applications rarely use a single tool.

Instead, related tools are grouped into Toolkits.

The notebook creates

```python
MathToolkit
```

which returns

```
Add Tool

Multiply Tool
```

using

```python
get_tools()
```

This keeps projects modular and organized.

---

# 🔄 Workflow

```
User Question
       │
       ▼
      LLM
       │
       ▼
Decides Tool Needed
       │
       ▼
Executes Tool
       │
       ▼
Receives Output
       │
       ▼
Generates Final Answer
```

---

# 📊 Comparison

| Method | Best For | Complexity |
|---------|----------|------------|
| `@tool` | Small functions | ⭐ |
| `StructuredTool` | Input validation | ⭐⭐ |
| `BaseTool` | Advanced custom tools | ⭐⭐⭐ |
| Toolkit | Grouping related tools | ⭐⭐ |

---

# 🌍 Real-World Applications

LangChain Tools are widely used in AI systems such as:

- AI Agents
- Personal Assistants
- Chatbots
- Search Engines
- Data Analysis
- Database Querying
- File Management
- Code Generation
- API Automation
- Enterprise Workflow Automation

---

# 🚀 Skills Gained

After completing this notebook, you'll know how to:

- Work with LangChain Tools
- Build custom tools
- Validate tool inputs
- Create reusable Toolkits
- Understand tool metadata
- Implement structured schemas
- Extend BaseTool for complex applications

---

# 📚 Technologies Used

- Python
- LangChain
- LangChain Community
- LangChain Experimental
- DuckDuckGo Search
- Pydantic

---

# 📖 References

- LangChain Documentation: https://python.langchain.com/
- LangChain Tools Documentation: https://python.langchain.com/docs/concepts/tools/
- Pydantic Documentation: https://docs.pydantic.dev/
- DuckDuckGo Search: https://duckduckgo.com/

---

## ⭐ If you found this project useful, consider giving it a star!
