<template>
  <div class="contract-section">
    <h2>智能合约交互</h2>
    <p v-if="!isConnected">请先连接钱包 🔗</p>
    <p v-if="isConnected">当前地址：{{ address?.slice(0, 6) }}...{{ address?.slice(-4) }}</p>
    
    <button 
      @click="getUSDTBalance" 
      :disabled="!isConnected"
    >
      🪙 查询 USDT 余额
    </button>
    
    <p v-if="balance">USDT 余额：{{ balance }} USDT</p>
    <p v-if="error" style="color: red;">{{ error }}</p>
  </div>
</template>

<script setup lang="ts">
import { useAppKitAccount } from "@reown/appkit/vue";
import { useAppKitProvider } from "@reown/appkit/vue";
import { BrowserProvider, Contract, formatUnits } from "ethers";
import { ref } from "vue";

// USDT 合约配置（注意：这里用 Sepolia 测试网的 USDT 地址，主网地址需替换）
// 主网 USDT 地址：0xdAC17F958D2ee523a2206206994597C13D831ec7
// Sepolia 测试网 USDT 地址（示例）：0x7163D0460a7E8202F3a228F60d2E9D36A9f64329
const USDTAddress = "0x7163D0460a7E8202F3a228F60d2E9D36A9f64329"; 

// ERC-20 合约 ABI（通用接口）
const USDTAbi = [
  "function name() view returns (string)",
  "function symbol() view returns (string)",
  "function balanceOf(address) view returns (uint)",
  "function transfer(address to, uint amount)",
  "event Transfer(address indexed from, address indexed to, uint amount)",
];

// 获取当前账户信息
const { address, isConnected } = useAppKitAccount();
// 获取钱包提供者（EIP-155 是以太坊兼容链标准）
const { walletProvider } = useAppKitProvider("eip155");

// 状态管理
const balance = ref<string | null>(null);
const error = ref<string | null>(null);

// 查询 USDT 余额函数
async function getUSDTBalance() {
  error.value = null;
  balance.value = null;
  
  try {
    if (!isConnected.value) {
      error.value = "请先连接钱包";
      return;
    }
    if (!walletProvider.value) {
      error.value = "未获取到钱包提供者";
      return;
    }

    // 通过 Ethers 连接钱包
    const ethersProvider = new BrowserProvider(walletProvider.value);
    const signer = await ethersProvider.getSigner(); // 获取签名者（当前连接的钱包）

    // 初始化合约实例
    const USDTContract = new Contract(USDTAddress, USDTAbi, signer);
    
    // 调用合约 balanceOf 方法查询余额
    const USDTBalance = await USDTContract.balanceOf(address.value!);
    
    // 格式化余额（USDT 通常是 6 位小数，测试网可能用 18 位，根据实际合约调整）
    // 主网 USDT 用 6 位：formatUnits(USDTBalance, 6)
    balance.value = formatUnits(USDTBalance, 18); // 测试网示例用 18 位
  } catch (err) {
    error.value = `错误：${err instanceof Error ? err.message : "未知错误"}`;
    console.error("查询余额失败：", err);
  }
}
</script>

<style scoped>
.contract-section {
  margin-top: 30px;
  padding: 20px;
  border: 1px solid #eee;
  border-radius: 8px;
}
</style>