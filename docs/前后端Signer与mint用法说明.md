# 前后端 Signer 与 mint 用法说明

本说明文档用于指导开发者在前后端环境下如何正确传入 Signer，特别是 mint 账户的 signer 用法和注意事项。

---

## 1. Signer 统一接口

SDK 所有需要签名的 API 均支持如下 Signer 接口：
```ts
export interface Signer {
  publicKey: PublicKey;
  signTransaction(tx: Transaction | VersionedTransaction): Promise<Transaction | VersionedTransaction>;
}
```
- 后端：Keypair 实现
- 前端：钱包对象（如 Phantom/Wallet Adapter）实现

---

## 2. mint 账户 signer 用法

### 后端用法
后端可直接用 Keypair 作为 mint signer：
```ts
import { Keypair } from "@solana/web3.js";
const mint = Keypair.generate();
// 直接传入 SDK
sdk.createAndBuy(creator, mint, ...)
```

### 前端用法
前端**不能用钱包对象作为 mint signer**，应如下操作：
1. 前端生成 mint Keypair：
   ```ts
   import { Keypair } from "@solana/web3.js";
   const mint = Keypair.generate();
   ```
2. 用钱包为 mint 账户分配 lamports（Airdrop/Transfer），确保 mint 账户有足够余额创建。
3. 传入 SDK：
   ```ts
   sdk.createAndBuy(walletSigner, mint, ...)
   // walletSigner 是钱包对象，mint 是 Keypair 包装的 Signer
   ```

#### ⚠️ 注意
- mint signer 必须是 Keypair 类型的 Signer，不能用钱包对象充当。
- 钱包对象只能作为用户（creator/buyer/seller）签名者。

---

## 3. 钱包 Signer 示例

以 Phantom/Wallet Adapter 为例：
```ts
const walletSigner = {
  publicKey: wallet.publicKey,
  signTransaction: (tx) => wallet.signTransaction(tx),
};
```

---

## 4. 典型用法对比

| 场景         | 用户 signer         | mint signer         |
|--------------|---------------------|---------------------|
| 后端         | Keypair             | Keypair             |
| 前端         | 钱包对象（Signer）  | Keypair             |

---

如有疑问请查阅 SDK 目标文档或联系维护者。 