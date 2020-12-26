<template>
  <el-form
    ref="ruleForm"
    label-width="100px"
    class="demo-ruleForm"
    :model="model"
  >
    <el-form-item
      v-for="(item, index) in componentData"
      :key="index"
      :label="item.label"
    >
      <component
        :is="item.component"
        v-bind="item.propValue"
        v-model="model[item.$id]"
      />
    </el-form-item>
  </el-form>
</template>
<script>
import ElSelect from "../widgets/select.vue";
import ElRadio from "../widgets/radio.vue";
export default {
  components: {
    ElSelect,
    ElRadio
  },
  props: {
    componentData: {
      type: Array,
      default: () => []
    },
    model: {
      type: Object,
      default: () => ({})
    }
  }
};
</script>

<style lang="scss" scoped>
.demo-ruleForm {
  flex: 1;
}
</style>

