<template>
  <div class="app-middle">
    <div class="actions">
      <el-button
        size="medium"
        v-if="!editing"
        @click="editing=true"
      >开始编辑</el-button>
      <el-button
        size="medium"
        v-if="editing"
        @click="showDemo"
      >最终展示</el-button>
      <el-button
        size="medium"
        @click="clear"
      >清空</el-button>
      <el-button
        size="medium"
        type="primary"
        @click="showSchema = true"
      >查看schema</el-button>
    </div>
    <div
      v-if="!editing"
      class="playground-demo"
    >
      <render-components
        :componentData="demoSchema.schema"
        :model="demoSchema.model"
      ></render-components>
    </div>
    <div
      v-show="editing"
      class="playground"
      :class="{border: showBorder}"
      @drop="handleDrop"
      @dragover="handleDragOver"
    >
      <el-form label-width="100px">
        <div
          class="field-wrapper"
          :class="{active: item===activeItem}"
          v-for="(item, index) in componentData"
          :key="item.$id"
        >
          <el-form-item
            :label="item.label"
            @click.native="selectCurComponent($event, item)"
          >
            <component
              :is="item.component"
              v-bind="item.propValue"
              v-model="item.propValue.value"
            />
          </el-form-item>
          <el-button-group class="item-actions">
            <el-button
              type="primary"
              size="mini"
              class="position-handler"
              icon="el-icon-position"
            ></el-button>
            <el-button
              size="mini"
              @click="copyItem(item, index)"
              icon="el-icon-document-copy"
            ></el-button>
            <el-button
              size="mini"
              @click="deleteItem(item, index)"
              icon="el-icon-delete"
            ></el-button>
          </el-button-group>
        </div>
      </el-form>
    </div>
    <!-- 弹窗 -->
    <el-dialog
      title="schema"
      :visible.sync="showSchema"
      width="60%"
    >
      <pre
        ref="schema"
        class="schema-display"
        contenteditable="true"
      >{{ schema }}</pre>
      <!-- 隐藏input用于复制 -->
      <textarea
        class="schema-input"
        ref="schemaInput"
        type="text"
      />
      <span
        slot="footer"
        class="dialog-footer"
      >
        <el-button @click="() => {showSchema = false;}">取 消</el-button>
        <el-button
          type="primary"
          @click="copySchema"
        >复制</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import ElementUI from "element-ui";
import RenderComponents from "./renderComponents.vue";
import ElSelect from "../widgets/select.vue";
import ElRadio from "../widgets/radio.vue";
export default {
  components: {
    RenderComponents,
    ElSelect,
    ElRadio
  },
  data() {
    return {
      // 用于展示demo的schema数据，
      demoSchema: {},
      // 显示schema数据
      showSchema: false,
      // 显示拖拽border
      showBorder: false,
      // 编辑模式
      editing: true,
      // 组件数据
      componentData: [
        {
          component: "ElSelect",
          label: "表单label",
          $id: "ElSelect_" + Math.floor(Math.random() * 1000),
          propValue: {
            value: "",
            enums: ["a", "b", "c"],
            enumNames: ["早", "中", "晚"]
          }
        },
        {
          component: "ElRadio",
          label: "表单label",
          $id: "ElRadio_" + Math.floor(Math.random() * 1000),
          propValue: {
            value: "",
            enums: ["a", "b", "c"],
            enumNames: ["早", "中", "晚"]
          }
        }
      ],
      // 当前选中的item
      activeItem: {}
    };
  },
  computed: {
    schema() {
      const schema = [];
      const model = {};
      this.componentData.forEach(item => {
        const newItem = {
          ...item,
          propValue: {
            ...item.propValue
          }
        };
        model[item.$id] = item.propValue.value;
        delete newItem.propValue.value;
        schema.push(newItem);
      });
      return {
        schema,
        model
      };
    }
  },
  mounted() {},
  methods: {
    /**
     * 展示demo
     */
    showDemo() {
      this.demoSchema = JSON.parse(JSON.stringify(this.schema));
      this.editing = false;
    },
    /**
     * 拖放
     */
    handleDrop(e) {
      e.preventDefault();
      e.stopPropagation();
      const component = e.dataTransfer.getData("component");
      this.addComponent(component);
    },
    /**
     * 向组件数据中添加一个组件
     * @param { String } name 组件名称
     */
    addComponent(name) {
      const item = {
        component: name,
        label: "表单label",
        propValue: {
          value: ""
        }
      };
      item.$id = name + "_" + Math.floor(Math.random() * 1000);
      if (["ElSelect", "ElRadio"].indexOf(name) > -1) {
        item.propValue.enums = ["a", "b", "c"];
        item.propValue.enumNames = ["早", "中", "晚"];
      }

      this.componentData.push(item);
      this.clearActive();
      this.editing = true;
    },
    /**
     * 拖拽结束
     */
    handleDragOver(e) {
      e.preventDefault();
      this.showBorder = true;
    },
    /**
     * 对外方法，设置是否正在拖拽
     * @param { Boolean } dragging 是否正在拖拽
     */
    setDragging(dragging) {
      this.showBorder = dragging;
    },
    /**
     * 设置选中项
     */
    selectCurComponent(e, activeItem) {
      this.activeItem = activeItem;
      this.$emit(
        "show-props",
        this.activeItem,
        ElementUI[this.activeItem.component.slice(2)]
      );
    },
    /**
     * 复制
     */
    copyItem(item, index) {
      this.componentData.splice(index + 1, 0, {
        ...item,
        propValue: { ...item.propValue },
        $id: item.name + "_" + Math.floor(Math.random() * 1000)
      });
    },
    /**
     * 删除
     */
    deleteItem(item, index) {
      this.componentData.splice(index, 1);
      this.clearActive();
    },
    /**
     * 清除
     */
    clear() {
      this.componentData = [];
      this.clearActive();
    },
    /**
     * 对外方法，改变props的值
     */
    changeProps(key, value) {
      this.$set(this.activeItem.propValue, key, value);
    },
    /**
     * 对外方法，改变item的只值
     */
    changeItem(key, value) {
      this.$set(this.activeItem, key, value);
    },
    /**
     * 清楚当前项
     */
    clearActive() {
      this.activeItem = {};
      this.$emit("show-props", {}, {});
    },
    copySchema() {
      const input = this.$refs.schemaInput;
      input.value = JSON.stringify(this.schema, null, 2);
      input.select(); // 选中文本
      document.execCommand("copy"); // 执行浏览器复制命令
      this.$message("复制成功");
      this.showSchema = false;
    }
  }
};
</script>

<style lang="scss" scoped>
.app-middle {
  height: 100%;
  padding: 10px;
  border-right: 1px solid #d9d9d9;
  border-left: 1px solid #d9d9d9;
  display: flex;
  flex-direction: column;
  overflow: auto;
}
.actions {
  margin-bottom: 10px;
}
.playground {
  background: #fafafa;
  flex: 1;
  border: 1px solid #eaeefb;
  &.border {
    border-color: #409eff;
  }
}
.field-wrapper {
  background: #fff;
  border: 1px dashed #eaeefb;
  margin: 10px;
  // padding: 20px;
  position: relative;
  animation: bg-flash 0.3s;
  .el-form-item {
    margin-bottom: 0;
    padding: 20px;
  }
  &.active {
    border-color: #409eff;
    .item-actions {
      display: block;
    }
  }
}
@keyframes bg-flash {
  0% {
    background: #fff;
  }
  50% {
    background: rgb(248, 237, 153);
  }
  100% {
    background: #fff;
  }
}
.item-actions {
  position: absolute;
  left: -1px;
  top: -20px;
  display: none;
}
.position-handler {
  cursor: all-scroll;
}
.schema-display {
  height: 300px;
  overflow: auto;
}
.schema-input {
  position: absolute;
  top: -100px;
  left: -100px;
  opacity: 0;
  z-index: -10;
}
</style>
