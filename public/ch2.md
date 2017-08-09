---
layout: post
title: Node ch.2
excerpt: "POST 요청 처리"
categories: [hello world]
comments: true
image:
  feature:
  credit: thomas shellberg
  creditlink: https://unsplash.com/photos/Ki0dpxd3LGc
---

# Node ch.2

### POST 요청 처리
  /pubilc/form.html 생성

  ```
  <!DOCTYPE html>
  <html>
    <head>
      <meta charset="utf-8">
      <title>email form</title>
    </head>
    <body>
      <form action="/email_post" method="post">
        email : <input type="text" name="email"> <br/>
        submit <input type="submit">
      </form>


    </body>
  </html>

  ```


1. 구동확인

    localhost:3000/form.html

2. email_post 라우팅 처리 ::app.js 수정

    ```
    app.post('/email_post', function(req,res){
    	res.send("post response")
    })
    ```

3. post방식으로 값을 받아오려면 bady-parser 모듈을 설치해줘야함

    `npm install body-parser --save`

4. app.js 에 모듈 등록

    ```
    var bodyParser = require('body-parser')
    ```

5. post 로 값을 받아오는 형식 지정 :: app.js

    ```
    app.use(bodyParser.json())
    app.use(bodyParser.urlencoded({extended:true}))
    ```

6. 받아온 값 사용 (여기선 email) :: app.js

    ```
    app.post('/email_post', function(req,res){
    	***console.log(req.body.email)***
    	res.send("post response")
    })
    ```

7. 받아온 값을 화면에 보여주기 :: app.js

    ```
    app.post('/email_post', function(req,res){
    	console.log(req.body.email)
    	res.send("<h1>Welcome!" + req.body.email + "</h1>")
    ```


### View engine을 이용한 응답처리
##### ejs나 jade와 같은 템플릿 엔진을 활용해 view 템플릿을 활용해 응답 할 수 있다

1. ejs 모듈 설치

    `npm install ejs --save`

2. 모듈 사용해서 나타내기 :: app.js

    ```
    app.set('view engin', 'ejs')
    ```
3. /views 경로 생성, /views/email.ejs 파일 생성

    ```
    <!DOCTYPE html>
    <html>
      <head>
        <meta charset="utf-8">
        <title>email ejs template</title>
      </head>
      <body>
        <h1>Welcom !! <%= email %> </h1>
        <p>Nice to meet you</p>

      </body>
    </html>

    ```

4. email.ejs를 사용하려면 :: app.js

    ```
    app.post('/email_post', function(req,res){
    	console.log(req.body.email)
    	//res.send("<h1>Welcome!" + req.body.email + "</h1>")
    	res.render('email.ejs', {'email' : req.body.email})
    })
    ```


### JSON 활용한 Ajax처리
* Ajax를 이용해 새로고침 없이 데이터 처리하기 :: form.html

  ```
  <!DOCTYPE html>
  <html>
    <head>
      <meta charset="utf-8">
      <title>email form</title>
    </head>
    <body>
      <form action="/email_post" method="post">
        email : <input type="text" name="email"> <br/>
        <input type="submit">
      </form>

      <button class="ajaxsend">ajaxsend</button>

      <script>
        document.querySelector('.ajaxsend').addEventListener('click', function() {
          var inputdata = document.forms[0].elements[0].value;
          sendAjax('http://127.0.0.1:3000/ajax_send_email',inputdata);
        })

        function sendAjax(url, data){
          var data = {'email': data};
          data = JSON.stringify(data);
          var xhr = new XMLHttpRequest();
          xhr.open('POST', url);
          xhr.setRequestHeader('Content-Type', "application/json"); //서버로 json형태로 보낸다는걸 나타내주기위해
          xhr.send(data);
          xhr.addEventListener('load', function(){
            console.log(xhr.responseText);
          });
        }
      </script>

    </body>
  </html>
  ```

* app.js에 ajax를 이용한 post처리를 추가 해준다
  ```
  app.post('/ajax_send_email', function(req, res){
  	console.log(req.body.email);
  	var responseData = {'result' : 'ok', 'email': req.body.email};
  	res.json(responseData)
  });
  ```

* 이때, 데이터가 제대로 처리되지 않고
  XMLHttpRequest cannot load http://127.0.0.1:3000/ajax_send_email. Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://localhost:3000' is therefore not allowed access.
  이런 오류가 난다면.....




* 입력한 데이터 화면에 다시 보여주기 :: form.html
  ```
  <!DOCTYPE html>
  <html>
    <head>
      <meta charset="utf-8">
      <title>email form</title>
    </head>
    <body>
      <form action="/email_post" method="post">
        email : <input type="text" name="email"> <br/>
        <input type="submit">
      </form>

      <button class="ajaxsend">ajaxsend</button>
      <div class="result"></div>
      <script>
        document.querySelector('.ajaxsend').addEventListener('click',
        function() {
          var inputdata = document.forms[0].elements[0].value;
          sendAjax('http://127.0.0.1:3000/ajax_send_email',inputdata);
        })

        function sendAjax(url, data){
          var data = {'email': data};
          data = JSON.stringify(data);
          var xhr = new XMLHttpRequest();
          xhr.open('POST', url);
          xhr.setRequestHeader('Content-Type', "application/json"); //서버로 json형태로 보낸다는걸 나타내주기위해
          xhr.send(data);
          xhr.addEventListener('load', function(){
            var result = JSON.parse(xhr.responseText);
            if(result.result !== "ok") return;
            document.querySelector(".result").innerHTML = result.email;
          });
        }
      </script>

    </body>
  </html>

  ```
