# NPM Publishing Checklist

## Prerequisites

- [x] GitHub repository created and pushed
- [x] All tests passing (19/19 ✅)
- [x] TypeScript compiles successfully
- [x] README documentation complete (English + 繁體中文)
- [x] .npmignore configured
- [x] package.json properly configured

## Before Publishing

### 1. Update Package Version

```bash
npm version patch  # for bug fixes
# OR
npm version minor  # for new features
# OR
npm version major  # for breaking changes
```

### 2. NPM Authentication

```bash
# Login to npm (if not already logged in)
npm login

# Verify you're logged in
npm whoami
```

### 3. Final Checks

```bash
# Run all tests
npm test

# Build production version
npm run build

# Check what will be published
npm pack --dry-run

# Verify package contents
tar -tzf *.tgz
```

## Publishing

### Option A: Public Package (Recommended)

```bash
npm publish --access public
```

### Option B: Scoped Package

If using `@ktseng/` scope:
```bash
npm publish --access public
```

## Post-Publishing

### 1. Verify Publication

```bash
# Check npm registry
npm info @ktseng/claude-code-toonify

# Test installation
npm install -g @ktseng/claude-code-toonify
claude-code-toonify --version
```

### 2. Tag Release on GitHub

```bash
git tag v0.1.0
git push origin v0.1.0
```

### 3. Update Documentation

- [ ] Add npm badge to README
- [ ] Update installation instructions with actual package name
- [ ] Create GitHub release with changelog

## Package Verification

Test the published package works correctly:

```bash
# Install globally
npm install -g @ktseng/claude-code-toonify

# Test MCP server
echo '{"jsonrpc":"2.0","id":1,"method":"tools/list"}' | claude-code-toonify

# Add to Claude Code settings
# Check: ~/.claude/settings.json
```

## Troubleshooting

### "You do not have permission to publish"

- Verify you're logged into correct npm account
- Check package name isn't already taken
- Ensure you have publish rights to @ktseng scope

### "Package name too similar to existing package"

- Choose a more unique name
- Consider adding descriptive suffix

### Build Errors

```bash
# Clean and rebuild
rm -rf dist node_modules
npm install
npm run build
```

## Rollback

If something goes wrong after publishing:

```bash
# Deprecate a version
npm deprecate @ktseng/claude-code-toonify@0.1.0 "Use version 0.1.1 instead"

# Unpublish (only within 72 hours)
npm unpublish @ktseng/claude-code-toonify@0.1.0
```

## Current Status

✅ **Ready to publish!**

All prerequisites met. Run `npm publish --access public` when ready.
