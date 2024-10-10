<template>
 <div>
  <h1>List product</h1>
  <form @submit.prevent="handleSubmit">
    <div>
      <label for="">Name</label>
      <input v-model="productInfo.name" type="text">
    </div>
    <div>
      <label for="">Price</label>
      <input v-model="productInfo.price" type="number">
    </div>
    <div>
      <label for="">Quantity</label>
      <input v-model="productInfo.quantity" type="number">
    </div>
    <button type="submit" @click="handleSubmit">Add</button>
  </form>
  <table border="1">
    <thead>
      <tr>
        <th>ID</th>
        <th>Name</th>
        <th>price</th>
        <th>quantity</th>
        <th>Option</th>
      </tr>
    </thead>
    <tbody v-for="product in products " :key="product.id">
      <tr>
        <td>{{ product.id }}</td>
        <td>{{ product.name }}</td>
        <td>{{ product.price }}</td>
        <td>{{ product.quantity }}</td>
        <td>
          <button>Edit</button>
          <button @click="handleDelete(product.id)">Delete</button>
        </td>
      </tr>
    </tbody>
  </table>
  <div v-if="isShowLoading">Loading....</div>
  <ListProduct></ListProduct>
  <ProductList></ProductList>
 </div>
</template>

<script setup>
import { onMounted, reactive, ref } from 'vue';

    const products = ref([]);
    const isShowLoading = ref(false);
    const loadData = async ()=>{
        // fetch("http://localhost:8080/product")
        // .then((res)=> res.json())
        // .then((data)=> console.log(data))
        // .catch((err)=> console.log(err));
        try {
          isShowLoading.value = true;
          const res = await fetch("http://localhost:8080/product");
          const data = await res.json();
          return data;
        } catch (error) {
          return error;
        } finally{
          // Tắt loading
          isShowLoading.value = false;
        }
    }

    onMounted( ()=>{
      loadData();
    })

    const productInfo = reactive({
      name: "",
      price: 0,
      quantity: 0
    })
    const handleSubmit = async ()=>{
      // validate dữ liệu
      // gọi api thêm mới dữ liệu
     const res = await fetch("http://localhost:8080/product", {
        method: "POST",
        headers:{
          "Content-type": "application/json",
        },
        body: JSON.stringify(productInfo),
      });
      console.log(res);
      loadData();
      // reset form 
    }
    const handleDelete = async (id)=>{
     const res = await fetch(`http://localhost:8080/product/${id}`, {
        method: "DELETE",
      });
      if(res.status === 200){
        loadData();
      }
    }
    import ListProduct from './Baitap/bai1-6/ListProduct.vue';
    import ProductList from './Baitap/bai7-8/ProductList.vue';
</script>
 
<style>

</style>