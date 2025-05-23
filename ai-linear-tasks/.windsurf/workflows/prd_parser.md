---
description: 解析PRD文档并在Linear中自动创建对应的任务。
---

# PRD Parser Workflow

## Overview
此工作流用于解析PRD文档并在Linear中自动创建对应的任务。

## Workflow Steps

### 1. PRD文档分析
```yaml
type: prompt
description: 分析PRD文档并提取任务信息
context: |
  作为一个专业的项目经理和需求分析专家，请仔细分析提供的PRD文档内容。
  你需要：
  1. 识别主要功能模块和特性
  2. 理解系统架构和技术要求
  3. 确定关键的用户场景和流程
  4. 注意项目约束和风险

  基于分析结果，提取任务信息，每个任务应包含：
  - title: 简短清晰的任务标题
  - description: 详细的任务描述，包括：
    * 功能需求
    * 技术要求
    * 实现指南
    * 验收标准
  - priority: 任务优先级 (High/Medium/Low)
  - dependencies: 依赖的其他任务ID
  - status: 初始状态为Backlog

  任务提取原则：
  1. 每个主要功能点对应一个任务
  2. 任务粒度要适中，既不过大也不过小
  3. 优先级基于业务重要性和技术依赖关系
  4. 任务描述要清晰具体，包含可验证的完成标准
```

### 2. 任务创建准备
```yaml
type: memory
description: 准备Linear任务创建所需的参数映射
context: |
  优先级映射：
  - High -> 1
  - Medium -> 2
  - Low -> 3

  状态映射：
  - Backlog -> 初始状态
  - In Progress -> 开发中
  - In Review -> 评审中
  - Done -> 已完成

  标签映射：
  - Feature -> 新功能
  - Improvement -> 改进
  - Bug -> 缺陷修复
```

### 3. Linear任务创建
```yaml
type: tool
description: 使用Linear MCP工具创建任务
tool: linear_create_issue
params:
  - title
  - description
  - priority
  - teamId
  - projectId
  - labelIds
```

## Best Practices
1. 任务描述使用markdown格式，确保良好的可读性
2. 包含充分的上下文信息和技术细节
3. 明确标注依赖关系和技术要求
4. 设置合理的任务优先级
5. 保持任务粒度的一致性

## Example
输入PRD片段：
```
功能模块：用户认证
- 实现基于JWT的认证机制
- 支持多种登录方式（用户名密码、手机号、社交账号）
- 提供密码重置和账号恢复功能
```

输出任务示例：
```json
{
  "title": "实现JWT认证基础架构",
  "description": "### 功能需求\n- 实现JWT token的生成和验证\n- 设计token结构和过期机制\n- 集成到请求中间件\n\n### 技术要求\n- 使用标准JWT库\n- 实现token刷新机制\n- 确保安全性和性能\n\n### 验收标准\n- JWT生成和验证功能正常\n- 过期token能被正确处理\n- 完整的单元测试覆盖",
  "priority": "high",
  "dependencies": [],
  "status": "backlog"
}
```