# Advanced Node and Express3
## Set up the Environment

The following challenges will make use of the `chat.pug` file. So, in your `routes.js` file, add a GET route pointing to `/chat` which makes use of `ensureAuthenticated`, and renders `chat.pug`, with `{ user: req.user }` passed as an argument to the response. Now, alter your existing `/auth/github/callback` route to set the `req.session.user_id = req.user.id` and redirect to `/chat`.(**serializeUser 함수가 있는데 왜 이 코드를 작성해야 하는지?**)

`socket.io@~2.3.0` has already been added as a dependency, so require/instantiate it in your server as follows with `http` (comes built-in with Nodejs):  

```node.js
const http = require('http').createServer(app);
const io = require('socket.io')(http);
```

**`require('http').createServer(app)`:**
- createServer 메서드를 사용하여 웹 서버를 생성합니다.
- createServer는 새로운 HTTP 서버 객체를 생성합니다.
- app은 Express.js 애플리케이션 객체입니다. 이 애플리케이션은 웹 서버에서 동작하는 응용 프로그램을 의미합니다.

**`const http = ...`:**
- 이렇게 생성된 HTTP 서버 객체를 http 상수에 할당합니다.
- 이제 이 http 객체를 사용하여 웹 서버를 제어하고 설정할 수 있습니다.

Now that the http server is mounted on the express app, you need to listen from the *http* server. Change the line with `app.listen` to `http.listen`.

The first thing needing to be handled is listening for a new connection from the client. The on keyword does just that- listen for a specific event. It requires 2 arguments: a string containing the title of the event that's emitted, and a function with which the data is passed through. In the case of our connection listener, use `socket` to define the data in the second argument. A socket is an individual client who is connected.

To listen for connections to your server, add the following within your database connection:
```node.js
io.on('connection', socket => {
  console.log('A user has connected');
});
```
Now for the client to connect, you just need to add the following to your `client.js` which is loaded by the page after you've authenticated:
```node.js
/*global io*/
let socket = io();
```
The comment suppresses the error you would normally see since 'io' is not defined in the file. You have already added a reliable CDN to the Socket.IO library on the page in `chat.pug`.

Now try loading up your app and authenticate and you should see in your server console `A user has connected`.

Note:`io()` works only when connecting to a socket hosted on the same url/server. For connecting to an external socket hosted elsewhere, you would use `io.connect('URL');`.

***

1. http: Node.js의 http 모듈을 사용하여 HTTP 서버를 생성하는 코드입니다. app은 Express.js 애플리케이션 객체로 가정됩니다. 이 코드는 **Express 애플리케이션을 HTTP 서버에 연결**하고 있습니다.
2. io: Socket.IO를 사용하여 WebSocket 통신을 설정하고 있습니다. Socket.IO를 사용하면 실시간 양방향 통신을 지원할 수 있습니다.

기존 코드에서 `app.listen`을 사용하면 Express 애플리케이션을 포트에 직접 바인딩하고 웹 서버를 시작하는 것이 일반적입니다. 하지만 이 코드에서는 이미 http를 통해 HTTP 서버를 만들었기 때문에 이 서버를 사용하여 리스닝해야 합니다. 이 변경을 통해 Express 애플리케이션은 http 서버를 통해 요청을 처리하게 되고, **Socket.IO 역시 이 서버를 통해 소켓 통신**을 할 수 있게 됩니다. 

Express 애플리케이션을 HTTP 서버를 통해 처리하는 이유는 일반적으로 Express.js 애플리케이션은 HTTP 서버 위에서 동작하도록 설계되어 있기 때문입니다.

기존에 `app.listen`를 사용하는 것은 *Express 애플리케이션을 직접 웹 서버로 만들어 요청을 처리*하는 방식입니다. 이렇게 하면 Express 애플리케이션은 *내부적으로 Node.js의 http 모듈을 사용*하여 서버를 만들고, 요청을 라우팅하며, 미들웨어를 적용합니다. 이것은 간단한 웹 애플리케이션을 개발할 때 편리한 방법입니다.

그러나 Socket.IO와 같은 웹소켓 라이브러리를 사용할 때, **Express의 HTTP 서버에 직접적으로 연결하면 소켓 통신과 HTTP 요청을 모두 하나의 서버에서 처리할 수 있습니다. 이렇게하면 소켓과 HTTP 모두 동일한 포트와 도메인에서 작동하여 연결 및 보안 관리가 용이하며, 코드가 단순화됩니다.**

따라서 http 서버를 만들고 Express 애플리케이션을 이 서버에 연결하는 것은 Express 애플리케이션과 Socket.IO를 함께 사용하는 경우 일반적인 방법입니다. 이렇게 하면 Express 애플리케이션과 소켓 통신을 하나의 서버에서 처리할 수 있으며, http.listen를 사용하여 이 서버를 시작합니다. 이렇게 하면 Express 애플리케이션과 Socket.IO 모두가 동일한 HTTP 서버를 공유하게 되어 더 효율적으로 동작합니다.

***

HTTP는 클라이언트와 서버의 연결을 유지하지 않는 비연결성이라는 특징이 있다. 또한 연결 설정이 클라이언트에서 서버로 요청이 발생할 때만 이뤄지므로 요청 없이 서버에서 응답을 보낼 수 없다. 이러한 문제를 해결하기 위해 개발된 프로토콜이 웹소켓이다. HTTP처럼 TCP/IP 4계층 중 응용 계층에서 동작하지만, HTTP와 달리 한 번 연결되면 끊어지지 않으며 클라이언트의 요청이 없어도 서버에서 응답을 보낼 수 있다. 웹 소켓의 초기 연결 설정은 HTTP로 한다. 즉 웹 소켓은 양방향 통신을 제공하는 프로토콜로, 서버와 클라이언트 간에 실시간으로 데이터를 주고 받을 수 있게 해줍니다. 이는 채팅 응용 프로그램, 실시간 게임, 협업 애플리케이션 등에서 유용하게 사용됩니다.  

***

[코딩애플](https://youtu.be/yXPCg5eupGM?feature=shared)  
[노마드코더](https://youtu.be/5EhsjtBE7I4?feature=shared)
***

가장 먼저 처리해야 하는 것은 클라이언트로부터의 새로운 연결을 기다리는 것입니다. 'on' 키워드는 정확히 그 역할을 합니다 - 특정 이벤트를 기다립니다. 이 키워드는 2개의 인수가 필요합니다: 이벤트의 제목을 포함한 문자열과 데이터가 전달되는 함수입니다. 우리의 연결 리스너의 경우, 두 번째 인수에서 데이터를 정의하는 데 'socket'을 사용합니다. **'소켓'은 연결된 개별 클라이언트**를 나타냅니다.  

```node.js
io.on('connection', socket => {
  console.log('A user has connected');
});
```
이 부분은 웹 소켓을 사용하여 서버와 클라이언트 간의 실시간 통신을 설정하는 과정 중 하나입니다. 코드는 서버에서 웹 소켓 연결을 기다리며, 클라이언트가 서버에 연결하면 'connection' 이벤트가 발생하고, 연결된 개별 클라이언트를 나타내는 'socket' 객체가 생성됩니다.

이 코드는 클라이언트가 서버에 연결되었을 때 "A user has connected"라는 메시지를 서버 콘솔에 출력합니다. 이것은 클라이언트와 서버 간의 연결이 수립되었음을 나타내며, 이 이후에 클라이언트와 서버 사이의 양방향 통신을 처리하는 코드를 추가할 수 있게 합니다. 이것은 실시간 채팅, 실시간 업데이트, 멀티플레이어 게임과 같이 실시간 특성을 필요로 하는 애플리케이션에서 중요한 단계입니다.  

***

```node.js
/*global io*/
let socket = io();
```
이 코드 조각은 클라이언트 측 JavaScript에서 Socket.IO를 사용하는 부분입니다. 주석 /*global io*/는 'io'라는 변수가 파일 내에서 정의되지 않았을 때 발생하는 오류를 억제합니다. 이 주석을 통해 에디터나 개발 도구에서 오류 표시를 피할 수 있습니다.

그런 다음, let socket = io();라는 코드는 클라이언트 측에서 소켓 연결을 설정하는 부분입니다. 'io()' 함수는 Socket.IO를 초기화하여 서버로부터 웹 소켓 연결을 수립하고, 'socket' 변수에 이 연결을 할당합니다.

이를 통해 클라이언트와 서버 간의 양방향 통신을 구현할 수 있으며, 서버에 연결하려면 'A user has connected'와 같은 메시지를 서버 콘솔에 로깅하는 것이 가능해집니다. 이것은 웹 애플리케이션에서 실시간 기능을 구현할 때 중요한 부분입니다.  

참고: 'io()'는 동일한 URL/서버에 호스팅된 소켓에 연결할 때만 작동합니다. 다른 곳에서 호스팅된 외부 소켓에 연결하려면 'io.connect('URL')'을 사용해야 합니다.  

let socket = io(); 코드는 클라이언트 측에서 Socket.IO를 사용하여 소켓 연결을 설정하는 부분입니다.

Socket.IO는 서버와 클라이언트 간의 양방향 통신을 위한 라이브러리이며, 이 코드는 클라이언트 측에서 Socket.IO 클라이언트를 초기화하여 서버와의 소켓 연결을 확립하는 역할을 합니다.

간단히 설명하면:

1. io() 함수는 Socket.IO 클라이언트를 초기화하고 서버에 연결을 시도합니다.
2. 이렇게 생성된 socket 객체는 서버와의 통신에 사용됩니다. 클라이언트는 이 socket 객체를 사용하여 서버로 데이터를 보낼 수 있고, 서버에서 보내는 데이터를 수신할 수 있습니다.

클라이언트 측에서 Socket.IO를 사용하면 실시간으로 데이터를 서버와 주고받을 수 있으며, 웹 애플리케이션에서 실시간 업데이트나 채팅과 같은 기능을 구현하는 데 유용합니다. 이 코드는 Socket.IO를 초기화하고 연결을 설정하는 첫 단계입니다.

***

```html
// chat.pug

<!DOCTYPE html>
<html>
<head>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.5.1/jquery.min.js" integrity="sha256-9/aliU8dGd2tb6OSsuzixeV4y/faTqgFtohetphbbj0=" crossorigin="anonymous"></script> // 제이쿼리 라이브러리 로드
</head>
<body>
    <script src="/socket.io/socket.io.js"></script> // Socket.IO 클라이언트 라이브러리 로드
    <script async defer src="/public/client.js"></script> //  클라이언트 JavaScript 파일
</body>
</html>
```

`<script src="/socket.io/socket.io.js"></script>` 코드는 웹 브라우저에서 직접 실행되는 클라이언트 측의 Socket.IO 라이브러리를 로드하는 코드입니다.  

이 스크립트 파일은 일반적으로 Socket.IO를 사용하기 위해 Node.js 서버에서 서비스되며, 클라이언트 측에서는 웹 브라우저를 통해 이 파일을 다운로드합니다. 따라서 npm install socket.io와 같이 Node.js 서버에서 Socket.IO를 설치했다면, 이 스크립트 파일을 서버가 자동으로 제공합니다.

웹 페이지에서 Socket.IO 클라이언트 라이브러리를 사용하려면, 먼저 Node.js 서버를 실행하고 해당 서버에서 클라이언트에게 /socket.io/socket.io.js 스크립트를 제공해야 합니다. 이러한 스크립트를 사용하려면 서버 백엔드 코드에서 Socket.IO를 설정하고 클라이언트에게 제공하는 것이 일반적입니다.  

일반적으로 io는 JavaScript 코드에서 내장된 전역 객체가 아닙니다. io는 주로 Socket.IO 라이브러리에서 사용되는 변수로, Socket.IO를 사용하는 경우에는 이 변수를 정의하고 사용하는 것이 일반적입니다. 이 변수는 Socket.IO 클라이언트 라이브러리에서 제공하며, 클라이언트 측에서 서버와의 소켓 통신을 설정할 때 사용됩니다.
***

**client.js**
jquery버전
```javascript.js
$(document).ready(function () {
  /*global io*/
  let socket = io();
  
  // Form submittion with new message in field with id 'm'
  $('form').submit(function () {
    var messageToSend = $('#m').val();

    $('#m').val('');
    return false; // prevent form submit from refreshing page
  });
});
```
javascript버전
```node.js
/*global io*/
        let socket = io();

        document.getElementById('send').addEventListener('click', function () {
            var messageToSend = document.getElementById('m').value;
            document.getElementById('m').value = '';
            socket.emit('chat message', messageToSend);
        });

        socket.on('chat message', function (message) {
            var li = document.createElement('li');
            li.textContent = message;
            document.getElementById('messages').appendChild(li);
        });
```

**server.js**
```node.js
'use strict';
require('dotenv').config();
const express = require('express');
const myDB = require('./connection');
const fccTesting = require('./freeCodeCamp/fcctesting.js');
const session = require('express-session');
const passport = require('passport');
const routes = require('./routes.js');
const auth = require('./auth.js');

const app = express();

const http = require('http').createServer(app);
const io = require('socket.io')(http);

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

fccTesting(app); // For fCC testing purposes
app.use('/public', express.static(process.cwd() + '/public'));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

myDB(async client => {
  const myDataBase = await client.db('database').collection('users');

  routes(app, myDataBase);
  auth(app, myDataBase);

  io.on('connection', socket => {
    console.log('A user has connected');
  });
  
}).catch(e => {
  app.route('/').get((req, res) => {
    res.render('index', { title: e, message: 'Unable to connect to database' });
  });
});
  
const PORT = process.env.PORT || 3000;
http.listen(PORT, () => {
  console.log(`Listening on port ${PORT}`);
});
```

**routes.js**
```node.js
const passport = require('passport');
const bcrypt = require('bcrypt');

module.exports = function (app, myDataBase) {
  app.route('/').get((req, res) => {
    res.render('index', {
      title: 'Connected to Database',
      message: 'Please log in',
      showLogin: true,
      showRegistration: true,
      showSocialAuth: true
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

  app.route('/register').post((req, res, next) => {
    const hash = bcrypt.hashSync(req.body.password, 12);
    myDataBase.findOne({ username: req.body.username }, (err, user) => {
      if (err) {
        next(err);
      } else if (user) {
        res.redirect('/');
      } else {
        myDataBase.insertOne({
          username: req.body.username,
          password: hash
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

  app.route('/auth/github').get(passport.authenticate('github'));
  app.route('/auth/github/callback').get(passport.authenticate('github', { failureRedirect: '/' }), (req, res) => {
    req.session.user_id = req.user.id;
    res.redirect("/chat");
  })

  app.use((req, res, next) => {
    res.status(404)
      .type('text')
      .send('Not Found');
  });
}

function ensureAuthenticated(req, res, next) {
    if (req.isAuthenticated()) {
        return next();
    }
    res.redirect('/');
};
```

## Communicate by Emitting

Emit is the most common way of communicating you will use. When you emit something from the server to 'io', you send an event's name and data to **all the connected sockets.** A good example of this concept would be emitting the current count of connected users **each time a new user connects**!

Start by adding a variable to keep track of the users, just before where you are currently listening for connections.
```node.js
let currentUsers = 0;
```
Now, when someone connects, you should increment the count before emitting the count. So, you will want to add the incrementer within the connection listener.
```node.js
++currentUsers;
```
Finally, after incrementing the count, you should emit the event (still within the connection listener). The event should be named 'user count', and the data should just be the `currentUsers`.
```node.js
io.emit('user count', currentUsers);
```
Now, you can implement a way for your client to listen for this event! Similar to listening for a connection on the server, you will use the `on` keyword.
```node.js
socket.on('user count', function(data) {
  console.log(data);
});
```
Now, try loading up your app, authenticate, and you should see in your client console '1' representing the current user count! Try loading more clients up, and authenticating to see the number go up.  

***

"Emit"은 서버에서 'io'로 무언가를 전달하는 가장 일반적인 통신 방법 중 하나입니다. 서버에서 'io'로 무언가를 발생시킬 때, 이벤트의 이름과 데이터를 모든 연결된 소켓에 보냅니다. 이 개념을 설명하는 좋은 예시는 새로운 사용자가 연결될 때마다 현재 연결된 사용자 수를 보내는 것입니다.

이렇게 하면 모든 클라이언트(소켓)가 서버에서 발생한 특정 이벤트를 듣고, 그 이벤트에 연결된 데이터를 받게 됩니다. 예를 들어, 사용자가 웹 애플리케이션에 접속하면 서버는 이를 감지하고 모든 연결된 클라이언트에게 "새로운 사용자가 연결되었습니다"라는 이벤트와 연결된 데이터(연결된 사용자 수)를 보낼 수 있습니다.

이것은 실시간 알림, 채팅 애플리케이션에서 메시지 전송, 게임에서 이벤트 전달 등 다양한 용도로 사용됩니다. 각 클라이언트는 서버가 발생시킨 이벤트에 응답하여 상호작용하거나 데이터를 업데이트할 수 있습니다. 이렇게 함으로써 서버와 클라이언트 간에 실시간으로 데이터를 주고받을 수 있습니다.  

***

사용자 수를 증가시킨 후, 'user count'라는 이벤트를 발생시켜야 합니다. 서버에서 클라이언트로 이 사용자 수를 보냅니다. 여전히 연결 이벤트 처리기 내부에 있어야 합니다.  
```node.js
io.emit('user count', currentUsers);
```
이제 클라이언트에서 이 이벤트를 수신할 수 있도록 구현합니다. 서버에서 발생한 'user count' 이벤트를 듣기 위해 on 키워드를 사용합니다. 클라이언트에서는 이 이벤트를 수신하여 사용자 수를 표시합니다.
```node.js
socket.on('user count', function(data) {
  console.log(data);
});
```
이제 앱을 실행하고 인증한 후, 클라이언트 콘솔에 현재 사용자 수를 나타내는 '1'을 볼 수 있을 것입니다. 더 많은 클라이언트를 로드하고 인증하면 숫자가 증가하는 것을 확인할 수 있습니다. 이것은 사용자가 연결 및 로그인할 때 실시간으로 사용자 수를 업데이트하고 모든 클라이언트에 표시하는 간단한 방법입니다.  

***

**client.js**
```node.js
$(document).ready(function () {
  /*global io*/
  let socket = io();

  socket.on('user count', function (data) {
    console.log(data);
  });
  
  // Form submittion with new message in field with id 'm'
  $('form').submit(function () {
    var messageToSend = $('#m').val();

    $('#m').val('');
    return false; // prevent form submit from refreshing page
  });
});
```

**server.js**
```node.js
myDB(async client => {
  const myDataBase = await client.db('database').collection('users');

  routes(app, myDataBase);
  auth(app, myDataBase);

  let currentUsers = 0;
  io.on('connection', (socket) => {
    ++currentUsers;
    io.emit('user count', currentUsers);
    console.log('A user has connected');
  });
```

## Handle a Disconnect

You may notice that up to now you have only been increasing the user count. Handling a user disconnecting is just as easy as handling the initial connect, except you have to listen for it on each socket instead of on the whole server.

To do this, add another listener inside the existing `'connect'` listener that listens for `'disconnect'` on the socket with no data passed through. You can test this functionality by just logging that a user has disconnected to the console.
```node.js
socket.on('disconnect', () => {
  /*anything you want to do on disconnect*/
});
```
To make sure clients continuously have the updated count of current users, you should decrease `currentUsers` by 1 when the disconnect happens then emit the `'user count'` event with the updated count.

Note: Just like `'disconnect'`, all other events that a socket can emit to the server should be handled within the connecting listener where we have 'socket' defined.

***

현재까지 사용자 수를 증가시키는 방법만 다루었습니다. 사용자가 연결을 해제하는 것을 다루는 것은 처음 연결을 다루는 것만큼 간단합니다. 다만 연결을 관리하는 것은 서버 전체가 아니라 각 소켓에서 처리해야 합니다.

이를 수행하려면 기존 'connect' 리스너 내에 'disconnect'를 소켓에서 듣도록 추가합니다. 데이터는 전달되지 않기 때문에 소켓에서 'disconnect'를 듣도록 합니다. 이 기능을 테스트하려면 사용자가 연결을 해제했음을 콘솔에 기록하는 것으로 충분합니다.  

사용자가 연결을 해제할 때, 사용자 수를 1 감소시킨 다음 업데이트된 수를 가진 'user count' 이벤트를 발생시켜야 합니다.

참고: 'disconnect'와 마찬가지로 소켓이 서버로 전송할 수 있는 다른 모든 이벤트는 'socket'이 정의된 연결 리스너 내에서 처리해야 합니다. 이것은 연결된 클라이언트가 연결을 해제하거나 다른 이벤트를 트리거하는 경우에 사용자 수를 업데이트하고 모든 클라이언트에게 이를 표시하기 위한 중요한 기능입니다.  

'connect' 리스너 내에서 'socket'이라는 개별 소켓을 정의합니다. 이 소켓은 개별 클라이언트와 연결된 것을 나타냅니다. 이것은 연결된 클라이언트의 모든 활동을 추적하고 처리하는 방법입니다.

'connect' 리스너 내에서 정의된 'socket'은 해당 클라이언트와 관련된 모든 이벤트를 처리합니다. 즉, 연결이 설정되면 클라이언트에서 발생하는 모든 이벤트를 이 'socket'을 통해 처리할 수 있습니다. 이것은 클라이언트가 연결을 해제하거나 다른 이벤트를 트리거하는 경우 사용자 수를 업데이트하고 모든 클라이언트에게 이 정보를 표시하기 위한 중요한 방법입니다.

따라서 'disconnect' 뿐만 아니라 모든 관련 이벤트를 'socket' 리스너 내에서 처리하여 클라이언트와 서버 간의 효과적인 통신 및 데이터 업데이트를 보장합니다.  

***

```node.js
// 클라이언트 측에서 소켓 연결 종료
document.getElementById('logout-button').addEventListener('click', function () {
  socket.disconnect(); // 소켓 연결 종료
});
```

***
**server.js**
```node.js
myDB(async client => {
  const myDataBase = await client.db('database').collection('users');

  routes(app, myDataBase);
  auth(app, myDataBase);

  let currentUsers = 0;
  io.on('connection', (socket) => {
    ++currentUsers;
    io.emit('user count', currentUsers);
    console.log('A user has connected');

    socket.on('disconnect', () => {
      console.log('A user has disconnected');
      --currentUsers;
      io.emit('user count', currentUsers);
    });
  });
...
```

## Authentication with Socket.IO
Currently, you cannot determine who is connected to your web socket. While `req.user` contains the user object, that's only when your user interacts with the web server, and with **web sockets you have no `req` (request) and therefore no user data.** One way to solve the problem of knowing who is connected to your web socket is by parsing and decoding the **cookie** that contains the passport session then deserializing it to obtain the user object. Luckily, there is a package on NPM just for this that turns a once complex task into something simple!

`passport.socketio@~3.7.0`, `connect-mongo@~3.2.0`, and `cookie-parser@~1.4.5` have already been added as dependencies. Require them as `passportSocketIo`, `MongoStore`, and `cookieParser` respectively. Also, we need to initialize a new memory store, from `express-session` which we previously required. It should look like this:
```node.js
const MongoStore = require('connect-mongo')(session);
const URI = process.env.MONGO_URI;
const store = new MongoStore({ url: URI });
```
Now we just have to tell Socket.IO to use it and set the options. Be sure this is added before the existing socket code and not in the existing connection listener. For your server, it should look like this:

```node.js
io.use(
  passportSocketIo.authorize({
    cookieParser: cookieParser,
    key: 'express.sid',
    secret: process.env.SESSION_SECRET,
    store: store,
    success: onAuthorizeSuccess,
    fail: onAuthorizeFail
  })
);
```
***

기본적으로 Express.js는 세션을 메모리에 저장하며, serializeUser에서 설정한 정보는 메모리에 저장된 세션에 반영됩니다. 그러나 이것은 보안적으로 안전하지 않을 수 있고, 서버가 재시작될 때 세션 정보가 손실될 수 있습니다. 따라서 connect-mongo와 같은 외부 모듈을 사용하여 세션 데이터를 데이터베이스에 저장함으로써, 세션 정보를 영속적으로 유지하고 서버 재시작에도 안전하게 대응할 수 있습니다. 이렇게 하면 사용자가 로그인한 상태가 서버 재시작 후에도 유지됩니다.

***

1. **const MongoStore = require('connect-mongo')(session);**: 이 코드는 세션 데이터를 MongoDB에 저장하는데 사용되는 connect-mongo 모듈을 가져옵니다. connect-mongo 모듈은 Express 애플리케이션의 세션 데이터를 MongoDB에 지속적으로 저장하고 검색하는 데 도움이 됩니다.
2. **const URI = process.env.MONGO_URI;**: MongoDB 서버의 연결 문자열(URI)를 환경 변수로부터 가져옵니다. 이 URI를 사용하여 MongoDB에 연결하고 세션 데이터를 저장합니다.
3. **const store = new MongoStore({ url: URI });**: connect-mongo를 사용하여 MongoDB에 세션 데이터를 저장할 수 있도록 MongoStore 인스턴스를 생성합니다. url 옵션을 통해 MongoDB 서버에 연결할 URI를 지정합니다.
4. **io.use(...)**: Socket.IO에서 미들웨어를 사용하여 요청 또는 연결을 처리할 수 있습니다. 이 부분은 *Passport와 Socket.IO를 연동*하기 위한 미들웨어를 설정하는 부분입니다.
5. **passportSocketIo.authorize({ ... })**: passportSocketIo 모듈을 사용하여 *Socket.IO의 인증을 처리*합니다. 이 부분에서는 인증 관련 설정을 제공하며, 이를 통해 **세션 쿠키를 해석하고 사용자 인증을 수행**합니다.
  - cookieParser: 세션 쿠키를 파싱하기 위해 사용되는 미들웨어.
  - key: 세션 식별자 키. Express 세션 설정과 일치해야 합니다.
  - secret: 세션 데이터를 암호화 및 복호화하는 데 사용되는 비밀 키.
  - store: 세션 데이터를 저장하기 위한 스토어 (MongoDB에 저장).
  - success: 인증이 성공했을 때 호출되는 콜백 함수.
  - fail: 인증이 실패했을 때 호출되는 콜백 함수.
  
이렇게 설정된 미들웨어를 사용하면 Socket.IO 연결 요청을 받을 때 사용자의 세션을 확인하고, 세션 정보를 통해 사용자를 인증하고 관련된 데이터를 가져올 수 있게 됩니다. Socket.IO를 통해 인증된 사용자와 관련된 작업을 수행할 수 있게 됩니다.

***

Note that configuring Passport authentication for Socket.IO is very similar to the way we configured the `session` middleware for the API. This is because they are meant to use the same authentication method — get the session id from a cookie and validate it.

Previously, when we configured the `session` middleware, we didn't explicitly set the cookie name for session (`key`). This is because the `session` package was using the default value. Now that we've added another package which needs access to the same value from the cookies, we need to explicitly set the `key` value in both configuration objects.

Be sure to add the `key` with the cookie name to the `session` middleware that matches the Socket.IO key. Also, add the `store` reference to the options, near where we set `saveUninitialized: true`. This is necessary to tell Socket.IO which session to relate to.

Now, define the success, and fail callback functions:
```node.js
function onAuthorizeSuccess(data, accept) {
  console.log('successful connection to socket.io');

  accept(null, true);
}

function onAuthorizeFail(data, message, error, accept) {
  if (error) throw new Error(message);
  console.log('failed connection to socket.io:', message);
  accept(null, false);
}
```
The user object is now accessible on your socket object as `socket.request.user`. For example, now you can add the following:
```node.js
console.log('user ' + socket.request.user.username + ' connected');
```
It will log to the server console who has connected!

***

1. 이 코드에서는 Socket.IO와 Express 세션을 같은 방식으로 인증을 처리하도록 설정하고자 합니다. 즉, 세션 ID를 쿠키에서 가져와서 인증을 수행하는 방식을 사용합니다.
2. 이전에 세션 미들웨어를 설정할 때 세션의 쿠키 이름을 별도로 지정하지 않았기 때문에, 기본값을 사용했을 것입니다. 이제 같은 세션을 사용하는 Socket.IO에서도 세션 쿠키 이름을 설정해야 합니다.
3. key 값은 세션 쿠키의 이름을 나타내며, Socket.IO 및 세션 설정에서 동일한 값을 사용해야 합니다. 이렇게 함으로써 Socket.IO와 Express 세션이 동일한 세션을 공유할 수 있습니다.
4. store 참조는 세션 데이터를 저장하는 데 사용되며, Socket.IO가 어떤 세션을 사용할지 알 수 있도록 설정에 포함되어야 합니다.
5. onAuthorizeSuccess 및 onAuthorizeFail 함수는 Socket.IO의 인증에 성공하거나 실패할 때 호출되는 콜백 함수입니다. 인증에 성공하면 사용자가 Socket.IO 서버에 연결할 수 있고, 실패하면 연결이 거부됩니다. 이러한 콜백 함수를 정의하여 사용자의 연결 상태를 관리하고 로그 정보를 출력할 수 있습니다.
6. accept(null, true): 이 코드는 연결 요청을 승인합니다. 즉, 클라이언트와의 소켓 연결을 허용하고 연결을 설정합니다. 이 경우, 연결이 성공했음을 의미하며 onAuthorizeSuccess 콜백 함수 내에서 사용됩니다.
7. accept(null, false): 이 코드는 연결 요청을 거부합니다. 즉, 클라이언트와의 소켓 연결을 거부하고 연결을 설정하지 않습니다. 이 경우, 연결이 실패했음을 의미하며 onAuthorizeFail 콜백 함수 내에서 사용됩니다. 이때, 연결이 실패한 이유와 관련된 메시지가 message 매개변수를 통해 제공됩니다.

***

`done(null, user._id)`는 Passport의 `serializeUser` 함수 내에서 사용자 ID를 세션에 저장하는 역할을 합니다. 이 함수에서 사용자 ID를 반환하면 `express-session` 및 연결된 세션 스토어가 세션 데이터베이스에 사용자 ID를 저장합니다. `done(null, user._id)`가 호출된 이후, 세션 데이터베이스에 사용자 ID가 알아서 저장됩니다.

세션 데이터베이스에 사용자 ID를 저장하는 구체적인 로직 및 동작은 세션 스토어(예: `connect-mongo`, `connect-redis`, `connect-mysql` 등)에 의해 처리됩니다. 각 스토어는 데이터베이스와의 연결 및 데이터 저장을 다루는 기능을 제공하며, 세션 데이터를 실제로 저장 및 관리합니다.

Passport의 `serializeUser` 함수는 사용자 ID를 세션에 저장하는 역할만 하며, 어떻게 저장되는지는 세션 스토어에 따라 다를 수 있습니다. 세션 스토어 설정을 통해 어떤 데이터베이스를 사용할지 및 어떻게 저장할지를 결정하게 됩니다.  

(**뇌피셜**: serializeUser는 세션 DB에 세션 ID를 저장하고, deserializeUser함수는 기본 데이터베이스에서 세션 ID를 통해 검색한 사용자 객체를 반환한다. 즉 둘이 사용하는 데이터베이스가 다르다!)

***

**server.js**
```node.js
'use strict';
require('dotenv').config();
const express = require('express');
const myDB = require('./connection');
const fccTesting = require('./freeCodeCamp/fcctesting.js');
const session = require('express-session');
const passport = require('passport');
const routes = require('./routes.js');
const auth = require('./auth.js');

const app = express();

const http = require('http').createServer(app);
const io = require('socket.io')(http);
const passportSocketIo = require('passport.socketio');
const cookieParser = require('cookie-parser');
const MongoStore = require('connect-mongo')(session);
const URI = process.env.MONGO_URI;
const store = new MongoStore({ url: URI });

app.set('view engine', 'pug');
app.set('views', './views/pug');

app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: true,
  saveUninitialized: true,
  cookie: { secure: false },
  key: 'express.sid',
  store: store
}));

app.use(passport.initialize());
app.use(passport.session());

fccTesting(app); // For fCC testing purposes
app.use('/public', express.static(process.cwd() + '/public'));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

io.use(
  passportSocketIo.authorize({
    cookieParser: cookieParser,
    key: 'express.sid',
    secret: process.env.SESSION_SECRET,
    store: store,
    success: onAuthorizeSuccess,
    fail: onAuthorizeFail
  })
);

myDB(async client => {
  const myDataBase = await client.db('database').collection('users');

  routes(app, myDataBase);
  auth(app, myDataBase);

  let currentUsers = 0;
  io.on('connection', (socket) => {
    ++currentUsers;
    io.emit('user count', currentUsers);
    console.log('A user has connected');

    socket.on('disconnect', () => {
      console.log('A user has disconnected');
      --currentUsers;
      io.emit('user count', currentUsers);
    });
  });
  
}).catch(e => {
  app.route('/').get((req, res) => {
    res.render('index', { title: e, message: 'Unable to connect to database' });
  });
});

function onAuthorizeSuccess(data, accept) {
  console.log('successful connection to socket.io');

  accept(null, true);
}

function onAuthorizeFail(data, message, error, accept) {
  if (error) throw new Error(message);
  console.log('failed connection to socket.io:', message);
  accept(null, false);
}
  
const PORT = process.env.PORT || 3000;
http.listen(PORT, () => {
  console.log(`Listening on port ${PORT}`);
});
```
## Announce New Users

Many chat rooms are able to announce when a user connects or disconnects and then display that to all of the connected users in the chat. Seeing as though you already are emitting an event on connect and disconnect, you will just have to modify this event to support such a feature. The most logical way of doing so is sending 3 pieces of data with the event: the username of the user who connected/disconnected, the current user count, and if that username connected or disconnected.

Change the event name to `'user'`, and pass an object along containing the fields `username`, `currentUsers`, and `connected` (to be `true` in case of connection, or `false` for disconnection of the user sent). Be sure to change both `'user count'` events and set the disconnect one to send `false` for the field `connected` instead of `true` like the event emitted on connect.
```node.js
io.emit('user', {
  username: socket.request.user.username,
  currentUsers,
  connected: true
});
```
Now your client will have all the necessary information to correctly display the current user count and announce when a user connects or disconnects! To handle this event on the client side we should listen for `'user'`, then update the current user count by using jQuery to change the text of `#num-users` to `'{NUMBER} users online'`, as well as append a `<li>` to the unordered list with id `messages` with `'{NAME} has {joined/left} the chat.'`.

An implementation of this could look like the following:
```node.js
socket.on('user', data => {
  $('#num-users').text(data.currentUsers + ' users online');
  let message =
    data.username +
    (data.connected ? ' has joined the chat.' : ' has left the chat.');
  $('#messages').append($('<li>').html('<b>' + message + '</b>'));
});
```

***

Socket.IO에서의 연결과 인증은 서로 다른 개념입니다. 먼저 Socket.IO 연결(`io.on('connection', ...)`)은 클라이언트와 서버 간의 소켓 연결을 설정하고 실시간 통신을 처리하는 데 사용됩니다. 이 연결은 기본적으로 클라이언트가 서버에 접속하고 실시간 이벤트를 교환하는 데 사용됩니다.

반면에 `io.use(passportSocketIo.authorize(...))` 코드는 Socket.IO에서의 인증 및 권한 부여를 다룹니다. 이 부분은 클라이언트의 연결을 허용하거나 거부하고, 연결이 성공적으로 이루어질 경우 사용자를 인증하며 연결이 거부될 경우 인증하지 않는 부분입니다. 이 인증은 주로 웹 애플리케이션에서 로그인한 사용자를 식별하고 해당 사용자에 대한 권한을 관리하기 위해 사용됩니다.

Socket.IO는 웹 소켓 프로토콜을 기반으로 하지만, 기본적으로 클라이언트의 연결을 누구나 수락합니다. 그러나 인증을 통해 특정 사용자만 연결을 허용하고 해당 사용자의 신원을 확인할 수 있게 됩니다. 이는 보안 및 권한 부여에 중요합니다. 인증 없이 누구나 연결을 허용한다면 누구나 서버와 통신할 수 있고, 이는 보안 문제를 야기할 수 있습니다.

따라서, `passportSocketIo.authorize`와 `io.use`를 사용하여 인증을 설정하는 것은 클라이언트의 연결을 안전하게 관리하고 로그인 사용자를 식별하기 위해 필요한 작업입니다. 인증은 쿠키를 사용할 때 또는 다른 인증 방법을 사용할 때 모두 중요합니다. 클라이언트가 연결되기 전에 인증 및 권한 부여를 수행하여 보안을 강화하고 로그인한 사용자의 데이터를 안전하게 처리할 수 있도록 합니다.  

**즉, `io.use(passportSocketIo.authorize({...`코드를 입력한 순간 전에 로그인을 해서 세션 id를 쿠키로 부여받은 사람만 웹 소켓 통신이 가능하게 된다**  
`io.use(passportSocketIo.authorize(...))` 코드는 Socket.IO에서의 연결을 인증하고 권한을 부여하기 위한 부분으로, 세션 기반의 사용자 인증을 통해 연결을 허용합니다. 이 코드를 사용하면 세션 ID가 쿠키로 제공된 사용자만 웹 소켓 통신을 가능하게 합니다.

세션 ID가 쿠키로 제공되면, 인증 미들웨어는 해당 세션 ID를 검증하고 사용자의 식별 정보를 가져옵니다. 사용자의 식별 정보(예: 사용자 ID)를 통해 해당 사용자를 식별하고 권한을 확인할 수 있습니다. 이를 통해 로그인한 사용자만 웹 소켓 통신에 참여하거나 특정 권한을 가진 사용자만 특정 이벤트에 접근할 수 있도록 보안 및 권한 부여를 구현할 수 있습니다.

따라서 `passportSocketIo.authorize`를 사용하면 로그인을 한 사용자 또는 세션 ID를 갖고 있는 사용자만 웹 소켓 통신에 참여할 수 있게 됩니다. 이것은 웹 소켓 통신을 보안하고 인증된 사용자만 서버와 통신하도록 하는 데 중요한 역할을 합니다.
***

**client.js**
```node.js
$(document).ready(function () {
  /* Global io */
  let socket = io();

  socket.on('user', (data) => {
    $('#num-users').text(data.currentUsers + ' users online');
    let message = data.username + (data.connected ? ' has joined the chat.' : ' has left the chat.');
    $('#messages').append($('<li>').html('<b>' + message + '</b>'));
  });

  // Form submittion with new message in field with id 'm'
  $('form').submit(function () {
    let messageToSend = $('#m').val();
    // Send message to server here?
    $('#m').val('');
    return false; // Prevent form submit from refreshing page
  });
});
```
```javascript
document.addEventListener('DOMContentLoaded', function () {
  // Global io
  let socket = io();

  socket.on('user', function (data) {
    document.getElementById('num-users').textContent = data.currentUsers + ' users online';
    let message = data.username + (data.connected ? ' has joined the chat.' : ' has left the chat.');
    let li = document.createElement('li');
    li.innerHTML = '<b>' + message + '</b>';
    document.getElementById('messages').appendChild(li);
  });

  // Form submission with new message in field with id 'm'
  document.querySelector('form').addEventListener('submit', function (event) {
    event.preventDefault();
    let messageToSend = document.getElementById('m').value;
    // Send message to server here?
    document.getElementById('m').value = '';
  });
});
```
**server.js**
```node.js
'use strict';
require('dotenv').config();
const express = require('express');
const myDB = require('./connection');
const fccTesting = require('./freeCodeCamp/fcctesting.js');
const session = require('express-session');
const passport = require('passport');
const routes = require('./routes.js');
const auth = require('./auth.js');

const app = express();

const http = require('http').createServer(app);
const io = require('socket.io')(http);
const passportSocketIo = require('passport.socketio');
const cookieParser = require('cookie-parser');
const MongoStore = require('connect-mongo')(session);
const URI = process.env.MONGO_URI;
const store = new MongoStore({ url: URI });

app.set('view engine', 'pug');
app.set('views', './views/pug');

app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: true,
  saveUninitialized: true,
  cookie: { secure: false },
  key: 'express.sid',
  store: store
}));

app.use(passport.initialize());
app.use(passport.session());

fccTesting(app); // For fCC testing purposes
app.use('/public', express.static(process.cwd() + '/public'));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

io.use(
  passportSocketIo.authorize({
    cookieParser: cookieParser,
    key: 'express.sid',
    secret: process.env.SESSION_SECRET,
    store: store,
    success: onAuthorizeSuccess,
    fail: onAuthorizeFail
  })
);

myDB(async client => {
  const myDataBase = await client.db('database').collection('users');

  routes(app, myDataBase);
  auth(app, myDataBase);

  let currentUsers = 0;
  io.on('connection', (socket) => {
    ++currentUsers;
    io.emit('user', {
      username: socket.request.user.username,
      currentUsers,
      connected: true
    });
    console.log('A user has connected');
    socket.on('disconnect', () => {
      console.log('A user has disconnected');
      --currentUsers;
      io.emit('user', {
        username: socket.request.user.username,
        currentUsers,
        connected: false
      });
    });
  });
  
}).catch(e => {
  app.route('/').get((req, res) => {
    res.render('index', { title: e, message: 'Unable to connect to database' });
  });
});

function onAuthorizeSuccess(data, accept) {
  console.log('successful connection to socket.io');

  accept(null, true);
}

function onAuthorizeFail(data, message, error, accept) {
  if (error) throw new Error(message);
  console.log('failed connection to socket.io:', message);
  accept(null, false);
}
  
const PORT = process.env.PORT || 3000;
http.listen(PORT, () => {
  console.log(`Listening on port ${PORT}`);
});
```

## Send and Display Chat Messages

It's time you start allowing clients to send a chat message to the server to emit to all the clients! In your `client.js` file, you should see there is already a block of code handling when the message form is submitted.
```node.js
$('form').submit(function() {
  /*logic*/
});
```
Within the form submit code, you should emit an event after you define `messageToSend` but before you clear the text box `#m`. The event should be named `'chat message'` and the data should just be `messageToSend`.
```node.js
socket.emit('chat message', messageToSend);
```
Now, on your server, you should be listening to the socket for the event `'chat message'` with the data being named `message`. Once the event is received, it should emit the event `'chat message'` to all sockets using `io.emit`, sending a data object containing the `username` and `message`.

In `client.js`, you should now listen for event `'chat message'` and, when received, append a list item to `#messages` with the username, a colon, and the message!

At this point, the chat should be fully functional and sending messages across all clients!

***

**client.js**
```node.js
$(document).ready(function () {
    /*global io*/
    let socket = io();

    socket.on('user', data => {
        $('#num-users').text(data.currentUsers + ' users online');
        let message =
            data.username +
            (data.connected ? ' has joined the chat.' : ' has left the chat.');
        $('#messages').append($('<li>').html('<b>' + message + '</b>'));
    });

    socket.on('chat message', data => {
        console.log('socket.on 1')
        $('#messages').append($('<li>').text(`${data.username}: ${data.message}`));
    })

    // Form submittion with new message in field with id 'm'
    $('form').submit(function () {
        let messageToSend = $('#m').val();
        //send message to server here?
        socket.emit('chat message', messageToSend);
        $('#m').val('');
        return false; // prevent form submit from refreshing page
    });
});
```
```javascript
document.addEventListener('DOMContentLoaded', function () {
  // Global io
  let socket = io();

  socket.on('user', function (data) {
    document.getElementById('num-users').textContent = data.currentUsers + ' users online';
    let message =
      data.username +
      (data.connected ? ' has joined the chat.' : ' has left the chat.');
    let li = document.createElement('li');
    li.innerHTML = '<b>' + message + '</b>';
    document.getElementById('messages').appendChild(li);
  });

  socket.on('chat message', function (data) {
    console.log('socket.on 1');
    let li = document.createElement('li');
    li.textContent = data.username + ': ' + data.message;
    document.getElementById('messages').appendChild(li);
  });

  // Form submission with new message in the field with id 'm'
  document.querySelector('form').addEventListener('submit', function (event) {
    event.preventDefault();
    let messageToSend = document.getElementById('m').value;
    // Send message to server here
    socket.emit('chat message', messageToSend);
    document.getElementById('m').value = '';
  });
});
```

- **폼 제출 방지**: 주어진 코드에서 event.preventDefault()는 폼 제출을 방지합니다. 폼 제출은 일반적으로 페이지를 다시 로드하거나 다른 동작을 수행하려는 브라우저의 기본 동작 중 하나입니다. 하지만 채팅 애플리케이션에서는 페이지를 다시 로드하지 않고도 실시간으로 메시지를 전송하려고 합니다. event.preventDefault()를 사용하면 이러한 기본 동작을 중지하고 추가 동작을 처리할 수 있습니다.

**server.js**
```node.js
'use strict';
require('dotenv').config();
const express = require('express');
const myDB = require('./connection');
const fccTesting = require('./freeCodeCamp/fcctesting.js');
const session = require('express-session');
const passport = require('passport');
const routes = require('./routes.js');
const auth = require('./auth.js');

const app = express();

const http = require('http').createServer(app);
const io = require('socket.io')(http);
const passportSocketIo = require('passport.socketio');
const cookieParser = require('cookie-parser');
const MongoStore = require('connect-mongo')(session);
const URI = process.env.MONGO_URI;
const store = new MongoStore({ url: URI });

app.set('view engine', 'pug');
app.set('views', './views/pug');

app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: true,
  saveUninitialized: true,
  cookie: { secure: false },
  key: 'express.sid',
  store: store
}));

app.use(passport.initialize());
app.use(passport.session());

fccTesting(app); // For fCC testing purposes
app.use('/public', express.static(process.cwd() + '/public'));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

io.use(
  passportSocketIo.authorize({
    cookieParser: cookieParser,
    key: 'express.sid',
    secret: process.env.SESSION_SECRET,
    store: store,
    success: onAuthorizeSuccess,
    fail: onAuthorizeFail
  })
);

myDB(async client => {
  const myDataBase = await client.db('database').collection('users');

  routes(app, myDataBase);
  auth(app, myDataBase);

  let currentUsers = 0;
  io.on('connection', (socket) => {
    ++currentUsers;
    io.emit('user', {
      username: socket.request.user.username,
      currentUsers,
      connected: true
    });
    socket.on('chat message', (message) => {
      io.emit('chat message', { username: socket.request.user.username, message });
    });
    console.log('A user has connected');
    socket.on('disconnect', () => {
      console.log('A user has disconnected');
      --currentUsers;
      io.emit('user', {
        username: socket.request.user.username,
        currentUsers,
        connected: false
      });
    });
  });
  
}).catch(e => {
  app.route('/').get((req, res) => {
    res.render('index', { title: e, message: 'Unable to connect to database' });
  });
});

function onAuthorizeSuccess(data, accept) {
  console.log('successful connection to socket.io');

  accept(null, true);
}

function onAuthorizeFail(data, message, error, accept) {
  if (error) throw new Error(message);
  console.log('failed connection to socket.io:', message);
  accept(null, false);
}
  
const PORT = process.env.PORT || 3000;
http.listen(PORT, () => {
  console.log(`Listening on port ${PORT}`);
});
```


