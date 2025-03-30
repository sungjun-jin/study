# PropertyEditor & Converter & Formatter

## PropertyEditor
대부분 문자열로 되어있는 사용자의 입력값을 자바 객체로 변환하는 기능을 담당한다.

### PropertyEditor 사용
```java
public class Book {
    private int bookId;

    public Book(int bookId) {
        bookId = bookId;
    }

    public int getBookId() {
        return bookId;
    }

    @Override
    public String toString() {
        return "Book{" +
                "bookId=" + bookId +
                '}';
    }
}
```

Book 클래스를 예시로 PropertyEditor를 사용해 bookId로 건네받은 문자열 입력값을 Book 객체로 변환시켜보자.

```java
public class BookEditor extends PropertyEditorSupport {

    @Override
    public String getAsText() {
        Book book = (Book) getValue();
        return String.valueOf(book.getBookId());
    }

    @Override
    public void setAsText(String text) throws IllegalArgumentException {
        setValue(new Book(Integer.parseInt(text)));
    }
}
```

PropertyEditorSupport 클래스를 상속해 getAsText, setAsText를 구현해준다. setAsText 메소드가 매개변수로 넘어온 입력값(text)를 Book 객체로 변환하는 작업을 수행한다.

```java
@Controller
public class BookController {

    @InitBinder
    public void init(WebDataBinder webDataBinder) {
        webDataBinder.registerCustomEditor(Book.class, new BookEditor());
    }
    @GetMapping("/book/{book}")
    public void getBook(@PathVariable Book book) {
        System.out.println(book);
    }
}
```

구현한 Editor 클래스를 @InitBinder를 적용한 메소드에서 WebDataBinder의 registerCustomEditor를 호출해 등록해준다.

이제 클라이언트에서 `/book/2` 과 같은 GET 요청이 오면 PropertyEditor가 자동적으로 bookId가 2인 Book 객체로 변환 해준다.

`/book/2 [GET]` 호출 결과
```
Book{bookId=2}
```

### PropertyEditor의 치명적인 단점
```java
public class PropertyEditorSupport implements PropertyEditor {
    // 나머지 내부 구현 생략
    public void setValue(Object value) {
        this.value = value;
        firePropertyChange();
    }

    private Object value;
}
```
PropertyEditorSupport 클래스는 내부적으로 변환된 객체에 대한 정보를 value라는 멤버 변수로 가지고 있는다. 해당 구현체는 쓰레드 동기화 처리가 되어 있지 않으므로 다른 쓰레드가 충분히 setValue 메소드를 호출해 값을 수정할 수 있다. 

## Converter
S 타입을 T 타입으로 변환할 수 있는 매우 일반적인 변환기이다.

### Converter 사용
```java
public class BookConverter {

    public static class StringToBookConverter implements Converter<String, Book> {
        @Override
        public Book convert(String source) {
            return new Book(Integer.parseInt(source));
        }
    }

    public static class BookToStringConverter implements Converter<Book, String> {
        @Override
        public String convert(Book book) {
            return String.valueOf(book.getBookId());
        }
    }
}
```

Converter<S, T> 인터페이스를 구현한 정적 내부 클래스로 사용할 수 있다. convert 메소드를 오버라이딩 하여 변환될 타입에 대한 코드를 구현한다.

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addConverter(new BookConverter.BookToStringConverter());
        registry.addConverter(new BookConverter.StringToBookConverter());
    }
}
```

WebMvcConfigurer 인터페이스를 구현한 자바 Configuration 어노테이션을 적용한 클래스를 통해 addFormatter 메소드를 오버라이딩 한다. registry에 앞서 작성해준 converter를 등록하면 끝이다.

## Formatter 
PropertyEditor의 대체제로 사용되며 다국어 처리가 가능한 Locale 처리 기능도 추가되었다.

### Formatter 사용

```java
public class BookFormatter implements Formatter<Book> {
    @Override
    public Book parse(String text, Locale locale) {
        return new Book(Integer.parseInt(text));
    }

    @Override
    public String print(Book object, Locale locale) {
        return String.valueOf(object.getBookId());
    }
}
```

Formatter<T> 인터페이스를 구현하고, parse, print 메소드를 오버라이딩 한다. 매개변수로 넘어오는 Local 클래스와 MessageSource를 같이 사용해서 다국어 처리도 가능하다.

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addFormatter(new BookFormatter());
    }
}
```

Converter와 마찬가지로 WebMvcConfigurer 인터페이스를 구현한 자바 Configuration 어노테이션을 적용한 클래스를 통해 addFormatter 메소드를 오버라이딩 하여 Formatter를 등록해준다.

## Spring Boot 사용
Spring Boot를 사용하면 Converter와 Formatter 인터페이스를 구현한 클래스를 빈으로 등록하면 자동으로 FormatterRegistry에 등록된다. 