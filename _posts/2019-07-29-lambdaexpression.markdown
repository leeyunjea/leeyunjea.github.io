---
layout: post
title: "JAVA8 - Lambda란?"
comments: true
date: 2019-07-29
tags: [java8, lambda]
categories:
  - java8
description: "Java8 lambd식에 대하여"
# image: /image/spring/spring-core-tutorial.png
# image-sm: /image/spring/spring-framework.png

---


<br>
### 람다식(lambda expression)이란?
<br />

- java 8부터 람다식이 추가되었다.
- 표현 방식중 하나로 함수형 스타일이라고 할 수 있다.
- 함수형 스타일은 함수의 입력만을 의존하며 출력을 만드는 구조로 외부에 상태를 변경하는 것을 <span style="color:red">지양</span>하는 패러다임으로 부작용 발생을 최소화 하는 방법론이다.
- 기존의 불필요한 코드를 줄이고 가독성을 향상시키는데 목적이 있다.

<br>

### 람다식의 장점
1. 코드를 간결하게 구성할 수 있다.
    - 함수형 버전은 간결하다.
    - 개발자의 의도를 효과적으로 잘 전달한다.
2. 오류를 줄일 수 있다.
    - 자바7에서는 변수를 명시적으로 변경하는 경우가 많지만 함수형 코드에서는 명시적인 변경이 없다.
3. 원하는 부분을 병렬화 하기 쉽다.
    - 병렬화를 구현하기는 매우 어렵다. 태스크를 다중 스레드로 나누고, 결과를 모은 뒤 순차적으로 옮겨야 한다. 또한 다른 스레드가 데이터를 업데이트 하는 동안에 스레드간의 충돌이 안되게 해야한다. 람다식을 이용하면 간단하게 메소드를 변경하는 것만으로도 구현할 수 있다.

<br />
## 예제

#### names라는 리스트에서 이름이 's'로 시작하는 사람의 수 구하기
#### 기존 JAVA코드

{% highlight java %}

    final List<String> names = Arrays.asList("yunjae", "yeji", "minsu", 
				"sungmoon", "jinho", "haneul");
		
    int count = 0;
    for(String name : names) {
    	if(name.startsWith("s")) {
    		count += 1;
    	}
    }
		
    System.out.println("이름이 s로 시작하는 사람 수 " + count);
		
		
}

{% endhighlight %}


#### 출력 결과

 {:.post-img.small}
![producer](/image/lambda/lambda1.png)

<br>

#### 람다식(함수형)

{% highlight java %}

    final List<String> names = Arrays.asList("yunjae", "yeji", "minsu", 
				"sungmoon", "jinho", "haneul");
		
    System.out.println("이름이 s로 시작하는 사람 수 " + names.stream()
		.filter(name -> name.startsWith("s")).count());

{% endhighlight %}


#### 출력 결과
{:.post-img.small}
![producer](/image/lambda/lambda2.png)


#### * 명령형, 함수형 스타일을 비교하면 소스가 훨씬 간결하다는 것을 볼 수 있다.


<br /><br />


