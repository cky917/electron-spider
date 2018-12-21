<template>
  <div id="wrapper">
    <button
      @click="doSearch"
      :disabled="isSearching">开始爬取</button>
    <p v-if="isSearching">
      爬取中, url: {{url}}
    </p>
    <webview class="search-webview" id="webview" src="" autosize="on" minwidth="1" minheight="1" :preload="fileName"></webview>
    文章列表
    <ul>
      <li v-for="post in posts" :key="post.link">
        <a :href="post.link" target="_blank">{{ post.title }}</a>
      </li>
    </ul>
  </div>
</template>

<script>
import path from 'path'
export default {
  name: 'landing-page',
  data () {
    return {
      isSearching: false,
      url: 'https://juejin.im',
      posts: [],
      webview: null,
      fileName: `file://${path.join(__static, '/doSearch.js')}`
    }
  },
  mounted () {
    this.webview = document.getElementById('webview')
    this.webview.addEventListener('ipc-message', this.receiveMessage)
  },
  methods: {
    getStorageData (key, defaultData = []) {
      const data = window.localStorage.getItem(key)
      return data ? JSON.parse(data) : defaultData
    },
    receiveMessage (e) {
      const result = JSON.parse(e.args[0])
      this.posts = result || []
      this.isSearching = false
    },
    searchHanlder () {
      this.webview.send('ping', 'xxxx')
    },
    /** 开始爬取 */
    doSearch () {
      const { url, isSearching } = this
      if (isSearching) return

      const webview = this.webview
      webview.src = url

      this.isSearching = true
      this.searchUrl = url
      webview.addEventListener('dom-ready', this.searchHanlder)
    }
  }
}
</script>

<style>
.search-webview{
  display: block;
  visibility: hidden;
  height: 0;
}
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body { font-family: 'Source Sans Pro', sans-serif; }
</style>
