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
After that, add another `set` method that sets the `views` property of your `app` to point to the `./views/pug` directory. This tells Express to render all views relative to that directory.

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

***

app.set 메서드는 Express.js 애플리케이션 설정을 위한 메서드입니다. 이 메서드를 사용하여 애플리케이션 레벨 설정을 구성합니다. 예를 들어, app.set('view engine', 'pug')와 같이 사용하여 Express에게 특정 설정을 알립니다.

일반적으로 사용하는 몇 가지 app.set 설정은 다음과 같습니다:
- view engine: 템플릿 엔진을 설정합니다. 예를 들어, app.set('view engine', 'pug')는 Pug를 템플릿 엔진으로 사용하겠다고 설정하는 것입니다.
- views: 템플릿 파일들이 위치한 디렉토리를 설정합니다. app.set('views', 'views')와 같이 사용하여 Express에게 템플릿 파일들이 "views" 디렉토리에 있다고 알립니다.
- port: 웹 서버의 포트 번호를 설정합니다. app.set('port', 3000)와 같이 사용하여 서버가 3000번 포트에서 실행되도록 설정할 수 있습니다.
- 기타 설정: Express 애플리케이션에서 필요한 다양한 설정을 app.set을 통해 구성할 수 있습니다. 이러한 설정은 애플리케이션의 동작과 구성을 제어하는 데 사용됩니다.

요약하면, app.set은 Express.js 애플리케이션의 설정을 구성하는 데 사용되며, 이러한 설정은 전체 애플리케이션에 영향을 미칩니다.  

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

브라우저에 쿠키를 저장한 후 그 해당 웹사이트를 방문할 때마다 브라우저는 해당 쿠키도 요쳥과 함께 보낸다. 쿠키는 인증 뿐만 아니라 여러 가지 정보를 저장할 수 있다. 만약 웹사이트 언어 설정을 바꾸면 서버는 쿠키를 주고, 바꾼 언어를 저장한다.  

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

