# Webhook 接收与付款确认

[返回首页](../../README.zh-CN.md) · [English](../en/webhooks.md)

在 Console 的「开发者设置 → 回调配置」保存公网回调 URL 与 PEM RSA 公钥。配对私钥保存在商户服务器。

## 当前通知格式

请求为 application/json，使用以下标识：

```text
X-JangoPay-Event: payment.completed
X-JangoPay-Encryption-Alg: AES-256-GCM
X-JangoPay-Key-Encryption: RSA-OAEP-SHA256
```

```json
{
  "event_type": "payment.completed",
  "algorithm": "AES-256-GCM",
  "key_algorithm": "RSA-OAEP-SHA256",
  "encrypted_key": "<base64>",
  "nonce": "<base64>",
  "ciphertext": "<base64>"
}
```

RSA 私钥以 OAEP-SHA256 解密 encrypted_key，得到 32 字节 AES 密钥；解码 nonce 和 ciphertext，再以 AES-256-GCM 解密。使用正式 SDK/接入实现确认密文认证标签的处理方式，不能把此格式当作 MD5 或 HMAC 签名。

加密解密成功不应单独作为付款确认依据。结合商户订单映射及服务端收款单/交易查询，核对通知中的 invoice_id、order_id、txn_id、tx_hash、支付资产与金额后执行业务。

## 处理顺序

1. 检查事件类型与加密算法，只处理当前业务需要的事件。
2. 解密并验证完整性，校验业务字段。
3. 查找本地订单与收款单映射，核对 JangoPay 服务端记录。
4. 持久化事件，以事件类型 + invoice_id/payout_id + tx_hash 建立唯一约束。
5. 通过事务或持久化任务确保同一订单只交付一次。混合付款场景可能有多个交易标识，事件去重不能替代订单去重。
6. 成功处理后返回 2xx；失败返回非 2xx，保留可恢复的处理状态。

当前负载没有独立 event_id。官方指南说明失败最多重试 5 次；不在此推测具体重试间隔。

## 业务场景

| 场景 | 完成通知后的操作 |
| :--- | :--- |
| 数字内容 | 确认订单已付后开放对应内容 |
| SaaS / API 额度 | 按订单只增加一次权益或额度 |
| 服务续费 | 只延长一次服务期限 |
| 转出完成 | 核对 payout_id 与转出记录，更新最终状态 |

## 联调

在独立测试配置中验证重复通知、非法密文、未知订单、记录不一致与暂时失败恢复。记录脱敏错误及订单标识，不记录密钥或完整敏感负载。

完整格式、事件示例与密钥生成命令见[官方 Webhook 指南](https://doc.jangopay.org/integration-guide?section=production)。核对日期：2026-10-12。
