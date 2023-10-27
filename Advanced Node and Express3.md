# Advanced Node and Express3
## Set up the Environment

The following challenges will make use of the `chat.pug` file. So, in your `routes.js` file, add a GET route pointing to `/chat` which makes use of `ensureAuthenticated`, and renders `chat.pug`, with `{ user: req.user }` passed as an argument to the response. Now, alter your existing `/auth/github/callback` route to set the `req.session.user_id = req.user.id` and redirect to `/chat`.

`socket.io@~2.3.0` has already been added as a dependency, so require/instantiate it in your server as follows with `http` (comes built-in with Nodejs):  

```node.js
const http = require('http').createServer(app);
const io = require('socket.io')(http);
```
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

```node.js
const http = require('http').createServer(app);
const io = require('socket.io')(http);
```
이 코드는 Node.js 애플리케이션에서 Socket.IO를 사용하기 위한 설정을 하는 부분입니다.

1. const http = require('http').createServer(app);: 이 줄에서는 Node.js의 내장 모듈인 http를 사용하여 웹 서버를 생성하고, 그 서버를 Express 애플리케이션 app에 연결합니다. 이것은 Express 애플리케이션을 HTTP 서버로 변환하는 과정입니다. 즉, Express 애플리케이션을 웹 서버로 사용하게 됩니다.

2. const io = require('socket.io')(http);: 이 줄에서는 Socket.IO 모듈을 사용하여 Socket.IO 서버를 생성하고, 이 서버를 앞에서 생성한 HTTP 서버에 연결합니다. Socket.IO는 실시간 양방향 통신을 지원하는 라이브러리로, 클라이언트와 서버 간의 웹 소켓 연결을 구현하는 데 사용됩니다.

결과적으로, 이 코드는 Express 애플리케이션을 서빙하는 HTTP 서버와 동시에 Socket.IO 서버를 시작하게 됩니다. 이를 통해 웹 애플리케이션에서 클라이언트와 서버 간의 실시간 통신을 구현할 수 있습니다.
