<template>
  <el-dialog
    :title="!dataForm.consumeId ? '新增或修改' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="消费ID" prop="consumeId">
        <el-input v-model="dataForm.consumeId" placeholder="消费ID"></el-input>
      </el-form-item>
      <el-form-item label="客户ID" prop="customerId">
        <el-input v-model="dataForm.customerId" placeholder="客户ID"></el-input>
      </el-form-item>
      <el-form-item label="消费类型" prop="consumeTypeId">
        <el-input v-model="dataForm.consumeTypeId" placeholder="消费类型"></el-input>
      </el-form-item>
      <el-form-item label="消费金额" prop="consumeAmount">
        <el-input v-model="dataForm.consumeAmount" placeholder="消费金额"></el-input>
      </el-form-item>
    
      <el-form-item label="操作员" prop="consumeStaff">
        <el-input v-model="dataForm.consumeStaff" placeholder="操作员"></el-input>
      </el-form-item>
      
      <el-form-item label="备注" prop="consumeInfo">
        <el-input v-model="dataForm.consumeInfo" placeholder="备注"></el-input>
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
        consumeList: [],
        dataForm: {
          consumeId: '',
          customerId: '',
          consumeTypeId: '',
          consumeAmount: 0,
          consumeDate: 0,
          consumeStaff: '',
          consumeInfo: ''
        },
        dataRule: {
  
        }
      }
    },
    methods: {
      init (id) {
        this.id = id
        this.dataForm.consumeId = id || 0
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
        if (this.dataForm.consumeId) {
          this.$http({
            url: this.$http.adornUrl(`/serviceaccount/consume/${this.dataForm.consumeId}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({data}) => {
            console.log(data)
            if (data && data.code === 20000) {
              this.dataForm.consumeId = data.data.item.consumeId
              this.dataForm.customerId = data.data.item.customerId
              this.dataForm.consumeTypeId = data.data.item.consumeTypeId
              this.dataForm.consumeAmount = data.data.item.consumeAmount
              this.dataForm.consumeDate = data.data.item.consumeDate
              this.dataForm.consumeStaff = data.data.item.consumeStaff
              this.dataForm.consumeInfo = data.data.item.consumeInfo
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
              url: this.$http.adornUrl(`/serviceaccount/consume/${this.flag ? 'update' : 'save'}`),
  
              method: 'post',
              data: this.$http.adornData({
                'consumeId': this.dataForm.consumeId || undefined,
                'customerId': this.dataForm.customerId,
                'consumeTypeId': this.dataForm.consumeTypeId,
                'consumeAmount': this.dataForm.consumeAmount,
                'consumeDate': this.dataForm.consumeDate,
                'consumeStaff': this.dataForm.consumeStaff,
                'consumeInfo': this.dataForm.consumeInfo
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
