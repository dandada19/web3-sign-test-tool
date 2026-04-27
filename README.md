# Solana authorityChanges Test Dapp

用于测试 Binance Wallet 扩展 `parse-transaction` 接口 `authorityChanges` 字段的静态单页 dapp。

## 使用

打开部署后的 Pages URL（或本地直接打开 `dapp.html`），连接 Binance Wallet 扩展，选择下列任一 tab 构造交易并触发签名：

- **AccountOwner** — SPL Token `SetAuthority(AccountOwner)`
- **CloseAccount** — SPL Token `SetAuthority(CloseAccount)`
- **System Assign** — System Program `Assign`（改变 account owner program）

签名弹窗中扩展会调用 `parse-transaction` 返回 `authorityChanges`。

## 依赖

- Binance Wallet Chrome 扩展（提供 `window.binancew3w.solana`）
- 浏览器可访问 `https://esm.sh`（加载 `@solana/web3.js`）
- 一个可用的 Solana mainnet RPC URL（页面顶部可改）

## 部署

静态文件，直接丢到任意静态托管即可。当前内部使用 git.toolsfdg.net GHE Pages。
