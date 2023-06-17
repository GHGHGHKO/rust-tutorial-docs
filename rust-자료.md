---
marp: true
---

# Rust의 예외와 에러 관리

### Result와 Match

---

# 목차
1. Rust의 강점
2. Rust의 단점 (싫어하는 이유)
3. 코드 및 실습 (Result와 Match)
4. 활용 예시 코드

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

# 단점 (혹은 싫어하는 이유)

1. 엄격한 타입
    * 명확한 타입, 빌림(Borrowing)
2. 생태계와 라이브러리
    * 메이저한 언어(Java, C++, Python ..)에 비해 정보가 부족
3. 비동기 프로그래밍은 어렵다
    * 비동기에 특화되어있지만 비동기는 어렵다

---

# 변수 선언, 불변성
1. 변수는 immutable
2. let 키워드를 사용하여 타입 자동 추론
3. 추가 설명 넣기

---

# 변수 선언, 불변성
 * 예시코드 추가
```rust
fn main() {
    let x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```
```rust
error[E0384]: re-assignment of immutable variable `x`
 --> src/main.rs:4:5
  |
2 |     let x = 5;
  |         - first assignment to `x`
3 |     println!("The value of x is: {}", x);
4 |     x = 6;
  |     ^^^^^ re-assignment of immutable variable
  ```

---

# 메모리와 할당
```rust
{
    let s = String::from("hello"); // s는 여기서부터 유효합니다

    // s를 가지고 뭔가 합니다
}                                  // 이 스코프는 끝났고, s는 더 이상 
                                   // 유효하지 않습니다
```
1. 런타임에 운영체제로부터 메모리가 요청되어야 한다.
2. String의 사용이 끝났을 때 운영체제에게 메모리를 반납할 방법이 필요하다.

*러스트는 `}` 괄호가 닫힐때 자동적으로 `drop`을 호출합니다.*


---

# Option (unwrap, match)
- nullable한 표현 가능한 enum
- 설명 추가 및 예제 추가

---

# match
- switch와 비슷하지만, match는 모든 케이스를 표현해야함 (안전성)
- 설명 추가 및 예시코드 변경
---

# match

```rust
fn find_prime_number(number: u32) {
    match number {
        1 => println!("{} 은 1입니다.", number),

        2 | 3 | 5 => println!("{} 은 prime number입니다.", number),

        4..=10 => println!("{} 은 4~10 사이의 숫자입니다.", number),

        n if n % 2 == 0 => println!("{} 은 짝수입니다.", number),

        _ => println!("{} 은 알 수 없는 숫자입니다!", number),
    }
}

fn main() {
    find_prime_number(5); // 5 is a prime number!
}
```

---

# match (tuple)
```rust
fn serve_coffee(coffee: bool, ice: bool, water: bool) -> &str {
    match (coffee, ice, water) {
        (false, _, _) => "차가운 물만 드세요",
        (_, false, _) => "따뜻한 아메리카노를 드세요",
        (_, _, false) => "아이스 에스프레소를 드세요",
        _ => "아이스 아메리카노를 드세요",
    }
}

fn main() {
    let coffee = true;
    let ice = false;
    let water = true;

    let drink = serve_coffee(coffee, ice, water);
    println!("Serve: {}", drink); // Serve: 따뜻한 아메리카노를 드세요
}
```

---

# result (bool)
- 실패할 가능성이 있는 값을 반환하는 Generic enum
- 예시 설명 추가 및 예시 코드 변경

---

# result (bool)
```rust
fn ok_or_err(is_success: bool) -> Result<String, String> {
    match is_success {
        true => {
            Ok(String::from("success!"))
        }
        false => {
            Err(String::from("error!"))
        }
    }
}

fn main() {
  println!("{:?}", ok_or_err(true)); // Ok("success!")
}
```

---

# result (파일 읽기)

```rust
use std::fs::File;
use std::io::{Error, Read};

fn read_receipt() -> Result<(), Error> {
    let file_name = "receipt.txt";

    let mut file = File::open(file_name)?;
    let mut contents = String::new();

    file.read_to_string(&mut contents)?;
    print!("{}", contents);

    Ok(())
}

fn main() {
  read_username_from_file().expect("TODO: panic message");
}
```

---

# trait
- Java의 interface와 유사
- trait bound 설명추가 및 예시 추가 (trait bound)

---

# trait 

```rust
use serde::{de::DeserializeOwned, Serialize};

pub trait FromDb {
    type Output;
    #[allow(clippy::wrong_self_convention)]
    fn from_db(self) -> Self::Output;
}

impl<T: FromDb> FromDb for Vec<T> where T: Send + Serialize + DeserializeOwned {
    type Output = Vec<T::Output>;
    #[allow(clippy::wrong_self_convention)]
    #[inline(always)]
    fn from_db(self) -> Self::Output {
        self.into_iter().map(FromDb::from_db).collect()
    }
}

impl<T: FromDb> FromDb for Option<T> {
    type Output = Option<T::Output>;
    #[allow(clippy::wrong_self_convention)]
    #[inline(always)]
    fn from_db(self) -> Self::Output {
        self.map(crate::FromDb::from_db)
    }
}
```

---

# trait
```rust
#[derive(Debug, Serialize, Deserialize)]
struct Person {
    name: String,
    age: u32,
}

impl FromDb for Person {
    type Output = Self;

    fn from_db(self) -> Self::Output {
        self
    }
}

fn main() {
    let persons: Vec<Person> = vec![
        Person { name: "John".to_string(), age: 30 },
        Person { name: "Jane".to_string(), age: 25 },
        Person { name: "Mike".to_string(), age: 40 },
    ];

    let persons_output: Vec<Person> = FromDb::from_db(persons);
    println!("{:?}", persons_output);

    let person: Option<Person> = Some(Person { name: "Alice".to_string(), age: 35 });

    let person_output: Option<Person> = FromDb::from_db(person);
    println!("{:?}", person_output);
}
```

---

# 참고자료

https://rinthel.github.io/rust-lang-book-ko/

https://www.youtube.com/watch?v=V6TR2FeCK5c 
(Rust 크로스 플랫폼 프로그래밍 | 인프콘 2022)

https://prev.rust-lang.org/ko-KR/faq.html

https://betterprogramming.pub/reading-and-writing-a-file-in-rust-47d2bc7086ac
