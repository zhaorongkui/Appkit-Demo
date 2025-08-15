<template>
  <div class="contract-section">
    <h2>智能合约交互</h2>
    <p v-if="!isConnected">请先连接钱包 🔗</p>
    <p v-else>钱包是否连接：{{ isConnected }}</p>
    <p v-if="isConnected">
      当前地址：{{ address?.slice(0, 6) }}...{{ address?.slice(-4) }}
    </p>
    <!-- ------{{JSON.stringfy(walletProvider)}} -->
    <button @click="getUSDTBalance" :disabled="!isConnected">
      🪙 查询 USDT 余额
    </button>

    <p v-if="balance">USDT 余额：{{ balance }} USDT</p>
    <p v-if="error" style="color: red">{{ error }}</p>
  </div>
</template>

<script setup lang="ts">
import { useAppKitAccount } from '@reown/appkit/vue';
import { useAppKitProvider } from '@reown/appkit/vue';
import { BrowserProvider, Contract, formatUnits } from 'ethers';
import { ref, toRefs } from 'vue';



const USDTAddress = '0xdAC17F958D2ee523a2206206994597C13D831ec7'; // 用的主网的地址，（上面真实有ERC20的合约）
// ERC-20 合约 ABI（通用接口）
const USDTAbi = [
  'function name() view returns (string)',
  'function symbol() view returns (string)',
  'function balanceOf(address) view returns (uint)',
  'function transfer(address to, uint amount)',
  'event Transfer(address indexed from, address indexed to, uint amount)'
];
// 获取当前账户信息
const { address, isConnected } = toRefs(useAppKitAccount().value); // 用于访问帐户数据和连接状态的可组合函数。

// 获取以太坊兼容链的provider（eip155是以太坊标准）
const provider = useAppKitProvider('eip155');
// 直接从响应式对象中获取walletProvider（无需toRefs，因为返回的是reactive对象）
const { walletProvider } = toRefs(provider);
// 状态管理
const balance = ref<string | null>(null);
const error = ref<string | null>(null);

// 查询 USDT 余额函数.
async function getUSDTBalance() {
  error.value = null;
  balance.value = null;

  try {
    if (!isConnected.value) {
      error.value = '请先连接钱包';
      return;
    }
    if (!walletProvider.value) {
      error.value = '未获取到钱包提供者';
      return;
    }
    console.log('useAppKitAccount.address:', address.value);
    // 通过 Ethers 连接钱包
    const ethersProvider = new BrowserProvider(walletProvider.value);
    const signer = await ethersProvider.getSigner(); // 获取签名者（当前连接的钱包）

    // 初始化合约实例
    console.log('address', USDTAddress);
    console.log('USDTAbi', USDTAbi);
    console.log('signer', signer);
    const USDTContract = new Contract(USDTAddress, USDTAbi, signer);
    console.log('777777合约打印是：', USDTContract);
    // 检测网络

    // 在getUSDTBalance函数中添加网络检查
    const network = await ethersProvider.getNetwork();
    console.log('当前网络:', network.name, '链ID:', network.chainId);

    // 在获取ethersProvider后添加：
    const contractCode = await ethersProvider.getCode(USDTAddress);
    console.log("合约地址的代码:", contractCode); 

    // 调用合约 balanceOf 方法查询余额
    const USDTBalance = await USDTContract.balanceOf(address.value);
    console.log(444444, USDTBalance);
    // 格式化余额（USDT 通常是 6 位小数，测试网可能用 18 位，根据实际合约调整）
    // 主网 USDT 用 6 位：formatUnits(USDTBalance, 6)
    balance.value = formatUnits(USDTBalance, 18); // 测试网示例用 18 位

  } catch (err) {
    console.log(555555);
    error.value = `错误：${err instanceof Error ? err.message : '未知错误'}`;
    console.error('查询余额失败：', err);
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
