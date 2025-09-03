# Google Nano Banana模型深度调研报告

## 执行概要

经过深入调研Google官方文档、New API源码和相关技术资料，现已确定Google "Nano Banana"模型的真实身份和最佳配置方案。

## 🔍 关键发现

### 1. 模型身份确认
- **"Nano Banana"并非Google官方模型名称**
- **真实身份**: Google Gemini 2.5 Flash Image (gemini-2.5-flash-image-preview)
- **功能**: AI图像生成模型，专门用于根据文本提示生成图像

### 2. New API支持情况
✅ **完全支持**: New API已内置Gemini模型支持
- API类型: `APITypeGemini`
- 渠道类型: `ChannelTypeGemini` 
- 适配器: `backend/relay/channel/gemini/adaptor.go`
- 图像生成: 支持`imagen`系列模型

### 3. 技术架构分析
```
前端应用 → New API (统一网关) → Google Gemini API → 图像生成服务
```

## 📋 配置方案建议

### 🎯 强烈推荐：通过New API管理界面配置

#### 优势分析
- ✅ **安全性**: API密钥仅存储在后端，前端无法获取
- ✅ **统一管理**: 支持多用户、权限控制、配额管理
- ✅ **计费透明**: 完整的使用统计和成本控制
- ✅ **高可用**: 内置重试机制、负载均衡
- ✅ **扩展性**: 未来可轻松添加其他AI模型

#### 具体步骤
1. **获取Google API权限**
   - 访问 Google Cloud Console
   - 启用 Vertex AI API 和 Generative Language API
   - 创建服务账号，分配"Vertex AI User"角色
   - 生成API密钥

2. **在New API中配置**
   - 访问 `http://localhost:3000` (New API管理后台)
   - 导航: 系统管理 → 渠道管理 → 新增渠道
   - 选择渠道类型: Gemini
   - 配置模型: `gemini-2.5-flash-image-preview`
   - 设置Base URL: `https://generativelanguage.googleapis.com`
   - 输入API密钥

### ❌ 不推荐：前端直接接入

#### 风险分析
- ❌ **安全风险**: API密钥暴露在前端代码中
- ❌ **管理困难**: 无法统一控制用户权限和配额
- ❌ **成本失控**: 缺乏使用监控和成本控制
- ❌ **维护复杂**: 需要在多处管理API密钥和错误处理

## 💡 实施建议

### 立即行动项
1. **获取Google Cloud API访问权限** (预计1-2小时)
2. **在New API管理界面配置Gemini渠道** (预计30分钟)
3. **前端调用测试** (预计1小时)

### 技术验证清单
- [ ] Google Cloud项目创建和API启用
- [ ] 服务账号创建和权限分配
- [ ] API密钥生成和安全设置
- [ ] New API渠道配置
- [ ] 前端调用接口测试
- [ ] 图像生成功能验证

## 📊 成本预估

### Google Gemini 2.5 Flash Image定价
- **标准生成**: ~$0.01-0.05 per image (1024x1024)
- **高质量生成**: ~$0.05-0.10 per image
- **批量优惠**: 大量使用可申请企业折扣

### 优化建议
1. **请求缓存**: 相同prompt结果缓存，避免重复计费
2. **并发控制**: 限制同时请求数，避免超出配额
3. **预算告警**: 设置月度预算上限和告警机制

## 🛠️ 技术实现

### 前端调用示例
```typescript
const response = await fetch('/api/v1/images/generations', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer USER_TOKEN'
  },
  body: JSON.stringify({
    model: 'gemini-2.5-flash-image-preview',
    prompt: '🍌 生成一个未来风格的香蕉机器人',
    n: 1,
    size: '1024x1024'
  })
});
```

### 环境变量配置
```env
GOOGLE_API_KEY="your_google_api_key_here"
GOOGLE_PROJECT_ID="your_project_id"
NEW_API_BASE_URL="http://localhost:3000"
```

## 📝 文档更新

已完成CLAUDE.md文档更新，新增章节：
- Google Nano Banana模型配置
- 详细配置步骤
- API调用示例
- 成本优化建议
- 故障排除指南

## 🎯 下一步行动

建议立即开始**方案一（New API管理界面配置）**的实施，这是最安全、最可维护的方案。

等待您的确认后，我们可以开始具体的配置操作。