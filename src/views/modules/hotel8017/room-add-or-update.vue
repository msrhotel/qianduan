<template>
  <el-dialog
    :title="!dataForm.roomNum ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="房间号码" prop="roomNum">
        <el-input v-model="dataForm.roomNum" placeholder="房间号码"></el-input>
      </el-form-item>
      <el-form-item label="房间类型" prop="roomType">
        <el-input v-model="dataForm.roomType" placeholder="房间类型"></el-input>
      </el-form-item>
      <el-form-item label="房间价格" prop="roomPrice">
        <el-input v-model="dataForm.roomPrice" placeholder="房间价格"></el-input>
      </el-form-item>
 
      <el-form-item label="房间状态" prop="roomState">
        <el-input v-model="dataForm.roomState" placeholder="房间状态"></el-input>
      </el-form-item>
      <el-form-item label="入住信息" prop="info">
        <el-input v-model="dataForm.info" placeholder="入住信息"></el-input>
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
        cutomerList: [],
        dataForm: {
          roomNum: '',
          roomType: 0,
          roomPrice: 0,
          roomState: '',
          info: ''
        },
        dataRule: {
  
        }
      }
    },
    methods: {
      init (roomNum) {
        this.roomNum = roomNum
        this.dataForm.roomNum = roomNum || ''
        // this.$http({
        //   url: this.$http.adornUrl(`/serviceroom/hotel-room/${roomNum}`),
        //   method: 'get',
        //   params: this.$http.adornParams()
        // }).then(({data}) => {
        //   console.log(data)
        //   this.cutomerList = data && data.code === 20000 ? data.data.item : []
        // }).then(() => {
        this.visible = true
        this.$nextTick(() => {
          console.log(this.$refs['dataForm'].resetFields())
          this.$refs['dataForm'].resetFields()
        })
        if (this.dataForm.roomNum) {
          this.$http({
            url: this.$http.adornUrl(`/serviceroom/hotel-room/${this.dataForm.roomNum}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({data}) => {
            console.log(data)
            if (data && data.code === 20000) {
              this.dataForm.roomNum = data.data.item.roomNum
              this.dataForm.roomType = data.data.item.roomType
              this.dataForm.roomPrice = data.data.item.roomPrice
              this.dataForm.roomState = data.data.item.roomState
              this.dataForm.info = data.data.item.info
            }
          })
        }
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/serviceroom/hotel-room/${!this.roomNum ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'roomNum': this.dataForm.roomNum || undefined,
                'roomType': this.dataForm.roomType,
                'roomPrice': this.dataForm.roomPrice,
                'roomState': this.dataForm.roomState,
                'info': this.dataForm.info
  
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
