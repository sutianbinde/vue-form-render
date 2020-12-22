<template>
  <el-form
    ref="ruleForm"
    label-width="100px"
    class="demo-ruleForm"
  >
    <el-form-item
      v-for="(item, index) in componentData"
      :key="index"
      :label="item.label"
    >
      <component
        :is="item.component"
        v-bind="item.propValue"
        v-model="item.propValue.value"
      />
    </el-form-item>
  </el-form>
</template>

<script>
export default {
  props: {
    componentData: {
      type: Array,
      default: () => []
    }
  }
};
</script>

<style lang="scss" scoped>
.demo-ruleForm {
  flex: 1;
}
</style>

