<template>
  <el-dialog
    :title="!dataForm.consumeId ? '新增或修改' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="收入ID" prop="incomeId">
        <el-input v-model="dataForm.incomeId" placeholder="收入ID"></el-input>
      </el-form-item>
      <el-form-item label="客户ID" prop="customerId">
        <el-input v-model="dataForm.customerId" placeholder="客户ID"></el-input>
      </el-form-item>
      <el-form-item label="收入类型" prop="incomeTypeId">
        <el-input v-model="dataForm.incomeTypeId" placeholder="收入类型"></el-input>
      </el-form-item>
      <el-form-item label="收入金额" prop="incomeAmount">
        <el-input v-model="dataForm.incomeAmount" placeholder="收入金额"></el-input>
      </el-form-item>
     
      <el-form-item label="操作员" prop="incomeStaff">
        <el-input v-model="dataForm.incomeStaff" placeholder="操作员"></el-input>
      </el-form-item>
      <el-form-item label="备注" prop="incomeInfo">
        <el-input v-model="dataForm.incomeInfo" placeholder="备注"></el-input>
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
        visible: false,
        incomeList: [],
        dataForm: {
          incomeId: '',
          customerId: '',
          incomeTypeId: 0,
          incomeAmount: 0,
          incomeDate: 0,
          incomeStaff: 0,
          incomeInfo: 0
        },
        dataRule: {
  
        }
      }
    },
    methods: {
      init (id) {
        this.id = id
        this.dataForm.incomeId = id || 0
        this.flag = false
        if (this.id) {
          this.flag = true
        }
        this.visible = true
        this.$nextTick(() => {
          console.log(this.$refs['dataForm'].resetFields())
          this.$refs['dataForm'].resetFields()
          this.id = 0
        })
        if (this.dataForm.incomeId) {
          this.$http({
            url: this.$http.adornUrl(`/serviceaccount/income/${this.dataForm.incomeId}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({data}) => {
            console.log(data)
            if (data && data.code === 20000) {
              this.dataForm.incomeId = data.data.item.incomeId
              this.dataForm.customerId = data.data.item.customerId
              this.dataForm.incomeTypeId = data.data.item.incomeTypeId
              this.dataForm.incomeAmount = data.data.item.incomeAmount
              this.dataForm.incomeDate = data.data.item.incomeDate
              this.dataForm.incomeStaff = data.data.item.incomeStaff
              this.dataForm.incomeInfo = data.data.item.incomeInfo
            }
          })
        }
      },
      // 表单提交
      dataFormSubmit () {
        console.log(this.id)
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/serviceaccount/income/${this.flag ? 'update' : 'save'}`),
  
              method: 'post',
              data: this.$http.adornData({
                'incomeId': this.dataForm.incomeId || undefined,
                'customerId': this.dataForm.customerId,
                'incomeTypeId': this.dataForm.incomeTypeId,
                'incomeAmount': this.dataForm.incomeAmount,
                'incomeDate': this.dataForm.incomeDate,
                'incomeStaff': this.dataForm.incomeStaff,
                'incomeInfo': this.dataForm.incomeInfo
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
