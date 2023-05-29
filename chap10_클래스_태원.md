## Chap 10장 클래스


### 캡슐화

- 테스트 코드가 함수를 호출하거나 변수를 사용해야한다면 `protected`로 선언하기도 한다
- 그러나 가급적 공개하지 않는 편이 좋다

### 클래스는 작아야한다
- 클래스가 맡은 "책임" 을 기준으로 크기를 책정한다
- 메서드의 개수와 관련이 없다
- 클래스를 표현하는 이름을 간결하게 사용할 정도의 책임을 할당해야한다

### 단일 책임 원칙
- 클래스나 모듈을 변경할 이유는 단 하나뿐이어야 한다는 원칙
- 객체 지향 설계에서 중요한 개념이다
- 큰 클래스가 아닌, 작은 클래스 여럿으로 이워진 시스템이 더 적합하며 작은 클래스들은 서로 협력해 동작을 수행해야한다


### 응집도
- 클래스의 인스턴스 변수의 수는 작아야 한다
- 응집도가 높은 클래스 (= 메서드가 변수를 더 많이 사용하고, 모든 인스턴스 변수를 메서드마다 사용하는 클래스)를 선호한다
- 응집도가 높으면 메서드와 변수를 논리적인 단위로 묶이 쉽다는 의미

```Java
public class Stack( {
   private int topOfStack = 0;
   List<Integer> elements = new LinkedList<Integer>();
   
   public int size() {
      return topOfStack;
   }
   
   public void push(int element) {
      topOfStack++;
      elements.add(element);
   }
   
   public int pop() throws PoppedWhenEmpty {
      if (topOfStack == 0)
         throw new PoppedWhenEmpty();
      int element = elements.get(--topOfStack);
      elements.remove(topOfStack);
      return element;
   }
}

// 위 클래스는 응집도가 높다. push(), pop()메소드 모두 topOfStack, elements 변수를 사용하기 때문
```


>> 응집도를 유지하면 작은 클래스가 여럿 생긴다 (기존의 응집도가 낮았던 코드를 분리하자 코드가 길어짐)


### 변경하기 쉬운 클래스 
- 이상적인 시스템에서는 새 기능을 추가할때 기존 코드를 변경하지 않는다. (확장한다)


```Java

public class Sql {
    public Sql(String table, Column[] columns)
    public String create()
    public String insert(Object[] fields)
    public String selectAll()
    public String findByKey(String keyColumn, String keyValue)
    public String select(Column column, String pattern)
    public String select(Criteria criteria)
    public String preparedInsert()
    private String columnList(Column[] columns)
    private String valuesList(Object[] fields, final Column[] columns) 
    private String selectWithCriteria(String criteria)
    private String placeholderList(Column[] columns)
}

// 위 코드는 새로운 SQL문 지원에 대해 반드시 Sql클래스에 직접적인 수정을 구현해야한다. -> SRP(단일책임원칙)위반

```



```Java

abstract public class Sql {
    public Sql(String table, Column[] columns)
    abstract public String generate();
}
 
public class CreateSql extends Sql {
    public CreateSql(String table, Column[] columns)
    @Override public String generate()
}
 
public class SelectSql extends Sql {
    public SelectSql(String table, Column[] columns)
    @Override public String generate()
}
 
public class InsertSql extends Sql {
    public InsertSql(String table, Column[] columns, Object[] fields)
    @Override public String generate()
    private String valuesList(Object[] fields, final Column[] columns)
}
 
public class SelectWithCriteriaSql extends Sql {
    public SelectWithCriteriaSql(
    String table, Column[] columns, Criteria criteria)
    @Override public String generate()
}
 
public class SelectWithMatchSql extends Sql {
    public SelectWithMatchSql(String table, Column[] columns, Column column, String pattern)
    @Override public String generate()
}
 
public class FindByKeySql extends Sql public FindByKeySql(
    String table, Column[] columns, String keyColumn, String keyValue)
    @Override public String generate()
}
 
public class PreparedInsertSql extends Sql {
    public PreparedInsertSql(String table, Column[] columns)
    @Override public String generate() {
    private String placeholderList(Column[] columns)
}
 
public class Where {
    public Where(String criteria) 
    public String generate()
}
 
public class ColumnList {
    public ColumnList(Column[] columns) 
    public String generate()
}

// 위 코드는 기존의 공개 인터페이스를 각각 파생클래스로 분리하였다.
// 클래스가 분리되었기때문에 SQL추가 및 수정에 대해 SRP, OCP를 준수
```

### 변경으로부터의 격리
- **인터페이스** 와 **추상 클래스**를 사용해 영향을 격리해야한다.
- 상세한 규현에 의존하는 클라이언트는 구현에 따라 변경이 강제되어 위험함 
- 테스트 또한 쉽지 않다. (실제 5분마다 값이 달라지는 API를 사용하며 테스트하면 코드 짜기가 쉽지않음)
- 결합도를 낮추자



