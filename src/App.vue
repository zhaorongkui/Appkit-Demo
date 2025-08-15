
<template>
  <div class="app">
    <h1>AppKit Vue Demo</h1>
    
    <!-- 连接钱包和网络切换按钮 -->
    <div class="buttons">
      <button @click="modal.open()">📦 连接钱包</button>
      <button @click="modal.open({ view: 'Networks' })">🌐 切换网络</button>
    </div>

    <!-- 智能合约交互组件（后续步骤添加） -->
    <ContractInteraction />
  </div>
</template>


<script setup lang="ts">
  import { createAppKit, useAppKit } from "@reown/appkit/vue";
  import { EthersAdapter } from "@reown/appkit-adapter-ethers";
  import { mainnet, arbitrum, sepolia } from "@reown/appkit/networks"; // 可选添加测试网
  // 在 App.vue 的 script 顶部添加
  import ContractInteraction from "./components/ContractInteraction.vue";

  // 1. 替换为你的 Project ID
  const projectId = "8a91047fb3695fde096fc541cc83d22e"; // 例如："clt8x2xxxxxxxxx8qfxxxxxx"

  // 2. 应用元数据（会显示在钱包连接弹窗中）
  const metadata = {
    name: "My AppKit Demo",
    description: "AppKit Vue Demo with Ethers",
    url: "http://localhost:5174", // 本地开发地址，需与浏览器地址一致
    icons: ["https://picsum.photos/200/200"], // 可选：替换为你的图标地址
  };

  // 3. 创建 AppKit 实例（配置钱包适配器、支持的网络）
  createAppKit({
    adapters: [new EthersAdapter()], // 使用 Ethers 适配器
    networks: [mainnet, arbitrum, sepolia], // 支持的网络（主网、Arbitrum、测试网）
    metadata, // 应用元数据（会显示在钱包连接弹窗中）
    projectId, // 项目id
    features: {
      analytics: true, // 可选：启用分析功能
    },
  });

  // 4. 获取模态框控制实例
  const modal = useAppKit();
</script>

<style scoped>
.buttons {
  margin: 20px 0;
  display: flex;
  gap: 10px;
}
button {
  padding: 8px 16px;
  cursor: pointer;
  background: #3b82f6;
  color: white;
  border: none;
  border-radius: 4px;
}
button:hover {
  background: #2563eb;
}
</style>