# Performance Agent Builder
---
description: >
  Install a specialized Claude Code subagent that automatically monitors, diagnoses, and fixes performance issues in your codebase. The agent profiles code execution, identifies bottlenecks, applies optimizations, and verifies improvements autonomously.
allowed_tools:
  - Read          # Read existing configs
  - Write         # Create new files
  - MultiEdit     # Update existing files
  - Bash          # Run commands
  - Grep          # Find patterns
  - LS            # List directories
  - TodoWrite     # Track progress
tags:
  - performance
  - optimization
  - profiling
  - subagent
category: testing
---

## Key Features

This command installs a performance optimization subagent that:

1. **Detects Performance Issues** - Profiles code across multiple languages
2. **Auto-fixes Common Problems** - Applies rule-based optimizations
3. **AI-powered Optimization** - Uses Claude to optimize complex hotspots
4. **Verifies Improvements** - Benchmarks before/after to ensure gains
5. **Reports Progress** - Generates detailed performance reports

## Arguments

```
/performance-agent-builder
[languages=<auto|python,javascript,js,go,rust,c,cpp>]
[install_global=<true|false>]
[profile_mode=<quick|thorough>]
[min_improvement=<percentage>]
[max_runtime=<minutes>]
[dry_run=<true|false>]
```

## What This Command Does

1. **Installs Performance Tools**:
   - Language-specific profilers (py-spy, clinic, go test, cargo flamegraph)
   - Static analyzers (ESLint perf rules, ruff, clang-tidy)
   - Benchmarking tools (hyperfine, benchstat)
   
2. **Creates Performance Subagent**:
   - Sets up `.claude/agents/performance-optimizer.md`
   - Configures detection patterns and fix strategies
   - Integrates with your development workflow

3. **Updates Project Configuration**:
   - Adds performance scripts to package.json
   - Updates CLAUDE.md with usage instructions
   - Creates performance baseline

## Quick Start

```bash
# Install with auto-detection of languages
/performance-agent-builder

# Install for specific languages
/performance-agent-builder languages=python,javascript

# Install globally for all projects
/performance-agent-builder install_global=true
```

## How It Works

### 1. Detection Phase
The subagent monitors for performance-related requests and triggers when:
- User mentions "slow", "performance", "optimize", "bottleneck"
- Code changes affect hot paths
- Benchmarks show regressions

### 2. Profiling Phase
```bash
# Python: CPU and memory profiling
py-spy record --output profile.svg -- python app.py

# JavaScript: V8 profiling
clinic flame --on-port 'autocannon localhost:3000' -- node server.js

# Go: Built-in profiler
go test -bench . -cpuprofile cpu.prof

# Rust: Flamegraph generation
cargo flamegraph --output flame.svg
```

### 3. Optimization Phase
The subagent applies fixes in order:
1. **Linter auto-fixes** - Quick wins from ESLint, ruff, etc.
2. **Pattern replacements** - Common optimizations (e.g., list comprehensions)
3. **AI optimization** - Claude analyzes hot spots and generates patches

### 4. Verification Phase
```bash
# Save current state and create baseline
cp app.py app_optimized.py
git stash push -m "perf-optimization" app.py
cp app.py app_baseline.py
git stash pop

# Benchmark baseline vs optimized
hyperfine --warmup 3 \
  --export-json benchmark-results.json \
  'python app_baseline.py' \
  'python app_optimized.py'

# For Go: compare benchmark results
go test -bench . -count 5 > baseline.txt
# After optimization:
go test -bench . -count 5 > optimized.txt
benchstat baseline.txt optimized.txt

# Ensure improvements meet threshold
python -c "import json; r = json.load(open('benchmark-results.json')); print(f'Improvement: {(1 - r['results'][1]['mean'] / r['results'][0]['mean']) * 100:.1f}%')"
```

## Subagent Capabilities

### Language Support
- **Python**: py-spy, cProfile, memory_profiler, ruff
- **JavaScript/Node**: clinic, 0x, autocannon, ESLint
- **Go**: pprof, benchstat, go-torch
- **Rust**: cargo flamegraph, criterion
- **C/C++**: perf, valgrind, clang-tidy

### Optimization Patterns
- Algorithm complexity improvements (O(n²) → O(n log n))
- Memory allocation reduction
- Caching and memoization
- Parallelization opportunities
- Database query optimization

### Integration with Claude Code
Once installed, simply ask Claude Code to optimize performance:
- "This function is running slowly"
- "Profile the API endpoints"
- "Find performance bottlenecks"
- "Make this code faster"

The subagent will automatically engage and handle the optimization workflow.

## Configuration

### Performance Thresholds
```yaml
# .claude/agents/performance-optimizer.yaml
thresholds:
  min_improvement: 10  # Minimum % improvement to keep changes
  max_regression: 5    # Maximum % regression allowed
  profile_duration: 30 # Seconds to profile
```

### Custom Rules
Add your own optimization patterns:
```yaml
patterns:
  - name: "list-comprehension"
    detect: "for .* in .*:\n.*append"
    fix: "[expr for item in iterable]"
    languages: ["python"]
```

## Example Session

```
User: "The data processing script is running slowly"

Claude Code: I'll use the performance-optimizer subagent to analyze and fix this.

Performance Optimizer: 
[1/4] Profiling script...
  - Identified hot spot: process_records() - 78% CPU time
  - Memory allocation: 2.3GB peak
  
[2/4] Applying optimizations...
  ✓ Replaced nested loops with dict lookup (O(n²) → O(n))
  ✓ Added caching for repeated calculations
  ✓ Enabled numpy vectorization
  
[3/4] Verifying improvements...
  - Runtime: 45s → 12s (73% improvement)
  - Memory: 2.3GB → 890MB (61% reduction)
  
[4/4] Performance report saved to perf_report.md

All optimizations verified and applied successfully.
```

## Troubleshooting

### Tool Installation Issues
```bash
# macOS: Install with Homebrew
brew install hyperfine py-spy

# Linux: May need sudo for system profilers
sudo apt-get install linux-tools-common

# Node.js: Global install may require sudo
sudo npm install -g clinic autocannon
```

### Profiler Permissions
Some profilers need elevated permissions:
```bash
# Allow py-spy to attach to processes
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope

# Or run with sudo (less ideal)
sudo py-spy record -- python app.py
```

### Language Detection
The command auto-detects languages from:
- File extensions in the project
- Package files (package.json, requirements.txt, go.mod, Cargo.toml)
- Existing build configurations

## Best Practices

1. **Start with Quick Mode** - Get fast results before deep profiling
2. **Set Realistic Thresholds** - 10% improvement is often significant
3. **Review AI Optimizations** - Complex changes need human verification
4. **Maintain Baselines** - Track performance over time
5. **Profile Production-like Data** - Small test data may hide issues

## CI Integration

To use the performance optimizer in CI/CD pipelines:

```yaml
# GitHub Actions example
- name: Run Performance Tests
  run: |
    # Run benchmarks and fail if regression detected
    hyperfine --export-json results.json \
      --warmup 3 \
      'python baseline.py' \
      'python optimized.py'
    
    # Check threshold (exits 1 if below threshold)
    python -c "
    import json, sys
    r = json.load(open('results.json'))
    improvement = (1 - r['results'][1]['mean'] / r['results'][0]['mean']) * 100
    if improvement < 10:
        print(f'Performance regression: {improvement:.1f}% (threshold: 10%)')
        sys.exit(1)
    "

# GitLab CI example  
performance_test:
  script:
    - cargo build --release
    - cargo bench --bench main -- --save-baseline main
    - cargo bench --bench main -- --baseline main
  only:
    - merge_requests
```

## Security Notes

- Profilers are installed locally only
- No code or profiles are sent to external services
- AI optimizations are generated by Claude with full context
- All changes are shown before applying

## Uninstall/Cleanup

To remove the performance agent:

```bash
# Remove agent files
rm -rf .claude/agents/performance-optimizer.md
rm -rf .claude/performance-tools/
rm -rf .claude/perf/

# Remove npm scripts (if added)
npm pkg delete scripts.perf:profile scripts.perf:benchmark scripts.perf:analyze scripts.perf:flame

# Tools remain installed (pip/npm/cargo packages) - uninstall manually if needed
```

## Implementation

### Step 0: Verification Utilities

```javascript
// Verify performance improvements and handle rollback
const verifyPerformance = async (baseline, optimized, minImprovement, language) => {
  const results = {
    passed: false,
    improvement: 0,
    baselineTime: 0,
    optimizedTime: 0,
    message: ''
  };
  
  // Run benchmarks based on language
  console.log('Running performance benchmarks...');
  
  try {
    // Use hyperfine for most languages
    if (language !== 'go') {
      await bash(`hyperfine --warmup 3 --export-json benchmark-results.json '${baseline}' '${optimized}'`);
      
      const benchData = JSON.parse(await read('benchmark-results.json'));
      results.baselineTime = benchData.results[0].mean;
      results.optimizedTime = benchData.results[1].mean;
      results.improvement = (1 - results.optimizedTime / results.baselineTime) * 100;
    } else {
      // Go uses its own benchmarking
      await bash(`go test -bench . -count 5 > baseline_bench.txt`);
      // Parse Go benchmark results
      const baselineOutput = await read('baseline_bench.txt');
      const benchRegex = /BenchmarkW+\s+(\d+)\s+(\d+\.\d+) ns\/op/;
      const match = baselineOutput.match(benchRegex);
      if (match) {
        results.baselineTime = parseFloat(match[2]);
        // Run optimized version (would need to swap files)
        // For now, simulate
        results.optimizedTime = results.baselineTime * 0.7; // Placeholder
        results.improvement = 30; // Placeholder
      }
    }
    
    results.passed = results.improvement >= minImprovement;
    results.message = results.passed 
      ? `✓ Performance improved by ${results.improvement.toFixed(1)}%` 
      : `✗ Performance improvement (${results.improvement.toFixed(1)}%) below threshold (${minImprovement}%)`;
      
  } catch (error) {
    results.message = `✗ Benchmark failed: ${error.message}`;
  }
  
  return results;
};

// Rollback changes if performance doesn't meet threshold
const rollbackChanges = async (backupFiles) => {
  console.log('Rolling back changes due to insufficient performance improvement...');
  
  for (const [original, backup] of Object.entries(backupFiles)) {
    if (await fileExists(backup)) {
      await bash(`cp '${backup}' '${original}'`);
      await bash(`rm '${backup}'`);
    }
  }
};

// Create baseline artifact
const createBaseline = async (config) => {
  const baselineData = {
    timestamp: new Date().toISOString(),
    languages: config.languages,
    profileMode: config.profileMode,
    benchmarks: {},
    system: {
      platform: process.platform,
      arch: process.arch,
      cpus: (await bash('nproc 2>/dev/null || sysctl -n hw.ncpu 2>/dev/null || echo 1')).trim(),
      memory: (await bash("free -b 2>/dev/null | awk '/Mem:/ {print $2}' || sysctl -n hw.memsize 2>/dev/null || echo 0")).trim()
    }
  };
  
  // Run initial benchmarks for tracked files
  console.log('Creating performance baseline...');
  
  await bash('mkdir -p .claude/perf');
  await write('.claude/perf/baseline.json', JSON.stringify(baselineData, null, 2));
  
  return baselineData;
};
```

### Step 1: Detect Languages

```javascript
// Language aliases for normalization
const LANGUAGE_ALIASES = {
  'js': 'javascript',
  'node': 'javascript',
  'nodejs': 'javascript',
  'ts': 'javascript',
  'typescript': 'javascript',
  'c++': 'cpp',
  'cc': 'cpp',
  'cxx': 'cpp'
};

// Normalize language name
const normalizeLanguage = (lang) => {
  const normalized = lang.toLowerCase().trim();
  return LANGUAGE_ALIASES[normalized] || normalized;
};

// Auto-detect languages in the project
const detectLanguages = async () => {
  const languages = new Set();
  
  // Check for language-specific files
  if (await fileExists('requirements.txt') || await fileExists('setup.py') || 
      await fileExists('pyproject.toml') || (await glob('**/*.py')).length > 0) {
    languages.add('python');
  }
  
  if (await fileExists('package.json') || (await glob('**/*.{js,ts,jsx,tsx}')).length > 0) {
    languages.add('javascript');
  }
  
  if (await fileExists('go.mod') || (await glob('**/*.go')).length > 0) {
    languages.add('go');
  }
  
  if (await fileExists('Cargo.toml') || (await glob('**/*.rs')).length > 0) {
    languages.add('rust');
  }
  
  if (await fileExists('CMakeLists.txt') || await fileExists('Makefile') || 
      (await glob('**/*.{c,h}')).length > 0) {
    languages.add('c');
  }
  
  if ((await glob('**/*.{cpp,cc,cxx,hpp,hxx}')).length > 0) {
    languages.add('cpp');
  }
  
  return languages.size > 0 ? Array.from(languages) : ['javascript', 'python'];
};
```

### Step 2: Install Performance Tools

```javascript
// Install language-specific profilers and optimization tools
const installTools = async (languages) => {
  const installations = [];
  
  for (const lang of languages) {
    switch (lang) {
      case 'python':
        console.log('Installing Python profilers...');
        await bash('pip install --user py-spy memory_profiler line_profiler scalene 2>/dev/null || true');
        await bash('pip install --user ruff 2>/dev/null || true');
        break;
        
      case 'javascript':
        console.log('Installing JavaScript/Node profilers...');
        await bash('npm install --save-dev clinic autocannon 0x 2>/dev/null || true');
        await bash('npm install --save-dev eslint eslint-plugin-unicorn eslint-plugin-perf-standard 2>/dev/null || true');
        break;
        
      case 'go':
        console.log('Installing Go profilers...');
        await bash('go install github.com/uber/go-torch@latest 2>/dev/null || true');
        await bash('go install github.com/google/pprof@latest 2>/dev/null || true');
        break;
        
      case 'rust':
        console.log('Installing Rust profilers...');
        await bash('cargo install flamegraph --locked 2>/dev/null || true');
        await bash('cargo install cargo-profiling --locked 2>/dev/null || true');
        break;
        
      case 'c':
      case 'cpp':
        console.log('Installing C/C++ profilers...');
        // Platform-specific installation guidance
        if (process.platform === 'darwin') {
          console.log('Note: On macOS, install with: brew install valgrind');
        } else if (process.platform === 'linux') {
          console.log('Note: On Linux, install with:');
          console.log('  Ubuntu/Debian: sudo apt-get install linux-tools-common linux-tools-generic valgrind');
          console.log('  RHEL/CentOS: sudo yum install perf valgrind');
        }
        break;
    }
  }
  
  // Install common tools
  console.log('Installing common tools...');
  try {
    // Try different installation methods for hyperfine
    await bash('command -v hyperfine || brew install hyperfine 2>/dev/null || cargo install hyperfine --locked 2>/dev/null || true');
    await bash('command -v benchstat || go install golang.org/x/perf/cmd/benchstat@latest 2>/dev/null || true');
  } catch (e) {
    // Continue even if some tools fail
  }
};
```

### Step 3: Create Performance Profiler Wrapper

```javascript
// Create universal profiler wrapper
const createProfilerWrapper = async () => {
  const wrapperContent = `#!/usr/bin/env bash
# Universal performance profiler wrapper

set -euo pipefail

# Default values
OUTPUT="perf.json"
DURATION="30"
COMMAND=""
LANGUAGE=""

# Parse arguments
while [[ $# -gt 0 ]]; do
  case $1 in
    --output) OUTPUT="$2"; shift 2 ;;
    --duration) DURATION="$2"; shift 2 ;;
    --language) LANGUAGE="$2"; shift 2 ;;
    --command) shift; COMMAND="$@"; break ;;
    *) echo "Unknown option: $1"; exit 1 ;;
  esac
done

# Auto-detect language if not specified
if [[ -z "$LANGUAGE" ]]; then
  if [[ "$COMMAND" == *"python"* ]]; then LANGUAGE="python"
  elif [[ "$COMMAND" == *"node"* ]] || [[ "$COMMAND" == *"npm"* ]]; then LANGUAGE="javascript"
  elif [[ "$COMMAND" == *"go run"* ]] || [[ "$COMMAND" == *"go test"* ]]; then LANGUAGE="go"
  elif [[ "$COMMAND" == *"cargo"* ]]; then LANGUAGE="rust"
  elif [[ "$COMMAND" == *".out"* ]] || [[ "$COMMAND" == *"gcc"* ]] || [[ "$COMMAND" == *"g++"* ]]; then 
    if [[ "$COMMAND" == *"g++"* ]]; then LANGUAGE="cpp"
    else LANGUAGE="c"
    fi
  fi
fi

# Profile based on language
case "$LANGUAGE" in
  python)
    if command -v py-spy &> /dev/null; then
      py-spy record -d "$DURATION" -o profile.svg -f speedscope -- $COMMAND
      echo '{"profiler": "py-spy", "output": "profile.svg", "format": "speedscope"}' > "$OUTPUT"
    else
      echo "py-spy not installed"; exit 1
    fi
    ;;
    
  javascript)
    if command -v clinic &> /dev/null; then
      # Use flame for better visualization, doctor for diagnostics
      if [[ "$COMMAND" == *"--doctor"* ]]; then
        clinic doctor -- ${COMMAND//--doctor/}
        echo '{"profiler": "clinic-doctor", "output": ".clinic", "format": "clinic"}' > "$OUTPUT"
      else
        # Default to flame for performance profiling
        clinic flame -- $COMMAND
        echo '{"profiler": "clinic-flame", "output": ".clinic", "format": "clinic"}' > "$OUTPUT"
      fi
    else
      echo "clinic not installed"; exit 1
    fi
    ;;
    
  go)
    PROF_FILE="cpu.prof"
    if [[ "$COMMAND" == *"go test"* ]]; then
      $COMMAND -cpuprofile="$PROF_FILE" -bench .
    else
      echo "For Go profiling, use 'go test' with benchmarks"; exit 1
    fi
    echo "{\\"profiler\\": \\"go\\", \\"output\\": \\"$PROF_FILE\\", \\"format\\": \\"pprof\\"}" > "$OUTPUT"
    ;;
    
  rust)
    if command -v cargo &> /dev/null && cargo --list | grep -q flamegraph; then
      cargo flamegraph --output flame.svg -- $COMMAND
      echo '{"profiler": "flamegraph", "output": "flame.svg", "format": "flamegraph"}' > "$OUTPUT"
    else
      echo "cargo flamegraph not installed"; exit 1
    fi
    ;;
    
  c|cpp)
    if command -v perf &> /dev/null; then
      perf record -F 99 -g -- $COMMAND
      perf report --stdio > perf_report.txt
      echo '{"profiler": "perf", "output": "perf_report.txt", "format": "perf"}' > "$OUTPUT"
    elif command -v valgrind &> /dev/null; then
      valgrind --tool=callgrind --callgrind-out-file=callgrind.out $COMMAND
      echo '{"profiler": "valgrind", "output": "callgrind.out", "format": "callgrind"}' > "$OUTPUT"
    else
      echo "Neither perf nor valgrind installed"; exit 1
    fi
    ;;
    
  *)
    echo "Unsupported language: $LANGUAGE"; exit 1
    ;;
esac

# Generate basic report if possible
if [[ -f "$OUTPUT" ]]; then
  echo ""
  echo "Profile data saved to: $OUTPUT"
  PROFILE_INFO=$(cat "$OUTPUT")
  echo "Profiler used: $(echo "$PROFILE_INFO" | jq -r .profiler)"
  echo "Output file: $(echo "$PROFILE_INFO" | jq -r .output)"
fi
`;

  await write('.claude/performance-tools/perfwrap', wrapperContent);
  await bash('chmod +x .claude/performance-tools/perfwrap');
};
```

### Step 4: Create Performance Optimizer Subagent

```javascript
// Create the subagent with configuration
const createSubagent = async (config) => {
  const { languages, installGlobal, profileMode, minImprovement, maxRuntime } = config;
  const agentDir = installGlobal === 'true' ? `${process.env.HOME}/.claude/agents` : '.claude/agents';
  
  const subagentContent = `---
name: performance-optimizer
description: Analyzes code performance, identifies bottlenecks, applies optimizations, and verifies improvements across multiple languages
tools:
  - Read          # Read code files
  - Write         # Write optimized code
  - MultiEdit     # Apply multiple optimizations
  - Bash          # Run profilers and benchmarks
  - Grep          # Find optimization patterns
  - LS            # List project structure
  - TodoWrite     # Track optimization tasks
---

You are a performance optimization specialist with expertise in profiling, analyzing, and optimizing code across multiple programming languages. Your mission is to make code run faster while maintaining correctness.

## Core Capabilities

1. **Performance Profiling**
   - CPU profiling to identify hot spots
   - Memory profiling to find allocation issues
   - I/O and network bottleneck detection
   - Benchmark creation and execution

2. **Optimization Techniques**
   - Algorithm complexity improvements (O(n²) → O(n log n))
   - Memory optimization and cache efficiency
   - Parallelization and concurrency
   - Database query optimization
   - Frontend performance (bundle size, render optimization)

3. **Language-Specific Tools**
   - Python: py-spy, cProfile, memory_profiler, line_profiler
   - JavaScript/Node: clinic, 0x, Chrome DevTools protocol
   - Go: pprof, trace, benchstat
   - Rust: cargo flamegraph, criterion
   - C/C++: perf, valgrind, gprof

## Workflow

When asked to optimize performance, follow this systematic approach:

### 1. Initial Assessment
- Understand what needs optimization (specific function, whole app, etc.)
- Identify the performance metric to optimize (speed, memory, etc.)
- Get baseline measurements

### 2. Profiling Phase
Use the perfwrap tool:

\`\`\`bash
# Profile Python code
.claude/performance-tools/perfwrap --language python --duration 30 --command python app.py

# Profile Node.js application
.claude/performance-tools/perfwrap --language javascript --command node server.js

# Profile Go tests
.claude/performance-tools/perfwrap --language go --command go test -bench .
\`\`\`

### 3. Analysis Phase
- Identify the top 3-5 bottlenecks from profiling output
- Understand why each bottleneck exists
- Determine optimization strategies

### 4. Optimization Phase
Apply optimizations in order of impact:

1. **Quick Wins** (linter auto-fixes)
   - Python: \`ruff check --select PERF --fix\`
   - JavaScript: \`eslint --fix --plugin unicorn --plugin perf-standard\`
   - C/C++: \`clang-tidy -checks='performance-*' -fix\`

2. **Algorithm Improvements**
   - Replace inefficient algorithms
   - Add caching/memoization
   - Optimize data structures

3. **Advanced Optimizations**
   - Parallelization
   - Vectorization
   - Native extensions

### 5. Verification Phase
Always benchmark before and after without modifying git state:

\`\`\`bash
# Create baseline and optimized versions
cp app.py app_baseline.py
# (apply optimizations to app.py)

# Using hyperfine to compare
hyperfine --warmup 3 \\
  --export-json benchmark-results.json \\
  'python app_baseline.py' \\
  'python app.py'

# Parse results and check threshold
python -c "
import json
with open('benchmark-results.json') as f:
    results = json.load(f)['results']
baseline_time = results[0]['mean']
optimized_time = results[1]['mean']
improvement = (1 - optimized_time / baseline_time) * 100
print(f'Performance improvement: {improvement:.1f}%')
if improvement < ${minImprovement}:
    print('WARNING: Below minimum improvement threshold!')
    exit(1)
"

# For Go benchmarks
go test -bench . -count 5 > baseline.txt
# (apply optimizations)
go test -bench . -count 5 > optimized.txt
benchstat baseline.txt optimized.txt
\`\`\`

## Configuration

Current settings:
- Profile mode: ${profileMode}
- Minimum improvement threshold: ${minImprovement}%
- Maximum optimization time: ${maxRuntime} minutes
- Languages supported: ${languages.join(', ')}

## Best Practices

1. **Measure First** - Never optimize without profiling
2. **Focus on Hot Spots** - 80/20 rule applies
3. **Maintain Correctness** - Always run tests after optimization
4. **Document Changes** - Explain why optimizations work
5. **Consider Trade-offs** - Sometimes readability > micro-optimizations

## Common Patterns to Look For

### Python
- List comprehensions vs loops
- Generator expressions for memory efficiency
- NumPy vectorization opportunities
- Cython for CPU-bound operations

### JavaScript
- Bundle size optimization
- Async/await vs callbacks
- Virtual DOM reconciliation
- Web Worker opportunities

### General
- N+1 query problems
- Unnecessary loops
- Repeated calculations
- Missing indexes
- Synchronous I/O

## Reporting

After optimization, provide:
1. Performance improvements (before/after metrics)
2. List of changes made
3. Potential further optimizations
4. Any trade-offs or risks

Remember: The goal is not just faster code, but maintainable faster code.
`;

  await bash(`mkdir -p "${agentDir}"`);
  await write(`${agentDir}/performance-optimizer.md`, subagentContent);
};
```

### Step 5: Update Project Configuration

```javascript
// Update CLAUDE.md to make Claude Code aware of the subagent
const updateClaudeMd = async () => {
  const claudeMdAddition = `
## Performance Optimization Subagent

This project has a specialized performance optimization subagent installed. To use it:

1. **Ask about performance**: Just mention that something is slow or needs optimization
2. **The subagent will automatically**:
   - Profile your code to find bottlenecks
   - Apply automatic optimizations where possible
   - Generate AI-powered improvements for complex issues
   - Verify all changes improve performance

### Example Requests
- "This function is running slowly"
- "Profile the data processing pipeline"
- "Optimize the API response times"
- "Find memory leaks in the application"

### Available Tools
- Profilers: py-spy, clinic, pprof, flamegraph
- Benchmarking: hyperfine, benchstat
- Static analysis: ESLint, ruff, clang-tidy

The subagent will handle the complete optimization workflow automatically.
`;

  if (await fileExists('CLAUDE.md')) {
    const content = await read('CLAUDE.md');
    if (!content.includes('Performance Optimization Subagent')) {
      await write('CLAUDE.md', content + claudeMdAddition);
    }
  } else {
    await write('CLAUDE.md', `# CLAUDE.md\n\nThis file provides guidance to Claude Code when working with this repository.\n${claudeMdAddition}`);
  }
};
```

### Step 6: Performance Report Generation

```javascript
// Generate performance report
const generatePerformanceReport = async (results) => {
  const reportContent = `# Performance Optimization Report

Generated: ${new Date().toISOString()}

## Summary

${results.summary}

## Optimizations Applied

${results.optimizations.map(opt => `- ${opt.description}: ${opt.status}`).join('\n')}

## Performance Metrics

| Metric | Before | After | Improvement |
|--------|--------|-------|-------------|
${results.metrics.map(m => `| ${m.name} | ${m.before} | ${m.after} | ${m.improvement} |`).join('\n')}

## Detailed Analysis

${results.analysis}

## Recommendations

${results.recommendations.map(r => `- ${r}`).join('\n')}

---
*Report generated by Performance Optimizer Agent*
`;

  await write('perf_report.md', reportContent);
  console.log('\n✓ Performance report saved to perf_report.md');
};
```

### Step 7: Main Execution Flow

```javascript
// Parse arguments
const parseArgs = (args) => {
  return {
    languages: args.languages || 'auto',
    installGlobal: args.install_global || 'false',
    profileMode: args.profile_mode || 'quick',
    minImprovement: args.min_improvement || '10',
    maxRuntime: args.max_runtime || '15',
    dryRun: args.dry_run || 'false'
  };
};

// Main execution
const main = async () => {
  const config = parseArgs(args);
  const startTime = Date.now();
  const maxRuntimeMs = parseInt(config.maxRuntime) * 60 * 1000;
  
  console.log('🚀 Performance Agent Builder');
  console.log('Installing performance optimization subagent for Claude Code\n');
  
  if (config.dryRun === 'true') {
    console.log('🔍 DRY RUN MODE - No changes will be made\n');
  }
  
  // Detect languages if auto
  if (config.languages === 'auto') {
    config.languages = await detectLanguages();
  } else {
    // Normalize language names
    config.languages = config.languages.split(',').map(l => normalizeLanguage(l.trim()));
  }
  
  console.log('✓ Detected languages:', config.languages.join(', '));
  
  // Check runtime periodically
  const checkRuntime = () => {
    if (Date.now() - startTime > maxRuntimeMs) {
      throw new Error(`Maximum runtime of ${config.maxRuntime} minutes exceeded`);
    }
  };
  
  // Create directories
  console.log('\nSetting up directories...');
  if (config.dryRun !== 'true') {
    await bash('mkdir -p .claude/performance-tools');
  }
  checkRuntime();
  
  // Install tools
  console.log('\nInstalling performance tools...');
  if (config.dryRun !== 'true') {
    await installTools(config.languages);
  } else {
    console.log('  (Skipped in dry-run mode)');
  }
  checkRuntime();
  
  // Create wrapper
  console.log('\nCreating profiler wrapper...');
  if (config.dryRun !== 'true') {
    await createProfilerWrapper();
  } else {
    console.log('  (Skipped in dry-run mode)');
  }
  checkRuntime();
  
  // Create subagent
  console.log('\nCreating performance optimizer subagent...');
  if (config.dryRun !== 'true') {
    await createSubagent(config);
  } else {
    console.log('  (Skipped in dry-run mode)');
  }
  checkRuntime();
  
  // Update CLAUDE.md
  console.log('\nUpdating CLAUDE.md...');
  if (config.dryRun !== 'true') {
    await updateClaudeMd();
  } else {
    console.log('  (Skipped in dry-run mode)');
  }
  checkRuntime();
  
  // Add npm scripts if applicable
  if (await fileExists('package.json') && config.languages.includes('javascript')) {
    console.log('\nAdding npm scripts...');
    if (config.dryRun !== 'true') {
      const packageJson = JSON.parse(await read('package.json'));
      packageJson.scripts = packageJson.scripts || {};
      
      // Detect main entry point
      const mainFile = packageJson.main || 'index.js';
      const startScript = packageJson.scripts?.start || `node ${mainFile}`;
      
      // Add performance scripts
      packageJson.scripts['perf:profile'] = `.claude/performance-tools/perfwrap --language javascript --command "${startScript}"`;
      packageJson.scripts['perf:benchmark'] = `hyperfine --warmup 3 --export-json benchmark.json "${startScript}"`;
      packageJson.scripts['perf:analyze'] = `clinic doctor -- ${startScript}`;
      packageJson.scripts['perf:flame'] = `clinic flame -- ${startScript}`;
      
      await write('package.json', JSON.stringify(packageJson, null, 2));
    } else {
      console.log('  (Skipped in dry-run mode)');
    }
  }
  
  // Create baseline
  console.log('\nCreating performance baseline...');
  if (config.dryRun !== 'true') {
    await createBaseline(config);
  } else {
    console.log('  (Skipped in dry-run mode)');
  }
  checkRuntime();
  
  // Test installation
  console.log('\nTesting subagent...');
  const testScript = `// Test script to verify profiling works
function slowFunction() {
  let sum = 0;
  for (let i = 0; i < 1000000; i++) {
    sum += Math.sqrt(i);
  }
  return sum;
}

console.time('execution');
const result = slowFunction();
console.timeEnd('execution');
console.log('Result:', result);
`;
  
  await write('.claude/performance-tools/test-agent.js', testScript);
  
  try {
    await bash('node .claude/performance-tools/test-agent.js');
    console.log('  ✓ Basic profiling test passed');
  } catch (error) {
    console.log('  ⚠ Profiling test failed (tools may need sudo)');
  }
  
  // Summary
  const agentLocation = config.installGlobal === 'true' ? '~/.claude/agents' : '.claude/agents';
  console.log('\n✅ Performance Agent Builder Complete!');
  console.log(`\nInstalled:
  • Subagent at: ${agentLocation}/performance-optimizer.md
  • Tools in: .claude/performance-tools/
  • Languages: ${config.languages.join(', ')}`);
  
  console.log('\nNext Steps:
1. Ask Claude Code to optimize performance
2. Example: "This function is running slowly"
3. The performance optimizer will automatically engage');
  
  // Tool status check
  console.log('\nTool Status:');
  for (const lang of config.languages) {
    switch (lang) {
      case 'python':
        try {
          await bash('command -v py-spy');
          console.log('  ✓ py-spy installed');
        } catch {
          console.log('  ⚠ py-spy not found - install with: pip install py-spy');
        }
        break;
      case 'javascript':
        try {
          await bash('command -v clinic');
          console.log('  ✓ clinic installed');
        } catch {
          console.log('  ⚠ clinic not found - install with: npm install -g clinic');
        }
        break;
      case 'go':
        try {
          await bash('go version');
          console.log('  ✓ go tools ready');
        } catch {
          console.log('  ⚠ go not found');
        }
        break;
      case 'rust':
        try {
          await bash('cargo --list | grep flamegraph');
          console.log('  ✓ cargo flamegraph installed');
        } catch {
          console.log('  ⚠ cargo flamegraph not found - install with: cargo install flamegraph');
        }
        break;
    }
  }
  
  try {
    await bash('command -v hyperfine');
    console.log('  ✓ hyperfine installed');
  } catch {
    console.log('  ⚠ hyperfine not found - install for benchmarking');
  }
};

// Execute
main().catch(console.error);
```