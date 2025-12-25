# 🎯 Toonify MCP

**[English](README.md) | [繁體中文](README.zh-TW.md)**

MCP 伺服器 + Claude Code 插件，提供結構化數據的自動 token 優化。
透過透明的 TOON 格式轉換，**平均減少 60%+ 的 Claude API token 使用**。

## v0.2.0 新功能

✨ **現在是完整的 Claude Code 插件！**
- ✅ 自動 PostToolUse hook 攔截
- ✅ 無需手動調用
- ✅ 透明優化
- ✅ 仍可作為獨立 MCP 伺服器使用

## 功能特色

- **60%+ Token 減少** - 針對 JSON、CSV、YAML 數據
- **完全自動** - PostToolUse hook 自動攔截工具結果
- **零配置** - 開箱即用，預設值合理
- **雙模式** - 插件模式（自動）+ MCP 伺服器模式（手動）
- **內建指標** - 本地追蹤 token 節省
- **靜默降級** - 永不中斷工作流程

## 安裝方式

### 選項 A：Claude Code 插件（推薦）⭐

**自動 token 優化，無需手動調用：**

```bash
# 1. 全域安裝
npm install -g toonify-mcp

# 2. 添加為插件（自動模式）
claude plugin add toonify-mcp

# 3. 驗證安裝
claude plugin list
# 應顯示：toonify-mcp ✓
```

**就這樣！** PostToolUse hook 現在會自動攔截並優化來自 Read、Grep 和其他文件工具的結構化數據。

### 選項 B：MCP 伺服器（手動模式）

**用於明確控制或非 Claude Code 的 MCP 客戶端：**

```bash
# 1. 全域安裝
npm install -g toonify-mcp

# 2. 註冊為 MCP 伺服器
claude mcp add toonify -- toonify-mcp

# 3. 驗證
claude mcp list
# 應顯示：toonify: toonify-mcp - ✓ Connected
```

然後明確調用工具：
```bash
claude mcp call toonify optimize_content '{"content": "..."}'
claude mcp call toonify get_stats '{}'
```

## 運作原理

### 插件模式（自動）

```
用戶：讀取大型 JSON 文件
  ↓
Claude Code 調用 Read 工具
  ↓
PostToolUse hook 攔截結果
  ↓
Hook 偵測 JSON，轉換為 TOON
  ↓
優化後的內容發送到 Claude API
  ↓
實現 60% token 減少 ✨
```

### MCP 伺服器模式（手動）

```
用戶：明確調用 mcp__toonify__optimize_content
  ↓
內容轉換為 TOON 格式
  ↓
返回優化後的結果
```

## 配置

創建 `~/.claude/toonify-config.json`（可選）：

```json
{
  "enabled": true,
  "minTokensThreshold": 50,
  "minSavingsThreshold": 30,
  "skipToolPatterns": ["Bash", "Write", "Edit"]
}
```

### 選項說明

- **enabled**: 啟用/禁用自動優化（預設：`true`）
- **minTokensThreshold**: 優化前的最小 token 數（預設：`50`）
- **minSavingsThreshold**: 所需的最小節省百分比（預設：`30%`）
- **skipToolPatterns**: 永不優化的工具（預設：`["Bash", "Write", "Edit"]`）

### 環境變數

```bash
export TOONIFY_ENABLED=true
export TOONIFY_MIN_TOKENS=50
export TOONIFY_MIN_SAVINGS=30
export TOONIFY_SKIP_TOOLS="Bash,Write"
export TOONIFY_SHOW_STATS=true  # 在輸出中顯示優化統計
```

## 範例

### 優化前（142 tokens）

```json
{
  "products": [
    {"id": 101, "name": "Laptop Pro", "price": 1299},
    {"id": 102, "name": "Magic Mouse", "price": 79}
  ]
}
```

### 優化後（57 tokens，-60%）

```
[TOON-JSON]
products[2]{id,name,price}:
  101,Laptop Pro,1299
  102,Magic Mouse,79
```

**在插件模式下自動應用 - 無需手動調用！**

## 使用技巧

### 何時觸發自動優化？

PostToolUse hook 在以下情況自動優化：
- ✅ 內容是有效的 JSON、CSV 或 YAML
- ✅ 內容大小 ≥ `minTokensThreshold`（預設：50 tokens）
- ✅ 預估節省 ≥ `minSavingsThreshold`（預設：30%）
- ✅ 工具不在 `skipToolPatterns` 中（例如，不是 Bash/Write/Edit）

### 查看優化統計

```bash
# 在插件模式下
claude mcp call toonify get_stats '{}'

# 或檢查 Claude Code 輸出的統計（如果 TOONIFY_SHOW_STATS=true）
```

## 故障排除

### Hook 未觸發

```bash
# 1. 檢查插件是否已安裝
claude plugin list | grep toonify

# 2. 檢查配置
cat ~/.claude/toonify-config.json

# 3. 啟用統計以查看優化嘗試
export TOONIFY_SHOW_STATS=true
```

### 未應用優化

- 檢查 `minTokensThreshold` - 內容可能太小
- 檢查 `minSavingsThreshold` - 節省可能 < 30%
- 檢查 `skipToolPatterns` - 工具可能在跳過列表中
- 驗證內容是有效的 JSON/CSV/YAML

### 性能問題

- 降低 `minTokensThreshold` 以更積極地優化
- 提高 `minSavingsThreshold` 以跳過邊際優化
- 如需要，將更多工具添加到 `skipToolPatterns`

## 比較：插件 vs MCP 伺服器

| 功能 | 插件模式 | MCP 伺服器模式 |
|------|---------|---------------|
| **啟動方式** | 自動（PostToolUse） | 手動（調用工具） |
| **兼容性** | 僅 Claude Code | 任何 MCP 客戶端 |
| **配置** | 插件配置文件 | MCP 工具 |
| **性能** | 零開銷 | 調用開銷 |
| **使用場景** | 日常工作流程 | 明確控制 |

**建議**：使用插件模式進行自動優化。使用 MCP 伺服器模式進行明確控制或其他 MCP 客戶端。

## 卸載

### 插件模式
```bash
claude plugin remove toonify-mcp
rm ~/.claude/toonify-config.json
```

### MCP 伺服器模式
```bash
claude mcp remove toonify
```

### 套件
```bash
npm uninstall -g toonify-mcp
```

## 連結

- **GitHub**: https://github.com/kevintseng/toonify-mcp
- **Issues**: https://github.com/kevintseng/toonify-mcp/issues
- **NPM**: https://www.npmjs.com/package/toonify-mcp
- **MCP 文檔**: https://code.claude.com/docs/mcp
- **TOON 格式**: https://github.com/toon-format/toon

## 貢獻

歡迎貢獻！請參閱 [CONTRIBUTING.md](CONTRIBUTING.md) 獲取指南。

## 授權

MIT License - 請參閱 [LICENSE](LICENSE)

## 更新日誌

### v0.2.0 (2025-12-25)
- ✨ 新增 Claude Code 插件支援與 PostToolUse hook
- ✨ 自動 token 優化（無需手動調用）
- ✨ 插件配置系統
- ✨ 雙模式：插件（自動）+ MCP 伺服器（手動）
- 📝 全面文檔更新

### v0.1.1 (2024-12-24)
- 🐛 錯誤修復和改進
- 📝 文檔更新

### v0.1.0 (2024-12-24)
- 🎉 初始發布
- ✨ MCP 伺服器實作
- ✨ TOON 格式優化
- ✨ 內建指標追蹤
