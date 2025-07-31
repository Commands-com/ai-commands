# Performance Agent Builder

Install a specialized Claude Code subagent that automatically monitors, diagnoses, and fixes performance issues in your codebase.

## What It Does

This command transforms Claude Code into a performance optimization powerhouse by:

1. **Installing performance profilers** for your languages (Python, JS, Go, Rust)
2. **Creating a specialized subagent** that knows how to optimize code
3. **Setting up automated workflows** for detect → fix → verify cycles

## Quick Start

```bash
# Auto-detect languages and install
/performance-agent-builder

# Install for specific languages
/performance-agent-builder languages=python,javascript

# Install globally (all projects)
/performance-agent-builder install_global=true
```

## How The Subagent Works

Once installed, just ask Claude Code about performance:
- "This function is slow"
- "Profile my API endpoints"
- "Find performance bottlenecks"
- "Optimize this algorithm"

The performance subagent will:
1. **Profile** your code to find hot spots
2. **Apply** automatic optimizations
3. **Generate** AI-powered improvements for complex issues
4. **Verify** all changes improve performance
5. **Report** detailed results

## Example Output

```
Performance Optimizer:
[1/4] Profiling script...
  - Hot spot: process_records() - 78% CPU time
  
[2/4] Applying optimizations...
  ✓ Replaced nested loops with dict lookup (O(n²) → O(n))
  ✓ Added caching for repeated calculations
  
[3/4] Verifying improvements...
  - Runtime: 45s → 12s (73% faster)
  - Memory: 2.3GB → 890MB (61% less)
  
[4/4] Results saved to perf_report.md
```

## Supported Languages

- **Python**: py-spy, cProfile, memory_profiler
- **JavaScript/Node**: clinic, autocannon, ESLint perf rules
- **Go**: pprof, benchstat, go test -bench
- **Rust**: cargo flamegraph, criterion
- **C/C++**: perf, valgrind, clang-tidy

## Requirements

- Claude Code (claude.ai/code)
- Package manager (npm, pip, cargo, go)
- Admin access for some profilers (optional)

## Learn More

See the full [command documentation](command.md) for detailed usage, configuration options, and troubleshooting.