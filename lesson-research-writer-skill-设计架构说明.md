# lesson-research-writer-skill 设计架构说明

## 1. 文档目的

这份文档用于说明根目录当前课程写作 skill 的定位、架构、工作流、输入输出协议、测试方式和维护规则。

当前正式 skill 位于：

- `skills/lesson-research-writer-skill/`

它服务的目标不是“管理整套课程进度”，而是：

- 为单节 AI 编程课程生成一份可溯源、可核对、可更新、可教学的课程稿

## 2. Skill 的定位

### 2.1 核心目标

`lesson-research-writer-skill` 负责一节课从研究到成稿的完整链路：

1. 拆解当前课题
2. 收集并分级来源
3. 建立结论与证据映射
4. 生成教学展开简报
5. 输出正式课程稿
6. 输出质量审查和丰富度审查

### 2.2 不负责什么

它明确不负责这些事：

- 决定下一节学什么
- 管理整套课程进度
- 长期学习路径规划
- 泛化聊天陪伴
- 默认写代码教学

### 2.3 当前设计原则

- 先研究，再写课
- 先证据，再结论
- 先边界，再展开
- 先标时效，再写变动信息
- 先做结构化 `teaching-expansion-brief`，再写正文
- 一节课只解决 `1-3` 个核心问题
- 返工只允许局部修订，不全文推翻

## 3. 总体架构

### 3.1 双层架构

这个 skill 现在采用双层结构：

#### 阶段 A：Research Layer

负责把课程做“准”。

产物：

- `topic-card`
- `source-map`
- `evidence-map`

这一层的重点是：

- 来源可信
- 术语边界明确
- 结论可追溯
- 时效风险已标

#### 阶段 B：Teaching Expansion Layer

负责把课程做“能学进去”。

新增产物：

- `teaching-expansion-brief`

这一层的重点是：

- 为什么会有这个概念
- 它解决什么问题
- 它和周围概念的关系
- 它在项目流程里怎么出现
- 初学者最容易怎么误解
- 学完后应该能判断什么

当前版本进一步把这一层升级为结构化“课程展开强制层”：

- 固定为 `7 个核心 layer + 3 个辅助 layer`
- 每个 layer 都要绑定 `claimId`
- 每个 layer 都要写 `summary + focusPoints`
- 不是只检查“有没有标题”，而是检查“有没有展开厚度”
- `术语性质与边界` 仍保留在概念课正文里，但不计入 7 + 3 的 layer 数量

### 3.2 七个逻辑角色

当前 skill 的内部逻辑角色如下：

1. `Topic Analyst`
2. `Source Researcher`
3. `Evidence Editor`
4. `Teaching Expander`
5. `Lesson Writer`
6. `Quality Reviewer`
7. `Richness Reviewer`

它们不要求多智能体运行，但要求产物分层清楚。

## 4. 目录结构

当前主目录结构如下：

```text
skills/lesson-research-writer-skill/
├─ SKILL.md
├─ ROLE.md
├─ WORKFLOW.md
├─ INPUT_SCHEMA.json
├─ OUTPUT_SCHEMA.json
├─ REFERENCES_POLICY.md
├─ EVIDENCE_RULES.md
├─ QUALITY_GATES.md
├─ TEACHING_DEPTH_RULES.md
├─ EXPANSION_PATTERNS.md
├─ CONCEPT_BRIDGE_RULES.md
├─ LESSON_RICHNESS_REVIEW.md
├─ LESSON_TEMPLATES/
├─ PROMPTS/
├─ EXAMPLES/
└─ TESTS/
```

### 4.1 各层职责

`SKILL.md`

- 定义 skill 目标、边界、触发说明、读取顺序

`ROLE.md`

- 定义内部逻辑角色和产物职责

`WORKFLOW.md`

- 固定单节课处理流程

`INPUT_SCHEMA.json`

- 定义输入协议

`OUTPUT_SCHEMA.json`

- 定义严格输出协议

`REFERENCES_POLICY.md`

- 来源等级、冲突处理、快照规则

`EVIDENCE_RULES.md`

- `fact / course_conclusion / author_inference` 的证据规则

`QUALITY_GATES.md`

- 检查课程“准不准”

`TEACHING_DEPTH_RULES.md`

- 检查课程是否有足够教学层次

`EXPANSION_PATTERNS.md`

- 规定每节课至少使用 3 种展开方式

`CONCEPT_BRIDGE_RULES.md`

- 规定概念桥接写法

`LESSON_RICHNESS_REVIEW.md`

- 单独审查课程有没有展开感

`LESSON_TEMPLATES/`

- 按课型提供固定模板

`PROMPTS/`

- 按工作环节拆分 prompt

`EXAMPLES/`

- 保存黄金样板

`TESTS/`

- 保存结构回归、来源审计、丰富度回归

## 5. 固定工作流

当前固定工作流共 8 步：

1. 读取课程输入上下文
2. 生成 `topic-card`
3. 建立 `source-map`
4. 建立 `evidence-map`
5. 生成 `teaching-expansion-brief`
6. 依据课型模板生成 `lesson draft`
7. 执行 `quality review`
8. 执行 `richness review`

### 5.1 关键约束

- `lesson draft` 只能基于 `evidence-map + teaching-expansion-brief` 写
- 不允许跳过教学展开层直接写定义型课程
- 不允许新增无证据核心判断
- 若审查失败，只允许按 `claimId`、段落块或审查项局部返工

## 6. 输入输出协议

### 6.1 输入协议

当前 `INPUT_SCHEMA.json` 保持轻量，核心必填字段包括：

- `lessonId`
- `lessonTitle`
- `stage`
- `lessonType`
- `audience`
- `fixedOutline`

可选字段包括：

- `currentProjectContext`
- `mustCover`
- `mustAvoid`
- `notes`

### 6.2 输出协议

当前 `OUTPUT_SCHEMA.json` 已切到严格版，必填字段为：

- `topicCard`
- `sourceMap`
- `evidenceMap`
- `teachingExpansionBrief`
- `lessonDraft`
- `qualityReview`
- `richnessReview`
- `updateRiskLevel`

其中 `teachingExpansionBrief` 不再是自由文本，而是结构化对象，至少包含：

- `standardStage`
- `lessonType`
- `requiredLayers`
- `supportingLayers`

每个 layer 至少包含：

- `layerId`
- `title`
- `summary`
- `focusPoints`
- `claimIds`

### 6.3 sourceMap 统一协议

当前 skill 内部不再允许两套字段并存，统一使用：

- `sourceId`
- `title`
- `url`
- `level`
- `usage`
- `checkedDate`
- `updateRisk`
- `snapshotNeeded`
- `supports`

### 6.4 evidenceMap 统一协议

每条证据至少包含：

- `claimId`
- `claim`
- `claimType`
- `supportingSources`
- `stability`
- `notes`

其中 `claimType` 固定为：

- `fact`
- `course_conclusion`
- `author_inference`

## 7. 教学展开层的设计重点

这是当前 skill 相比旧版本最重要的升级。

### 7.1 旧问题

旧版课程虽然来源更准、结构更整洁，但容易停留在：

- 定义
- 场景
- 小结

问题不在字数少，而在层次少。

### 7.2 新要求

概念课现在进入“统一标准阶段”，至少覆盖：

### 7 个核心展开层

1. 为什么会有这个概念
2. 它真正解决什么问题
3. 它和相邻概念是什么关系
4. 一个贯穿式 mini 案例
5. 它在项目流程中出现在哪一步
6. 初学者为什么会误解
7. 学完后应该能判断什么

### 3 个辅助强制层

1. 它在当前项目里的位置
2. 本课不讲什么
3. 上下课衔接

补充说明：

- `术语性质与边界` 是概念课正文固定锚点，不属于 `teaching-expansion-brief` layer
- `current_project_anchor` 必须绑定 `claimId`
- `out_of_scope` 和 `lesson_bridge` 可以不绑定 `claimId`

### 7.3 展开方式

每节课至少使用 3 种展开方式：

- 对比展开
- 流程展开
- 案例展开
- 误区展开
- 演化展开
- 项目映射展开

### 7.4 概念桥接

当前强制桥接项包括：

- `AI 编程 ↔ 传统编程`
- `生成 ↔ 修改 ↔ 连接 ↔ 整合`
- `前端 ↔ API ↔ 后端 ↔ 数据库`
- `数据库存储 ↔ 持久化`

## 8. 模板策略

### 8.1 概念课增强模板

当前概念课固定结构如下：

1. 本节要解决什么问题
2. 一句话结论
3. 为什么会有这个概念
4. 术语性质与边界
5. 它真正解决什么问题
6. 它和相邻概念是什么关系
7. 一个贯穿式 mini 案例
8. 它在项目流程中出现在哪一步
9. 它在当前项目里的位置
10. 初学者最容易怎么误解，为什么
11. 本节学完你应该能判断什么
12. 本课不讲什么
13. 上下课衔接
14. 本节练习
15. 本节小结
16. 来源与延伸阅读

### 8.2 模板目标

目标不是把课程写长，而是把课程从：

- 定义型课程

升级成：

- 解释型课程
- 关系型课程
- 判断型课程

## 9. 黄金样板

当前活动黄金样板为：

- `skills/lesson-research-writer-skill/EXAMPLES/1.1-ai-programming/`
- `skills/lesson-research-writer-skill/EXAMPLES/1.4-web-stack-map/`

包含 7 件套：

- `topic-card.md`
- `source-map.json`
- `evidence-map.json`
- `teaching-expansion-brief.json`
- `lesson.md`
- `review.md`
- `richness-review.md`

它的职责不是替代课程正文，而是作为 skill 内部样板，验证：

- 新 output schema
- 新 teaching expansion layer
- 新 richness review

## 10. 测试与验证

当前测试分为 3 层：

### 10.1 结构回归

脚本：

- `skills/lesson-research-writer-skill/TESTS/run_regression.py`

检查内容：

- 输出 schema 是否完整
- sourceMap 是否统一字段协议
- evidenceMap 是否可追溯

### 10.2 来源审计

脚本：

- `skills/lesson-research-writer-skill/TESTS/audit_example_sources.py`

检查内容：

- 来源字段是否完整
- 来源链接是否当前可访问
- `snapshotNeeded` 为真时是否有本地快照

### 10.3 丰富度回归

目前 richness 检查已经并入结构回归逻辑，至少会拦截：

- 只有定义，没有“为什么”
- 没有结构化展开层
- 只有分类，没有“关系”
- 只有例子，没有“流程”
- 只有结论，没有“判断”
- 没有当前项目映射
- 没有本课边界
- 没有上下课衔接
- 缺少 supporting layer 或 supporting layer claim 绑定退化
- 只有来源列表，没有正文展开

## 11. 当前状态与维护建议

### 11.1 当前状态

当前 skill 已经完成：

- 双层 workflow
- 严格版 output schema
- 教学展开强制层
- 黄金样板进入 `1.1 + 1.4`
- 三层测试通过

### 11.2 后续维护建议

后续继续扩展时，建议遵守以下顺序：

1. 先稳定 skill
2. 再用 skill 重写课程
3. 先拿少量样板课验证
4. 再推广到整阶段课程

### 11.3 不要做的事

- 不要同时维护两套 sourceMap 协议
- 不要让课程正文和 skill 样板长期漂移
- 不要把 `QUALITY_GATES` 和 `LESSON_RICHNESS_REVIEW` 混成一份
- 不要让概念课退回“定义 + 场景 + 小结”的旧薄结构
