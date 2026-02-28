# TASK - llm-web-arena

## Step
3

## Phase
2 – Element Resolver & Locator Test (包含 uv 环境管理)

## 重要新增要求：使用 uv 管理环境与依赖
- 在执行任何依赖安装或虚拟环境创建时，必须使用 [uv](https://docs.astral.sh/uv/) 工具。
- 首次运行时，应在项目根目录执行 `uv venv` 创建 `.venv` 环境；之后所有包安装均使用 `uv pip install …`。
- uv 会自动创建和管理虚拟环境并高效安装依赖，减少版本冲突和速度问题 [oai_citation:0‡docs.astral.sh](https://docs.astral.sh/uv/pip/environments/#:~:text=Python%20installations%20that%20come%20with,a%20virtual%20environment%20by%20default)。
- 本项目所需的依赖应写入 `pyproject.toml` 或 `requirements.txt`，并通过 `uv pip install -r requirements.txt` 安装。以后如有新增依赖，也必须通过 uv 安装。

## Goal
为 ChatGPT、Gemini、Grok 三个站点实现可配置的多定位器解析器，并通过 `locator_test` 模式验证每个站点的输入框、发送按钮和回复容器都能找到，且整个项目依赖管理由 uv 负责。

## Work Repo (private/local)
`/Users/daisor/Documents/Github/Ds2026/llm-web-arena`

## Status Repo (public)
`llm-project-build-status`（目录：`llm-web-arena`）

## Required Code Changes (in work repo)

1. **使用 uv 初始化和安装依赖**  
   - 在项目根目录运行 `uv venv` 创建虚拟环境（若不存在）。  
   - 使用 `uv pip install` 安装 selenium、pydantic、tenacity、loguru、rich 等依赖，并确保 `pyproject.toml` 或 `requirements.txt` 中记录了这些依赖。
   - 后续开发若需新增包，一律通过 `uv pip install` 安装。

2. **创建 `resolver.py` 模块**  
   - 实现 `find_first(driver, locator_list, timeout_s=15) -> (element, hit_index)`：  
     * `locator_list` 是包含多个定位方式与表达式的字典列表，例如 `{"by": "css", "value": "textarea[data-id='root']"}`。  
     * 逐个尝试定位，每次使用 `WebDriverWait` 与期望条件，成功返回元素及命中索引；全部失败则调用诊断逻辑并抛出异常。

3. **扩展 CLI**  
   - 在 `cli.py` 中新增 `--mode locator_test`：  
     * 读取配置文件中每个站点的 `locators` 列表。  
     * 依次解析并查找输入框、发送按钮、回复容器。  
     * 成功则打印 `[ai][LOCATOR][OK] input hit=1` 等信息；失败则保存诊断信息并返回失败状态码。

4. **配置文件更新**  
   - 确保 `config.example.json` 中包含每个站点的 `locators` 列表（input/send/reply_container），以便解析器使用。  
   - 确保端口、URL、超时时间等配置正确无误。

## Commands to Run
先使用 uv 安装依赖并创建虚拟环境：

```bash
cd /Users/daisor/Documents/Github/Ds2026/llm-web-arena
uv venv  # 如已存在虚拟环境可跳过
uv pip install -r requirements.txt  # 安装依赖
```
