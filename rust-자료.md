---
marp: true
---

# Rust의 예외와 에러 관리

### Result와 Match

---

# 목차
1. Rust의 강점
2. Rust의 단점 (싫어하는 이유)
3. Result와 Match

---

# Rust의 강점
1. 안전한 메모리 관리 (Ownership)
    * ex) 변수가 스코프 밖으로 벗어나면 값이 버려짐(drop 호출)
2. **철저한 예외나 에러 관리**
    * *Ok( ), Err( ), **panic!*** 명시적으로 작성
3. 정확한 오류 지적
```rust
warning: function `tenor_client` is never used
 --> src\client\tenor_client.rs:5:8
  |
5 | pub fn tenor_client() -> Result<TenorResponse>, TenorErrorResponse> {
  |        ^^^^^^^^^^^^
```

---

# 단점 (싫어하는 이유)

1. 엄격한 타입
2. 생태계와 라이브러리
3. 비동기 프로그래밍은 어렵다

---

# 변수 선언, 불변성
https://rinthel.github.io/rust-lang-book-ko/ch03-01-variables-and-mutability.html

1. 변수는 immutable
2. let 키워드를 사용하여 타입 자동 추론

---

# match
- switch와 비슷하지만, match는 모든 케이스를 표현해야함 (안전성)

# option
- nullable한 표현 가능한 enum

# result
- 실패할 가능성이 있는 값을 반환하는 Generic enum

# trait
- 다형성을 지원

---

# 소유권

https://rinthel.github.io/rust-lang-book-ko/ch04-01-what-is-ownership.html

1. 소유자가 함수의 인자로 전달되면, 소유권은 그 함수의 매개변수로 Move 된다
2. 이동된 이후 원래 함수에 있던 변수는 더 이상 사용 할 수 없다.

---

# 참고자료

https://rinthel.github.io/rust-lang-book-ko/

https://www.youtube.com/watch?v=V6TR2FeCK5c 
(Rust 크로스 플랫폼 프로그래밍 | 인프콘 2022)

https://prev.rust-lang.org/ko-KR/faq.html
