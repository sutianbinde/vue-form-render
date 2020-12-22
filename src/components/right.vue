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
        <div class="component-name">{{ itemName }}</div>
        <div>
          <!-- 选项设置 -->
          <!-- label设置 -->
          <div
            v-for="(prop, key) in itemProps"
            :key="key"
          >
            <span>{{ labelMap[key] || key }}:</span>
            <component
              :is="prop.component"
              v-model="prop.propValue.value"
              @change="valueChange(key, $event)"
              @input="valueChange(key, $event)"
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
      itemName: "",
      itemProps: {},
      typeMap: {
        Boolean: (val = false) => ({
          component: "ElCheckbox",
          propValue: { value: val }
        }),
        String: () => ({
          component: "ElInput",
          propValue: { value: "" }
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
    setItem(item = {}, defaultProps = {}) {
      console.log("", defaultProps, item);
      this.itemName = item.name;
      const itemProps = { ...item.props };
      Object.keys(itemProps).forEach(key => {
        const pItem = itemProps[key];
        const name = pItem.type && pItem.type.name;
        let defaultValue = defaultProps[key]
          ? defaultProps[key]
          : typeof pItem.default !== "function"
          ? pItem.default
          : undefined;

        const com =
          name && this.typeMap[name]
            ? this.typeMap[name](defaultValue)
            : this.typeMap["String"]();
        Object.assign(pItem, com);
      });
      this.itemProps = itemProps;
    },
    valueChange(key, value) {
      console.log("值改变", key, value);
      this.$emit("change", key, value);
    }
  }
};
</script>

<style lang="scss" scoped>
.app-right {
  height: 100%;
  padding: 10px;
  overflow: auto;
  line-height: 34px;
}
.component-name {
  font-size: 16px;
  margin-bottom: 10px;
  padding-bottom: 5px;
  font-weight: bold;
  border-bottom: 1px solid #d9d9d9;
}
</style>
