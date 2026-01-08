# Requirements Document

## Introduction

重构 Vibe Coding 课程体系，将原有的6阶段结构调整为10阶段，共80节课程。需要创建新的文件夹结构，移动现有文件，创建缺少的空文件。

## Glossary

- **课程文件**：Markdown 格式的课程内容文件
- **阶段文件夹**：按阶段组织的课程文件夹

## Requirements

### Requirement 1: 创建新的文件夹结构

**User Story:** 作为课程管理者，我需要创建10个阶段的文件夹结构

#### Acceptance Criteria

1. THE System SHALL 创建以下文件夹：
   - 第1阶段-认识Vibe Coding世界
   - 第2阶段-开发环境和工具
   - 第3阶段-AI沟通技能
   - 第4阶段-技术栈认知
   - 第5阶段-调试和问题解决
   - 第6阶段-网页实战
   - 第7阶段-桌面软件实战
   - 第8阶段-安卓应用实战
   - 第9阶段-部署上线
   - 第10阶段-进阶内容

### Requirement 2: 创建所有课程文件

**User Story:** 作为课程管理者，我需要为每节课创建对应的 Markdown 文件

#### Acceptance Criteria

1. THE System SHALL 为80节课程创建对应的 .md 文件
2. 文件命名格式：X.Y-课程名称.md
3. 文件内容为占位符：# X.Y 课程名称\n\n（内容待补充）
