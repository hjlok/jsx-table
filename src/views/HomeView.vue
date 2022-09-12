<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <test />
  </div>
</template>

<script>
import test from '@/components/test'

export default {
  name: 'HomeView',
  components: {
    test
  },
  // created() {
  //   const files = require.context('@/components', true, /\.vue$/)
  //   files.keys().forEach(name => {
  //     if (name=== './test.vue') {
  //       const file = files(name)
  //       console.log('hello wenj jiann', file)
  //     }
  //   })
  // },
  mounted() {
    this.readTestFile()
  },
  methods: {
    // 读取test.properties
    readTestFile() {
      const file = this.loadFile('./vue/test.vue')
      console.info(file)
      // console.log(this.unicodeToUtf8(file))
    },
    // 读取文件
    loadFile(name) {
        const xhr = new XMLHttpRequest()
        const okStatus = document.location.protocol === 'file:' ? 0 : 200
        xhr.open('GET', name, false)
        xhr.overrideMimeType('text/html;charset=utf-8')
        // 默认为utf-8
        xhr.send(null)
        return xhr.status === okStatus ? xhr.responseText : null
    },
    // unicode转utf-8
    // unicodeToUtf8(data) {
    //   data = data.replace(/\\/g, '%')
    //   return unescape(data)
    // }
  }
}
</script>
