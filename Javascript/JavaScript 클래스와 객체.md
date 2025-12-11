# JavaScript 클래스와 객체

#javascript #클래스 #객체 #상속 #면접 #개념정리

**관련 개념:** [[JavaScript 함수]] [[프로토타입]] [[객체지향]] [[스프레드 연산자]]

---

## 핵심 개념

JavaScript는 클래스 기반 객체지향 프로그래밍을 지원하며, 객체 리터럴로도 객체를 생성할 수 있습니다.

---

## 클래스 선언

### 기본 클래스

```javascript
class Person {
    // 생성자: 객체 초기화
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    getName() {
        return this.name;
    }

    setName(name) {
        this.name = name;
    }

    printInfo() {
        console.log(`이름 : ${this.name}, 나이 : ${this.age}`);
    }
}

const user1 = new Person("Jeameon", 22);
user1.printInfo();
```

---

## 클래스 상속

```javascript
class Student extends Person {
    constructor(name, age, gender, grade) {
        super(name, age); // 부모 생성자 호출
        this.gender = gender;
        this.grade = grade;
    }

    introduce() {
        console.log(`이름 : ${this.name}, 나이 : ${this.age}, 성별 : ${this.gender}`);
    }
}

const student1 = new Student("Jeameon", 22, "male", 1);
student1.printInfo(); // 부모 메서드 사용 가능
student1.introduce(); // 자식 메서드
```

---

## 객체 리터럴

클래스 없이도 객체를 만들 수 있습니다.

```javascript
const car = {
    name: "소나타",
    brand: "현대",
    year: 2025,
    speed: 0,
    start: function() {
        console.log("차가 출발합니다.");
    },
    stop: function() {
        console.log("차가 정지합니다.");
    }
}

car.start();
```

---

## 스프레드 연산자로 객체 복사

```javascript
const car2 = {
    ...car,           // 기존 속성 복사
    name: "모닝",     // 덮어쓰기
    year: 2024,
    color: "흰색"     // 새 속성 추가
}
```

**용도**: React에서 불변성을 지키기 위해 사용

---

## static 메서드

인스턴스 없이 클래스에서 직접 호출

```javascript
class Math {
    static add(a, b) { 
        return a + b; 
    }
}

console.log(Math.add(1, 2)); // 3
```

---

## 관련 노트

- [[JavaScript 함수]] - 메서드와 함수의 차이
- [[프로토타입]] - 클래스의 내부 동작 원리
- [[객체지향]] - 객체지향 프로그래밍 개념
- [[스프레드 연산자]] - 객체 복사와 불변성

---

**태그:** #javascript #클래스 #객체 #상속 #면접 #개념정리

