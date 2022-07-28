<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>ntf0 minted: {{ nft0 }}</p>
    <p>ntf1 minted: {{ nft1 }}</p>
    <p>ntf2 minted: {{ nft2 }}</p>
    <p>{{ msg }}</p>
    <p>
      <button @click="connect"> {{ connectText }} </button>
      <button @click="mint(0)"> mint NFT 0 </button>
      <button @click="mint(1)"> mint NFT 1 </button>
      <button @click="mint(2)"> mint NFT 2 </button>

    </p>
  </div>
</template>

<script>
import Web3 from 'web3';
import ERC20 from '../js/ERC20ABI';
import NFT from '../js/NFTABI';
const NFTADDRESS = "0x44c1439d1598a7f117f7ea617188ef568689c4ba"

export default {
  name: "WalletTest",
  props: {
    msg: String,
    
  },
  data() {
    return {
        connected: false,
        address: "",
        connectText: "Wallet Connect",
        message: 'Hello World!',
        nft0: 0,
        nft1: 0,
        nft2: 0,
        erc20: null,
        nft: null,
    }
  },
  methods: {
    async connect () {
        if(this.address) {
            return
        }
        if(!this.connected) {
            const ok = await this.ethEnabled();
            if (!ok) {
                console.log("do not support wallet")
                return
            }
            
        }
        const accounts = await window.web3.eth.getAccounts();
        this.address = accounts[0];
        this.connectText = this.address;
          window.ethereum.on("chainChanged", (networkId) => {
            console.log("chainChanged", networkId);
          });
          window.ethereum.on("accountsChanged", (accounts) => {
            console.log("accountsChanged", accounts);
            this.address = accounts[0];
            this.connectText = this.address;
          });
        
        
    },
    async mint(tokenId) {
       const approve =  await this.erc20.methods.allowance(this.address, NFTADDRESS).call()
       const metaInfo = await this.nft.methods.meta(tokenId).call()
       if(metaInfo.amount > approve) {
            await this.erc20.methods.approve(NFTADDRESS, metaInfo.amount).send()
       }
       await this.nft.methods.mint(tokenId, metaInfo.amount).send({from: this.address})
       this.updateContractMinted()

    },

    async ethEnabled () {
        if (window.ethereum) {
            await window.ethereum.request({method: 'eth_requestAccounts'});
            window.web3 = new Web3(window.ethereum);
            this.connected = true
            this.erc20 = new window.web3.eth.Contract(ERC20, "0xBb7368f926D479732F5Ec2F0e67d463743ED7cB3")
            this.nft = new window.web3.eth.Contract(NFT, NFTADDRESS)
            this.updateContractMinted()
            return true;
        }
        return false;
    },

    async updateContractMinted() {
        this.nft0 = await this.nft.methods.totalSupply(0).call()
        this.nft1 = await this.nft.methods.totalSupply(1).call()
        this.nft2 = await this.nft.methods.totalSupply(2).call()

    }
   
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
