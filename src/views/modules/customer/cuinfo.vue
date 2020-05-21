<template>
<div>
  <!--查询表单-->
    <el-form :inline="true" class="demo-form-inline" >
        <el-form-item >
            <el-input  v-model="searchObj.customerId" placeholder="身份证"/>
        </el-form-item>
        <el-form-item>
            <el-input  v-model="searchObj.inRoom" placeholder="入住房间号"/>
        </el-form-item>

      <!-- <el-form-item>
        <el-select clearable placeholder="讲师头衔">
          <el-option :value="1" label="高级讲师"/>
          <el-option :value="2" label="首席讲师"/>
        </el-select>
      </el-form-item> -->

        <el-form-item>
            <el-input  placeholder="电话号码"/>
        </el-form-item>

      <!-- <el-form-item label="入住时间">
        <el-date-picker
          type="datetime"
          placeholder="选择开始时间"
          value-format="yyyy-MM-dd HH:mm:ss"
          default-time="00:00:00"
        />
      </el-form-item>
      <el-form-item>
        <el-date-picker
          
          type="datetime"
          placeholder="选择截止时间"
          value-format="yyyy-MM-dd HH:mm:ss"
          default-time="00:00:00"
        />
      </el-form-item> -->

      <el-button type="primary" icon="el-icon-search">查询</el-button>
      <el-button type="default" >清空</el-button>
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
      prop="inRoom"
      label="入住房间号">
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
</div>
</template>
<script>
export default {

  data () { // 定义数据
    return {
      listLoading: true, // 是否显示loading信息
      list: [], // 数据列表
      total: 0, // 总记录数
      page: 1, // 页码
      limit: 2, // 每页记录数
      searchObj: {}// 查询条件
    }
  },

  created () { // 当页面加载时获取数据
    this.fetchData()
  },

  methods: {
    fetchData (page = 1) { // 调用api层获取数据库中的数据
      console.log('加载列表')
      this.page = page
      this.listLoading = true
      this.$http({
        url: this.$http.adornUrl('/hotel/customer/list'),
        method: 'get',
        params: this.$http.adornParams({
          'page': this.page,
          'limit': this.limit,
          'searchObj': this.searchObj
        })
      }).then(({data}) => {
        console.log(data)
        if (data && data.code === 20000) {
          this.list = data.data.cutomers
          // this.totalPage = data.total
          console.log(this.list)
        } else {
          this.dataList = []
          this.totalPage = 0
        }
        this.dataListLoading = false
      })
    }
  }
}
</script>