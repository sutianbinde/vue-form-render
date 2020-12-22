<template>
  <div id="app">
    <el-row>
      <el-col :span="24">
        <div class="app-header">Form-Render</div>
      </el-col>
    </el-row>
    <el-row class="app-main">
      <el-col :span="3">
        <app-left
          @com-dragend="onDragend"
          @com-select="(name)=>{$refs.middle.addComponent(name)}"
        ></app-left>
      </el-col>
      <el-col :span="16">
        <app-middle
          ref="middle"
          @show-props="(item) => {$refs.right.setItem(item)}"
        ></app-middle>
      </el-col>
      <el-col :span="5">
        <app-right ref="right"></app-right>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import AppLeft from "./components/left.vue";
import AppRight from "./components/right.vue";
import AppMiddle from "./components/middle.vue";

export default {
  name: "App",
  components: {
    AppLeft,
    AppRight,
    AppMiddle
  },
  methods: {
    onDragend() {
      this.$refs.middle.setDragging(false);
    }
  }
};
</script>

<style lang="scss">
* {
  padding: 0;
  margin: 0;
}
body,
html {
  height: 100%;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  height: 100%;
}
.app-header {
  height: 60px;
  line-height: 60px;
  padding-left: 30px;
  box-shadow: 0 8px 24px -2px rgba(0, 0, 0, 0.05);
  border-bottom: 1px solid #d9d9d9;
  font-weight: bold;
  font-size: 20px;
}
.app-main {
  height: calc(100% - 80px);
  & > * {
    height: 100%;
  }
}
</style>
