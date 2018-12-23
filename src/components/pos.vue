<template>
  <div class="pos">
      <el-row>
          <el-col :span='7' class="pos-order" id="order-list">
              <el-tabs>
                  <el-tab-pane label="点餐">
                      <el-table 
                        :data="tableData"
                         border 
                         style="width:100%">
                          <el-table-column prop='goodsName' label="商品名称" width='120'></el-table-column>
                          <el-table-column prop='count' label="数量" width='50'></el-table-column>
                          <el-table-column prop='price' label="金额" width='60'></el-table-column>
                          <el-table-column  label="操作" width='100' fixed="right">
                              <template scope="scope">
                                <el-button type='text' size='small'  @click='delSingleGoods(scope.row)'>删除</el-button>
                                <el-button type='text' size='small'@click="addOrderList(scope.row)">添加</el-button>
                              </template>
                          </el-table-column>
                      </el-table>
                      <div class="clearfix left total">
                          <span class="fl">数量：{{totalCount}}</span>
                         <span class="fr">金额：{{totalMoney}}</span> 
                      </div>
                      <div class="btn-foot">
                        <el-button type="warning" >挂单</el-button>
                        <el-button type="danger" @click='deleteAllGoods()'>删除</el-button>
                        <el-button type="success" @click='checkout()'>结账</el-button>
                      </div>
                  </el-tab-pane>   
                  <el-tab-pane label="挂单">
                    挂单
                  </el-tab-pane>   
                  <el-tab-pane label="外卖">
                    外卖
                  </el-tab-pane>   
              </el-tabs>
          </el-col>
          <el-col :span='17' class="center">
            <div class="often-goods">
                <div class="title">常用商品</div>
                <div class="often-goods-list">
                    <ul class="clearfix"> 
                      <li v-for="goods in oftenGoods" @click="addOrderList(goods)">
                        <span>{{goods.goodsName}}</span>
                        <span class="o-price">￥{{goods.price}}</span>
                      </li>
                    </ul>
                </div>
            </div>
             <!--right-footer-->
             <div class="goods-type">
                <el-tabs>
                    <el-tab-pane label="汉堡">
                      <div>
                        <ul class="cookList clearfix">
                          <li v-for="goods in type0Goods" @click="addOrderList(goods)">
                              <span class="foodImg"><img :src="goods.goodsImg" alt="我是图片" width="100%"></span>
                              <span class="foodName">{{goods.goodsName}}</span>
                              <span class="foodPrice">￥{{goods.price}}元</span>
                          </li>
                        </ul>
                      </div>
                    </el-tab-pane>
                    <el-tab-pane label="小吃">
                        <ul class="cookList clearfix">
                          <li v-for="goods in type0Goods1" @click="addOrderList(goods)">
                              <span class="foodImg"><img :src="goods.goodsImg" alt="我是图片"  width="100%"></span>
                              <span class="foodName">{{goods.goodsName}}</span>
                              <span class="foodPrice">￥{{goods.price}}元</span>
                          </li>
                        </ul>
                    </el-tab-pane>
                    <el-tab-pane label="软饮">
                      <ul class="cookList clearfix">
                          <li v-for="goods in type0Goods2" @click="addOrderList(goods)">
                              <span class="foodImg"><img :src="goods.goodsImg" alt="我是图片" width="100%"></span>
                              <span class="foodName">{{goods.goodsName}}</span>
                              <span class="foodPrice">￥{{goods.price}}元</span>
                          </li>
                        </ul>
                    </el-tab-pane>
                    <el-tab-pane label="套餐">
                      <ul class="cookList clearfix">
                          <li v-for="goods in type0Goods3" @click="addOrderList(goods)">
                              <span class="foodImg"><img :src="goods.goodsImg" alt="我是图片" width="100%"></span>
                              <span class="foodName">{{goods.goodsName}}</span>
                              <span class="foodPrice">￥{{goods.price}}元</span>
                          </li>
                        </ul>
                    </el-tab-pane>
                </el-tabs>
             </div>
          </el-col>
      </el-row>
  </div>
</template>
<script>
import axios from 'axios'
export default {
  name: 'pos',
  data(){
    return{
     tableData: []
    ,oftenGoods:[] 
    ,type0Goods:[]
    ,type0Goods1:[]
    ,type0Goods2:[]
    ,type0Goods3:[]
    ,totalMoney:0
    ,totalCount:0
    }
  },
  mounted:function(){
    let orderList = document.getElementById('order-list'),
        orderHeight = document.body.clientHeight;
        orderList.style.height = orderHeight+'px'
  },
  created:function(){
    axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods')
      .then(reponse=>{
         this.oftenGoods = reponse.data
      })
      .catch(error=>{
       alert('网络错误，不能访问')
      })
      axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods')
      .then(reponse=>{
        this.type0Goods = reponse.data[0]
        this.type0Goods1 = reponse.data[1]
        this.type0Goods2 = reponse.data[2]
        this.type0Goods3 = reponse.data[3]
      })
      .catch(error=>{
       alert('网络错误，不能访问')
      })
  },
  methods:{
      addOrderList(goods){
        this.totalMoney = 0;
        this.totalCount = 0;
        //商品是否存在订单列表
        let isHave = false
        for(let i = 0; i<this.tableData.length;i++){
            if(this.tableData[i].goodsId == goods.goodsId){
              isHave = true
            }
        }
        //根据判断的值编写业务逻辑
          if(isHave){
            let arr = this.tableData.filter(function(o){
              return o.goodsId == goods.goodsId
            })
            arr[0].count++
          }else{
            let newGoods ={
              goodsId:goods.goodsId,
              goodsName : goods.goodsName,
              price : goods.price,
              count:1
            }
            this.tableData.push(newGoods)
          }
          //计算金额和数量
          this.getAllMoney()
      },
      //删除单个商品
      delSingleGoods(goods){
        this.tableData = this.tableData.filter(function(o){
          return o.goodsId!= goods.goodsId  
        })
        //汇总数量和金额
        this.getAllMoney()
      },
      deleteAllGoods(){
          this.tableData= []
          this.totalMoney = 0;
          this.totalCount = 0;
      },
      //模拟结账
      checkout(){
        if(this.totalCount!=0){
            this.tableData = []
            this.totalMoney = 0
            this.totalCount = 0
            this.$message({
              message:'结账成功，感谢您为小店里出了一份力！',
              type:'success'
            })
        }else{
            this.$message.error('不能空结！请继续努力')
        }
      },
      getAllMoney(){
          this.totalMoney = 0;
          this.totalCount = 0;
          if(this.tableData){
                this.tableData.forEach((element)=>{
                  this.totalCount += element.count
                  this.totalMoney = this.totalMoney+(element.price*element.count)
                })
          }
      }
  }
}
</script>
<style scoped>
  .pos-order{
    text-align: center;
    background-color:#f9fafc;
    border-right: 1px solid #409EFF; 
  }
  .btn-foot{
    margin-top: 20px;
  }
   .title{
       height: 20px;
       border-bottom:1px solid #D3DCE6;
       background-color: #F9FAFC;
       padding:10px;
   }
   .often-goods-list ul li{
      list-style: none;
      float:left;
      border:1px solid #E5E9F2;
      padding:10px;
      margin:10px;
      background-color:#fff;
      cursor: pointer;
   }
   .goods-type{
     border-top:1px solid #b0c5e8;
     padding:0 10px;
     margin-top: 10px
   }
  .o-price{
      color:#58B7FF; 
   }
   .cookList li{
       list-style: none;
       width:23%;
       border:1px solid #E5E9F2;
       height: auot;
       overflow: hidden;
       background-color:#fff;
       padding: 2px;
       float:left;
       margin: 2px;
       cursor: pointer;
   }
   .cookList li span{
        display: block;
        float:left;
   }
   .cookList li span{
        display: block;
        float:left;
   }
   .foodImg{
       width: 40%;
       font-size: 0;
   }
   .foodName{
       font-size:16px;
       padding-left: 8px;
       color:brown;
   }
   .foodPrice{
       font-size: 14px;
       padding-left:8px;
       padding-top:3px;
   }
   .total{
     padding:10px;
     background: #fff;
     border-bottom:1px solid #cfcfcf; 
   }
</style>
