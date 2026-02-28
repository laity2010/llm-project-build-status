# TASK - llm-web-arena

## Step
2

## Phase
1 – Driver Attach + Diagnostics

## Goal
1. **Driver 连接**  
   使用 `selenium 4.x` 的 `webdriver.Chrome` 通过 `Options(debuggerAddress="127.0.0.1:PORT")` 分别连接 chatgpt、gemini、grok 三个已打开的 Chrome 实例；禁止创建新浏览器。

2. **诊断逻辑**  
   创建 `diagnostics.py`，实现 `capture_failure(ai: str, step: str, driver: webdriver, extra_msg: str)`：  
   - 失败时在本地仓库 `./debug_screenshots/` 保存 `.png` 截图和 `.txt` 文件，内容包含 URL、标题、DOM 片段（≤3000 字符）和额外信息。  
   - 对 token/cookie/Authorization 等敏感信息进行脱敏。

3. **CLI 功能扩展**  
   在 `cli.py` 中实现 `--mode attach_test`：  
   - 读取 `config.example.json`，按配置进行连接测试。  
   - 成功输出 `[ai][ATTACH][OK] url=... title=...`。  
   - 失败调用 `capture_failure` 保存诊断，并以非零状态码退出。

## Additional Instructions
在开始执行上述任务前，请先将本文件（整个 TASK 内容）写入 `llm-project-build-status/inbox/llm-web-arena/TASK.md`，覆盖旧内容，并执行 commit + push。这是为了确保任务指令在公共仓库中留存记录，便于后续审阅。

## Work Repo (private/local)
`/Users/daisor/Documents/Github/Ds2026/llm-web-arena`

## Status Repo (public)
`llm-project-build-status`（子目录 `llm-web-arena`）

## Commands to Run
```bash
python3 -m web_llm_arena.cli --mode attach_test --ai chatgpt --config config.example.json
python3 -m web_llm_arena.cli --mode attach_test --ai gemini  --config config.example.json
python3 -m web_llm_arena.cli --mode attach_test --ai grok    --config config.example.json
```
