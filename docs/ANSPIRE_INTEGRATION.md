# Anspire Search 集成指南

## 概述

Anspire Search 已成功集成到 A 股自选股智能分析系统中，作为**最高优先级**的搜索引擎。

## 功能特性

- ✅ **中文搜索优化**：专为 AI 应用设计的中文搜索引擎
- ✅ **高优先级**：在 Bocha、Tavily 等引擎之前优先使用
- ✅ **多 Key 负载均衡**：支持配置多个 API Key，自动轮询和故障转移
- ✅ **时间范围过滤**：支持 day/week/month/year 时间粒度
- ✅ **AI 摘要支持**：返回结构化的搜索结果和摘要
- ✅ **无缝集成**：与现有搜索服务架构完全兼容

## 快速开始

### 1. 获取 API Key

访问 [Anspire Search](https://aisearch.anspire.cn/) 注册并获取 API Key。

### 2. 配置环境变量

在 `.env` 文件中添加：

```bash
# Anspire Search API Keys（多个 key 用逗号分隔）
ANSPIRE_API_KEYS=your_api_key_1,your_api_key_2,...
```

### 3. 验证配置

运行以下命令验证集成：

```bash
python -c "from src.config import get_config; c = get_config(); print('Anspire keys configured:', len(c.anspire_api_keys))"
```

## 技术实现

### 代码结构

```
src/
├── search_service.py      # 搜索服务核心
│   └── AnspireSearchProvider  # 新增的 Anspire 搜索引擎实现
├── config.py              # 配置管理
│   └── anspire_api_keys   # 新增的配置字段
└── ...
```

### API 端点

- **URL**: `https://api.anspire.cn/v1/search`
- **认证**: `Authorization: Bearer {api_key}`
- **方法**: POST
- **超时**: 10 秒
- **重试**: 3 次（针对网络错误）

### 请求参数

```json
{
  "query": "搜索关键词",
  "freshness": "week",
  "count": 10,
  "summary": true
}
```

### 响应格式

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "webPages": {
      "value": [
        {
          "title": "结果标题",
          "snippet": "摘要内容",
          "url": "https://example.com",
          "siteName": "来源网站",
          "datePublished": "2024-01-01"
        }
      ]
    }
  }
}
```

## 搜索引擎优先级

系统按以下顺序尝试使用搜索引擎：

1. **Anspire** ⭐ (新增，最高优先级)
2. Bocha
3. Tavily
4. Brave Search
5. SerpAPI
6. MiniMax
7. SearXNG

当高优先级引擎不可用时，自动降级到下一个引擎。

## 配置选项

| 环境变量 | 说明 | 示例 | 必填 |
|---------|------|------|------|
| `ANSPIRE_API_KEYS` | Anspire API Key 列表 | `key1,key2,key3` | 推荐 |

## 使用示例

### Python 代码调用

```python
from src.search_service import get_search_service

# 获取搜索服务实例
service = get_search_service()

# 搜索股票新闻
response = service.search_stock_news("600519", "贵州茅台", max_results=5)

# 检查响应
if response.success:
    print(f"搜索成功，来自：{response.provider}")
    print(f"找到 {len(response.results)} 条结果")
    
    for i, result in enumerate(response.results, 1):
        print(f"\n{i}. {result.title}")
        print(f"   来源：{result.source}")
        print(f"   日期：{result.published_date}")
        print(f"   摘要：{result.snippet[:100]}...")
else:
    print(f"搜索失败：{response.error_message}")
```

### 日志输出示例

```
INFO | 已配置 Anspire Search 搜索，共 2 个 API Key
INFO | [Anspire] 搜索 '贵州茅台 最新新闻' 成功，返回 5 条结果，耗时 1.23s
```

## 错误处理

### 常见错误码

| HTTP 状态码 | 含义 | 解决方案 |
|------------|------|----------|
| 401 | API KEY 无效 | 检查 API Key 是否正确配置 |
| 403 | 余额不足或权限不足 | 充值或升级套餐 |
| 400 | 请求参数错误 | 检查请求参数格式 |
| 429 | 请求频率达到限制 | 降低请求频率或增加 Key 数量 |
| 5xx | 服务器错误 | 稍后重试 |

### 故障转移机制

- 每个 API Key 独立跟踪错误计数
- 连续 3 次错误后暂时跳过该 Key
- 自动切换到下一个可用的 Key
- 所有 Key 都失败时降级到其他搜索引擎

## 性能优化

### 缓存策略

- 搜索结果缓存 10 分钟
- 相同查询直接返回缓存结果
- 减少重复 API 调用

### 并发控制

- 多 Key 轮询避免单 Key 限流
- 请求间隔自动控制
- 支持批量查询

## 测试验证

### 单元测试

```bash
# 运行搜索服务测试
python -m pytest tests/test_search_service.py -v
```

### 手动测试

```python
# test_anspire.py
from src.search_service import get_search_service

service = get_search_service()

# 验证 Anspire 被加载
has_anspire = any(p.name == "Anspire" for p in service._providers)
print(f"Anspire 已配置：{has_anspire}")

# 测试实际搜索
response = service.search("A 股市场 今日走势", max_results=3)
print(f"提供商：{response.provider}")
print(f"结果数：{len(response.results)}")
print(f"耗时：{response.search_time:.2f}s")
```

## 监控与日志

### 关键指标

- 搜索成功率
- 平均响应时间
- API Key 错误率
- 故障转移次数

### 日志级别

- **INFO**: 搜索成功、Key 使用情况
- **WARNING**: Key 错误、降级到其他引擎
- **ERROR**: 网络错误、API 异常

## 最佳实践

1. **多 Key 配置**: 建议配置 2-3 个 API Key 提高可用性
2. **监控配额**: 定期检查 API 使用量和余额
3. **错误告警**: 设置错误率阈值告警
4. **性能基线**: 记录正常响应时间作为参考

## 常见问题 FAQ

### Q: Anspire 和其他搜索引擎有什么区别？

A: Anspire 是专为 AI 优化的中文搜索引擎，在中文新闻和市场情报搜索方面表现更好，响应速度更快。

### Q: 如何切换回使用 Bocha 或 Tavily？

A: 只需在 `.env` 文件中移除或注释掉 `ANSPIRE_API_KEYS` 配置即可。

### Q: API Key 有配额限制吗？

A: 是的，请参考 Anspire 官方文档了解具体配额和定价策略。

### Q: 支持自定义搜索参数吗？

A: 当前版本支持基础搜索参数，高级参数（如地域过滤、站点限制等）将在后续版本中添加。

## 更新历史

### v1.0.0 (2024-01)

- ✅ 初始版本发布
- ✅ 基础搜索功能
- ✅ 多 Key 负载均衡
- ✅ 故障转移机制
- ✅ 时间范围过滤

## 联系与支持

- **项目主页**: https://github.com/daily_stock_analysis
- **问题反馈**: 提交 GitHub Issue
- **Anspire 官网**: https://aisearch.anspire.cn/

## 许可证

遵循项目主许可证。
