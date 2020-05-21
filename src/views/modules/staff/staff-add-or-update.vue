<template>
  <el-dialog
    :title="!dataForm.staffId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible"
  >
    <el-form
      :model="dataForm"
      :rules="dataRule"
      ref="dataForm"
      @keyup.enter.native="dataFormSubmit()"
      label-width="80px"
    >
      <el-form-item label="员工ID" prop="staffId">
        <el-input v-model="dataForm.staffId" placeholder="员工ID"></el-input>
      </el-form-item>
      <el-form-item label="员工姓名" prop="staffName">
        <el-input v-model="dataForm.staffName" placeholder="员工姓名"></el-input>
      </el-form-item>
      <el-form-item label="身份证" prop="staffIdcard">
        <el-input v-model="dataForm.staffIdcard" placeholder="身份证"></el-input>
      </el-form-item>
      <el-form-item label="员工所属部门" prop="staffEmp">
        <el-input v-model="dataForm.staffEmp" placeholder="员工所属部门"></el-input>
      </el-form-item>
      <el-form-item label="员工职位" prop="staffPosition">
        <el-input v-model="dataForm.staffPosition" placeholder="员工职位"></el-input>
      </el-form-item>
      <el-form-item label="员工入职日期" prop="staffDate">
        <el-input v-model="dataForm.staffDate" placeholder="员工入职日期"></el-input>
      </el-form-item>
      <el-form-item label="员工基本工资" prop="staffSalary">
        <el-input v-model="dataForm.staffSalary" placeholder="员工基本工资"></el-input>
      </el-form-item>
      <el-form-item label="员工奖金" prop="staffBonus">
        <el-input v-model="dataForm.staffBonus" placeholder="员工奖金"></el-input>
      </el-form-item>
      <el-form-item label="备注" prop="staffInfo">
        <el-input v-model="dataForm.staffInfo" placeholder="备注"></el-input>
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
      // staffList: [],
      dataForm: {
        staffId: '',
        staffName: '',
        staffIdcard: '',
        staffEmp: '',
        staffPosition: '',
        staffDate: '',
        staffSalary: 0,
        staffBonus: 0,
        staffInfo: ''
      },
      dataRule: {}
    }
  },
  methods: {
    init (id) {
      this.id = id
      this.dataForm.staffId = id || ''
      // this.$http({
      //   url: this.$http.adornUrl(`/servicestaff/hotel-staff/${id}`),
      //   method: 'get',
      //   params: this.$http.adornParams()
      // })
      //   .then(({ data }) => {
      //     console.log(data)
      //     this.staffList = data && data.code === 20000 ? data.data.item : []
      //   })
      //   .then(() => {
      this.visible = true
      this.$nextTick(() => {
        console.log(this.$refs['dataForm'].resetFields())
        this.$refs['dataForm'].resetFields()
      })
      // })
      // .then(() => {
      if (this.dataForm.staffId) {
        this.$http({
          url: this.$http.adornUrl(
            `/servicestaff/hotel-staff/${this.dataForm.staffId}`
          ),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({ data }) => {
          console.log(data)
          if (data && data.code === 20000) {
            this.dataForm.staffId = data.data.item.staffId
            this.dataForm.staffName = data.data.item.staffName
            this.dataForm.staffIdcard = data.data.item.staffIdcard
            this.dataForm.staffEmp = data.data.item.staffEmp
            this.dataForm.staffPosition = data.data.item.staffPosition
            this.dataForm.staffDate = data.data.item.staffDate
            this.dataForm.staffSalary = data.data.item.staffSalary
            this.dataForm.staffBonus = data.data.item.staffBonus
            this.dataForm.staffInfo = data.data.item.staffInfo
          }
        })
      }
    },
    // 表单提交
    dataFormSubmit () {
      this.$refs['dataForm'].validate(valid => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(
              `/servicestaff/hotel-staff/${
                !this.id ? 'save' : 'update'
              }`
            ),
            method: 'post',

            data: this.$http.adornData({
              'staffId': this.dataForm.staffId || undefined,
              'staffName': this.dataForm.staffName,
              'staffIdcard': this.dataForm.staffIdcard,
              'staffEmp': this.dataForm.staffEmp,
              'staffPosition': this.dataForm.staffPosition,
              'staffDate': this.dataForm.staffDate,
              'staffSalary': this.dataForm.staffSalary,
              'staffBonus': this.dataForm.staffBonus,
              'staffInfo': this.dataForm.staffInfo
            })
          }).then(({ data }) => {
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

<style>
</style>