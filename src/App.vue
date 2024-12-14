

<script setup>
import { reactive, watch, computed } from 'vue';

  const data = reactive({
    isSelected: false,
    itemList: [{
      id: 0,
      name: '商品1',
      price: 1.0,
      amount: 1,
      number: 1,
    },
    {
      id: 2,
      name: '商品2',
      price: 2.0,
      amount: 2,
      number: 2,
    },
    {
      id: 0,
      name: '商品3',
      price: 3.0,
      amount: 3,
      number: 3,
    }
    ],
    selectedItemList: []
  })

  // totalPrice是函数，在插值表达式中要写成{{ totalPrice() }}
  const totalPrice = () => {
    console.log('计算总价格：');
    let total = 0;
    data.selectedItemList.forEach(item => {
      total += item.price * item.number;
    });
    console.log(total);
    return total;
  }


  const totalPrice2 = computed(() => {
    return data.selectedItemList.reduce((total, item) => total += item.price * item.amount, 0);
  })



  function selectAll() {
    if (data.isSelected) {
      data.selectedItemList = data.itemList;
    } else {
      data.selectedItemList = [];
    }
  }

  function sub(item) {
    if (item.number <= 0) {
      return;
    }
    item.number--;
  }

  function add(item) {
    if (item.number >= item.amount) {
      return;
    }
    item.number++;
  }
  
  function selectItem(item) {
    if (data.selectedItemList.length != 0 && data.selectedItemList.length === data.itemList.length) {
      data.isSelected = true;
    } else {
      data.isSelected = false;
    }
  }

  function deleteItem(index, name) {
    data.itemList.splice(index, 1);
    let newSelectedItemList = data.selectedItemList.filter((value) => {
      value.name != name;
    })
    data.selectedItemList = newSelectedItemList;
  }

  
  let flag = true;
  watch(() => data.isSelected, (newValue, oldValue) => {
    if (newValue) {
      data.selectedItemList = data.itemList;
    } else {
      if (flag) {
        data.selectedItemList = [];
      }
    }
  });

  watch(() => data.selectedItemList.length, (newValue, oldValue) => {
    // 引起全选框（所有框）状态发生变化的情况：
    // 1. 最后一个未被选中的选项被选中：
    // 2. 最后一个被选中的选中被取消选中
    if (newValue === 0) {
      data.isSelected = false;
      flag = true;
    } else if (newValue === data.itemList.length) {
      data.isSelected = true;
      flag = true;
    } else {
      // 这里设为false，会影响上面的监听器，需要另外增加一个标志位
      data.isSelected = false;
      flag = false;
    }
  })
</script>

<template>
  <table border="1">
    <thead>
      <tr>
        <!-- @change="selectAll()，用监听器代替change事件 -->
        <th><input type="checkbox" v-model="data.isSelected"></th>
        <th>名称</th>
        <th>单价</th>
        <th>库存</th>
        <th colspan="2">操作</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(item, index) in data.itemList">
        <!-- 这里的v-model必须要写，不然复选框不会被选中 -->
         <!-- 选中复选框，vue会将item添加到selectedItemList，取消选中，vue会将item从selectedItemList移除 -->

         <!-- @change="selectItem(item)"，用监听器代替change事件 -->
        <td><input type="checkbox" :value="item" v-model="data.selectedItemList"></td>
        <td>{{ item.name }}</td>
        <td>{{ item.price }}</td>
        <td>{{ item.amount }}</td>
        <td><input type="button" value="-" @click="sub(item)">{{ item.number }}<input type="button" value="+" @click="add(item)"></td>
        <td><input type="button" @click="deleteItem(index, item.name)" value="删除"></td>
        </tr>
    </tbody>
    <tfoot>
      <tr>
        <td colspan="6">总价格：{{ totalPrice2 }}</td>
      </tr>
    </tfoot>
  </table>

  <!-- <input type="checkbox" value="1">性别 -->
</template>

<style scoped>

</style>
