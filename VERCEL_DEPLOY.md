# 🚀 Vercel 部署指南

本文档详细说明如何将 AlphaCouncil AI 系统部署到 Vercel 平台。

## ✅ 部署前检查清单

### 1. 代码安全检查
- ✅ 所有硬编码的 API 密钥已移除
- ✅ `.gitignore` 已配置，`.env` 文件不会被提交
- ✅ 后端 API 使用 `process.env` 读取环境变量
- ✅ 前端支持手动输入 API 密钥作为备选方案

### 2. 必需的环境变量
在 Vercel 项目设置中配置以下环境变量：

| 变量名 | 是否必需 | 说明 |
|--------|---------|------|
| `GEMINI_API_KEY` | ✅ 必需 | Google Gemini API 密钥 |
| `DEEPSEEK_API_KEY` | ✅ 必需 | DeepSeek API 密钥 |
| `JUHE_API_KEY` | ✅ 必需 | 聚合数据 API 密钥 |
| `QWEN_API_KEY` | ⚪ 可选 | 通义千问 API 密钥 |

---

## 📋 部署步骤

### 步骤 1: 准备 GitHub 仓库

1. 确保代码已推送到 GitHub：
   ```bash
   git add .
   git commit -m "feat: 添加 API 密钥管理和 Vercel 部署支持"
   git push origin main
   ```

2. 确认 `.env` 文件未被提交（应该被 `.gitignore` 忽略）

### 步骤 2: 导入到 Vercel

1. 访问 [Vercel Dashboard](https://vercel.com/dashboard)
2. 点击 **"Add New Project"**
3. 选择你的 GitHub 仓库
4. Vercel 会自动检测到 Vite 框架

### 步骤 3: 配置环境变量

在 Vercel 项目配置页面：

1. 进入 **"Settings"** → **"Environment Variables"**
2. 添加以下环境变量：

```
GEMINI_API_KEY=你的_Gemini_API_密钥
DEEPSEEK_API_KEY=你的_DeepSeek_API_密钥
JUHE_API_KEY=你的_聚合数据_API_密钥
QWEN_API_KEY=你的_通义千问_API_密钥（可选）
```

3. 选择环境：
   - ✅ Production
   - ✅ Preview
   - ✅ Development

### 步骤 4: 部署

1. 点击 **"Deploy"** 按钮
2. 等待构建完成（通常需要 2-3 分钟）
3. 部署成功后，Vercel 会提供一个访问链接

---

## 🔍 验证部署

部署完成后，请验证以下功能：

### 1. 前端页面加载
- [ ] 访问 Vercel 提供的 URL
- [ ] 页面正常显示，无 404 错误
- [ ] CSS 样式正常加载

### 2. API 密钥调用
- [ ] 输入股票代码（如 600519）
- [ ] 检查是否能成功获取实时数据
- [ ] 检查 AI 分析是否正常生成

### 3. 错误处理
- [ ] 如果未配置环境变量，应显示友好的错误提示
- [ ] 前端 API 配置面板应正常工作

---

## 🛠️ Vercel Serverless Functions 配置

### API 路由映射

Vercel 会自动将 `api/` 目录下的文件映射为 Serverless Functions：

| 文件路径 | API 端点 | 说明 |
|---------|---------|------|
| `api/ai/gemini.js` | `/api/ai/gemini` | Gemini 模型调用 |
| `api/ai/deepseek.js` | `/api/ai/deepseek` | DeepSeek 模型调用 |
| `api/ai/qwen.js` | `/api/ai/qwen` | 通义千问模型调用 |
| `api/stock/[symbol].js` | `/api/stock/{symbol}` | 股票数据获取 |

### 环境变量访问

Vercel Serverless Functions 中通过 `process.env` 访问环境变量：

```javascript
// ✅ 正确示例
const API_KEY = process.env.GEMINI_API_KEY;

// ❌ 错误示例（硬编码）
const API_KEY = 'AIzaSy...'; 
```

---

## ⚠️ 常见问题排查

### 问题 1: API 密钥无法读取

**症状**: 后端返回 "未配置 API Key" 错误

**解决方案**:
1. 检查 Vercel 环境变量是否正确配置
2. 确保环境变量名称完全匹配（区分大小写）
3. 重新部署项目以应用新的环境变量

### 问题 2: Serverless Function 超时

**症状**: API 请求超过 10 秒后超时

**解决方案**:
- Vercel 免费版 Serverless Function 超时限制为 10 秒
- 考虑升级到 Pro 版本（60 秒超时）
- 优化 AI 模型调用逻辑，减少响应时间

### 问题 3: CORS 错误

**症状**: 浏览器控制台显示跨域错误

**解决方案**:
- 检查后端 API 文件是否设置了正确的 CORS 头：
  ```javascript
  res.setHeader('Access-Control-Allow-Origin', '*');
  res.setHeader('Access-Control-Allow-Methods', 'GET, POST, OPTIONS');
  res.setHeader('Access-Control-Allow-Headers', 'Content-Type');
  ```

### 问题 4: 构建失败

**症状**: Vercel 构建过程中报错

**解决方案**:
1. 检查 `package.json` 中的依赖是否完整
2. 确保 `vite.config.ts` 配置正确
3. 查看 Vercel 构建日志获取详细错误信息

---

## 🔐 安全最佳实践

### 1. 环境变量管理
- ✅ 所有敏感信息通过 Vercel 环境变量配置
- ✅ 不在代码中硬编码任何 API 密钥
- ✅ `.env` 文件仅用于本地开发，不提交到 Git

### 2. API 密钥保护
- ✅ 后端 API 优先使用环境变量
- ✅ 前端输入的密钥仅用于临时会话
- ✅ 定期轮换 API 密钥

### 3. 访问控制
- 考虑添加用户认证（如需要）
- 限制 API 调用频率（Rate Limiting）
- 监控 API 使用量，防止滥用

---

## 📊 监控与优化

### Vercel Analytics

启用 Vercel Analytics 监控：
1. 访问项目设置 → Analytics
2. 启用 Web Analytics
3. 查看页面性能、访问量等数据

### 性能优化建议

1. **启用 Edge Caching**
   - 静态资源自动缓存在 CDN
   
2. **优化 Bundle 大小**
   - 使用动态导入减少初始加载
   
3. **监控 API 响应时间**
   - 设置合理的超时时间
   - 使用 Promise.race 实现快速失败

---

## 🎯 部署后测试清单

- [ ] 首页正常加载
- [ ] 输入框可以输入股票代码
- [ ] API 密钥配置面板可以展开/收起
- [ ] 点击"启动系统"后能获取股票数据
- [ ] 10 个 AI 智能体依次生成分析报告
- [ ] 打字机动画正常显示
- [ ] 图表（柱状图、雷达图）正常渲染
- [ ] 移动端界面正常显示
- [ ] 错误提示友好清晰

---

## 📝 更新部署

当代码有更新时，只需：

```bash
git add .
git commit -m "你的更新说明"
git push origin main
```

Vercel 会自动检测 Git 推送并触发新的部署。

---

## 🆘 获取帮助

如果遇到问题：
1. 查看 [Vercel 文档](https://vercel.com/docs)
2. 检查 Vercel 部署日志
3. 查看浏览器控制台错误信息
4. 参考本项目的 `.env.example` 文件

---

**部署完成！** 🎉

你的 AlphaCouncil AI 系统现在已经运行在 Vercel 的全球 CDN 上，享受超快的访问速度！
