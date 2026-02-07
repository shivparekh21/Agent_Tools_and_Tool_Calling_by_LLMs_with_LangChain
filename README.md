# Agent_Tools_and_Tool_Calling_by_LLMs_with_LangChain

##Rule of thumb: If the class name has "Tool", "Run", or "Results" in it, try using it directly first. If it has "Wrapper" or "API", you'll need to wrap it!

## Method 1: Check the class name and imports

```python
# If it ends with "Results", "Run", "Tool" - it's usually ready to use
from langchain_community.tools.tavily_search import TavilySearchResults  # ✓ Direct tool
from langchain_community.tools import WikipediaQueryRun  # ✓ Direct tool
from langchain_community.tools import DuckDuckGoSearchRun  # ✓ Direct tool

# If it ends with "Wrapper", "API", "Client" - it needs Tool wrapping
from langchain_community.utilities import WikipediaAPIWrapper  # ✗ Needs wrapping
from langchain_community.utilities import GoogleSerperAPIWrapper  # ✗ Needs wrapping
```

## Method 2: Check what you're importing from

```python
# From .tools → Usually ready to use directly
from langchain_community.tools import Something  # Probably a complete tool

# From .utilities → Usually needs Tool wrapping  
from langchain_community.utilities import Something  # Probably just a wrapper
```

## Method 3: Inspect the object

```python
# Create the object and check its type/methods
tavily_tool = TavilySearchResults()

# Check if it has tool-specific attributes
print(hasattr(tavily_tool, 'name'))  # Tools have this
print(hasattr(tavily_tool, 'description'))  # Tools have this
print(hasattr(tavily_tool, 'run'))  # Tools have this

# Check the inheritance
print(type(tavily_tool).__bases__)  # See if it inherits from BaseTool
```

## Method 4: Try using it directly

```python
# If this works, it's a complete tool
result = tavily_tool.run("test query")

# If you get an error about missing 'name' or 'description', 
# you need to wrap it in Tool
```

## Quick reference pattern:

```python
# Pattern 1: Direct tool (all-in-one)
tool = TavilySearchResults(...)
result = tool.run("query")

# Pattern 2: Wrapper + QueryRun tool (two-step)
wrapper = WikipediaAPIWrapper(...)
tool = WikipediaQueryRun(api_wrapper=wrapper)
result = tool.run("query")

# Pattern 3: Wrapper + manual Tool (two-step)
wrapper = SomeAPIWrapper(...)
tool = Tool(name="...", func=wrapper.run, description="...")
result = tool.run("query")
```
