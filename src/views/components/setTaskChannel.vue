<template>
<div>
  <el-dialog :title="title" :visible.sync="dialogFormVisible" size="small">
    <el-form class="inline" label-width="50px" v-for="(salestask, index) in salestasks" :key="index">
      <el-form-item :label="salestask.Name">
        <el-input class="inputW" v-model="salestask.TaskNumMonth"></el-input>
      </el-form-item>
    </el-form>
    <div slot="footer" class="dialog-footer">
      <el-button @click="dialogFormVisible = false">取 消</el-button>
      <el-button type="primary" @click="submitForm">确 定</el-button>
    </div>
  </el-dialog>
</div>
</template>

<script>
import {
  saveTask,
  saveTaskPost
} from '../../api/api'
export default {
  props: ['year', 'salestasks', 'length'],
  data() {
    return {
      title: '',
      dialogFormVisible: true
    }
  },
  created() {
    this.title = `代理商任务设置(${this.year}年)`
  },
  methods: {
    submitForm() {
      if (this.length) {
        saveTask(this.salestasks).then(res => {
          if (res.status) {
            this.$message({
              type: 'success',
              message: '保存成功!'
            })
            this.dialogFormVisible = false
          }
        }).catch(() => {})
      } else {
        saveTaskPost(this.salestasks).then(res => {
          if (res.status) {
            this.$message({
              type: 'success',
              message: '保存成功!'
            })
            this.dialogFormVisible = false
          }
        }).catch(() => {})
      }
    }
  }
}
</script>

<style scoped>
.inline {
  width: 50%;
  display: inline-block;
}

.setW {
  width: 40%;
}
</style>
