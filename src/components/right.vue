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
        <div v-show="componentName">
          <div class="component-name">{{ componentName }}</div>
          <div>
            <!-- label设置 -->
            <div>label设置:</div>
            <el-input
              v-model="label"
              @input="itemChange('label', $event)"
            ></el-input>
            <div>$id（表单v-model绑定的Key）</div>
            <el-input
              v-model="itemId"
              @input="itemChange('$id', $event)"
            ></el-input>
            <!-- 选项设置 -->
            <div v-if="this.enumOptions">
              <div>选项字段:</div>
              <el-select
                v-model="enumValue"
                multiple
                filterable
                allow-create
                default-first-option
                placeholder="请选择"
                @change="valueChange('enums', $event)"
              >
                <el-option
                  v-for="item in enumOptions"
                  :key="item"
                  :label="item"
                  :value="item"
                ></el-option>
              </el-select>

              <div>选项名称:</div>
              <el-select
                v-model="enumNameValue"
                multiple
                filterable
                allow-create
                default-first-option
                placeholder="请选择"
                @change="valueChange('enumNames', $event)"
              >
                <el-option
                  v-for="item in enumNameOptions"
                  :key="item"
                  :label="item"
                  :value="item"
                ></el-option>
              </el-select>
            </div>
          </div>
          <div class="prop-items">
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
      // 激活的tab
      activeName: "com",
      // 组件名称
      componentName: "",
      // label
      label: "",
      // $id
      itemId: "",
      // 选项值
      enumValue: [],
      enumOptions: [],
      // 选项显示名称
      enumNameValue: [],
      enumNameOptions: [],
      // props配置项
      itemProps: {},
      // 类型表单生成Map
      typeMap: {
        Boolean: (val = false) => ({
          component: "ElCheckbox",
          propValue: {
            value: val
          }
        }),
        String: () => ({
          component: "ElInput",
          propValue: { value: "" }
        })
      },
      // props的中文对应Map
      labelMap: {
        value: "值",
        size: "尺寸",
        disabled: "禁用",
        type: "类型",
        resize: "调整大小",
        readonly: "只读",
        autosize: "自动大小",
        border: "边框",
        autocomplete: "自动填充"
      }
    };
  },
  methods: {
    handleClick(tab, event) {
      console.log(tab, event);
    },
    /**
     * 对外方法，设置当前项信息
     */
    setItem(item = {}, elementItem = {}) {
      console.log("", elementItem, item);
      this.componentName = elementItem.name;
      this.label = item.label;
      this.itemId = item.$id;
      const defaultProps = item.propValue || {};
      // 从原始组件获取props值，并赋值部分默认值
      const itemProps = { ...elementItem.props };
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

      // 选项
      this.enumOptions = defaultProps.enums;
      this.enumValue = defaultProps.enums;
      this.enumNameOptions = defaultProps.enumNames;
      this.enumNameValue = defaultProps.enumNames;
    },
    /**
     * 改变props相关属性
     */
    valueChange(key, value) {
      this.$emit("change", key, value);
    },
    /**
     * 改变item相关属性
     */
    itemChange(key, value) {
      this.$emit("item-change", key, value);
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
.prop-items {
  // padding-top: 10px;
  margin-top: 10px;
  border-top: 1px solid #d9d9d9;
}
.el-select {
  width: 100%;
}
</style>
