<template>
  <div class="bills-container">
    <h1>账单管理</h1>
    <el-form :inline="true" :model="billsVo" class="demo-form-inline">
      <el-form-item label="账单类型">
        <el-select v-model="billsVo.typeId" placeholder="请选择账单类型">
          <el-option v-for="item in billsType" :key="item.id"  :label="item.name" :value="item.id"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="开始时间">
            <el-date-picker
              v-model="billsVo.startTime"
              value-format="yyyy-MM-dd HH:mm:ss"
              type="datetime"
              placeholder="选择日期时间">
            </el-date-picker>
      </el-form-item>
       <el-form-item label="结束时间">
            <el-date-picker
              v-model="billsVo.endTime"
              type="datetime"
              value-format="yyyy-MM-dd HH:mm:ss"
              placeholder="选择日期时间">
            </el-date-picker>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">查询</el-button>
        <el-button type="danger" @click="openAddDialog">添加</el-button>
      </el-form-item>
    </el-form>
    <div class="table-bills-data">
      <el-table
      :data="billsData"
      border
      style="width: 100%">
      <el-table-column
        prop="id"
        label="ID">
      </el-table-column>
      <el-table-column
        prop="title"
        label="标题">
      </el-table-column>
       <el-table-column
        prop="typeName"
        label="分类名">
      </el-table-column>
      <el-table-column
        prop="billtime"
        label="日期">
      </el-table-column>
      <el-table-column
        prop="price"
        label="金额">
      </el-table-column>
      <el-table-column
        prop="remark"
        label="备注">
      </el-table-column>
    </el-table>

    <el-pagination
    style="margin-top:20px"
    background
    @current-change="handleCurrentChange"
    :page-size="billsVo.limit"
    layout="total, prev, pager, next"
    :total="dataGridView.count">
</el-pagination>
    </div>



    <el-dialog title="新增账单" :visible.sync="addDialog">
      <el-form :model="bills">
        <el-form-item label="账单类型">
          <el-radio-group v-model="bills.typeid">
            <el-radio v-for="item in billsType" :key="item.id"  :label="item.id">{{item.name}}</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="标题" >
          <el-input v-model="bills.title" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="日期" >
          <el-input v-model="bills.billtime" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="金额" >
          <el-input v-model="bills.price" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="备注" >
          <el-input   type="textarea" :rows="2" placeholder="请输入内容" v-model="bills.remark" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="addDialog = false">取 消</el-button>
        <el-button type="primary" @click="addBill">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script>
function dateFormat(fmt, date) {
    let ret;
    const opt = {
        "Y+": date.getFullYear().toString(),        // 年
        "m+": (date.getMonth() + 1).toString(),     // 月
        "d+": date.getDate().toString(),            // 日
        "H+": date.getHours().toString(),           // 时
        "M+": date.getMinutes().toString(),         // 分
        "S+": date.getSeconds().toString()          // 秒
        // 有其他格式化字符需求可以继续添加，必须转化成字符串
    };
    for (let k in opt) {
        ret = new RegExp("(" + k + ")").exec(fmt);
        if (ret) {
            fmt = fmt.replace(ret[1], (ret[1].length == 1) ? (opt[k]) : (opt[k].padStart(ret[1].length, "0")))
        }
    }
    return fmt;
}
var api ="http://localhost:8080/api"
import axios from "axios"
  export default {
    data() {
      return {
        billsData:[],
        billsVo: {
          typeid:null,
          startTime:"",
          endTime:"",
          page:1,
          limit:10
        },
        dataGridView:{
          count:100,
        },
        billsType:[],
        addDialog:false,
        bills:{
          title:'',
          typeid:'',
          price:'',
          remark:'',
          billtime:new Date()
        }
      }
    },
    created(){
      this.getBillsData();
      this.getBillsType();
    },
    methods: {
      onSubmit() {
        this.getBillsData()
      },
      getBillsData(){
        var that = this;
        axios({
          method:"post",
          url: api +"/bills/loadAllBills",
          data:that.billsVo
        })
        .then(res =>{
            that.dataGridView=res.data
            that.billsData=res.data.data
        })
      },
      getBillsType(){
        var that = this;
        axios({
            method: 'get',
            url: api+'/billsType/loadAllBillsType'
        })
        .then(function (response) {
            that.billsType=response.data.data
            // console.log(that.billsType)
        })
        .catch(function (error) {
            console.log(error);
        });
      },
       handleCurrentChange(val) {
        this.billsVo.page=val
        this.getBillsData()
      },
      openAddDialog(){
      this.addDialog=true
      },
      addBill(){
        var that = this;
        that.bills.billtime=dateFormat("YYYY-mm-dd HH:MM:SS",that.bills.billtime)
        axios({
          method:"post",
          url: api +"/bills/addBills",
          data:that.bills
        })
        .then(res =>{
            that.$message.success(res.data.msg)
            that.getBillsData()
            that.addDialog=false
        })
      }
    }
  }
</script>
<style scoped>
.bills-container{
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.table-bills-data{
  width: 1000px;
  height: 600px;
}
</style>