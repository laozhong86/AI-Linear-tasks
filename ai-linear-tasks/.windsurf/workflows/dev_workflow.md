---
description: 使用Linear和MCP管理任务驱动的开发工作流指南
globs: "**/*"
alwaysApply: true
---
# 基于Linear的MCP开发工作流

本指南概述了通过MCP服务器使用Linear管理软件开发项目的典型流程,特别是在与AI助手协作时。

## 主要交互方式: Linear MCP 服务器

对于AI代理和集成开发环境(如Cursor),通过其**MCP服务器与Linear交互是任务管理的首选方法**。

- Linear MCP服务器通过一组工具公开功能(例如`get_issue`、`create_issue`、`update_issue`、`list_issues`)。
- 这种方法提供结构化的数据交换、强大的错误处理和与AI驱动工作流的无缝集成。
- 请参阅特定Linear MCP服务器的文档(例如`mcp.config.usrlocalmcp.linear`)以获取可用工具列表及其用法。

## 开发工作流程全流程

### 阶段0: 需求获取、澄清与PRD生成

1. **用户提出初步需求**:
   - 用户通过自然语言向AI编程工具描述功能、修复或想法(通常相对简洁或模糊)
   - *示例: "我需要一个功能，让用户可以把他们的数据导出成CSV文件。"*

2. **AI启动需求澄清对话**:
   - **触发**: AI编程工具基于规则或对用户输入的理解，判断这是新需求描述
   - **AI角色**: 规则指示AI扮演"业务分析师"或"产品经理"角色
   - **分析与提问**: AI分析需求完整性，如信息不足则提出澄清性问题:
     - *示例问题: "关于CSV导出功能，我想确认几点：具体导出哪些数据字段？对CSV格式有特定要求吗？主要面向哪些用户角色？解决的核心痛点是什么？预期的用户流程是怎样的？"*
   - **对话交互**: 进行多轮对话，直到收集足够信息

3. **AI生成结构化PRD草案**:
   - **触发**: 用户确认信息完毕或AI判断信息足够
   - **执行**: 基于对话内容和预定义PRD模板生成结构化文档草案(Markdown格式)
   - **模板应用**: 将收集的信息填入相应章节，信息不足部分可标记为TBD

4. **用户审核与确认PRD**:
   - **呈现**: AI在界面中展示PRD草案
   - **请求确认**: "这是根据讨论生成的PRD草案，请审阅。如无问题，我将基于此创建相应的Linear任务。"

5. **PRD定稿**:
   - 用户确认内容无误，此PRD文本将作为下一阶段输入

### 阶段1: PRD分析与Linear任务创建

1. **获取团队ID**:
   - 使用`get_team`获取团队ID。例如:
     ```
     run_mcp server_name='mcp.config.usrlocalmcp.linear' tool_name='get_team' args='{"query":"gxgen"}'
     ```
     返回的团队ID为: `e16e7921-9f2e-46ff-82de-3d13bd656cb9`

2. **创建项目**:
   - 使用`create_project` MCP工具在Linear中创建新项目。例如:
     ```
     run_mcp server_name='mcp.config.usrlocalmcp.linear' tool_name='create_project' args='{"name":"AI-tasks", "description":"AI编程与任务协同体系的开发项目", "teamId":"e16e7921-9f2e-46ff-82de-3d13bd656cb9"}'
     ```

3. **PRD分析与任务提取**:
   - **触发**: 用户确认PRD后
   - **分析**: AI使用内置LLM分析PRD内容，提取任务列表(含title、description等)
   - **执行**: 遍历提取的任务列表，逐个调用`create_issue` MCP工具
   - **反馈**: MCP服务将创建结果返回给AI编程工具，工具再反馈给用户

4. **建立任务依赖关系**:
   - 分析任务间逻辑关系，根据依赖关系更新任务描述
   - 使用适当的标签标记任务类型(如Feature、Bug、Improvement等)

### 阶段2: 开发规划与任务跟踪

1. **开始编码会话**:
   - 使用`list_issues`检索当前任务。例如:
     ```
     run_mcp server_name='mcp.config.usrlocalmcp.linear' tool_name='list_issues' args='{"teamId":"e16e7921-9f2e-46ff-82de-3d13bd656cb9", "stateId":"96d6504a-2318-4fdc-be63-230e6753a5c5"}'
     ```
   - 使用`list_my_issues`查看分配给你的任务。

2. **确定下一个任务**:
   - 分析`list_issues`返回的问题列表。
   - 根据依赖关系(确保前置任务已完成)、优先级和项目里程碑选择任务。
   - 使用Linear的标签和状态过滤功能识别可执行的任务。

3. **理解任务需求**:
   - 使用`get_issue`和问题ID查看具体任务详情。
   - 检查问题的标题、描述、评论、附件以及任何链接的子问题或相关问题。

4. **分解复杂任务**:
   - 如果任务(问题)太大,将其分解为更小、可管理的子问题。
   - 使用`create_issue`创建子问题,在描述中使用`Parent: #XX`格式链接到父问题。
   - 子问题应继承父问题的标签和优先级。

### 阶段3: 实施与进度管理

1. **实施**:
   - 根据Linear问题、其子问题和项目标准实施代码。
   - 使用`update_issue`将问题状态更新为"In Progress"。例如:
     ```
     run_mcp server_name='mcp.config.usrlocalmcp.linear' tool_name='update_issue' args='{"id":"ISSUE_ID", "stateId":"32030904-9675-4dce-9d5d-bf56395ac72c"}'
     ```

2. **记录进度和详情**:
   - 使用`create_comment`在Linear问题上添加实施说明、发现或问题。例如:
     ```
     run_mcp server_name='mcp.config.usrlocalmcp.linear' tool_name='create_comment' args='{"issueId":"ISSUE_ID", "body":"实施更新: ..."}'
     ```
   - 对于子问题,同样更新其描述或添加评论。

3. **验证和测试**:
   - 根据测试策略验证任务(可能记录在问题本身或单独的测试指南中)。
   - 完成测试后将状态更新为"In Review"。

4. **完成任务**:
   - 使用`update_issue`将已完成的任务标记为"Done"。例如:
     ```
     run_mcp server_name='mcp.config.usrlocalmcp.linear' tool_name='update_issue' args='{"id":"ISSUE_ID", "stateId":"9e265471-931c-41e0-bca2-a7c3f4436c02"}'
     ```

### 阶段4: 处理实施偏差和新任务

1. **处理实施偏差**:
   - 如果实施与原计划不同,使用`update_issue`更新相关Linear问题。
   - 添加评论详细说明偏差原因和解决方案。

2. **处理新发现的任务**:
   - 当在实施过程中发现新任务时,使用`create_issue`创建新问题。
   - 确保设置适当的标签、状态和依赖关系。
   - 添加详细说明,明确新任务的来源和必要性。

## Linear工作流状态体系

Linear中配置了以下工作流状态:
- `Backlog` (cc5223a5-d531-40b3-91af-683f723d1755): 待规划的任务
- `Todo` (96d6504a-2318-4fdc-be63-230e6753a5c5): 已规划待开始的任务
- `In Progress` (32030904-9675-4dce-9d5d-bf56395ac72c): 开发中的任务
- `In Review` (31077f84-e37e-4ff8-bf8a-2a7852d082cf): 代码审查中
- `Done` (9e265471-931c-41e0-bca2-a7c3f4436c02): 已完成的任务
- `Canceled` (cf74b37f-79d3-4d86-ab49-ec718e8d6b1a): 已取消的任务
- `Duplicate` (7effd6f0-92ed-41ec-88fc-1bd1d73b7c87): 重复的任务

## 标签体系

Linear中配置了以下标签类型:
- `Feature` (f7986ce2-a610-435a-ae33-73a5d241d987): 新功能开发
- `Improvement` (72431fc3-8d9a-43a8-8511-d6691621f450): 功能改进
- `Bug` (657ed0d6-8d2c-433f-b049-f898f11e9bf9): 缺陷修复

## 管理依赖关系

- Linear支持问题关系(例如'阻塞'、'被阻塞')。
- 在创建或更新问题时,使用描述字段标注依赖关系: "Blocks: #XX" 或 "Blocked by: #XX"。
- 选择任务时确保遵守依赖链。

## Linear中的任务结构(典型字段)

Linear问题通常具有以下可通过MCP工具访问的字段:

- **id**: 问题的唯一标识符。
- **title**: 简短的描述性标题。
- **description**: 任务、需求和上下文的详细说明。
- **stateId (状态)**: 任务的当前状态(例如'Todo'、'In Progress'、'Done')。使用`list_issue_statuses`查找团队可用的状态ID。
- **assigneeId**: 任务的负责人。使用`list_users`或`get_user`获取用户ID。
- **priority**: Linear可能通过数值或标签表示优先级。检查你团队的配置。
- **labels (labelIds)**: 用于分类、类型或优先级的标签。使用`list_issue_labels`获取可用的标签ID。
- **teamId**: 问题所属的团队。
- **projectId**: 如果问题属于特定Linear项目。
- **comments**: 与问题相关的讨论、更新和注释。
- **subIssues/relatedIssues**: 指向其他相关问题的链接。

## Linear MCP配置

```json
{
  "mcpServers": {
    "linear": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.linear.app/sse"]
    }
  }
}
```