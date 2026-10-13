# Báo cáo so sánh thời gian CI với và không có Gradle Dependency Caching

## Thông tin bài tập
- Bài 3: Tối ưu tốc độ CI bằng Gradle Dependency Caching
- Tên file: `.github/workflows/ci.yml`

## Thay đổi thực hiện
Đã bổ sung `cache: 'gradle'` vào bước thiết lập Java trong workflow:

```yaml
      - name: Thiết lập môi trường Java JDK 17
        uses: actions/setup-java@v5
        with:
          java-version: '17'
          distribution: 'temurin'
          cache: 'gradle'
```

## Kết quả dự kiến khi chạy CI

### Lần push thứ 1 (không có cache)
- Bước `setup-java` sẽ tải và cài đặt JDK, đồng thời khởi tạo cache key nhưng chưa có cache - có thể mất thêm thời gian để xác định/tạo cache
- Bước `./gradlew test` và các bước khác cần tải dependencies Gradle (nếu chưa có trong cache của runner) → thời gian chạy toàn bộ job sẽ dài hơn

### Lần push thứ 2 (đã có cache)
- Bước `setup-java` sẽ hiển thị thông báo: **"Cache restored successfully from key: gradle-..."**
- Dependencies Gradle đã được cache từ lần chạy trước → `./gradlew test` và các task Gradle sẽ chạy nhanh hơn đáng kể
- Tổng thời gian toàn bộ job giảm rõ rệt

## Ghi chú
Sau khi push lần thứ 2 lên GitHub, cần kiểm tra logs của workflow run trên GitHub Actions để đối chiếu thời gian thực tế giữa 2 lần chạy. 

Cấu hình đã được áp dụng và đẩy lên repository tại commit `5c72aea`.