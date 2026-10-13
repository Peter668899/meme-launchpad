# Meme Launchpad

Meme 代币发射台：联合曲线定价发行，达到阈值后"毕业"自动创建 DEX 流动性池。

## 核心机制
- **联合曲线（Bonding Curve）**：买/卖价格由 `k = x · y` 恒定乘积曲线推导，早期买家成本低、越晚越贵；合约持有虚拟储备，无需外部做市。
- **发行阈值判定**：募资达 `TARGET_RAISE` 后停止曲线发售，触发毕业流程。
- **毕业上 DEX**：达标后按募资额与预留代币在 Uniswap V2 池注入流动性，LP 通证销毁。
- **Vesting 线性解锁**：团队/早鸟份额按 `start + i * CLIFF` 线性释放，支持领取与转赠。
- **权限与安全**：UUPS 可升级代理 + OpenZeppelin AccessControl 角色分离 + 后端 ECDSA 签名防女巫申购；代币地址由 CREATE2 确定部署。

## 技术难点
- 曲线定价舍入误差处理（买/卖两侧方向不同，防套利）
- 毕业时曲线储备 → DEX 池子的资金/代币精确结算
- 升级权限与发行资格签名校验分离

## 测试
```bash
forge test        # 全部测试
forge test -vvv   # 带调用 trace
forge coverage    # 覆盖率
```
覆盖：重入、权限边界、曲线边界值、毕业失败回滚、Vesting 时间绕过。
