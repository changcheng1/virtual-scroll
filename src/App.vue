<!--
 * @Author: your name
 * @Date: 2020-03-11 16:00:29
 * @LastEditTime: 2020-03-14 14:47:58
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /webNotes/Users/changcheng/Downloads/virtual-scroll/src/App.vue
 -->
<template>
  <div id="app">
    <virtual-list
      :size="size"
      :remain="remain"
      :items="items"
      :variable="variable"
    >
      <item slot-scope="{ item }" :item="item"></item>
    </virtual-list>
  </div>
</template>

<script>
import virtualList from './components/virtual-list'
import item from './components/item'
var Mock = require('mockjs')
let items = []
let total = 200;
for (var i = 0; i < total; i++) {
  items.push({ id: i, value: Mock.Random.cparagraph(1, 4) })
}
console.log('items', items)
export default {
  name: 'App',
  data () {
    return {
      items,
      size: 30,
      remain: 8,
      variable: true
    }
  },
  components: {
    virtualList,
    item
  }
}
</script>
<style lang="scss">
* {
  padding: 0;
  margin: 0;
}
</style>