# Bug 修复记录

## 问题描述

**发现时间**: 2026-04-02  
**发现者**: 用户  
**涉及文件**: `src/search_service.py`, `tests/test_anspire_search.py`

### 问题详情

在 `AnspireSearchProvider._do_search()` 方法中，存在参数传递类型错误：

```python
# ❌ 错误代码（第 853 行）
response = _get_with_retry(url, headers=headers, json=payload, timeout=10)
```

**问题分析**:
1. [_get_with_retry()](file://e:\vscodeworkspace\daily_stock_analysis\src\search_service.py#L69-L73) 函数定义为：
   ```python
   def _get_with_retry(
       url: str, *, headers: Dict[str, str], params: Dict[str, Any], timeout: int
   ) -> requests.Response:
   ```
   - 接受 `params` 参数（查询参数字典）
   - 不接受 `json` 参数

2. **Anspire API 规范**要求使用 **GET 请求**，参数应作为 Query Parameters 传递

3. 实际代码使用了 `json=payload`，导致：
   - 函数调用时出现 `TypeError: _get_with_retry() got an unexpected keyword argument 'json'`
   - 即使函数能调用，也会将参数放在 HTTP body 中而非 URL 查询字符串中

---

## 修复方案

### 代码修复

**文件**: `src/search_service.py` (第 853 行)

```python
# ✅ 修复后代码
response = _get_with_retry(url, headers=headers, params=payload, timeout=10)
```

**修改说明**:
- 将 `json=payload` 改为 `params=payload`
- 符合 GET 请求规范
- 符合 [_get_with_retry()](file://e:\vscodeworkspace\daily_stock_analysis\src\search_service.py#L69-L73) 函数签名

### 测试修复

**文件**: `tests/test_anspire_search.py`

添加了验证逻辑：

```python
# 验证使用 params 而非 json
self.assertIn("params", call_args[1])
self.assertNotIn("json", call_args[1])
```

---

## 影响范围

### 受影响的功能
- ✅ Anspire Search 股票新闻搜索
- ✅ Anspire Search 通用搜索
- ✅ 所有依赖 `AnspireSearchProvider` 的功能

### 修复后的行为
1. **正确的 GET 请求**: 参数作为 URL 查询字符串传递
2. **符合 API 规范**: 遵循 Anspire API 的 GET 方法要求
3. **函数调用正常**: 不再抛出 `TypeError`

---

## 验证方法

### 1. 运行快速测试
```bash
# Windows PowerShell
$env:ANSPIRE_API_KEYS="your_api_key"
python test_anspire_quick.py
```

### 2. 运行单元测试
```bash
# Windows PowerShell
$env:ANSPIRE_API_KEYS="your_api_key"
python -m pytest tests/test_anspire_search.py -v
```

### 3. 手动验证
```python
from src.search_service import get_search_service

service = get_search_service()
response = service.search_stock_news("600519", "贵州茅台", max_results=3)

print(f"状态：{'成功' if response.success else '失败'}")
print(f"结果数量：{len(response.results)}")
```

---

## 经验教训

### 最佳实践
1. **严格遵循 API 文档**: 
   - GET 请求 → 使用 `params`
   - POST 请求 → 使用 `json` 或 `data`

2. **函数签名检查**:
   - 调用前仔细查看函数定义
   - IDE 类型提示很重要

3. **测试覆盖**:
   - Mock 测试要验证参数传递方式
   - 集成测试能发现实际调用问题

4. **代码审查重点**:
   - HTTP 方法（GET/POST）与参数类型的匹配
   - 第三方 API 调用的规范性

---

## 相关文档

- [Anspire API 文档](https://open.anspire.cn/document/docs/searchApi/)
- [Requests 库文档 - GET 参数](https://requests.readthedocs.io/en/latest/user/quickstart/#passing-parameters-in-urls)
- [项目搜索服务实现](src/search_service.py)

---

## 修复确认

- [x] 代码语法检查通过
- [ ] 单元测试全部通过（需要 API Key）
- [ ] 快速测试验证通过（需要 API Key）
- [ ] 集成测试验证通过（需要 API Key）

**修复完成时间**: 2026-04-02  
**修复状态**: ✅ 已修复，待测试验证
