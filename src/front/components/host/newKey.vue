<template>
  <!-- 右侧From表单弹出抽屉 -->
  <el-drawer
    custom-class="pageColor"
    size="40%"
    :visible.sync="drawer"
    direction="rtl"
    :with-header="false"
    :before-close="handleClose">
    <!-- 右侧抽屉头部 -->
    <div class="header">
      <div>
        <div class="closeLeft fontColor">
          <i class="el-icon-back" @click="leftBack"></i>
        </div>
        <div class="addTitle">NEW KEY</div>
      </div>
      <div>
        <el-button @click="submitForm" type="success" size="mini" round>SAVE</el-button>
      </div>
    </div>
    <!-- 右侧抽屉主体 -->
    <div class="body">
      <el-form ref="key" label-position="left" label-width="80px" :model="key" :rules="rules">
        <el-form-item label="NAME" prop="name">
          <el-input v-model="key.name" clearable placeholder="key name"></el-input>
        </el-form-item>
        <el-form-item label="TYPE" prop="type">
          <el-select v-model="key.type" placeholder="Please select type" style="width: 100%" clearable>
            <el-option v-for="(item, i) of keyType" :key="i"
            :label="item.name" :value="item.value"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="EXPIRE" prop="expireModel">
          <el-input placeholder="Please enter the expiry time" v-model="key.expireTime" clearable>
            <el-select style="width: 100px;" clearable
               v-model="key.expireModel" slot="prepend" placeholder="请选择">
              <el-option label="seconds" value="EX"></el-option>
              <el-option label="milliseconds" value="PX"></el-option>
            </el-select>
          </el-input>
        </el-form-item>
        <el-form-item label="VALUE" prop="value">
          <el-input
            v-model="key.value" clearable
            type="textarea" :autosize="{ minRows: 2}"
            placeholder="key value">
          </el-input>
        </el-form-item>
      </el-form>
    </div>
  </el-drawer>
</template>
<script>
import { STRING, LIST, HASH, SET } from '../../../lib/redis/singal'
export default {
  name: 'NewKey',
  computed: {
    drawer: {
      get () {
        return this.$store.state.newKey.drawer
      },
      set (value) {
        this.$store.state.newKey.drawer = value
      }
    },
    current() {
      return this.$store.state.hostView.current
    }
  },
  data: function () {
    return {
      keyType: [STRING, LIST, HASH, SET],
      key: {},
      rules: {
        name: [
          { required: true, message: 'Please enter the key name', trigger: 'blur' }
        ],
        type: [
          { required: true, message: 'Please select the key type', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    handleClose (done) {
      this.$refs.key.resetFields()
      this.key = {}
      done()
    },
    leftBack () {
      this.$refs.key.resetFields()
      this.key = {}
      this.drawer = false
    },
    // 保存key
    submitForm () {
      this.$refs.key.validate(valid => {
        if (valid) {
          const data = {
            key: this.key.name,
            time: this.current.time,
            index: this.current.selectDB,
            expireModel: this.key.expireModel,
            expireTime: this.key.expireTime
          }
          switch (this.key.type) {
            case STRING.value: // string
              data.type = STRING.value
              data.value = this.key.value
              break
            default:
              break
          }
          this.$store.commit('redis/setKey', data) // 提交保存key
          this.current.dbLoading = true
          this.drawer = false
          this.$refs.key.resetFields()
          this.key = {}
        } else {
          return false
        }
      })
    }
  }
}
</script>
<style lang="less" scoped>
.header {
  height: 10%;
  border-bottom: solid 1px #334460;
  background-color: #1c3046;
  color: #fff;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 25px;
  > div:first-child {
    width: 80%;
    display: flex;
    justify-content: flex-start;
    > .closeLeft:hover {
      background-color: #334460;
      cursor: pointer;
      color: #fff;
    }
    > .closeLeft {
      margin-right: 10px;
      height: 25px;
      width: 25px;
      border-radius: 6px;
      > i {
        font-size: 14px;
        height: 14px;
      }
      display: flex;
      justify-content: center;
      align-items: center;
    }
    > .addTitle {
      line-height: 25px;
    }
  }
  > div:last-child {
    width: 20%;
  }
}
.body {
  margin: 15px;
  background-color: #fff;
  border-radius: 6px;
  padding: 15px;
}
</style>
