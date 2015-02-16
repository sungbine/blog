---
layout: post
title:  "리다이렉트(redirect)시 값(parameter) 전달"
categories: Spring
---

리다이렉트 할 때 파라미터 전달이 필요한 경우가 생긴다. <br>
당연히 있을거라고 생각하고 찾아보았는데 역시나 있었다. <br>
<br>
다음은 RedirectAttributes를 이용한 리다이렉트시 값전달 예제이다. 

```java
@RequestMapping(“/test/redirectFrom") 
    public String testFrom(RedirectAttributes redirectAttributes) {
        redirectAttributes.addAttribute(“testParameter", testParameter);
    return "redirect:/test/redirectTo";
}
```
```java
@RequestMapping(“/test/redirectTo")
public String testTo(@RequestParam(“testParameter”) testParameter) {
        System.out.println(testParameter);
    return "/test/testPage";
}
```

