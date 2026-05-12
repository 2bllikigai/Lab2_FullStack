# AI_PROMPTS.md — Lab 02

## Prompt 1

| Hạng mục | Nội dung |
|---|---|
| Mục tiêu prompt | Tạo khung khởi đầu cho `App.vue` và khởi tạo state danh sách sản phẩm. |
| Prompt đã dùng | "Tôi đang làm Lab 02 FIT4104 với Vue 3 script setup. Hãy tạo scaffold cho App.vue có danh sách products dùng ref... Sau code, hãy giải thích vì sao dùng ref và cách Vue cập nhật giao diện." |
| AI trả lời tóm tắt | AI cung cấp code khởi tạo biến `const products = ref([...])`. Giải thích rằng dùng `ref` để tạo dữ liệu có tính "phản ứng" (reactive). Khi `ref` thay đổi, Vue sẽ nhận diện được và tự động cập nhật lại giao diện ở DOM. |
| Code nào được dùng | Cấu trúc `<script setup>`, khai báo mảng `products` sử dụng `ref()`. |
| Em đã sửa gì | Em đã thêm trường `image` chứa đường dẫn hình ảnh vào từng object sản phẩm và chỉnh sửa lại CSS tổng thể của app để có nền xám đẹp hơn. |
| Em hiểu gì sau khi sửa | Em hiểu được cách khai báo state cơ bản trong Composition API và sự khác biệt giữa biến `ref` với biến thông thường trong JavaScript. |

## Prompt 2

| Hạng mục | Nội dung |
|---|---|
| Mục tiêu prompt | Tạo component `ProductCard.vue` nhận dữ liệu từ cha và gửi sự kiện khi click. |
| Prompt đã dùng | "Tôi đang học Vue 3 trong FIT4104. Hãy tạo scaffold component ProductCard.vue... Khi bấm nút 'Thêm vào giỏ', emit event add-to-cart. Hãy giải thích props, defineProps, defineEmits và luồng dữ liệu cha-con." |
| AI trả lời tóm tắt | AI cung cấp code cho thẻ sản phẩm. Giải thích `defineProps` dùng để nhận dữ liệu từ cha (mang tính chỉ đọc), còn `defineEmits` dùng để khai báo các sự kiện gửi ngược lên cha. Đề cập quy tắc "Data Down, Events Up". |
| Code nào được dùng | `const props = defineProps(...)`, `const emit = defineEmits(...)`, và đoạn dùng `Intl.NumberFormat` trong `computed` để format tiền VND. |
| Em đã sửa gì | Em đã chèn thêm thẻ `<img :src="product.image">` vào template, thêm điều kiện `v-if="product.image"` và viết thêm hiệu ứng CSS hover phóng to ảnh. |
| Em hiểu gì sau khi sửa | Hiểu rằng component con đóng vai trò hiển thị (nhận Props) và báo cáo tương tác (bắn Emit), tuyệt đối không được tự ý thay đổi (gán lại giá trị) trực tiếp cho Props. |

## Debug log

| Hạng mục | Nội dung |
|---|---|
| Lỗi gặp phải | Bấm nút "Thêm vào giỏ" ở thẻ sản phẩm nhưng không có hiện tượng gì xảy ra, giỏ hàng không cập nhật. |
| Triệu chứng | Code biên dịch không báo lỗi, nhưng chức năng không hoạt động. |
| Prompt hỏi AI | "Trong ProductCard.vue tôi emit event addToCart, nhưng trong App.vue tôi lắng nghe @add-to-cart. Hãy giúp tôi phân tích nguyên nhân, cách kiểm tra bằng console.log..." |
| Gợi ý của AI | AI hướng dẫn đặt `console.log` ở cả component cha và con để kiểm tra đường truyền. Giải thích rằng do HTML không phân biệt hoa thường nên `addToCart` (camelCase) sẽ bị trình duyệt tự ép thành `addtocart`, làm component cha không nhận diện được `@add-to-cart`. |
| Cách em kiểm tra | Em đã bật tab Console (F12) trên trình duyệt, bấm nút và thấy log số 1 của `ProductCard` xuất hiện nhưng log số 2 của `App.vue` không chạy. Từ đó xác định lỗi do rớt sự kiện ở khâu Emit. |
| Dòng code em đã sửa | Sửa `emit('addToCart', props.product)` thành chuẩn kebab-case: `emit('add-to-cart', props.product)`. |
| Bài học rút ra | Luôn luôn sử dụng định dạng kebab-case (chữ-thường-có-gạch-ngang) khi đặt tên custom event trong Vue để đảm bảo tính nhất quán giữa JavaScript và HTML Template. |

## Câu hỏi kiểm tra hiểu code

| Câu hỏi | Trả lời |
|---|---|
| Nếu bỏ `:key="product.id"` thì sao? | Vue sẽ cảnh báo trên console. Thiếu `:key`, Vue không định danh được chính xác từng thẻ HTML ứng với dữ liệu nào khi danh sách thay đổi. Nó sẽ dùng chiến lược "vá tại chỗ", dễ gây lỗi sai lệch giao diện và giảm hiệu năng. |
| Nếu bỏ `:product="product"` thì sao? | Component con `ProductCard.vue` sẽ bị mất dữ liệu đầu vào. `props.product` sẽ mang giá trị `undefined`, dẫn đến lỗi trắng trang ngay lập tức khi template cố tình truy cập `product.name` hay `product.price`. |
| Nếu bỏ `@add-to-cart="handleAddToCart"` thì sao? | Khi người dùng click nút, component con vẫn bắn sự kiện (emit) bình thường, nhưng component cha (`App.vue`) không thèm lắng nghe. Do đó hàm cập nhật giỏ hàng sẽ không bao giờ được kích hoạt. |
| Em giải thích được luồng props xuống, event lên như thế nào? | Đây là luồng dữ liệu một chiều (One-Way Data Flow): **Data Down** (Dữ liệu gốc được quản lý ở Component cha và chảy xuống các Component con thông qua *Props* - con chỉ được đọc). **Events Up** (Khi người dùng tương tác, Component con dùng *Emit* để thông báo lên cha. Cha nhận thông báo, cập nhật lại dữ liệu gốc. Dữ liệu thay đổi sẽ tự động đẩy Props mới xuống con để vẽ lại giao diện). |