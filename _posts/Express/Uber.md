---
layout: post
title: "Express, Typescript로 Uber BE&FE 정리"
subtitle: "[Express]"
date: 2020-07-07 13:00
background: 
tag: [Tips, Github io, Notion, Node.js, Express, Typescript]
---
## 2.1, 2.2 Project Setup

- yarn이란

`npm install -g yarn`으로 yarn을 설치한다.

`yarn --version`으로 yarn이 잘 설치되었는지 확인한다.

잘 설치되었다면, `yarn init`으로 프로젝트를 초기화한다.

`yarn add typescript ts-node nodemon --dev` dev폴더에 3가지 프로그램을 설치한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a371aedb-725e-4171-8a97-326c87994f76/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a371aedb-725e-4171-8a97-326c87994f76/Untitled.png)

다음 세 폴더/파일을 생성한다.

- src폴더
- tsconfig.json - 추천 설정 붙여넣기 할 파일, typescript를 설정하기 위한 파일
- tslint.json - typescript linter을 설정하기 위한 파일(linter : best typescript code를 작성하도록 도와줌)

`yarn add tslint-config-prettier --dev`로 linter을 설치한다.

typescript를 최적으로 설정해 준다.

```jsx
//tsconfig.json
{
    "compilerOptions": {
      "baseUrl": ".",
      "module": "commonjs",
      "target": "es5",
      "lib": ["es6", "dom", "esnext.asynciterable"],
      "sourceMap": true,
      "allowJs": true,
      "moduleResolution": "node",
      "rootDir": "src",
      "forceConsistentCasingInFileNames": true,
      "noImplicitReturns": true,
      "noImplicitThis": true,
      "noImplicitAny": false,
      "strictNullChecks": true,
      "suppressImplicitAnyIndexErrors": true,
      "noUnusedLocals": true,
      "esModuleInterop": true,
      "skipLibCheck": true,
      "experimentalDecorators": true,
      "emitDecoratorMetadata": true
    },
    "exclude": [
      "node_modules",
      "build",
      "scripts",
      "acceptance-tests",
      "webpack",
      "jest",
      "src/setupTests.ts"
    ]
  }
```

```jsx
//tslint.json
{
  "extends": ["tslint:recommended", "tslint-config-prettier"],
  "linterOptions": {
    "exclude": ["config/**/*.js", "node_modules/**/*."]
  },
  "rules": {
    "no-console": false,
    "member-access": false,
    "object-literal-sort-keys": false,
    "ordered-imports": true,
    "interface-name": false,
    "strict-null-checks": false
  },
  "rulesDirectory": []
}
```

DefinitelyTyped의 node 패키지를 `yarn add @types/node --dev`로 설치해준다.

DefinitelyTyped??  '**모든 것**'들의 **type**를 설명한다. 패키지처럼 설치해서 사용한다. github소스를 참고해서 필요한 것을 설치해서 사용하면 된다.

**파일 저장할 때마다 자동으로 node 재시작하게 설정하기!**

```jsx
//package.json
"ts-node": "^8.10.2",
    "tslint-config-prettier": "^1.18.0",
    "typescript": "^3.9.3"
  },
"scripts": {
  "dev": "cd src && nodemon --exec ts-node index.ts"
}
}
```

`yarn dev` 명령어를 실행하면, 터미널에서 src파일의  index.ts을 실행시킨다.

```jsx
"scripts": {
  "dev": "cd src && nodemon --exec ts-node index.ts -e ts,graphql"
}
```

확장자를 설정해주면 typescript와 graphql을 watching하게 설정할 수 있다.

## 2.4 GraphQL Yoga and Express part One

graphql-yoga설치 `yarn add graphql-yoga`

GraphQL Yoga?? graphql로 개발환경을 만들어주는 역할.

미들웨어 설치 `yarn add helmet morgan cors`

helmet : 보안을 위한 미들웨어. 요청시마다 미들웨어가 요청을 잠시 멈추고 검사. 위험 없다면 요청 진행.

middle ware?? 앱의 연결이나 요청들을 다루는 방식을 수정하는 역할. 예) api로 무언가 할때마다 미들웨어가 요청을 *가로채서*, 요청을 기록하고, 요청이 다음 단계로 진행되도록 한다.

types설치 `yarn add @types/cors @types/helmet @types/morgan --dev`

src디렉터리 > app.ts 파일을 만든다. app.ts에는 앱의 모든 설정들을 넣어 둔다. 

```jsx
//app.ts
import cors from "cors";
import { GraphQLServer } from "graphql-yoga";
import helmet from "helmet";
import logger from "morgan";

class App {  
  public app: GraphQLServer;
  constructor() {  //app class를 새로 시작할 때마다 호출되는 함수
    this.app = new GraphQLServer({});
    this.middlewares();
  }
  private middlewares = (): void => {
    this.app.express.use(cors()); //graphQL은 express를 포함하고 있다.
    this.app.express.use(logger("dev")); //logger의 type를 dev로 선언
    this.app.express.use(helmet());
  };
}

export default new App().app;
```

constructor은 app class를 새로 시작할 때마다 호출되는 함수이다.  서버와 미들웨어를 정의한다. graphQL은 express를 포함하고있다. graphQLserver인 app의 express를 사용햐여 미들웨어를 정의한다. 

## 2.5 GraphQL Yoga and Express part Two

에러는 발생하지만 작동하는 express 서버와 graphql-yoga 서버 만들기

app.ts를 작성했지만, GraphQLServer에 옵션이 필요해서 오류가 날 것이다. GraphQL에 resolvers와 definitions를 입력해야 한다.

src > index.ts를 만든다. 이곳에 방금 만든 app을 import할 것이다.

```jsx
import app from "./app";
app.start()
```
## 2.1, 2.2 Project Setup

- yarn이란

`npm install -g yarn`으로 yarn을 설치한다.

`yarn --version`으로 yarn이 잘 설치되었는지 확인한다.

잘 설치되었다면, `yarn init`으로 프로젝트를 초기화한다.

`yarn add typescript ts-node nodemon --dev` dev폴더에 3가지 프로그램을 설치한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a371aedb-725e-4171-8a97-326c87994f76/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a371aedb-725e-4171-8a97-326c87994f76/Untitled.png)

다음 세 폴더/파일을 생성한다.

- src폴더
- tsconfig.json - 추천 설정 붙여넣기 할 파일, typescript를 설정하기 위한 파일
- tslint.json - typescript linter을 설정하기 위한 파일(linter : best typescript code를 작성하도록 도와줌)

`yarn add tslint-config-prettier --dev`로 linter을 설치한다.

typescript를 최적으로 설정해 준다.

```jsx
//tsconfig.json
{
    "compilerOptions": {
      "baseUrl": ".",
      "module": "commonjs",
      "target": "es5",
      "lib": ["es6", "dom", "esnext.asynciterable"],
      "sourceMap": true,
      "allowJs": true,
      "moduleResolution": "node",
      "rootDir": "src",
      "forceConsistentCasingInFileNames": true,
      "noImplicitReturns": true,
      "noImplicitThis": true,
      "noImplicitAny": false,
      "strictNullChecks": true,
      "suppressImplicitAnyIndexErrors": true,
      "noUnusedLocals": true,
      "esModuleInterop": true,
      "skipLibCheck": true,
      "experimentalDecorators": true,
      "emitDecoratorMetadata": true
    },
    "exclude": [
      "node_modules",
      "build",
      "scripts",
      "acceptance-tests",
      "webpack",
      "jest",
      "src/setupTests.ts"
    ]
  }
```

```jsx
//tslint.json
{
  "extends": ["tslint:recommended", "tslint-config-prettier"],
  "linterOptions": {
    "exclude": ["config/**/*.js", "node_modules/**/*."]
  },
  "rules": {
    "no-console": false,
    "member-access": false,
    "object-literal-sort-keys": false,
    "ordered-imports": true,
    "interface-name": false,
    "strict-null-checks": false
  },
  "rulesDirectory": []
}
```

DefinitelyTyped의 node 패키지를 `yarn add @types/node --dev`로 설치해준다.

DefinitelyTyped??  '**모든 것**'들의 **type**를 설명한다. 패키지처럼 설치해서 사용한다. github소스를 참고해서 필요한 것을 설치해서 사용하면 된다.

**파일 저장할 때마다 자동으로 node 재시작하게 설정하기!**

```jsx
//package.json
"ts-node": "^8.10.2",
    "tslint-config-prettier": "^1.18.0",
    "typescript": "^3.9.3"
  },
"scripts": {
  "dev": "cd src && nodemon --exec ts-node index.ts"
}
}
```

`yarn dev` 명령어를 실행하면, 터미널에서 src파일의  index.ts을 실행시킨다.

```jsx
"scripts": {
  "dev": "cd src && nodemon --exec ts-node index.ts -e ts,graphql"
}
```

확장자를 설정해주면 typescript와 graphql을 watching하게 설정할 수 있다.

## 2.4 GraphQL Yoga and Express part One

graphql-yoga설치 `yarn add graphql-yoga`

GraphQL Yoga?? graphql로 개발환경을 만들어주는 역할.

미들웨어 설치 `yarn add helmet morgan cors`

helmet : 보안을 위한 미들웨어. 요청시마다 미들웨어가 요청을 잠시 멈추고 검사. 위험 없다면 요청 진행.

middle ware?? 앱의 연결이나 요청들을 다루는 방식을 수정하는 역할. 예) api로 무언가 할때마다 미들웨어가 요청을 *가로채서*, 요청을 기록하고, 요청이 다음 단계로 진행되도록 한다.

types설치 `yarn add @types/cors @types/helmet @types/morgan --dev`

src디렉터리 > app.ts 파일을 만든다. app.ts에는 앱의 모든 설정들을 넣어 둔다. 

```jsx
//app.ts
import cors from "cors";
import { GraphQLServer } from "graphql-yoga";
import helmet from "helmet";
import logger from "morgan";

class App {  
  public app: GraphQLServer;
  constructor() {  //app class를 새로 시작할 때마다 호출되는 함수
    this.app = new GraphQLServer({});
    this.middlewares();
  }
  private middlewares = (): void => {
    this.app.express.use(cors()); //graphQL은 express를 포함하고 있다.
    this.app.express.use(logger("dev")); //logger의 type를 dev로 선언
    this.app.express.use(helmet());
  };
}

export default new App().app;
```

constructor은 app class를 새로 시작할 때마다 호출되는 함수이다.  서버와 미들웨어를 정의한다. graphQL은 express를 포함하고있다. graphQLserver인 app의 express를 사용햐여 미들웨어를 정의한다. 

## 2.5 GraphQL Yoga and Express part Two

에러는 발생하지만 작동하는 express 서버와 graphql-yoga 서버 만들기

app.ts를 작성했지만, GraphQLServer에 옵션이 필요해서 오류가 날 것이다. GraphQL에 resolvers와 definitions를 입력해야 한다.

src > index.ts를 만든다. 이곳에 방금 만든 app을 import할 것이다.

```jsx
import app from "./app";
app.start()
```

app.start에는 Oprtion, callback함수가 필요하다. Option이 없으니까 만들어 준다. 

```jsx
import { Options } from "graphql-yoga";
import app from "./app";

const appOptions : Options = {
}
app.start()
```

appOptions를 만들고, graphql-yoga를 import한다. typescript에서는 import를 알파벳 순으로 해야한다. 그리고 모듈 → 라이브러리 순이다. types에서 보여주는 대로 PORT, cors, ....들을 적어준다.  PORT상수를 먼저 입력한다.

```jsx
import { Options } from "graphql-yoga";
import app from "./app";

const PORT : number | string= process.env.PORT || 4000;

const appOptions : Options = {
	port : PORT,
}
app.start()
```

PORT는 number이나 string타입이다. 입력받은 port 상수에는 환경변수나 4000을 할당한다.  

```jsx
import { Options } from "graphql-yoga";
import app from "./app";

const PORT : number | string= process.env.PORT || 4000;
const PLAYGROUND_ENDPOINT : string = "/playground";
const GRAPHQL_ENDPOINT : string = "/graphql";

const appOptions : Options = {
	port : PORT,
	playground : PLAYGROUND_ENDPOINT,
	endpoint: GRAPHQL_ENDPOINT
}
app.start()
```

playground와 endpoint상수를 선언하고 Option설정해준다.

call back 함수를 작성한다.

```jsx
import { Options } from "graphql-yoga";
import app from "./app";

const PORT : number | string= process.env.PORT || 4000;
const PLAYGROUND_ENDPOINT : string = "/playground";
const GRAPHQL_ENDPOINT : string = "/graphql";

const appOptions : Options = {
	port : PORT,
	playground : PLAYGROUND_ENDPOINT,
	endpoint: GRAPHQL_ENDPOINT
}

const handleAppStart = () => console.log(`Listening on port ${PORT}`);

app.start()
```

간단하게 console에 port를 찍는 핸들러를 만든다.

이렇게까지 하면 서버를 실행 시킬 수 있다. `yarn dev` 명령어를 치면 Error: No schema defined 오류가 뜨긴 하지만 graphql과 함께 동작하는 express서버를 만들었다!!

## 2.6 API and Schema Structue part One

resolver와 type definition을 graphql에 입력하는 방법을 배운다.

graphQL의 API는 express나 django의 API와는 다르다.  url이 없다.!!

폴더를 만들고 resolver을 만드는 방식으로 API를 만든다. 

**src > api > Hello > sayHello > sayHello.graphql  & sayHello.resolvers.ts**

api폴더에는 데이터베이스와 다른 것들을 설정하고 난 후에 user, driver, ride, location등의 폴더를 추가할 예정이다.

sayHello.resolvers.ts에 resolver객체를 만든다.

```jsx
const resolvers = {
    Query: {
        sayHello: () => "Hey hello how are ya"
    }
};

export default resolvers;
```

graphql작업을 위한 폴더를 살펴보자. 모든 폴더들에서 resolver파일을 찾아서 그것들을 하나로 합쳐서 graphql에 입력한다. 즉, 우리가 만들 graphql server에 입력하는 것이다. 

resolver가
app.start에는 Oprtion, callback함수가 필요하다. Option이 없으니까 만들어 준다. 

```jsx
import { Options } from "graphql-yoga";
import app from "./app";

const appOptions : Options = {
}
app.start()
```

appOptions를 만들고, graphql-yoga를 import한다. typescript에서는 import를 알파벳 순으로 해야한다. 그리고 모듈 → 라이브러리 순이다. types에서 보여주는 대로 PORT, cors, ....들을 적어준다.  PORT상수를 먼저 입력한다.

```jsx
import { Options } from "graphql-yoga";
import app from "./app";

const PORT : number | string= process.env.PORT || 4000;

const appOptions : Options = {
	port : PORT,
}
app.start()
```

PORT는 number이나 string타입이다. 입력받은 port 상수에는 환경변수나 4000을 할당한다.  

```jsx
import { Options } from "graphql-yoga";
import app from "./app";

const PORT : number | string= process.env.PORT || 4000;
const PLAYGROUND_ENDPOINT : string = "/playground";
const GRAPHQL_ENDPOINT : string = "/graphql";

const appOptions : Options = {
	port : PORT,
	playground : PLAYGROUND_ENDPOINT,
	endpoint: GRAPHQL_ENDPOINT
}
app.start()
```

playground와 endpoint상수를 선언하고 Option설정해준다.

call back 함수를 작성한다.

```jsx
import { Options } from "graphql-yoga";
import app from "./app";

const PORT : number | string= process.env.PORT || 4000;
const PLAYGROUND_ENDPOINT : string = "/playground";
const GRAPHQL_ENDPOINT : string = "/graphql";

const appOptions : Options = {
	port : PORT,
	playground : PLAYGROUND_ENDPOINT,
	endpoint: GRAPHQL_ENDPOINT
}

const handleAppStart = () => console.log(`Listening on port ${PORT}`);

app.start()
```

간단하게 console에 port를 찍는 핸들러를 만든다.

이렇게까지 하면 서버를 실행 시킬 수 있다. `yarn dev` 명령어를 치면 Error: No schema defined 오류가 뜨긴 하지만 graphql과 함께 동작하는 express서버를 만들었다!!

## 2.6 API and Schema Structue part One

resolver와 type definition을 graphql에 입력하는 방법을 배운다.

graphQL의 API는 express나 django의 API와는 다르다.  url이 없다.!!

폴더를 만들고 resolver을 만드는 방식으로 API를 만든다. 

**src > api > Hello > sayHello > sayHello.graphql  & sayHello.resolvers.ts**

api폴더에는 데이터베이스와 다른 것들을 설정하고 난 후에 user, driver, ride, location등의 폴더를 추가할 예정이다.

sayHello.resolvers.ts에 resolver객체를 만든다.

```jsx
const resolvers = {
    Query: {
        sayHello: () => "Hey hello how are ya"
    }
};

export default resolvers;
```

graphql작업을 위한 폴더를 살펴보자. 모든 폴더들에서 resolver파일을 찾아서 그것들을 하나로 합쳐서 graphql에 입력한다. 즉, 우리가 만들 graphql server에 입력하는 것이다. 

resolver가  