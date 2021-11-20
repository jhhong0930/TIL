### Forward vs Redirect

- Forward 방식은 Web Container 차원에서 페이지의 이동만 존재한다

  - 웹 브라우저는 다른 페이지로 이동했음을 알 수 없다, 때문에 웹 브라우저에는 최초에 호출한 URL이 표시되고 이동한 페이지의 URL 정보는 확인할 수 없다

  - 현재 실행중인 페이지와 forward에 의해 호출되는 페이지는 Request 객체와 Response 객체를 공유한다

- Redirect 방식은 Web Container로 명령이 들어오면 웹 브라우저에게 다른 페이지로 이동하라고 명령을 내린다

  - 웹 브라우저는 URL을 지시된 주소로 바꾸고 해당 주소로 이동한다
  - 다른 웹 컨테이너에 있는 주소로 이동하여 새로운 페이지에서는 Request와 Response 객체가 새롭게 생성된다

### addAttribute vs addFlashAttribute

- 기본적으로 Redirect는 Post / Redirect / Get 방식이기 때문에 Get으로 데이터가 넘아가게 된다
- addAttribute로 값을 전달하면 url뒤로 붙게되면 새로고침을 해도 데이터가 유지된다
- addFlashAttribute로 전달한 값은 url뒤에 붙지 않으며 일회성이라 새로고침할 경우 데이터가 소멸한다, 또한 2개이상 쓸 경우 데이터는 소멸한다

