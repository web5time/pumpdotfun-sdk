# pump.fun SDK 浏览器钱包适配改造 TODO

## 一、项目背景与目标

pump.fun 是 Solana 上的流动性协议，本 SDK 原为后端（服务器）用法，直接用明文私钥（Keypair）签名交易。为适配前端浏览器钱包（如 Phantom、Backpack、Solflare 等），需重构 SDK：
- 让前端可用钱包扩展签名和发交易，私钥不暴露。
- 兼容后端 Keypair 用法。
- 统一签名接口，便于维护和扩展。

## 二、已完成的主要工作
1. **需求梳理与调研**：
   - 梳理了所有涉及签名/Keypair 的 API，详见 `API需要支持Signer传入列表.md`。
2. **技术方案设计**：
   - 设计了统一 Signer 接口，兼容 Keypair 和钱包对象。
   - 改造 sendTx 工具函数，支持 Signer。
3. **代码改造**：
   - 所有主流程（如 createAndBuy、buy、sell）已支持 Signer。
   - getCreateInstructions 的 mint 参数已兼容 Signer。
4. **文档与示例**：
   - 在 docs/ 目录下补充了《前后端Signer与mint用法说明.md》，详细说明前后端用法、mint 账户注意事项、典型代码示例。
   - 目标、方案、开发计划详见 `目标.md`。
5. **依赖管理**：
   - 已用 yarn 安装所有依赖（@solana/web3.js, @coral-xyz/anchor, @solana/spl-token, bn.js 等）。

## 三、剩余待办事项（TODO）

### 1. 测试
- [ ] **后端用例回归测试**：
  - 跑通 example/ 和 __tests__/ 目录下的后端用例，确保 Keypair 用法无误。
- [ ] **前端钱包集成测试**：
  - 用 Phantom/Wallet Adapter 等钱包在前端集成 SDK，跑通 createAndBuy、buy、sell 等主流程。
  - 重点测试 mint 账户创建流程（前端需用 Keypair 生成 mint，钱包为其转账）。
- [ ] **兼容性测试**：
  - 多钱包、多环境（不同浏览器/Node 版本）下测试。

### 2. 文档完善
- [ ] **更新 README.md**：
  - 增加 Signer 适配说明、前后端用法示例、常见问题解答。
  - 引导开发者阅读 docs/ 目录下的详细文档。

### 3. 发布与反馈
- [ ] **发布新版本**：
  - 按需 bump 版本号，发布到 npm/yarn。
- [ ] **收集用户反馈**：
  - 关注 issue、用户反馈，持续优化。

## 四、注意事项与常见坑
- **mint 账户 signer**：前端不能用钱包对象作为 mint signer，需用 Keypair 生成并用钱包为其转账。
- **依赖管理**：统一用 yarn，避免混用 npm。
- **类型检查**：开发中多用 `yarn tsc --noEmit` 检查类型问题。
- **多签名场景**：如有多 signer，需依次调用 signTransaction。
- **文档同步**：所有新用法、注意事项务必同步到 docs/ 和 README。

## 五、参考资料
- 目标、方案、开发计划详见 `目标.md`
- API 适配点详见 `API需要支持Signer传入列表.md`
- 前后端用法详见 `docs/前后端Signer与mint用法说明.md`
- Solana 钱包开发：[Solana Wallet Adapter](https://github.com/solana-labs/wallet-adapter)
- Phantom 钱包开发文档：[Phantom Docs](https://docs.phantom.app/integrating/extension-and-in-app-browser-web-apps/using-the-extension)

---
如有疑问请先查阅上述文档，或联系前任开发者/维护者。祝你顺利衔接！ 