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



