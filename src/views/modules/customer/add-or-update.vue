<template>
  <el-dialog
    :title="!id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="身份证号" prop="customerId">
        <el-input v-model="dataForm.customerId" placeholder="身份证号"></el-input>
      </el-form-item>
      <el-form-item label="入住房间号" prop="inRoom">
        <el-input v-model="dataForm.inRoom" placeholder="入住房间号"></el-input>
      </el-form-item>
      <el-form-item label="入住日期" prop="inDate">
        <el-input v-model="dataForm.inDate" placeholder="入住日期"></el-input>
      </el-form-item>
      <el-form-item label="预定退房时间" prop="outDate">
        <el-input v-model="dataForm.outDate" placeholder="预定退房时间"></el-input>
      </el-form-item>
      <el-form-item label="收取的房费" prop="customerRent">
        <el-input v-model="dataForm.customerRent" placeholder="收取的房费"></el-input>
      </el-form-item>
      <el-form-item label="收取押金" prop="customerDeposit">
        <el-input v-model="dataForm.customerDeposit" placeholder="收取押金"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        id: 0,
        visible: false,
        // customerList: [],
        dataForm: {
          customerId: 0,
          inRoom: '',
          inDate: '',
          outDate: '',
          customerRent: 0,
          customerDeposit: 0
        },
        dataRule: {
  
        }
      }
    },
    methods: {
      init (id) {
        this.id = id
        this.dataForm.customerId = id || 0
        // this.$http({
        //   url: this.$http.adornUrl(`/hotel/customer/list`),
        //   method: 'get',
        //   params: this.$http.adornParams()
        // }).then(({data}) => {
        //   this.customerList = data && data.code === 20000 ? data.data.cutomers : []
        // }).then(() => {
        this.visible = true
        this.$nextTick(() => {
          console.log(this.$refs['dataForm'].resetFields())
          this.$refs['dataForm'].resetFields()
          this.id = 0
        })
        if (this.dataForm.customerId) {
          this.$http({
            url: this.$http.adornUrl(`/hotel/customer/${this.dataForm.customerId}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({data}) => {
            console.log(data)
            if (data && data.code === 20000) {
              this.dataForm.customerId = data.data.item.customerId
              this.dataForm.inRoom = data.data.item.inRoom
              this.dataForm.inDate = data.data.item.inDate
              this.dataForm.outDate = data.data.item.outDate
              this.dataForm.customerRent = data.data.item.customerRent
              this.dataForm.customerDeposit = data.data.item.customerDeposit
            }
          })
        }
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/hotel/customer/${!this.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'customerId': this.dataForm.customerId || undefined,
                'inRoom': this.dataForm.inRoom,
                'inDate': this.dataForm.inDate,
                'outDate': this.dataForm.outDate,
                'customerRent': this.dataForm.customerRent,
                'customerDeposit': this.dataForm.customerDeposit
              })
            }).then(({data}) => {
              if (data && data.code === 20000) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
