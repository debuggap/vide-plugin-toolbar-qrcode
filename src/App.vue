<template>
  <div class="qrcode" style="z-index:100">
    <input v-model="url" @focus="focusUrl" ref="url" class="url-input" />
    <img src="" class="small" ref="small" />
    <img src="" class="big" ref="big" />
  </div>
</template>

<script>
import localforage from 'localforage'
import QRCode from 'qrcode'

localforage.config({
  driver: localforage.WEBSQL,
  name: 'vide_store',
  version: 1.0,
  size: 49807360,
  storeName: 'vide_store',
  description: 'store'
})
let store = localforage.createInstance({
  name: 'store_instance'
})
let key = 'qrcode_url'
let options = {
  type: 'image/png',
  rendererOpts: {
    quality: 1
  }
}

export default {
  data () {
    return {
      url: ''
    }
  },
  watch: {
    url () {
      this.action()
    }
  },
  mounted () {
    store.getItem(key).then((value) => {
      if (value && value.length) {
        this.url = value
      } else {
        this.url = 'http://www.debuggap.com'
      }
    })
  },
  methods: {
    focusUrl () {
      this.$refs.url.select()
    },
    action () {
      QRCode.toDataURL(this.url, options, (err, url) => {
        if (err) {
          url = ''
        }
        store.setItem(key, this.url)
        this.$refs.big.src = url
        this.$refs.small.src = url
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.qrcode {
  $bottomHeight: 30px;
  $bgColor:rgb(207,50,46);
  $color:rgb(207,50,46);
  
  position:absolute;
  border:1px solid #ccc;
  width: 600px;
  height:400px;
  left: 50%;
  top:50%;
  margin-left: -300px;
  margin-top:-200px;
  background-color:#fff;
  padding:10px;
  box-shadow: 0px 0px 5px 2px #ccc;
  
  .url-input {
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
    background-color: #fff;
    background-image: none;
    border-radius: 4px;
    border: 1px solid #bfc7d9;
    box-sizing: border-box;
    color: #1f283d;
    display: block;
    font-size: inherit;
    height: 36px;
    line-height: 1;
    outline: 0;
    padding: 3px 10px;
    transition: border-color .2s cubic-bezier(.645,.045,.355,1);
    width: 100%;
  }
  
  .small {
    width: 230px;
    vertical-align:middle;
  }
  
  .big {
    width: 350px;
    vertical-align:middle;
  }
}
</style>
