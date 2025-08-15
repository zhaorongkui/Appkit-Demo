# Reown AppKit Example using Solana (Vite + Vue)

This is a [Vite](https://vitejs.dev) project together with Vue.

## Usage

1. Go to [Reown Cloud](https://cloud.reown.com) and create a new project.
2. Copy your `Project ID`
3. Rename `.env.example` to `.env` and paste your `Project ID` as the value for `VITE_PROJECT_ID`
4. Run `pnpm install` to install dependencies
5. Run `pnpm run dev` to start the development server

## Resources

- [Reown — Docs](https://docs.reown.com)
- [Vite — GitHub](https://github.com/vitejs/vite)
- [Vite — Docs](https://vitejs.dev/guide/)
- [Vue - Docs](https://vuejs.org/guide/introduction)



项目会在 http://localhost:5173 启动，接下来按以下步骤测试：
测试 1：连接钱包
点击页面中的 "连接钱包" 按钮，会弹出 AppKit 连接弹窗。
选择一个钱包（推荐 MetaMask，确保已安装浏览器插件）。
按钱包提示完成授权（连接账户、批准网站访问），连接成功后会显示你的钱包地址。
测试 2：切换网络
点击 "切换网络" 按钮，会弹出网络选择弹窗。
选择列表中的网络（如 Arbitrum 或 Sepolia 测试网），钱包会自动切换到对应网络（需钱包支持该网络）。
测试 3：查询 USDT 余额
确保已连接钱包，且网络切换到你配置的 USDT 合约所在网络（例如 Sepolia 测试网）。
点击 "查询 USDT 余额" 按钮，控制台会输出查询过程，页面会显示你的 USDT 余额（如果测试网没有余额，可以向合约地址转账测试代币）。
常见问题与解决
"Project ID 无效" 错误：确保替换了 YOUR_PROJECT_ID 为 Reown Dashboard 获取的真实 ID，且项目状态为 "激活"。
钱包连接失败：检查浏览器钱包插件是否正常运行，清除缓存后重试；确保 metadata.url 与本地地址 http://localhost:5173 一致。
余额查询失败：
确认 USDT 合约地址与当前网络匹配（主网 / 测试网地址不同）。
检查小数位数是否正确（主网 USDT 用 6 位，测试网可能用 18 位，需对应 formatUnits 的第二个参数）。
确保钱包在正确的网络上（例如查询 Sepolia 测试网合约时，钱包需切换到 Sepolia）。

通过以上步骤，你可以完整演示 AppKit 的核心功能：钱包连接、网络管理和智能合约交互。如果需要进一步测试转账功能，可以基于现有代码扩展 transfer 方法调用逻辑。

