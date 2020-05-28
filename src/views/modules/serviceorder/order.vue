<template>
  <div class="mod-order">
    <el-form :inline="true" class="demo-form-inline">
      <el-form-item>
        <el-input v-model="searchObj.orderId" placeholder="订单号" clearable></el-input>
      </el-form-item>
      <el-form-item >
            <el-input  v-model="searchObj.customerId" placeholder="身份证"/>
        </el-form-item>
      <el-form-item>
        <el-button @click="fetchData()">查询</el-button>
        <el-button type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button type="default" @click="resetData()">清空</el-button>
        <el-button type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column
        prop="orderId"
        header-align="center"
        align="center"
        width="80"
        label="订单ID">
      </el-table-column>
      <el-table-column
        prop="customerId"
        header-align="center"
        align="center"
        label="身份证号">
      </el-table-column>
      <el-table-column
        prop="orderDays"
        header-align="center"
        align="center"
        label="入住天数">
      </el-table-column>
      <el-table-column
        prop="orderType"
        header-align="center"
        align="center"
        label="客房类型">
      </el-table-column>
      <el-table-column
        prop="inDate"
        header-align="center"
        align="center"
        type="Date"
        label="预订入住日期">
      </el-table-column>
      <el-table-column
        prop="outDate"
        header-align="center"
        align="center"
        type="Date"
        label="预订退房日期">
      </el-table-column>
      <el-table-column
        prop="gmtModified"
        header-align="center"
        align="center"
        width="180"
        label="更新时间">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.orderId)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.orderId)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
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
  import AddOrUpdate from './order-add-or-update'
  export default {
    data () {
      return {
        dataList: [],
        total: 0, // 总记录数
        page: 1, // 页码
        limit: 10, // 每页记录数
        searchObj: {}, // 查询条件
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.fetchData()
    },
    methods: {
      // 获取数据列表
      fetchData (page = 1) { // 调用api层获取数据库中的数据
        this.dataListLoading = true
        this.page = page
        this.$http({
          url: this.$http.adornUrl(`/serviceorder/order/${this.page}/${this.limit}`),
          method: 'get',
          params: this.$http.adornParams(this.searchObj)
        }).then(({data}) => {
          console.log(data)
          if (data && data.code === 20000) {
            this.dataList = data.data.rows
            this.total = data.data.total
          } else {
            this.dataList = []
            this.total = 0
          }
          this.dataListLoading = false
        })
      },
      // 重置页面
      resetData () {
        this.searchObj = {}
        this.fetchData()
      },
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 删除
      deleteHandle (id) {
        var orderIds = id ? [id] : this.dataListSelections.map(item => {
          return item.orderId
        })
        for (id in orderIds) {
          this.$confirm(`确定对[id=${orderIds.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            return this.$http({
              url: this.$http.adornUrl(`/serviceorder/order/${id}`),
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
          }).catch(() => {})
        }
      }
    }
  }
</script>