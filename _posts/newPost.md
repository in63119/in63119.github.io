---
title: Rust - 변수 선언하기
author: IN
date: 2023-12-24 13:21:00 +0800
categories: [Blogging, Blockchain]
tags: [Core, Rust]
pin: true

---

> 관련 레포: [https://github.com/in63119/Core-practice](https://github.com/in63119/Core-practice)

<br />
<br />

### 2-3. 변수 선언하기

Rust는 JS와 비슷하게 변수를 선언하려면 `let`과 `const`를 사용 할 수 있다.
<br />

나는 이제 `sha2` 크레이트를 사용하기 위해 `main.rs`를 다음과 같이 코드를 짰다.

> 코드 해석은 다음 포스트로...
> <br />

```rust
use sha2::{Sha256, Digest};

fn main() {
    // 해싱할 문자열
    let data = "Hello, world!";

    // SHA-256 객체 생성
    let mut hasher = Sha256::new();

    // 데이터 업데이트
    hasher.update(data);

    // 해시 계산 완료
    let result = hasher.finalize();

    // 결과 출력
    println!("SHA-256 hash of '{}' is {:?}", data, result);
}
```

<br />

`main 함수`의 처음 줄을 보면, data라는 변수에 "Hello, world!" 문자열을 담았다.
<br />

```rust
...

let data = "Hello, world!";

...
```

<br />

이것만 보면 JS와 다른 것이 없다. 먼저, Rust는 JS와 같이 `let`, `const`를 사용한다. 하지만 Rust의 이 `let`, `const`는 JS와 다른 비밀이 있다.
<br />

**Rust vs JS의 `let`, `const`**

- `let`
  - JS: `let` 키워드로 선언된 변수는 항상 가변(mutable) 변수이다.
  - Rust:
    - `let` 키워드로 선언된 변수는 기본적으로 불변(immutable)이지만, 가변(mutable)이 가능하다.
    - `let` 키워드로 선언된 변수를 가변(mutable)으로 만들기
      ```rust
      let x = 5; // 불변 변수 선언
      let mut y = 5; // 가변 변수 선언
      y = 10; // 가능
      ```
- `const`

  - JS: `const` 키워드로 선언된 변수는 항상 불변(immutable) 변수이다.
    (배열(array)같은 참조 타입의 자료형의 내부 요소들은 변경 가능하다.)
  - Rust:

    - `const`는 컴파일 타임에 결정되는 상수를 선언하는 데 사용된다.
      > - 컴파일 타임: 프로그램이 실행되기 전, 소스 코드가 실행 가능한 기계 코드로 변환되는 과정을 의미한다. 이 과정에서 프로그램의 구조, 타입 체크, 변수 할당 등이 수행되는 것.
    - 상수는 항상 불변(immutable)이며, 타입을 명시적으로 지정해야 한다.

      ```rust
      // 타입 명시 예제 코드
      // u32는 솔리디티의 uint32라고 보면 된다.
        // u32: 부호 없는 32비트 정수 (0 ~ 4,294,967,295)
      // 여기서 숫자의 '_'는 가독성을 위해 사용된다.(세 자리 마다 쓰임)
      const MAX_POINTS: u32 = 100_000;
      ```

    - `const`는 전역 상수 또는 모듈 수준에서 주로 사용됩니다.
      - 전역 상수: JS로 치면 전역 스코프에서 선언되어 사용된다는 말이다.
      - 모듈 수준:
        - Rust에서 모듈 수준이란 특정 모듈 내에서 정의되고 사용되는 함수, 구조체, 상수 등을 의미한다.
        - 모듈은 Rust 코드를 구조화하고 관련된 기능을 그룹화하는 데 사용된다. 예를 들어, 관련된 함수들을 하나의 모듈로 묶어 관리할 수 있다.
        - 모듈 내에서 정의된 요소들은 기본적으로 해당 모듈 내부에서만 접근 가능하다. 하지만 pub 키워드를 사용하여 다른 모듈에서도 접근할 수 있도록 만들 수 있다.(약간 솔리디티에서 접근 제한 관리 같은 너낌)


