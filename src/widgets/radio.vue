<script>
import { Radio } from "element-ui";
export default {
  props: {
    enums: {
      type: Array,
      default: () => [],
    },
    enumNames: {
      type: Array,
      default: () => [],
    },
    ...Radio.props,
  },
  render () {
    return (
      <div>
        {
          this.enums.map((val, index) => {
            return <el-radio {...{props: {...this.$props, label:val}}} onInput={(value)=>this.$emit('input',value)}>{ this.enumNames[index] }</el-radio>
          })
        }
      </div>
    );
  }
}
</script>

<style>

</style>
