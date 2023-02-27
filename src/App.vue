<template>
  <div class="app-container">
    <div class="test-box">
      <list :list="data" :itemHeight="itemHeight">
        <template #default="{item}">
          <div class="item-box">
            <img :src="item.avatar" alt="">
            <span>{{ item.title }}</span>
          </div>
        </template>
      </list>
    </div>
  </div>
</template>

<script setup lang='ts'>
import List from './components/list.vue'
import {ref, reactive} from 'vue'

interface item {
  id: number,
  title: string,
  avatar: string
}

let arr = new Array(1000).fill({}).map((item, index) => ({
  id: index,
  title: `我是测试标题---${index}`,
  avatar: `https://t11.baidu.com/it/u=2162308300,96045043&fm=58`
}))

let data = reactive<Array<item>>(arr)

let itemHeight = 100

</script>

<style scoped lang="less">
:global(body) {
  margin: 0;
}
.test-box {
  box-sizing: border-box;
  max-width: 750px;
  height: 100vh;
  margin: auto;
}

.item-box {
  height: calc(v-bind(itemHeight) * 1px);
  display: flex;
  align-items: center;
  padding: 0 10px;
  img {
    margin-right: 10px;
    border-radius: 50%;
  }
}
</style>