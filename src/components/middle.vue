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
        @click="editing=false"
      >最终展示</el-button>
      <el-button
        size="medium"
        @click="componentData = []"
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
      <render-components :componentData="schema"></render-components>
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
          :key="item.component+index"
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
              @click="copy(item, index)"
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
      >{{ this.schema }}</pre>
      <span
        slot="footer"
        class="dialog-footer"
      >
        <el-button @click="() => {showSchema = false;}">取 消</el-button>
        <el-button
          type="primary"
          @click="showSchema = false"
        >复制</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import ElementUI from "element-ui";
import RenderComponents from "./renderComponents.vue";

export default {
  components: {
    RenderComponents
  },
  data() {
    return {
      showSchema: false,
      showBorder: false,
      editing: true,
      componentData: [
        {
          component: "ElSelect",
          label: "表单",
          propValue: {
            value: ""
          }
        }
      ],
      activeItem: {}
    };
  },
  computed: {
    schema() {
      return this.componentData;
    }
  },
  mounted() {
    // document.addEventListener('ondragend', () => {
    //   debugger; // eslint-disable-line
    //   this.showBorder = false;
    // });
  },
  methods: {
    handleDrop(e) {
      // debugger; // eslint-disable-line
      e.preventDefault();
      e.stopPropagation();
      const component = e.dataTransfer.getData("component");
      this.addComponent(component);
    },
    addComponent(name) {
      this.componentData.push({
        component: name,
        label: "表单",
        propValue: {
          value: ""
        }
      });
      this.activeItem = {};
      this.editing = true;
    },
    handleDragOver(e) {
      e.preventDefault();
      this.showBorder = true;
    },
    setDragging(dragging) {
      this.showBorder = dragging;
    },
    selectCurComponent(e, item) {
      this.activeItem = item;
      console.log("鼠标点击", e);
      e.path.forEach(item => {
        if (
          item.className &&
          item.className.indexOf("el-form-item") > -1 &&
          item.className.indexOf("el-form-item__") === -1 &&
          item.__vue__
        ) {
          // 找到from-item组件
          const {
            $children: [, vueComponent]
          } = item.__vue__;
          const {
            // _data: data,
            $vnode: {
              componentOptions: { tag: name }
            }
          } = vueComponent;
          // console.log("找到", data, name);
          // console.log("全局组件", ElementUI[name.slice(2)]);
          this.$emit("show-props", ElementUI[name.slice(2)], item.propValue);
        }
      });
    },
    copy(item, index) {
      this.componentData.splice(index + 1, 0, { ...item });
    },
    deleteItem(item, index) {
      this.componentData.splice(index, 1);
    },
    changeProps(key, value) {
      this.$set(this.activeItem.propValue, key, value);
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
</style>
