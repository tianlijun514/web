<!-- vue快捷创建组件 -->
<template>
  <div class='APPX'>
    <el-form :inline="true" class="demo-form-inline">

      <el-form-item label="收货部门">
        <el-input v-model="shDeptCode"></el-input>
      </el-form-item>

      <span class="demonstration">发货日期</span>
      <el-date-picker v-model="value1" @change='datas' type="daterange" range-separator="至" start-placeholder="开始日期" end-placeholder="结束日期" format="yyyy-MM-dd">
      </el-date-picker>

      <el-form-item style="margin-left: 10px">
        <el-button type="primary" @click="chaxun">查询</el-button>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="herleiten">Excel导出</el-button>
      </el-form-item>
    </el-form>
    <span class="searchRst">查询结果：共{{total}}条记录/显示第{{currentPage}}页</span>
    <el-table :data="tableData" border style="width: 100%">
      <el-table-column prop="spCkId" label="出库单号" width="120"></el-table-column>
      <el-table-column prop="spCk.fhDeptName" label="发货部门" width="120"></el-table-column>
      <el-table-column prop="spCk.fhDate" label="发货日期" width="120"></el-table-column>
      <el-table-column prop="spCk.shDeptName" label="收货部门" width="120"></el-table-column>
      <el-table-column prop="spCode" label="商品编号" width="120"></el-table-column>
      <el-table-column prop="spName" label="商品名称"></el-table-column>
      <el-table-column prop="spUnit" label="单位" width="120"></el-table-column>
      <el-table-column prop="spNum" label="数量" width="120"></el-table-column>
      <el-table-column prop="spCk.empName" label="操作员" width="120"></el-table-column>
    </el-table>
    <div class="uys">
      <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="currentPage" :page-sizes="[10, 20, 30, 40]" :page-size="10" layout="total, sizes, prev, pager, next, jumper" :total="total">
      </el-pagination>
    </div>
  </div>

</template>

<script>
// 这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
import axios from "axios";
import { base } from '../js/url'
export default {
  name: 'DeliveryProductSummary',
  props: {

  },
  // import引入的组件需要注入到对象中才能使用
  components: {

  },
  data () {
    // 这里存放数据
    return {
      currentPage: 1,
      total: 0,
      size: 10,
      shDeptCode: '',
      value1: '',
      date_s: '',
      date_e: '',
      tableData: [{
        date: '2016-05-02',
        name: '王小虎',
        province: '上海',
        city: '普陀区',
        address: '上海市普陀区金沙江路 1518 弄',
        zip: 200333
      }]
    }
  },
  // 监听属性 类似于data概念
  computed: {},
  // 监控data中的数据变化
  watch: {},
  // 方法集合
  methods: {
    handleSizeChange (val) {
      console.log(`每页 ${val} 条`);
      this.size = val;
    },
    handleCurrentChange (val) {
      console.log(`当前页: ${val}`);
      this.currentPage = val;
      this.chaxun();
    },

    datas (even) {
      var mydata = even[0]
      var y = mydata.getFullYear();
      var m = mydata.getMonth() + 1;
      var d = mydata.getDate();
      this.date_s = y + "-" + m + "-" + d;
      var enddata = even[1]
      var y = enddata.getFullYear();
      var m = enddata.getMonth() + 1;
      var d = enddata.getDate();
      this.date_e = y + "-" + m + "-" + d;
    },
    // 导出
    herleiten () {
      axios
        .get(base + '/visit/excel', { responseType: 'arraybuffer' })
        .then(res => {
          let blob = new Blob([res.data], { type: "application/vnd.ms-excel" });
          let objectUrl = URL.createObjectURL(blob);
          window.location.href = objectUrl;
        })
    },

    // 查询
    chaxun () {
      axios
        .post(base + '/commodity/getAllSpCkInfo', {
          shDeptCode: this.shDeptCode,
          page: this.currentPage,
          size: this.size,
          startDate: this.date_s,
          endDate: this.date_e
        }).then(res => {
          this.tableData = res.data.d;
          this.total = res.data.t;
        })
    },
    handleClick (row) {
      console.log(row);
    }
  },
  // 生命周期 - 创建完成（可以访问当前this实例）
  created () {
    this.chaxun()
  },
  // 生命周期 - 挂载完成（可以访问DOM元素）
  mounted () {

  },
  beforeCreate () { }, // 生命周期 - 创建之前
  beforeMount () { }, // 生命周期 - 挂载之前
  beforeUpdate () { }, // 生命周期 - 更新之前
  updated () { }, // 生命周期 - 更新之后
  beforeDestroy () { }, // 生命周期 - 销毁之前
  destroyed () { }, // 生命周期 - 销毁完成
  activated () { } // 如果页面有keep-alive缓存功能，这个函数会触发
}
</script>
<style scoped>
@import './../../assets/css/table.css';
.uys {
    width: 32%;
    margin: auto;
}
.APPX {
    width: 100%;
}
</style>
