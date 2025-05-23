# 项目集成说明

## 概述

本项目整合了两个AI辅助开发工具：

1. **claude-task-master** - 原有的AI任务管理系统
2. **AI-Linear-tasks** - 基于Linear和Cursor/Windsurf的新一代AI开发工作流

## 项目结构

```
claude-task-master/                 # 主项目（claude-task-master）
├── .cursor/rules/                  # Cursor规则配置
├── mcp-server/                     # MCP服务器
├── scripts/                        # 核心脚本
├── tasks/                          # 任务文件
├── ai-linear-tasks/                # AI-Linear-tasks子项目
│   ├── README.md                   # AI-Linear-tasks项目说明
│   ├── .cursor/rules/              # AI-Linear-tasks的Cursor规则
│   ├── .windsurf/                  # Windsurf配置
│   ├── .trae/                      # Trae配置
│   └── docs/                       # 项目文档
└── INTEGRATION_README.md           # 本文档
```

## 使用方式

### 方式1：使用claude-task-master（传统方式）

适用于需要本地任务管理和MCP服务的场景：

```bash
# 初始化项目
npm install -g task-master-ai
task-master init

# 使用MCP服务
# 配置.cursor/mcp.json指向本地MCP服务器
```

### 方式2：使用AI-Linear-tasks（推荐）

适用于与Linear集成的现代AI开发工作流：

```bash
# 进入AI-Linear-tasks目录
cd ai-linear-tasks

# 配置Linear MCP服务
# 参考ai-linear-tasks/README.md中的详细说明
```

## 核心差异

| 特性 | claude-task-master | AI-Linear-tasks |
|------|-------------------|-----------------|
| 任务存储 | 本地tasks.json | Linear云端 |
| AI调用 | 外部LLM API | Cursor/Windsurf内置 |
| 集成方式 | MCP服务器 | Linear MCP客户端 |
| 需求处理 | 直接解析PRD | 交互式需求澄清 |
| 适用场景 | 独立开发 | 团队协作 |

## 迁移建议

1. **新项目**：建议直接使用AI-Linear-tasks工作流
2. **现有项目**：可以继续使用claude-task-master，或逐步迁移到Linear
3. **混合使用**：可以在同一项目中同时配置两套规则，根据需要选择

## 配置说明

### Cursor配置

- 主项目的`.cursor/rules/`包含claude-task-master的规则
- `ai-linear-tasks/.cursor/rules/`包含AI-Linear-tasks的规则
- 可以根据需要在Cursor中切换或合并规则

### MCP配置

- claude-task-master：配置本地MCP服务器
- AI-Linear-tasks：配置Linear MCP客户端

## 更新日期

2025年1月27日 - 初始集成版本 