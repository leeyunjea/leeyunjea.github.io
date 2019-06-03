---
layout: post
title: "@Autowire"
comments: true
date: 2019-05-23
tags: [spring]
categories:
  - SPRING
description:
image: /image/spring/spring-core-tutorial.png
image-sm: /image/spring/spring-framework.png

---
<!-- {:.post-img.small}
![producer](/image/rabbitmq/producer.png) -->




### @Autowire
<br />

- 필요한 의존 객체의 "타입"에 해당하는 빈을 찾아 주입한다.
- 옵션
    - required: 기본 값은 true(못찾으면 애플리케이션 구동 실패)

<br>

#### 사용할 수 있는 위치
<br>
1. 생성자
{% highlight java %}
@Service
public class BookService  { 

   BookRepository bookRepository;

   @Autowired
   public BookService(BookRepository bookRepository) {
      this.bookRepository = bookRepository;
   }
}
{% endhighlight %}


2. 세터
{% highlight java %}
@Service
public class BookService  { 

   BookRepository bookRepository;

   @Autowired(required = false)
   public void setBookRepository(BookRepository bookRepository) {
      this.bookRepository = bookRepository;
   }
}
{% endhighlight %}
3. 필드
{% highlight java %}
@Service
public class BookService  { 

   @Autowired
   BookRepository bookRepository;

}
{% endhighlight %}
<br>

#### 같은 타입의 빈이 여러개 일 때
<br>
1. @Primary
{% highlight java %}
   @Repository @Primary 
   public class KeesunBookRepository implements BookRepository {
   }
{% endhighlight %}
2. 해당 타입의 빈 모두 주입 받기
3. @Qualifier (빈 이름으로 주입)
{% highlight java %}
   @Service
   public class BookService  { 

       @Autowired @Qualifier("keesunBookRepository")
       BookRepository bookRepository;

   }
{% endhighlight %}

<br>

