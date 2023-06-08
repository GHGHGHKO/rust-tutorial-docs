---
marp: true
---

# Rust의 강점
1. 안전한 메모리 관리
2. 철저한 예외나 에러 관리
3. 특이한 enum 시스템
4. 트레이트
5. 하이지닉 매크로
6. 비동기 프로그래밍
7. 제네릭

---

# 단점

1. 러닝커브
2. 잦은 스펙 변경 (0.x.x 버전의 라이브러리 존재)
3. 컴파일이 느림

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

# OOP (trait)

1. 클래스가 없음
2. 구조체로 선언
3. 상속이 없음

대신 trait으로 다형성을 지원
trait은 다른 trait의 메서드들을 상속 받을 수 있다.

---

# Rust 트러블 슈팅 경험

1. Result 경험
```rust
pub async fn github_latest_tag_client()
    -> Result<GithubLatestTagResult, GithubErrorResponse>
```

2. unwarp, warp

```rust
let response = client
    .get(url)
    .send()
    .await
    .unwrap();
```

3. 파생(Derive)
```rust
#[derive(Serialize, Deserialize)]
pub struct TenorResults {
    pub results: Vec<Results>
}
```

---

# 참고자료

https://rinthel.github.io/rust-lang-book-ko/

https://www.youtube.com/watch?v=V6TR2FeCK5c 
(Rust 크로스 플랫폼 프로그래밍 | 인프콘 2022)
