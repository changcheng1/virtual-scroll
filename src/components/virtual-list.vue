<!--
 * @Author: your name
 * @Date: 2020-03-11 16:36:52
 * @LastEditTime: 2020-03-14 14:48:37
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /webNotes/Users/changcheng/Downloads/virtual-scroll/src/components/virtual-list.vue
 -->
<template>
  <div class="viewPort" ref="viewPort" @scroll="virtualScroll">
    <div class="viewScroll" ref="viewScroll"></div>
    <div
      class="viewContainer"
      ref="viewContainer"
      :style="`transform:translateY(${offset}px)`"
    >
      <div
        v-for="item in visibleData"
        :key="item.id"
        :vid="item.id"
        class="item_style"
        ref="nodes"
      >
        <slot :item="item"></slot>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data () {
    return {
      start: 0,
      end: this.remain,
      offset: 0,
      positions: []
    }
  },
  props: {
    size: Number,  //可见每一项的高度
    remain: Number, // 展示的数量
    items: Array,    // 数据
    variable: Boolean  // 是否根据动态高度渲染
  },
  mounted () {
    this.$refs.viewPort.style.height = (this.size * this.remain) + 'px'
    this.$refs.viewScroll.style.height = (this.size * this.items.length) + 'px'
    this.cacheList()
  },
  updated () {
    this.$nextTick(() => {
      var nodes = this.$refs.nodes
      if (nodes.length) {
        nodes.forEach((nodes) => {
          let { height } = nodes.getBoundingClientRect()  // 真实的高度
          let id = nodes.getAttribute('vid');
          let oldHeight = this.positions[id].height;  // 老的高度
          let val = oldHeight - height;  // 计算当前的高度是否和之前的有变化
          if (val) {
            this.positions[id].height = height;
            this.positions[id].bottom = this.positions[id].bottom - val;
            // 链表 将后续所有人都要往后移动
            for (let i = id + 1; i < this.positions.length; i++) {
              this.positions[i].top = this.positions[i - 1].bottom;
              this.positions[i].bottom = this.positions[i].bottom - val
            }
          }
          this.$refs.viewScroll.style.height = this.positions[this.positions.length - 1].bottom + 'px'
        })
      }
    })
  },
  computed: {
    visibleData () {
      let arr = this.items.slice(this.start, this.end)
      return arr
    }
  },
  methods: {
    // 获取当前滑动的哪条数据
    getCurrentIndex (val) {
      let start = 0;    // 初始位置
      let end = this.positions.length - 1 // 当前的最后一个位置
      let temp = null;
      while (start <= end) {
        let middleIndex = parseInt((start + end) / 2)  // 获取中间位置的key
        let middleVal = this.positions[middleIndex].bottom  // 获取中间位置的bottom
        if (val == middleVal) {
          return middleIndex + 1
        } else if (val > middleVal) {  // 在左边
          start = middleIndex + 1
        } else if (val < middleVal) {   // 在右边
          if (temp == null || temp > middleIndex) {
            temp = middleIndex
          }
          end = middleIndex - 1
        }
      }
      return temp
    },
    // 缓存大概的宽高bottom属性
    cacheList () {
      this.positions = this.items.map((item, index) => {
        return {
          height: this.size,
          top: index * this.size,
          bottom: (index + 1) * this.size,
        }
      })
    },
    virtualScroll () {
      // 获取滚动的高度
      let scrollTop = this.$refs.viewPort.scrollTop
      if (this.variable) {
        this.start = this.getCurrentIndex(scrollTop)
        this.end = this.start + this.remain
        this.offset = this.positions[this.start].top ? this.positions[this.start].top : 0
      } else {
        // 算截取的index
        this.start = Math.floor(scrollTop / this.size)
        // 算结束的index
        this.end = this.start + this.remain
        // 位置上移
        this.offset = scrollTop
      }
    }
  },
}
</script>
<style lang="scss">
.viewPort {
  overflow-y: scroll;
  position: relative;
}
.viewScroll {
  position: absolute;
  top: 0;
  left: 0;
}
.item_style {
  padding: 12px;
  border: 1px solid red;
  box-sizing: border-box;
}
</style>