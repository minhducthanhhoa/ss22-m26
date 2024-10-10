<template>
    <div>
      <h1>Danh sách sản phẩm</h1>
      <table>
        <thead>
          <tr>
            <th>#</th>
            <th>Tên sản phẩm</th>
            <th>Hình ảnh</th>
            <th>Giá</th>
            <th>Số lượng (kg)</th>
            <th>Ngày thêm</th>
            <th>Chức năng</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(product, index) in products" :key="product.id">
            <td>{{ index + 1 }}</td>
            <td>{{ product.name }}</td>
            <td><img :src="product.image" alt="product.name" class="product-image" /></td>
            <td>{{ product.price }} VNĐ</td>
            <td>{{ product.quantity }}</td>
            <td>{{ product.dateAdded }}</td>
            <td>
              <button @click="editProduct(product)">Sửa</button>
              <button @click="confirmDelete(product.id)">Xóa</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  
  const products = ref([]);
  
  const getAllProducts = async () => {
    try {
      const response = await fetch('http://localhost:8080/products2');
      if (!response.ok) {
        throw new Error('Network response was not ok');
      }
      products.value = await response.json();
    } catch (error) {
      console.error('Error:', error);
    }
  };
  
  const confirmDelete = (id) => {
    const confirmed = confirm('Bạn có chắc chắn muốn xóa sản phẩm này?');
    if (confirmed) {
      removeProductById(id);
    }
  };
  
  const removeProductById = async (id) => {
    try {
      const response = await fetch(`http://localhost:8080/products2/${id}`, {
        method: 'DELETE'
      });
      if (!response.ok) {
        throw new Error('Network response was not ok');
      }
      products.value = products.value.filter(product => product.id !== id);
      console.log('Sản phẩm đã được xóa');
    } catch (error) {
      console.error('Error:', error);
    }
  };
  
  onMounted(getAllProducts);
  </script>
  
  <style scoped>
  .product-image {
    width: 50px;  
    height: 50px; 
    object-fit: cover; 
  }
  
  table {
    width: 100%;
    border-collapse: collapse;
  }
  
  th, td {
    border: 1px solid #ddd;
    padding: 10px;
    text-align: left;
  }
  
  th {
    background-color: #f2f2f2;
  }
  </style>