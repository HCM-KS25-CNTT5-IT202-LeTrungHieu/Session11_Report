# BÁO CÁO TỰ HỌC STORED PROCEDURE

## 1. Mục tiêu tự học

Báo cáo này trình bày quá trình tìm hiểu về `Stored Procedure` trong MySQL.

Mục tiêu chính gồm:

- Hiểu khái niệm Stored Procedure.
- Biết cách tạo, gọi và xóa Stored Procedure.
- Biết sử dụng biến, vòng lặp, điều kiện trong Stored Procedure.
- Hiểu ưu điểm và nhược điểm của Stored Procedure.
- Biết ứng dụng Stored Procedure vào các bài toán thực tế như thêm dữ liệu mẫu, xử lý nghiệp vụ và tự động hóa thao tác database.

---

## 2. Stored Procedure là gì?

`Stored Procedure` là một tập hợp các câu lệnh SQL được lưu trữ sẵn trong hệ quản trị cơ sở dữ liệu.

Thay vì viết lại nhiều câu lệnh SQL mỗi lần sử dụng, ta có thể gom chúng vào một Procedure, sau đó gọi lại bằng lệnh `CALL`.

Ví dụ:

```sql
CALL procedure_name();
```

Stored Procedure thường được dùng để xử lý các nghiệp vụ lặp đi lặp lại trong database.

---

## 3. Cú pháp tạo Stored Procedure

Cú pháp cơ bản:

```sql
DELIMITER //

CREATE PROCEDURE procedure_name()
BEGIN
    -- Các câu lệnh SQL
END //

DELIMITER ;
```

Trong MySQL, cần đổi `DELIMITER` vì thân Procedure có nhiều dấu `;`.

Nếu không đổi delimiter, MySQL sẽ hiểu nhầm dấu `;` đầu tiên là kết thúc câu lệnh `CREATE PROCEDURE`.

---

## 4. Ví dụ Stored Procedure đơn giản

```sql
DELIMITER //

CREATE PROCEDURE helloProcedure()
BEGIN
    SELECT 'Hello Stored Procedure' AS message;
END //

DELIMITER ;
```

Gọi Procedure:

```sql
CALL helloProcedure();
```

Kết quả:

| message                |
| ---------------------- |
| Hello Stored Procedure |

---

## 5. Stored Procedure có tham số

Stored Procedure có thể nhận tham số đầu vào để xử lý linh hoạt hơn.

Ví dụ:

```sql
DELIMITER //

CREATE PROCEDURE findPatientById(IN patientId INT)
BEGIN
    SELECT *
    FROM patients
    WHERE id = patientId;
END //

DELIMITER ;
```

Gọi Procedure:

```sql
CALL findPatientById(1);
```

Trong ví dụ trên:

- `IN` là tham số đầu vào.
- `patientId` là giá trị được truyền vào khi gọi Procedure.

---

## 6. Các loại tham số trong Stored Procedure

Stored Procedure trong MySQL có 3 loại tham số chính:

| Loại tham số | Ý nghĩa                 |
| ------------ | ----------------------- |
| `IN`         | Nhận dữ liệu đầu vào    |
| `OUT`        | Trả dữ liệu ra ngoài    |
| `INOUT`      | Vừa nhận vào vừa trả ra |

### Ví dụ tham số OUT

```sql
DELIMITER //

CREATE PROCEDURE countPatients(OUT totalPatients INT)
BEGIN
    SELECT COUNT(*)
    INTO totalPatients
    FROM patients;
END //

DELIMITER ;
```

Gọi Procedure:

```sql
CALL countPatients(@total);
SELECT @total;
```

---

## 7. Khai báo biến trong Stored Procedure

Có thể khai báo biến bằng từ khóa `DECLARE`.

Ví dụ:

```sql
DELIMITER //

CREATE PROCEDURE demoVariable()
BEGIN
    DECLARE total INT DEFAULT 0;

    SELECT COUNT(*)
    INTO total
    FROM patients;

    SELECT total AS total_patients;
END //

DELIMITER ;
```

Lưu ý: Trong MySQL, biến `DECLARE` phải được đặt ở đầu khối `BEGIN ... END`.

---

## 8. Sử dụng IF trong Stored Procedure

Stored Procedure có thể dùng điều kiện `IF`.

Ví dụ:

```sql
DELIMITER //

CREATE PROCEDURE checkHeartRate(IN heartRate INT)
BEGIN
    IF heartRate > 120 OR heartRate < 50 THEN
        SELECT 'CRITICAL' AS urgency_level;
    ELSE
        SELECT 'STABLE' AS urgency_level;
    END IF;
END //

DELIMITER ;
```

Gọi Procedure:

```sql
CALL checkHeartRate(130);
```

Kết quả:

| urgency_level |
| ------------- |
| CRITICAL      |

---

## 9. Sử dụng vòng lặp WHILE

Stored Procedure có thể dùng vòng lặp để xử lý nhiều dòng dữ liệu hoặc sinh dữ liệu mẫu.

Ví dụ chèn 1000 bệnh nhân mẫu:

```sql
DELIMITER //

CREATE PROCEDURE seedPatients()
BEGIN
    DECLARE i INT DEFAULT 1;

    WHILE i <= 1000 DO
        INSERT INTO patients(full_name, phone, age, address)
        VALUES (
            CONCAT('Patient ', i),
            CONCAT('090', i),
            FLOOR(RAND() * 100),
            'Ho Chi Minh City'
        );

        SET i = i + 1;
    END WHILE;
END //

DELIMITER ;
```

Gọi Procedure:

```sql
CALL seedPatients();
```

Procedure trên sẽ tự động chèn 1000 dòng dữ liệu vào bảng `patients`.

---

## 10. Xóa Stored Procedure

Để xóa một Stored Procedure, dùng lệnh:

```sql
DROP PROCEDURE procedure_name;
```

Ví dụ:

```sql
DROP PROCEDURE seedPatients;
```

Có thể dùng thêm `IF EXISTS` để tránh lỗi nếu Procedure chưa tồn tại:

```sql
DROP PROCEDURE IF EXISTS seedPatients;
```

---

## 11. Ứng dụng thực tế của Stored Procedure

Stored Procedure có nhiều ứng dụng trong thực tế, ví dụ:

- Tự động thêm dữ liệu mẫu để kiểm thử.
- Tính toán báo cáo doanh thu.
- Kiểm tra điều kiện nghiệp vụ trước khi thêm dữ liệu.
- Cập nhật trạng thái đơn hàng.
- Xử lý giao dịch ngân hàng.
- Tự động hóa các thao tác phức tạp trong database.

Ví dụ trong hệ thống bệnh viện, Stored Procedure có thể dùng để:

- Thêm bệnh nhân mới.
- Ghi nhận chỉ số sinh tồn.
- Tính tổng doanh thu theo khoa.
- Tìm bệnh nhân theo số điện thoại.
- Sinh dữ liệu giả lập để kiểm tra hiệu năng Index.

---

## 12. Ưu điểm của Stored Procedure

Stored Procedure có một số ưu điểm:

### 12.1. Tái sử dụng mã SQL

Các câu lệnh SQL phức tạp có thể được đóng gói lại và gọi nhiều lần bằng `CALL`.

### 12.2. Giảm lỗi khi viết lại truy vấn

Thay vì viết đi viết lại cùng một logic SQL, ta chỉ cần viết một lần trong Procedure.

### 12.3. Tăng tính bảo mật

Người dùng có thể được cấp quyền gọi Procedure mà không cần truy cập trực tiếp vào bảng gốc.

### 12.4. Xử lý nghiệp vụ gần database

Một số logic nghiệp vụ có thể được xử lý ngay trong database, giúp giảm số lượng thao tác từ phía ứng dụng.

### 12.5. Hỗ trợ kiểm thử dữ liệu lớn

Stored Procedure rất hữu ích khi cần sinh hàng nghìn hoặc hàng triệu dòng dữ liệu mẫu để kiểm tra hiệu năng.

---

## 13. Nhược điểm của Stored Procedure

Bên cạnh ưu điểm, Stored Procedure cũng có một số hạn chế:

### 13.1. Khó bảo trì nếu quá phức tạp

Nếu viết quá nhiều logic trong database, hệ thống có thể khó đọc và khó sửa.

### 13.2. Khó quản lý phiên bản

Stored Procedure nằm trong database nên nếu không lưu script trong Git, việc theo dõi thay đổi sẽ khó khăn.

### 13.3. Phụ thuộc vào hệ quản trị cơ sở dữ liệu

Cú pháp Stored Procedure có thể khác nhau giữa MySQL, SQL Server, PostgreSQL và Oracle.

### 13.4. Khó debug hơn code ứng dụng

Debug Stored Procedure thường không tiện bằng debug trong các ngôn ngữ lập trình như Java, JavaScript hoặc Python.

---

## 14. So sánh Stored Procedure với câu lệnh SQL thông thường

| Tiêu chí                   | SQL thông thường       | Stored Procedure           |
| -------------------------- | ---------------------- | -------------------------- |
| Cách sử dụng               | Viết và chạy trực tiếp | Tạo trước, gọi bằng `CALL` |
| Tái sử dụng                | Thấp                   | Cao                        |
| Phù hợp với logic phức tạp | Không tối ưu           | Phù hợp hơn                |
| Bảo trì                    | Dễ với truy vấn ngắn   | Cần quản lý cẩn thận       |
| Sinh dữ liệu mẫu           | Khó hơn                | Dễ hơn nhờ vòng lặp        |

---

## 15. Ví dụ tổng hợp

Ví dụ tạo bảng bệnh nhân và Procedure thêm bệnh nhân mới:

```sql
CREATE TABLE patients (
    id INT PRIMARY KEY AUTO_INCREMENT,
    full_name VARCHAR(100) NOT NULL,
    phone VARCHAR(20) NOT NULL,
    age INT,
    address VARCHAR(255)
);
```

Tạo Procedure:

```sql
DELIMITER //

CREATE PROCEDURE addPatient(
    IN patientName VARCHAR(100),
    IN patientPhone VARCHAR(20),
    IN patientAge INT,
    IN patientAddress VARCHAR(255)
)
BEGIN
    INSERT INTO patients(full_name, phone, age, address)
    VALUES (patientName, patientPhone, patientAge, patientAddress);
END //

DELIMITER ;
```

Gọi Procedure:

```sql
CALL addPatient(
    'Nguyen Van A',
    '0901234567',
    25,
    'Ho Chi Minh City'
);
```

Kiểm tra dữ liệu:

```sql
SELECT *
FROM patients;
```

---

## 16. Lưu ý khi dùng Stored Procedure

Khi sử dụng Stored Procedure trong MySQL, cần chú ý:

- Phải đổi `DELIMITER` khi tạo Procedure.
- Biến `DECLARE` phải đặt ở đầu khối `BEGIN ... END`.
- Nên dùng `DROP PROCEDURE IF EXISTS` trước khi tạo lại Procedure.
- Không nên đưa quá nhiều logic ứng dụng vào database.
- Nên lưu toàn bộ script Procedure vào Git để dễ quản lý.
- Khi chèn dữ liệu lớn, nên cân nhắc dùng `START TRANSACTION` và `COMMIT` để tối ưu tốc độ.

Ví dụ:

```sql
START TRANSACTION;

-- nhiều câu INSERT

COMMIT;
```

---

## 17. Kết luận

Stored Procedure là một công cụ mạnh trong MySQL, giúp đóng gói và tái sử dụng các câu lệnh SQL.

Thông qua quá trình tự học, em hiểu được cách tạo, gọi, xóa Stored Procedure, cách sử dụng tham số, biến, điều kiện và vòng lặp.

Stored Procedure đặc biệt hữu ích trong các bài toán cần xử lý nghiệp vụ lặp lại, sinh dữ liệu mẫu hoặc tối ưu thao tác trực tiếp trong database.

Tuy nhiên, cần sử dụng Stored Procedure hợp lý. Nếu lạm dụng quá nhiều logic trong database, hệ thống có thể trở nên khó bảo trì và khó mở rộng.

Vì vậy, Stored Procedure nên được dùng cho các nghiệp vụ phù hợp như xử lý dữ liệu gần database, báo cáo, kiểm thử dữ liệu lớn và các thao tác có tính lặp lại cao.
