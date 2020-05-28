<template>
  <div class="app-container">
    <!--查询表单-->
    <el-form :inline="true" class="demo-form-inline">
          <el-button type="primary" @click="addOrUpdateHandle()">新增</el-button>
      <el-form-item>
        <el-input v-model="searchObj.roomType" placeholder="房间类型"/>
      </el-form-item>

       <el-form-item>
        <el-input v-model="searchObj.roomState" placeholder="房间状态"/>
      </el-form-item>

       <el-form-item>
        <el-input v-model="searchObj.roomPrice" placeholder="房间价格"/>
      </el-form-item>

      <el-form-item label="添加时间">
        <el-date-picker
          v-model="searchObj.begin"
          type="datetime"
          placeholder="选择开始时间"
          value-format="yyyy-MM-dd HH:mm:ss"
          default-time="00:00:00"
        />
      </el-form-item>
      <el-form-item>
        <el-date-picker
          v-model="searchObj.end"
          type="datetime"
          placeholder="选择截止时间"
          value-format="yyyy-MM-dd HH:mm:ss"
          default-time="00:00:00"
        />
      </el-form-item>
  
      <el-button type="primary" icon="el-icon-search" @click="fetchData()">查询</el-button>
      <el-button type="default" @click="resetData()">清空</el-button>
    </el-form>
    <!-- 表格 -->
    <el-table
      v-loading="listLoading"
      :data="list"
      element-loading-text="数据加载中"
      border
      fit
      highlight-current-row>

      <!-- <el-table-column
        label="序号"
        width="70"
        align="center">
        <template slot-scope="scope">
          {{ (page - 1) * limit + scope.$index + 1 }}
        </template>
      </el-table-column> -->
       <el-table-column prop="roomNum" label="房间号码" width="80" />

      <el-table-column prop="roomType" label="房间类型" width="100" />
      <el-table-column prop="roomState" label="房间状态" width="90"/>
      <el-table-column prop="roomPrice" label="房间价格" width="80"/>
      <el-table-column prop="info" label="入住信息" width="320" />
      <el-table-column prop="gmtCreate" label="添加时间" width="160"/>


      <el-table-column label="操作" width="200" align="center">
        <template slot-scope="scope">
        
            <el-button type="primary" size="mini" icon="el-icon-edit" @click="addOrUpdateHandle(scope.row.roomNum)">修改</el-button>
       
       
          <el-button type="danger" size="mini" icon="el-icon-delete" @click="removeDataById(scope.row.roomNum)">删除</el-button>
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
import AddOrUpdate from './room-add-or-update'
export default {

  data () { // 定义数据
    return {
      listLoading: true, // 是否显示loading信息
      list: null, // 数据列表
      total: 0, // 总记录数
      page: 0, // 页码
      limit: 5, // 每页记录数
      searchObj: {}, // 查询条件
      addOrUpdateVisible: false
    }
  },
  components: {
    AddOrUpdate
  },

  activated () { // 当页面加载时获取数据
    this.fetchData()
  },

  methods: {

    fetchData (page = 1) { // 调用api层获取数据库中的数据
      console.log('加载列表')
      this.page = page
      this.listLoading = true
      this.$http({
        url: this.$http.adornUrl(`/serviceroom/hotel-room/${this.page}/${this.limit}`),
        method: 'get',
        params: this.$http.adornParams(this.searchObj)
        // params: this.$http.adornParams({
        //   page: this.page,
        //   limit: this.limit,
        //   searchObj: this.searchObj
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
        this.listLoading = false
      })
    },
    resetData () {
      this.searchObj = {}
      this.fetchData()
    },
    removeDataById (roomNum) {
      this.$confirm('此操作将永久删除该记录, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
          url: this.$http.adornUrl(`/serviceroom/hotel-room/${roomNum}`),
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
    addOrUpdateHandle (roomNum) {
      this.addOrUpdateVisible = true
      this.$nextTick(() => {
        this.$refs.addOrUpdate.init(roomNum)
      })
    }
  }
}
</script>
