<template>
  <div class="app-right">
    <el-tabs v-model="activeName" @tab-click="handleClick">
      <el-tab-pane label="组件配置" name="com">
        <div>{{ item.name }}</div>
        <div>
          <div v-for="(prop, key) in itemProps" :key="key" >
            <span>{{ key }}: </span>
            <span>{{ prop.type && prop.type.name }}</span>
          </div>
        </div>
      </el-tab-pane>
      <el-tab-pane label="配置管理" name="item"></el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
export default {
  data() {
    return {
      activeName: 'com',
      item: {},
      itemProps: {},
    };
  },
  methods: {
    handleClick(tab, event) {
      console.log(tab, event);
    },
    setItem(item = {}) {
      console.log('right拿到数据',item);
      this.item = item;
      this.itemProps = item.props || {};
    }
  }
}
</script>

<style lang="scss" scoped>
.app-right {
  height: 100%;
  padding: 10px;
}
</style>
