# 3장-함수

## 작게 만들어라!
함수는 되도록 한 함수당 3~5줄 이내로 줄이는 것을 권장한다.

## 한 가지만 해라!

- 함수는 한 가지를 해야 한다. 그 한 가지를 잘 해야 한다. 그 한가지만을 해야한다.
- 한가지를 한다는 것은 무엇일까? 아래 코드에서 하는 일을 나열해보자.
  ```java
  public static String renderPageWithSetupsAndTeardowns(
  	PageData pageData, boolean isSuite) throws Exception {
  	if (isTestPage(pageData))
  		includeSetupAndTeardownPages(pageData, isSuite);
  	return pageData.getHtml();
  }
  ```
  - 테스트 페이지인지 아닌지 판단한다.
  - 테스트 페이지라면 설정 페이지와 해제 패이지를 넣는다.
  - pageData로 HTML을 생성한다.
- 함수가 한가지를 한다는 것 = 지정된 함수 이름 아래 추상화 수준이 하나인 단계만 수행하는 함수


## 함수 당 추상화 수준은 하나로!

- 함수가 한가지 작업만 하려면 함수 내 모든 문장의 추상화 수준이 동일해야 한다.
- 근본 개념과 세부사항을 뒤섞기 시작하면, 깨어진 창문처럼 사람들이 함수에 세부사항을 점점 더 추가한다.

## switch 문

- switch 문은 작게 만들기 어렵다. 또한 한 가지 작업만 하는 switch 문을 만들기 어렵다.
- 하지만 많은 경우에 다형성(polymorphism)을 사용한다면 해결할 수 있다. 이를 통해 ‘함수는 한 가지 일만 해야한다’는 클린코드 컨셉을 유지할 수 있다.
- 다형성 : **하나의 객체가 여러 가지 타입을 가질 수 있는 것을 의미**
  합니다. 자바에서는 이러한 다형성을 부모 클래스 타입의 참조 변수로 자식 클래스 타입의 인스턴스를 참조할 수 있도록 하여 구현하고 있습니다.
- Bad

```java
  public Money calculatePay(Employee e) throws InvalidEmployeeType {
	switch (e.type) { 
		case COMMISSIONED:
			return calculateCommissionedPay(e); 
		case HOURLY:
			return calculateHourlyPay(e); 
		case SALARIED:
			return calculateSalariedPay(e); 
		default:
			throw new InvalidEmployeeType(e.type); 
	}
}
```

- Good

```java
  public abstract class Employee {
	public abstract boolean isPayday();
	public abstract Money calculatePay();
	public abstract void deliverPay(Money pay);
}
-----------------
public interface EmployeeFactory {
	public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType; 
}
-----------------
public class EmployeeFactoryImpl implements EmployeeFactory {
	public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
		switch (r.type) {
			case COMMISSIONED:
				return new CommissionedEmployee(r) ;
			case HOURLY:
				return new HourlyEmployee(r);
			case SALARIED:
				return new SalariedEmploye(r);
			default:
				throw new InvalidEmployeeType(r.type);
		} 
	}
}
```

## 서술적인 이름을 사용하라!

- 길고 서술적인 이름이 짧고 어려운 이름보다 좋다.
- IDE를 사용해 이런저런 이름을 시도한 후 최대한 서술적인 이름을 고르자.




## 함수 인수  
함수에서 이상적인 인수 개수는 0개(무항).
인수는 코드 이해에 방해가 되는 요소이므로 최선은 0개이고, 차선은 1개뿐인 경우이다.
출력인수(함수의 반환 값이 아닌 입력 인수로 결과를 받는 경우)는 이해하기 어려우므로 왠만하면 쓰지 않는 것이 좋겠다.

#### 많이 쓰는 단항 형식  
  - 인수에 질문을 던지는 경우  
	`boolean fileExists(“MyFile”);`  
  - 인수를 뭔가로 변환해 결과를 변환하는 경우  
	`InputStream fileOpen(“MyFile”);`  
  - 이벤트 함수일 경우 (이 경우에는 이벤트라는 사실이 코드에 명확하게 드러나야 한다.)

위의 3가지가 아니라면 단항 함수는 가급적 피하는 것이 좋다.

#### 플래그 인수  
플래그 인수는 추하다. 쓰지마라. bool 값을 넘기는 것 자체가 그 함수는 한꺼번에 여러가지 일을 처리한다고 공표하는 것과 마찬가지다.

#### 이항 함수  
단항 함수보다 이해하기가 어렵다.
Point 클래스의 경우에는 이항 함수가 적절하다.
2개의 인수간의 자연적인 순서가 있어야함 
`Point p = new Point(x,y);`
무조건 나쁜 것은 아니지만, 인수가 2개이니 만큼 이해가 어렵고 위험이 따르므로 가능하면 단항으로 바꾸도록

#### 삼항 함수  
이항 함수보다 이해하기가 훨씬 어려우므로, 위험도 2배 이상 늘어난다.
삼항 함수를 만들 때는 신중히 고려하라.

#### 인수 객체  
인수가 많이 필요할 경우, 일부 인수를 독자적인 클래스 변수로 선언할 가능성을 살펴보자
x,y를 인자로 넘기는 것보다 Point를 넘기는 것이 더 낫다.

#### 인수 목록  
때로는 String.format같은 함수들처럼 인수 개수가 가변적인 함수도 필요하다. 
String.format의 인수는 List형 인수이기 때문에 이항함수라고 할 수 있다.

#### 동사와 키워드
단항 함수는 함수와 인수가 동사/명사 쌍을 이뤄야한다.  
`writeField(name);`  
함수이름에 키워드(인수 이름)을 추가하면 인수 순서를 기억할 필요가 없어진다.  
`assertExpectedEqualsActual(expected, actual);`  
## 부수 효과를 일으키지 마라


- 부수 효과는 거짓말이다. 함수에서 한 가지를 하겠다고 약속하고선 남몰래 다른 짓도 하니까.
- 한 함수에서는 딱 한가지만 수행할 것!
- 아래 코드에서 Session.initialize();는 함수명과는 맞지 않는 부수효과이다.
``` java
public class UserValidator {
	private Cryptographer cryptographer;
	public boolean checkPassword(String userName, String password) { 
		User user = UserGateway.findByName(userName);
		if (user != User.NULL) {
			String codedPhrase = user.getPhraseEncodedByPassword(); 
			String phrase = cryptographer.decrypt(codedPhrase, password); 
			if ("Valid Password".equals(phrase)) {
				Session.initialize();
				return true; 
			}
		}
		return false; 
	}
}
```

#### 출력인수  
   일반적으로 출력 인수는 피해야 한다.   
   함수에서 상태를 변경해야 한다면 함수가 속한 객체 상태를 변경하는 방식을 택하라.

## 명령과 조회를 분리하라
함수는 뭔가 객체 상태를 변경하거나, 객체 정보를 반환하거나 둘 중 하나다. 둘 다 수행해서는 안 된다.  
`public boolean set(String attribute, String value);`같은 경우에는 속성 값 설정 성공 시 true를 반환하므로 괴상한 코드가 작성된다.  
`if(set(“username”, “unclebob”))...` 그러므로 명령과 조회를 분리해 혼란을 주지 않도록 한다.  

## 오류코드보다 예외를 사용하라!

try/catch를 사용하면 오류 처리 코드가 원래 코드에서 분리되므로 코드가 깔끔해 진다.

#### Try/Catch 블록 뽑아내기  

```java
if (deletePage(page) == E_OK) {
	if (registry.deleteReference(page.name) == E_OK) {
		if (configKeys.deleteKey(page.name.makeKey()) == E_OK) {
			logger.log("page deleted");
		} else {
			logger.log("configKey not deleted");
		}
	} else {
		logger.log("deleteReference from registry failed"); 
	} 
} else {
	logger.log("delete failed"); return E_ERROR;
}
```

정상 작동과 오류 처리 동작을 뒤섞는 추한 구조이므로 if/else와 마찬가지로 블록을 별도 함수로 뽑아내는 편이 좋다.

```java
public void delete(Page page) {
	try {
		deletePageAndAllReferences(page);
  	} catch (Exception e) {
  		logError(e);
  	}
}

private void deletePageAndAllReferences(Page page) throws Exception { 
	deletePage(page);
	registry.deleteReference(page.name); 
	configKeys.deleteKey(page.name.makeKey());
}

private void logError(Exception e) { 
	logger.log(e.getMessage());
}
```

오류 처리도 한가지 작업이다.


## 반복하지 마라!

- 중복을 제거하기 위해 노력해야 한다. 중복된 코드는 어떤 로직을 변경할 때 한 곳 이상을 변경해야 하기 때문이다.
- 많은 원칙과 기법이 중복을 없애거나 제어할 목적으로 나왔다.
  - 객체지향 프로그래밍은 코드를 부모 클래스로 몰아 중복을 없앤다.
- 중복을 제거하여 추상화를 올바르게 하고자 **SOLID 원칙**을 따른다.


## 구조적 프로그래밍  
다익스트라의 구조적 프로그래밍의 원칙을 따르자면 모든 함수와 함수 내 모든 블록에 입구와 출구가 하나여야 된다. 즉, 함수는 return문이 하나여야 되며, **루프 안에서 break나 continue를 사용해선 안된며 goto는 절대로, 절대로 사용하지 말자.** 함수가 클 경우에만 상당 이익을 제공하므로, 함수를 작게 만든다면 오히려 여러차례 사용하는 것이 함수의 의도를 표현하기 쉬워진다.

그런데 구조적 프로그래밍의 목표와 규율은 공감하지만 함수가 작다면 위 규칙은 별 이익을 제공하지 못한다. 함수가 아주 클 때만 상당한 이익을 제공한다. 그러므로 함수를 작게 만든다면 간혹 return, break, continue를 사용해도 괜찮다. 오히려 때로는 단일 입/출구 규칙보다 의도를 표현하기 쉬워진다.


## 함수를 어떻게 짜죠?  
처음에는 길고 복잡하고, 들여쓰기 단계나 중복된 루프도 많다. 인수목록도 길지만, 이 코드들을 빠짐없이 테스트하는 단위 테스트 케이스도 만들고, 
코드를 다듬고, 함수를 만들고, 이름을 바꾸고, 중복을 제거한다. 처음부터 탁 짜지지는 않는다.
