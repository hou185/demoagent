# DemoAgent

## OpenAI 访问指南

本文档提供了关于如何访问和使用 OpenAI API 的详细说明。

### 目录
- [注册 OpenAI 账户](#注册-openai-账户)
- [获取 API 密钥](#获取-api-密钥)
- [设置 API 密钥](#设置-api-密钥)
- [认证方法](#认证方法)
- [使用示例](#使用示例)
- [费率限制与最佳实践](#费率限制与最佳实践)
- [常见问题排除](#常见问题排除)

### 注册 OpenAI 账户

1. 访问 [OpenAI 官方网站](https://openai.com/)
2. 点击右上角的"注册"按钮
3. 填写您的电子邮件地址和密码
4. 验证您的电子邮件地址
5. 完成账户设置过程

### 获取 API 密钥

1. 登录您的 OpenAI 账户
2. 导航至 [API 密钥页面](https://platform.openai.com/api-keys)
3. 点击"创建新密钥"按钮
4. 为密钥添加描述（可选）
5. 复制并安全地存储生成的 API 密钥（请注意：密钥仅显示一次）

### 设置 API 密钥

#### 环境变量方式

在您的开发环境中，将 API 密钥设置为环境变量：

```bash
# Linux/macOS
export OPENAI_API_KEY="your-api-key-here"

# Windows (CMD)
set OPENAI_API_KEY=your-api-key-here

# Windows (PowerShell)
$env:OPENAI_API_KEY="your-api-key-here"
```

#### 配置文件方式

创建一个配置文件（如 `.env` 文件）来存储您的 API 密钥：

```
OPENAI_API_KEY=your-api-key-here
```

确保将此文件添加到 `.gitignore` 以避免将密钥提交到版本控制系统。

### 认证方法

在代码中使用 API 密钥进行认证：

```python
import openai

# 方法一：直接设置
openai.api_key = "your-api-key-here"

# 方法二：从环境变量加载
import os
openai.api_key = os.getenv("OPENAI_API_KEY")
```

### 使用示例

#### 使用 GPT 模型生成文本

```python
import openai
from openai import OpenAI

client = OpenAI()  # 自动从环境变量加载 API 密钥

response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "system", "content": "你是一个有用的助手。"},
        {"role": "user", "content": "请解释什么是人工智能。"}
    ]
)

print(response.choices[0].message.content)
```

#### 使用 DALL-E 生成图像

```python
from openai import OpenAI

client = OpenAI()

response = client.images.generate(
    model="dall-e-3",
    prompt="一只蓝色的猫坐在窗台上看星星",
    n=1,
    size="1024x1024"
)

image_url = response.data[0].url
print(image_url)
```

### 费率限制与最佳实践

#### API 限制

- 免费试用账户通常有每分钟请求次数和每日请求总数的限制
- 付费账户根据账户类型和计划有不同的限制
- 超过限制会导致 API 请求失败，返回 429 错误码

#### 最佳实践

1. **实现重试机制**：使用指数退避策略处理临时错误
2. **缓存响应**：减少重复请求，降低 API 使用成本
3. **批量处理**：尽可能合并多个请求
4. **监控用量**：定期检查 API 使用情况，避免超出预算
5. **设置超时**：为 API 请求设置合理的超时时间
6. **处理敏感信息**：确保不要在提示中包含敏感或私人信息

```python
# 实现重试机制示例
import time
import random
from openai import OpenAI
from openai.error import RateLimitError

client = OpenAI()

def call_api_with_retry(max_retries=5):
    for attempt in range(max_retries):
        try:
            # 你的 API 调用
            response = client.chat.completions.create(
                model="gpt-3.5-turbo",
                messages=[{"role": "user", "content": "Hello!"}]
            )
            return response
        except RateLimitError as e:
            if attempt == max_retries - 1:
                raise e
            # 指数退避策略
            sleep_time = (2 ** attempt) + random.random()
            print(f"Rate limit reached. Retrying in {sleep_time} seconds...")
            time.sleep(sleep_time)
```

### 常见问题排除

#### 认证错误

**问题**：收到 "Authentication Error" 或 "Incorrect API key provided" 错误

**解决方案**：
- 验证 API 密钥是否正确复制，没有多余的空格
- 确认 API 密钥是否过期或被撤销
- 检查是否正确设置了环境变量

#### 费率限制错误

**问题**：收到 "Rate limit exceeded" 错误

**解决方案**：
- 实现重试机制，使用指数退避策略
- 减少 API 调用频率
- 考虑升级您的 API 使用计划

#### 超时错误

**问题**：API 请求超时

**解决方案**：
- 检查网络连接
- 减小请求规模或复杂度
- 增加客户端超时设置

#### 内容过滤

**问题**：收到 "Content filtered" 错误

**解决方案**：
- 修改您的提示，避免潜在的有害或不适当内容
- 确保您的请求符合 OpenAI 的使用政策

#### 模型不可用

**问题**：指定的模型不可用

**解决方案**：
- 确认您使用的是正确的模型名称
- 检查该模型是否对您的账户可用
- 尝试使用替代模型