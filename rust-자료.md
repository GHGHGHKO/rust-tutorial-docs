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

# Option (unwrap, match)
- nullable한 표현 가능한 enum
```rust
fn give_drink(drink: Option<&str>) {
    match drink {
        Some("lemonade") => println!("너무 달아요"),
        Some(inner)   => println!("{}? 좋아요", inner),
        None          => println!("안마신다구요?"),
    }
}
```
```rust
fn drink(drink: Option<&str>) {
    let inside = drink.unwrap();
    if inside == "lemonade" { panic!("아아아악!!!!!"); }

    println!("{}는 좋아요!!!!!", inside);
}
```
```rust
fn main() {
    let water  = Some("water");
    let lemonade = Some("lemonade");
    let void  = None;

    give_adult(water);
    give_adult(lemonade);
    give_adult(void);

    let coffee = Some("coffee");
    let nothing = None;

    drink(coffee);
    drink(nothing);
}
```

---

# match
- switch와 비슷하지만, match는 모든 케이스를 표현해야함 (안전성)
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
- 다형성을 지원
```rust
struct BankAccount {
    owner: String,
    balance: f64,
}

impl BankAccount {
    // 계좌 생성 메서드
    fn new(owner: String, balance: f64) -> Self {
        BankAccount {
            owner,
            balance,
        }
    }

    // 입금 메서드
    fn deposit(&mut self, amount: f64) {
        self.balance += amount;
        println!("{}님의 계좌에 {}원이 입금되었습니다.", self.owner, amount);
    }

    // 출금 메서드
    fn withdraw(&mut self, amount: f64) -> Result<(), String> {
        if amount <= self.balance {
            self.balance -= amount;
            println!("{}님의 계좌에서 {}원이 출금되었습니다.", self.owner, amount);
            Ok(())
        } else {
            Err("잔액이 부족합니다.".to_string())
        }
    }

    // 잔액 조회 메서드
    fn check_balance(&self) {
        println!("{}님의 현재 잔액은 {}원입니다.", self.owner, self.balance);
    }
}
```
```rust
let mut account = BankAccount::new("고민수".to_string(), 1000.0);
    account.check_balance(); // 현재 잔액 조회: 1000원
    account.deposit(500.0); // 500원 입금
    account.check_balance(); // 현재 잔액 조회: 1500원
    account.withdraw(200.0).unwrap(); // 200원 출금
    account.check_balance(); // 현재 잔액 조회: 1300원
    account.withdraw(2000.0)
        .unwrap_or_else(|error| println!("에러: {}", error)); // 잔액 부족으로 인한 출금 실패, 에러 출력
```

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

https://betterprogramming.pub/reading-and-writing-a-file-in-rust-47d2bc7086ac
