---
layout: post
title:  "DB - INSERT INTO SELECT"
categories: Spring
---

<h3>개요</h3>
- 운영 업무를 하다가 간혹 생기는 일인것 같으면서 자주 생기는 일인데 업무담당자의 실수 혹은 다른 문제로 데이터가 잘못 입력되어졌고 

<h3>본론</h3>
- HTML로 부터 넘어오는 파아미터값을 모델에 맵핑하기 위해서는 보통 메서드를 정의할 때 인자의 참조형 앞에 @ModelAttribute 어노테이션을 선언한다. 
- 그렇다면, 하나의 객체에 맵핑되는게 아닌 배열의 형태 혹은 리스트의 형태로 여러줄을 맵핑시킬때는 어떻게 해야할까?

아래와 같이 같은 형태로 연속된 값이 있다고 하자.

```html
<form>
<table>
    <tr>
        <td><input type="text" name="targetId" id="targetId"/></td>
        <td><input type="text" name="targetName" id="targetName"/></td>
    </tr>
    <tr>
        ...
    </tr>
    ...
</table>
</form>
```

아마도 이 값들을 전송하기 위해서 개발자는 [form.submit](http://api.jquery.com/submit/) 을 하거나 ajax를 이용해서 
data에 [form.serialiaze()](http://api.jquery.com/serialize/) 를 담아서 보낼것이다.

이 한 아이템(Row)를 모델에 맵핑하기 위해서는 먼저 넘어오는 데이터의 이름과 같은 멤버변수를 지닌 
객체를 모델링 해줘야한다.

```java
public class Target {
    private String targetId;
    private String targetName;
    
    public String getTargetId() {
        return targetId;
    }
    ...
}
```
