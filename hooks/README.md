# Claude Code Toonify Hook

Automatic TOON format optimization hook for Claude Code CLI.

## What This Does

This hook **automatically intercepts** tool results from Read, Grep, and other file operations, detects structured data (JSON/CSV/YAML), and applies TOON format optimization when token savings > 30%.

**No manual calls required** - optimization happens transparently.

## Installation

### As Part of Plugin (Recommended)

**The hook is automatically registered when you install toonify-mcp as a plugin:**

```bash
# 1. Install package globally
npm install -g toonify-mcp

# 2. Add as Claude Code plugin
claude plugin add toonify-mcp

# The PostToolUse hook is now active!
```

### Standalone Installation (Advanced)

If you need to install the hook without the full plugin:

```bash
# 1. Build the hook
cd hooks/
npm install
npm run build

# 2. Manually symlink or copy to Claude plugins directory
# (Not recommended - use plugin installation instead)
```

## Configuration

Create `~/.claude/toonify-config.json`:

```json
{
  "enabled": true,
  "minTokensThreshold": 50,
  "minSavingsThreshold": 30,
  "skipToolPatterns": ["Bash", "Write", "Edit"]
}
```

### Options

- `enabled`: Enable/disable automatic optimization (default: true)
- `minTokensThreshold`: Minimum tokens before optimization (default: 50)
- `minSavingsThreshold`: Minimum savings percentage required (default: 30%)
- `skipToolPatterns`: Tools to never optimize (default: ["Bash", "Write", "Edit"])

### Environment Variables

- `TOONIFY_SHOW_STATS=true` - Show optimization stats in output

## How It Works

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
60% token reduction achieved
```

## vs MCP Server

| Feature | PostToolUse Hook (Plugin) | MCP Server |
|---------|-------------------------|-----------|
| **Activation** | Automatic (intercept) | Manual (call tool) |
| **Compatibility** | Claude Code only | Any MCP client |
| **Configuration** | Hook config file | MCP tools |
| **Performance** | Zero overhead | Call overhead |
| **Installation** | Plugin installation | MCP registration |

**Recommendation**: Use PostToolUse hook (plugin mode) for automatic optimization in Claude Code. Use MCP server for explicit control or other MCP clients.

## Troubleshooting

### Hook not triggering

```bash
# 1. Check plugin is installed
claude plugin list | grep toonify

# 2. Check hooks.json exists
ls ~/.claude/plugins/.../toonify-mcp/hooks/hooks.json

# 3. Check configuration
cat ~/.claude/toonify-config.json

# 4. Test manually
echo '{"toolName":"Read","toolResult":{"content":[{"type":"text","text":"{\"test\":123}"}]}}' | node post-tool-use.js
```

### Optimization not applied

- Check `minTokensThreshold` - content might be too small
- Check `minSavingsThreshold` - savings might be < 30%
- Check `skipToolPatterns` - tool might be in skip list
- Verify content is valid JSON/CSV/YAML

## Examples

**Before (142 tokens)**:
```json
{
  "products": [
    {"id": 101, "name": "Laptop Pro", "price": 1299},
    {"id": 102, "name": "Magic Mouse", "price": 79}
  ]
}
```

**After (57 tokens, -60%)**:
```
[TOON-JSON]
products[2]{id,name,price}:
  101,Laptop Pro,1299
  102,Magic Mouse,79
```

**Automatically applied** - no manual calls needed!

## Development

### Building the hook

```bash
cd hooks/
npm install
npm run build
```

This compiles TypeScript to JavaScript and makes the hook ready for use.

### Testing locally

```bash
# Test the hook directly
echo '{"toolName":"Read","toolResult":{"content":[{"type":"text","text":"{\"foo\":\"bar\"}"}]}}' | node post-tool-use.js

# Should output optimized JSON in TOON format
```

## Files

- `post-tool-use.ts` - TypeScript source
- `post-tool-use.js` - Compiled JavaScript (executed by Claude Code)
- `hooks.json` - Hook registration config (read by Claude Code Plugin system)
- `package.json` - Dependencies and build scripts

## See Also

- Main README: [../README.md](../README.md)
- Plugin configuration: [../.claude-plugin/plugin.json](../.claude-plugin/plugin.json)
- TOON format spec: https://github.com/toon-format/toon
