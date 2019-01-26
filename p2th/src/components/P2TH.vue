<template>
  <div class="hello">
    <h3>{{ msg }}</h3>Transaction hash:
    <input
      v-bind:class="{ notx: !isSHA }"
      v-model="inputhash"
      size="68"
      placeholder="1bdae84eff34e15399335fc2a48c70fa6b0b9caf972e38b0e3bda106d223f668"
    >
    <h1>{{p2thPeercoinAddress}}</h1>
  </div>
</template>

<script>
var bitcore = require("bitcore-lib");

//
// Set peercoin as default network
//

bitcore.Networks.add({
  name: "peercoin",
  alias: "ppcoin",
  pubkeyhash: 0x37,
  privatekey: 0xb7,
  scripthash: 0x75,
  xpubkey: 0x0488b21e,
  xprivkey: 0x0488ade4
});

bitcore.Networks.add({
  name: "peercoin-testnet",
  alias: "ppcoin-test",
  pubkeyhash: 0x6f,
  privatekey: 0xef,
  scripthash: 0xc4,
  xpubkey: 0x043587cf,
  xprivkey: 0x04358394
});

bitcore.Networks.defaultNetwork = bitcore.Networks.get("peercoin");

export default {
  name: "P2TH",
  props: {
    msg: String
  },
  data() {
    return {
      inputhash: ""
    };
  },
  computed: {
    isSHA: function() {
      return !!this.inputhash
        ? this.inputhash.match(/^[a-f0-9]{64}$/i) !== null
        : false;
    },
    p2thPeercoinAddress: function() {
      if (this.isSHA) {
        const txnid = this.inputhash;

        // Create the compressed address for txnid
        const binaryTxnId = Buffer.from(txnid, "hex");
        const bn = bitcore.crypto.BN.fromBuffer(binaryTxnId);
        const address = new bitcore.PrivateKey(bn).toPublicKey().toAddress();

        return address.toString();
      } else {
        return "";
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
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
.notx {
  border: 2px solid salmon;
}
</style>
