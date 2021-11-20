### HTTP 메시지

- HTTP 메시지의 구조
  1. 시작줄(start line) - Response에서는 상태줄(status line) 이라고 부른다
  2. 헤더(headers)
  3. 본문(body)
- Request 메시지
  1. 시작줄: API 요청 내용
     - GET naver.com HTTP/1.1
  2. 헤더
     - Content-type
       1. 없음
       2. HTML form 태그로 요청 시
          - Content-type: application/x-www-form-urlencoded
       3. AJAX 요청
          - Content-type: application/json
  3. 본문
     1. GET 요청시: 없음
     2. POST 요청 시: 사용자가 입력한 폼 데이터
        - name=홍길동&age=20
- Response 메시지
  1. 상태줄: API 요청 결과(상태 코드, 상태 텍스트)
     - HTTP/1.1 404 Not Found
  2. 헤더
     - Content-type
       1. 없음
       2. Response 본문 내용이 HTML인 경우
          - Content-type: text/html
       3. Response 본문 내용이 JSON인 경우
          - Content-type: application/json
     - Location: Redirect 할 페이지 URL
       - Location: http://localhost:8888/hello.html
  3. 본문
     1. HTML
     2. JSON

### Controller와 HTTP Request 메시지

![request](https://user-images.githubusercontent.com/76515226/135455544-528e0473-754c-4767-a010-b1fbac8186bd.png)

### Controller와 Response 메시지

![response](https://user-images.githubusercontent.com/76515226/135455645-38e378e3-9799-4cf4-af6b-ac78a8e50c3f.png)

