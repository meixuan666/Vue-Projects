<template>
  <div class="order-wrap">
    <h3>您的产品</h3>
    <div class="order-list-choose">
      <div class="order-list-option">
        选择产品：
        <v-selection :selections="products" @on-change="productChange"></v-selection>
      </div>

      <div class="order-list-option">
        开始日期：
        <v-date-picker :value='startDate' @change="changeStartDate"></v-date-picker>
      </div>

      <div class="order-list-option">
        结束日期：
        <v-date-picker @change="changeEndDate"></v-date-picker>
      </div>

      <div class="order-list-option">
        关键词：
        <input type="text" v-model.lazy="query" class="order-query">
        <!-- lazy，光标不在输入框内时绑定，即改变全部结束调用 -->
      </div>
    </div>
    <div class="order-list-table">
      <table>
        <tr>
          <th v-for="head in tableHeads" @click="changeOrder(head)" :class="{active:head.active}">{{head.label}}</th>
        </tr>
        <tr v-for="item in tableData">
          <td v-for="head in tableHeads">{{item[head.key]}}</td>
        </tr>
      </table>
    </div>

  </div>
</template>
<script>
import VSelection from '../components/base/selection'
import VDatePicker from '../components/base/datepicker'
import _ from 'lodash'

export default {
    components:{
        VSelection,
        VDatePicker
    },
    data(){
        return {
      query:'',
      productId:0,
      startDate:'',
      endDate:'',
      tableData:[],
      currentOrder:'asc',
      products: [
        {
          label: '数据统计',
          value: 0
        },
        {
          label: '数据预测',
          value: 1
        },
        {
          label: '流量分析',
          value: 2
        },
        {
          label: '广告发布',
          value: 3
        }
      ],
      tableHeads: [
        {
          label: '订单号',
          key: 'orderId'
        },
        {
          label: '购买产品',
          key: 'product'
        },
        {
          label: '版本类型',
          key: 'version'
        },
        {
          label: '有效时间',
          key: 'period'
        },
        {
          label: '购买日期',
          key: 'date'
        },
        {
          label: '数量',
          key: 'buyNum'
        },
        {
          label: '总价',
          key: 'amount'
        }
      ],

    }
  },
  methods:{
    productChange(obj){
      // console.log(obj)
    this.productId = obj.value
    this.getTableData()

    },
    changeStartDate(date){
      this.startDate = date
      this.getTableData()
    },
    changeEndDate(date){
      this.endDate = date
      this.getTableData()
    },
    getTableData(){
      let reqParams = {
        query: this.query,
        productId: this.productId,
        startDate: this.startDate,
        endDate: this.endDate,
        bankId:this.bankId
      }
      //发送请求获取数据
      this.$http.post('/api/getOrderList',reqParams)
      .then((res)=>{
        this.tableData = res.data.list
        console.log(this.tableData)
      },(err)=>{
        console.log(err)
      })
    },
    //给数组排序
    changeOrder(headItem){
      //点击按钮时，当前高亮，其他常灰
      this.tableHeads.map((item)=>{
        item.active = false
        return item
      })
      headItem.active = true

      if (this.currentOrder === 'asc' ) {
        this.currentOrder = 'desc'
      }
       else if(this.currentOrder === 'desc'){
        this.currentOrder = 'asc'
      }
      console.log(headItem)
      //orderBy(排序的数组，排序的依据，正序/反序)
      this.tableData = _.orderBy(this.tableData,headItem.key,this.currentOrder)
    }
  },
  // 当关键字改变时执行一个方法
  watch:{
    query(){
    this.getTableData()
    }
  },
  mounted(){
    this.getTableData()
  }
};
</script>


<style>
.order-wrap {
  width: 1200px;
  min-height: 800px;
  margin: 20px auto;
  overflow: hidden;
}
.order-wrap h3 {
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 20px;
}
.order-query {
  height: 25px;
  line-height: 25px;
  border: 1px solid #e3e3e3;
  outline: none;
  text-indent: 10px;
}
.order-list-option {
  display: inline-block;
  padding-left: 15px;
}
.order-list-option:first-child {
  padding-left: 0;
}
.order-list-table {
  margin-top: 20px;
}
.order-list-table table {
  width: 100%;
  background: #fff;
}
.order-list-table td,
.order-list-table th {
  border: 1px solid #e3e3e3;
  text-align: center;
  padding: 10px 0;
}
.order-list-table th {
  background: #4fc08d;
  color: #fff;
  border: 1px solid #4fc08d;
  cursor: pointer;
}
.order-list-table th.active {
  background: #35495e;
}
</style>