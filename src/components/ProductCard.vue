<script setup>
import { computed } from 'vue'

const props = defineProps({
  product: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['add-to-cart'])

const formattedPrice = computed(() => {
  return new Intl.NumberFormat('vi-VN', { style: 'currency', currency: 'VND' }).format(props.product.price)
})

const handleAddToCart = () => {
  // Đã sửa thành chuẩn kebab-case để App.vue có thể lắng nghe được
  emit('add-to-cart', props.product);
}
</script>

<template>
  <div class="product-card" :class="{ 'out-of-stock': !product.inStock }">
    <div class="card-content">
      <h3 class="product-name">{{ product.name }}</h3>
      <p class="price">{{ formattedPrice }}</p>
      
      <div class="status-badge">
        <span v-if="product.inStock" class="badge in-stock">✅ Còn hàng</span>
        <span v-else class="badge sold-out">❌ Hết hàng</span>
      </div>
    </div>

    <button 
      class="btn-add"
      @click="handleAddToCart" 
      :disabled="!product.inStock"
    >
      {{ product.inStock ? 'Thêm vào giỏ hàng' : 'Tạm hết hàng' }}
    </button>
  </div>
</template>

<style scoped>
.product-card {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  border: 1px solid #eaeaea;
  border-radius: 12px;
  background-color: #ffffff;
  overflow: hidden;
  transition: all 0.3s ease;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.product-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 20px rgba(0, 0, 0, 0.08);
  border-color: #d1d5db;
}

.card-content {
  padding: 20px;
}

.out-of-stock {
  opacity: 0.75;
  background-color: #fafafa;
}

.product-name {
  font-size: 1.1rem;
  color: #1f2937;
  margin-bottom: 8px;
  font-weight: 600;
}

.price {
  font-size: 1.25rem;
  font-weight: 700;
  color: #4f46e5; /* Màu chàm hiện đại */
  margin-bottom: 16px;
}

.status-badge {
  margin-bottom: 10px;
}

.badge {
  display: inline-block;
  padding: 4px 10px;
  border-radius: 20px;
  font-size: 0.85rem;
  font-weight: 500;
}

.in-stock {
  background-color: #dcfce7;
  color: #166534;
}

.sold-out {
  background-color: #fee2e2;
  color: #991b1b;
}

.btn-add {
  width: 100%;
  padding: 14px;
  background-color: #4f46e5;
  color: white;
  border: none;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.2s ease;
}

.btn-add:hover:not(:disabled) {
  background-color: #4338ca;
}

.btn-add:disabled {
  background-color: #d1d5db;
  color: #6b7280;
  cursor: not-allowed;
}
</style>