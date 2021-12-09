---
layout: post
title: React Overview
tags: [React]
---

#### Concepts
- JSX : HTML과 비슷하면서도 약간의 차이가 존재하는 자바스크립트로서 (객체를 의미하는 거겠지?) HTML처럼 앱의 구조를 만들 때 사용
- Component : 재사용이 가능하며 독립적인 성격을 가진 UI에 대한 논리적 구분
  - 한 번 선언하고 나면 HTML 처럼 사용 가능
  - Attribute 이름은 HTML과 다르게 CamelCase
  - Return 값은 JSX 혹은 또 다른 Component
- Props : Component의 속성으로 데이터를 Component에게 전달할 수 있음
  - String을 전달하고 싶은 경우에는 "", 변수를 전달하고 싶으면 {}
  - key : React가 사용하는 식별자로 UI 업데이트시에 사용
  - map() : 자바스크립트의 map과 다르게 JSX를 리턴함
- State : 데이터의 변화에 따라 UI 업데이트가 일어나게 만들어줌
  - useState() : state 변수로 만들 데이터와 state 변수의 값을 수정할 수 있는 함수를 리턴함
  - state 업데이트 -> re-render
- Event
  - 이벤트 처리 함수는 handle이라는 prefix로 시작하는게 convention
- Re-render
  - 부모 콤포넌츠의 re-render는 자식 콤포넌츠도 영향을 끼침
- Ref : Dom elements를 참조할 수 있도록 해주는 React의 기능
  - JSX로 생성한 태그에 ref 객체를 넘기면 콤포넌츠 내에서 ref 객체를 통해 해당 태그에 접근 가능

### References
- https://www.freecodecamp.org/news/react-tutorial-build-a-project/
