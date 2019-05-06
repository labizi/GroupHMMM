<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card>
        <el-row>
          <el-button type="info" size="small" class="title" @click="addSkills">新增面试技巧</el-button>
        </el-row>
        <el-form :inline="true" :model="formInline" class="">
          <el-form-item style="float:left" label="请输入">
           <el-input  v-model="formInline.user" placeholder="请输入题目编号/题干"></el-input>
          </el-form-item>
          <el-button class="search" type="primary" style="float:right">搜索</el-button> <el-button style="float:right">清除 </el-button> 
        </el-form>
      <el-table
        :data="tableData"
        style="width: 100%">
        <el-table-column
          prop="xuhao"
          label="序号"
          width="180">
        </el-table-column>
        <el-table-column
          prop="title"
          label="标题"
          width="180">
        </el-table-column>
        <el-table-column
          prop="readNub"
          label="阅读数 "
          width="180">
        </el-table-column>
        <el-table-column
          prop="status"
          label="状态"
          width="180">
        </el-table-column>
        <el-table-column
          prop="inputMan"
          label=" 录入人"
          width="180">
        </el-table-column>
        <el-table-column
          prop="done"
          label=" 操作">
        </el-table-column>
      </el-table>
      <el-pagination
      style="float:right"
      background
        layout="prev, pager, next"
        :total="100">
      </el-pagination>
      </el-card>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ArticlesList',
  data() {
    return {
      input: '',
      formInline: {
          user: '',
          region: '',
          status: '启用',
          inputMan: 'labi',
          done: ' 修改'
        },
      tableData: [{
        xuhao: '1',
        title: '互联网公司面试十大注意事项， 出现三项不符合恐怕就没戏了。',
        readNub: '1234567',
         status: '启用',
          inputMan: 'labi',
          done: ' 修改'
      }, {
        xuhao: '2',
       title: '互联网公司面试十大注意事项， 出现三项不符合恐怕就没戏了。',
        readNub: '734687',
         status: '启用',
          inputMan: 'labi',
          done: ' 修改'
      }, {
        xuhao: '3',
       title: '互联网公司面试十大注意事项， 出现三项不符合恐怕就没戏了。',
        readNub: '3576893',
         status: '启用',
          inputMan: 'labi',
          done: ' 修改'
      },  {
        xuhao: '4',
       title: '互联网公司面试十大注意事项， 出现三项不符合恐怕就没戏了。',
        readNub: '743678',
         status: '启用',
          inputMan: 'labi',
          done: ' 修改'
      },  {
        xuhao: '5',
       title: '互联网公司面试十大注意事项， 出现三项不符合恐怕就没戏了。',
        readNub: '736897',
         status: '启用',
          inputMan: 'labi',
          done: ' 修改'
      },  {
        xuhao: '6',
       title: '互联网公司面试十大注意事项， 出现三项不符合恐怕就没戏了。',
        readNub: '4763598',
         status: '启用',
          inputMan: 'labi',
          done: ' 修改'
      },  ]
    }
  }
}
</script>

<style scoped>
.title{
  background-color: RGB(243,243,243);
  margin-bottom: 10px;
  color:black;
}
.demo-form-inline{
  background-color: RGB(242,242,242);
  margin-bottom: 10px;
}
.search{
  margin-left: 10px;
}
</style>
