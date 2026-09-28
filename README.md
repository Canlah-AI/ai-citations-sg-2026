---
license: cc-by-4.0
language:
- en
- zh
task_categories:
- question-answering
- text-retrieval
size_categories:
- n<1K
pretty_name: "Singapore GEO Citation Probes, September 2026 (ChatGPT and Google AI Mode)"
tags:
- generative-engine-optimization
- geo
- ai-search
- citation-analysis
- chatgpt
- google-ai-mode
- singapore
configs:
- config_name: queries
  data_files: queries.csv
- config_name: citations
  data_files: citations.csv
- config_name: chatgpt_answers
  data_files: chatgpt_answers.jsonl
- config_name: page_annotations
  data_files: page_annotations.csv
- config_name: page_types
  data_files: page_types.csv
---

# Singapore GEO Citation Probes 2026-09

**English summary.** 110 buyer questions from five Singapore industries (dental and aesthetics, family law, tuition, B2B SaaS, e-commerce) were put to ChatGPT (OpenAI Responses API, `gpt-5.5` with the web search tool, approximate location Singapore) and, in round 1, also to Google AI Mode (through a third-party SERP API, `gl=sg`). This dataset contains the questions, every cited URL with its order in the answer (917 rows, 767 of them from the official runs), the ChatGPT answer texts, our page-by-page annotations of 92 cited pages, and our catalogue of 46 cited-page types. Google AI Mode answer text, Google organic result lists and copies of third-party pages are not included; see "What is not included". Licence: CC BY 4.0.

**DOI** [10.5281/zenodo.23005020](https://doi.org/10.5281/zenodo.23005020) · Mirrors: [GitHub](https://github.com/Canlah-AI/ai-citations-sg-2026) · [Hugging Face](https://huggingface.co/datasets/CanlahAI/ai-citations-sg-2026) · [canlah.ai](https://canlah.ai/data/ai-citations-sg-2026/) · Method book: *GEO Playbook* v1.0, https://canlah.ai/zh/playbook/ (DOI [10.5281/zenodo.23005022](https://doi.org/10.5281/zenodo.23005022))

本数据集由 Canlah AI 在写《GEO Playbook》时采集。我们在两个 AI 引擎上问了一批新加坡买家会问的问题，记下答案引用了哪些网页、按什么顺序，再逐页拆解了一部分被引网页。数字的重数过程和关键发现见 [stats.md](stats.md)。

---

## 1. 动机

- **为什么做**：想知道中小企业的网页怎样才会被 AI 答案引用，而且要有实测，不只凭经验。
- **谁做的**：Canlah AI（新加坡），作者 Haoyang Pang。
- **经费**：公司自有经费，没有外部资助。
- **公开的目的**：让别人能核对《GEO Playbook》里每个数字的出处，也让别人能在同一批问句上复测。

## 2. 组成

| 文件 | 行数 | 一行是什么 |
|---|---|---|
| `queries.csv` | 110 | 一句问句（第一轮 60 + 第二轮 50） |
| `citations.csv` | 917 | 一个答案里的一个不同网址（正式 767 + 补充 150） |
| `chatgpt_answers.jsonl` | 141 | 一次 ChatGPT 调用的答案（第一轮 60、第二轮正式 50、第二轮补跑 30、补跑 1） |
| `page_annotations.csv` | 141 | 我们对一个被引网页的标注（第一轮逐页拆解 92 + 第二轮页型目录样本 49） |
| `page_types.csv` | 46 | 一种被引页型 |
| `stats.md` | — | 重数结果、与旧口径的差异、关键发现 |

### 2.1 两轮探测

| | 第一轮 | 第二轮 |
|---|---|---|
| 时间（UTC+8，取自记录） | 2026-09-23 00:10 | 2026-09-23 01:05 |
| 问句 | 5 行业 × 12 = 60，其中 6 句中文（牙科、家事法、补习各 2 句） | 5 行业 × 10 = 50，其中 5 句中文（每个行业 1 句） |
| 问法 | 买东西前的决策问题：多少钱、哪家好、A 还是 B、能不能、出了问题怎么办 | 每行业同一套 9 类问法，每类 1 句：定义、流程、统计、计算、附近、单品测评、支柱（全指南）、规则、清单；第 10 句按行业不同 |
| 引擎 | ChatGPT + Google AI Mode | 只有 ChatGPT（SerpApi 配额用完，AI Mode 没跑） |
| 正式引用条数 | ChatGPT 307、AI Mode 219，共 526 | 241 |
| 另外 | 每句还取了 Google 自然结果前 10（只用于聚合统计，不公开） | 牙科、B2B、电商 30 句又独立跑了一次；培训第 7 句正式那次超时，另补跑了一次 |

**「一条引用」的意思**：一个答案里出现的一个不同网址。探测脚本在答案内部去重，所以同一网址在同一答案里只记一条。「526 条」不等于 526 个不同网址（跨答案去重后是 474 个）。

### 2.2 `queries.csv`

| 列 | 说明 |
|---|---|
| `query_id` | `R1-DEN-01` 这种格式：轮次 - 行业（DEN 牙科医美 / LAW 家事法 / TUI 补习教育 / B2B / ECO 电商）- 本行业第几句 |
| `industry`, `industry_zh` | 行业 |
| `round`, `position_in_round` | 轮次、本轮第几句 |
| `language` | `en` / `zh`，按问句里有没有汉字判定 |
| `intent_family` | 问法大类。第一轮全部是「决策」；第二轮是定义 / 流程 / 统计 / 计算 / 附近 / 单品测评 / 支柱 / 规则 / 清单 |
| `intent_detail` | 问法细类，我们标的。第一轮用分号列出一句里混合的几种问法，例如 `价格;资格` |
| `query` | 问句原文 |
| `probed_at` | 该轮记录里的时间 |
| `engines_run` | 这句问了哪些引擎 |
| `chatgpt_status`, `ai_mode_status` | `ok`（有引用）/ `ok_no_citations`（答了但没有引用）/ `error`（接口报错或超时）/ `rate_limited`（AI Mode 返回了请求上限提示）/ `not_run_quota`（第二轮没跑 AI Mode） |
| `google_organic_top10_collected` | 有没有取 Google 自然结果前 10（第二轮培训行业没取） |

### 2.3 `citations.csv`

| 列 | 说明 |
|---|---|
| `citation_id` | 问句 - 运行 - 引擎 - 顺序 |
| `run_id` | `r1`、`r2`：正式运行。`r2_rerun`：第二轮牙科、B2B、电商的独立第二次调用。`r2_retry`：培训第 7 句的补跑 |
| `official` | `yes` = 正式口径（stats.md 里的主数字只用这些行） |
| `engine` | `chatgpt` / `google_ai_mode` |
| `position` | 在这个答案里的顺序，从 1 开始。ChatGPT：按答案正文里第一次标注这个网址的先后排。AI Mode：按接口返回的来源列表顺序排，不一定等于正文里出现的先后 |
| `url` | 被引网址，只删了 `utm_source=openai` 这个跟踪参数 |
| `domain`, `reg_domain` | 主机名（去掉 `www.`）、注册域名（`booking.estheclinic.com.sg` → `estheclinic.com.sg`） |
| `site_type` | 按域名规则自动分类，规则见第 4 节第 3 条 |
| `is_ugc` | 是否论坛社媒类，`site_type = ugc_social` 时为 `yes` |
| `mentions_in_answer` | 这个网址在答案正文里被标注了几次。只有牙科和电商的补跑文件保存了这一层信息，其余留空 |
| `page_type_id` | 页型编号，见 `page_types.csv`。**只有牙科行业的正式运行有**，是我们写牙科版时逐条人工标的。另有三个不在 46 型里的标签：`maker`（厂商或品牌的产品资料页）、`booking`（预约商品页）、`gcard`（Google 内部卡片） |

### 2.4 `chatgpt_answers.jsonl`

每行一个 JSON 对象，字段有：`query_id`、`round`、`run_id`、`official`、`engine`、`model_requested`（都是 `gpt-5.5`）、`model_receipt`（接口返回的实际型号，例如 `gpt-5.5-2026-04-23`，没存的为 null）、`tool`、`user_location`、`api`、`probed_at`、`status`、`error`、`n_citations`、`text_chars`、`text_truncated`、`truncated_at_chars`、`note`、`text`。

**截断**：第一轮和第二轮的正式运行只存了答案**前 600 个字符**。这 110 条里，108 条正好 600 字符（`text_truncated = true`），2 条是接口报错、没有正文。第二轮补跑的 30 条是**全文**。培训第 7 句的补跑存到前 1,500 字符。

### 2.5 `page_annotations.csv`

我们自己的标注，分两种来源：

- `r1_page_teardown`（92 行）：第一轮报告《实测-被引页版式》的逐页底稿。底稿由 AI 助手（多代理工作流）按我们定的字段读页面原件写成，没有逐条人工复核；这里把它结构化成表。
- `r2_catalogue_sample`（49 行）：第二轮页型目录里每种页型列的「被引样本」。

| 列 | 说明 |
|---|---|
| `cited_by`, `n_citations_chatgpt`, `n_citations_ai_mode` | 从 `citations.csv` 按网址（逐字相同）反查。第一轮只查正式运行。第二轮样本：`cited_by` 看所有运行，`n_citations_chatgpt` 只数正式运行 `r2`，所以只在补跑里出现的页这里是 0，`notes` 里写明「found only in the supplementary re-run」 |
| `page_type_teardown` | 第一轮拆解时用的页型标签（registry / qa / pdp / article / comparison / listicle / docs / profile / category），这是 46 型之前的一套叫法 |
| `page_type_id` | 46 型编号：第一轮牙科取自逐条人工标注，第二轮样本取自它所在的目录小节；其余留空 |
| `site_owner_teardown`, `h1` | 拆解时记下的站点身份与 H1 |
| `above_fold_words` | 首屏（H1 到第一个 H2 之间）的词数，拆解时估的 |
| `n_tables`, `has_table` | HTML 表格张数。用 div 画的「表」按 0 计 |
| `has_faq` | 有没有 FAQ 区块 |
| `numbers_on_page_approx` | 页面上数字的个数，拆解时估的 |
| `byline_date_note` | 署名和日期的原始记录 |
| `excerpt_pos_pct`, `excerpt_pos_min_pct` | 被抄那句在页面上的位置，按篇幅百分比。**目测估计**，从 `excerpt_position_note` 里抽出来的；一页被抄了几处就有几个值 |
| `note_mentions_table`, `note_mentions_faq` | 位置记录里有没有提到表格或 FAQ。这是关键词匹配的结果，不是逐条编码 |
| `excerpt_position_note`, `notes` | 标注原文（中文），里面有少量从页面或 ChatGPT 答案里摘出的短句。凡是摘了 Google AI Mode 答案原句（20 个字符以上）的地方，已替换成「（AI Mode 答案原句，不公开）」，共 44 处（30 处能和存下的前 600 字逐字对上；另 14 处是标注里明说出自 AI Mode 答案、但落在前 600 字之外的，按标注的说法替换） |

抽不出来的字段一律留空，没有补猜。

### 2.6 `page_types.csv`

46 种页型：`page_type_id`、`catalogue_no`、中英文名、六个家族（`family_en` / `family_zh`）、证据档（A = 第一轮两个引擎都测过，B = 第二轮只在 ChatGPT 上测过，C = 只在一份外部拆解里见过，我们没测）、典型问法、目录里写的被引数（原文）、第二轮 ChatGPT 的 n 及分行业拆分（目录里写了的才填）、主要被哪个引擎引、典型长度、受监管行业能不能做、适用行业，以及牙科的逐条重数（`dental_recount_*`）。

**注意**：目录里的 n 是分析时按页型点数的，口径混了正式运行、补跑和出现次数（见 stats.md 第 2 节），只能当页型之间的相对大小看。`dental_recount_*` 三列才是从正式记录重数的。

## 3. 采集方法

- **ChatGPT**：OpenAI Responses API，`model = gpt-5.5`，工具 `web_search`，`user_location = {type: approximate, country: SG, city: Singapore}`，没有系统提示词，问句原样作为 `input`。引用取自 `output_text` 里类型为 `url_citation` 的标注，每个答案内部去重。这是**接口**结果，不是 chatgpt.com 网页版看到的界面。型号是锁定的，不随主力型号更换。
- **Google AI Mode**（只有第一轮）：通过 SerpApi 的 `google_ai_mode` 引擎，`gl=sg`、`hl=en`；引用取自返回的 `references` 列表。是第三方接口的结果，不是浏览器里看到的页面。
- **Google 自然结果**（只用于聚合统计）：Serper.dev，`gl=sg`，每句取前 10。
- **问句来源**：第一轮的热词取自 Google 自动补全（`gl=sg`），我们挑了决策类问法，另写了中文句；第二轮按 9 类问法每类写 1 句。
- **被引网页**：第一轮对 92 个被引网页下载原件逐页拆解，部分页面用浏览器渲染后再读。原件不公开。
- **每句只问了一次**（正式口径）。第二轮有 30 句问了两次，两次的差异见 stats.md 3.5。

## 4. 预处理

1. 网址只删 `utm_source=openai` 参数（917 条里 30 条受影响），其余不动。删完之后没有答案内部新增重复。
2. `domain` 去掉开头的 `www.`；`reg_domain` 按常见二级后缀（`.com.sg`、`.gov.sg`、`.co.uk` 等）截取。
3. **站点类型规则**（自动分类，没有逐条人工核对）：
   - `government`：主机名以 `.gov.sg` 或 `.gov` / `.gov.xx` 结尾；另加 `chas.sg`、`healthhub.sg`、`visitsingapore.com`、`roc-taiwan.org`。
   - `public_nonprofit`：主机名以 `.moe.edu.sg` 或 `.int` 结尾，或注册域名是下列之一：`singhealth.com.sg`、`kkh.com.sg`、`sgh.com.sg`、`nuh.com.sg`、`nhghealth.com.sg`、`ndcs.com.sg`、`healthxchange.sg`（公立医疗）；`nus.edu.sg`、`smu.edu.sg`、`nyp.edu.sg`、`sota.edu.sg`（公立院校）；`elitigation.sg`、`sal.sg`、`academypublishing.org.sg`、`lawsociety.org.sg`、`sda.org.sg`、`case.org.sg`、`sias.org.sg`、`abs.org.sg`、`aomss.org.sg`、`cdac.org.sg`、`probono.sg`、`britishcouncil.sg`（法定、专业与非营利团体）；`aad.org`、`aaoinfo.org`、`mouthhealthy.org`、`perio.org`、`cda-adc.ca`、`bos.org.uk`、`peppol.org`（海外专业团体与标准组织）；`mayoclinic.org`、`clevelandclinic.org`（学术医疗机构）。注意：`.edu.sg` 结尾的私立补习机构不算在内。
   - `ugc_social`（即 `is_ugc = yes`）：reddit、quora、facebook、youtube、instagram、tiktok、x / twitter、linkedin、medium、lemon8、小红书、知乎、tripadvisor、yelp、glassdoor、scribd、pinterest、threads，以及主机名以 `forum.` 或 `forums.` 开头的站。
   - `google_internal`：`google.com` 本身的页面（AI Mode 的 searchviewer 卡片、Shopping 商品卡）。
   - `commercial_media_other`：其余全部，包括企业官网、诊所、律所、电商、媒体、目录站、评测站。**这一类没有再细分**。
4. 页型标注：牙科是写牙科版时逐条人工标的；其他行业没有逐条页型。
5. 页面标注从中文底稿结构化：字段按「H1：」「表：」「FAQ：」等标签切出；位置百分比用正则抽取，抽取前先去掉引号里的内容，并跳过「都在前 30%」这类概括性说法。

## 5. 局限

- **每句只问一次。** 第二轮同一句问两次，网址的交集只占并集的 24%（53 / 222，stats.md 3.5）。任何单句结果都是一次抽样。
- **第二轮只有一个引擎。** AI Mode 没测，所以 33 种第二轮页型只有 ChatGPT 的证据；「没测」不等于「AI Mode 不引」。
- **位置是目测。** 被抄句的位置是标注人按页面篇幅估的，不是按字符编码的。
- **样本小。** 每个行业 10–12 句，每个页型的 n 大多是个位数。
- **新加坡单一市场**，英文为主，中文只有 11 句。
- **接口，不是界面。** 两个引擎都是通过接口取的，和用户在网页或 App 里看到的可能不同；AI Mode 还经过了第三方 SERP 接口。
- **时间点。** 全部取自 2026-09-23 凌晨，两轮相隔约 55 分钟。模型和搜索索引会变。
- **答案截断。** 正式运行的答案只存了前 600 字符，没法把答案后半段的每一句对应到引用。
- **站点类型是规则分类**，「其他」里混着商业站和媒体站。
- **页型目录的 n 值口径不一**（见 stats.md 第 2 节）。
- **两个 AI 引擎都被我们要求在新加坡定位下作答**，但 AI Mode 腿只设了 `gl=sg`，没有更细的地理位置。

## 6. 适合与不适合的用途

**适合**：
- 核对《GEO Playbook》里的数字；
- 研究 AI 答案的引用来源结构（政府、商业、论坛社媒的比例，两个引擎的差别，问法对来源的影响）；
- 在同一批问句上复测，比较不同时间、不同型号；
- 作为被引页版式研究的起点（网址 + 标注）。

**不适合**：
- 当作排名或「AI 可见度」的稳定基准（每句一次抽样）；
- 推断某家具体企业的表现，或给企业打分；
- 推广到新加坡以外的市场，或推广到这 5 个行业以外；
- 训练模型去复刻某个引擎的答案；
- 在没有重新核对的情况下，把答案里的价格、法规等事实当作现行信息使用（答案是 2026-09-23 的快照，可能有错）。

## 7. 不包含什么

| 不包含 | 原因 | 用什么代替 |
|---|---|---|
| Google AI Mode 的答案正文，包括标注里摘的原句 | Google 的内容，不归我们再发布 | 只发被引网址、顺序和引擎；标注里的原句已替换成占位符 |
| Serper 抓的 Google 自然结果列表 | Google 的排名结果 | 只在 stats.md 里发聚合数（前 10 里论坛社媒占几成、AI 引用有几成在前 10 里） |
| 下载的第三方网页原件 | 第三方版权 | 只发网址和我们的标注 |
| 补跑文件里「每条引用前 260–350 字」的片段 | 和全文答案重复 | 全文在 `chatgpt_answers.jsonl` |
| 培训第 7 句两次超时的补跑（其中一次换了措辞） | 没有数据 | 在本节记录 |
| 第一轮的备份文件 | 和正式文件逐字相同，只是少了 Google 自然结果字段 | — |

答案文本里可能出现公开的机构名、执业者名和新闻里提到的事件，这些都来自公开网页。问句里点名的企业（例如某家诊所、律所、补习中心）是作为「某家好不好」这类问法的例子，不代表我们对它们的评价。

## 8. 许可

内容以 [CC BY 4.0](LICENSE) 发布。使用时请署名并注明出处。ChatGPT 答案文本是我们通过 OpenAI API 取得的输出；被引网址指向的网页，版权归各自的所有者。

## 9. 怎么引用

请引用本数据集（元数据见 [CITATION.cff](CITATION.cff)）：

> Pang, Haoyang (2026). *Singapore GEO Citation Probes, September 2026 (ChatGPT and Google AI Mode)* (Version 1.0) [Data set]. Canlah AI. Zenodo. https://doi.org/10.5281/zenodo.23005020

```bibtex
@dataset{pang_2026_sg_geo_citations,
  author    = {Pang, Haoyang},
  title     = {Singapore GEO Citation Probes, September 2026 (ChatGPT and Google AI Mode)},
  year      = {2026},
  version   = {1.0},
  publisher = {Zenodo},
  doi       = {10.5281/zenodo.23005020},
  url       = {https://doi.org/10.5281/zenodo.23005020}
}
```

同一份数据的其他副本：GitHub https://github.com/Canlah-AI/ai-citations-sg-2026 · Hugging Face https://huggingface.co/datasets/CanlahAI/ai-citations-sg-2026 · 官网 https://canlah.ai/data/ai-citations-sg-2026/。与本数据集配套的方法书是《GEO Playbook》v1.0：https://canlah.ai/zh/playbook/（DOI 10.5281/zenodo.23005022）。

## 10. 维护

- 联系方式：Canlah AI，https://canlah.ai
- 已知的下一步：Google AI Mode 腿的第二轮补测，以及对关键问句做多次重复。补测数据会作为新版本发布，不覆盖本版。
