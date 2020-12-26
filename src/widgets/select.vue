<script>
import { Select } from "element-ui";
export default {
  props: {
    enums: {
      type: Array,
      default: () => []
    },
    enumNames: {
      type: Array,
      default: () => []
    },
    ...Select.props
  },
  render() {
    return (
      <el-select
        {...{ props: this.$props }}
        onInput={value => this.$emit("input", value)}
      >
        {this.enums.map((val, index) => {
          return <el-option value={val} label={this.enumNames[index]} />;
        })}
      </el-select>
    );
  }
};
</script>

<style>
</style>
