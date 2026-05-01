##  Refactor AuctionManager theo SOLID

### 📌 Mô tả

Refactor `AuctionManager` từ một **God Class** thành nhiều service nhỏ theo nguyên tắc SOLID.

---

### 🔧 Thay đổi chính

* Tách `PersistenceService` (load/save dữ liệu)
* Tách `AuthService` (authentication)
* Tách `AuctionService` (quản lý auction)
* Tách `BidService` (đặt giá)
* Tách `AuctionQueryService` (truy vấn)
* Tách `AuctionNotificationService` (observer/event)
* Tách `AuctionScheduler` (background job)
* Giữ `AuctionManager` làm facade

---

###  Mục tiêu

* Tuân thủ **Single Responsibility Principle**
* Giảm coupling
* Dễ test
* Dễ mở rộng

---

###  Kết quả

| Trước          | Sau               |
| -------------- | ----------------- |
| 1 class lớn    | Nhiều service nhỏ |
| Khó maintain   | Dễ maintain       |
| Khó test       | Dễ test           |
| Tight coupling | Loose coupling    |

---

### ⚠️ Lưu ý

* Không thay đổi API public của `AuctionManager`
* Không ảnh hưởng đến client hiện tại

---

### ✅ Checklist

* [x] Build thành công
* [x] Không phá vỡ flow hiện tại
* [x] Code tuân thủ SOLID
* [x] Có thể mở rộng trong tương lai

---

### 📌 Hướng phát triển tiếp theo

* Áp dụng Dependency Injection (Spring)
* Thay FileDataStore bằng Database
* Tích hợp WebSocket cho real-time bidding

---
