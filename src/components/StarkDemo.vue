<template>
  <div class="stark">
    <h1>{{ msg }}</h1>
    <div>
      <div >
        <div class="div1">
          <button v-if="display0" @click="connectWalletHandler">
            {{ connectWallet }}
          </button>
          <span v-bind:class="{ active: isActive }"> {{ walletAddress }} </span>
          <button v-if="display" @click="disconnectWalletHandler">
            {{ disConnectWallet }}
          </button>
        </div>
        <form>
        <div class="div2" style="text-align: left;margin-top: 10px;">
          <span style="margin-right: 10px;">{{ blindBox }}</span>
          <input type="number" v-model="blindBoxOpened" :placeholder="blindBoxOpenedInfo1" />
          <br />
          <span style=" margin-left: 40px; font-size: 10px;">{{ info }}</span>
        </div>
        <div class="div3" style="text-align: left;margin-top: 10px;">
          <span style="margin-right: 10px;">{{ blindBoxUri }}</span>
          <input type="text" v-model="blindBoxUrl" :placeholder="blindBoxUrlInfo" />
          <br />
          <span style=" margin-left: 60px; font-size: 10px;">{{ blindBoxUrlPath }}</span>
        </div>
        <div class="div4" style="text-align: left;margin-top: 10px;">
          <span style="margin-right: 10px;">{{ total }}</span>
          <input type="number" v-model="initTotal" :placeholder="totalInfo" />
        </div>
        <div class="div5" style="text-align: left;margin-top: 10px;">
          <span style="margin-right: 10px;">{{ LimitAmount }}</span>
          <input type="number" v-model="amount" :placeholder="LimitAmountInfo" />
        </div>
        <div class="div6" style="text-align: left;margin-top: 10px;">
          <span style="margin-right: 10px;">{{ aiprDrop }}</span>
          <input type="number" v-model="aiprDropAmount" :placeholder="aiprDropInfo" />
        </div>
        <div class="div7" style="text-align: left;margin-top: 10px;">
          <span style="margin-right: 10px;">{{ switchName }}</span>
          <input type="number" v-model="setSwitch" :placeholder="switchInfo" />
          <br />
          <span style=" margin-left: 55px; font-size: 10px;">{{ info }}</span>
        </div>
        <div class="div8" style="text-align: left;margin-top: 10px;">
          <span style="margin-right: 10px;">{{ price }}</span>
          <input type="number" v-model="publicMintPrice" :placeholder="publicMintPriceInfo" />
          <br />
          <span style=" margin-left: 40px; font-size: 10px;">{{ priceInfo }}</span>
        </div>
        <div class="div9" style="text-align: left;margin-top: 10px;">
          <span style="margin-right: 10px;">{{ nftName }}</span>
          <input type="text" v-model="setNftName" :placeholder="nftNameInfo"/>
        </div>
        <div class="div10" style="text-align: left;margin-top: 10px;">
          <span style="margin-right: 10px;">{{ nftSymbol }}</span>
          <input type="text" v-model="setNftSymbol" :placeholder="nftSymbolInfo" />
        </div>
      </form>
        <div class="div11" style="text-align: left;margin-top: 10px;">
          <button @click="deployContract">{{ deploy }}</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// argent钱包提供商推出的sdk,官网文档https://docs.argent.xyz/starknet/web-wallet-sdk/set-up-guide
import { connect, disconnect } from "starknetkit";
// 官方starknet交互库
import {
  cairo,
  stark,
  RpcProvider,
  CallData,
  Contract,
} from "starknet";

const alchemyRPC = "https://starknet-mainnet.g.alchemy.com/v2/bpLKXrBsKZz-_9m2ePjSefISGEW4x7NV";
// 主网
const provider = new RpcProvider({rpcnodeUrl: alchemyRPC});

const nftClassHash = "0x060480e880aa7f169f8197ab92fbb18effd607fbb458c25331f62a780f9de815";

const RouterAddress = "0x033f56d81bca42e6f67bee946d183ed58f694e8d5cacd07bb726e538321886bb";

export default {
  name: "StarkDemo",
  props: {
    msg: String,
  },
  data() {
    return {
      display0: true,
      display: false,
      isActive: false,
      connectWallet: "连接钱包",
      disConnectWallet: "断开钱包",
      deploy: "部署NFT",
      myConnect: null,
      walletAddress: null,
      blindBox: "盲盒",
      blindBoxOpened: null,
      blindBoxOpenedInfo1: "请设置盲盒",
      blindBoxUrlPath: "图片来源路径",
      info: "1 关闭,2 开启",
      total: "总量",
      initTotal: null,
      totalInfo: "请输入NFT总量",
      LimitAmount: "限额",
      amount: null,
      LimitAmountInfo: "请输入个人最多Mint数量",
      aiprDrop: "空投",
      aiprDropAmount: null,
      aiprDropInfo: "请输入空投数量",
      aiprDropInfo2: "免费赠送给用户数量",
      switchName: "白名单",
      setSwitch: null,
      switchInfo: "请设置白名单",
      price: "价格",
      publicMintPrice: null,
      publicMintPriceInfo: "请设置公开Mint价格",
      priceInfo: "以太坊是1000000000000000000",
      blindBoxUri: "盲盒Url",
      blindBoxUrl: null,
      blindBoxUrlInfo: "请设置盲盒的URL",
      nftName: "统称",
      setNftName: null,
      nftNameInfo: "请设置NFT统称",
      nftSymbol: "简称",
      setNftSymbol: null,
      nftSymbolInfo: "请设置NFT简称"
    };
  },
  methods: {
    // 连接钱包
    async connectWalletHandler() {
      const connection = await connect({
        webWalletUrl: "https://web.argent.xyz",
      });

      // 确认钱包是否连接成功
      if (connection && connection.isConnected) {
        this.display0 = false;
        // 钱包地址过长截取一部分显示
        this.walletAddress =
          connection.selectedAddress.slice(0, 12) +
          "..." +
          connection.selectedAddress.slice(-8);
        this.myConnect = connection;
        this.chainId = this.myConnect.chainId;
        this.display = true;
        this.isActive = true;
      }
    },

    // 断开钱包连接
    async disconnectWalletHandler() {
      await disconnect({ webWalletUrl: "https://web.argent.xyz" });
      this.display = false;
      this.isActive = false;
      this.walletAddress = "";
      this.display0 = true;
    },

    // 产生随机数
    myRandom(min = 0, max = 900000000000) {
      let generatedNumbers = new Set();       
      if (max - min + 1 <= generatedNumbers.size) {
            throw new Error('已经生成了所有可能的随机数');
      }
      let randomNum;
      do {
        randomNum = Math.floor(Math.random() * (max - min + 1)) + min;
      } while (generatedNumbers.has(randomNum));
        generatedNumbers.add(randomNum);
        return randomNum;
  },


    // 调用NFT平台部署erc721合约
    async deployContract() {
      
    //获取输入框的数据
    const result = await this.myConnect.account
        .execute([
          {
            contractAddress:
              "0x049D36570D4e46f48e99674bd3fcc84644DdD6b96F7C741B1562B82f9e004dC7",
            entrypoint: "approve",
            // approve 1 wei for NFT平台
            calldata: CallData.compile({
              spender: RouterAddress,
              // 10**14即0.0001 eth
              amount: cairo.uint256(100000000000000)
            }),
          },
          {
            contractAddress: RouterAddress,
            // Router合约的createNft方法名
            entrypoint: "createNft",
            // inputData数据等同于solidity的inputData
            calldata: CallData.compile({
              _erc721Hash: nftClassHash,
              _salt: this.myRandom(),
              _blindBoxOpened: this.blindBoxOpened,
              _total: this.initTotal,
              _mintMaxAmount: this.amount,
              _airDrop: this.aiprDropAmount,
              _switch: this.setSwitch,
              _publicMintPrice: this.publicMintPrice,
              _blindTokenURI: this.blindBoxUrl,
              _name: this.setNftName,
              _symbol: this.setNftSymbol      
            }),
          },
        ])
        .catch(() => location.reload());
      // 等待交易被打包确认,拿到合约的交易收据
      const txReceipt = await provider.waitForTransaction(result.transaction_hash);  
      //根据交易哈希哪个浏览器显示交易数据
      this.openNewWindow(result.transaction_hash);
      //可以通过alchemy的starknet_getTransactionReceipt去获取事件,通过解析事件拿到NFT合约地址
      //接口文档https://docs.alchemy.com/reference/starknet-gettransactionreceipt
    },

    // 封装一个公用的方法
    openNewWindow( tx, time = 3000) {
     
          setTimeout(function fn() {
            window.open(
              `https://voyager.online/tx/${txHash}`,
              "_blank"
            );
          }, time);
    },
  },
  
};
</script>

<style scoped>
.stark h1 + div {
  display: flex;
  justify-content: center;
}
.stark div button:first-child {
  margin-right: 20px;
}
.stark div .active {
  color: black;
}
.stark div button:first-child + button {
  color: white;
  background: red;
  border-style: none;
  border-radius: 4px;
}

.div2,
.div3,
.div4,
.div5 {
  margin-top: 10px;
}

.div1,
.div2,
.div4,
.div5 {
  text-align: left;
}

</style>
