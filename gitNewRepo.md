# git 저장소에 내 프로젝트 올리기

1. github page 에서 new repository 생성

    (repository name 을 프로젝트 이름과 동일하게 해주는게 알기 쉬움..)

    ex) test1


2. 내 프로젝트 디렉토리(test1)에서 터미널을 실행


3. 아래 명령어 입력

    `test1 $ git init `

    `test1 $ git add . `

    `test1 $ git commit -m "first commit" `

    새로 생성한 git repository의 https 를 clone 후

    `test1 $ git remote add origin [복사한 주소 붙여넣기] `

    `test1 $ git push -u origin master`


    하면 끝!

