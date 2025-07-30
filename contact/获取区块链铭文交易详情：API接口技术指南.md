# 获取区块链铭文交易详情：API接口技术指南

在区块链技术应用中，铭文（Inscription）交易的查询能力对开发者至关重要。本文将系统解析通过交易哈希查询BTC链及分形比特币链铭文交易的API接口，涵盖BRC-20、Runes等主流协议的技术实现细节。

## 接口核心功能解析

该API接口支持以下区块链资产的交易查询：
- BTC主链的Runes、BRC-20、SRC-20、ARC-20协议资产
- 分形比特币链的BRC-20协议资产

👉 [立即获取API接入指南](https://bit.ly/okx_welcome)

## 接口调用规范

### 请求地址
```http
GET https://web3.okx.com/api/v5/wallet/post-transaction/inscription-transaction-detail-by-txhash
```

### 必填参数说明
| 参数名       | 类型   | 描述说明                     |
|------------|--------|----------------------------| 
| chainIndex | String | 区块链唯一标识符（需预先配置） |
| txHash     | String | 待查询的交易哈希值           |
| protocol   | String | 协议类型标识（默认BRC-20）    |

### 可选参数扩展
| 参数名  | 类型   | 默认值 | 最大值 |
|--------|--------|--------|--------|
| cursor | String | 无     | 无     |
| limit  | String | 20     | 100    |

## 响应数据结构详解

### 核心返回字段
```json
{
  "transactionDetails": [
    {
      "txStatus": "success",
      "from": "sender_address",
      "to": "receiver_address",
      "eventType": "mint",
      "protocol": "1",
      "txHash": "transaction_hash",
      "blockHash": "block_hash",
      "height": "block_height",
      "txTime": "timestamp",
      "amount": "transfer_amount",
      "symbol": "token_symbol",
      "tokenInscriptionId": "inscription_id",
      "inscriptionNumber": "inscription_number",
      "outputIndex": "utxo_index"
    }
  ],
  "cursor": "next_cursor"
}
```

### 交易状态映射表
| 状态码 | 含义说明       |
|--------|----------------|
| success| 交易成功       |
| fail   | 交易失败       |

### 协议类型对照
| 协议类型 | 标识值 | 支持交易类型                          |
|----------|--------|---------------------------------------|
| BRC-20   | 1      | 部署/铸造/转账                        |
| ARC-20   | 2      | 资产创建/拆分/合并/分发              |
| Runes    | 3      | 铸造/销毁/转移                        |
| Ordinals | 4      | NFT铸造/转移                          |
| SRC-20   | 5      | 标准代币操作                          |

## 开发实践指南

### 请求示例
```bash
curl -X GET "https://web3.okx.com/api/v5/wallet/post-transaction/inscription-transaction-detail-by-txhash?chainIndex=btc&txHash=abc123&protocol=1"
```

### 响应示例
```json
{
  "transactionDetails": [
    {
      "txStatus": "success",
      "from": "bc1p001...",
      "to": "bc1p002...",
      "eventType": "mint",
      "protocol": "1",
      "txHash": "abc123...",
      "blockHash": "block789...",
      "height": "755001",
      "txTime": "1694521011000",
      "amount": "1000",
      "symbol": "BTC",
      "tokenInscriptionId": "i123456",
      "inscriptionNumber": "123456",
      "outputIndex": "0"
    }
  ]
}
```

👉 [深入学习区块链开发技术](https://bit.ly/okx_welcome)

## 常见问题解答（FAQ）

**Q1：该接口是否支持批量查询？**  
A：目前不支持批量查询，单次请求仅可查询单笔交易详情。建议通过分页参数（limit/cursor）实现多笔数据的分批获取。

**Q2：交易状态显示"fail"时如何处理？**  
A：建议通过区块链浏览器验证交易哈希有效性，同时检查节点同步状态。若需进一步协助，请提供完整交易信息与错误日志。

**Q3：Runes协议的outputIndex字段有何特殊作用？**  
A：该字段用于标识UTXO在交易输出中的索引位置，在Runes协议的Token转移场景中具有关键作用，开发者需特别注意索引值的准确性。

**Q4：如何获取chainIndex参数的正确值？**  
A：chainIndex需根据目标区块链的配置文档获取，例如BTC主链使用"btc"，分形比特币链使用"fbtc"。错误的链标识会导致查询失败。

## 性能优化建议

1. **分页策略**：使用limit参数控制单次返回记录数（建议20-50），配合cursor实现高效分页
2. **缓存机制**：对高频查询的交易哈希建立本地缓存，减少重复网络请求
3. **错误重试**：建议实现指数退避重试机制（3次重试，间隔2^n秒）
4. **异步处理**：在Web应用中采用异步请求方式，避免阻塞主线程

👉 [获取区块链开发最佳实践](https://bit.ly/okx_welcome)

## 安全注意事项

1. **参数校验**：所有请求参数需进行合法性校验，特别注意txHash格式的正确性
2. **权限控制**：通过API密钥机制控制接口访问权限，建议采用IP白名单策略