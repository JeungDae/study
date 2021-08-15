#NPM
>NPM은 외부 모듈을 사용했을 때 사용했었습니다.   
>NPM을 외부 모듈을 다운로드 받았을때 node_modules 라는 폴더에 다운받았던 모듈이 설치 됩니다.   
>"npm init"이라는 명령어로 프로젝트를 초기화 할 수 있습니다.   
>npm init이라는 명령어를 실행하면 package.json 이라는 파일 이생김

+ package.json
```json
{
  "name": "node-api",
  "version": "1.0.0",
  "main": "index.js",
  "dependencies": {},
  "devDependencies": {},
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "description": ""
}

```
+ Dependencies
>해당 부분에 외부모듈이 설정되는데 모듈을 다운받을 때 --save 명령어를 추가하면 됩니다.   
>npm install express --save   
>npm install morgan --save
>--save 옵션을 하여 json에 자동으로 추가합니다.
```json
{
  "name": "node-api",
  "version": "1.0.0",
  "main": "index.js",
  "dependencies": {
    "express": "4.17.1",
    "morgan": "1.10.0"
  },
  "devDependencies": {},
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "description": ""
}
```

+ npm init을 사용하는 이유
>npm init을 통해서 프로젝트에서 사용한 모듈을 json에 정리할 수 있습니다.   
>pakage.json 이 있고 node_modeules가 없는 상태에서 npm install 을 하게되면 json에 정리되어있는 모듈을 설치합니다.

+ script
>json의 scripts에 명령어를 추가하면 실행함.
```json
{
  "name": "node-api",
  "version": "1.0.0",
  "main": "index.js",
  "dependencies": {
    "express": "4.17.1",
    "morgan": "1.10.0"
  },
  "devDependencies": {},
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1", 
    "start":"node index.js"
  },
  "author": "",
  "license": "ISC",
  "description": ""
}
```
>console 에서 "npm start" 라는 명령어를 입력하여 index.js 를 실행시킬 수 있습니다.
