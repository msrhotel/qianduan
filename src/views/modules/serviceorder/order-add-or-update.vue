<template>
  <el-dialog :title="!id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form
      :model="dataForm"
      ref="dataForm"
      @keyup.enter.native="dataFormSubmit()"
      label-width="80px"
    >
      <el-form-item label="订单ID" prop="orderId">
        <el-input v-model="dataForm.orderId" placeholder="订单ID"></el-input>
      </el-form-item>
      <el-form-item label="身份证号" prop="customerId">
        <!-- <el-input v-model="dataForm.customerId" placeholder="身份证号"></el-input>  -->
        <el-select v-model="dataForm.customerId" placeholder="身份证号">
          <el-option
            v-for="item in slist"
            :key="item.value"
            :label="item.label+' '+item.value"
            :value="item.value"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="入住天数" prop="orderDays">
        <el-input v-model="dataForm.orderDays" placeholder="入住天数"></el-input>
      </el-form-item>
      <el-form-item label="客房类型" prop="orderType">
        <el-select v-model="dataForm.orderType" placeholder="客房类型">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="预订入住日期" prop="inDate">
        <el-input v-model="dataForm.inDate" type="Date" placeholder="预定入住日期"></el-input>
      </el-form-item>
      <el-form-item label="预订退房时间" prop="outDate">
        <el-input v-model="dataForm.outDate" type="Date" placeholder="预定退房时间"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
import cusinfo from '@/views/modules/customerinfo/cusinfo'
export default {
  components: { cusinfo },
  data () {
    return {
      id: 0,
      visible: false,
      dataForm: {
        orderId: '',
        customerId: 0,
        orderDays: 0,
        orderType: 0,
        inDate: '',
        outDate: ''
      },
      slist: [
        {
          value: 111111111222222222,
          label: '王二'
        },
        {
          value: 123456123456123456,
          label: '李四'
        },
        {
          value: 123456789123456789,
          label: '张三'
        },
        {
          value: 444555666777888999,
          label: '大西瓜'
        }
      ],
      options: [
        {
          value: 1,
          label: '单人间'
        },
        {
          value: 2,
          label: '双人间'
        },
        {
          value: 3,
          label: '总统套房'
        }
      ],
      dataRule: {
        //     userName: [
        //       { required: true, message: '用户名不能为空', trigger: 'blur' }
        //     ],
        //     password: [
        //       { validator: validatePassword, trigger: 'blur' }
        //     ],
        //     comfirmPassword: [
        //       { validator: validateComfirmPassword, trigger: 'blur' }
        //     ],
        //     email: [
        //       { required: true, message: '邮箱不能为空', trigger: 'blur' },
        //       { validator: validateEmail, trigger: 'blur' }
        //     ],
        //     mobile: [
        //       { required: true, message: '手机号不能为空', trigger: 'blur' },
        //       { validator: validateMobile, trigger: 'blur' }
        //     ]
      }
    }
  },
  methods: {

    init (id) {
      this.id = id
      this.dataForm.orderId = id || ''
      this.visible = true
      // this.$http({
      //   url: this.$http.adornUrl(`/servicecustomerinfo/hotel-customerinfo/list`),
      //   method: 'get',
      //   params: this.$http.adornParams({})
      // }).then(({data}) => {
      //   this.slist = data.data.rows
      //   console.log(data)
      //   this.customerId = this.slist.customerId
      //   this.customerName = this.slist.customerName
      // })
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
      })
      if (this.dataForm.orderId) {
        this.$http({
          url: this.$http.adornUrl(
            `/serviceorder/order/${this.dataForm.orderId}`
          ),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({ data }) => {
          if (data && data.code === 20000) {
            this.dataForm.orderId = data.data.item.orderId
            this.dataForm.customerId = data.data.item.customerId
            this.dataForm.orderDays = data.data.item.orderDays
            this.dataForm.orderType = data.data.item.orderType
            this.dataForm.inDate = data.data.item.inDate
            this.dataForm.outDate = data.data.item.outDate
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
            `/serviceorder/order/${!this.id ? 'save' : 'update'}`
          ),
            method: 'post',
            data: this.$http.adornData({
              orderId: this.dataForm.orderId || undefined,
              customerId: this.dataForm.customerId,
              orderDays: this.dataForm.orderDays,
              orderType: this.dataForm.orderType,
              inDate: this.dataForm.inDate,
              outDate: this.dataForm.outDate
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