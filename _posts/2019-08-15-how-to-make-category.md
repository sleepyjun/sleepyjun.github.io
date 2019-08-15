---
layout: post
title:  "우여곡절 카테고리 만들기"
author: "sleepyjun"
categories: [web]
---
## 1. 서론
필자는 [Jekyll](https://jekyllrb-ko.github.io/)의 [Tale](https://github.com/chesterhow/tale/)테마를 이용해서 Github Page를 운영하고 있다.

Tale테마가 제일 예쁘다고 생각되서 바로 포크해서 가져왔다.  
![Tale Layout](./assets/images/2019-08-15-how-to-make-category/tale.jpg)

페이지네이션도 있고 무엇보다 깔끔해서 마음에 들었는데 한가지 의문점이 들었다.  
> 도대체 Tale과 Posts는 왜 같은 곳을 가리키는거지??

그래서 Posts를 카테고리로 바꾸기로 했다!

카테고리는 이 글 작성이전에도 있었으나 최근에야 버그를 발견했다..  

버그를 완전히 고쳤으니 이제야 말로 포스팅할 때다 싶어 포스팅한다.

## 2. 카테고리 생성
기본적으로 Tale엔 카테고리가 없기에 만들어야한다.  

출처: [지킬(Jekyll) 블로그 카테고리(category) 만들기](https://devyurim.github.io/development%20environment/github%20blog/2018/08/07/blog-6.html)  

물론 필자는 루비에 전혀 지식이 없기에 검색엔진과 이 블로그를 상당히 많이 참조했다.  

일단 카테고리 폴더를 생성해준다.  
그 후 카테고리의 이름.md 파일을 생성한 후 파일안에 이런식으로 작성한다  
![category](./assets/images/2019-08-15-how-to-make-category/category.jpg) ![categorymd](./assets/images/2019-08-15-how-to-make-category/categorymd.jpg)

## 3. Tale nav의 구조 및 수정
Tale은 크게 나눴을때 nav, main, footer로 구성되어있다.  
우리가 볼 것은 nav안에 존재하는 Posts라는 a태그다.  
```
<nav class="nav">
  <div class="nav-container">
    <a href="{{ site.baseurl }}/">
      <h2 class="nav-title">{{ site.title }}</h2>
    </a>
    <ul>
      <li><a href="{{ '/about' | prepend: site.baseurl }}">About</a></li>
      <li><a href="{{ site.baseurl }}/">Posts</a></li>
    </ul>
  </div>
</nav>
```  
역시 site.baseurl이 중복으로 나타나고 있다.  

```
<li>
		{% assign pages_list = site.pages %} 
		{% for node in pages_list %} 
		{% if node.title != null %} 
		{% if node.layout == "category" %}
		<a class="category-link {% if page.url == node.url %} active{% endif %}" href="{{ site.baseurl }}{{ node.url }}">{{ node.title }}</a>
		{% endif %} {% endif %} {% endfor %}
</li>
```  
이렇게 하면 내가 생성한 카테고리들이 싹 나온다!  
이제 클릭했을시 카테고리에 해당하는 포스트들을 보여주기위해 _layouts폴더에 category.html을 만들고 이와 같이 작성한다.
```
---
layout: default
---
<ul class="posts-list">
  
  {% assign category = page.category | default: page.title %}
  {% for post in site.categories[category] %}
    <li>
      <h3>
        <a href="{{ site.baseurl }}{{ post.url }}">
          {{ post.title }}
        </a>
        <small>{{ post.date | date: "%Y. %m. %d.(%a)" }}</small>
      </h3>
    </li>
  {% endfor %}
  
</ul>
```  
나머지는 jekyll이 해줄 것이다.

## 4. 드롭다운 생성
목록만 무미건조하게 쭉 나오니 상당히 예쁘지 않았다.  
따라서 간단한 드롭다운을 작성하기로 했다.  
```
<li class="dropdown">
	<input id="chk-dropdown" type="checkbox" name="menu">
	<label for="chk-dropdown">Category</label>
	<div class="dropdown-content">
		{% assign pages_list = site.pages %} 
		{% for node in pages_list %} 
		{% if node.title != null %} 
		{% if node.layout == "category" %}
		<a class="category-link {% if page.url == node.url %} active{% endif %}" href="{{ site.baseurl }}{{ node.url }}">{{ node.title }}</a>
		{% endif %} {% endif %} {% endfor %}
	</div>
</li>
```  
필자는 클릭시 드롭다운이 펼쳐지고 다시 클릭시 접히는 형식을 택했다.  
이런 편이 모바일 기기에 편했다.  
>hover와 click둘다 해봤지만 click쪽이 아무래도 편했다

문제는 이 li태그를 감싸는 .nav가 overflow: auto;이기에 드롭다운 메뉴를 펼치면 .nav안에 스크롤이 생기는 것이다.  
![scroll](./assets/images/2019-08-15-how-to-make-category/scroll.jpg)  

처음 프로토타입은 div에 position:fixed를 주어 해결했다.
>고 생각했다.  
![error](./assets/images/2019-08-15-how-to-make-category/fixed.jpg)  
보다시피 카테고리 스스로 스크롤을 따라서 움직였다. position: fixed탓이었다..  

문제점을 파악하고 overflow대신 가상선택자 ::after을 이용해서 레이아웃을 개선했다.
```
.nav::after {
    content: '';
    display: table;
    clear: both;
}
```

## 5. 끝맺음
필자는 Jekyll, Ruby on rails, CSS에 대한 지식이 턱없이 부족했기에 그냥 "내가 이렇게헀어요" 수준의 포스팅을 올렸다.  

만약 이 포스팅을 본다면 단지 참고만 해줬으면 좋겠다..