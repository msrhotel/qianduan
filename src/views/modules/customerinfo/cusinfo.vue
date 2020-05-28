
<template>
<div>
  <!--查询表单-->
    <el-form :inline="true" class="demo-form-inline" >
        <el-form-item >
            <el-input  v-model="searchObj.customerId" placeholder="身份证"/>
        </el-form-item>
        <el-form-item>
            <el-input  v-model="searchObj.customerName" placeholder="客户姓名"/>
        </el-form-item>
        <el-form-item>
            <el-input v-model="searchObj.customerinfoTel" placeholder="电话号码"/>
        </el-form-item>
      <el-button type="primary" icon="el-icon-search" @click="fetchData()">查询</el-button>
      <el-button type="default" @click="resetData()">清空</el-button>
      <el-button  type="primary" @click="addOrUpdateHandle()">新增</el-button>
    </el-form>
  <el-table
    :data="list"
    height="250"
    border
    style="width: 100%">

    <el-table-column
      prop="customerId"
      label="身份证号">
    </el-table-column>
        <el-table-column
      prop="customerName"
      label="客户姓名">
    </el-table-column>
     <el-table-column
      prop="customerinfoTel"
      label="客户联系电话">
    </el-table-column>
     <el-table-column
      prop="customerinfoBirthday"
      label="客户出生日期">
    </el-table-column>
     <el-table-column
      prop="customerinfoInfo"
      label="备注">
    </el-table-column>
    <!-- <el-table-column
      prop="customerinfoBirthday"
      label="出生日期"
      width="180">
    </el-table-column> -->
    <!-- <el-table-column
      prop="customerName"
      label="姓名"
      width="180">
    </el-table-column> -->
        <el-table-column label="操作" width="200" align="center">
        <template slot-scope="scope">
          <!-- <router-link :to="'/cutomer/edit/'+scope.row.customerId"> -->
            <el-button  type="primary" size="mini" icon="el-icon-edit" @click="addOrUpdateHandle(scope.row.customerId)">修改</el-button>
          <!-- </router-link> -->
          <el-button type="danger" size="mini" icon="el-icon-delete" @click="removeDataById(scope.row.customerId)">删除</el-button>
        </template>
      </el-table-column>
  </el-table>
    <!-- 分页 -->
    <el-pagination
      :current-page="page"
      :page-size="limit"
      :total="total"
      style="padding: 30px 0; text-align: center;"
      layout="total, prev, pager, next, jumper"
      @current-change="fetchData"
    />
       <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="fetchData"></add-or-update>
</div>
</template>
<script>
import AddOrUpdate from './customerinfo-add-or-update'
export default {

  data () { // 定义数据
    return {
      listLoading: true, // 是否显示loading信息
      list: [], // 数据列表
      total: 0, // 总记录数
      page: 1, // 页码
      limit: 2, // 每页记录数
      searchObj: {}, // 查询条件
      addOrUpdateVisible: false
    }
  },

  activated () { // 当页面加载时获取数据
    this.fetchData()
  },
  components: {
    AddOrUpdate
  },

  methods: {
    fetchData (page = 1) { // 调用api层获取数据库中的数据
      console.log('加载列表')
      this.page = page
      this.listLoading = true
      this.$http({
        url: this.$http.adornUrl(`/servicecustomerinfo/hotel-customerinfo/${this.page}/${this.limit}`),
        method: 'get',
        params: this.$http.adornParams(this.searchObj)
        // params: this.$http.adornParams({
        //   'page': this.page,
        //   'limit': this.limit,
        //   'searchObj': this.searchObj
        // })
      }).then(({data}) => {
        console.log(data)
        if (data && data.code === 20000) {
          this.list = data.data.rows
          this.total = data.data.total
          console.log(this.list)
        } else {
          this.dataList = []
          this.totalPage = 0
        }
        this.dataListLoading = false
      })
    },
    resetData () {
      this.searchObj = {}
      this.fetchData()
    },
    removeDataById (id) {
    // debugger
      this.$confirm('此操作将永久删除该记录, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        console.log(id)
        return this.$http({
          url: this.$http.adornUrl(`/servicecustomerinfo/hotel-customerinfo/${id}`),
          method: 'delete'
        })
      }).then(() => {
        this.fetchData()
        this.$message({
          type: 'success',
          message: '删除成功!'
        })
      }).catch((response) => { // 失败
        if (response === 'cancel') {
          this.$message({
            type: 'info',
            message: '已取消删除'
          })
        } else {
          this.$message({
            type: 'error',
            message: '删除失败'
          })
        }
      })
    },
    // 新增
    addOrUpdateHandle (id) {
      this.addOrUpdateVisible = true
      this.$nextTick(() => {
        this.$refs.addOrUpdate.init(id)
      })
    }
  }
}
</script>