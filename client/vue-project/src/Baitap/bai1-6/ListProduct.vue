<template>
  <div>
    <h1>Danh sách sản phẩm</h1>
    <table>
      <thead>
        <tr>
          <th>Hình ảnh</th>
          <th>Tên sản phẩm</th>
          <th>Giá</th>
          <th>Số lượng</th>
          <th>Chi tiết</th>
          <th>Xóa</th>
          <th>Cập nhật</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="product in products" :key="product.id">
          <td><img :src="product.image" alt="product.name" class="product-image" /></td>
          <td>{{ product.name }}</td>
          <td>{{ product.price }} VNĐ</td>
          <td>{{ product.quantity }}</td>
          <td>
            <button @click="getProductById(product.id)">Get detail</button>
          </td>
          <td>
            <button @click="removeProductById(product.id)">Delete</button>
          </td>
          <td>
            <button @click="editProduct(product)">Edit</button>
          </td>
        </tr>
      </tbody>
    </table>

    <h2>Cập nhật sản phẩm</h2>
    <form @submit.prevent="updateProductById">
      <input v-model="selectedProduct.name" placeholder="Tên sản phẩm" required />
      <input v-model="selectedProduct.price" type="number" placeholder="Giá" required />
      <input v-model="selectedProduct.quantity" type="number" placeholder="Số lượng" required />
      <input v-model="selectedProduct.image" placeholder="URL hình ảnh" required />
      <button type="submit">Cập nhật sản phẩm</button>
    </form>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const products = ref([]);
const selectedProduct = ref({
  id: null,
  name: '',
  price: 0,
  quantity: 0,
  image: ''
});

const getAllProduct = async () => {
  try {
    const response = await fetch('http://localhost:8080/products');
    if (!response.ok) {
      throw new Error('Network response was not ok');
    }
    products.value = await response.json();
  } catch (error) {
    console.error('Error:', error);
  }
};

const getProductById = async (id) => {
  try {
    const response = await fetch(`http://localhost:8080/products/${id}`);
    if (!response.ok) {
      if (response.status === 404) {
        console.log("Không tìm thấy bản ghi");
      }
      throw new Error('Network response was not ok');
    }
    const product = await response.json();
    console.log(product); 
  } catch (error) {
    console.error('Error:', error);
  }
};

const removeProductById = async (id) => {
  try {
    const response = await fetch(`http://localhost:8080/products/${id}`, {
      method: 'DELETE'
    });
    if (!response.ok) {
      throw new Error('Network response was not ok');
    }
    await response.json();
    console.log("Sản phẩm đã được xóa");
    products.value = products.value.filter(product => product.id !== id);
  } catch (error) {
    console.error('Error:', error);
  }
};

const editProduct = (product) => {
  selectedProduct.value = { ...product }; 
};

const updateProductById = async () => {
  try {
    const response = await fetch(`http://localhost:8080/products/${selectedProduct.value.id}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(selectedProduct.value)
    });

    if (!response.ok) {
      throw new Error('Network response was not ok');
    }

    const updatedProduct = await response.json();
    console.log("Sản phẩm đã được cập nhật:", updatedProduct);
    
    const index = products.value.findIndex(product => product.id === updatedProduct.id);
    if (index !== -1) {
      products.value[index] = updatedProduct;
    }
    
    selectedProduct.value = { id: null, name: '', price: 0, quantity: 0, image: '' };

  } catch (error) {
    console.error('Error:', error);
  }
};

onMounted(getAllProduct);
</script>

<style scoped>
.product-image {
  width: 100px;  
  height: 100px; 
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