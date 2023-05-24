# Chap09 단위테스트

### TDD 법칙 세가지

1. 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않기
2. 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 작성하기
3. 현재 실패하는 테스트를 통과할 정도로만 코드 작성하기

-> 위 기본 규칙은 빙산의 일각에 불과하다. 수 많은 테스트 코드가 생성되면 관리문제가 발생하기때문


### 테스트 코드는 실제 코드 못지 않게 중요하다"

> 저자의 과거 경험
> 1. 실제 코드가 변화하면 테스트 코드도 변화해야하는 문제점 발생
> 2. 테스트 코드 작성에 공수가 과한 공수가 들어가고 결국 폐기하게 됨
> 3. 테스트 코드가 없기때문에 코드의 검증이 불가
> 4. 결함율이 높아지고 코드가 망가지기 시작한다.
> 5. 즉 테스트 코드 작성은 매우 중요


### 단위테스트를 통한 유연성, 유지보수성, 재사용성의 활용

- 테스트 케이스가 있다면 변경에 대한 버그 발생에 대한 방지가 가능하다
- 즉 테스트 케이스는 "변경"을 보다 용이하게 할 수 있다.


### 깨끗한 테스트 코드

1. 가독성을 높일 것
- "BUILD-OPERATE-CHECK" 패턴을 사용하면 "테스트 자료 생성 - 테스트 자료 조작 - 조작된 결과 확인"에 해당하는 케이스에 적합하다
- 잡다하고 세세한 코드를 지웠다는 사실에 주목할 것

```java

// 수정 전 (자질 구레한 사항이 너무 많아 가독성이 떨어진다)

public void testGetPageHieratchyAsXml() throw Exception{

  crawler.addPager(root,PathParser.parse("PageOne"));
  crawler.addPager(root,PathParser.parse("PageTow"));
  
  request.setResorce("root")
  request.addInput("type","page");
  
  { .... }
  
  
  assertEquals("text/xml" , response.getContentType())
  assetSubString("<name>PageOne</name>", xml)
  assetSubString("<name>PageTwo</name>", xml)
 }
 
 ```
 
 ```java

// 수정 후 (생성, 조작, 결과확인 로직이 명확하게 분리되어있음)

public void testGetPageHieratchyAsXml() throw Exception{

  makePages("PageOne, "PageTwo")
  
  submitRequet("root" , "type=pages")
  
  assertResponseIsXML();
  asserResponseContatins("<name>PageOne</name>, "<name>PageTwo</name>)
  
 }
 
 ```

> BUILD-OPERATE-CHECK이란?
>
> (BOC)는 소프트웨어 개발, 특히 테스트 주도 개발(Test-Driven Development, TDD)에서 사용되는 패턴입니다. 이 패턴은 코드 테스트를 위한 과정을 세 단계로 나눕니다
>
> - Build: 테스트를 위한 초기 설정을 수행합니다. 이 단계에서는 테스트에 필요한 데이터를 생성하고, 테스트 대상 객체를 인스턴스화하고, 필요한 경우 테스트에 사용될 목(mock) 객체를 설정하는 등의 작업을 수행합니다.
>
> - Operate: 테스트 대상 코드를 실행합니다. 이 단계에서는 테스트 대상 메소드를 호출하고, 그 결과를 변수에 저장하는 등의 작업을 수행합니다.
> 
> - Check: 실행 결과가 예상한 대로인지 확인합니다. 이 단계에서는 주로 assert 문을 사용하여 테스트 결과와 예상 결과를 비교합니다. 결과가 예상과 다를 경우, 테스트는 실패로 판정됩니다.
>
> BOC 패턴은 코드가 예상대로 작동하는지 확인하는 효과적인 방법을 제공하며, 테스트 주도 개발의 핵심 원칙 중 하나입니다. 이 패턴은 테스트 코드가 깔끔하고 명확하게 유지될 수 있도록 돕습니다.



2. 도메인에 특화된 테스트 언어
- 위 수정후 코드는 DSL(도메인에 특화된 언어)를 활용한 기법
- API위에 함수와 유틸리틴를 구현해서 가독성을 높여준다.


> DSL?
>  DSL(Domain-Specific Language)는 특정 도메인 또는 분야에 특화된 프로그래밍 언어

3. 이중 표준
- 테스트 코드는 실제 API와 다르게 효율적일 필요가 없다.
- 가독성을 위해 실제 코드와 다르게 처리할 필요가 있다 (테스트 환경의 자원은 제한적이기때문)


4. 테스트 당 assert 하나? 테스트 당 개념하나
- 각 함수마다 하나의 assert문을 사용한다면 "결론이 하나" 이기때문에 코드 이해와 가독성이 높아지는 것은 사실이다
- 그러나 테스트를 과하게 분리하면 중복되는 코드가 많아진다는 단점이 발생 (최대한 줄이는 방향은 좋다)
- 따라서 개념당 asser문을 최대한 줄이지만, 테스트 함수 하나에서는 하나의 개념을 테스트하는게 좋다

```java
// 아래의 예지는 날짜와 관련된 개념을 테스트한다. 31로 끝나거나, 30일로 끝나거나 등등..

public void testAddMonths() {
  SerialDate d1 = SerialDate.createInstance(31, 5, 2004);

  SerialDate d2 = SerialDate.addMonths(1, d1); 
  assertEquals(30, d2.getDayOfMonth()); 
  assertEquals(6, d2.getMonth()); 
  assertEquals(2004, d2.getYYYY());

  SerialDate d3 = SerialDate.addMonths(2, d1); 
  assertEquals(31, d3.getDayOfMonth()); 
  assertEquals(7, d3.getMonth()); 
  assertEquals(2004, d3.getYYYY());

  SerialDate d4 = SerialDate.addMonths(1, SerialDate.addMonths(1, d1)); 
  assertEquals(30, d4.getDayOfMonth());
  assertEquals(7, d4.getMonth());
  assertEquals(2004, d4.getYYYY());
}


```



5. 정리 - 깨끗한 코드의 다섯가지 규칙

1. 빨라야한다. 자주 돌려서 문제를 확인하고 초반에 문제를 찾아내기 위함
2. 독립적이어야 한다. 각 테스트는 서로 의존해서는 안된다 (단위 테스트 기준)
3. 반복가능해야한다. 환경에 따라 돌아가지 않는 경우가 생기면 안됨
4. 자가검증테스트는 boolean값을 결과로. 성공과 실패 오직 두 결과를 통해 객관적인 평가를 수행해야한다
5. 적시에 해야한다. 단위테스트는 테스트 하려는 실제 코드를 구현하기 직전에 작성할 것. 

----

부록

## 테스트 코드의 종류

단위 테스트(Unit Test): 단위 테스트는 소프트웨어의 가장 작은 부분, 즉 "유닛"을 테스트하는 방법입니다. 이는 일반적으로 개별 함수나 메소드에 대한 테스트를 포함합니다.

-> 이 테스트는 소프트웨어의 기본 빌딩 블록이기 때문에, 대부분의 테스트 작업을 단위 테스트로 수행하는 것이 일반적

통합 테스트(Integration Test): 통합 테스트는 여러 유닛이 제대로 작동하는지 확인하기 위해 그들이 함께 작동하는 방식을 테스트합니다. 이는 일반적으로 단위 테스트 이후에 수행됩니다.

-> 단위 테스트만으로는 발견하기 어려운 문제를 찾아내는 데 중요합니다. 이들은 여러 컴포넌트나 서비스가 서로 잘 통신하는지 확인

시스템 테스트(System Test): 시스템 테스트는 완전한 시스템의 기능을 검증합니다. 이는 일반적으로 모든 컴포넌트가 통합된 후에 수행됩니다.

->  애플리케이션이 전체적으로, 그리고 실제 사용자에게 제공될 때 잘 작동하는지 확인하는 데 중요

회귀 테스트(Regression Test): 회귀 테스트는 소프트웨어의 변경이 기존 기능에 부정적인 영향을 미치지 않았는지 확인하기 위해 수행됩니다. 이는 코드의 변경이나 업데이트 후에 수행됩니다.

성능 테스트(Performance Test): 성능 테스트는 소프트웨어의 성능, 예를 들어 응답 시간이나 처리량 등을 측정하고, 성능 기준이 충족되는지 검증합니다.

허용 테스트(Acceptance Test): 허용 테스트는 종종 사용자 스토리나 비즈니스 요구 사항을 충족하는지 확인하기 위해 수행됩니다. 이는 일반적으로 개발 프로세스의 마지막 단계에서 수행됩니다.


"모든 테스트 유형이 중요하지만, 일반적으로 단위 테스트는 개발자가 일상적으로 많이 사용하고, 자주 작성하는 테스트입니다.  이유는 단위 테스트가 개별 기능과 메소드를 테스트하기 때문에, 문제를 빠르게 찾고 해결할 수 있기 때문입니다. 통합 테스트와 시스템 테스트는 보통 더 큰 시스템의 부분을 테스트하기 때문에 실행 시간이 더 오래 걸릴 수 있지만, 중요한 로직과 통합 부분에 대해 꼼꼼하게 테스트하는 것이 중요"


