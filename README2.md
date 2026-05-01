# Refactor AuctionManager theo nguyên tắc SOLID

## 📌 Mục tiêu

Ban đầu, class `AuctionManager` đảm nhiệm quá nhiều chức năng:

* Đăng ký / đăng nhập
* Quản lý phiên đấu giá
* Đặt giá (bidding)
* Lưu / đọc dữ liệu
* Gửi thông báo (Observer)
* Scheduler tự động kết thúc phiên

Điều này vi phạm **Single Responsibility Principle (SRP)** trong SOLID:

> Một class chỉ nên có **1 lý do để thay đổi**

---

## ❌ Vấn đề trước khi refactor

`AuctionManager` là một **God Class**:

* Khó maintain
* Khó test
* Khó mở rộng
* Code bị coupling cao

Ví dụ:

* Thay đổi logic login → phải sửa `AuctionManager`
* Đổi từ file → database → sửa `AuctionManager`
* Thêm loại notification → sửa `AuctionManager`

---

## ✅ Giải pháp

Tách `AuctionManager` thành nhiều service nhỏ, mỗi class chỉ chịu trách nhiệm **1 chức năng duy nhất**.

---

## 🏗️ Kiến trúc sau khi refactor

```
com.auction.server.service
│
├── AuctionManager                // Facade (điều phối)
├── PersistenceService           // Lưu & load dữ liệu
├── AuthService                  // Đăng ký / đăng nhập
├── AuctionService               // Quản lý phiên đấu giá
├── BidService                   // Đặt giá
├── AuctionQueryService          // Truy vấn dữ liệu
├── AuctionNotificationService   // Observer / Event
└── AuctionScheduler             // Tự động đóng phiên
```

---

## 📦 Mô tả từng class

### 1. `AuctionManager` (Facade)

* Entry point của hệ thống
* Giữ Singleton để không phá vỡ code cũ
* Chỉ **delegate** sang các service khác

👉 Không chứa business logic

---

### 2. `PersistenceService`

**Trách nhiệm:**

* Load dữ liệu từ `DataStore`
* Lưu dữ liệu khi có thay đổi

**Lợi ích:**

* Tách biệt storage (File / DB)
* Dễ thay đổi backend

---

### 3. `AuthService`

**Trách nhiệm:**

* Đăng ký (`register`)
* Đăng nhập (`login`)

**Lợi ích:**

* Tách authentication khỏi business logic đấu giá
* Dễ mở rộng (JWT, OAuth...)

---

### 4. `AuctionService`

**Trách nhiệm:**

* Tạo phiên đấu giá
* Cập nhật phiên
* Hủy phiên
* Kết thúc phiên

**Lợi ích:**

* Quản lý lifecycle của Auction rõ ràng

---

### 5. `BidService`

**Trách nhiệm:**

* Xử lý đặt giá (`placeBid`)

**Lợi ích:**

* Tách logic bidding (validation, update price)
* Dễ mở rộng auto-bid, proxy-bid

---

### 6. `AuctionQueryService`

**Trách nhiệm:**

* Lấy danh sách auction
* Tìm auction theo id / item

**Lợi ích:**

* Tách read logic khỏi write logic (CQRS-lite)

---

### 7. `AuctionNotificationService`

**Trách nhiệm:**

* Quản lý Observer
* Gửi event khi có thay đổi

**Lợi ích:**

* Tách hoàn toàn event system
* Có thể thay bằng Kafka/WebSocket sau này

---

### 8. `AuctionScheduler`

**Trách nhiệm:**

* Tự động kiểm tra auction hết hạn
* Gọi finishAuction

**Lợi ích:**

* Tách logic background job
* Dễ thay bằng Quartz/Spring Scheduler

---

## 🎯 Lợi ích đạt được

### ✅ Tuân thủ SOLID

* SRP: mỗi class 1 nhiệm vụ
* OCP: dễ mở rộng
* DIP: phụ thuộc abstraction (`DataStore`)

---

### ✅ Dễ bảo trì

* Code rõ ràng
* Ít ảnh hưởng lẫn nhau

---

### ✅ Dễ test

* Có thể test từng service độc lập
* Mock `PersistenceService`

---

### ✅ Dễ mở rộng

Ví dụ:

| Tính năng mới    | Không cần sửa  |
| ---------------- | -------------- |
| Thêm Redis cache | AuctionService |
| Thêm WebSocket   | BidService     |
| Thêm role mới    | AuctionService |

---

## 🔄 So sánh trước và sau

| Trước        | Sau             |
| ------------ | --------------- |
| 1 class lớn  | Nhiều class nhỏ |
| Khó hiểu     | Rõ ràng         |
| Coupling cao | Loose coupling  |
| Khó test     | Dễ test         |
| Khó mở rộng  | Dễ mở rộng      |

---

## 🚀 Kết luận

Việc tách `AuctionManager` thành các service nhỏ giúp:

* Code sạch hơn
* Tuân thủ SOLID
* Dễ maintain & scale

> `AuctionManager` giờ chỉ còn là **Facade**, còn logic thực sự nằm ở các service chuyên biệt.

---

## 📌 Ghi chú

* Không xóa `AuctionManager` để tránh break code cũ
* Có thể tiến tới dùng **Dependency Injection (Spring)** để bỏ Singleton

---
