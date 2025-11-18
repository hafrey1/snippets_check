# Cloudflare Snippets Status Checker

该仓库用于自动检查你的 Cloudflare 账户下所有域名的 **Snippets 可用情况**，并生成一个 Markdown 表格 `snippets_status.md`，每天自动更新。

---

## 功能

- ✅ 自动获取账户下所有域名（支持分页）  
- ✅ 并发请求每个域名的 Snippets 权限，提高执行效率  
- ✅ 检查是否可以使用 Snippets  
- ✅ 获取最大可用 Snippets 数量（API 未返回时显示 `Unlimited`）  
- ✅ 自动生成 Markdown 表格  
- ✅ 自动提交更新到 GitHub 仓库  

示例表格：(根据Max Snippets的数量判断是否可用)

| Domain       | Zone ID | Snippets Available | Max Snippets |
|--------------|---------|-----------------|--------------|
| example.com  | abc123  | ❌              | N/A          |
| mysite.net   | def456  | ✅              | 5            |

---

## 使用方法

### 1️⃣ 准备 GitHub Secrets
- Settings --> secrets and variables --> Actions 
- `CF_API_EMAIL`：你的 Cloudflare 邮箱  
- `CF_API_KEY`：你的 Cloudflare 全局 API Key

⭐打开workflow 读写权限步骤：

- 进入仓库 Settings

- 左侧选择 Actions

- 在 General 标签页找到：

- 🔧 Workflow permissions

- Read repository contents permission（默认）(运行 workflow 时只能读内容)

- Read and write permissions  (Workflow 运行时可读写仓库，例如自动提交代码)

- 选择可读写权限后点 Save。

### 2️⃣ 配置 Workflow

文件路径：`.github/workflows/snippets_parallel_max.yml`  

已配置功能：  
- 每天中午 12 点自动运行  
- 支持手动触发（workflow_dispatch）  
- 自动生成并提交 `snippets_status.md`  

### 3️⃣ 运行结果

- 运行后会在仓库根目录生成 `snippets_status.md`  
- 包含所有域名的 Snippets 可用状态和最大数量  

---

## 获取当前计划的 Max Snippets

可以用 Cloudflare API 查询 `rulesets.snippets_rule_max`：

```bash
curl -s -X GET "https://api.cloudflare.com/client/v4/zones/<ZONE_ID>/rulesets/capabilities" \
  -H "X-Auth-Email: <EMAIL>" \
  -H "X-Auth-Key: <API_KEY>" \
  -H "Content-Type: application/json" | jq
```


返回示例：

```bash
{
  "id": "rulesets.snippets_rule_max",
  "value": 100
}
```

value 就是该 Zone 可用的最大 Snippets 数量。

---

## 注意事项

- Free Plan 域名可能无法返回具体 Max Snippets 数量，默认显示 `Unlimited`
- 并发处理依赖 `jq` 和 `curl`，确保 Actions 运行环境有这些工具
- 如果仓库是 fork 或 private，确保 Actions 权限设置为 **Read and Write**

---

## 自定义

- 可以修改 workflow 的 `PER_PAGE` 值来调整每页请求数量（默认 50）
- 可以修改 cron 表达式，调整自动运行时间

---

## License

MIT License
