#개발일지 #SpringBoot #Java 

## <mark style="background: #ABF7F7A6;">Background</mark>

스프링부트 프로젝트 진행하기 전, OpenAI API와 연동하기 위해 불가피하게 파이썬 사용하게 되었습니다. 자바에서 정보를 가공할 바에 파이썬 내부함수를 통해 빠르게 가공하고 스프링부트 프로젝트에 전달하는 것이 **작업 효율에 좋기** 때문입니다.

일반적인 방법으로는 스프링부트 애플리케이션에 파이썬 스크립트를 통합시키는 것입니다.
쉽게 말해, 스프링부트 애플리케이션에서 직접 파이썬 파일을 **실행하고 출력 결과를 도출하는 것**입니다.

다음 예제입니다.

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

자바의 `Process.class`를 통해 자식 프로세스를 생성하고 `exec`함수를 이용해 명령어 실행할 수 있습니다. 파이썬이 만약 `Hello from Python!`를 출력한다면 `Process`는 파이썬이 출력한 부문을 담게 되는 것입니다.

예제와 OpenAI API가 준비되었고 이를 실천하는 시간을 갖었습니다.

---
## 연습

예제 적용하기 전, 스프링부트 프로젝트를 빌딩한 후 **어디에서 실행**되는지 알아야 파이썬을 실행할 수 있을 것 같아 먼저 `pwd` 명령어를 실행해보았습니다. 
그 결과, 실행 경로는 `/home/hamseungwoo/IdeaProjects/ExecutePython` 입니다. 해당 프로젝트의 취상위 루트에서 파이썬 파일을 실행할 수 있다는 것을 파악할 수 있었습니다.

여기서 조금 더 변화를 줘서 실제로 파이썬을 실행시켜보았습니다.
```java
Process process = Runtime.getRuntime().exec("python3 script/script.py");  
  
// getInputStream to String  
String print = new String(process.getInputStream().readAllBytes());  
System.out.println(print);
```

```python
print("Hello World!")
```

결과는 Hello World! 이라고 뜹니다.

---
## OpenAI API 준비

https://platform.openai.com/docs/quickstart?context=python 이 가이드를 통해 PRIVATE_KEY 생성했고. 파이썬 라이브러리 `openai`를 설치한 후 Path 추가해야 합니다.
```text title:~/.zrcsh
# OPEN AI KEYS PATH
export OPENAI_API_KEY='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'
```

저의 경우 ChatGPT 이용한지 오래 되어서 Usage에 들어갔을 때, 이미 만료되어 있었습니다. 만약 ChatGPT에 처음 가입한 경우 18달러 가량을 무료로 제공해줍니다.

학습과 연습을 위해 5달러 결제했습니다.
![[Pasted image 20231116174708.png]]

---
## 다시 프로젝트

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

---
## 결론

스프링부트 프로젝트에서 자바의 HTTP와 관련한 라이브러리를 통해 외부 API를 호출 할 수 있으나, 파이썬의 특성상 **내부 함수**와 `openai` **라이브러리가 있어서 직관성 및 간결성**이 자바보다 높습니다. 또한 파이썬과 자바의 영역을 분리함으로써 **확실하게 용도를 나뉘게 되어 편리했습니다.**

다만 해당 컴퓨터에 파이썬 설치되어있지 않거나, 버전이 맞지 않는다면 실행되지 않을 수 있다는 점을 고려하여, **안정성 보완**을 위해 스프링부트 프로젝트에서 파이썬 스크립트 통합하지 않고 **자체에서 실행하는 것이 좋은 편**이라 생각합니다.

이 예제 프로젝트를 진행함으로써 자바의 `Process`에 대해 알게 되었고 파이썬과 자바의 통합이 마냥 신기했습니다. 이를 활용해 **복잡한 처리를 파이썬으로 간편하게 처리**하게 하고 결과를 스프링부트로 전달한다면 좋을 것 같습니다.