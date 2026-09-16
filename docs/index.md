# MatriQ Cloud SDK

MatriQ Cloud 是面向中性原子(Neutral Atom)量子计算任务的云平台。本站点提供 **Python SDK / 第三方集成** 所需的公开接口文档。

## 能力概览

| 能力 | 说明 |
| --- | --- |
| 认证 | JWT 登录流(PAT 个人访问令牌在路线图中) |
| 执行目标查询 | 模拟器/QPU 目录、状态、可提交性、队列深度 |
| 任务提交 | 幂等创建,支持 digital(门电路)与 analog(绝热演化)两类程序 |
| 任务查询 | 列表(分页/筛选)、详情(状态/阶段/进度/等待原因) |
| 任务操作与产物 | 取消、日志、最终结果、结果下载 |

## 快速开始

```python
import time
import uuid
import requests

BASE = "https://<matriq-cloud-host>"   # 平台地址,由服务方提供

# 1. 登录取令牌
tokens = requests.post(f"{BASE}/v1/auth/login",
                       json={"email": "you@example.com", "password": "***"}).json()["data"]
headers = {"Authorization": f"Bearer {tokens['access_token']}"}

# 2. 提交任务(带幂等键)
task = requests.post(f"{BASE}/v1/tasks", headers={
    **headers, "Idempotency-Key": str(uuid.uuid4()),
}, json={
    "name": "bell-state-demo",
    "type": "quantum_task",
    "device_id": "sim-na-01",
    "program": {
        "type": "openqasm3",
        "content": 'OPENQASM 3.0;\nqubit[2] q;\nbit[2] c;\nh q[0];\ncx q[0], q[1];\nc = measure q;',
    },
    "parameters": {"mode": "digital", "shots": 1000},
}).json()["data"]

# 3. 轮询直到终态
while task["status"] not in {"COMPLETED", "FAILED", "CANCELLED"}:
    time.sleep(3)
    task = requests.get(f"{BASE}/v1/tasks/{task['id']}", headers=headers).json()["data"]
    print(task["status"], task["phase"], f"{task['progress']}%")

# 4. 读取结果
if task["status"] == "COMPLETED":
    result = requests.get(f"{BASE}/v1/tasks/{task['id']}/results", headers=headers).json()["data"]
    print(result["output"]["measurements"])
```

## 文档

- **[SDK 接口文档](SDK_API.md)** —— 全部 15 个接口的请求/响应、枚举、状态机、幂等与错误码规范,以及完整的 Python 客户端示例。

## 支持与反馈

通过平台控制台或服务方支持渠道提交问题。
