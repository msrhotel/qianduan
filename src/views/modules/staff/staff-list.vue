<template>
<div>
    <!--查询表单-->
    <el-form :inline="true" class="demo-form-inline" >
        <el-form-item >
            <el-input  v-model="searchObj.staffId" placeholder="员工ID"/>
        </el-form-item>
        <el-form-item >
            <el-input  v-model="searchObj.staffName" placeholder="员工姓名"/>
        </el-form-item>
        <el-form-item>
            <el-input  v-model="searchObj.staffEmp" placeholder="所属部门"/>
        </el-form-item>
        <el-form-item>
            <el-input  v-model="searchObj.staffPosition" placeholder="所在职位"/>
        </el-form-item>
        <el-button type="primary" icon="el-icon-search" @click="fetchData()">查询</el-button>
      <el-button type="default" >清空</el-button>
      <el-button  type="primary" @click="addOrUpdateHandle()">新增</el-button>
    </el-form>
    <el-table
    :data="list"
    height="250"
    border
    style="width: 100%">

    <el-table-column
      prop="staffId"
      label="员工ID">
    </el-table-column>
    <el-table-column
      prop="staffName"
      label="员工姓名">
    </el-table-column>
    <el-table-column
      prop="staffIdcard"
      label="身份证">
    </el-table-column>
    <el-table-column
      prop="staffEmp"
      label="所属部门">
    </el-table-column>
    <el-table-column
      prop="staffPosition"
      label="所在职位">
    </el-table-column>
    <el-table-column
      prop="staffDate"
      label="员工入职日期">
    </el-table-column>
    <el-table-column
      prop="staffSalary"
      label="员工基本工资">
    </el-table-column>
    <el-table-column
      prop="staffBonus"
      label="员工奖金">
    </el-table-column>
    <el-table-column
      prop="staffInfo"
      label="备注">
    </el-table-column>

    <el-table-column label="操作" width="200" align="center">
        <template slot-scope="scope">
          <el-button type="primary" size="mini" icon="el-icon-edit" @click="addOrUpdateHandle(scope.row.staffId)">修改</el-button>
          <el-button type="danger" size="mini" icon="el-icon-delete" @click="removeDataById(scope.row.staffId)">删除</el-button>
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
import AddOrUpdate from './staff-add-or-update'
export default {
  data () { // 定义数据
    return {
      listLoading: true, // 是否显示loading信息
      list: [], // 数据列表
      total: 0, // 总记录数
      page: 1, // 页码
      limit: 2, // 每页记录数
      searchObj: {}, // 查询条件
      pickerOptions: {

      },
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
      console.log(this.searchObj)
      this.page = page
      this.listLoading = true
      this.$http({
        url: this.$http.adornUrl(`/servicestaff/hotel-staff/${this.page}/${this.limit}`),
        method: 'get',
        params: this.$http.adornParams(this.searchObj)
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
    removeDataById (id) {
    // debugger
      this.$confirm('此操作将永久删除该记录, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        console.log(id)
        return this.$http({
          url: this.$http.adornUrl(`/servicestaff/hotel-staff/${id}`),
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
    addOrUpdateHandle (id) {
      this.addOrUpdateVisible = true
      this.$nextTick(() => {
        this.$refs.addOrUpdate.init(id)
      })
    },
    resetData () {
      this.searchObj = {}
      this.fetchData()
    }
  }
}
</script>

<style>

</style>