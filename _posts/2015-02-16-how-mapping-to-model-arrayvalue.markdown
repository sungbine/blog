---
layout: post
title:  "Spring - 한개가아닌 여러개(multi-row)의 파라미터맵을 모델에 맵핑하기"
categories: Spring
---

<h3>개요</h3>
- HTML에서 넘어오는 여러줄의 값들을 모델에 맵핑하기 위해서 검색하고 프로젝트에 직접 적용하며 알게된 방법을 소개한다. 

<h3>본론</h3>
- 보통 넘어오는 인자값을 모델에 맵핑하기 위해서 메서드를 정의할 때 인자의 참조형을 ModelAttribute 로 선언한다. 
- 그렇다면, 하나의 객체에 맵핑되는게 아닌 배열의 형태 혹은 리스트의 형태로 여러줄을 맵핑시킬때는 어떻게 해야할까?

아래와 같이 같은 형태로 연속된 값이 있다고 하자.
```java
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

그리고 컨트롤러 클래스에서 다음과 같은 메서드를 작성할 것이다.

```java
@RequestMapping(value = "/test/modelMap/")
public String getMultiRowToModel(@ModelAttribute Target target) {
    ...
}
```

여기까지는 한 개의 아이템이 넘어올때 모델객체에 맵핑시키는 방법이다.
하지만 여러 로우가 넘어올때는 하나의 모델 객체를 더 만든다.

```java
public class MultiRowTarget {
    private List<Target> targets;
    
    public List<Target> getTargets() {
        return targets;
    }
    public void setTargets(List<Target> targets) {
        this.targets = targets;
    }
}
```

그리고 HTML에서 넘겨줄때 해당 ROW들의 아이템 이름들을 변경해줘야 한다.
정적인 데이터인 경우엔 별 문제가 안되고, 삽입/삭제 버튼을 통한 가변 데이터를 다룰때 이슈가 있다.
( 사실 정적인 데이터엔 이 테크닉을 쓸 필요도 없다. )

```java
<tr>
    <td><input type="text" name="targets[0].targetId" id="targets[0].targetId"/></td>
    <td><input type="text" name="targets[0].targetName" id="targets[0].targetName"/></td>
</tr>
<tr>
    <td><input type="text" name="targets[1].targetId" id="targets[1].targetId"/></td>
    <td><input type="text" name="targets[1].targetName" id="targets[1].targetName"/></td>
</tr>
```

물론, HTML 페이지에서 삽입/삭제 될 때마다 테이블의 로우를 계산해서 
연결리스트 처럼 index를 먹인다는건 쉬운일이 아니다.

나는 form.submit 을 하거나 Ajax를 통해서 전송할 시 그때 다음과 같은 함수를 작성해서 해결했다.

```javascript
function renameForModelAttribute() {
    $("#form").each( function (index) {
        $(this).find("input[name=targetId]").attr("name", "targets[" + index + "].targetId");
        $(this).find("input[name=targetName]").attr("name", "targets[" + index + "].targetName");
    }
}
```

이 함수를 이용하면 서버로 데이터로 전송할때 이름이 바뀌어서 전달하게 된다.
이 바뀐 양식의 모델을 받기 위해서는 아까 작성한 컨트롤러 메서드로 변경해야 한다.
생각나는대로 쓰고 있어서 두서가 없는점 양해 바란다.

```java
@RequestMapping(value = "/test/modelMap/")
public String getMultiRowToModel(@ModelAttribute MultiRowTarget targets) {
    ...
}
```

이러면 이 메서드는 넘어온 값을 targets[0].targetName 을 MultiRowTarget.targets[0].targetName에 집어넣게 된다. 
따라서 넘어온 모든줄의 데이터는 MultiRowTarget 객체의 targets 안에 들어가 있게된다.

자 모두 끝났다.
생각보다 심플하지 않지만 난 아직 이것보다 심플한 방법을 찾지 못했다.
( 물론, 이것을 작성할 때 꽤 고생했고 잘돌아가는것을 확인했기에 더이상 건드리지 않았다. )

추가적으로 해야할 사항을 다시 되짚어보면
- 해당 모델의 리스트형태를 가진 전용 모델 객체를 만든다. 
-- MultiRowTarget
- 넘어올 데이터의 이름을 리스트의 이름과 인덱스로 맞춰춘다.
-- <input name="targets[0].targetName" />

