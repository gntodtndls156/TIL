#개발일지 

일반적인 방법으로는 <mark style="background: #FFB86CA6;">Spring boot 애플리케이션에 파이썬 스크립트를 통합시키는 것</mark>이다. 파이썬 파일을 작성하고 Spring boot에서 실행시켜서 <mark style="background: #FFF3A3A6;">파이썬 스크립트의 출력</mark>을 읽어올 수 있다.

다음 예제다.

```java title:Springboot
import java.io.IOException;

public class PythonIntegration {
    public static void main(String[] args) throws IOException {
        Process process = Runtime.getRuntime().exec("python script.py");
        // 스트림을 통해 파이썬 스크립트의 출력을 읽을 수 있음
    }
}
```

```python
print("Hello from Python!")
```

## 연습
더 나아가, OpenAI API를 활용해 파이썬 파일을 만든 후 Spring boot에서 불러 올 수 있을까? 의문을 들어 예제 프로젝트를 만들었다.

스트림을 통해 파이썬 스크립트 출력하기 전에 Spring boot의 프로젝트가 어디서 실행되는지 알고 싶어서 `pwd` 명령어를 실행시켜 보았다. 그 결과, `/home/hamseungwoo/IdeaProjects/ExecutePython` 이 출력되는 것을 보았을 때, 해당 프로젝트 최상위 루트에서 실행할 수 있는 것을 알 수 있었다.

여기서 조금 더 변화를 줘서 실제로 파이썬을 실행시켜보자.
```java
Process process = Runtime.getRuntime().exec("python3 script/script.py");  
  
// getInputStream to String  
String print = new String(process.getInputStream().readAllBytes());  
System.out.println(print);
```

```python
print("Hello World!")
```

결과는 Hello World! 이라고 뜬다. 성공적이다.

### OpenAI API 준비

https://platform.openai.com/docs/quickstart?context=python 이 가이드를 통해 PRIVATE_KEY 생성했고. 파이썬 라이브러리 `openai`를 설치한 후 Path 추가해야 한다.
```text title:~/.zrcsh
# OPEN AI KEYS PATH
export OPENAI_API_KEY='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'
```

나의 경우 ChatGPT 이용한지 오래 되어서 Usage에 들어갔을 때, 이미 만료되어 있었다. 처음 가입한 경우 18 달러 가량을 무료로 제공해준다. 어쩔 수 없이 5달러 결제했다.
![[Pasted image 20231116174708.png]]

### 다시 프로젝트
이 예제 프로젝트를 진행함으로써 다른 프로젝트 진행할 때 openAI 적용하여 컨텐츠가 조금 더 윤택있게 하고 싶었다. 만약 openAI가 없었다면 개발자가 구현해야 기능이 매우 동적적인 기능이라면 힘들 수 있겠다 싶었다.

```java title:Springboot

package com.github.gntodtndls;  
  
import org.springframework.boot.SpringApplication;  
import org.springframework.boot.autoconfigure.SpringBootApplication;  
  
import java.io.IOException;  
  
@SpringBootApplication  
public class ExecutePythonApplication {  
  
    public static void main(String[] args) throws IOException, InterruptedException {  
        SpringApplication.run(ExecutePythonApplication.class, args);  
        Process process = Runtime.getRuntime().exec("python3 src/script/script.py");  
        int exitCode = process.waitFor();  
  
        if (exitCode == 0) {  
            String print = new String(process.getInputStream().readAllBytes());  
            System.out.println(print);  
        } else {  
            String error = new String(process.getErrorStream().readAllBytes());  
            System.out.println(error);  
        }  
        // getInputStream to String  
    }  
}
```

```python title:python
from openai import OpenAI  
  
client = OpenAI(api_key='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')  
  
completion = client.chat.completions.create(  
    model="gpt-3.5-turbo",  
    messages=[  
        {"role": "user", "content": "Hello!"}  
    ]  
)  
  
print(completion.choices[0].message)
```

```text title:result
ChatCompletionMessage(content='Hello! How can I assist you today?', role='assistant', function_call=None, tool_calls=None)
```

