<template>
  <div class="pos">
     <el-row >
        <el-col :span='7' class="pos-order" id="order-list">
          <el-tabs>
            <el-tab-pane label="点餐">
              <el-table :data="tableData" border style="width: 100%" >
                <el-table-column prop="goodsName" label="商品"  ></el-table-column>
                <el-table-column prop="count" label="量" width="50"></el-table-column>
                <el-table-column prop="price" label="金额" width="70"></el-table-column>
                <el-table-column  label="操作" width="100" fixed="right">
                  <template slot-scope="scope">
                      <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                      <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                  </template>
                </el-table-column>
              </el-table>  
              <div class="total-div">
                <small>价格：</small>{{totalMoney}}&nbsp;&nbsp;&nbsp;<small>数量：</small>{{totalCount}}
              </div> 
              <div class="div-btn">
                <el-button type="warning" >挂单</el-button>
                <el-button type="danger" @click="delAllGoods()">删除</el-button>
                <el-button type="success" @click="checkout()">结账</el-button>
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
        <!--商品展示-->
        <el-col :span="17">
          <div class="often-goods">
            <div class="title">常用商品</div>
            <div class="often-goods-list">
                <ul>
                    <li v-for="(goods,index) in oftenGoods" :key="index" @click="addOrderList(goods)">
                        <span>{{goods.goodsName}}</span>
                        <span class="o-price">￥{{goods.price}}元</span>
                    </li>
                </ul>
            </div>
          </div>
          <div class="goods-type">
            <el-tabs>
                <el-tab-pane label="汉堡">
                  <ul class='cookList'>
                    <li v-for="(goods,index) in type0Goods" :key="index" @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                  </ul>
                </el-tab-pane>
                    <el-tab-pane label="小食">
                    <ul class='cookList'>
                      <li v-for="(goods,index) in type1Goods" :key="index" @click="addOrderList(goods)">
                        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                        <span class="foodName">{{goods.goodsName}}</span>
                        <span class="foodPrice">￥{{goods.price}}元</span>
                      </li>
                    </ul>
                </el-tab-pane>
                <el-tab-pane label="饮料">
                  <ul class='cookList'>
                    <li v-for="(goods,index) in type2Goods" :key="index" @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                  </ul>
                </el-tab-pane>
                <el-tab-pane label="套餐">
                  <ul class='cookList'>
                    <li v-for="(goods,index) in type3Goods" :key="index" @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
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
import axios from "axios";
export default {
  name: "pos",
  created() {
    axios
      .get("http://jspang.com/DemoApi/oftenGoods.php")
      .then(response => {
        console.log(response);
        this.oftenGoods = response.data;
      })
      .catch(error => {
        console.log(error);
        alert("网络错误，不能访问");
      });

    //读取分类商品列表
    axios
      .get("http://jspang.com/DemoApi/typeGoods.php")
      .then(response => {
        console.log(response);
        this.type0Goods = response.data[0];
        this.type1Goods = response.data[1];
        this.type2Goods = response.data[2];
        this.type3Goods = response.data[3];
      })
      .catch(error => {
        console.log(error);
        alert("网络错误，不能访问");
      });
  },
  //虚拟dom加载完
  mounted: function() {
    var orderHeight = document.body.clientHeight;
    document.getElementById("order-list").style.height = orderHeight + "px";
  },
  data() {
    return {
      tableData: [],
      oftenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      totalMoney: 0,
      totalCount: 0
    };
  },
  methods: {
    //添加订单列表的方法
    addOrderList(goods) {
      let isHave = false;
      //判断是否这个商品已经存在于订单列表
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId == goods.goodsId) {
          isHave = true;
        }
      }
      //根据isHave的值判断订单列表中是否已经有此商品
      if (isHave) {
        //存在就进行数量累加
        let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
        arr[0].count++;
      } else {
        //不存在就推入数组
        let newGoods = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          price: goods.price,
          count: 1
        };
        this.tableData.push(newGoods);
      }
      this.getAllGoods();
    },
    delSingleGoods(goods) {
      this.tableData = this.tableData.filter(o => o.goodsId != goods.goodsId);
      this.getAllGoods();
    },
    //进行数量和价格的汇总计算
    getAllGoods() {
      this.totalMoney = 0;
      this.totalCount = 0;
      this.tableData.forEach(elememt => {
        this.totalCount += elememt.count;
        this.totalMoney += elememt.price * elememt.count;
      });
    },
    //删除所有的商品
    delAllGoods() {
      this.tableData = [];
      this.totalMoney = 0;
      this.totalCount = 0;
    },
    //结账
    checkout() {
      if (this.totalCount != 0) {
        this.tableData = [];
        this.totalMoney = 0;
        this.totalCount = 0;
        this.$message({
          message: "结账成功，感谢你又为店里出了一份力!",
          type: "success"
        });
      } else {
        this.$message.error("不能空结。老板了解你急切的心情！");
      }
    }
  }
};
</script>

<style scoped>
.pos-order {
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
}
.div-btn {
  margin-top: 20px;
  text-align: center;
}

.title {
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
}
.often-goods-list ul li {
  list-style: none;
  float: left;
  border: 1px solid #e5e9f2;
  padding: 10px;
  margin: 5px;
  background-color: #fff;
  cursor: pointer;
}
.o-price {
  color: #58b7ff;
}
.goods-type {
  clear: both;
}
.cookList li {
  list-style: none;
  width: 23%;
  border: 1px solid #e5e9f2;
  height: auot;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
  cursor: pointer;
}
.cookList li span {
  display: block;
  float: left;
}
.foodImg {
  width: 40%;
}
.foodName {
  font-size: 16px;
  padding-left: 10px;
  color: brown;
}
.foodPrice {
  font-size: 16px;
  padding-left: 10px;
  padding-top: 10px;
}
.total-div {
  background-color: #fff;
  padding: 10px;
  border-bottom: 1px solid #ccc;
  text-align: center;
}
</style>
