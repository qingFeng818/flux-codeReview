# Encode Code Review

--基于本地和github代码进行code review

## License

<!-- [MIT](./LICENSE) License © [Encode Studio](https://github.com/qingFeng818) -->

## 特性

- 🤖 **自动代码审查**: 对合并请求和提交进行自动化审查，提供差异分析
- 🧠 **智能反馈**: 通过AI模型提供代码质量评估、最佳实践建议和性能优化建议
- ⚙️ **灵活配置**: 支持多种AI模型和自定义审查规则，管理配置优先级
- 🔄 **持续集成**: 与CI/CD工具集成，实现自动触发和结果通知

### 主要模块

- **CLI入口**: 处理命令行输入和执行相应操作
- **配置管理**: 处理多来源配置的加载和合并
- **平台服务**: 提供与不同代码托管平台的集成
- **AI提供者**: 封装不同AI服务的调用逻辑
- **代码审查核心**: 处理代码差异分析和审查逻辑

## 安装

```bash
# 全局安装
npm install -g encode-code-review

# 或使用pnpm
pnpm add -g encode-code-review

# 或使用yarn
yarn global add encode-code-review
```

## 配置

在项目根目录创建 `.encode_review.yml` 文件：

```yaml
# AI模型配置
ai:
  provider: openai # 或 ollama
  model: deepseek/deepseek-chat-v3-0324:free # 或其他模型
  apiKey: your_openai_key # API密钥可直接配置在文件中
  baseUrl: https://openrouter.ai/api/v1
  temperature: 0.1
  maxTokens: 4000

# 平台配置
platform:
  type: github
  token: YOUR_TOKEN

# 审查配置
review:
  # 忽略文件
  ignoreFiles:
    - '*.lock'
    - '*.min.js'
  # 忽略路径
  ignorePaths:
    - node_modules/
    - dist/
  # 自定义提示
  prompts:
    # 系统提示
    system: |
      你是一个专业的代码审查助手，擅长识别代码中的问题并提供改进建议。
    # 审查提示（支持占位符：{{language}}、{{filePath}}、{{diffContent}}）
    review: |
      请审查以下{{language}}代码...
    # 总结提示（支持占位符：{{filesCount}}、{{issuesCount}}、{{resultsSummary}}）
    summary: |
      请总结以下代码审查结果...
```

你也可以使用环境变量作为替代或补充。

配置优先级: CLI参数 > 环境变量 > 配置文件 > 默认配置

## 使用方法

### CLI命令

```bash
# 审查GitHub拉取请求
encode-code-review github-pr --owner user --repo project --pr-id 123

# 审查本地 diff 代码
encode-code-review local --path ./test_review_file/index.js
```

### GitHub Actions集成

本项目支持通过GitHub Actions自动审查PR代码。将以下内容添加到你的仓库中：

1. 在仓库的Settings > Secrets and variables > Actions中设置以下secrets：

   - `AI_REVIEWER_OPENAI_KEY`: (必需) OpenAI/OpenRouter API密钥
   - `AI_REVIEWER_GITHUB_TOKEN`: (必需) GitHub个人访问令牌
   - `AI_REVIEWER_MODEL`: (可选) 使用的AI模型, 默认: `deepseek/deepseek-chat-v3-0324:free`
   - `AI_REVIEWER_BASE_URL`: (可选) API基础URL, 默认: `https://openrouter.ai/api/v1`
   - `AI_REVIEWER_PROMPT_SYSTEM`: (可选) 自定义系统提示词
   - `AI_REVIEWER_PROMPT_REVIEW`: (可选) 自定义审查提示词
   - `AI_REVIEWER_PROMPT_SUMMARY`: (可选) 自定义总结提示词

注意：

Actions -> General -> Workflow permissions

设置为 Read and  write permissions