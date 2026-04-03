# Anspire Search 集成测试总结

## 📋 已创建的测试文件

### 1. 完整单元测试套件
- **文件**: `tests/test_anspire_search.py`
- **类型**: pytest/unittest 单元测试
- **测试类**:
  - `TestAnspireSearchProvider`: Provider 级别测试 (Mock)
  - `TestAnspireSearchService`: Service 级别测试
  - `TestAnspireIntegration`: 真实 API 集成测试

### 2. 快速测试脚本
- **文件**: `test_anspire_quick.py`
- **类型**: 手动验证脚本
- **特点**: 直观输出、实时结果、无需测试框架

### 3. 测试指南文档
- **文件**: `ANSPIRE_TEST_GUIDE.md`
- **内容**: 完整的测试说明、故障排查、最佳实践

---

## 🚀 快速开始

### 方式一：快速测试（推荐首次使用）

```powershell
# Windows PowerShell
$env:ANSPIRE_API_KEYS="your_api_key"
python test_anspire_quick.py
```

### 方式二：完整单元测试

```powershell
# Windows PowerShell
$env:ANSPIRE_API_KEYS="your_api_key"
python -m pytest tests/test_anspire_search.py -v
```

---

## ✅ 测试覆盖范围

### 单元测试（Mock 测试）
- [x] Provider 初始化和名称验证
- [x] Provider 可用性检测
- [x] 域名提取功能
- [x] 成功响应处理
- [x] 无效 API Key 错误处理
- [x] 超时错误处理
- [x] 网络错误处理
- [x] 空结果处理
- [x] 长内容截断功能
- [x] 时间范围参数验证

### 集成测试（真实 API）
- [x] SearchService 正确初始化 Anspire Provider
- [x] Anspire 优先级最高（排在第一位）
- [x] 股票新闻搜索功能
- [x] 通用搜索功能
- [x] 环境变量配置加载

---

## 📊 测试输出示例

### 成功场景
```
✅ 已加载 1 个 Anspire API Key
✅ 搜索服务初始化成功
✅ Anspire Provider 已注册 (优先级：第 1 位)

📊 搜索结果:
   - 状态：✅ 成功
   - 搜索引擎：Anspire
   - 结果数量：3
   - 耗时：1.23s

🎉 Anspire Search 集成测试整体通过!
```

### 失败场景（无 API Key）
```
❌ 错误：未设置 ANSPIRE_API_KEYS 环境变量

请设置环境变量:
  Windows PowerShell:
    $env:ANSPIRE_API_KEYS="your_api_key_here"
```

### 失败场景（无效 Key）
```
📊 搜索结果:
   - 状态：❌ 失败
   - 搜索引擎：Anspire
   - 结果数量：0
   - 错误信息：API KEY 无效：Invalid API key
```

---

## 🔧 环境配置

### 1. 获取 API Key
访问 [Anspire 开放平台](https://open.anspire.cn) 注册并获取

### 2. 设置环境变量（临时）
```powershell
# Windows PowerShell
$env:ANSPIRE_API_KEYS="sk-xxxxx"
```

### 3. 设置环境变量（永久）
**Windows**: 系统属性 → 环境变量 → 用户变量
**Linux/Mac**: 添加到 `~/.bashrc` 或 `~/.zshrc`

### 4. 多个 API Keys
```powershell
$env:ANSPIRE_API_KEYS="key1,key2,key3"
```

---

## 🧪 运行测试的不同方式

### 基础测试（无 API Key）
```bash
# 自动跳过需要 API Key 的测试
python -m unittest tests.test_anspire_search
```

### 完整测试（有 API Key）
```bash
# Windows PowerShell
$env:ANSPIRE_API_KEYS="your_key"
python -m pytest tests/test_anspire_search.py -v

# 或
python -m unittest tests.test_anspire_search -v
```

### 只运行特定测试类
```bash
# 只运行单元测试
python -m unittest tests.test_anspire_search.TestAnspireSearchProvider -v

# 只运行服务测试
python -m unittest tests.test_anspire_search.TestAnspireSearchService -v

# 只运行集成测试（需要 API Key）
python -m unittest tests.test_anspire_search.TestAnspireIntegration -v
```

### 快速验证
```bash
python test_anspire_quick.py
```

---

## 📝 验证清单

运行测试后，确认以下项目:

- [ ] **配置加载**: 日志显示 "已配置 Anspire Search 搜索"
- [ ] **优先级正确**: Anspire Provider 在第一位
- [ ] **API 调用成功**: HTTP 200, success=true
- [ ] **结果格式**: 包含 title, url, snippet, source
- [ ] **错误处理**: 无效 Key 返回明确错误信息
- [ ] **时间范围**: FromTime 和 ToTime 参数正确
- [ ] **内容截断**: 超过 500 字符的内容被截断
- [ ] **域名提取**: 正确从 URL 提取来源域名

---

## 🐛 常见问题

### Q1: 提示 "未设置环境变量"
**解决**: 确保在当前终端会话中设置环境变量

### Q2: 提示 "ModuleNotFoundError: No module named 'requests'"
**解决**: 
```bash
pip install requests
```

### Q3: 提示 "API KEY 无效"
**解决**: 
1. 检查 Key 是否复制完整
2. 登录 Anspire 平台确认 Key 状态
3. 确认账户有足够余额

### Q4: 提示 "请求超时"
**解决**:
1. 检查网络连接
2. 确认能访问 `plugin.anspire.cn`
3. 检查代理/防火墙设置

### Q5: 导入错误
**解决**:
```bash
# 在项目根目录运行
cd E:\vscodeworkspace\daily_stock_analysis

# 或添加 PYTHONPATH
$env:PYTHONPATH="E:\vscodeworkspace\daily_stock_analysis"
```

---

## 📚 相关文档

- [Anspire 官方 API 文档](https://open.anspire.cn/document/docs/searchApi/)
- [详细测试指南](ANSPIRE_TEST_GUIDE.md)
- [搜索服务源码](src/search_service.py)
- [配置管理源码](src/config.py)

---

## 🎯 下一步

1. **运行快速测试**:
   ```bash
   python test_anspire_quick.py
   ```

2. **查看测试结果**: 检查输出中的成功/失败信息

3. **运行完整测试** (可选):
   ```bash
   python -m pytest tests/test_anspire_search.py -v
   ```

4. **集成到 CI/CD** (可选):
   - 将测试添加到项目的 CI 流程
   - 配置 GitHub Actions 自动运行

---

## 📞 获取帮助

如遇到问题:
1. 查看详细测试指南：`ANSPIRE_TEST_GUIDE.md`
2. 检查日志输出的详细错误信息
3. 参考 Anspire 官方文档
4. 查看项目 README 和 Wiki

---

**创建时间**: 2026-04-02  
**测试文件位置**: 
- `tests/test_anspire_search.py` (完整单元测试)
- `test_anspire_quick.py` (快速验证脚本)
