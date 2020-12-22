<template>
  <div
    class="app-left"
    @dragstart="handleDragStart"
  >
    <div class="title">基础组件</div>
    <div
      v-for="(item, index) in componentList"
      :key="index"
      class="buttom-item"
      draggable
      :data-component="item.component"
      @dragend="onDragend"
    >
      <el-button
        size="medium"
        @click="itemClick(item)"
      >{{ item.label }}</el-button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      componentList: [
        {
          label: "输入框",
          component: "ElInput"
        },
        {
          label: "选择器",
          component: "ElSelect"
        },
        {
          label: "单选框",
          component: "ElRadio"
        },
        {
          label: "多选框",
          component: "ElCheckbox"
        },
        {
          label: "日期选择器",
          component: "ElDatePicker"
        }
      ]
    };
  },
  methods: {
    handleDragStart(e) {
      e.dataTransfer.setData("component", e.target.dataset.component);
    },
    onDragend() {
      this.$emit("com-dragend");
    },
    itemClick(item) {
      this.$emit("com-select", item.component);
    }
  }
};
</script>

<style lang="scss" scoped>
.app-left {
  height: 100%;
  padding: 10px;
  overflow: auto;
}
.buttom-item {
  padding-top: 20px;
  text-align: center;
}
</style>
