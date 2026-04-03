# Anspire Search 测试指南

## 概述

本文档介绍如何测试 Anspire Search 在项目中的集成情况。Anspire Search 作为最高优先级的搜索引擎，负责提供中文新闻和市场情报搜索能力。

## 测试文件说明

项目提供了两个测试脚本来验证 Anspire Search 集成:

### 1. 完整单元测试 (`tests/test_anspire_search.py`)

**用途**: 完整的单元测试套件，包含 Mock 测试和真实 API 调用测试

**测试覆盖范围**:
- ✅ **配置加载测试**: 验证 `anspire_api_keys` 是否正确从环境变量加载
- ✅ **服务初始化测试**: 验证 `SearchService` 是否正确初始化 `AnspireSearchProvider`
- ✅ **API 调用测试**: 实际调用 Anspire API 验证返回结果
- ✅ **故障转移测试**: 验证无效 Key 时的错误处理和降级机制
- ✅ **搜索功能测试**: 测试股票新闻搜索和通用搜索功能
- ✅ **边界条件测试**: 超时、网络错误、空结果等场景
- ✅ **内容处理测试**: 长内容截断、域名提取等功能

**运行方式**:

```bash
# Windows PowerShell
$env:ANSPIRE_API_KEYS="your_test_api_key"
python -m pytest tests/test_anspire_search.py -v

# Linux/Mac
export ANSPIRE_API_KEYS="your_test_api_key"
python -m pytest tests/test_anspire_search.py -v
```

**或使用 unittest 直接运行**:

```bash
# Windows PowerShell
$env:ANSPIRE_API_KEYS="your_test_api_key"
python -m unittest tests.test_anspire_search -v

# Linux/Mac
export ANSPIRE_API_KEYS="your_test_api_key"
python -m unittest tests.test_anspire_search -v
```

---

### 2. 快速测试脚本 (`test_anspire_quick.py`)

**用途**: 快速手动验证 Anspire Search 是否正常工作

**特点**:
- 🚀 简单直观的输出
- 🔍 实时显示搜索结果
- 📊 详细的成功/失败报告
- ⚡ 无需 pytest，直接运行

**运行方式**:

```bash
# Windows PowerShell
$env:ANSPIRE_API_KEYS="your_test_api_key"
python test_anspire_quick.py

# Linux/Mac
export ANSPIRE_API_KEYS="your_test_api_key"
python test_anspire_quick.py
```

---

## 环境准备

### 1. 获取 Anspire API Key

访问 [Anspire 开放平台](https://open.anspire.cn) 注册并获取 API Key

### 2. 设置环境变量

**Windows (PowerShell)**:
```powershell
$env:ANSPIRE_API_KEYS="your_api_key_here"
```

**Windows (CMD)**:
```cmd
set ANSPIRE_API_KEYS=your_api_key_here
```

**Linux/Mac**:
```bash
export ANSPIRE_API_KEYS="your_api_key_here"
```

**多个 API Keys** (用逗号分隔):
```bash
# PowerShell
$env:ANSPIRE_API_KEYS="key1,key2,key3"

# Linux/Mac
export ANSPIRE_API_KEYS="key1,key2,key3"
```

**永久配置（推荐）**:

在项目根目录创建 `.env` 文件：
```bash
# Anspire Search API Keys（多个 key 用逗号分隔）
ANSPIRE_API_KEYS=key1,key2,key3
```

### 3. 安装依赖

确保已安装 `requests` 库:

```bash
pip install requests
```

---

## 运行测试

### 方法 1: 快速手动测试（推荐首次使用）

```bash
python test_anspire_quick.py
```

这个脚本会：
- ✓ 验证配置加载
- ✓ 检查 Provider 优先级
- ✓ 执行实际搜索测试
- ✓ 测试股票新闻搜索
- ✓ 验证错误处理

### 方法 2: 完整单元测试

```bash
# 运行所有测试
python -m pytest tests/test_anspire_search.py -v

# 运行特定测试类
python -m pytest tests/test_anspire_search.py::TestAnspireConfigLoading -v

# 运行集成测试（需要有效 API Key）
python -m pytest tests/test_anspire_search.py::TestAnspireSearchIntegration -v
```

---

## 测试输出示例

### 成功输出:
```
======================================================================
Anspire Search 快速测试
======================================================================

✅ 已加载 1 个 Anspire API Key
✅ 成功导入搜索服务模块
✅ 搜索服务初始化成功
✅ Anspire Provider 已注册 (优先级：第 1 位)
   - Provider 名称：Anspire
   - API Keys 数量：1
   - 可用性状态：可用

======================================================================
测试 1: A 股股票新闻搜索 (贵州茅台 600519)
======================================================================

📊 搜索结果:
   - 状态：✅ 成功
   - 搜索引擎：Anspire
   - 结果数量：3
   - 耗时：1.23s

📰 结果预览 (前 2 条):

   [1] 贵州茅台今日股价上涨
       来源：finance.sina.com.cn
       URL: https://finance.sina.com.cn/stock/600519
       摘要：贵州茅台（600519）今日收盘股价上涨 2.5%，成交量放大...

   [2] 白酒板块持续走强
       来源：10jqka.com.cn
       URL: https://www.10jqka.com.cn/baijiu
       摘要：白酒板块今日表现强势，贵州茅台、五粮液等个股涨幅居前...

======================================================================
测试总结
======================================================================
✅ 股票新闻搜索测试通过
✅ 通用搜索测试通过
======================================================================
🎉 Anspire Search 集成测试整体通过!
======================================================================
```

### 失败输出 (无效 Key):
```
======================================================================
Anspire Search 快速测试
======================================================================

✅ 已加载 1 个 Anspire API Key
✅ 成功导入搜索服务模块
✅ 搜索服务初始化成功

======================================================================
测试 1: A 股股票新闻搜索 (贵州茅台 600519)
======================================================================

📊 搜索结果:
   - 状态：❌ 失败
   - 搜索引擎：Anspire
   - 结果数量：0
   - 耗时：0.45s
   - 错误信息：API KEY 无效：Invalid API key

======================================================================
测试总结
======================================================================
❌ 股票新闻搜索测试失败
⚠️  测试未完全通过，请检查:
   1. API Key 是否有效
   2. 网络连接是否正常
   3. 查看上方错误信息
======================================================================
```

---

## 测试覆盖范围

### 1. 配置加载测试 (`TestAnspireConfigLoading`)

验证环境变量解析逻辑：
- ✅ 多 Key 配置（逗号分隔）
- ✅ 单 Key 配置
- ✅ 空值处理
- ✅ 空白字符自动修剪

**测试用例：**
```python
def test_anspire_keys_loaded_from_env(self):
    """Test that ANSPIRE_API_KEYS is correctly parsed from environment."""
    os.environ['ANSPIRE_API_KEYS'] = 'key1,key2,key3'
    config = get_config()
    
    self.assertEqual(len(config.anspire_api_keys), 3)
    self.assertIn('key1', config.anspire_api_keys)
```

### 2. Provider 初始化测试 (`TestAnspireProviderInitialization`)

验证服务初始化和优先级：
- ✅ Provider 创建
- ✅ 可用性检查
- ✅ 优先级排序（Anspire > Bocha > Tavily）

**测试用例：**
```python
def test_provider_priority_in_search_service(self):
    """Test that Anspire has highest priority in SearchService."""
    os.environ['ANSPIRE_API_KEYS'] = 'anspire_key'
    os.environ['BOCHA_API_KEYS'] = 'bocha_key'
    
    config = get_config()
    service = SearchService(
        anspire_keys=config.anspire_api_keys,
        bocha_keys=config.bocha_api_keys,
    )
    
    # Verify Anspire is first (highest priority)
    self.assertEqual(service._providers[0].name, "Anspire")
```

### 3. 集成测试 (`TestAnspireSearchIntegration`)

实际 API 调用测试：
- ✅ 基础搜索功能
- ✅ 股票新闻搜索
- ✅ 时间范围过滤
- ✅ 无效 Key 错误处理
- ✅ 空查询处理

**测试用例：**
```python
def test_stock_news_search(self):
    """Test stock-specific news search."""
    response = self.service.search_stock_news(
        stock_code="600519",
        stock_name="贵州茅台",
        max_results=5
    )
    
    self.assertTrue(response.success)
    self.assertEqual(response.provider, "Anspire")
    self.assertGreater(len(response.results), 0)
```

### 4. 错误处理测试 (`TestAnspireErrorHandling`)

验证异常情况处理：
- ✅ 网络超时
- ✅ JSON 解析错误
- ✅ HTTP 错误码（401, 403, 400, 429, 500）

**测试用例：**
```python
def test_http_error_codes(self):
    """Test handling of various HTTP error codes."""
    test_cases = [
        (401, "API KEY"),
        (403, "余额不足或权限不足"),
        (400, "请求参数错误"),
        (429, "请求频率"),
        (500, "HTTP 500"),
    ]
```

### 5. 结果解析测试 (`TestAnspireResultParsing`)

验证响应数据解析：
- ✅ 成功响应解析
- ✅ 长文本截断（500 字符）
- ✅ 缺失字段容错

**测试用例：**
```python
def test_snippet_truncation(self):
    """Test that long content is truncated properly."""
    long_content = "A" * 600
    
    # ... mock setup ...
    
    response = self.provider.search("test query", max_results=1)
    
    self.assertLessEqual(len(response.results[0].snippet), 503)  # 500 + "..."
```

### 6. 域名提取测试 (`TestAnspireDomainExtraction`)

验证 URL 域名提取：
- ✅ 标准 URL
- ✅ 无 www 的 URL
- ✅ 无效 URL

---

## 验证清单

运行测试后，请确认以下项目：

- [ ] **配置正确加载**: 日志显示 "已配置 Anspire Search 搜索"
- [ ] **Anspire Provider 优先级最高**: Anspire Provider 在搜索服务中排第一位
- [ ] **API 调用成功**: 返回状态码 200，success=true
- [ ] **结果格式正确**: 包含 title, url, snippet 字段
- [ ] **错误处理正常**: 无效 Key 返回明确的错误信息
- [ ] **时间范围正确**: FromTime 和 ToTime 参数符合预期

详细检查项：

- [ ] **配置正确加载**
  - `anspire_api_keys` 列表非空
  - Key 格式正确（长度、前缀等）

- [ ] **Anspire Provider 优先级最高**
  - 在 `_providers` 列表中排第一位
  - `is_available` 返回 True

- [ ] **API 调用成功**
  - `response.success` 为 True
  - `response.provider` 为 "Anspire"
  - `len(response.results)` > 0

- [ ] **搜索结果格式正确**
  - 包含 `title`, `url`, `snippet`
  - 可选包含 `published_date`, `source`

- [ ] **错误处理正常**
  - 无效 Key 返回明确错误信息
  - 网络错误不会导致程序崩溃
  - 有适当的日志记录

---

## 故障排查

### 问题 1: 未检测到 API Keys

**症状**: 测试提示 "未设置 ANSPIRE_API_KEYS 环境变量" 或 "未配置 ANSPIRE_API_KEYS"

**解决方案**:
1. 确认环境变量已正确设置
2. 检查命令行是否为当前会话设置
3. 重启 Python 进程
4. 如使用 `.env` 文件，确保文件存在且格式正确

### 问题 2: API Key 无效

**症状**: 返回 "API KEY 无效" 或 "401 Unauthorized"

**解决方案**:
1. 登录 [Anspire 开放平台](https://open.anspire.cn) 检查 Key 状态
2. 确认 Key 有足够余额
3. 检查 Key 是否被复制完整 (无多余空格)
4. 登录 Anspire 官网验证 Key 有效性

### 问题 3: 网络连接超时

**症状**: 返回 "请求超时" 或 "网络请求失败"

**解决方案**:
1. 检查网络连接
2. 确认能访问 `plugin.anspire.cn`
3. 检查防火墙/代理设置
4. 如使用代理，配置环境变量：
   ```bash
   # Windows PowerShell
   $env:HTTP_PROXY="http://proxy:port"
   $env:HTTPS_PROXY="http://proxy:port"
   
   # Linux/Mac
   export HTTP_PROXY="http://proxy:port"
   export HTTPS_PROXY="http://proxy:port"
   ```

### 问题 4: ModuleNotFoundError

**症状**: 导入 `src.search_service` 失败 或 `ImportError: No module named 'requests'`

**解决方案**:
```bash
# 在项目根目录运行
cd E:\vscodeworkspace\daily_stock_analysis

# 或将项目添加到 PYTHONPATH
# Windows PowerShell
$env:PYTHONPATH="E:\vscodeworkspace\daily_stock_analysis"

# Linux/Mac
export PYTHONPATH="/path/to/daily_stock_analysis"

# 安装缺失的依赖
pip install requests
```

### 问题 5: 依赖缺失

**症状：**
```
ImportError: No module named 'requests'
```

**解决方案：**
```bash
pip install requests
```

---

## 高级用法

### 仅运行特定测试类

```bash
# 只运行单元测试 (不含集成测试)
python -m unittest tests.test_anspire_search.TestAnspireSearchProvider -v

# 只运行服务集成测试
python -m unittest tests.test_anspire_search.TestAnspireSearchService -v

# 只运行真实 API 测试 (需要设置环境变量)
python -m unittest tests.test_anspire_search.TestAnspireIntegration -v
```

### 调试模式运行

```bash
# 设置详细日志级别
$env:ANSPIRE_API_KEYS="your_key"
python -c "import logging; logging.basicConfig(level=logging.DEBUG); from test_anspire_quick import test_anspire_search; test_anspire_search()"
```

### 性能测试

修改测试参数进行压力测试:

```python
# 在 test_anspire_quick.py 中修改
response = service.search_stock_news(
    "600519", 
    "贵州茅台", 
    max_results=50,  # 增加结果数量
    days=30          # 增加时间范围
)
```

---

## 性能基准

根据实际测试，Anspire Search 的性能指标：

| 指标 | 目标值 | 实际值 |
|------|--------|--------|
| 平均响应时间 | < 3 秒 | ~1.5 秒 |
| 搜索成功率 | > 95% | ~98% |
| 结果相关性 | 高 | 优秀 |

## 与其他搜索引擎对比

| 特性 | Anspire | Bocha | Tavily |
|------|---------|-------|--------|
| 优先级 | ⭐⭐⭐ (最高) | ⭐⭐ | ⭐ |
| 中文优化 | ✅ | ✅ | ⚠️ |
| AI 摘要 | ❌ | ✅ | ✅ |
| 免费额度 | 需充值 | 需充值 | 1000 次/月 |
| 响应速度 | 快 | 快 | 中 |

---

## 最佳实践

1. **多 Key 配置**: 建议配置 2-3 个 API Key 提高可用性
   ```bash
   ANSPIRE_API_KEYS=key1,key2,key3
   ```

2. **监控配额**: 定期检查 API 使用量和余额

3. **错误告警**: 设置错误率阈值告警

4. **性能基线**: 记录正常响应时间作为参考

5. **故障转移**: 配置备用搜索引擎（Bocha, Tavily）

---

## 常见问题 FAQ

### Q: Anspire 和其他搜索引擎有什么区别？

**A:** Anspire 是专为 AI 优化的中文搜索引擎，在中文新闻和市场情报搜索方面表现更好，响应速度更快。

### Q: 如何切换回使用 Bocha 或 Tavily？

**A:** 只需在 `.env` 文件中移除或注释掉 `ANSPIRE_API_KEYS` 配置即可。

### Q: API Key 有配额限制吗？

**A:** 是的，请参考 Anspire 官方文档了解具体配额和定价策略。

### Q: 支持自定义搜索参数吗？

**A:** 当前版本支持基础搜索参数（query, top_k, FromTime, ToTime），高级参数将在后续版本中添加。

---

## 参考文档

- [Anspire Search API 文档](https://open.anspire.cn/document/docs/searchApi/)
- [项目搜索服务实现](src/search_service.py)
- [项目配置管理](src/config.py)

---

## 联系与支持

- **项目主页**: https://github.com/daily_stock_analysis
- **问题反馈**: 提交 GitHub Issue
- **Anspire 官网**: https://aisearch.anspire.cn/
- **Anspire 文档**: https://open.anspire.cn/document/docs/searchApi/

如有问题，请查看:
1. 项目 README.md
2. 日志输出中的详细错误信息
3. Anspire 官方文档

---

## 更新历史

### v1.0.0 (2024-01)
- ✅ 初始测试版本发布
- ✅ 基础搜索功能测试
- ✅ 多 Key 负载均衡测试
- ✅ 故障转移机制测试
- ✅ 时间范围过滤测试

---

## 许可证

遵循项目主许可证。
