<template>
  <div class="editor">
    <div class="qrcode-con">
      <img :src="url" />
    </div>
    <div class="form-group">
      <input class="form-control" :placeholder="placeHolderText" v-model="text" type="text" />
    </div>
    <div class="form-group">
      <button class="btn btn-mini btn-primary" id="button" data-clipboard-action="copy">Copy</button>
    </div>
  </div>
</template>
<script>
const QRCode = require('qrcode');
const Clipboard = require('clipboard');

const getUrlParams = name => {
  var results = new RegExp(`[\\?&]${name}=([^&#]*)`).exec(location.href);
  return results ? results[1] : null;
};

const query = getUrlParams('url');

const defaultUrl = query ? decodeURIComponent(query) : location.href;

export default {
  name: 'editor',
  data() {
    return {
      text: defaultUrl,
      url: defaultUrl,
      placeHolderText: 'please input to generate qrcode',
      pushUrl: defaultUrl
    };
  },

  beforeMount() {
    QRCode.toDataURL(this.text, (err, url) => {
      this.url = url;
      if (err) {
        console.log(err);
      }
    });
  },

  mounted() {
    var clipboard = new Clipboard('#button', {
      text: () => {
        return this.pushUrl;
      }
    });
    clipboard.on('success', function(e) {
      console.log(e);
    });
    clipboard.on('error', function(e) {
      console.log(e);
    });
  },
  watch: {
    text: function(val) {
      this.updateQRCode(val);
    }
  },
  methods: {
    updateQRCode(text) {
      QRCode.toDataURL(text, (err, url) => {
        const str = `?url=${encodeURIComponent(this.text)}`;
        history.replaceState({}, document.title, str);
        this.pushUrl = `${location.protocol}//${location.host}${location.pathname}${str}`;
        this.url = url;
        if (err) {
          console.log(err);
        }
      });
    }
  }
};
</script>
<style lang="less">
.qrcode-con {
  text-align: center;
  margin-top: 100px;
  img {
    min-height: 200px;
    max-height: 500px;
  }
}
.form-group {
  margin-top: 20px;
  text-align: center;
}
.form-control {
  padding: .5rem 1rem;
  font-size: 2.5rem;
  line-height: 3;
  border-radius: .3rem;
  height: 5rem;
}
</style>
