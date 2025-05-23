---
description: 创建和维护Cursor规则的指南，以确保一致性和有效性。
globs: .cursor/rules/*.mdc
alwaysApply: true
---

- **必需的规则结构：**
  ```markdown
  ---
  description: 清晰的单行描述，说明规则强制执行的内容
  globs: path/to/files/*.ext, other/path/**/*
  alwaysApply: boolean
  ---

  - **粗体显示的主要要点**
    - 带有详细信息的子要点
    - 示例和说明
  ```

- **文件引用：**
  - 使用 `[filename](mdc:path/to/file)` ([filename](mdc:filename)) 来引用文件
  - 示例：[prisma.mdc](mdc:.cursor/rules/prisma.mdc) 用于规则引用
  - 示例：[schema.prisma](mdc:prisma/schema.prisma) 用于代码引用

- **代码示例：**
  - 使用特定语言的代码块
  ```typescript
  // ✅ 推荐：展示好的示例
  const goodExample = true;
  
  // ❌ 不推荐：展示反模式
  const badExample = false;
  ```

- **规则内容指南：**
  - 从高层次概述开始
  - 包含具体的、可执行的要求
  - 展示正确实现的示例
  - 尽可能引用现有代码
  - 通过引用其他规则保持规则的DRY原则

- **规则维护：**
  - 当出现新模式时更新规则
  - 从实际代码库添加示例
  - 删除过时的模式
  - 交叉引用相关规则

- **最佳实践：**
  - 使用项目符号以提高清晰度
  - 保持描述简洁
  - 包含正反两面的示例
  - 优先使用实际代码而非理论示例
  - 在规则之间保持一致的格式
