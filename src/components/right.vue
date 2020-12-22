<template>
  <div class="app-right">
    <el-tabs
      v-model="activeName"
      @tab-click="handleClick"
    >
      <el-tab-pane
        label="组件配置"
        name="com"
      >
        <div class="component-name">{{ item.name }}</div>
        <div>
          <div
            v-for="(prop, key) in itemProps"
            :key="key"
          >
            <span>{{ labelMap[key] || key }}:</span>
            <component
              :is="prop.com.component"
              :propValue="prop.com.propValue"
            />
          </div>
        </div>
      </el-tab-pane>
      <el-tab-pane
        label="配置管理"
        name="item"
      ></el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
export default {
  data() {
    return {
      activeName: "com",
      item: {},
      itemProps: {},
      typeMap: {
        Boolean: (val = false) => ({
          component: "ElCheckbox",
          propValue: { value: val }
        }),
        String: (val = "") => ({
          component: "ElInput",
          propValue: { value: val }
        })
      },
      labelMap: {
        value: "值",
        size: "尺寸",
        disabled: "禁用",
        type: "类型",
        resize: "调整大小",
        readonly: "只读",
        autosize: "自动大小",
        label: "自动大小",
        border: "边框",
        autocomplete: "自动填充"
      }
    };
  },
  methods: {
    handleClick(tab, event) {
      console.log(tab, event);
    },
    setItem(item = {}) {
      this.item = item;
      this.itemProps = item.props || {};
      Object.keys(this.itemProps).forEach(key => {
        const pItem = this.itemProps[key];
        const name = pItem.type && pItem.type.name ? pItem.type.name : "String";
        pItem.com = this.typeMap[name](pItem.default);
      });
      console.log("right拿到数据", item, this.itemProps);
    }
  }
};
</script>

<style lang="scss" scoped>
.app-right {
  height: 100%;
  padding: 10px;
  overflow: auto;
}
.component-name {
  font-size: 16px;
  // border-bottom: ;
}
</style>
