## Chap01 깨끗한 코드


### 나쁜 코드란?

사례1) "킬러 앱"을 구현한 회사의 몰락 : 서두르고 급하게 코드를 짠 탓에 체계화된 좋은 코드를 작성하지 못해 결국 망한 사례

-> 좋지 않은 코드가 쌓일수록 코드 해독에 더 많은시간이 걸리고 생산성이 낮아지게된다.

"시간을 들여 깨끗한 코드를 만드는 노력이 비용을 절감하는 방법일 뿐 아니라 전문가로서 살아남는 길"

### 깨끗한 코드란?

- "보기에 즐거운 코드"
- "CPU 자원을 낭비하지 않는 코드"
- "각 함수와 클래스와 모듈이 독립적으로 분리되어있는 코드"   
   - 객체가 여러 기능을 수행한다면 여러 객체로 나누고 메서드도 마찬가지로 하나의 기능만 수행하도록 분리한다"
- "명확한 변수명과 컨벤션 기반의 가독성이 높은 코드"
- "테스트 케이스가 있는 코드"
- "중복이 없는 코드"


### 결론 
이 책에서는 깨끗한 변수, 함수, 클래스를 만드는 방법을 포함해 오브젝트 관점의 깨끗한 코드를 설명한다.

------

## 기타 조사 자료

클린 코드를 위한 가이드라인

1. 함수와 클래스, 모듈 등은 독립적으로 분리되어야 합니다. 하나의 함수나 클래스는 한 가지 기능만 수행하도록 작성하는 것이 좋습니다. 이렇게 하면 코드를 이해하기 쉬워지고 유지보수성이 높아집니다.

```java

// 나쁜 코드
public static String getUserInfo(int userId) {
    // 유저 정보 조회 및 가공 로직
    return userInfo;
}


// 좋은 코드
public class User {
    private int id;
    private String name;
    private int age;
    private String email;
    
    // Getter/Setter 메소드 생략
    
    public void save() {
        // DB에 유저 정보 저장 로직
    }
}


// 나쁜 코드에서는 getUserInfo() 하나의 함수에서 여러 가지 기능을 수행

// 좋은 코드에서는 하나의 User 클래스에서 유저 정보를 저장하고, 저장된 정보를 DB에 저장하는 기능만을 수행
```



2. 둘째, 변수명과 컨벤션은 명확하고 가독성이 높아야 합니다. 변수명은 함수나 클래스의 역할을 명확히 나타내야 하며, 코드 컨벤션에 따라 작성하는 것이 좋습니다.


```java

// 나쁜 코드
public static int add(int num1, int num2) {
    int result = num1 + num2;
    return result;
}

// 좋은 코드
public static int calculateSum(int operand1, int operand2) {
    int sum = operand1 + operand2;
    return sum;
}

```




3. 셋째, 테스트 케이스는 코드를 작성할 때 함께 작성하는 것이 좋습니다. 테스트 케이스를 작성하면 코드의 버그를 사전에 발견할 수 있으며, 코드의 변경이나 업데이트 시 테스트 케이스를 활용하여 코드의 정확성을 보장할 수 있습니다.


```java

// 나쁜 코드
public static int divide(int num1, int num2) {
    int result = num1 / num2;
    return result;
}


// 좋은 코드
public static int divide(int dividend, int divisor) {
    if (divisor == 0) {
        throw new IllegalArgumentException("Divisor cannot be zero.");
    }
    int quotient = dividend / divisor;
    return quotient;
}

@Test
public void testDivide() {
    assertEquals(2, divide(6, 3));
    assertEquals(-2, divide(6, -3));
    assertEquals(0, divide(0, 10));
    assertThrows(IllegalArgumentException.class, () -> divide(6, 0));
}

```



4. 넷째, 중복 코드는 최소화해야 합니다. 중복 코드가 많을수록 코드의 유지보수성이 떨어지기 때문입니다.


```java

// 나쁜 코드
public static void printName(String name) {
    System.out.println("My name is " + name);
}

public static void printAge(int age) {
    System.out.println("My age is " + age);
}

public static void printGender(String gender) {
    System.out.println("My gender is " + gender);
}

// 좋은 코드
public static void printInfo(String label, String value) {
    System.out.println("My " + label + " is " + value);
}

```
