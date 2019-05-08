<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card shadow="never">
         <!-- 新增面试技巧 -->
        <el-row>
          <el-button type="info" size="small" class="title">新增面试技巧</el-button>
        </el-row>
        <!-- 关键字 -->
        <el-form :inline="true" class="keyWords">
          <el-form-item style="float:left" label="关键字">
           <el-input v-model="requestParameters.query"  placeholder="请输入题目编号/题干"></el-input>
          </el-form-item>
          <el-button class="search" type="primary" style="float:right" @click="search()">搜索</el-button> 
          <el-button style="float:right" @click="clear()">清除 </el-button> 
        </el-form>
        <!-- 数据列表 -->
        <el-table
          :key="tableKey"
          :data="dataList"
          highlight-current-row
          style="width: 100%"
          border>
           <el-table-column
              type="index"
              width="50"
              label="序号"
              >
           </el-table-column>
          <el-table-column align="center" label="标题">
            <template slot-scope="scope">
              <span>{{scope.row.title}}</span>
              <el-button icon="el-icon-s-promotion" size="mini" style="float:right"></el-button>
            </template>
          </el-table-column>
          <el-table-column align="center" label="阅读数">
            <template slot-scope="scope">
              <span>{{scope.row.visits}}</span>
            </template>
          </el-table-column>
          <el-table-column align="center" label="状态">
            <template slot-scope="scope">
              <span v-if="scope.row.state==1">启用</span>
              <span v-else>禁用</span>
            </template>
          </el-table-column>
          <el-table-column align="center" label="录入人">
            <template slot-scope="scope">
              <span>{{scope.row.creator}}</span>
            </template>
          </el-table-column>
          <!-- 操作按钮 -->
          <el-table-column
            align="center"
            label="操作"
            width="300"
            class-name="small-padding fixed-width"
          >
          <template slot-scope="scope">
            <el-button type="primary" size="mini" @click="handleLook(scope.row.id)">预览</el-button>
            <el-button type="primary" size="mini" @click="handleChange(scope.row.id)">编辑</el-button>
            <el-button type="primary" size="mini" @click="handleStatus(scope.row)">
              <span v-if="scope.row.state==0">启用</span>
              <span v-else>禁用</span>
            </el-button>
            <el-button
              v-if="scope.row.status!='deleted'"
              size="mini"
              type="danger"
              @click="removeArticle(scope.row.id)"
            >删除</el-button>
          </template>
          </el-table-column>
        </el-table>
        <!-- end -->
        <!-- 分页 -->
        <div class="pagination">
          <div class="pages">
            <el-pagination
              background
              @size-change="handleSizeChange"
              @current-change="handleCurrentChange"
              :current-page="Number(requestParameters.page)"
              :total="Number(total)"
              :page-size="Number(requestParameters.pagesize)"
              :page-sizes="[10,20,30, 50]"
              layout="sizes, prev, pager, next, jumper"
            ></el-pagination>
          </div>
        </div>
        <!-- end -->
        <!-- 预览标签弹层 -->
        <Dialog ref="editUser" :titleInfo="titleInfo" :formBase="formData" v-on:newDataes="getList"></Dialog>
      </el-card>
    </div>
  </div>
</template>

<script>
import { status } from '@/api/hmmm/constants'
import { list, remove, detail } from '@/api/hmmm/articles'
import { provinces, citys } from '@/api/hmmm/citys.js'
import Dialog from './../components/companys-add'
var _this = null
export default {
  name: 'CompanysList',
  components: {
    Dialog
  },
  data() {
    return {
      tableKey: 0,
      dataList: [],
      total: null,
      listLoading: true,
      alertText: '',
      requestParameters: {
        query: '',
        page: 1,
        pagesize: 10
      },
      titleInfo: {
        pageTitle: '用户', // 页面标题
        text: '' // 新增、编辑文本
      },
      formData: {
        isFamous: '',
        shortName: '',
        company: '',
        province: '',
        city: '',
        tags: '',
        remarks: ''
      },
      citySelect: {
        province: [],
        cityDate: []
      }
    }
  },
  computed: {
    status() {
      return status
    }
  },
  
  methods: {
    initialDate() {
      // 读取数据
      this.getList()
    },
    // 获取列表数据
    async getList() {
      const { data: res } = await list(this.requestParameters)
      // 获取数据给dataList
      this.dataList = res.items
    },
    // 清除关键字
    clear() {
      this.requestParameters.query = ''
    },
    // 搜索关键字
     search () {
      this.requestParameters.page = 1
      this.getList()
    },
    // 重置
    resetForm() {
      this.$refs['requestParameters'].resetFields()
    },
    // 搜索信息
    handleFilter() {
      this.requestParameters.page = 1
      this.getList(this.requestParameters)
    },
    // 每页显示信息条数
    handleSizeChange(val) {
      this.requestParameters.pagesize = val
      if (this.requestParameters.page === 1) {
        this.getList(this.requestParameters)
      }
    },
    // 进入某一页
    handleCurrentChange(val) {
      this.requestParameters.page = val
      this.getList()
    },

    // **********************************
    // 搜索的项目
    // 添加、编辑之后表单清空
    query() {
      this.formData = {}
    },
    // 新增用户、编辑用户
    handleChange(id) {
      this.query()
      this.$refs.editUser.dialogFormV()
      if (id === 'add') {
        this.titleInfo.text = '创建' // 给弹层传参
      } else {
        this.titleInfo.text = '编辑' // 给弹层传参
        this.hanldeEditForm(id)
      }
    },
    // 窗口操作**********************************
    // 编辑
    // 表单详情数据加载
    async hanldeEditForm(objeditId) {
      this.formData.id = objeditId
      const { data: res } = await detail({ id: objeditId })
      // 获取详情 给formData
      this.formData = res
    },
    // 删除
    /* removeArticle(id) {
      this.$confirm('此操作将永久删除文章 ' + ', 是否继续?', '提示', {
        type: 'warning'
      })
        .then(async () => {
          await remove({ id })
            .then(response => {
              this.$message.success('成功删除了文章' + '!')
              this.dataList.splice(id, 1)
              this.getList(this.requestParameters)
            })
            .catch(response => {
              this.$message.error('删除失败!')
            })
        })
        .catch(() => {
          this.$message.info('已取消操作!')
        })
    }, */
    removeArticle(id) {
      this.$confirm('是否删除该目录信息', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async () => {
        this.$message.success('成功删除了文章')
        const data = await remove ({'id': id})
        console.log(data)
        this.getList()
      }) 
      .catch(response => {
        this.$message.error('删除失败!')
        })
    },
    // 启用、禁用
    handleStatus(val) {
      var status = ''
      if (val.state === true) {
        val.state = 0
        status = '禁用'
      } else {
        val.state = 1
        status = '启用'
      }
      var data = {
        id: val.id,
        state: val.state
      }
      this.$confirm('已成功' + status + ', 是否继续?', '提示', {
        type: 'warning'
      })
        .then(async () => {
          await disabled(data)
            .then(response => {
              this.$message.success('已成功' + status + '!')
              this.getList(this.requestParameters)
            })
            .catch(response => {
              this.$message.error(status + '失败!')
            })
        })
    },
    // 获取省
    getCityData: function() {
      this.citySelect.province = provinces()
    },
    // 选省获取到市
    handleProvince: function(e) {
      this.citySelect.cityDate = citys(e)
      this.requestParameters.city = this.citySelect.cityDate[0]
    }
  },
  // 挂载结束
  mounted: function() {},
  // 创建完毕状态
  created() {
    var _this = this
    this.initialDate()
    this.getCityData()
    // 键盘enter操作
    document.onkeydown = function(e) {
      var key = window.event.keyCode
      if (key === 13) {
        _this.handleFilter(this.requestParameters)
      }
    }
  },
  // 组件更新
  updated: function() {}
}
</script>
<style rel="stylesheet/scss" lang="scss" scoped>
.alert {
  margin: 10px 0px;
}
.pagination {
  margin-top: 10px;
}
</style>

<style>
.title{
  background-color: RGB(243,243,243);
  margin-bottom: 10px;
  color:black;
}
.demo-form-inline{
  background-color: RGB(242,242,242);
  margin-bottom: 10px;
}
.pagination{
  float: right;
}
.search{
  margin-left: 10px;
}
.el-table th {
  background-color: #fafafa;
}
.el-table th.is-leaf {
  border-bottom: 2px solid #e8e8e8;
}
.disabled td {
  background-color: #f9f9f9;
  color: #c1c1c1;
}
.disabled .el-button--primary,
.disabled .el-button--danger {
  background-color: #dbdada;
  border: 1px solid #dbdada;
  color: #999;
  cursor: not-allowed;
}
</style>
