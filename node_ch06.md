# node ch 06


## MySQL 구현

    클라이언트로부터 입력값을 받아 그 값을 DB를 통해 찾고, 결과를 보여주는 페이지를 구현해보자


1. jsman database 에 user 테이블 생성

    uid, email, name, password 의 4개의 컬럼을 가진 테이블을 생성 한 후

    test용 데이터를 입력해준다

    ` INSERT into user (uid, email, name, password) values (1, "abc@naver.com", "CSA", 1111) `






2. form.html 수정

    ajaxsend < 이 버튼이 DB 사용하는 버튼

    ```

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
            var data = {'email' : data};
            data = JSON.stringify(data);
            var xhr = new XMLHttpRequest();
            xhr.open('POST', url);
            xhr.setRequestHeader('Content-Type', "application/json"); //서버로 json형태로 보낸다는걸 나타내주기위해
            xhr.send(data);

            xhr.addEventListener('load', function(){
                //console.log(xhr, responseText);
                var result = JSON.parse(xhr.responseText);
                var resultDiv = document.querySelector(".result");
                if(result.result !== "ok") resultDiv.innerHTML = "your email is not found"
                else resultDiv.innerHTML = result.name;
            });
          }
        </script>

      </body>
    </html>


    ```


3. app.js 수정

    ```

    app.post('/ajax_send_email', function(req, res){
        var email = req.body.email;
        var responseData = {};

        var query = connection.query('select name from user where email="' + email + '"', function (err, rows) {
            if (err) throw err;
            if (rows[0]) {
                console.log(rows[0])
                responseData.result = "ok";
                responseData.name = rows[0].name;
            } else {
                responseData.result = "none";
                responseData.name = "";
            }
            res.json(responseData)
        })
    })

    ```



3. 결과 확인하기

    * DB에 없는 새로운 email주소를 입력하면, Your email is not found 라는 문구가 출력


    * DB에 들어있는 abc@naver.com 을 입력하면, CSA(name) 가 출력됨
