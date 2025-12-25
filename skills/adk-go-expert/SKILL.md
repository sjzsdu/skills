---
name: adk-go-expert
description: 熟练使用 adk-go 包的智能体开发专家。用于创建、部署和管理 AI 智能体，包括单智能体、多智能体系统和工作流智能体。适用于需要使用 Go 语言开发云原生智能体应用的场景。
---

# ADK-Go 智能体开发专家

## 概述

ADK-Go 是一个开源的、代码优先的 Go 工具包，用于构建、评估和部署复杂的 AI 智能体。本技能提供了熟练使用 adk-go 包开发智能体的专业知识，包括核心概念、API 使用、最佳实践和部署指南。

## 核心功能

### 1. 智能体创建
- 单智能体开发
- 多智能体系统设计
- 工作流智能体编排（顺序、并行、循环）

### 2. 工具生态系统
- 预构建工具使用
- 自定义函数工具开发
- 工具集成与管理

### 3. 模型集成
- Gemini 集成
- 其他模型支持（OpenAI、DeepSeek 等）
- 模型工厂使用

### 4. 部署与运行
- 本地开发与测试
- 容器化部署
- 云原生环境支持（如 Google Cloud Run）

## 快速开始

### 安装 ADK-Go

```bash
go get github.com/sjzsdu/adk-go
```

### 创建简单智能体

```go
package main

import (
	"context"
	"fmt"
	"log"

	adk "github.com/sjzsdu/adk-go"
	"github.com/sjzsdu/adk-go/agent"
	"github.com/sjzsdu/adk-go/model/gemini"
	"github.com/sjzsdu/adk-go/tool/functiontool"
)

func main() {
	// 创建模型实例
	model := gemini.NewModel(gemini.WithModelName("gemini-1.5-flash"))

	// 定义自定义工具
	sayHelloTool := functiontool.NewFunctionTool(
		sayHello,
		"sayHello",
		"向用户打招呼",
		"使用这个工具向用户打招呼并返回问候语",
	)

	// 创建智能体
	myAgent := agent.NewAgent("my-agent", model, agent.WithTools(sayHelloTool))

	// 运行智能体
	err := adk.Run(context.Background(), myAgent)
	if err != nil {
		log.Fatalf("Failed to run agent: %v", err)
	}
}

// 自定义工具函数
func sayHello(ctx context.Context, name string) (string, error) {
	return fmt.Sprintf("Hello, %s!", name), nil
}
```

## 资源

### 核心文档

- **API 参考**: [references/api_reference.md](references/api_reference.md) - 详细的 API 文档
- **代码结构**: 了解 adk-go 包的目录结构和核心组件
- **示例库**: 参考 adk-go/examples 目录下的各种示例

### 最佳实践

- 使用代码优先的方式定义智能体逻辑
- 为智能体设计合适的工具集
- 采用模块化设计构建多智能体系统
- 充分利用 Go 的并发特性
- 遵循云原生设计原则进行部署

### 常见场景

- 构建对话式智能体
- 开发任务自动化智能体
- 设计多智能体协作系统
- 部署云原生智能体应用

## 深入学习

### 智能体类型

1. **LLM 智能体**: 基于大语言模型的核心智能体
2. **远程智能体**: 通过 A2A 协议与其他智能体通信
3. **工作流智能体**: 
   - 顺序智能体
   - 并行智能体
   - 循环智能体

### 工具开发

- **函数工具**: 将 Go 函数转换为智能体可用工具
- **代理工具**: 调用其他智能体作为工具
- **退出循环工具**: 用于控制循环智能体的执行
- **加载资源工具**: 用于加载智能体所需的资源

### 部署选项

- **本地运行**: 使用内置的启动器
- **容器部署**: 构建 Docker 镜像
- **云服务部署**: 部署到 Google Cloud Run 等服务

## 参考资料

- **ADK-Go 源码**: `/Users/juzhongsun/Codes/gos/skills/adk-go`
- **官方文档**: https://google.github.io/adk-docs/
- **示例库**: https://github.com/google/adk-go/tree/main/examples

## 注意事项

- 确保 Go 版本符合要求（Go 1.21+）
- 熟悉 Go 语言的并发编程模型
- 了解云原生开发最佳实践
- 遵循 API 设计原则开发工具

## 故障排除

- 检查模型配置和 API 密钥
- 验证工具定义和参数
- 查看日志输出以定位问题
- 使用调试模式运行智能体

---

**资源目录说明:**

### scripts/
包含辅助脚本，用于智能体开发和部署。

### references/
包含详细的 API 文档和参考资料，供智能体开发时查阅。

### assets/
包含智能体开发所需的资源文件。
