# 🎯 Toonify MCP

**[English](README.md) | [繁體中文](README.zh-TW.md)**

MCP server + Claude Code Plugin providing automatic token optimization for structured data.
Reduces Claude API token usage by **60%+ on average** through transparent TOON format conversion.

## What's New in v0.2.0

✨ **Now a full Claude Code Plugin!**
- ✅ Automatic PostToolUse hook interception
- ✅ Zero manual calls needed
- ✅ Transparent optimization
- ✅ Still works as standalone MCP server

## Features

- **60%+ Token Reduction** for JSON, CSV, YAML data
- **Fully Automatic** - PostToolUse hook intercepts tool results
- **Zero Configuration** - Works out of the box with sensible defaults
- **Dual Mode** - Works as Plugin (auto) or MCP Server (manual)
- **Built-in Metrics** - Track token savings locally
- **Silent Fallback** - Never breaks your workflow

## Installation

### Option A: Claude Code Plugin (Recommended) ⭐

**Automatic token optimization with zero manual calls:**

```bash
# 1. Install globally
npm install -g toonify-mcp

# 2. Add as plugin (automatic mode)
claude plugin add toonify-mcp

# 3. Verify installation
claude plugin list
# Should show: toonify-mcp ✓
```

**That's it!** The PostToolUse hook will now automatically intercept and optimize structured data from Read, Grep, and other file tools.

### Option B: MCP Server (Manual mode)

**For explicit control or non-Claude Code MCP clients:**

```bash
# 1. Install globally
npm install -g toonify-mcp

# 2. Register as MCP server
claude mcp add toonify -- toonify-mcp

# 3. Verify
claude mcp list
# Should show: toonify: toonify-mcp - ✓ Connected
```

Then call tools explicitly:
```bash
claude mcp call toonify optimize_content '{"content": "..."}'
claude mcp call toonify get_stats '{}'
```

## How It Works

### Plugin Mode (Automatic)

```
User: Read large JSON file
  ↓
Claude Code calls Read tool
  ↓
PostToolUse hook intercepts result
  ↓
Hook detects JSON, converts to TOON
  ↓
Optimized content sent to Claude API
  ↓
60% token reduction achieved ✨
```

### MCP Server Mode (Manual)

```
User: explicitly calls mcp__toonify__optimize_content
  ↓
Content converted to TOON format
  ↓
Returns optimized result
```

## Configuration

Create `~/.claude/toonify-config.json` (optional):

```json
{
  "enabled": true,
  "minTokensThreshold": 50,
  "minSavingsThreshold": 30,
  "skipToolPatterns": ["Bash", "Write", "Edit"]
}
```

### Options

- **enabled**: Enable/disable automatic optimization (default: `true`)
- **minTokensThreshold**: Minimum tokens before optimization (default: `50`)
- **minSavingsThreshold**: Minimum savings percentage required (default: `30%`)
- **skipToolPatterns**: Tools to never optimize (default: `["Bash", "Write", "Edit"]`)

### Environment Variables

```bash
export TOONIFY_ENABLED=true
export TOONIFY_MIN_TOKENS=50
export TOONIFY_MIN_SAVINGS=30
export TOONIFY_SKIP_TOOLS="Bash,Write"
export TOONIFY_SHOW_STATS=true  # Show optimization stats in output
```

## Examples

### Before Optimization (142 tokens)

```json
{
  "products": [
    {"id": 101, "name": "Laptop Pro", "price": 1299},
    {"id": 102, "name": "Magic Mouse", "price": 79}
  ]
}
```

### After Optimization (57 tokens, -60%)

```
[TOON-JSON]
products[2]{id,name,price}:
  101,Laptop Pro,1299
  102,Magic Mouse,79
```

**Automatically applied in Plugin mode - no manual calls needed!**

## Usage Tips

### When Does Auto-Optimization Trigger?

The PostToolUse hook automatically optimizes when:
- ✅ Content is valid JSON, CSV, or YAML
- ✅ Content size ≥ `minTokensThreshold` (default: 50 tokens)
- ✅ Estimated savings ≥ `minSavingsThreshold` (default: 30%)
- ✅ Tool is NOT in `skipToolPatterns` (e.g., not Bash/Write/Edit)

### View Optimization Stats

```bash
# In Plugin mode
claude mcp call toonify get_stats '{}'

# Or check Claude Code output for stats (if TOONIFY_SHOW_STATS=true)
```

## Troubleshooting

### Hook Not Triggering

```bash
# 1. Check plugin is installed
claude plugin list | grep toonify

# 2. Check configuration
cat ~/.claude/toonify-config.json

# 3. Enable stats to see optimization attempts
export TOONIFY_SHOW_STATS=true
```

### Optimization Not Applied

- Check `minTokensThreshold` - content might be too small
- Check `minSavingsThreshold` - savings might be < 30%
- Check `skipToolPatterns` - tool might be in skip list
- Verify content is valid JSON/CSV/YAML

### Performance Issues

- Reduce `minTokensThreshold` to optimize more aggressively
- Increase `minSavingsThreshold` to skip marginal optimizations
- Add more tools to `skipToolPatterns` if needed

## Comparison: Plugin vs MCP Server

| Feature | Plugin Mode | MCP Server Mode |
|---------|------------|-----------------|
| **Activation** | Automatic (PostToolUse) | Manual (call tool) |
| **Compatibility** | Claude Code only | Any MCP client |
| **Configuration** | Plugin config file | MCP tools |
| **Performance** | Zero overhead | Call overhead |
| **Use Case** | Daily workflow | Explicit control |

**Recommendation**: Use Plugin mode for automatic optimization. Use MCP Server mode for explicit control or other MCP clients.

## Uninstall

### Plugin Mode
```bash
claude plugin remove toonify-mcp
rm ~/.claude/toonify-config.json
```

### MCP Server Mode
```bash
claude mcp remove toonify
```

### Package
```bash
npm uninstall -g toonify-mcp
```

## Links

- **GitHub**: https://github.com/kevintseng/toonify-mcp
- **Issues**: https://github.com/kevintseng/toonify-mcp/issues
- **NPM**: https://www.npmjs.com/package/toonify-mcp
- **MCP Docs**: https://code.claude.com/docs/mcp
- **TOON Format**: https://github.com/toon-format/toon

## Contributing

Contributions welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

MIT License - see [LICENSE](LICENSE)

## Changelog

### v0.2.0 (2025-12-25)
- ✨ Added Claude Code Plugin support with PostToolUse hook
- ✨ Automatic token optimization (no manual calls needed)
- ✨ Plugin configuration system
- ✨ Dual mode: Plugin (auto) + MCP Server (manual)
- 📝 Comprehensive documentation update

### v0.1.1 (2024-12-24)
- 🐛 Bug fixes and improvements
- 📝 Documentation updates

### v0.1.0 (2024-12-24)
- 🎉 Initial release
- ✨ MCP Server implementation
- ✨ TOON format optimization
- ✨ Built-in metrics tracking
