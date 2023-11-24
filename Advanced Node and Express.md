# Advanced Node and Express
Now it's time to take a deep dive into Node.js and Express.js by building a chat application with a sign-in system.

To implement the sign-in system safely, you'll need to learn about authentication. This is the act of verifying the identity of a person or process.

In this course, you'll learn how to use Passport to manage authentication, Pug to create reusable templates for quickly building the front end, and web sockets for real-time communication between the clients and server.

## Set up a Template Engine

A template engine enables you to use static template files (such as those written in Pug) in your app. At runtime, the template engine replaces variables in a template file with actual values which can be supplied by your server. Then it transforms the template into a static HTML file that is sent to the client. This approach makes it easier to design an HTML page and allows for displaying variables on the page without needing to make an API call from the client.  

`pug@~3.0.0` has already been installed, and is listed as a dependency in your `package.json` file.

Express needs to know which template engine you are using. Use the set method to assign `pug` as the `view engine` property's value:
```node.js
app.set('view engine', 'pug');
```
After that, add another `set` method that sets the `views` property of your `app` to point to the `./views/pug` directory.(상대 경로) This tells Express to render all views relative to that directory.

Finally, use `res.render()` in the route for your home page, passing `index` as the first argument. This will render the `pug` template.

If all went as planned, your app home page will no longer be blank. Instead, it will display a message indicating you've successfully rendered the Pug template!

***

파이썬으로 만든 어플리케이션(프로그램)을 상상해봐요. 이 어플리케이션은 사용자에게 웹 페이지를 보여주는 역할을 합니다. 그런데 이 웹 페이지를 만들려면 어떤 내용이 들어갈지 미리 정해놓은 '템플릿' 파일이 필요해요. 이 템플릿 파일은 마치 미리 준비된 빈 종이 시트 같아요.

그런데 이 빈 시트를 실제로 사용자에게 보여줄 때, 그 안에 있는 특정 내용을 바꿔치기해야 해요. 예를 들어, "안녕하세요, [사용자 이름]님!"이라고 쓰여진 템플릿이 있으면, [사용자 이름] 부분에 실제 사용자의 이름을 적어줘야 해요. 그런 다음, 이 템플릿을 웹 페이지로 만들어서 사용자에게 보여줄 거에요.

요약하면, 이 엔진(프로그램 일종)은 빈 템플릿을 가져와서 그 안에 정보를 넣어서 실제 웹 페이지로 바꿔주고, 그것을 사용자에게 보여주는 역할을 하는 거에요.

***

템플릿 엔진은 웹 어플리케이션에서 ***정적인 템플릿 파일을 사용하여 동적 컨텐츠를 생성***하는 데 도움을 주는 도구입니다. 이 템플릿 파일은 예를 들어 Pug와 같은 언어로 작성됩니다. 이 파일에는 변수가 포함되어 있는데, 이 변수는 *서버에서 제공되는 실제 값*으로 대체됩니다. 그런 다음, 이 템플릿은 *정적 HTML 파일로 변환되고 클라이언트로 전송*됩니다. 이 방식을 통해 HTML 페이지를 디자인하는 것이 더 쉽고, *클라이언트에서 API 호출을 수행하지 않고도 페이지에 변수를 표시할 수 있습니다.*

먼저, Express 프레임워크에서 사용 중인 템플릿 엔진을 설정해야 합니다. app.set('view engine', 'pug')라는 코드를 사용하여 Express에게 Pug를 사용하겠다고 알려줍니다. 또한, app 객체의 views 속성을 ./views/pug 디렉토리로 설정하는 app.set 메소드를 사용합니다. 이렇게 하면 Express에게 모든 뷰를 이 디렉토리를 기준으로 렌더링하라고 알려줍니다.  

마지막으로, 홈 페이지 라우트에서 res.render()를 사용하여 index를 첫 번째 인수로 전달합니다. 이것은 Pug 템플릿을 렌더링하도록 하는 것입니다. res.render('index')는 Express에게 "index.pug"라는 이름의 Pug 템플릿 파일을 찾아서 렌더링하라고 지시하는 것입니다. 이것은 특정 라우트에서 웹 페이지를 생성할 때 해당 라우트에 맞는 Pug 템플릿을 사용하도록 하는 것입니다.

만약 모든 것이 제대로 작동한다면, 앱의 홈 페이지는 이제 빈 화면이 아니라 Pug 템플릿을 렌더링한 메시지가 표시될 것입니다.  

***

Pug는 템플릿 엔진이며 프레임워크가 아닙니다. Pug(이전 이름은 Jade)은 HTML과 유사한 마크업 언어로, 웹 페이지의 구조를 정의하고 동적 데이터를 삽입하기 위한 템플릿 언어입니다. Pug는 Express.js와 같은 Node.js 웹 애플리케이션 프레임워크와 함께 자주 사용됩니다. Express와 Pug를 함께 사용하면 서버 측에서 웹 페이지를 동적으로 생성하고 클라이언트에게 제공할 수 있습니다. Pug은 서버 측에서 HTML을 생성하는 데 사용되는 템플릿 언어이고, 서버에서 동적으로 웹 페이지를 생성하는 데 사용합니다. 서버 측 렌더링 및 템플릿 작성에 중점을 둡니다.  

**장점**
- 가독성과 유지보수: Pug와 같은 템플릿 엔진을 사용하면 코드의 가독성이 향상되며 유지보수가 쉬워집니다. HTML 코드를 들여쓰기를 통해 시각적으로 나타내므로 코드의 구조를 빠르게 파악할 수 있습니다.
- 재사용성: 템플릿 엔진을 사용하면 페이지 요소를 쉽게 재사용할 수 있습니다. 이로 인해 개발 시간을 단축하고 코드 중복을 방지할 수 있습니다.
- 동적 데이터 처리: 템플릿 엔진은 동적 데이터를 쉽게 처리할 수 있습니다. 변수를 템플릿에 삽입하고 조건문, 반복문을 사용하여 동적 콘텐츠를 생성할 수 있습니다.
- 템플릿 상속: 템플릿 엔진은 템플릿 상속을 지원하여 레이아웃을 정의하고 이를 여러 페이지에 적용할 수 있게 합니다. 이로써 일관된 디자인과 레이아웃을 쉽게 구현할 수 있습니다.

**단점**
- 학습 곡선: Pug와 같은 템플릿 엔진을 처음 사용할 때 기존의 HTML과 다른 문법을 사용하므로 학습 곡선이 있을 수 있습니다. 개발자는 이러한 새로운 문법을 익혀야 합니다.
- 성능: 템플릿 엔진은 일반적으로 서버에서 HTML을 동적으로 렌더링하는 데 사용되므로 이는 추가적인 처리 시간을 필요로 합니다. 성능은 고려해야 할 요소 중 하나일 수 있습니다.
- 클라이언트 측 렌더링: 템플릿 엔진은 주로 서버 측 렌더링에 사용됩니다. 따라서 클라이언트 측 렌더링에 대한 요구 사항이 있는 경우에는 다른 접근 방식을 고려해야 할 수 있습니다.

***

app.set 메서드는 Express.js 애플리케이션 설정을 위한 메서드입니다. 이 메서드를 사용하여 애플리케이션 레벨 설정을 구성합니다. 예를 들어, app.set('view engine', 'pug')와 같이 사용하여 Express에게 특정 설정을 알립니다.

일반적으로 사용하는 몇 가지 app.set 설정은 다음과 같습니다:
- view engine: 템플릿 엔진을 설정합니다. 예를 들어, app.set('view engine', 'pug')는 Pug를 템플릿 엔진으로 사용하겠다고 설정하는 것입니다.
- views: 템플릿 파일들이 위치한 디렉토리를 설정합니다. app.set('views', 'views')와 같이 사용하여 Express에게 템플릿 파일들이 "views" 디렉토리에 있다고 알립니다.
- port: 웹 서버의 포트 번호를 설정합니다. app.set('port', 3000)와 같이 사용하여 서버가 3000번 포트에서 실행되도록 설정할 수 있습니다.
- 기타 설정: Express 애플리케이션에서 필요한 다양한 설정을 app.set을 통해 구성할 수 있습니다. 이러한 설정은 애플리케이션의 동작과 구성을 제어하는 데 사용됩니다.

요약하면, app.set은 Express.js 애플리케이션의 설정을 구성하는 데 사용되며, 이러한 설정은 전체 애플리케이션에 영향을 미칩니다.  

***

app.set('port', 3000)과 app.listen(3000, () => {})는 비슷한 기능을 수행하지만 약간의 차이가 있습니다.

1. **app.set('port', 3000)**: 이 설정은 Express 애플리케이션에 포트 번호를 할당합니다. 이 포트 번호는 애플리케이션 설정 중 하나로, Express 애플리케이션에서 사용될 포트를 지정하는 용도로 사용됩니다. 이 설정만으로는 서버가 시작되지 않으며, 별도의 `app.listen()` 함수를 **호출하여 서버를 시작**해야 합니다.

예를 들어:
```node.js
const express = require('express');
const app = express();
app.set('port', 3000);

app.listen(app.get('port'), () => {
  console.log(`Server is running on port ${app.get('port')}`);
});
```
`app.get('port')`를 사용하여 설정된 포트 번호를 가져와서 `app.listen()` 함수에 전달합니다.

2. **app.listen(3000, () => {})**: 이 함수는 Express 애플리케이션을 특정 포트(여기서는 3000)에서 바로 시작합니다. 따라서 `app.listen()`을 호출하면 서버가 지정된 포트에서 실행됩니다.

예를 들어:
```node.js
const express = require('express');
const app = express();

app.listen(3000, () => {
  console.log('Server is running on port 3000');
});
```
주요 차이점은 `app.set('port', 3000)`을 사용하면 포트 설정을 따로 저장하고 `app.listen()`에서 그 값을 가져와서 서버를 시작해야 하지만, `app.listen(3000, () => {})`을 사용하면 한 번에 포트를 설정하고 서버를 시작할 수 있다는 것입니다. 두 가지 방법 모두 사용 가능하며 개발자의 선호나 코드 구조에 따라 선택할 수 있습니다.

***

📝 index.pug
```pug
html
  head
    title FCC Advanced Node and Express
    meta(name='description', content='Home page')
    meta(charset='utf-8')
    meta(http-equiv='X-UA-Compatible', content='IE=edge')
    meta(name='viewport', content='width=device-width, initial-scale=1')
    link(rel='stylesheet', href='/public/style.css')
  body
    h1.border.center FCC Advanced Node and Express
    h2.center#pug-success-message
    | Looks like this page is being rendered from Pug into HTML!
    | #{title}
    p#pug-variable=message
    
    
    
    if showLogin
      hr
      h2.center Login Form
      form(action='/login', method='post').center
        div
          label Username:
          input(type='text', name='username')
        div
          label Password:
          input(type='password', name='password')
        div
          input(type='submit', value='Log In')
          
    if showRegistration
      hr
      h2.center Registration Form
      form(action='/register', method='post').center
        div
          label Username:
          input(type='text', name='username')
        div
          label Password:
          input(type='password', name='password')
        div
          input(type='submit', value='Register')

    if showSocialAuth
      hr
      h2.center Social Login
      .login.center
        a(href='/auth/github').text Login with Github!
```

***

📝 server.js
```node.js
'use strict';
require('dotenv').config();
const express = require('express');
const myDB = require('./connection');
const fccTesting = require('./freeCodeCamp/fcctesting.js');

const app = express();

fccTesting(app); //For FCC testing purposes
app.use('/public', express.static(process.cwd() + '/public'));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Index page (static HTML), 여기 작성
app.set('view engine', 'pug');
app.set('views', './views/pug');

app.route('/').get((req, res) => {
  // 여기 작성
  res.render('index');
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log('Listening on port ' + PORT);
});
```

***

1. **app.use(express.json());**: 이 코드는 Express 애플리케이션에서 JSON 형식의 요청 데이터를 파싱할 수 있도록 미들웨어를 추가하는 부분입니다. Express의 express.json() 미들웨어는 들어오는 HTTP 요청의 본문(body)을 JSON 형식으로 파싱하여 JavaScript 객체로 만들어줍니다. 이것은 클라이언트가 JSON 형식의 데이터를 POST 또는 PUT 요청을 통해 서버로 보낼 때 사용됩니다. 파싱된 데이터는 req.body를 통해 라우트 핸들러에서 접근할 수 있게 됩니다.

2. **app.use(express.urlencoded({ extended: true }));**: 이 코드는 Express 애플리케이션에서 URL-encoded 형식의 요청 데이터를 파싱할 수 있도록 미들웨어를 추가하는 부분입니다. URL-encoded 형식은 폼 데이터를 전송할 때 주로 사용됩니다. express.urlencoded() 미들웨어는 들어오는 HTTP 요청의 본문(body)을 URL-encoded 형식으로 파싱하여 JavaScript 객체로 만들어줍니다. { extended: true } 옵션은 파싱된 데이터가 복잡한 객체 및 배열을 지원하도록 하는 옵션입니다. 파싱된 데이터는 req.body를 통해 라우트 핸들러에서 접근할 수 있게 됩니다.

이 두 가지 미들웨어를 사용하면 Express 애플리케이션에서 클라이언트로부터 JSON 또는 URL-encoded 형식의 데이터를 읽을 수 있으며, 이 데이터를 가공하여 서버 측에서 사용할 수 있습니다.  

Express 버전 4.16.0 이후부터 body-parser의 대부분 기능이 Express 내장 미들웨어로 포함되었습니다. 따라서 새로운 Express 애플리케이션에서는 `body-parser`를 따로 설치하거나 사용할 필요가 없습니다. 결론적으로, Express 프레임워크의 최신 버전에서는 `app.use(express.urlencoded({ extended: true }));`와 같은 내장 미들웨어를 사용하는 것이 권장되며, `body-parser`를 사용할 필요가 없습니다. 이로 인해 미들웨어 설정이 단순화되고 더 좋은 기능을 제공할 수 있습니다.
***

URL-encoded 형식은 웹에서 데이터를 전송하거나 저장할 때 사용되는 데이터 인코딩 방식 중 하나입니다. 이 형식은 특수 문자나 공백과 같은 일부 문자를 URL에서 안전하게 전송하기 위해 사용됩니다.

URL-encoded 형식의 주요 특징은 다음과 같습니다:

1. **문자 대체**: URL-encoded 형식은 URL에서 사용되는 특수 문자와 일반적인 문자를 대체하는 방식으로 동작합니다. 예를 들어, 스페이스(space) 문자는 %20으로 인코딩됩니다. 이렇게 인코딩된 데이터는 URL에서 사용 가능하며, 데이터가 정확하게 전달됩니다.

2. **데이터 전송**: URL-encoded 데이터는 주로 HTML 폼에서 사용자로부터 수집한 데이터를 서버로 전송할 때 쓰입니다. 사용자가 웹 폼에서 입력한 데이터(예: 이름, 이메일 주소, 메시지)는 URL-encoded 형식으로 서버에 전송됩니다.

3. **쿼리 문자열**: URL-encoded 데이터는 URL의 쿼리 문자열 부분에서 매개변수를 전달하는 데도 사용됩니다. 예를 들어, http://example.com/search?q=keyword에서 "keyword"는 URL-encoded 형식으로 쿼리 문자열에 포함됩니다.

## Use a Template Engine's Powers

One of the greatest features of using a template engine is being able to pass variables from the server to the template file before rendering it to HTML.

In your Pug file, you're able to use a variable by referencing the variable name as `#{variable_name}` inline with other text on an element or by using an equal sign on the element without a space such as `p=variable_name` which assigns the variable's value to the p element's text.

Pug is all about using whitespace and tabs to show nested elements and cutting down on the amount of code needed to make a beautiful site.

Take the following Pug code for example:
```pug
head
  script(type='text/javascript').
    if (foo) bar(1 + 5);
body
  if youAreUsingPug
      p You are amazing
    else
      p Get on it!
```
```html
<head>
  <script type="text/javascript">
    if (foo) bar(1 + 5);
  </script>
</head>
<body>
  <p>You are amazing</p>
</body>
```
Your `index.pug` file included in your project, uses the variables `title` and `message`.

Pass those from your server to the Pug file by adding an object as a second argument to your `res.render` call with the variables and their values. Give the `title` a value of `Hello` and `message` a value of `Please log in`.

It should look like:
```node.js
res.render('index', { title: 'Hello', message: 'Please log in' });
```

Now refresh your page, and you should see those values rendered in your view in the correct spot as laid out in your index.pug file!  

***

`Looks like this page is being rendered from Pug into HTML!`에서  
```
Looks like this page is being rendered from Pug into HTML! Hello
Please log in
```
으로 바뀌었다. 

***

1. **Inline Text with Variable**
변수의 값을 원하는 HTML요소 안에 집어넣고 싶을 때. 변수 값을 #{variable_name}으로 표시
```pug
p Hello, #{user}! How are you today?
```
```html
// 예를 들어, user 변수가 "Alice"라면
<p>Hello, Alice! How are you today?</p>
```
2. **Assign Variable's Value to Element Text**
변수의 값을 특정 HTML 요소의 텍스트로 할당. 이것은 변수의 값을 지정한 요소의 텍스트로 대체.
```pug
p=user
```
```html
<p>Bob</p>
```
***

`p#pug-variable=message`는?  

- p: p는 HTML <p> 요소를 나타냅니다. 이는 단락(paragraph)을 생성하는 HTML 태그입니다.
- #pug-variable: #은 HTML 요소에 ID를 할당하는 부분입니다. 여기서 "pug-variable"이라는 ID를 <p> 요소에 할당합니다.
- =message: =는 해당 HTML 요소의 텍스트 내용을 정의하는 부분입니다. 여기에서는 message 변수의 값을 이 <p> 요소의 텍스트로 대체합니다.

따라서 이 코드 조각은 "pug-variable" ID를 가진 <p> 요소를 생성하고, 이 요소의 텍스트 내용은 message 변수의 값으로 설정됩니다. 결과적으로, 웹 페이지에서 이 요소는 "message" 변수의 값으로 채워진 단락을 나타냅니다.
```html
<p id="pug-variable">Hello, World!</p>
```

## Set up Passport

It's time to set up Passport so you can finally start allowing a user to register or log in to an account. In addition to Passport, you will use Express-session to handle sessions. Express-session has a ton of advanced features you can use, but for now you are just going to use the basics. Using this middleware saves the session id as a cookie in the client, and allows us to access the session data using that id on the server. This way, you keep personal account information out of the cookie used by the client to tell to your server clients are authenticated and keep the key to access the data stored on the server.

`passport@~0.4.1` and `express-session@~1.17.1` are already installed, and are both listed as dependencies in your `package.json` file.

You will need to set up the session settings and initialize Passport. First, create the variables `session` and `passport` to require `express-session` and `passport` respectively.

Then, set up your Express app to use the session by defining the following options:
```node.js
app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: true,
  saveUninitialized: true,
  cookie: { secure: false }
}));
```
Be sure to add `SESSION_SECRET` to your `.env` file, and give it a random value. This is used to compute the hash used to encrypt your cookie!

After you do all that, tell your express app to use `passport.initialize()` and `passport.session()`.

***
```
const session = require('express-session');
const passport = require('passport');

app.use(passport.initialize());
app.use(passport.session());
app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: true,
  saveUninitialized: true,
  cookie: { secure: false }
}));
```
***

1. **express-session 모듈**: 이 모듈은 세션 관리를 위한 Express의 미들웨어입니다. 세션은 웹 애플리케이션에서 사용자의 상태를 유지하고 세션 정보를 저장하는 데 사용됩니다. express-session을 사용하면 세션 데이터를 서버 측에서 유지하고, 클라이언트에 대한 고유한 세션 식별자를 부여할 수 있습니다. 이 모듈은 secret, resave, saveUninitialized, cookie 등의 설정을 사용하여 세션의 동작을 제어할 수 있습니다.

2. **passport.session() 미들웨어**: passport.session()은 Passport 인증 미들웨어와 함께 사용됩니다. Passport는 사용자 인증을 처리하는 도구이며, 사용자 인증을 성공적으로 완료한 후 사용자를 세션에 저장하려면 세션 관리 기능이 필요합니다. passport.session()은 Passport가 사용자 정보를 세션에 저장하고 사용자가 다시 요청을 보낼 때 해당 세션 정보를 검색할 수 있도록 도와주는 미들웨어입니다.

결국, passport.session()는 Passport가 세션을 사용하여 사용자를 추적하고 인증 상태를 유지하는 데 필요한 부분이며, express-session은 이 세션을 관리하는 데 사용되는 Express의 세션 미들웨어입니다. 따라서 이 두 미들웨어를 함께 사용함으로써 Passport를 통해 사용자 인증을 처리하고 세션을 효과적으로 관리할 수 있습니다.

***

1. **passport.initialize():**

passport.initialize()는 Passport.js를 초기화하는 미들웨어입니다.
Passport.js는 사용자 인증 및 인가를 처리하는 데 사용되며, 이를 Express.js 애플리케이션과 함께 사용하려면 초기화해야 합니다.
이 미들웨어는 요청 처리 과정에서 Passport.js가 사용될 수 있도록 Passport 객체를 설정하고 초기화합니다. 즉, Passport.js를 Express.js 애플리케이션에 연결하는 역할을 합니다.

2. **passport.session():**

passport.session()은 세션을 사용하는 Passport.js 미들웨어입니다.
Passport.js는 세션을 통해 사용자의 로그인 상태를 유지하고 사용자 식별을 관리합니다. passport.session()은 세션 데이터를 처리하고 이 데이터를 Passport.js에 제공합니다.
이 미들웨어는 요청에서 세션을 사용할 수 있도록 설정하고, Passport.js가 사용자 세션을 유지하고 관리할 수 있게 합니다.  

3. **secret: process.env.SESSION_SECRET:**

secret은 세션 데이터를 서명할 때 사용하는 비밀 키입니다.
process.env.SESSION_SECRET은 환경 변수로부터 비밀 키를 가져오는 것을 의미합니다. 비밀 키는 세션 데이터를 보호하고 위조를 방지하는 데 사용됩니다. 보안상 중요한 옵션입니다.

4. **resave: true:**

resave는 세션 데이터가 변경되지 않더라도 세션을 다시 저장할지 여부를 나타냅니다.
true로 설정하면 세션 데이터가 변경되지 않더라도 세션이 요청마다 다시 저장됩니다. 일반적으로 true로 설정하는 것이 좋습니다.

5. **saveUninitialized: true:**

saveUninitialized는 초기화되지 않은 세션을 저장할지 여부를 나타냅니다.
true로 설정하면 초기화되지 않은 세션도 저장됩니다. 초기화되지 않은 세션은 처음 요청에서 생성되고 사용자가 로그인과 같은 동작을 하기 전까지 비어 있는 상태로 남게 됩니다.

6. **cookie: { secure: false }:**

cookie 객체는 클라이언트에 저장되는 세션 쿠키에 대한 설정을 제공합니다.
secure: false로 설정하면 세션 쿠키가 안전하지 않은 연결(HTTPS가 아닌 연결)에서도 사용될 수 있도록 허용합니다. true로 설정하면 세션 쿠키는 HTTPS 연결에서만 전송됩니다. 이것은 보안 관련 옵션으로, 보통 개발 환경에서는 false로 설정하고 프로덕션 환경에서는 true로 설정합니다.  

- 기본적으로 express-session은 클라이언트와의 각 요청마다 세션을 확인하고 관리합니다. 클라이언트가 처음 요청을 보내면 세션 ID를 생성하고 쿠키에 저장한 후, 이후의 요청에서는 클라이언트가 가지고 있는 쿠키에 있는 세션 ID를 확인하여 해당 세션과 관련된 데이터를 로드합니다.
- resave 옵션이 false로 설정되면, 세션 데이터가 변경되지 않은 경우에만 세션을 저장합니다. 이렇게 함으로써 불필요한 세션 저장을 방지하고 성능을 향상시킵니다.
- saveUninitialized 옵션이 true로 설정되면, 세션이 초기화될 때도 세션을 저장합니다. 이는 클라이언트가 최초로 서버에 접속했을 때 세션을 저장한다는 것을 의미합니다.

***

패스포트를 사용해 사용자들이 자신의 계정을 만들고 로그인할 수 있다. 이 작업을 할 때 세션을 다루는데, 세션은 서버에서 사용자에 대한 정보를 저장하고, 그 정보를 사용해 사용자를 식별하는 방법이다.  

익스프레스 세션을 사용하면 세션 ID를 사용자의 컴퓨터에 작은 정보 조각으로 저장하고, 이 ID를 사용해 서버에서 사용자와 관련된 정보를 찾을 수 있다. 이렇게 하면 사용자의 계정 정보를 사용자의 컴퓨터에 저장하지 않고도 서버에서 정보를 안전하게 다룰 수 있다.  

*** 

익스프레스-세션은 세션 데이터를 처리하기 위한 미들웨어 중 하나이며, 클라이언트 측에 세션 ID를 쿠키로 저장하고 이 ID를 사용하여 서버에서 세션 데이터에 액세스할 수 있게 해줍니다. 이렇게 함으로써, 클라이언트가 서버에 인증되었음을 나타내는 데 사용되는 쿠키에는 개인 계정 정보가 들어가지 않고, 서버에서 데이터에 액세스하기 위한 키를 안전하게 보관할 수 있게 됩니다. 이는 보안과 인증에 관련된 웹 애플리케이션 개발에서 중요한 역할을 합니다.

***

로그인 프로세스에서 클라이언트와 서버 간의 정보 교환은 일반적으로 다음과 같은 단계로 이루어집니다. 아래의 상세 설명은 Express.js와 Passport.js와 같은 웹 애플리케이션 프레임워크와 라이브러리를 사용하는 일반적인 시나리오를 기반으로 합니다:

1. **사용자 로그인 페이지 표시**
- 클라이언트가 로그인 페이지를 요청
- 서버는 클라이언트에게 로그인 페이지를 렌더링하기 위한 HTML을 제공
2. **사용자 인증 요청**
- 사용자가 로그인 정보를 로그인 페이지에 입력하고, 로그인 버튼을 클릭
- 클라이언트는 이 정보를 서버로 POST 요청을 보낸다.
3. **서버 측 인증**
- 서버는 클라이언트로부터 받은 로그인 정보를 검증하고, 사용자가 유효한지 확인
- 만약 인증이 성공하면, 서버는 클라이언트에게 세션을 생성하고, 세션 ID를 클라이언트에게 전달. 이 세션 ID는 일반적으로 쿠키로 저장된다.
4. **클라이언트 측 응답**
- 클라이언트는 세션 ID를 쿠키로 저장하고, 로그인 성공 페이지로 리디렉션
5. **세션 유지**
- 클라이언트응 이후 요청에서 세션 ID를 함께 서버에 전달. 이 세션 ID를 통해 서버는 해당 클라이언트의 세션 정보를 식별
6. **보호된 페이지 접근**
- 클라이언트가 보호된 페이지에 액세스하려고 시도하면, 클라이언트의 세션 ID가 서버로 전송된다.
- 서버는 세션 ID를 사용하여 해당 클라이언트가 인증되었는지 확인하고, 접근 권한을 부여하거나 거부한다.
7. **세션 만료 및 로그아웃**
- 세션은 시간이 지남에 따라 만료될 수 있으며, 로그아웃 시에는 세션을 무효화한다.

***

**세션이란?**  

세션은 웹 애플리케이션에서 사용자 관련 정보를 유지하고 관리하기 위한 방법 중 하나입니다. 세션은 일시적인 데이터 저장 공간으로, 사용자가 웹 애플리케이션과 상호 작용하는 동안 정보를 보관하는 데 사용됩니다. 일반적으로 서버 측에서 세션 데이터를 저장하며, 클라이언트와 서버 간의 상호 작용을 추적하고 사용자를 식별하는 데 도움을 줍니다. 예를 들어, 사용자가 로그인한 후에 웹 페이지를 탐색하는 동안 세션을 통해 사용자의 로그인 상태를 유지할 수 있습니다.  

http 프로토콜은 stateless이고, 이는 서버로 가는 모든 요청이 이전 요청과 독립적으로 다뤄진다는 것을 말한다. 요청끼리 연결이 없다. 이처럼 요청이 끝나면 서버는 클라이언트를 잊기 때문에, 요청할 때마다 우리가 누군지 알려줘야 하고 이 방법 중 하나가 세션이다. 만약 같은 웹사이트의 다른 페이지로 이동하면, 브라우저는 자동으로 세션 ID 쿠기를 서버에 보내고, 서버는 세션 ID를 확인한다. 중요한 유저 정보는 서버에 있고, 유저가 가진 것은 세션 ID뿐이다.  
[노마드 코더](https://youtu.be/tosLBcAX1vk?feature=shared)

***

**쿠키?**

쿠키는 웹 브라우저와 웹 서버 간에 정보를 교환하기 위한 작은 데이터 조각입니다. 이 데이터는 클라이언트(웹 브라우저) 측에 저장되며, 웹 서버에서 전송하고 응답을 받을 때 사용됩니다. 쿠키는 클라이언트 측에서 지속적으로 저장될 수 있으며, 사용자의 브라우징 세션 간에 정보를 유지하거나 사용자를 식별하는 데 사용됩니다.

예를 들어, 웹 사이트에 로그인하면 서버는 클라이언트에게 고유한 세션 ID와 같은 정보를 쿠키로 전달합니다. 그 후에 사용자가 다른 페이지로 이동할 때, 브라우저는 이 쿠키를 함께 서버에 다시 전송하여 사용자를 인증하고 이전 상태나 로그인 정보를 복원할 수 있습니다. 쿠키는 웹 애플리케이션에서 세션 관리, 로그인 유지, 사용자 추적 및 기타 다양한 작업에 사용됩니다  

브라우저에 쿠키를 저장한 후 그 해당 웹사이트를 방문할 때마다 브라우저는 해당 쿠키도 요쳥과 함께 보낸다. 쿠키는 인증 뿐만 아니라 여러 가지 정보를 저장할 수 있다. 만약 웹사이트 언어 설정을 바꾸면 서버는 쿠키를 주고, 바꾼 언어를 저장한다.  

***

```node.js
'use strict';
require('dotenv').config();
const express = require('express');
const myDB = require('./connection');
const fccTesting = require('./freeCodeCamp/fcctesting.js');

const app = express();
const session = require('express-session');
const passport = require('passport');

fccTesting(app); //For FCC testing purposes
app.use('/public', express.static(process.cwd() + '/public'));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.use(passport.initialize());
app.use(passport.session());
app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: true,
  saveUninitialized: true,
  cookie: { secure: false }
}));

app.set('view engine', 'pug');
app.set('views', './views/pug');

app.route('/').get((req, res) => {
  res.render('index', { title: 'Hello', message: 'Please log in' });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log('Listening on port ' + PORT);
});
```

## Serialization of a User Object

Serialization and deserialization are important concepts in regards to authentication. To serialize an object means to convert its contents into a small key that can then be deserialized into the original object. This is what allows us to know who has communicated with the server without having to send the authentication data, like the username and password, at each request for a new page.

To set this up properly, you need to have a serialize function and a deserialize function. In Passport, these can be created with:
```node.js
passport.serializeUser(cb);
passport.deserializeUser(cb);
```
The callback function passed to `serializeUser` is called with two arguments: the full user object, and a callback used by passport.

The callback expects two arguments: An error, if any, and a unique key to identify the user that should be returned in the callback. You will use the user's `_id` in the object. This is guaranteed to be unique, as it is generated by MongoDB.

Similarly, `deserializeUser` is called with two arguments: the unique key, and a callback function.

This callback expects two arguments: An error, if any, and the full user object. To get the full user object, make a query search for a Mongo `_id`, as shown below:
```node.js
passport.serializeUser((user, done) => {
  done(null, user._id); // 저장할 사용자의 고유 ID를 세션에 저장
});

passport.deserializeUser((id, done) => {
  myDataBase.findOne({ _id: new ObjectID(id) }, (err, doc) => {
    done(null, null); // 사용자 객체를 세션에서 가져와 반환
  });
});
```
Add the two functions above to your server. The `ObjectID` class comes from the `mongodb` package. `mongodb@~3.6.0` has already been added as a dependency. Declare this class with:
```node.js
const { ObjectID } = require('mongodb');
```
The `deserializeUser` will throw an error until you set up the database connection. So, for now, comment out the `myDatabase.findOne` call, and just call `done(null, null)` in the `deserializeUser` callback function.
;
***

시리얼라이제이션(Serialization)과 디시리얼라이제이션(Deserialization)은 인증과 관련된 중요한 개념입니다. 이것을 이해하기 위해 좀 더 상세하게 설명해 드리겠습니다. 시리얼라이제이션은 어떤 객체나 데이터를 그 내용을 나타내는 작은 키로 변환하는 프로세스를 의미합니다. 이 작은 키는 나중에 해당 객체나 데이터로 복원할 수 있는 정보를 담고 있습니다. 이것은 서버와의 통신에서 새로운 페이지를 요청할 때마다 사용자 이름과 비밀번호와 같은 인증 데이터를 계속 보내지 않고도, 누가 서버와 소통하는지를 파악할 수 있게 해줍니다

***

1. **serializeUser함수**: 이 함수는 사용자 객체를 직렬화하는데 사용된다. 직렬화란 사용자 객체를 고유한 식별자로 변환하는 과정을 의미. 보통 이 식별자는 세션에 저장된다. serializeUser함수는 두 개의 인수를 받는다. 첫 번째 인수는 전체 사용자 객체이고, 두 번째 인수는 Passport에서 사용하는 콜백 함수입니다. 이 콜백 함수는 다음 두 가지 인수를 기대합니다:
- 에러
- 사용자를 식별하는 고유한 키(일반적으로 사용자의 데이터베이스ID)
일반적으로, serializeUser 함수는 사용자의 ID를 사용하여 사용자를 식별하고 세션에 저장합니다.

사용자 객체를 받아 해당 사용자를 세션에 저장. 일반적으로 사용자 객체의 고유 식별자를 세션에 저장. 

2. **deserializeUser함수**: 이 함수는 역직렬화하는데 사용된다. 역직렬화란 세션에서 저장된 사용자 식별자(일반적으로 데이터베이스ID)를 사용하여 실제 사용자 객체로 변환하는 과정. 두 개의 인수를 받는다. 첫 번째 인수는 사용자를 식별하는 고유한 키이고, 두 번째는 Passport에서 사용하는 콜백 함수입니다. 이 콜백 함수는 다음 두 가지 인수를 기대합니다:
- 에러
- 전체 사용자 객체
deserializeUser 함수는 주어진 고유한 키(사용자 ID)를 사용하여 데이터베이스에서 해당 사용자를 찾아서 가져와야 합니다. 그 후, 가져온 사용자 객체를 콜백 함수를 통해 Passport에 반환합니다.

세션에서 사용자 식별자를 받아 해당 사용자 객체를 검색하고 반환하는 역할. 즉 사용자 객체를 복구

***

```node.js
app.post('/login', passport.authenticate('local', {
    successRedirect: '/dashboard',
    failureRedirect: '/login',
    failureFlash: true
}));

// serializeUser에 사용자를 전달
passport.serializeUser(function(user, done) {
    done(null, user._id);
});
```
위의 코드에서 passport.authenticate('local', ...) 메서드는 사용자 인증이 성공하면 사용자 객체(user)를 인자로 받고, 이 객체가 passport.serializeUser로 전달됩니다.

요약하면, passport.serializeUser 함수에서 user는 Passport가 로그인 후에 사용자 정보를 저장하는 데 사용되는 사용자 객체를 나타냅니다. 

- 여기서 `failureFlash: true`는 로그인 실패 시에 failureFlash 메시지를 활성화하는 옵션입니다. 이 옵션이 활성화되면, 로그인 시도가 실패할 때 해당 메시지가 `req.flash()`에 저장되어 이후 요청에서 사용할 수 있습니다.
- `req.flash()`는 Express의 일종의 플래시 메시지 시스템입니다. 플래시 메시지는 현재 요청 및 다음 요청 간에 메시지를 저장하고 전달하는 데 사용됩니다. 일반적으로 로그인 실패와 같은 상황에서 오류 메시지를 사용자에게 표시하는 데 활용됩니다.
- 따라서 `failureFlash: true`를 설정하면 로그인 실패 시에 플래시 메시지가 설정되어, 예를 들어 `/login` 경로로 리디렉션될 때 해당 메시지가 함께 전달됩니다. 실패한 로그인 시도에 대한 메시지를 사용자에게 표시하는 데 유용합니다.


***

```node.js
const { ObjectID } = require('mongodb');
const newId = new ObjectID(); // 새로운 고유한 ObjectID 생성
```
- 이렇게 생성된 newId는 MongoDB 문서를 삽입하거나 업데이트할 때 _id 필드에 사용할 수 있습니다.
- const { ObjectID } 코드는 mongodb 패키지에서 ObjectID 객체를 가져와서 현재 스코프에 ObjectID 변수로 사용할 수 있도록 합니다. 이후에 ObjectID를 사용하여 MongoDB에서 _id 필드를 다루거나 새로운 ObjectID를 생성할 수 있습니다.
- new ObjectID(id)를 사용하면 MongoDB에서 사용하는 특정 형식의 _id로 변환됩니다.
-  _id는 일반적으로 ObjectId 형식을 가진다.
***

```node.js
'use strict';
require('dotenv').config();
const express = require('express');
const myDB = require('./connection');
const fccTesting = require('./freeCodeCamp/fcctesting.js');

const app = express();
const session = require('express-session');
const passport = require('passport');
const ObjectID = require('mongodb').ObjectID;

fccTesting(app); //For FCC testing purposes
app.use('/public', express.static(process.cwd() + '/public'));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.use(passport.initialize());
app.use(passport.session());
app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: true,
  saveUninitialized: true,
  cookie: { secure: false }
}));

app.set('view engine', 'pug');
app.set('views', './views/pug');

app.route('/').get((req, res) => {
  res.render('index', { title: 'Hello', message: 'Please log in' });
});


passport.serializeUser((user, done) => {
  done(null, user._id);
});

passport.deserializeUser((id, done) => {
  // myDataBase.findOne({ _id: new ObjectID(id) }, (err, doc) => {
  done(null, null);
  // });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log('Listening on port ' + PORT);
});
```

## Implement the Serialization of a Passport User

You are not loading an actual user object since the database is not set up. Connect to the database once, when you start the server, and keep a persistent connection for the full life-cycle of the app. To do this, add your database's connection string (for example: `mongodb+srv://<username>:<password>@cluster0-jvwxi.mongodb.net/?retryWrites=true&w=majority`) to the environment variable `MONGO_URI`. This is used in the `connection.js` file.

If you are having issues setting up a free database on MongoDB Atlas, check out this tutorial.

Now you want to connect to your database, then start listening for requests. The purpose of this is to not allow requests before your database is connected or if there is a database error. To accomplish this, encompass your serialization and app routes in the following code:
```node.js
myDB(async client => {
  const myDataBase = await client.db('database').collection('users');

  // Be sure to change the title
  app.route('/').get((req, res) => {
    // Change the response to render the Pug template
    res.render('index', {
      title: 'Connected to Database',
      message: 'Please login'
    });
  });

  // Serialization and deserialization here...

  // Be sure to add this...
}).catch(e => {
  app.route('/').get((req, res) => {
    res.render('index', { title: e, message: 'Unable to connect to database' });
  });
});
// app.listen out here...
```
Be sure to uncomment the `myDataBase` code in `deserializeUser`, and edit your `done(null, null)` to include the `doc`.

***

📝 server.js
```node.js
'use strict';
require('dotenv').config();
const express = require('express');
const myDB = require('./connection');
const fccTesting = require('./freeCodeCamp/fcctesting.js');

const app = express();
const session = require('express-session');
const passport = require('passport');
const ObjectID = require('mongodb').ObjectID;

fccTesting(app); //For FCC testing purposes
app.use('/public', express.static(process.cwd() + '/public'));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.use(passport.initialize());
app.use(passport.session());
app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: true,
  saveUninitialized: true,
  cookie: { secure: false }
}));

app.set('view engine', 'pug');
app.set('views', './views/pug');

myDB(async client => {
  const myDataBase = await client.db('database').collection('users');

  // Be sure to change the title
  app.route('/').get((req, res) => {
    // Change the response to render the Pug template
    res.render('index', {
      title: 'Connected to Database',
      message: 'Please login'
    });
  });

  // Serialization and deserialization here...
  passport.serializeUser((user, done) => {
    done(null, user._id);
  });

  passport.deserializeUser((id, done) => {
    myDataBase.findOne({ _id: new ObjectID(id) }, (err, doc) => {
    done(null, doc);
    });
  });

  // Be sure to add this...
}).catch(e => {
  app.route('/').get((req, res) => {
    res.render('index', { title: e, message: 'Unable to connect to database' });
  });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log('Listening on port ' + PORT);
});
```

MONGO_URI='mongodb+srv://<>:<>@cluster0.agb0oy4.mongodb.net/?retryWrites=true&w=majority'

📝 connection.js
```node.js
// Do not change this file
require('dotenv').config();
const { MongoClient } = require('mongodb');

async function main(callback) {
    const URI = process.env.MONGO_URI; // Declare MONGO_URI in your .env file
    const client = new MongoClient(URI, { useNewUrlParser: true, useUnifiedTopology: true });

    try {
        // Connect to the MongoDB cluster
        await client.connect();

        // Make the appropriate DB calls
        await callback(client);

    } catch (e) {
        // Catch any errors
        console.error(e);
        throw new Error('Unable to Connect to Database')
    }
}

module.exports = main;
```
***

```node.js
const myDataBase = await client.db('database').collection('users'); 
```
1. client: 이것은 MongoDB 클라이언트 객체입니다. MongoDB 클라이언트는 MongoDB 서버와 통신하고 데이터베이스 작업을 수행하는 데 사용됩니다.

2. client.db('database'): 이 부분은 'database'라는 데이터베이스에 연결하는 코드입니다. MongoDB는 여러 데이터베이스를 지원하며 이 코드는 'database'라는 데이터베이스에 연결하려는 것을 나타냅니다.

3. .collection('users'): 이 부분은 'users'라는 컬렉션(데이터 테이블과 유사한 개념)을 가리키는 코드입니다. MongoDB는 데이터를 컬렉션 단위로 구성하며, 이 코드는 'users' 컬렉션에 연결하려는 것을 나타냅니다.

따라서 이 코드는 MongoDB 클라이언트를 사용하여 'database' 데이터베이스 내의 'users' 컬렉션을 가리키는 데이터베이스 객체를 가져온다고 해석할 수 있습니다.  

***

1. **const URI = process.env.MONGO_URI;**: 이 줄은 환경 변수(MONGO_URI)에서 MongoDB 연결 URI를 읽어와 URI 변수에 저장합니다. 이 URI는 MongoDB 서버에 연결하는 데 사용됩니다. 일반적으로 .env 파일에 저장된 환경 변수를 사용하며, 이렇게 함으로써 중요한 연결 정보를 숨길 수 있습니다.
2. **const client = new MongoClient(URI, { useNewUrlParser: true, useUnifiedTopology: true });**: 이 줄은 MongoDB 클라이언트를 생성하고, 이 클라이언트를 사용하여 MongoDB 서버에 연결합니다. URI 변수에서 가져온 연결 URI와 함께 useNewUrlParser와 useUnifiedTopology 옵션을 전달합니다. useNewUrlParser는 MongoDB 드라이버의 새로운 URL 파싱 엔진을 사용하도록 지시하며, useUnifiedTopology는 MongoDB 드라이버의 새로운 토폴로지 엔진을 사용하도록 지시합니다.
3. **await client.connect();**: 이 줄은 MongoDB 클라이언트를 사용하여 MongoDB 서버에 연결합니다. connect 메서드는 비동기로 호출되며 연결이 확립될 때까지 기다립니다.
4. **const myDataBase = await client.db('database').collection('users');**: 이 줄은 연결된 MongoDB 서버에서 데이터베이스와 users 컬렉션에 액세스하기 위한 변수를 설정합니다. client.db('database')는 database라는 데이터베이스에 연결하고, collection('users')는 해당 데이터베이스 내의 users 컬렉션에 액세스합니다. 이후에 myDataBase 변수를 사용하여 users 컬렉션에 대한 데이터베이스 작업을 수행할 수 있습니다.

***

1. myDB호출
2. connection.js의 main 비동기 함수 실행
3. MongoDB서버에 연결하고, callback(client)실행 -> server.js로
4. myDataBase에 문제 없으면, 이후 route와 serializerUser등 함수 실행되고, 에러 있으면 에러에 맞는 route 실행,

즉, DB에 문제 없을 시에만 코드가 실행된다. 


## Authentication Strategies

A strategy is a way of authenticating a user. You can use a strategy for allowing users to authenticate based on locally saved information (if you have them register first) or from a variety of providers such as Google or GitHub. For this project, we will use Passport middleware. Passport provides a comprehensive set of strategies that support authentication using a username and password, GitHub, Google, and more.

`passport-local@~1.0.0` has already been added as a dependency. Add it to your server as follows:
```node.js
const LocalStrategy = require('passport-local');
```
Tell passport to use an instantiated `LocalStrategy` object with a few settings defined. Make sure this (as well as everything from this point on) is encapsulated in the database connection since it relies on it!:
```node.js
passport.use(new LocalStrategy((username, password, done) => {
  myDataBase.findOne({ username: username }, (err, user) => {
    console.log(`User ${username} attempted to log in.`);
    if (err) return done(err);
    if (!user) return done(null, false);
    if (password !== user.password) return done(null, false);
    return done(null, user);
  });
}));
```
This is defining the process to use when you try to authenticate someone locally. First, it tries to find a user in your database with the username entered. Then, it checks for the password to match. Finally, if no errors have popped up that you checked for (e.g. an incorrect password), the `user` object is returned and they are authenticated.

Many strategies are set up using different settings. Generally, it is easy to set it up based on the README in that strategy's repository. A good example of this is the GitHub strategy where you don't need to worry about a username or password because the user will be sent to GitHub's auth page to authenticate. As long as they are logged in and agree then GitHub returns their profile for you to use.

In the next step, you will set up how to actually call the authentication strategy to validate a user based on form data.  

***

passport.use()는 Passport.js 라이브러리를 사용할 때, 사용자 인증을 처리하는 데 사용되는 메서드 중 하나입니다. 이 메서드는 Passport에서 사용할 인증 전략을 설정하고 등록하는 데 사용됩니다.

Passport.js는 다양한 인증 전략을 지원하며, 각 전략은 다른 방식으로 사용자 인증을 처리합니다. 예를 들어, 로컬 인증, 소셜 미디어 인증, OpenID, 또는 사용자 지정 인증 방법을 사용할 수 있습니다. passport.use() 메서드는 이러한 전략 중 하나를 선택하고 설정하는 데 사용됩니다.  

```node.js
passport.use(new LocalStrategy(options, verifyCallback));
```

- LocalStrategy는 Passport의 로컬 전략을 나타냅니다.
- options는 해당 전략의 옵션을 설정하는 객체입니다. 이 객체는 로그인 양식의 필드 이름, 사용자 정보가 저장된 위치 등과 관련된 설정을 포함할 수 있습니다.
- verifyCallback는 사용자 인증을 처리하는 콜백 함수입니다. 이 함수는 사용자 이름과 비밀번호를 받아서 실제 사용자 정보를 확인하고 사용자가 인증되었는지 여부를 결정합니다.

전략을 설정하고 passport.use()를 호출한 후, 해당 전략은 Passport.js가 요청을 처리할 때 사용자 인증을 수행하게 됩니다. 사용자가 로그인 시도를 하면 설정된 전략이 활성화되고, 해당 전략에 따라 사용자 정보의 유효성을 확인하고 사용자를 인증하거나 거부할 수 있습니다.

## How to Use Passport Strategies

In the `index.pug` file supplied, there is a login form. It is hidden because of the inline JavaScript `if showLogin` with the form indented after it.

In the `res.render` for that page, add a new variable to the object, `showLogin: true`. When you refresh your page, you should then see the form! This form is set up to POST on `/login`. So, this is where you should set up to accept the POST request and authenticate the user.

For this challenge, you should add the route `/login` to accept a POST request. To authenticate on this route, you need to add a middleware to do so before then sending a response. This is done by just passing another argument with the middleware before with your response. The middleware to use is `passport.authenticate('local')`.

`passport.authenticate` can also take some options as an argument such as `{ failureRedirect: '/' }` which is incredibly useful, so be sure to add that in as well. Add a response after using the middleware (which will only be called if the authentication middleware passes) that redirects the user to `/profile`. Add that route, as well, and make it render the view `profile.pug`.

If the authentication was successful, the user object will be saved in `req.user`.

At this point, if you enter a username and password in the form, it should redirect to the home page `/`, and the console of your server should display `'User {USERNAME} attempted to log in.'`, since we currently cannot login a user who isn't registered.  

***

```node.js
'use strict';
require('dotenv').config();
const express = require('express');
const myDB = require('./connection');
const fccTesting = require('./freeCodeCamp/fcctesting.js');
const session = require('express-session');
const passport = require('passport');
const ObjectID = require('mongodb').ObjectID;
const LocalStrategy = require('passport-local');

const app = express();

app.set('view engine', 'pug');
app.set('views', './views/pug');

app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: true,
  saveUninitialized: true,
  cookie: { secure: false }
}));

app.use(passport.initialize());
app.use(passport.session());

fccTesting(app); //For FCC testing purposes
app.use('/public', express.static(process.cwd() + '/public'));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));


myDB(async client => {
  const myDataBase = await client.db('database').collection('users');

  // Be sure to change the title
  app.route('/').get((req, res) => {
    // Change the response to render the Pug template
    res.render('index', {
      title: 'Connected to Database',
      message: 'Please login',
      showLogin: true
    });
  });

  app.route('/login').post(passport.authenticate('local', { failureRedirect: '/' }), (req, res) => {
    res.redirect('/profile');
  });

  passport.use(new LocalStrategy((username, password, done) => {
    myDataBase.findOne({ username: username }, (err, user) => {
      console.log(`User ${username} attempted to log in.`);
      if (err) return done(err);
      if (!user) return done(null, false);
      if (password !== user.password) return done(null, false);
      return done(null, user);
    });
  }));

  // Serialization and deserialization here...
  passport.serializeUser((user, done) => {
    done(null, user._id);
  });

  passport.deserializeUser((id, done) => {
    myDataBase.findOne({ _id: new ObjectID(id) }, (err, doc) => {
    done(null, doc);
    });
  });

  // Be sure to add this...
}).catch(e => {
  app.route('/').get((req, res) => {
    res.render('index', { title: e, message: 'Unable to connect to database' });
  });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log('Listening on port ' + PORT);
});
```

***

1. **index.pug 파일**: index.pug 파일에는 로그인 폼이 포함되어 있으나 초기에는 숨겨져 있습니다. 이 폼은 사용자가 로그인 정보(사용자 이름 및 비밀번호)를 입력할 수 있는 폼입니다.

2. **res.render() 함수**: 웹 애플리케이션의 서버에서 res.render() 함수를 사용하여 웹 페이지를 렌더링하고 브라우저에 표시하는 부분입니다. 이 때, showLogin: true라는 변수를 템플릿으로 전달하여 로그인 폼을 표시할 수 있도록 합니다.

3. **/login 라우트**: POST 요청을 수신하고 사용자 인증을 처리하는 라우트를 설정합니다. 이 라우트는 사용자가 로그인을 시도할 때 호출됩니다. passport.authenticate('local') 미들웨어를 사용하여 사용자의 자격을 검사하고, { failureRedirect: '/' } 옵션을 사용하여 인증 실패 시 홈 페이지로 리디렉션합니다.

4. **req.user**: 사용자가 성공적으로 인증되면 req.user 객체에 사용자 정보가 저장됩니다.

5. **/profile 라우트**: 인증에 성공한 사용자를 위한 프로필 페이지를 설정합니다. 이 페이지는 profile.pug 뷰를 렌더링하여 사용자 프로필 정보를 표시합니다.

6. **사용자 로그인 시도 로그**: 사용자가 로그인 시도를 할 때, 서버 콘솔에 "User {USERNAME} attempted to log in."와 같은 로그 메시지를 출력하도록 설정합니다. 이는 로그인 시도 이력을 추적하고 디버깅에 도움이 됩니다.

***

- /login 라우터는 passport.authenticate('local') 미들웨어를 사용하여 사용자의 자격을 검사하고, 이 때 Passport.js는 LocalStrategy를 사용합니다. passport.authenticate('local') 미들웨어는 내부적으로 LocalStrategy를 실행합니다. 이때, 사용자가 입력한 아이디(username)와 비밀번호(password)는 LocalStrategy로 전달됩니다. 즉, 사용자의 입력이 자동으로 LocalStrategy 내의 매개변수로 전달됩니다.

- passport.authenticate('local') 미들웨어는 다음을 수행합니다:
  - 사용자가 제출한 로그인 정보(사용자 이름과 비밀번호)를 추출합니다.
  - 이 정보를 사용하여 LocalStrategy를 실행합니다.
  - LocalStrategy에서는 데이터베이스에서 사용자 정보를 검색하고, 입력된 비밀번호와 저장된 비밀번호를 비교하여 사용자를 확인합니다.
  - 만약 사용자 정보가 일치하지 않거나 인증에 실패하면 미들웨어는 실패 상태로 처리됩니다.
  - 인증에 성공하면, 사용자 정보가 req.user 객체에 저장됩니다.
 
- LocalStrategy에서는 데이터베이스에서 사용자를 찾아보고, 입력된 비밀번호와 저장된 비밀번호를 비교하여 사용자를 인증하거나 거부합니다. 이 과정에서 콜백 함수(done)를 사용하여 Passport에 결과를 알립니다.
- 만약 인증에 성공하면 done(null, user)를 호출하여 사용자 정보(user)를 passport.authenticate에 반환하고, 이로써 인증이 성공한 것을 나타냅니다.

- 인증이 성공하든 실패하든, passport.authenticate('local') 미들웨어는 이에 따라 리디렉션을 수행합니다.
- 사용자가 리디렉션된 페이지로 이동하게 됩니다. 만약 인증에 성공했다면, 사용자는 로그인된 상태로 프로필 페이지나 다른 보호된 페이지로 이동하게 됩니다.

- 요약하면, passport.authenticate('local')는 사용자의 아이디와 비밀번호를 LocalStrategy에 전달하고, LocalStrategy는 이를 검사하여 사용자 인증을 처리합니다. 사용자가 HTML 폼에서 입력한 정보는 passport.authenticate와 LocalStrategy를 통해 자동으로 전달됩니다.

## Create New Middleware

As is, any user can just go to `/profile` whether they have authenticated or not by typing in the URL. You want to prevent this by checking if the user is authenticated first before rendering the profile page. This is the perfect example of when to create a middleware.  

현재 상태에서는 어떤 사용자라도 URL에 직접 "/profile"을 입력하면 프로필 페이지에 접근할 수 있습니다. 즉, 로그인 여부를 확인하지 않고도 누구나 프로필 페이지를 열 수 있습니다. 이렇게 하면 보안에 취약하게 됩니다.

이 문장은 이 문제를 해결하기 위해 사용자가 프로필 페이지에 액세스하기 전에 사용자가 로그인되었는지 확인하는 중요한 조치를 취해야 한다는 것을 나타냅니다. 이것을 구현하는 데 사용되는 것이 "미들웨어"입니다.  

The challenge here is creating the middleware function `ensureAuthenticated(req, res, next)`, which will check if a user is authenticated by calling Passport's `isAuthenticated` method on the `request` which checks if `req.user` is defined. If it is, then `next()` should be called. Otherwise, you can just respond to the request with a redirect to your homepage to login.

An implementation of this middleware is:
```node.js
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }
  res.redirect('/');
};
```
Create the above middleware function, then pass `ensureAuthenticated` as middleware to requests for the profile page before the argument to the GET request:
```node.js
app
 .route('/profile')
 .get(ensureAuthenticated, (req,res) => {
    res.render('profile');
 });
```
***

```node.js
'use strict';
require('dotenv').config();
const express = require('express');
const myDB = require('./connection');
const fccTesting = require('./freeCodeCamp/fcctesting.js');
const session = require('express-session');
const passport = require('passport');
const ObjectID = require('mongodb').ObjectID;
const LocalStrategy = require('passport-local');

const app = express();

app.set('view engine', 'pug');
app.set('views', './views/pug');

app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: true,
  saveUninitialized: true,
  cookie: { secure: false }
}));

app.use(passport.initialize());
app.use(passport.session());

fccTesting(app); //For FCC testing purposes
app.use('/public', express.static(process.cwd() + '/public'));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));


myDB(async client => {
  const myDataBase = await client.db('database').collection('users');

  // Be sure to change the title
  app.route('/').get((req, res) => {
    // Change the response to render the Pug template
    res.render('index', {
      title: 'Connected to Database',
      message: 'Please login',
      showLogin: true
    });
  });

  app.route('/login').post(passport.authenticate('local', { failureRedirect: '/' }), (req, res) => {
    res.redirect('/profile');
  });

  app.route('/profile').get(ensureAuthenticated, (req,res) => {
    res.render('profile');
  });

  passport.use(new LocalStrategy((username, password, done) => {
    myDataBase.findOne({ username: username }, (err, user) => {
      console.log(`User ${username} attempted to log in.`);
      if (err) return done(err);
      if (!user) return done(null, false);
      if (password !== user.password) return done(null, false);
      return done(null, user);
    });
  }));

  // Serialization and deserialization here...
  passport.serializeUser((user, done) => {
    done(null, user._id);
  });

  passport.deserializeUser((id, done) => {
    myDataBase.findOne({ _id: new ObjectID(id) }, (err, doc) => {
    done(null, doc);
    });
  });

  // Be sure to add this...
}).catch(e => {
  app.route('/').get((req, res) => {
    res.render('index', { title: e, message: 'Unable to connect to database' });
  });
});

function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }
  res.redirect('/');
};

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log('Listening on port ' + PORT);
});
```
***

1. **passport.authenticate('local', { failureRedirect: '/' })**: 이 미들웨어는 Passport.js의 authenticate 메서드를 호출하여 사용자 인증을 수행합니다. 인증에 성공하면 이 미들웨어는 다음 라우팅 핸들러로 요청을 전달하기 위해 ***내부적으로 next()를 호출***합니다. 인증에 실패한 경우, failureRedirect 옵션에 지정된 경로로 리디렉션합니다. 즉, 인증에 실패하면 이 미들웨어는 다음 라우팅 핸들러로 이동하지 않고, 대신 리디렉션을 수행합니다.

2. **function ensureAuthenticated(req, res, next)**: 이 미들웨어는 사용자가 로그인되었는지 확인하는 역할을 합니다. req.isAuthenticated() 메서드를 사용하여 현재 사용자가 인증되었는지를 확인하고, 인증되었을 경우 ***next()를 호출***하여 다음 라우팅 핸들러로 요청을 전달합니다. 이 미들웨어는 다음 라우팅 핸들러로 진행하기 전에 사용자 인증을 확인하고, 인증되지 않은 경우 리디렉션을 수행합니다.

passport.authenticate은 인증 처리에 사용되며, ensureAuthenticated는 인증 상태를 확인하는 역할을 합니다.  

***

이 코드에서 done은 콜백 함수(callback function)로 사용되는 매개변수입니다. done 함수는 Passport.js와 같은 인증 미들웨어에서 사용되며, 로그인 시도나 인증 과정의 결과를 알려주는 데 사용됩니다.  

```node.js
done(error, user, info);
```
- error: 오류가 발생한 경우 오류 객체를 전달하며, 오류가 없는 경우 null을 전달합니다.
- user: 로그인에 성공한 경우 인증된 사용자 객체를 전달하며, 로그인에 실패한 경우 false나 null을 전달합니다.
- info: 추가 정보나 메시지를 전달하는 데 사용되며, 주로 실패한 경우에 사용됩니다.
특히, done(null, user)는 로그인에 성공했음을 나타내며, done(null, false)는 로그인에 실패했음을 나타냅니다. done 함수가 호출되면 Passport.js는 이 정보를 기반으로 로그인 상태를 관리하고 다음 미들웨어나 라우트 핸들러로 이동합니다.

done 함수는 Passport.js나 다른 인증 미들웨어에서 미리 정의된 함수로, 개발자가 직접 정의할 필요가 없습니다. Passport.js 및 인증 미들웨어는 done 함수를 제공하며, 이 함수를 사용하여 인증 및 로그인 프로세스를 처리합니다.

개발자는 done 함수를 호출하여 인증 프로세스의 결과를 알립니다. done 함수에 전달하는 매개변수에 따라 Passport.js는 해당 결과를 처리하고 적절한 조치를 취하게 됩니다. 따라서 개발자가 직접 done 함수를 정의할 필요는 없으며, 미들웨어에서 제공된 done 함수를 사용하여 인증 프로세스를 완료합니다.  



## How to Put a Profile Together

Now that you can ensure the user accessing the `/profile` is authenticated, you can use the information contained in `req.user` on your page.

Pass an object containing the property `username` and value of `req.user.username` as the second argument for the `render` method of the profile view.

Then, go to your `profile.pug` view, and add the following line below the existing `h1` element, and at the same level of indentation:
```pug
h2.center#welcome Welcome, #{username}!
```
This creates an `h2` element with the class `center` and id `welcome` containing the text `Welcome, ` followed by the username.

Also, in `profile.pug`, add a link referring to the `/logout` route, which will host the logic to unauthenticate a user:
```pug
a(href='/logout') Logout
```
***
```
html
  head
    title FCC Advanced Node and Express
    meta(name='description', content='Profile')
    meta(charset='utf-8')
    meta(http-equiv='X-UA-Compatible', content='IE=edge')
    meta(name='viewport', content='width=device-width, initial-scale=1')
    link(rel='stylesheet', href='/public/style.css')
  body
    h1.border.center FCC Advanced Node and Express Profile
    h2.center#welcome Welcome, #{username}!
    a(href='/logout') Logout

```
***

```node.js
app.route('/profile').get(ensureAuthenticated, (req,res) => {
    res.render('profile', { username: req.user.username });
  });
```

## Logging a User Out

Creating the logout logic is easy. The route should just unauthenticate the user, and redirect to the home page instead of rendering any view.

In passport, unauthenticating a user is as easy as just calling `req.logout()` before redirecting. Add this `/logout` route to do that:
```node.js
app.route('/logout')
  .get((req, res) => {
    req.logout();
    res.redirect('/');
});
```
You may have noticed that you are not handling missing pages (404). The common way to handle this in Node is with the following middleware. Go ahead and add this in after all your other routes:
```node.js
app.use((req, res, next) => {
  res.status(404)
    .type('text')
    .send('Not Found');
});
```
***
```node.js
'use strict';
require('dotenv').config();
const express = require('express');
const myDB = require('./connection');
const fccTesting = require('./freeCodeCamp/fcctesting.js');
const session = require('express-session');
const passport = require('passport');
const ObjectID = require('mongodb').ObjectID;
const LocalStrategy = require('passport-local');

const app = express();

app.set('view engine', 'pug');
app.set('views', './views/pug');

app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: true,
  saveUninitialized: true,
  cookie: { secure: false }
}));

app.use(passport.initialize());
app.use(passport.session());

fccTesting(app); //For FCC testing purposes
app.use('/public', express.static(process.cwd() + '/public'));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));


myDB(async client => {
  const myDataBase = await client.db('database').collection('users');

  // Be sure to change the title
  app.route('/').get((req, res) => {
    // Change the response to render the Pug template
    res.render('index', {
      title: 'Connected to Database',
      message: 'Please login',
      showLogin: true
    });
  });

  app.route('/login').post(passport.authenticate('local', { failureRedirect: '/' }), (req, res) => {
    res.redirect('/profile');
  });

  app.route('/profile').get(ensureAuthenticated, (req,res) => {
    res.render('profile', { username: req.user.username });
  });

  app.route('/logout').get((req, res) => {
    req.logout();
    res.redirect('/');
  });

  app.use((req, res, next) => {
    res.status(404)
      .type('text')
      .send('Not Found');
  });

  passport.use(new LocalStrategy((username, password, done) => {
    myDataBase.findOne({ username: username }, (err, user) => {
      console.log(`User ${username} attempted to log in.`);
      if (err) return done(err);
      if (!user) return done(null, false);
      if (password !== user.password) return done(null, false);
      return done(null, user);
    });
  }));

  // Serialization and deserialization here...
  passport.serializeUser((user, done) => {
    done(null, user._id);
  });

  passport.deserializeUser((id, done) => {
    myDataBase.findOne({ _id: new ObjectID(id) }, (err, doc) => {
    done(null, doc);
    });
  });

  // Be sure to add this...
}).catch(e => {
  app.route('/').get((req, res) => {
    res.render('index', { title: e, message: 'Unable to connect to database' });
  });
});

function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }
  res.redirect('/');
};

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log('Listening on port ' + PORT);
});
```
***

- 먼저 정의된 라우트 및 미들웨어가 우선적으로 처리됩니다. 따라서 404 에러 미들웨어는 정의된 모든 라우트와 미들웨어가 해당 요청에 맞는 처리 방법을 찾지 못한 경우에만 실행됩니다. 404 에러 미들웨어는 마지막 수단으로, 다른 모든 라우트 및 미들웨어에서 요청을 처리하지 못한 경우에만 실행됩니다. 
- 404 에러 미들웨어는 요청된 페이지나 엔드포인트를 찾지 못한 경우 실행됩니다. 이것은 일종의 "에러 상황"을 나타내며, 이 미들웨어는 해당 에러를 처리하는 역할을 합니다.
- 따라서 이 코드는 미들웨어 스택의 가장 마지막에 추가되는 것이 일반적입니다. 이 위치에 있으면, 404 에러 미들웨어는 모든 다른 라우트 및 미들웨어를 통과한 후에만 실행되며, 클라이언트에게 "Not Found"와 같은 404 에러 메시지를 반환하게 됩니다.

***

- res.status(404): 이 부분은 HTTP 응답 상태 코드를 404로 설정하는 부분입니다. 404은 "Not Found" 상태 코드를 나타냅니다. 이것은 서버에서 요청한 리소스 또는 페이지를 찾을 수 없을 때 사용됩니다.
- .type('text'): 이 부분은 응답의 콘텐츠 타입을 설정하는 부분입니다. 여기서는 'text'로 설정되어 있으므로 응답 콘텐츠의 타입이 일반 텍스트로 지정됩니다.
- .send('Not Found'): 이 부분은 클라이언트에게 응답을 보내는 부분입니다. 'Not Found'라는 텍스트를 클라이언트로 보내고, 이것이 클라이언트에게 반환되는 내용이 됩니다.

## Registration of New Users

Now you need to allow a new user on your site to register an account. In the `res.render` for the home page add a new variable to the object passed along - `showRegistration: true`. When you refresh your page, you should then see the registration form that was already created in your `index.pug` file. This form is set up to **POST** on `/register`, so create that route and have it add the user object to the database by following the logic below.

The logic of the registration route should be as follows:

1. Register the new user
2. Authenticate the new user
3. Redirect to /profile
  
The logic of step 1 should be as follows:

1. Query database with `findOne`
2. If there is an error, call `next` with the error
3. If a user is returned, redirect back to home
4. If a user is not found and no errors occur, then `insertOne` into the database with the username and password. As long as no errors occur there, call `next` to go to step 2, authenticating the new user, which you already wrote the logic for in your `POST /login` route.

```node.js
app.route('/register')
  .post((req, res, next) => {
    myDataBase.findOne({ username: req.body.username }, (err, user) => {
      if (err) {
        next(err);
      } else if (user) {
        res.redirect('/');
      } else {
        myDataBase.insertOne({
          username: req.body.username,
          password: req.body.password
        },
          (err, doc) => {
            if (err) {
              res.redirect('/');
            } else {
              // The inserted document is held within
              // the ops property of the doc
              next(null, doc.ops[0]);
            }
          }
        )
      }
    })
  },
    passport.authenticate('local', { failureRedirect: '/' }),
    (req, res, next) => {
      res.redirect('/profile');
    }
  );
```
**NOTE**: From this point onwards, issues can arise relating to the use of the picture-in-picture browser. If you are using an online IDE which offers a preview of the app within the editor, it is recommended to open this preview in a new tab.

***

```node.js
'use strict';
require('dotenv').config();
const express = require('express');
const myDB = require('./connection');
const fccTesting = require('./freeCodeCamp/fcctesting.js');
const session = require('express-session');
const passport = require('passport');
const ObjectID = require('mongodb').ObjectID;
const LocalStrategy = require('passport-local');

const app = express();

app.set('view engine', 'pug');
app.set('views', './views/pug');

app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: true,
  saveUninitialized: true,
  cookie: { secure: false }
}));

app.use(passport.initialize());
app.use(passport.session());

fccTesting(app); //For FCC testing purposes
app.use('/public', express.static(process.cwd() + '/public'));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));


myDB(async client => {
  const myDataBase = await client.db('database').collection('users');

  // Be sure to change the title
  app.route('/').get((req, res) => {
    // Change the response to render the Pug template
    res.render('index', {
      title: 'Connected to Database',
      message: 'Please login',
      showLogin: true,
      showRegistration: true
    });
  });

  app.route('/login').post(passport.authenticate('local', { failureRedirect: '/' }), (req, res) => {
    res.redirect('/profile');
  });

  app.route('/profile').get(ensureAuthenticated, (req,res) => {
    res.render('profile', { username: req.user.username });
  });

  app.route('/logout').get((req, res) => {
    req.logout();
    res.redirect('/');
  });

  app.route('/register')
  .post((req, res, next) => {
    myDataBase.findOne({ username: req.body.username }, (err, user) => {
      if (err) {
        next(err);
      } else if (user) {
        res.redirect('/');
      } else {
        myDataBase.insertOne({
          username: req.body.username,
          password: req.body.password
        },
          (err, doc) => {
            if (err) {
              res.redirect('/');
            } else {
              // The inserted document is held within
              // the ops property of the doc
              next(null, doc.ops[0]);
            }
          }
        )
      }
    })
  },
    passport.authenticate('local', { failureRedirect: '/' }),
    (req, res, next) => {
      res.redirect('/profile');
    }
  );

  app.use((req, res, next) => {
    res.status(404)
      .type('text')
      .send('Not Found');
  });

  passport.use(new LocalStrategy((username, password, done) => {
    myDataBase.findOne({ username: username }, (err, user) => {
      console.log(`User ${username} attempted to log in.`);
      if (err) return done(err);
      if (!user) return done(null, false);
      if (password !== user.password) return done(null, false);
      return done(null, user);
    });
  }));

  // Serialization and deserialization here...
  passport.serializeUser((user, done) => {
    done(null, user._id);
  });

  passport.deserializeUser((id, done) => {
    myDataBase.findOne({ _id: new ObjectID(id) }, (err, doc) => {
    done(null, doc);
    });
  });

  // Be sure to add this...
}).catch(e => {
  app.route('/').get((req, res) => {
    res.render('index', { title: e, message: 'Unable to connect to database' });
  });
});

function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }
  res.redirect('/');
};

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log('Listening on port ' + PORT);
});
```
***

- passport.authenticate('local', { failureRedirect: '/' }): Passport.js를 사용하여 사용자를 로그인합니다. 이 미들웨어는 사용자가 입력한 로그인 정보를 확인하고, 로그인이 실패하면 홈 페이지로 리디렉션합니다.
- passport.authenticate('local', { failureRedirect: '/' })의 다음 미들웨어는 로그인이 성공하면 실행됩니다.
- 요약하면, 사용자가 등록 페이지에서 POST 요청을 보내면, Express 애플리케이션은 사용자 이름이 이미 데이터베이스에 있는지 확인하고, 이미 등록된 경우 홈 페이지로 리디렉션하거나, 등록되지 않은 경우 사용자 정보를 데이터베이스에 추가한 다음, 로그인을 수행하고 로그인이 성공하면 프로필 페이지로 리디렉션합니다.

