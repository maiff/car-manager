<template>
  <div>
    <div class="title">信息</div>
    <div class="items">
      <div class="item">
        <div class="name">状态:</div>
        <div class="value">{{ status }}</div>
      </div>
      <div class="item">
        <div class="name">距离:</div>
        <div class="value">{{ distance }}</div>
      </div>
      <div class="item">
        <div class="name">纬度:</div>
        <div class="value">{{ lat }}</div>
      </div>
      <div class="item">
        <div class="name">经度:</div>
        <div class="value">{{ lng }}</div>
      </div>
      <!-- <div class="item">
        <div class="name">Node:</div>
        <div class="value">{{ node }}</div>
      </div>
      <div class="item">
        <div class="name">Platform:</div>
        <div class="value">{{ platform }}</div>
      </div> -->
    </div>
  </div>
</template>

<script>
  export default {
    props: ['status', 'distance', 'lat', 'lng'],
    data () {
      return {
        electron: process.versions.electron,
        name: this.$route.name,
        node: process.versions.node,
        path: this.$route.path,
        platform: require('os').platform(),
        vue: require('vue/package.json').version
      }
    }
  }
</script>

<style scoped>
  .title {
    color: #888;
    font-size: 18px;
    font-weight: initial;
    letter-spacing: .25px;
    margin-top: 10px;
  }

  .items { margin-top: 8px; }

  .item {
    display: flex;
    margin-bottom: 6px;
  }

  .item .name {
    color: #6a6a6a;
    margin-right: 6px;
  }

  .item .value {
    color: #35495e;
    font-weight: bold;
  }
</style>
