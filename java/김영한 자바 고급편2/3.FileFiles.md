# File, Files

자바에서 파일 또는 디렉토리를 다룰 때는 `File`, `Files`, `Path` 클래스를 사용하면 된다.

이 클래스들을 사용하면 파일이나 폴더를 생성하고 삭제하고 또 정보를 확인할 수 있다.

이런 클래스들은 외우기보다는 간단히 알아두고 필요할 때 찾아서 사용하면 된다.

`File`은 좀 오래된 방식이고 `Files` 방식을 사용하자.

- 자바 1.7에서 Files, Path 클래스가 등장했다.

## Files

### **Files의 특징**

- 성능과 편의 모두 개선
- 다양한 유틸리티 클래스 제공
- 기능이 매우 다양함

```java
// 파일이나 디렉토리의 경로는 Path 클래스를 사용해야한다.
Path file = Path.of(FILE_PATH);
Path dir = Path.of(DIR_PATH)

Files.exists(file); // 파일 존재하는지 체크
Files.createFile(file); // 파일 생성
Files.createDirectory(dir) // 디렉토리 생성
Files.delete(file); // 파일 삭제

등등 다양한 기능이 있다.
```

## 경로 표시

파일이나 디렉토리가 있는 경로는 크게 절대 경로와 정규 경로로 나눌 수 있다.

**절대 경로 : 경로의 처음부터 내가 입력한 모든 경로를 다 표현 (../, ./ 같은 것들을 모두 그대로 표현)**

**정규 경로 : 경로의 계산이 모두 끝난 경로이다. (../ , ./ 와 같은 것들을 다 계산 후 표현하는 경로)**

### Files 경로 표시

```java
Path path = Path.of("temp/..");

// 절대경로
path.toAbsolutePath(); // /Users/kjh/java/java-adv2/temp/..
// 정규 경로
path.toRealPath(); // /Users/kjh/java/java-adv2
```

## Files로 문자 파일 읽기

문자로된 파일을 읽고 쓸 때 과거(자바 1.7 이전)에는 FileReader, Writer와 같은 복잡한 스트림 클래스를 사용해야했다.

거기에 모든 문자를 읽으려면 반복문을 사용해서 파일의 끝까지 읽어야하는 과정을 추가해야한다.

또 한 줄 단위로 파일을 읽으려면 BufferedReader 같은 스트림 클래스를 추가해야 했다.

Files는 이런 문제를 깔끔하게 해결해준다.

```java
String writeString. = "abc\n가나다";

Path path = Path.of(PATH);

// 파일 쓰기
Files.writeString(path, writeString, UTF_8); 

// 파일 전체 읽기
Files.readString(path, UTF_8);

// 파일 한줄씩 읽기
List<String> lines = Files.readAllLines(path, UTF_8); // 한줄씩 리스트에 반환

// 위 방식은 전체를 가져오기때문에 메모리적으로 문제가 있을 수 있다.
// 스트림 방식으로 한줄씩 읽을때마다 파일에서 읽어옴 -> 메모리 효율적임
Stream<String> lineStream = Files.lines(path, UTF_8); 
```

Files를 사용하면 아주 쉽게 파일에 문자를 쓰고 읽을 수 있다.

## 파일 복사 최적화

```java
Path source = Path.of("temp/copy.dat");
Path target = Path.of("temp/copy_new.dat");

// 알아서 최적화된 카피를 해준다.
Files.copy(soruce, target, StandartCopyOption.REPLACE_EXISTING);
```

### 결론

- 파일을 다루어야할 일이 있다면 항상 `Files`의 기능을 먼저 찾아보자.