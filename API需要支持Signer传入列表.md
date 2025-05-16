# pump.fun SDK 需要支持 Signer 传入的 API 列表

本列表梳理了当前 SDK 里所有涉及签名、私钥、Keypair 的关键点，为后续改造为 Signer 适配做准备。

---

## 1. 主要涉及签名/私钥/Keypair 的位置

### A. `src/pumpfun.ts`
- **类方法参数**：`createAndBuy`, `buy`, `sell` 等方法直接接收 `Keypair` 作为参数（如 `creator: Keypair`, `buyer: Keypair`, `seller: Keypair`）。
- **交易签名与发送**：这些方法最终会调用 `sendTx`，并将 `[creator, mint]` 或 `[buyer]` 这样的 Keypair 数组传入，进行签名。
- **getCreateInstructions**：参数 `mint: Keypair`，并在 `.signers([mint])` 里用到。
- 其它方法如 `getBuyInstructions`, `getSellInstructionsByTokenAmount` 等，虽然参数是 `PublicKey`，但实际签名还是在主方法里完成。

### B. `src/util.ts`
- `sendTx` 工具函数，负责实际的交易签名和发送，参数里有 `signers: Keypair[]`。

---

## 2. 其它相关点

- **IDL 及 types**：合约方法如 `create`, `buy`, `sell` 等都要求 `user` 是 signer，但不限定必须是 Keypair，只要能签名即可。
- **其它文件**（如 `globalAccount.ts`, `bondingCurveAccount.ts`, `events.ts`）只涉及数据结构，不涉及签名逻辑。

---

## 3. 需要改造的关键点

1. **所有方法参数 Keypair → Signer**  
   - `createAndBuy`, `buy`, `sell` 等方法的 `creator`, `buyer`, `seller` 参数类型需改为 `Signer`。
   - 相关的 `mint: Keypair` 也要考虑适配（mint 账户创建在前端一般不由钱包签名，但要兼容）。

2. **sendTx 工具函数**  
   - 目前只支持 Keypair[]，需要支持 Signer 或 Wallet Adapter 的签名方式。

3. **getCreateInstructions 等方法**  
   - `.signers([mint])` 需要兼容 Signer。

4. **文档和示例**  
   - 需要同步更新参数类型和用法说明。

---

## 4. 下一步建议

- 详细梳理 `sendTx` 的实现，确定如何抽象签名流程，兼容 Keypair 和钱包签名。
- 列出所有需要改造的方法签名和调用链。
- 设计 Signer 接口和类型定义。 