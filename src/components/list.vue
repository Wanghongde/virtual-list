<template>
  <div class="container-out">
    <div class="container-scroll" ref="containerScrollRef" @scroll="onScroll">
      <div class="empty-box" :style="{height: emptyBoxHeight + 'px'}"></div>
      <div class="virtual-list" :style="translateValueStyle">
        <div class="item" v-for="item in renderList" :key="item.id">
          <slot name="default" :item="item">
            {{ item }}
          </slot>
        </div>
      </div>
    </div>   
  </div>
</template>

<script setup lang='ts'>
import {ref, toRefs, computed} from 'vue'

interface item {
  id: number,
  title: string,
  avatar: string
}

interface props {
  list: Array<item>,
  itemHeight?: number
}

let Props = withDefaults(defineProps<props>(), {
  itemHeight: 120
})

const {list, itemHeight } = toRefs(Props)

const emptyBoxHeight = computed<number>(() => list.value.length * itemHeight.value)

let containerScrollRef = ref<HTMLDivElement>()

let containerScrollRefHeight = computed<number>(() => {
  return containerScrollRef.value ? containerScrollRef.value.clientHeight : 0
})

let pageShowCount = computed(() => {
  return Math.ceil(containerScrollRefHeight.value / itemHeight.value) + 1
})

let start = ref<number>(0)
let end = computed<number>(() => start.value + pageShowCount.value)

const renderList = computed<item[]>(() => {
  let realEnd = Math.min(end.value, list.value.length)

  return list.value.slice(start.value, realEnd)
})

const translateValue = ref<number>(0)

const translateValueStyle = computed(() => {
  console.log(translateValue.value)
  return `transform:translate(0, ${translateValue.value}px)`
})

function onScroll(event:UIEvent) {
  let container = event.target as HTMLDivElement

  if(!container) return 

  const {scrollTop} = container

  start.value = Math.floor(scrollTop / itemHeight.value)
  
  translateValue.value = start.value * itemHeight.value
}

</script>

<style scoped lang="less">
.container-out {
  height: 100%;
  width: 100%;
}

.container-scroll {
  position: relative;
  width: 100%;
  height: 100%;
  overflow: auto;
  .empty-box {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: -1;
  }
  .virtual-list {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;

    .item{
      width: 100%;
      height: calc(v-bind(itemHeight) * 1px);
      box-sizing: border-box;
      border-bottom: 1px solid #ccc;
      overflow: hidden;
      &:last-child{
        border-bottom: 0;
      }
    }
  }
}

</style>