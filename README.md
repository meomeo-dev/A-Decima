# A-DECIMA

> 基于 CLI 的 AI 驱动股票研究框架 (AI-driven stock research framework)。集成 `iwencai`（数据源）+ `deep-research`（DAG研究路径管理），结构化执行从数据抓取、分析、质量检查到报告导出的完整投研流程。  
>
> 能力边界: 数据基于问财的基础行情数据、新闻、研报。因缺乏历史数据的获取来源不具备回测能力。  

## 基于 CLI 的选股助手

1、使用方法复制下方YAML模版到 Codex / CC 先让其完成冷启动安装  

```md
{{下方 YAML 的内容}}

完成冷启动, 安装 iwencai 或 deep-research 两个CLI工具
```

2、然后让其基于模版流程创建基于某种策略的股票选股研究  

```md
{{下方 YAML 的内容}}

基于通用策略研究标准流程模版, 结合如下研究:

{{选股策略/调研/想法}}
 
新建策略研究流程到新的项目文件夹下的YAML文件中.
```

3、开始研究, 设置研究深度和预算

```md
完整阅读: <步骤2新建的YAML文件路径>

研究深度为DAG深度4，总节点数<50.
开始进行，按步骤一直执行到研究满足要求时停止, 返回最终报告与 DAG。
```

## 示例

<!-- markdownlint-disable MD033 -->
<img src="assets/dag.png" alt="DAG 示例图 (DAG Example)" height="500">
<!-- markdownlint-enable MD033 -->  

[报告预览-没有特别要求大纲-GPT随机的大纲结构](./assets/report.md)

基于模型: GPT-5.4-xhigh

## 研究模版

```yaml
execution_protocol:
  name: "通用策略研究标准流程"
  version: "1.2"
  intended_use: >
    适用于先完成研究设计与边界确认，再使用 deep-research 管理研究状态、
    证据链、报告工件与 DAG，同时使用 iwencai 获取跨领域（宏观、行业、个股、
    商品等）结构化数据与文档的通用策略研究任务。

  parameters:
    target_entity: "<研究对象，例如：新能源汽车行业 / 铜期货 / 某宏观指标>"
    entity_code: "<对象代码，如果有，例如：申万行业代码 / 期货合约代码>"
    benchmark_date: "<研究基准日 YYYY-MM-DD>"
    research_question: "<核心研究问题，例如：未来半年该资产的供需缺口与价格推演>"
    time_scope: "<时间范围，例如：近5年 / 最近3个完整年度>"
    audience: "<报告对象，例如：投研内部 / 投委会 / 客户简报>"
    focus_dimensions:
      - "<核心维度1，例如：宏观政策与产业周期>"
      - "<核心维度2，例如：基本面供需格局>"
      - "<核心维度3，例如：量价特征与资金面>"
      - "<核心维度4，例如：尾部风险与黑天鹅>"

  guiding_principles:
    - "研究设计先于 init；未明确边界前不进入正式研究状态"
    - "deep-research 是研究容器；iwencai 是数据入口"
    - "若本机缺少 iwencai 或 deep-research，则先执行冷启动安装，再进入正式研究流"
    - "官方/权威数据源（如统计局、交易所、官方公告）优先于二手摘要"
    - "query2data 用于结构化数值；search 用于文档与事件检索；每条 iwencai 命令尽量只表达一个意图"
    - "任何最终结论都应落在 deep-research artifact，而不是停留在终端输出"
    - "严格闭环默认包含 graph_snapshot、run --mode synthesize/review/complete 与 gate_check"
    - "最终报告优先使用 evidence 脚注引用，而不是裸链接堆叠"

  tool_roles:
    deep_research:
      responsibility:
        - "研究设计确认、初始化与节点建模"
        - "证据固化、核验、挂载与版本快照"
        - "研究报告写作、生命周期推进与 DAG 管理"
        - "门禁检查与最终导出"
      preferred_commands:
        - "status / doctor / init"
        - "node_add / node_update / node_resolve"
        - "evidence_archive / evidence_add / evidence_verify / evidence_link"
        - "graph_link / graph_snapshot / graph_export / graph_visualize"
        - "artifact_add / artifact_export"
        - "run --mode plan / evidence / synthesize / review / complete"
        - "gate_check / export"
    iwencai:
      responsibility:
        - "当前截面数据抓取"
        - "历史时间序列数据抓取"
        - "权威文档、研报与事件新闻检索"
        - "查询写法与 family 选择校准"
      preferred_commands:
        - "query2data"
        - "search"
        - "skillbook"
    shell:
      responsibility:
        - "缺失 CLI 时执行冷启动安装与 sidecar 初始化"
        - "命令调度与本地 JSON/HTML/PNG 落盘"
        - "按研究 slug 组织 .research-data 目录产物"

  standard_workflow:
    - phase_id: "S-1"
      name: "冷启动安装（如需）"
      objective: "当本机缺少 iwencai 或 deep-research 时完成首次安装，并准备 crawl4ai sidecar"
      primary_tool: "shell"
      preflight_checks:
        - "检查 Node.js >= 22、pnpm、Python >= 3.10 是否可用"
        - "检查 iwencai 与 deep-research 命令是否已存在；若均可用则跳过本阶段"
      install_commands:
        iwencai:
          - "version: 0.2.4"
          - "git clone https://github.com/meomeo-dev/iwencai_skills.git <tool_root>/iwencai_skills"
          - "cd <tool_root>/iwencai_skills && python -m pip install ."
        deep_research:
          - "version: 0.1.7"
          - "git clone https://github.com/meomeo-dev/deep-research.git <tool_root>/deep-research"
          - "cd <tool_root>/deep-research && pnpm run install:cli"
          - "deep-research sidecar_setup --project <workdir> --run-setup"
      notes:
        - "仅当命令缺失或需首次部署时执行安装；已安装时直接进入 S0"
        - "deep-research 的 managed sidecar 基于 crawl4ai；首次执行 sidecar_setup --run-setup 会创建共享 venv、安装依赖并执行 crawl4ai-setup"
        - "sidecar_setup 首次运行会下载 Chromium 等浏览器依赖，主要用于 evidence_archive 在网页反爬场景下通过浏览器获取内容"
        - "安装完成后执行 deep-research doctor 与 iwencai --help / iwencai skillbook 做最小验证"

    - phase_id: "S0"
      name: "确认工具"
      objective: "确认 deep-research 与 iwencai CLI 可用，并了解当前命令面"
      steps:
        - "检查 deep-research CLI 与 iwencai CLI 状态"
        - "若命令不存在，则回到 S-1 执行冷启动安装"
        - "必要时读取 iwencai skillbook，确认 family 选择与 query 写法"

    - phase_id: "S1"
      name: "研究设计与边界确认"
      objective: "明确研究边界、读者对象与本轮输出要求"
      primary_tool: "deep-research"
      steps:
        - "先写清 target_entity / benchmark_date / time_scope / audience / focus_dimensions"
        - "确认本轮 search/fetch 数据入口；若仅使用 iwencai，则声明 query2data + search 组合"
        - "完成边界确认后，再执行 init"

    - phase_id: "S2"
      name: "初始化研究"
      objective: "创建 research context 并进入计划阶段"
      primary_tool: "deep-research"
      steps:
        - "init --title '<研究主题>' --question '<核心研究问题>'"
        - "run --mode plan"

    - phase_id: "S3"
      name: "研究骨架建模"
      objective: "先定义问题、假设、缺口与结论框架，再去取证"
      primary_tool: "deep-research"
      recommended_nodes:
        - kind: "question"
          template: "<target_entity> 的核心策略问题"
        - kind: "task"
          template: "拆解 <核心维度1> 与 <核心维度2>"
        - kind: "hypothesis"
          template: "<核心维度1> 的现状及趋势推演"
        - kind: "gap"
          template: "<核心维度3> 的数据缺失与潜在风险"
        - kind: "conclusion"
          template: "<target_entity> 在 <time_scope> 内的阶段性策略判断"
      steps:
        - "用 node_add / graph_link 建立最小 DAG"

    - phase_id: "S4"
      name: "结构化数据抓取"
      objective: "通过 iwencai query2data 获取核心指标数值"
      primary_tool: "iwencai query2data"
      query_templates:
        snapshot_data:
          - query: "<target_entity> 最新 <核心指标1>"
          - query: "<target_entity> 最新 <核心指标2>"
          - query: "<target_entity> 最新 <核心指标3>"
        historical_timeseries:
          - query: "<target_entity> <time_scope> <核心指标序列1>"
          - query: "<target_entity> <time_scope> <核心指标序列2>"
      notes:
        - "每条 query2data 命令尽量只表达一个意图；多指标、多范围时拆分查询"
      file_outputs:
        - "<workdir>/.research-data/<slug>-snapshot.json"
        - "<workdir>/.research-data/<slug>-historical.json"

    - phase_id: "S5"
      name: "正式来源定位"
      objective: "通过 iwencai search 锁定权威报告、公告与关键事件"
      primary_tool: "iwencai search"
      query_templates:
        report_channel:
          - query: "<target_entity> <time_scope> 研究报告"
            channel: "report"
        announcement_channel:
          - query: "<target_entity> <time_scope> 公告"
            channel: "announcement"
        news_channel:
          - query: "<target_entity> <time_scope> 政策 新闻"
            channel: "news"
        investor_channel:
          - query: "<target_entity> <time_scope> 投资者关系活动"
            channel: "investor"
      notes:
        - "search 一条命令尽量只对应一个内容类型；多主体、多频道、多意图时拆成多条命令"

    - phase_id: "S6"
      name: "证据固化"
      objective: "把外部来源与本地数据写回 deep-research"
      primary_tool: "deep-research"
      steps:
        - "对正式 URL 使用 evidence_archive；必要时补充 --summary / --published-at"
        - "对本地 JSON/CSV/MD 使用 evidence_add"

    - phase_id: "S7"
      name: "证据核验"
      objective: "标记每条 evidence 的可信依据与时间口径"
      primary_tool: "deep-research"
      steps:
        - "对关键 evidence 执行 evidence_verify"

    - phase_id: "S8"
      name: "证据挂载"
      objective: "把 evidence 与研究节点建立 supports/refutes/annotates 关系"
      primary_tool: "deep-research"
      mapping_template:
        dimension_1_node:
          evidence:
            - "历史时间序列数据 JSON"
            - "权威深度报告 PDF"
        risk_node:
          evidence:
            - "事件/政策检索结果"
            - "截面数据异常值"
      steps:
        - "使用 evidence_link 把关键 evidence 挂到对应 question / hypothesis / conclusion / gap 节点"

    - phase_id: "S9"
      name: "状态更新与快照"
      objective: "在高信息增益节点后更新状态并保留版本快照"
      primary_tool: "deep-research"
      steps:
        - "node_update 更新本轮判断与状态变化"
        - "对已闭合的 gap/task/question 使用 node_resolve"
        - "graph_snapshot --reason '<阶段变化原因>'"

    - phase_id: "S10"
      name: "报告写作"
      objective: "把研究结论写入 artifact"
      primary_tool: "deep-research"
      artifact_kind: "report 或 conclusion_summary"
      recommended_sections:
        - "核心策略判断 (结论先行)"
        - "研究对象现状与定位"
        - "核心指标轨迹与归因"
        - "主要驱动力 / 利多因素"
        - "主要风险 / 利空因素"
        - "后续重点跟踪指标"
        - "参考（正文使用 [^evidence_xxx] 脚注，文末统一列出）"

    - phase_id: "S11"
      name: "生命周期闭环与门禁"
      objective: "满足 export 的结构、证据与生命周期要求"
      primary_tool: "deep-research"
      minimum_requirements:
        - "至少 1 条 graph_link，且 DAG 结构与研究问题相关"
        - "至少 1 次 graph_snapshot"
        - "核心结论节点的 epistemic-state 不应停留在 untested"
        - "至少执行 run --mode synthesize、run --mode review、run --mode complete；需要显式收束取证时可先执行 run --mode evidence"
        - "gate_check 通过"

    - phase_id: "S12"
      name: "导出"
      objective: "导出最终报告与 DAG"
      primary_tool: "deep-research"
      outputs:
        - type: "report export"
          command: "export 或 artifact_export"
        - type: "dag png"
          command: "graph_export --export-format png"
        - type: "dag html"
          command: "graph_visualize --html-path <workdir>/.research-data/<slug>-dag.html"

  canonical_interleaving:
    summary: "标准化执行序列：冷启动（如需） -> 设计 -> init -> 建模 -> 取数 -> 固化 -> 快照 -> 写作 -> 生命周期闭环 -> 门禁 -> 导出"
    sequence:
      - "检查 CLI；缺失时执行冷启动安装并完成 deep-research sidecar_setup"
      - "边界确认 -> deep-research init -> run --mode plan"
      - "deep-research node_add / graph_link 建立最小 DAG"
      - "iwencai query2data / search（每条命令一个意图，落盘 JSON）"
      - "deep-research evidence_archive/add -> evidence_verify -> evidence_link"
      - "deep-research node_update / node_resolve -> graph_snapshot"
      - "deep-research artifact_add"
      - "deep-research run --mode synthesize -> run --mode review -> run --mode complete"
      - "deep-research gate_check -> export / artifact_export / graph_export / graph_visualize"

  failure_handling:
    - condition: "iwencai 或 deep-research 未安装，导致命令不存在"
      mitigation: "回到 S-1；按仓库 README 完成本地安装，并在安装后重新执行 S0"
    - condition: "首次冷启动时 deep-research sidecar_setup 失败"
      mitigation: "先确认 Python 3.10+、网络可用，再重试 deep-research sidecar_setup --project <workdir> --run-setup；必要时先执行 deep-research doctor"
    - condition: "iwencai query2data 报错或返回噪声较大"
      mitigation: "拆分 query、减少单条命令意图、缩短时间范围、改为多次查询分别落盘"
    - condition: "iwencai search 结果混杂"
      mitigation: "按 report/announcement/news/investor 拆分 channel，并拆成单主体单内容类型查询"
    - condition: "evidence_archive 归档失败"
      mitigation: "先执行 sidecar_setup --run-doctor；必要时改用 --backend node，或先外部抓取后用 evidence_add"
    - condition: "export 被 gate_check 拦截"
      mitigation: "补充 graph_link / graph_snapshot，更新 node 状态，补跑 synthesize/review/complete 后重试"

  done_definition:
    conditions:
      - "研究边界已确认，且已完成 init"
      - "核心节点与边已建模，且至少保留 1 个 graph_snapshot"
      - "关键数据源已进入 evidence，并完成 verify/link"
      - "artifact(report 或 conclusion_summary) 已写入，正文引用 verified evidence"
      - "已执行 run --mode complete"
      - "gate_check 已通过，且报告与 DAG PNG/HTML 已成功导出"
```

## MIT License
