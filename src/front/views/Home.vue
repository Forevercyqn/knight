<template>
  <div class="home">
      <el-header height="10%"><Header/></el-header>
      <el-container>
        <el-aside width="18%" class="fontColor"><Aside></Aside></el-aside>
        <el-main :class="mainClass"><Main></Main></el-main>
      </el-container>
  </div>
</template>

<script>
import Header from '@/front/components/Header.vue'
import Aside from '@/front/components/Aside/index.vue'
import Main from '@/front/components/Main/index.vue'
import { mapState } from 'vuex'

export default {
  name: 'Home',
  computed: mapState({
    mainClass: state => state.app.mainClass
  }),
  components: {
    Header,
    Aside,
    Main
  }
}
</script>
<style scoped lang="less">
  .home {
    height: 100%;
    > .el-container {
      height: 90%;
      > ::-webkit-scrollbar { // 主body隐藏滚动条
        display: none; /* Chrome Safari */
      }
    }
  }
  .el-header, .el-footer {
    background-color: #1c3046;
    border-bottom: solid 1px #334460;
    color: #c9ced4;
    text-align: center;
  }
  .el-aside {
    background-color: #1c3046;
    text-align: center;
  }
  .el-main {
    // background-color: #E9EEF3;
    color: #333;
    height: 100%;
    padding: 0;
  }
  // body > .el-container {
  //   margin-bottom: 40px;
  // }
</style>
