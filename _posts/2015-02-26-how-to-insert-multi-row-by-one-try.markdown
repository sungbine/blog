---
layout: post
title:  "DB - INSERT INTO SELECT"
categories: Spring
---

<h3>개요</h3>
- 운영 업무를 하다가 간혹 생기는 일인것 같으면서 자주 생기는 일인데 업무담당자의 실수 혹은 다른 시스템 외부적인 문제로 데이터를 수작업으로 입력해야 하는 상황이 생긴다. 이때 프로세스를 전단계로 돌려서 업무담당자로 하여금 재작업하게 하던가 이미 시스템에 간단히 데이터를 수정할 수 있는 관리자 메뉴가 있다면 작업소요가 크지 않겠지만, 프로세스를 전 단계로 돌리기 난해한 상황이거나 잘마련된 관리자 시스템이 없는데 시스템을 만들자니 코딩 소요도 분명 조금이지만 있을것이고 운영서버에 배포까지 해야하기 때문에 긴급한 요청이라면 시스템을 수정하는건 어렵다. 그러면 결국 이걸 다 데이터베이스 일일히 INSERT를 해야하나하고 미간에 주름이 잡히기 마련이다. 절대 어려운건 아니지만 귀찮고 아트하지 못하다. 나 역시 그랬으며 개발자는 보통 이런상황을 가장 싫어할거라 믿어 의심치 않는다.
- 최근에 나에게도 이런 상황이 발생하였는데 당시의 상황은 이렇다
1. 몇단계에 걸쳐서 프로세스가 진행되는 시스템인데 각 단계마다 입력해야할 값은 동일하다.
2. 단계는 A단계까지만 진행된 상태이며 요청자는 A단계 데이터를 특정 몇개의 필드를 제외하고는 추후 단계에 동일하게 입력하고 싶어한다. 


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
