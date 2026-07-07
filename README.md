# Multi-Chain Simulation-Failure Signing Test Tool

用于测试钱包在遇到"交易模拟失败"场景时的展示与拦截行为的单页静态 dapp。

## 覆盖矩阵

11 个测试场景 × 2 种钱包连接方式:

| # | 链   | 失败类型                                | 构造方式 |
|---|------|-----------------------------------------|---|
| 1 | BSC  | insufficient funds for gas * price      | 原生转账 value = balance + 1 native |
| 2 | BSC  | simulation reverted                     | USDT-BSC `transfer(self, 10^30)` |
| 3 | ETH  | insufficient funds for gas * price      | 原生转账 value = balance + 1 ETH |
| 4 | ETH  | simulation reverted                     | ERC20 `transfer(self, 10^30)` |
| 5 | BASE | insufficient funds for gas * price      | 原生转账 value = balance + 1 ETH |
| 6 | BASE | simulation reverted                     | USDC-Base `transfer(self, 10^30)` |
| 7 | SOL  | InsufficientFundsForFee                 | ComputeBudget 优先费 1e12 µLamports/CU × 1.4M CU |
| 8 | SOL  | InsufficientFundsForRent                | `createAccount` space=10MB, lamports=100 |
| 9 | SOL  | simulation reverted (generic)           | `SystemProgram.transfer` 转 1e18 lamports 到 self |
|10 | TRON | insufficient funds                      | `sendTrx` 1e9 TRX 到 self |
|11 | TRON | simulation reverted                     | USDT-TRC20 `transfer(self, 10^30)` |

## 支持的钱包

- **Binance Wallet 扩展** — 直接读 `window.binancew3w.{ethereum, solana, tron}`
- **WalletConnect v2** — 通过 `@walletconnect/universal-provider`,建立 `eip155` + `solana` + `tron` 三个 namespace 的 session

Testnet / Mainnet 由页面顶部下拉切换,默认 **Testnet**。EVM 链会自动通过 `wallet_switchEthereumChain` / `wallet_addEthereumChain` 加/切换到目标网络。

## 使用

1. 打开 `dapp.html`(或部署后的 GitHub Pages URL)
2. 保持顶部 Network = **Testnet**(默认)
3. 选一种连接方式:
   - **Binance Wallet** — 直接点 `Connect Binance Wallet`
   - **WalletConnect** — 在输入框填入自己的 Project ID(从 [cloud.reown.com](https://cloud.reown.com) 免费申请),点 `Connect WalletConnect`,用手机钱包扫码授权
4. 在场景卡上点 `Sign Only`,观察钱包弹窗:
   - **期望**:钱包检测到模拟失败,在弹窗中展示错误详情或直接拒签
   - **异常**:钱包放行(签名成功) → dapp 侧显示为 "Signed",这是需要报告的 QA 问题

所有场景都是"Sign only"路径 — dapp 从不广播交易。即使切到 Mainnet,只要不在钱包侧确认,链上状态不会变。

## Faucet

Testnet 每条链的水龙头链接直接嵌在 Chain Status 卡片里:

- BSC Testnet → `testnet.bnbchain.org/faucet-smart`
- Sepolia → `sepoliafaucet.com`
- Base Sepolia → `alchemy.com/faucets/base-sepolia`
- Solana Devnet → `faucet.solana.com`
- Shasta → `shasta.tronex.io`

## 依赖

通过 `esm.sh` 按需加载,无需 npm/构建:

- `@solana/web3.js@1.95.3` — Solana 交易构造
- `@walletconnect/universal-provider@2.17` — 仅在点击 `Connect WalletConnect` 时懒加载

EVM/TRON 侧完全手写 JSON-RPC / HTTP API,不引入 viem/ethers/tronweb。

## 部署

静态文件,任意静态托管(GitHub Pages / GHE Pages / S3 / nginx …)均可。仓库里 `index.html` 就是一个 6 行的 refresh 重定向到 `dapp.html`。

## 历史

初始 commit `5c9532b` 是一个 Solana 专用的 `SetAuthority` / `Assign` 测试 dapp(用于测试 `parse-transaction` 的 `authorityChanges` 字段)。当前版本重写为多链模拟失败测试,原场景已下线;如需回滚到旧版本,`git checkout 5c9532b -- dapp.html`。
