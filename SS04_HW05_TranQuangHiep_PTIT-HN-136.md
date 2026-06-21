BÀI 5: Thiết kế Quy trình & Prompt Kiểm tra Giao dịch Đáng ngờ (Fraud Detector)
1. Ý đồ thiết kế quy trình 2 bước

Quy trình được chia thành 2 bước nhằm mô phỏng cách làm việc thực tế của một nhóm phát triển phần mềm sử dụng AI:

Bước 1 – Code Generation: AI đóng vai trò lập trình viên, sinh mã nguồn Java theo đúng yêu cầu nghiệp vụ phát hiện giao dịch đáng ngờ. Mục tiêu là tạo ra phiên bản chạy đúng chức năng trước.
Bước 2 – Performance Audit: AI đóng vai trò chuyên gia tối ưu thuật toán, kiểm tra lại mã nguồn ở bước 1, phát hiện các điểm nghẽn hiệu năng và đề xuất phiên bản tối ưu hơn. Điều này giúp giảm độ phức tạp từ O(N²) xuống O(NlogN) khi xử lý tập dữ liệu lớn (10.000 giao dịch trở lên).

Quy trình này tạo cơ chế kiểm toán chéo (cross-audit) giữa hai vai trò AI khác nhau để nâng cao chất lượng phần mềm.

Bước 1 – Prompt sinh mã nguồn (Code Generation Prompt)
Bạn là một Senior Java Developer.

Hãy tạo mã nguồn Java cho hệ thống phát hiện giao dịch đáng ngờ (Fraud Transaction Detector).

Yêu cầu:

1. Tạo lớp FraudDetector.

2. Tạo phương thức:

public List<Transaction> detectFraud(List<Transaction> transactions)

3. Giả sử lớp Transaction có các thuộc tính:

- String transactionId
- String cardId
- double amount
- String location
- LocalDateTime transactionTime
- boolean suspicious

4. Quy tắc phát hiện giao dịch đáng ngờ:

Rule 1:
Nếu amount > 100000000 VND thì giao dịch bị đánh dấu suspicious = true.

Rule 2:
Nếu tồn tại hai giao dịch liên tiếp của cùng cardId,
được thực hiện ở hai location khác nhau,
và khoảng cách thời gian giữa chúng nhỏ hơn 10 phút,
thì cả hai giao dịch đều bị đánh dấu suspicious = true.

Ví dụ:
Transaction A:
cardId = CARD01
location = Hanoi
time = 2025-01-01T10:00

Transaction B:
cardId = CARD01
location = Saigon
time = 2025-01-01T10:05

=> Cả A và B đều suspicious.

5. Sử dụng Java 8 Date Time API:
- LocalDateTime
- Duration.between()

để tính chênh lệch thời gian.

6. Xử lý dữ liệu lỗi biên:

- Nếu transactions == null → trả về danh sách rỗng.
- Nếu transactions rỗng → trả về danh sách rỗng.
- Bỏ qua các transaction có:
   + cardId null
   + transactionTime null
   + location null hoặc rỗng

7. Ưu tiên code dễ đọc, có comment giải thích.

8. Chưa cần tối ưu hiệu năng.
   Có thể sử dụng thuật toán duyệt lặp để kiểm tra các giao dịch cùng card.

9. Trả về:
- Mã nguồn Java đầy đủ.
- Giải thích độ phức tạp thời gian và bộ nhớ.
  Bước 2 – Prompt kiểm chứng chéo hiệu năng (Performance Audit Prompt)
  Bạn là Principal Software Engineer và chuyên gia tối ưu thuật toán Java.

Hãy review đoạn mã FraudDetector dưới đây.

Nhiệm vụ:

1. Phân tích độ phức tạp thời gian hiện tại.

2. Xác định các điểm nghẽn hiệu năng khi xử lý:

N = 10.000 giao dịch

3. Kiểm tra xem code có đang sử dụng thuật toán so sánh lồng nhau O(N²) hay không.

4. Nếu tồn tại O(N²), hãy giải thích:

- Tại sao nó gây chậm.
- Số lượng phép so sánh xấp xỉ.
- Tác động tới hệ thống FinTech thực tế.

5. Đề xuất phương án tối ưu:

- Gom nhóm giao dịch theo cardId bằng HashMap.
- Với mỗi cardId:
   + Sắp xếp theo transactionTime.
   + Chỉ so sánh các giao dịch lân cận liên tiếp theo thời gian.
- Tiếp tục áp dụng Rule 1 và Rule 2.

6. Mục tiêu:

Giảm độ phức tạp từ:

O(N²)

xuống:

O(N log N)

hoặc tốt hơn.

7. Hãy sinh lại toàn bộ mã nguồn FraudDetector đã tối ưu.

8. Trình bày:

- Phân tích code cũ.
- Thuật toán mới.
- So sánh Big-O trước và sau.
- Mã nguồn Java hoàn chỉnh.
  Minh chứng thực tế (Log Chat mẫu)
  Lượt chat 1 – Sinh mã nguồn ban đầu
  User
  [Prompt Bước 1]
  AI
  public class FraudDetector {

  public List<Transaction> detectFraud(List<Transaction> transactions) {

        if (transactions == null || transactions.isEmpty()) {
            return Collections.emptyList();
        }

        for (Transaction tx : transactions) {

            if (tx == null) {
                continue;
            }

            if (tx.getAmount() > 100000000) {
                tx.setSuspicious(true);
            }
        }

        for (int i = 0; i < transactions.size(); i++) {

            Transaction t1 = transactions.get(i);

            if (t1 == null
                    || t1.getCardId() == null
                    || t1.getLocation() == null
                    || t1.getTransactionTime() == null) {
                continue;
            }

            for (int j = i + 1; j < transactions.size(); j++) {

                Transaction t2 = transactions.get(j);

                if (t2 == null
                        || t2.getCardId() == null
                        || t2.getLocation() == null
                        || t2.getTransactionTime() == null) {
                    continue;
                }

                if (t1.getCardId().equals(t2.getCardId())) {

                    long minutes =
                            Math.abs(Duration.between(
                                    t1.getTransactionTime(),
                                    t2.getTransactionTime())
                                    .toMinutes());

                    if (!t1.getLocation().equals(t2.getLocation())
                            && minutes < 10) {

                        t1.setSuspicious(true);
                        t2.setSuspicious(true);
                    }
                }
            }
        }

        return transactions;
  }
  }
  AI giải thích
  Độ phức tạp thời gian:

O(N²)

Do sử dụng hai vòng lặp lồng nhau để so sánh mọi cặp giao dịch.

Độ phức tạp bộ nhớ:

O(1)
Lượt chat 2 – Kiểm chứng chéo hiệu năng
User
[Prompt Bước 2]
AI
PHÂN TÍCH HIỆU NĂNG

Code hiện tại sử dụng hai vòng lặp lồng nhau.

for i = 0..N
for j = i+1..N

=> Độ phức tạp:

O(N²)

Với N = 10.000

Số phép so sánh xấp xỉ:

10.000 × 10.000
= 100.000.000

≈ 100 triệu lần so sánh.

Điều này có thể gây độ trễ đáng kể trên hệ thống FinTech thời gian thực.
AI đề xuất thuật toán tối ưu
Tối ưu:

Bước 1:
HashMap<String, List<Transaction>>

Gom nhóm theo cardId.

Bước 2:
Sắp xếp từng nhóm theo transactionTime.

Bước 3:
Chỉ so sánh transaction hiện tại với transaction liền trước.

Độ phức tạp:

Grouping:
O(N)

Sorting:
O(N log N)

Scanning:
O(N)

Tổng:
O(N log N)
AI sinh mã nguồn tối ưu
public class FraudDetector {

    public List<Transaction> detectFraud(List<Transaction> transactions) {

        if (transactions == null || transactions.isEmpty()) {
            return Collections.emptyList();
        }

        Map<String, List<Transaction>> cardMap = new HashMap<>();

        for (Transaction tx : transactions) {

            if (tx == null) {
                continue;
            }

            if (tx.getAmount() > 100000000) {
                tx.setSuspicious(true);
            }

            if (tx.getCardId() == null
                    || tx.getLocation() == null
                    || tx.getLocation().trim().isEmpty()
                    || tx.getTransactionTime() == null) {
                continue;
            }

            cardMap.computeIfAbsent(
                    tx.getCardId(),
                    k -> new ArrayList<>())
                    .add(tx);
        }

        for (List<Transaction> cardTransactions : cardMap.values()) {

            cardTransactions.sort(
                    Comparator.comparing(
                            Transaction::getTransactionTime));

            for (int i = 1; i < cardTransactions.size(); i++) {

                Transaction prev = cardTransactions.get(i - 1);
                Transaction curr = cardTransactions.get(i);

                long minutes =
                        Duration.between(
                                prev.getTransactionTime(),
                                curr.getTransactionTime())
                                .toMinutes();

                if (minutes < 10
                        && !prev.getLocation()
                                .equals(curr.getLocation())) {

                    prev.setSuspicious(true);
                    curr.setSuspicious(true);
                }
            }
        }

        return transactions;
    }
}
AI kết luận
Trước tối ưu:
O(N²)

Sau tối ưu:
O(N log N)

Phù hợp cho tập dữ liệu lớn từ 10.000 giao dịch trở lên và đáp ứng yêu cầu hệ thống FinTech thực tế.
Kết luận

Quy trình 2 bước trên giúp:

Sinh mã nguồn đúng nghiệp vụ trước.
Kiểm toán chéo bởi AI chuyên tối ưu thuật toán.
Phát hiện sớm điểm nghẽn O(N²).
Nâng cấp lên O(NlogN) bằng HashMap + Sort theo thời gian + so sánh lân cận.
Phù hợp với hệ thống Fraud Detection xử lý dữ liệu giao dịch quy mô lớn.