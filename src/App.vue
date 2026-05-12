<script setup>
import { ref, computed } from 'vue'
import ProductCard from './components/ProductCard.vue'
import CartSummary from './components/CartSummary.vue'

const products = ref([
  { 
    id: 1, name: 'Bàn phím cơ AKKO', price: 1250000, inStock: true, 
    image: 'https://encrypted-tbn3.gstatic.com/shopping?q=tbn:ANd9GcR9mPFT95QQGPDn-5Zyi2w0Pz02FGLE9SMQ_LOZ_Dbj70k6JdVZ8AExNHNrHj9iHXWMwFldxz45u0vzjGDGg6GwZkI06ktCLl1uT_HYZi14xdQO-fM9AoYq' 
  },
  { 
    id: 2, name: 'Chuột Logitech G Pro', price: 2990000, inStock: true, 
    image: 'https://encrypted-tbn3.gstatic.com/shopping?q=tbn:ANd9GcR9mPFT95QQGPDn-5Zyi2w0Pz02FGLE9SMQ_LOZ_Dbj70k6JdVZ8AExNHNrHj9iHXWMwFldxz45u0vzjGDGg6GwZkI06ktCLl1uT_HYZi14xdQO-fM9AoYq' 
  },
  { 
    id: 3, name: 'Tai nghe HyperX Cloud II', price: 1850000, inStock: false, 
    image: 'https://encrypted-tbn3.gstatic.com/shopping?q=tbn:ANd9GcR9mPFT95QQGPDn-5Zyi2w0Pz02FGLE9SMQ_LOZ_Dbj70k6JdVZ8AExNHNrHj9iHXWMwFldxz45u0vzjGDGg6GwZkI06ktCLl1uT_HYZi14xdQO-fM9AoYq' 
  },
  { 
    id: 4, name: 'Lót chuột Razer Gigantus', price: 450000, inStock: true, 
    image: 'https://encrypted-tbn3.gstatic.com/shopping?q=tbn:ANd9GcR9mPFT95QQGPDn-5Zyi2w0Pz02FGLE9SMQ_LOZ_Dbj70k6JdVZ8AExNHNrHj9iHXWMwFldxz45u0vzjGDGg6GwZkI06ktCLl1uT_HYZi14xdQO-fM9AoYq' 
  },
  { 
    id: 6, name: 'Webcam Logitech C922', price: 2150000, inStock: true, 
    image: 'https://encrypted-tbn3.gstatic.com/shopping?q=tbn:ANd9GcR9mPFT95QQGPDn-5Zyi2w0Pz02FGLE9SMQ_LOZ_Dbj70k6JdVZ8AExNHNrHj9iHXWMwFldxz45u0vzjGDGg6GwZkI06ktCLl1uT_HYZi14xdQO-fM9AoYq' 
  }
])

const cartItems = ref([])

// --- CÁC STATE CHO TÌM KIẾM VÀ LỌC ---
const searchQuery = ref('')
const filterStatus = ref('all') // 'all', 'inStock', 'outOfStock'
const sortPrice = ref('default') // 'default', 'asc', 'desc'

// --- COMPUTED: DANH SÁCH SẢN PHẨM SAU KHI LỌC VÀ SẮP XẾP ---
const displayedProducts = computed(() => {
  let result = products.value

  // 1. Tìm kiếm theo tên (không phân biệt hoa thường)
  if (searchQuery.value.trim() !== '') {
    const query = searchQuery.value.toLowerCase()
    result = result.filter(p => p.name.toLowerCase().includes(query))
  }

  // 2. Lọc theo trạng thái
  if (filterStatus.value === 'inStock') {
    result = result.filter(p => p.inStock)
  } else if (filterStatus.value === 'outOfStock') {
    result = result.filter(p => !p.inStock)
  }

  // 3. Sắp xếp theo giá (cần tạo mảng mới bằng [...result] để không lỗi vue strict mode)
  if (sortPrice.value === 'asc') {
    result = [...result].sort((a, b) => a.price - b.price)
  } else if (sortPrice.value === 'desc') {
    result = [...result].sort((a, b) => b.price - a.price)
  }

  return result
})

// --- LOGIC GIỎ HÀNG ---
const handleAddToCart = (product) => {
  const existingItem = cartItems.value.find(item => item.id === product.id)
  if (existingItem) {
    existingItem.quantity++
  } else {
    cartItems.value.push({ ...product, quantity: 1 })
  }
}

// Hàm mới: Nhận sự kiện từ CartSummary để tăng/giảm số lượng
const handleUpdateQuantity = (id, change) => {
  const existingItem = cartItems.value.find(item => item.id === id)
  if (existingItem) {
    existingItem.quantity += change
    
    // Nếu số lượng tụt xuống 0 hoặc bé hơn, xóa luôn khỏi giỏ
    if (existingItem.quantity <= 0) {
      cartItems.value = cartItems.value.filter(item => item.id !== id)
    }
  }
}
</script>

<template>
  <div class="app-wrapper">
    <div class="app-container">
      <header class="app-header">
        <h1>Cửa hàng của Trường</h1>
        <p class="subtitle">Chuyên thiết bị Gaming Gear</p>
      </header>

      <div class="filters-container">
        <input 
          v-model="searchQuery" 
          type="text" 
          placeholder="🔍 Tìm kiếm sản phẩm..." 
          class="filter-input"
        />
        <select v-model="filterStatus" class="filter-select">
          <option value="all">Tất cả trạng thái</option>
          <option value="inStock">Còn hàng</option>
          <option value="outOfStock">Hết hàng</option>
        </select>
        <select v-model="sortPrice" class="filter-select">
          <option value="default">Sắp xếp: Mặc định</option>
          <option value="asc">Giá: Thấp đến cao</option>
          <option value="desc">Giá: Cao đến thấp</option>
        </select>
      </div>
      
      <div class="product-list" v-if="displayedProducts.length > 0">
        <ProductCard 
          v-for="product in displayedProducts" 
          :key="product.id"
          :product="product"
          @add-to-cart="handleAddToCart"
        />
      </div>
      <div v-else class="no-products">
        <p>Không tìm thấy sản phẩm nào phù hợp với bộ lọc.</p>
      </div>

      <CartSummary 
        :items="cartItems" 
        @update-quantity="handleUpdateQuantity"
      />
      
    </div>
  </div>
</template>

<style scoped>
.app-wrapper {
  min-height: 100vh;
  background-color: #f3f4f6;
  padding: 40px 20px;
}

.app-container {
  max-width: 900px;
  margin: 0 auto;
  font-family: 'Inter', system-ui, -apple-system, sans-serif;
}

.app-header {
  text-align: center;
  margin-bottom: 30px;
}

.app-header h1 {
  font-size: 2.2rem;
  color: #111827;
  margin-bottom: 8px;
}

.subtitle {
  color: #6b7280;
  font-size: 1.1rem;
}

/* Style cho phần bộ lọc */
.filters-container {
  display: flex;
  gap: 12px;
  margin-bottom: 24px;
  flex-wrap: wrap;
}

.filter-input, .filter-select {
  padding: 10px 16px;
  border: 1px solid #d1d5db;
  border-radius: 8px;
  font-size: 1rem;
  outline: none;
  transition: border-color 0.2s;
  flex: 1;
  min-width: 200px;
}

.filter-input:focus, .filter-select:focus {
  border-color: #4f46e5;
  box-shadow: 0 0 0 2px rgba(79, 70, 229, 0.1);
}

.product-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
  gap: 24px;
}

.no-products {
  text-align: center;
  padding: 40px;
  background-color: #fff;
  border-radius: 12px;
  color: #6b7280;
}
</style>