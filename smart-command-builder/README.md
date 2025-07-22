# Smart Command Builder

An intelligent command generator that creates complete Commands.com-compatible commands from simple task descriptions. This tool leverages the Commands.com MCP server to discover available capabilities and generate professional-quality command implementations.

## What It Does

The Smart Command Builder takes a task description and automatically:

- **Analyzes** your requirements and selects optimal MCP server combinations
- **Generates** complete command implementations with proper formatting
- **Creates** all necessary files for Commands.com integration
- **Updates** your commands.yaml with the new command entry
- **Ensures** no naming conflicts with your existing commands

## Key Features

- **Intelligent MCP Selection**: Automatically chooses the best MCP servers for your task
- **Complete File Generation**: Creates command.md, README.md, and updates commands.yaml
- **Conflict Prevention**: Checks your existing Commands.com assets to avoid name conflicts
- **Professional Quality**: Follows Commands.com best practices and formatting standards
- **Immediate Integration**: Generated commands are ready for GitHub → Commands.com import

## Usage

```bash
/build-command task_description="Create a tool that analyzes website performance and generates optimization reports"
```

### Parameters

- **task_description** (required): Describe what you want the command to do
- **command_name** (optional): Custom name for the command (auto-generated if not provided)
- **category** (optional): Command category (auto-detected if not specified)
- **complexity** (optional): simple, medium, or advanced (affects feature richness)
- **include_examples** (optional): Whether to include usage examples (default: yes)
- **learning_mode** (optional): Analyze existing patterns for better generation (default: yes)

### Examples

#### Simple File Processing Command
```bash
/build-command task_description="Convert CSV files to JSON format with data validation"
```

#### Advanced Web Analysis Tool
```bash
/build-command 
task_description="Analyze website accessibility, performance, and SEO metrics"
category=development
complexity=advanced
```

#### API Documentation Generator
```bash
/build-command 
task_description="Generate comprehensive API documentation from OpenAPI specs"
command_name="api-doc-generator"
category=api-tools
```

## How It Works

1. **Task Analysis**: Parses your description to understand requirements
2. **MCP Discovery**: Queries available MCP servers and their capabilities
3. **Smart Matching**: Selects optimal MCP combinations based on task needs
4. **Structure Generation**: Creates proper Commands.com command structure
5. **File Creation**: Generates all necessary files with professional formatting
6. **Integration**: Updates your commands.yaml for seamless Commands.com import

## MCP Server Requirements

This command requires only the **Commands.com MCP server** (`commands-com/mcp-server`), which provides:

- Access to available MCP servers and their capabilities
- Commands.com formatting guidelines and specifications
- Your existing asset information for conflict prevention
- Intelligence for optimal MCP server selection

## Generated Files

After running the command, you'll have:

```
your-new-command/
├── command.md          # Complete command implementation
├── README.md          # User documentation
└── preview.png        # Thumbnail placeholder

commands.yaml          # Updated with new command entry
```

## Next Steps

1. **Review** the generated command files
2. **Test** the command locally if desired
3. **Commit** changes to your Git repository
4. **Push** to GitHub
5. **Import to Commands.com**: Go to Commands.com and manually import your new command from your GitHub repository (Commands.com only auto-updates existing commands, not new ones)

## Tips for Best Results

- **Be specific** in your task description
- **Mention key technologies** if relevant (e.g., "React", "Python", "REST API")
- **Specify data sources** if applicable (e.g., "GitHub repositories", "CSV files")
- **Include output format** preferences (e.g., "save to CSV files", "store in database", "generate markdown reports")

## Examples of Task Descriptions

- "Analyze code quality and security vulnerabilities in GitHub repositories"
- "Scrape website data and save structured results to a database"
- "Generate comprehensive API documentation from code repositories"
- "Process CSV files and transform data with validation and reporting"
- "Automate repository analysis and generate development reports"
- "Extract data from multiple websites and compile comparison reports"
- "Analyze database schemas and generate optimization recommendations"

The more specific your description, the better the generated command will match your needs!