<template>
  <el-dialog
    :title="!id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="身份证号" prop="customerId">
        <el-input v-model="dataForm.customerId" placeholder="身份证号"></el-input>
      </el-form-item>
      <el-form-item label="客户联系电话" prop="customerinfoTel">
        <el-input v-model="dataForm.customerinfoTel" placeholder="客户联系电话"></el-input>
      </el-form-item>
      <el-form-item label="客户生日" prop="customerinfoBirthday">
        <el-input v-model="dataForm.customerinfoBirthday" placeholder="客户生日"></el-input>
      </el-form-item>
      <el-form-item label="客户姓名" prop="customerName">
        <el-input v-model="dataForm.customerName" placeholder="客户姓名"></el-input>
      </el-form-item>
      <el-form-item label="备注" prop="customerinfoInfo">
        <el-input v-model="dataForm.customerinfoInfo" placeholder="备注"></el-input>
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
        // cutomerList: [],
        dataForm: {
          cutomerId: 0,
          customerinfoTel: '',
          customerinfoBirthday: '',
          customerName: '',
          customerinfoInfo: ''
        },
        dataRule: {
  
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.customerId = id || 0
        // this.$http({
         // url: this.$http.adornUrl(`/servicecustomerinfo/hotel-customerinfo/${id}`),
          // method: 'get',
          // params: this.$http.adornParams()
        // }).then(({data}) => {
          // console.log(data)
         // this.cutomerList = data && data.code === 20000 ? data.data.item : []
       // }).then(() => {
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
        })
        if (this.dataForm.customerId) {
          this.$http({
            url: this.$http.adornUrl(`/servicecustomerinfo/hotel-customerinfo/${this.dataForm.customerId}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({data}) => {
            console.log(data)
            if (data && data.code === 20000) {
              this.dataForm.customerId = data.data.item.customerId
              this.dataForm.customerinfoTel = data.data.item.customerinfoTel
              this.dataForm.customerinfoBirthday = data.data.item.customerinfoBirthday
              this.dataForm.customerName = data.data.item.customerName
              this.dataForm.customerinfoInfo = data.data.item.customerinfoInfo
            }
          })
        }
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/servicecustomerinfo/hotel-customerinfo/${!this.dataForm.customerId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'customerId': this.dataForm.customerId || undefined,
                'customerinfoTel': this.dataForm.customerinfoTel,
                'customerinfoBirthday': this.dataForm.customerinfoBirthday,
                'customerName': this.dataForm.customerName,
                'customerinfoInfo': this.dataForm.customerinfoInfo
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
