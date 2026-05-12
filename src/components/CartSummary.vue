<script setup>
import { computed } from 'vue'

const props = defineProps({
  items: {
    type: Array,
    required: true
  }
})

// Khai báo sự kiện báo lên cha khi bấm nút cộng/trừ
const emit = defineEmits(['update-quantity'])

const totalQuantity = computed(() => {
  return props.items.reduce((total, item) => total + item.quantity, 0)
})

const totalPrice = computed(() => {
  return props.items.reduce((total, item) => total + (item.price * item.quantity), 0)
})

// Hàm tiện ích format tiền
const formatPrice = (price) => {
  return new Intl.NumberFormat('vi-VN', { style: 'currency', currency: 'VND' }).format(price)
}

const formattedTotalPrice = computed(() => formatPrice(totalPrice.value))
</script>

<template>
  <div class="cart-summary" v-if="items.length > 0">
    <div class="summary-header">
      <h2>🛒 Chi tiết giỏ hàng</h2>
      <span class="item-count">{{ totalQuantity }} sản phẩm</span>
    </div>

    <div class="cart-items-list">
      <div v-for="item in items" :key="item.id" class="cart-item">
        <div class="item-info">
          <h4 class="item-name">{{ item.name }}</h4>
          <span class="item-price">{{ formatPrice(item.price) }}</span>
        </div>
        
        <div class="item-controls">
          <button class="btn-qty" @click="emit('update-quantity', item.id, -1)">-</button>
          <span class="item-qty">{{ item.quantity }}</span>
          <button class="btn-qty" @click="emit('update-quantity', item.id, 1)">+</button>
        </div>
        
        <div class="item-subtotal">
          {{ formatPrice(item.price * item.quantity) }}
        </div>
      </div>
    </div>
    
    <div class="summary-details">
      <div class="summary-row total-row">
        <span>Tổng thanh toán:</span>
        <strong class="total-price">{{ formattedTotalPrice }}</strong>
      </div>
    </div>
  </div>
  <div class="cart-summary empty" v-else>
    <div class="empty-icon">🛍️</div>
    <p>Giỏ hàng của bạn đang trống.</p>
    <p class="empty-subtext">Hãy chọn thêm sản phẩm nhé!</p>
  </div>
</template>

<style scoped>
.cart-summary {
  margin-top: 40px;
  padding: 24px;
  background-color: #ffffff;
  border-radius: 16px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
  border: 1px solid #f3f4f6;
}

.summary-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  padding-bottom: 16px;
  border-bottom: 1px solid #f3f4f6;
}

.summary-header h2 {
  font-size: 1.25rem;
  color: #1f2937;
  margin: 0;
}

.item-count {
  background-color: #f3f4f6;
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 0.9rem;
  font-weight: 600;
  color: #4b5563;
}

/* Danh sách Item */
.cart-items-list {
  margin-bottom: 24px;
}

.cart-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 12px 0;
  border-bottom: 1px dashed #e5e7eb;
}

.item-info {
  flex: 2;
}

.item-name {
  margin: 0 0 4px 0;
  font-size: 1rem;
  color: #1f2937;
}

.item-price {
  font-size: 0.9rem;
  color: #6b7280;
}

.item-controls {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
}

.btn-qty {
  width: 28px;
  height: 28px;
  border-radius: 50%;
  border: 1px solid #d1d5db;
  background: white;
  color: #374151;
  font-weight: bold;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s;
}

.btn-qty:hover {
  background: #f3f4f6;
  border-color: #9ca3af;
}

.item-qty {
  font-weight: 600;
  min-width: 20px;
  text-align: center;
}

.item-subtotal {
  flex: 1;
  text-align: right;
  font-weight: 600;
  color: #4f46e5;
}

/* --- */

.summary-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.total-row {
  padding-top: 16px;
  font-size: 1.2rem;
  color: #1f2937;
}

.total-price {
  color: #4f46e5;
  font-size: 1.5rem;
}

.cart-summary.empty {
  text-align: center;
  padding: 40px 20px;
  background-color: #f9fafb;
}

.empty-icon {
  font-size: 3rem;
  margin-bottom: 12px;
  opacity: 0.5;
}

.cart-summary.empty p {
  color: #4b5563;
  font-size: 1.1rem;
  font-weight: 500;
  margin: 0;
}

.empty-subtext {
  font-size: 0.9rem !important;
  color: #9ca3af !important;
  margin-top: 4px !important;
}
</style>