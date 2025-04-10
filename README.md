# IDA Pro MCP

Simple [MCP Server](https://modelcontextprotocol.io/introduction) to allow vibe reversing in IDA Pro.

https://github.com/user-attachments/assets/6ebeaa92-a9db-43fa-b756-eececce2aca0

The binaries and prompt for the video are available in the [mcp-reversing-dataset](https://github.com/mrexodia/mcp-reversing-dataset) repository.

Available functionality:

- `check_connection`: Check if the IDA plugin is running.
- `get_metadata()`: Get metadata about the current IDB.
- `get_function_by_name(name)`: Get a function by its name.
- `get_function_by_address(address)`: Get a function by its address.
- `get_current_address()`: Get the address currently selected by the user.
- `get_current_function()`: Get the function currently selected by the user.
- `convert_number(text, size)`: Convert a number (decimal, hexadecimal) to different representations.
- `list_functions(offset, count)`: List all functions in the database (paginated).
- `list_strings(offset, count)`: List all strings in the database (paginated).
- `search_strings(pattern, offset, count)`: Search for strings containing the given pattern (case-insensitive).
- `decompile_function(address)`: Decompile a function at the given address.
- `disassemble_function(start_address)`: Get assembly code (address: instruction; comment) for a function.
- `get_xrefs_to(address)`: Get all cross references to the given address.
- `get_entry_points()`: Get all entry points in the database.
- `set_comment(address, comment)`: Set a comment for a given address in the function disassembly and pseudocode.
- `rename_local_variable(function_address, old_name, new_name)`: Rename a local variable in a function.
- `rename_global_variable(old_name, new_name)`: Rename a global variable.
- `set_global_variable_type(variable_name, new_type)`: Set a global variable's type.
- `rename_function(function_address, new_name)`: Rename a function.
- `set_function_prototype(function_address, prototype)`: Set a function's prototype.
- `declare_c_type(c_declaration)`: Create or update a local type from a C declaration.
- `set_local_variable_type(function_address, variable_name, new_type)`: Set a local variable's type.

- `dump_memory(address, size, terminator_pattern_hex, element_size, max_size, save_to_file, filename)`: Get memory dump (Appropriate for analysis with Python)
- `get_data_info(address)` : Get data infomation
- `get_struct_size(name)` : Get struct or union size

## Prerequisites

- [Python](https://www.python.org/downloads/) (**3.11 or higher**) 
  - Use `idapyswitch` to switch to the newest Python version
- [IDA Pro](https://hex-rays.com/ida-pro) (8.3 or higher, 9 recommended)
- Supported MCP Client (pick one you like)
  - [Cline](https://cline.bot)
  - [Roo Code](https://marketplace.visualstudio.com/items?itemName=RooVeterinaryInc.roo-cline)
  - [Claude](https://claude.ai/download)

## Installation

Install (or upgrade) the IDA Pro MCP package:

```sh
# pip install --upgrade git+https://github.com/mrexodia/ida-pro-mcp
pip install --upgrade git+https://github.com/qw3rtyou/ida-pro-mcp
```

Configure the MCP servers and install the IDA Plugin:

```
ida-pro-mcp --install
```

**Important**: Make sure you completely restart IDA/Visual Studio Code/Claude for the installation to take effect. Claude runs in the background and you need to quit it from the tray icon.

https://github.com/user-attachments/assets/65ed3373-a187-4dd5-a807-425dca1d8ee9

## Prompt Engineering

LLMs are prone to hallucinations and you need to be specific with your prompting. For reverse engineering the conversion between integers and bytes are especially problematic. Below is a minimal example prompt, feel free to start a discussion or open an issue if you have good results with a different prompt:

> Your task is to analyze a crackme in IDA Pro. You can use the MCP tools to retrieve information. In general use the following strategy:
> - Inspect the decompilation and add comments with your findings
> - Rename variables to more sensible names
> - Change the variable and argument types if necessary (especially pointer and array types)
> - Change function names to be more descriptive
> - If more details are necessary, disassemble the function and add comments with your findings
> - NEVER convert number bases yourself. Use the convert_number MCP tool if needed!
> - Do not attempt brute forcing, derive any solutions purely from the disassembly and simple python scripts
> - Create a report.md with your findings and steps taken at the end
> - When you find a solution, prompt to user for feedback with the password you found

This prompt was just the first experiment, please share if you found ways to improve the output!

## Tips for Enhancing LLM Accuracy

Large Language Models (LLMs) are powerful tools, but they can sometimes struggle with complex mathematical calculations or exhibit "hallucinations" (making up facts). Make sure to tell the LLM to use the `conver_number` MCP and you might also need [math-mcp](https://github.com/EthanHenrickson/math-mcp) for certain operations.

Another thing to keep in mind is that LLMs will not perform well on obfuscated code. Before trying to use an LLM to solve the problem, take a look around the binary and spend some time (automatically) removing the following things:

- String encryption
- Import hashing
- Control flow flattening
- Code encryption
- Anti-decompilation tricks

You should also use a tool like Lumina or FLIRT to try and resolve all the open source library code and the C++ STL, this will further improve the accuracy.

## Manual Installation

_Note_: This section is for LLMs and power users who need detailed installation instructions.

<details>

## Manual MCP Server Installation (Cline/Roo Code)

To install the MCP server yourself, follow these steps:

1. Install [uv](https://github.com/astral-sh/uv) globally:
   - Windows: `pip install uv`
   - Linux/Mac: `curl -LsSf https://astral.sh/uv/install.sh | sh`
2. Clone this repository, for this example `C:\MCP\ida-pro-mcp`.
3. Navigate to the Cline/Roo Code _MCP Servers_ configuration (see screenshot).
4. Click on the _Installed_ tab.
5. Click on _Configure MCP Servers_, which will open `cline_mcp_settings.json`.
6. Add the `ida-pro-mcp` server:

```json
{
  "mcpServers": {
    "github.com/mrexodia/ida-pro-mcp": {
      "command": "uv",
      "args": [
        "--directory",
        "c:\\MCP\\ida-pro-mcp",
        "run",
        "server.py",
        "--install-plugin"
      ],
      "timeout": 1800,
      "disabled": false,
      "autoApprove": [
        "check_connection",
        "get_metadata",
        "get_function_by_name",
        "get_function_by_address",
        "get_current_address",
        "get_current_function",
        "convert_number",
        "list_functions",
        "list_strings",
        "search_strings",
        "decompile_function",
        "disassemble_function",
        "get_xrefs_to",
        "get_entry_points",
        "set_comment",
        "rename_local_variable",
        "rename_global_variable",
        "set_global_variable_type",
        "rename_function",
        "set_function_prototype",
        "declare_c_type",
        "set_local_variable_type"
      ],
      "alwaysAllow": [
        "check_connection",
        "get_metadata",
        "get_function_by_name",
        "get_function_by_address",
        "get_current_address",
        "get_current_function",
        "convert_number",
        "list_functions",
        "list_strings",
        "search_strings",
        "decompile_function",
        "disassemble_function",
        "get_xrefs_to",
        "get_entry_points",
        "set_comment",
        "rename_local_variable",
        "rename_global_variable",
        "set_global_variable_type",
        "rename_function",
        "set_function_prototype",
        "declare_c_type",
        "set_local_variable_type"
      ]
    }
  }
}
```

To check if the connection works you can perform the following tool call:

```
<use_mcp_tool>
<server_name>github.com/mrexodia/ida-pro-mcp</server_name>
<tool_name>check_connection</tool_name>
<arguments></arguments>
</use_mcp_tool>
```

## IDA Plugin installation

The IDA Pro plugin will be installed automatically when the MCP server starts. If you disabled the `--install-plugin` option, use the following steps:

1. Copy (**not move**) `src/ida_pro_mcp/mcp-plugin.py` in your plugins folder (`%appdata%\Hex-Rays\IDA Pro\plugins` on Windows).
2. Open an IDB and click `Edit -> Plugins -> MCP` to start the server.

</details>

## Comparison with other MCP servers

There are a few IDA Pro MCP servers floating around, but I created my own for a few reasons:

1. Installation should be fully automated.
2. The architecture of other plugins make it difficult to add new functionality quickly (too much boilerplate of unnecessary dependencies).
3. Learning new technologies is fun!

If you want to check them out, here is a list (in the order I discovered them):

- https://github.com/taida957789/ida-mcp-server-plugin (SSE protocol only, requires installing dependencies in IDAPython).
- https://github.com/fdrechsler/mcp-server-idapro (MCP Server in TypeScript, excessive boilerplate required to add new functionality).
- https://github.com/MxIris-Reverse-Engineering/ida-mcp-server (custom socket protocol, boilerplate).

Feel free to open a PR to add your IDA Pro MCP server here.

## Development

Adding new features is a super easy and streamlined process. All you have to do is add a new `@jsonrpc` function to [`mcp-plugin.py`](https://github.com/mrexodia/ida-pro-mcp/blob/164df8cf4ae251cc9cc0f464591fa6df8e0d9df4/src/ida_pro_mcp/mcp-plugin.py#L406-L419) and your function will be available in the MCP server without any additional boilerplate! Below is a video where I add the `get_metadata` function in less than 2 minutes (including testing):

https://github.com/user-attachments/assets/951de823-88ea-4235-adcb-9257e316ae64

To test the MCP server itself:

```sh
uv run fastmcp dev server.py
```

This will open a web interface at http://localhost:5173 and allow you to interact with the MCP tools for testing.

For testing I create a symbolic link to the IDA plugin and then POST a JSON-RPC request directly to `http://localhost:13337/mcp`. After [enabling symbolic links](https://learn.microsoft.com/en-us/windows/apps/get-started/enable-your-device-for-development) you can run the following command:

```sh
uv run ida-pro-mcp --install
```
