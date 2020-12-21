<template>
  <div class="app-middle">
    <div class="actions">
      <el-button size="medium" v-if="!editing" @click="editing=true">开始编辑</el-button>
      <el-button size="medium" v-if="editing" @click="editing=false">最终展示</el-button>
      <el-button size="medium">清空</el-button>
      <el-button size="medium" type="primary">查看schema</el-button>
    </div>
    <div class="playground" :class="{border: showBorder}" @drop="handleDrop" @dragover="handleDragOver">
      <el-form :model="ruleForm" status-icon ref="ruleForm" label-width="100px" class="demo-ruleForm">
        <div class="field-wrapper" v-for="(item, index) in componentData" :key="index">
          <el-form-item :label="item.label" prop="pass" @click.native="selectCurComponent">
            <component 
              :is="item.component"
              :style="item.style"
              :propValue="item.propValue"
            />
            <!-- <el-input type="password" v-model="ruleForm.pass" autocomplete="off"></el-input> -->
          </el-form-item>
        </div>
      </el-form>
    </div>
  </div>
</template>

<script>
import ElementUI from 'element-ui';

export default {
  data() {
    return {
      showBorder: false,
      editing: true,
      ruleForm: {
        pass: '',
        checkPass: '',
        age: ''
      },
      componentData: [{
        component: 'ElInput',
        label: '表单',
        propValue: {
          value: '',
        }
      }],
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
      const component = e.dataTransfer.getData('component');
      this.componentData.push({
        component,
        label: '表单',
        propValue: {
          value: '',
        }
      });
    },
    handleDragOver(e) {
      e.preventDefault();
      this.showBorder = true;
    },
    setDragging(dragging) {
      this.showBorder = dragging;
    },
    selectCurComponent(e) {
      console.log('鼠标点击', e);
      e.path.forEach((item) => {
        if (item.className && item.className.indexOf('el-form-item ') > -1 && item.__vue__) {
          // 找到from-item组件
          const { $children: [, vueComponent] } = item.__vue__;
          const { _data: data, $vnode: {componentOptions: {tag: name}}} = vueComponent;
          console.log('找到', data, name);
          console.log('全局组件', ElementUI[name.slice(2)]);
          this.$emit('show-props', ElementUI[name.slice(2)]);
        }
      });
    },
  }
}
</script>

<style lang="scss" scoped>
.app-middle {
  height: 100%;
  padding: 10px;
  border-right: 1px solid #d9d9d9;
  border-left: 1px solid #d9d9d9;
  display: flex;
  flex-direction: column;
}
.actions {
  margin-bottom: 10px;
}
.playground {
  background: #fafafa;
  flex: 1;
  border: 1px solid #eaeefb;
  &.border{
    border-color: #409EFF;
  }
}
.field-wrapper {
  background: #fff;
  border:  1px dashed #eaeefb;
  margin: 10px;
  padding: 10px;
  .el-form-item{
    margin-bottom: 0;
  }
}
</style>
