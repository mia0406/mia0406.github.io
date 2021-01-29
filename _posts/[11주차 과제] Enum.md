
# 11주차 과제 : Enum

### 목표
자바의 열거형에 대해 학습하세요.

### 학습할 것 (필수)
- enum 정의하는 방법
- enum이 제공하는 메소드(values()와 valueOf())
- java.lang.Enum
- EnumSet
---
### 1. Enum 정의하는 방법

#### (1) Enum이란?
- 관련이 있는 상수(고정값)들의 집합
- Java 1.5부터 지원
- java.lang 패키지의 최종 하위 클래스
- 모든 enum 클래스는 java.lang.enum 이외에 상속을 받을 수 없다.
- 모든 enum types은 reference type이며, 모든 enum type은 enum 선언으로 시작된다.

#### (2) 특징
1. 모든 enum은 static final 성격을 가지고 있다.
2. enum 인스턴스를 비교할 때 == 또는 .equals() 메소드 모두 가능 
    - == 연산자와 equals 메소드 모두 사용할 수 있는 이유?
        - enum은 인스턴스를 생성할 수 없기 때문에 오직 하나의 인스턴스만 가질 수 있다. 하나의 인스턴스만 가질 수 있기 때문에 주소값을 비교하는 == 연산자를 사용해도, 실제값을 비교하는 equals 메소드를 사용해도 동일한 결과를 받을 수 있다.
    - 둘 다 사용해도 무방하나, == 을 쓰는 것을 더 권장
        - (근거 1) == 은 equals() 메소드와 달리 유형 호환성 검사를 진행한다. 호환성 검사를 진행함으로써 서로 다른 enum 클래스 상수를 비교하는 컴파일 오류를 잡아낼 수 있다.
            - equals 메소드를 사용하게 된다면 값 자체가 동일하다면 문제가 없을 것이라고 판단 내릴 것이다. 반면에 == 연산자는 주소값을 비교하기 때문에 컴파일 오류가 발생
        - (근거 2) 또다른 의견은 enum은 싱글톤이기 때문에 ==와 equals 둘다 된다는 의견도 봄

    ```java
    // if
    Members n = Members.BOBBY;
    if(n.equals(Members.JERRY)){
    	System.out.println("Jerry!");
    }
    if(n == Members.BOBBY){
    	System.out.println("BOBBY!");
    }

    // switch
    Members ifName = Members.BOBBY;
    switch(ifName){
    	case JERRY:
    		System.out.println("Jerry!");
    		break;
    	case BOBBY:
    		System.out.println("BOBBY!");
    		break;
    }
    ```
    
#### (3) Enum을 사용하며 생기는 장점
1. 코드가 단순해지면서 가독성이 좋아진다.
    - 클래스에 static final으로만 이용하여 상수를 정의하게 된다면, 상수가 많아질수록 관리하게 되는 코드가 복잡해지면서 가독성 ㅈㅈㅂㄱㅅㄴㅇㄹㅎㅋㅌㅊㅍㅠㅂ
2. 인스턴스 생성과 상속을 방지하면서 상수의 안정성이 보장된다.
    - Interface로 구현했을 때와 다르게, enum을 사용하면 서로 다른 상수 그룹에 대한 비교를 컴파일 시점에서 차단할 수 있다
3. Enum 키워드를 사용해서 구현의 의도가 열거임을 나타낼 수 있다.

#### (4) Enum 정의하는 방법
```java
[''/public/protected/private] enum enumName {
	// enum constants
	value_name0("AAA"), 
	value_name1("BBB"), 
	value_name2("CCC"); 

	// instance field
	String valueName;

	// constructor
	private enumName(String valueName){
		this.valueName= valueName;
	}	

	// method
	public String getValueName(){
		return valueName;
	}
};

enum Country {
	KOREA("한국"),
	USA("미국");
	
	String countryName;
	
	private Country(String countryName) {
		this.countryName=countryName;
	}
}
```
- enum은 enum 상수(Enum constant)와 생성자(Constructor), 인스턴스(Instance field), 메소드(Method)로 구성
1. Enum 상수 (Enum constant)
    - Enum 상수 이름은 대문자로 써주는 것이 좋다.
    - 주의점
        - Enum 상수를 생성자, 인스턴스, 메소드 뒤에 정의하게 되면 syntax error가 발생
        - String 형태가 아닌 int형은 사용할 수 없다.
        ```java
        enum Test{
        	TEST1, TEST2, 
        	1; // int형을 이용하여 enum 상수를 정의한다면 error!
        }
        ```

2. 생성자 (Constructor)
    - Enum도 클래스라고 볼 수 있기 때문에 생성자를 가질 수 있다.
    - 만약 enum 상수 뒤에 오는 인수가 없다면, 생성자 선언 없이 default 생성자를 사용해도 무방
    - 각 enum 상수들은 생성자를 호출한다.
    - 생성자는 오직 private으로만 선언할 수 있다.
        - = 인스턴스를 생성할 수 없다, new 연산자를 사용할 수 없다.
        - enum 상수는 클래스가 로드되는 시점에 하나의 인스턴스로 생성되기 때문에 임의로 생성하여 사용할 수 없다.
        - 클래스가 로드되는 시점에 하나의 인스턴스로 생성되어 싱클톤 형태로 어플리케이션 전체에서 사용된다.
3. 인스턴스 필드 (Instance field)
    - enum 상수와 연관된 데이터를 상수 자체에 포함시켜 용이하게 관리하기 위해서 사용한다.
    - 인스턴스 변수를 사용하게 된다면 생성자를 overload하여 정의해야 한다.
4. 메소드 (Method)
- enum 클래스 안의 각 enum constants는 사실상 객체인데 그 객체 안의 인스턴스를 가지고 있는 것이다.
```java
public enum Book{
	// enum constants
	JHTP("Java How to Program", "2012"),
	CHTP("C How to Program", "2007"),
	VBHTP("Visual Basic How to Program", "2011");

	// 인스턴스 필드
	private final String title;
	private final String copyrightYear; 
	/*
		여기서 그럼 궁금한건, enum 안에 int를 넣을수 없으니 그럼 여기 인스턴스도 그냥 String 말고는
		선언하기 어려운거 아닌가?
	*/

	// constructor
	Book(String bookTitle, String year){
	}
	
	// method
	public String getTitle(){
		return title;
	}

	public String getCopyrightYear(){
		return copyrightYear;
	}
}

public enumTest{
	public static void main(String[] args){
		// 내 이해 설명 위한 code
		Book b = Book.JHTP; // JHTP, CHTP 이런게 각각 하나의 객체라고 생각하면 된다.
		b.getTitle();
		b.getCopyrightYear(); // 그래서 각 객체에 인스턴스를 가지고 있는 것이다라고 이해하면 편하다.
		
		// print all books in enum Book
		for(Book book : Book.values()){
			System.out.printf("%-10s%-45s%s\n", book, book,getTitle(), book.getCopyrightYear());
			// 이게 enum constant를 String으로 convert하는 방법같은데....사용법이 뭐지?
		}

		// print first 2 books
		for(Book book : EnumSet.range(Book.JHTP, Book.CHTP)){
			System.out.printf("%-10s%-45s%s\n", book, book,getTitle(), book.getCopyrightYear());
		}		
	}
}
```

### 2. enum이 제공하는 메소드(values()와 valueOf())
- 모든 enum에는 values()와 valueOf()라는 메소드가 내장
- 
#### (1) values()
- 모든 enum마다 컴파일러는 values()라고 하는 static 메소드를 생성한다.
- 우리가 선언한 모든 enum 상수 값을 배열 형태로 리턴
- String 형태로 리턴하는 것이 아니라 인스턴스를 리턴해준다.
```java
public enum Book{
	JHTP("Java How to Program"),
	CHTP("C How to Program"),
	VBHTP("Visual Basic How to Program");

	private final String title;

	Book(String title){
			this.title = title;
	}
	
	public String getTitle(){
		return title;
	}
}

public EnumTest{
	public static void main(String[] args){	
		for(Book book : Book.values()){
			System.out.printf("enum = " + book + ", title = " + book,getTitle());
		}	
	}
}
```

#### (2) valueOf()
- 인자로 받은 String과 일치하는 enum 인스턴스가 존재하면 해당 인스턴스를 리턴
- values()와 마찬가지로 String 형태가 아닌 인스턴스를 리턴
```java
public enum Day{
	MON, TUE, WEB, THU, FRI, SAT, SUN
};

public DayTest{
	public static void main(String[] args){
		Day d = Day.WED;
		Day t = Day.valueOf("WED");
		System.out.println("I say d and t are the same": " + (d == t)); //true
	}
}
```

### 3. java.lang.Enum
- enum을 새로 만드는 것은 사실 새로운 클래스를 만드는 것이다. 간접적으로 java.lang.Enum을 확장하여 클래스를 만드는 것
- enum에는 생성자, 메소드, 변수 그리고 상수별 클래스 본체(constant-specific class body)라는 것도 추가할 수 있다.
```java
public class MiaEnum {

	enum Names{
		JERRY("lead guitar"){ 
			public String sings() {
				return "plaintively";
			}
		},
		BOBBY("rhythm guitar"){
			public String sings() {
				return "hoarsely";
			}
		},
		PHIL("bass");
		
		private String instrument;
		
		// 생성자를 생성하지 않으니 위에 변수를 선언할 때 빨간줄이 그어지네?
		Names(String instrument){
			this.instrument = instrument;
		}
		
		public String getInstrument() {
			return this.instrument;
		}
		
		public String sings() {
			return "occasionally";
		}
	}
	
	public static void main(String[] args) {
		for(Names n : Names.values()) {
			System.out.println(n);
			System.out.print(", instrument: " + n.getInstrument());
			System.out.println(", sings: " + n.sings());
		}
	}
}
```

### 4. EnumSet
- java.util 패키지에 정의
- enum 타입에 사용하기 위한 Set
- 비트 필드를 대신할 수 있는 기술 (비트 필드의 상위 호환)
