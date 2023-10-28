# Advanced Node and Express2

## Hashing Your Passwords

Going back to the information security section, you may remember that storing plaintext passwords is never okay. Now it is time to implement BCrypt to solve this issue.

`bcrypt@~5.0.0` has already been added as a dependency, so require it in your server. You will need to handle hashing in 2 key areas: where you handle registering/saving a new account, and when you check to see that a password is correct on login.

Currently on your registration route, you insert a user's plaintext password into the database like so: `password: req.body.password`. Hash the passwords instead by adding the following before your database logic: `const hash = bcrypt.hashSync(req.body.password, 12);`, and replacing the `req.body.password` in the database saving with just `password: hash`.

On your authentication strategy, you check for the following in your code before completing the process: `if (password !== user.password) return done(null, false);`. After making the previous changes, now `user.password` is a hash. Before making a change to the existing code, notice how the statement is checking if the password is not equal then return non-authenticated. With this in mind, change that code to look as follows to properly check the password entered against the hash:

```node.js
if (!bcrypt.compareSync(password, user.password)) { 
  return done(null, false);
}
```
That is all it takes to implement one of the most important security features when you have to store passwords.  

***

비밀번호를 그대로 저장하면 위험하기 때문에, 비밀번호를 암호화해서 저장해야 한다.BCrypt는 비밀번호를 안전하게 저장하는데 도움을 주는 라이브러리입니다. 이것을 사용하면 비밀번호를 해시화하고 저장할 수 있습니다. 해시화된 비밀번호는 원래 비밀번호를 찾을 수 없도록 만들어줍니다. 또한 로그인 시 비밀번호를 확인할 때도 해시된 비밀번호와 입력된 비밀번호를 비교하여 일치 여부를 확인합니다. 

*** 

1. "registering/saving a new account" 영역:
이 부분은 사용자가 새로운 계정을 등록하거나 저장할 때 해당 계정의 비밀번호를 해시화해야 하는 곳입니다. 새로운 사용자가 사이트에 가입할 때, 사용자의 비밀번호를 안전하게 저장하려면 이 곳에서 비밀번호를 해시화하고 저장해야 합니다.

2. "checking to see that a password is correct on login" 영역:
이 부분은 사용자가 로그인할 때 제출한 비밀번호가 올바른지 확인해야 하는 곳입니다. 사용자가 로그인 시에 입력한 비밀번호를 해시화하여 저장된 해시값과 비교하여 올바른지 확인해야 합니다. 이렇게 하면 로그인 시 비밀번호가 정확히 일치하는지 확인할 수 있습니다.

```node.js
const hash = bcrypt.hashSync(req.body.password, 12);
```
- bcrypt: bcrypt는 비밀번호 해시화를 수행하기 위한 라이브러리로, 비밀번호 보안에 널리 사용됩니다.
- bcrypt.hashSync(): bcrypt 라이브러리의 메서드 중 하나로, 동기적인 방식으로 비밀번호를 해시화합니다. 비밀번호를 해시화할 때 사용됩니다.
- 12는 해시화의 난이도를 지정하는 매개변수로, 높은 숫자일수록 더 많은 계산이 필요하여 해시화를 더욱 복잡하게 만듭니다.

***

```node.js
if (!bcrypt.compareSync(password, user.password)) { 
  return done(null, false);
}
```
BCrypt를 사용하면 비밀번호를 해시화하여 저장합니다. 따라서 로그인 시 비밀번호를 확인할 때에도 입력된 비밀번호를 다시 해시화한 후 데이터베이스에 저장된 해시와 비교해야 합니다.  

- bcrypt.compareSync(password, user.password)는 입력된 비밀번호(password)와 저장된 해시 비밀번호(user.password)를 비교하는 BCrypt 함수입니다.
- compareSync 함수는 입력된 비밀번호와 해시된 비밀번호가 일치하지 않으면 false를 반환하고, 일치하면 true를 반환합니다.

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
const bcrypt = require('bcrypt');

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
      if (!bcrypt.compareSync(password, user.password)) { 
          return done(null, false);
      }
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

## Clean Up Your Project with Modules
Right now, everything you have is in your `server.js` file. This can lead to hard to manage code that isn't very expandable. Create 2 new files: `routes.js` and `auth.js`.  

Both should start with the following code:
```node.js
module.exports = function (app, myDataBase) {

}
```
Now, in the top of your server file, require these files like so: `const routes = require('./routes.js');` Right after you establish a successful connection with the database, instantiate each of them like so: `routes(app, myDataBase)`

Finally, take all of the routes in your server and paste them into your new files, and remove them from your server file. Also take the `ensureAuthenticated` function, since it was specifically created for routing. Now, you will have to correctly add the dependencies in which are used, such as `const passport = require('passport');`, at the very top, above the export line in your `routes.js` file.

Keep adding them until no more errors exist, and your server file no longer has any routing (**except for the route in the catch block**)!

Do the same thing in your `auth.js` file with all of the things related to authentication such as the serialization and the setting up of the local strategy and erase them from your server file. Be sure to add the dependencies in and call `auth(app, myDataBase)` in the server in the same spot.

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

  routes(app, myDataBase);
  auth(app, myDataBase);
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

**auth.js**
```node.js
const passport = require('passport');
const LocalStrategy = require('passport-local');
const bcrypt = require('bcrypt');
const { ObjectID } = require('mongodb');

module.exports = function (app, myDataBase) {
  passport.serializeUser((user, done) => {
      done(null, user._id);
  });
  passport.deserializeUser((id, done) => {
      myDataBase.findOne({ _id: new ObjectID(id) }, (err, doc) => {
          if (err) return console.error(err);
          done(null, doc);
      });
  });

  passport.use(new LocalStrategy((username, password, done) => {
    myDataBase.findOne({ username: username }, (err, user) => {
      console.log(`User ${username} attempted to log in.`);
      if (err) { return done(err); }
      if (!user) { return done(null, false); }
      if (!bcrypt.compareSync(password, user.password)) { 
          return done(null, false);
      }
      return done(null, user);
    });
  }));
}
```
***

**Q: route.js에서 passport.authenticate('local', { failureRedirect: '/' })이 가능한 이유?**  

route.js에서 auth.js를 직접 불러오지 않더라도 server.js에서 auth(app, myDataBase)를 호출함으로써 auth.js 모듈이 route.js 모듈 내에서 사용될 수 있습니다.

server.js에서 routes(app, myDataBase)와 auth(app, myDataBase)를 호출하는 것은 각각의 모듈을 초기화하고 실행하는 역할을 합니다. 따라서 auth.js에서 설정한 Passport와 관련된 로직이 route.js에서 사용 가능한 것입니다. 이렇게 모듈을 초기화하고 서로 연결하는 것은 Node.js의 모듈 시스템의 장점 중 하나입니다.  

## Implementation of Social Authentication

The basic path this kind of authentication will follow in your app is:

1. User clicks a button or link sending them to your route to authenticate using a specific strategy (e.g. GitHub).
2. Your route calls `passport.authenticate('github')` which redirects them to GitHub.
3. The page the user lands on, on GitHub, allows them to login if they aren't already. It then asks them to approve access to their profile from your app.
4. The user is then returned to your app at a specific callback url with their profile if they are approved.
5. They are now authenticated, and your app should check if it is a returning profile, or save it in your database if it is not.


Strategies with OAuth require you to have at least a Client ID and a Client Secret which is a way for the service to verify who the authentication request is coming from and if it is valid. These are obtained from the site you are trying to implement authentication with, such as GitHub, and are unique to your app--**THEY ARE NOT TO BE SHARED** and should never be uploaded to a public repository or written directly in your code. A common practice is to put them in your `.env` file and reference them like so: `process.env.GITHUB_CLIENT_ID`. For this challenge you are going to use the GitHub strategy.

Follow [these instructions](https://www.freecodecamp.org/news/how-to-set-up-a-github-oauth-application/) to obtain your Client ID and Secret from GitHub. Set the homepage URL to your Replit homepage (**not the project code's URL**), and set the callback URL to the same homepage URL with `/auth/github/callback` appended to the end. Save the client ID and your client secret in your project's `.env` file as `GITHUB_CLIENT_ID` and `GITHUB_CLIENT_SECRET`.

In your `routes.js` file, add `showSocialAuth: true` to the homepage route, after `showRegistration: true`. Now, create 2 routes accepting GET requests: `/auth/github` and `/auth/github/callback`. The first should only call passport to authenticate `'github'`. The second should call passport to authenticate `'github'` with a failure redirect to `/`, and then if that is successful redirect to `/profile` (similar to your last project).

An example of how `/auth/github/callback` should look is similar to how you handled a normal login:
```node.js
app.route('/login')
  .post(passport.authenticate('local', { failureRedirect: '/' }), (req,res) => {
    res.redirect('/profile');
  });
```

***

- **The user is then returned to your app at a specific callback url with their profile if they are approved**: 사용자가 권한을 승인하면, 인증 제공자(예: GitHub)는 사용자를 웹 애플리케이션으로 특정한 콜백 URL로 리디렉션합니다. 리디렉션된 콜백 URL에는 사용자의 프로필 정보 및 인증 토큰이 포함됩니다.
- **They are now authenticated, and your app should check if it is a returning profile, or save it in your database if it is not**: 사용자가 콜백 URL로 리디렉션되면, 웹 애플리케이션은 사용자의 프로필 정보를 수신합니다. 이 정보를 사용하여 사용자를 인증하고 로그인 상태로 설정합니다.웹 애플리케이션은 이 사용자 프로필 정보를 데이터베이스에 저장하거나 데이터베이스에서 해당 프로필을 검색하여 사용자를 식별합니다. 만약 사용자가 처음 로그인한 경우, 그 정보를 저장하고 필요한 경우 추가 데이터베이스 작업을 수행합니다.

***

1. 개발자 또는 웹 애플리케이션 소유자는 깃허브 또는 다른 OAuth 인증 제공자의 개발자 포털 또는 애플리케이션 등록 페이지에 이동하여 새로운 애플리케이션을 등록합니다.
2. 애플리케이션 등록 프로세스에서, 인증 제공자 (예: 깃허브)는 해당 애플리케이션에 대해 고유한 Client ID를 생성하고 제공합니다. 이 Client ID는 해당 애플리케이션을 고유하게 식별합니다.
3. 개발자는 자신의 웹 애플리케이션 코드에 이 Client ID를 통합합니다. 이것은 웹 애플리케이션이 깃허브와 통신할 때 인증 요청이 어디서 왔는지를 식별하는 데 사용됩니다.
4. 사용자가 웹 애플리케이션에서 "GitHub으로 로그인" 버튼 또는 링크를 클릭하면, 웹 애플리케이션은 깃허브에 로그인 요청을 보냅니다. 이 요청에는 Client ID와 기타 필요한 정보가 포함됩니다.
5. 깃허브는 이 요청을 받고, Client ID를 확인하여 해당 요청이 등록된 애플리케이션에서 온 것임을 확인합니다. 이렇게 하면 *애플리케이션의 신원을 확인*합니다.
6. 깃허브는 사용자를 로그인 페이지로 리디렉션하고, 사용자가 로그인하고 권한 부여 요청을 승인하면, 인증 제공자는 액세스 *토큰을 생성하고 해당 애플리케이션으로 다시 리디렉션*합니다.
7. 웹 애플리케이션은 *액세스 토큰*을 사용하여 사용자 정보를 요청하고, 사용자를 *로그인 상태로 설정*합니다.

***

OAuth는 외부 서비스 (예: GitHub)를 사용하여 사용자 인증 및 권한 부여를 처리하는 프로토콜입니다. 다음은 각 단계에 대한 설명입니다:  

1. 사용자가 버튼 또는 링크를 클릭하여 서비스 (예: GitHub)의 인증을 요청하는 경로로 이동합니다. 이 경로는 Passport 또는 다른 OAuth 라이브러리를 사용하여 구현됩니다.
2. 이 경로에서는 passport.authenticate('github')와 같은 메서드를 호출하여 사용자를 외부 서비스 (GitHub)의 인증 페이지로 리디렉션합니다. 사용자는 외부 서비스에서 로그인하거나 로그인되어 있지 않다면 로그인하게 됩니다.
3. 사용자가 외부 서비스 (GitHub)에 로그인하면 해당 서비스는 앱의 권한 요청을 확인하고, 사용자에게 자신의 프로필 정보에 대한 액세스를 승인할지 묻는 화면을 표시합니다.
4. 사용자가 권한을 승인하면 외부 서비스는 사용자를 앱으로 다시 리디렉션하고, 특정 콜백 URL에 사용자 프로필 정보와 함께 리디렉션합니다.
5. 앱은 이러한 프로필 정보를 받아와서 사용자가 이미 데이터베이스에 저장되어 있는지 확인합니다. 사용자가 새로운 사용자일 경우, 해당 프로필 정보를 데이터베이스에 저장하고 사용자를 인증한 후, 필요한 작업을 수행합니다.

***

- OAuth를 사용할 때, 앱이 외부 서비스 (예: GitHub)와 통신하려면 최소한 "클라이언트 ID"와 "클라이언트 비밀"이라는 인증 정보가 필요합니다. 이 정보는 앱이 인증 요청이 유효한 것인지를 확인하고, 요청이 어느 앱에서 온 것인지 확인하는 데 사용됩니다. 이 클라이언트 ID와 클라이언트 비밀은 외부 서비스로부터 얻으며, 각 앱에 고유합니다. 이 정보는 공개되어서는 안 되며, 공개 저장소에 업로드하거나 코드에 직접 포함시키면 안 됩니다.
- 클라이언트 ID와 클라이언트 비밀은 앱을 식별하고 서비스에 대한 액세스 권한을 부여하는 데 필요한 것으로, 앱과 서비스 간의 신뢰성을 보장하기 위해 사용됩니다.
- Client ID는 애플리케이션을 고유하게 식별하고, 해당 애플리케이션과 인증 제공자 간의 통신을 안전하게 만들기 위해 사용됩니다. 따라서 Client ID를 발급받는 작업은 애플리케이션을 개발하고 등록한 개발자 또는 애플리케이션 소유자가 수행하는 것이 일반적입니다. 그 후, 다른 사용자들은 해당 애플리케이션을 통해 로그인하고 권한을 승인할 수 있습니다.

***

- /auth/github 경로(route)는 Passport를 사용하여 'github' 전략을 인증하도록 호출해야 합니다. 즉, 사용자를 GitHub의 인증 페이지로 리디렉션하여 GitHub에서 로그인 및 권한 부여 프로세스를 수행하도록 합니다.
- /auth/github/callback 경로(route)는 GitHub에서의 인증이 성공하면 /profile로 리디렉션하도록 설정해야 하며, 인증이 실패하면 /로 리디렉션하도록 설정해야 합니다.

- **/auth/github 엔드포인트**:
   - 처음에 사용자가 "GitHub으로 로그인" 버튼을 클릭하면 /auth/github 엔드포인트로 리디렉션됩니다.
   - 여기서 passport.authenticate('github')는 GitHub에 대한 인증 요청을 시작하고, 사용자를 GitHub 로그인 페이지로 리디렉션합니다.
- **/auth/github/callback 엔드포인트**:
   - 사용자가 GitHub에서 로그인하고 권한을 부여하면, GitHub은 사용자를 /auth/github/callback 엔드포인트로 리디렉션합니다.
   - 이 엔드포인트에서도 passport.authenticate('github')를 사용합니다. 이번에는 GitHub에서 받은 인증 코드를 처리하고, 사용자 정보를 가져옵니다. failureRedirect 옵션은 만약 인증에 실패하면 어디로 리디렉션할지를 지정합니다.
 

요약하면, 두 가지 다른 엔드포인트가 다른 시점에 다른 동작을 수행하며, 각각의 엔드포인트에서 passport.authenticate('github') 함수가 중복으로 사용됩니다. 첫 번째 엔드포인트는 인증 요청을 시작하고, 두 번째 엔드포인트는 인증 결과를 처리합니다.

***

[노마드 코더](https://youtu.be/tosLBcAX1vk?feature=shared)  

**쿠키**  
- 쿠키를 이용해 브라우저에 데이터 저장, 브라우저에만 존재
- 유효기간이 있다. 공간 제약이 있음
- 인증 뿐만 아니라, 여러 가지 정보를 저장할 수 있다.
- ***세션ID를 전달하기 위한 매개체***

**세션**
- 유저 정보를 세션DB에 저장,
- 세션ID를 가지고 세션DB에서 검색

**토큰**
- 브라우저에만 있는 쿠키를 사용할 수 없을 때
- 이상하게 생긴 string. 서버에 보내고 서버는 세션 DB에서 해당 토큰과 일치하는 유저를 찾는다.

**JWT**
- 토큰 형식, 유저 인증을 이걸로 처리하면 세션DB 필요 없고, 서버는 유저 인증한다고 많은 일을 할 필요 없다.
- 제약이 없어 엄청 길어도 된다. 엄청 긴 string.
- DB를 건드리는 대신, 사인 알고리즘을 이용해 사인을 하고, 사인된 정보를 string 형태로 보낸다.
- 사인된 정보를 통해 서버에 요청.

**세션와 JWT의 차이**
- 세션은 세션ID만 주면 된다. 세션에 대한 모든 정보는 세션 DB에 저장되어 있다. 페이지를 요청하면 서버는 세션DB에서 찾으면 되는 것
- JWT에서 서버는 유저를 인증하는데 필요한 정보를 토큰에 저장. 그리고 해당 토큰을 우리에게 준다. 요청이 있을 때 서버는 해당 토큰이 유효한지만 검증하면 된다.DB를 거치지 않는다. 암호화되지 않았기 때문에 누구나 열어서 해당 컨텐츠를 볼 수 있다. 토큰을 사인하고 이를 통해 유효한지 검증하는 것!
- 세션을 사용하면 유저의 모든 정보를 저장, DB를 사고 유지하는 비용
- JWT에서는 생성된 토큰을 추적하지 않고, 오직 토큰이 유효한가 여부를 검사. 

***

**route.js**
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
    res.redirect('/profile');
  });


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

## Implementation of Social Authentication II

The last part of setting up your GitHub authentication is to create the strategy itself. `passport-github@~1.1.0` has already been added as a dependency, so require it in your `auth.js` file as `GithubStrategy` like this: `const GitHubStrategy = require('passport-github').Strategy;`. Do not forget to require and configure `dotenv` to use your environment variables.

To set up the GitHub strategy, you have to tell Passport to use an instantiated `GitHubStrategy`, which accepts 2 arguments: an object (containing `clientID`, `clientSecret`, and `callbackURL`) and a function to be called when a user is successfully authenticated, which will determine if the user is new and what fields to save initially in the user's database object. This is common across many strategies, but some may require more information as outlined in that specific strategy's GitHub README. For example, Google requires a scope as well which determines what kind of information your request is asking to be returned and asks the user to approve such access.

The current strategy you are implementing authenticates users using a GitHub account and OAuth 2.0 tokens. The client ID and secret obtained when creating an application are supplied as options when creating the strategy. The strategy also requires a `verify` callback, which receives the access token and optional refresh token, as well as `profile` which contains the authenticated user's GitHub profile. The `verify` callback must call `cb` providing a user to complete authentication.

Here's how your new strategy should look at this point:

```node.js
passport.use(new GitHubStrategy({
  clientID: process.env.GITHUB_CLIENT_ID,
  clientSecret: process.env.GITHUB_CLIENT_SECRET,
  callbackURL: /*INSERT CALLBACK URL ENTERED INTO GITHUB HERE*/
},
  function(accessToken, refreshToken, profile, cb) {
    console.log(profile);
    //Database logic here with callback containing your user object
  }
));
```
Your authentication won't be successful yet, and it will actually throw an error without the database logic and callback, but it should log your GitHub profile to your console if you try it!

***

1. auth.js 파일에서 passport-github 패키지를 가져와야 합니다. 이것은 GitHub 전략을 사용하기 위한 라이브러리로, 다음과 같이 가져올 수 있습니다: `const GitHubStrategy = require('passport-github').Strategy;`. 또한 환경 변수를 사용하려면 dotenv도 가져와야 합니다.

2. GitHub 전략을 설정하기 위해 Passport에게 GitHubStrategy를 사용하도록 알려야 합니다. 이를 위해서는 GitHubStrategy의 인스턴스를 만들어야 합니다. 이 인스턴스는 다음 두 가지 인수를 받습니다.
  - 객체: 이 객체는 clientID, clientSecret, 그리고 callbackURL과 같은 정보를 포함합니다. 이 정보는 GitHub 앱 설정에서 얻을 수 있는데, clientID와 clientSecret는 앱을 식별하고, *callbackURL은 사용자가 GitHub에서 로그인 및 권한 부여를 마치면 돌아갈 URL*을 지정합니다.
  - 함수: 사용자가 성공적으로 인증될 때 호출되는 함수를 지정해야 합니다. 이 함수는 사용자가 새로운 사용자인지 여부를 결정하고, 사용자 데이터베이스 객체에 초기로 저장할 필드를 지정합니다. 이 함수는 사용자 전략에 따라 다를 수 있으며, GitHub 전략의 README에 자세히 설명되어 있습니다. 예를 들어, Google 전략은 정보 요청에 대한 범위(scope)를 필요로하며, 사용자에게 어떤 종류의 정보 액세스를 요청하고 사용자 승인을 요청합니다.

***

**verify 콜백**: verify 콜백은 사용자가 인증되면 호출되는 함수입니다. 이 콜백은 사용자에게 액세스 토큰과 선택적으로 리프레시 토큰을 전달받습니다. 이를 통해 사용자의 GitHub 프로필 정보에 액세스할 수 있습니다. verify 콜백은 이 정보를 기반으로 사용자를 확인하고, 사용자 인증을 완료하기 위해 Passport에게 사용자를 제공해야 합니다.

***

**auth.js**

```node.js
const passport = require('passport');
const LocalStrategy = require('passport-local');
const bcrypt = require('bcrypt');
const { ObjectID } = require('mongodb');
const GitHubStrategy = require('passport-github').Strategy;
require('dotenv').config();

module.exports = function (app, myDataBase) {
  passport.serializeUser((user, done) => {
      done(null, user._id);
  });
  passport.deserializeUser((id, done) => {
      myDataBase.findOne({ _id: new ObjectID(id) }, (err, doc) => {
          if (err) return console.error(err);
          done(null, doc);
      });
  });

  passport.use(new LocalStrategy((username, password, done) => {
    myDataBase.findOne({ username: username }, (err, user) => {
      console.log(`User ${username} attempted to log in.`);
      if (err) { return done(err); }
      if (!user) { return done(null, false); }
      if (!bcrypt.compareSync(password, user.password)) { 
          return done(null, false);
      }
      return done(null, user);
    });
  }));

  passport.use(new GitHubStrategy({
      clientID: process.env.GITHUB_CLIENT_ID,
      clientSecret: process.env.GITHUB_CLIENT_SECRET,
      callbackURL: 'https://boilerplate-advancednode-5.artistich.repl.co/auth/github/callback'
    },
    function (accessToken, refreshToken, profile, cb) {
      console.log(profile);
      //Database logic here with callback containing our user object
    }
  ));
}
```

## Implementation of Social Authentication III

The final part of the strategy is handling the profile returned from GitHub. We need to load the user's database object if it exists, or create one if it doesn't, and populate the fields from the profile, then return the user's object. GitHub supplies us a unique id within each profile which we can use to search with to serialize the user with (already implemented). Below is an example implementation you can use in your project--it goes within the function that is the second argument for the new strategy, right below where `console.log(profile);` currently is:
```node.js
myDataBase.findOneAndUpdate(
  { id: profile.id },
  {
    $setOnInsert: {
      id: profile.id,
      username: profile.username,
      name: profile.displayName || 'John Doe',
      photo: profile.photos[0].value || '',
      email: Array.isArray(profile.emails)
        ? profile.emails[0].value
        : 'No public email',
      created_on: new Date(),
      provider: profile.provider || ''
    },
    $set: {
      last_login: new Date()
    },
    $inc: {
      login_count: 1
    }
  },
  { upsert: true, new: true },
  (err, doc) => {
    return cb(null, doc.value);
  }
);
```
`findOneAndUpdate` allows you to search for an object and update it. If the object doesn't exist, it will be inserted and made available to the callback function. In this example, we always set `last_login`, increment the `login_count` by `1`, and only populate the majority of the fields when a new object (new user) is inserted. Notice the use of default values. Sometimes a profile returned won't have all the information filled out or the user will keep it private. In this case, you handle it to prevent an error.

You should be able to login to your app now. Try it!  

***

이 부분은 GitHub로부터 반환된 프로필을 처리하는 전략의 마지막 부분을 다룹니다. 우리는 사용자 데이터베이스 객체를 로드해야 하며, 해당 사용자 데이터베이스 객체가 존재하면 프로필에서 필드를 채우고 사용자 객체를 반환해야 합니다. 사용자 프로필 내부에는 각 프로필에 대해 고유한 ID가 제공되는데, 이 ID를 사용하여 사용자를 식별하는 데 사용할 수 있습니다. 이러한 작업은 사용자 직렬화(User Serialization)에 필요합니다.

여기에는 프로젝트에서 사용할 수 있는 예제 구현이 포함되어 있습니다. 이 예제는 GitHub 프로필을 처리하기 위한 것으로, 이 코드는 새 전략의 두 번째 인수로 사용됩니다. 즉, 현재 console.log(profile); 아래에 배치됩니다. 아래는 이 부분의 설명입니다:

1. 사용자 데이터베이스 객체 로드 또는 생성: 이 부분에서는 GitHub 프로필 정보를 사용하여 사용자 데이터베이스 객체를 로드하거나 새로 생성해야 합니다. 즉, 사용자가 이미 데이터베이스에 등록되어 있는지 확인하고, 등록되어 있지 않다면 새로운 사용자 데이터베이스 객체를 만들어야 합니다.

2. 프로필 필드 채우기: GitHub 프로필에서 반환된 정보를 사용하여 사용자 데이터베이스 객체의 필드를 채웁니다. 이것은 사용자에 대한 추가 정보를 제공하고 사용자 데이터베이스를 업데이트하는 데 사용됩니다.

3. 사용자 객체 반환: 이 작업을 마치면 사용자 객체를 반환해야 합니다. 이 사용자 객체는 이후에 Passport에 의해 사용자 세션으로 직렬화될 것이며, 사용자가 애플리케이션에서 인증된 상태를 유지할 수 있게 됩니다.

이 부분은 사용자 프로필을 처리하고 GitHub에서 반환된 정보를 사용하여 사용자를 식별하고 데이터베이스에 저장하는 과정을 설명하고 있습니다.

***

- { id: profile.id }: MongoDB에서 찾을 문서의 조건을 정의합니다. 여기서는 GitHub 프로필의 고유 ID (profile.id)를 사용하여 사용자를 식별합니다.
- $setOnInsert: 새로운 문서가 삽입될 때 설정할 필드를 정의합니다. 즉, 사용자가 처음 등록될 때 이러한 필드가 설정됩니다.
  - id: GitHub 프로필의 고유 ID를 저장합니다.
  - username: GitHub 프로필의 사용자 이름을 저장합니다.
  - name: GitHub 프로필의 표시 이름을 저장하며, 이 값이 없으면 기본으로 'John Doe'가 저장됩니다.
  - photo: GitHub 프로필 사진 URL을 저장하며, 이 값이 없으면 빈 문자열로 저장됩니다.
  - email: GitHub 프로필의 이메일 주소를 저장하며, 이 값이 없거나 배열 형식이 아닌 경우 'No public email'이 저장됩니다.
  - created_on: 사용자 등록일을 현재 날짜로 저장합니다.
  - provider: 사용자 인증 공급자 정보를 저장합니다.
- $set: 문서를 업데이트할 때 설정할 필드를 정의합니다. 여기서는 last_login 필드를 현재 날짜로 설정합니다.
- $inc: 숫자 필드를 증가시키는데 사용됩니다. 여기서는 login_count 필드를 1 증가시킵니다.
- { upsert: true, new: true }: 옵션 객체로, 이 부분은 다음을 수행합니다.
  - upsert: true: 조건에 맞는 문서를 찾지 못하면 새 문서를 삽입합니다.
  - new: true: 업데이트된 문서를 반환하도록 설정합니다.
- (err, doc) => { return cb(null, doc.value); }: MongoDB에서의 작업이 완료되면 콜백 함수가 호출됩니다. 이 콜백 함수는 오류 (err)와 업데이트된 문서 (doc)를 처리하고 Passport에 사용자 정보를 반환하기 위해 cb(null, doc.value)를 호출합니다. Passport는 이 정보를 사용자 세션으로 직렬화합니다.

***

findOneAndUpdate 함수는 MongoDB에서 문서를 찾고 업데이트할 때 사용되는 메서드로, 다음과 같은 인수를 받습니다:

1. filter (조건 객체): 업데이트할 문서를 선택하는 데 사용되는 조건을 지정하는 객체입니다. 이 객체는 어떤 문서를 업데이트할지 결정합니다.
2. update (업데이트 객체): 업데이트될 내용을 정의하는 객체입니다. 이 객체는 새로운 데이터나 수정된 데이터를 포함하며, 필요한 경우 다양한 업데이트 연산자를 사용하여 업데이트 작업을 구체화할 수 있습니다.
3. options (옵션 객체): 업데이트 작업을 제어하는 다양한 옵션을 설정하는 객체입니다.
- upsert 옵션은 조건에 맞는 문서가 없을 때 새로운 문서를 삽입할지 여부를 결정합니다. 
- returnOriginal 옵션은 업데이트 전의 문서를 반환할지, 업데이트 후의 문서를 반환할지 설정합니다.
4. callback (콜백 함수): 업데이트 작업이 완료되면 호출되는 콜백 함수입니다. 이 함수는 두 개의 인수를 받는데, 첫 번째는 오류 (err) 객체이고, 두 번째는 업데이트된 문서 또는 결과를 나타내는 객체입니다.

***

이 예시에서는 항상 last_login 필드를 업데이트하고, login_count 필드를 1 증가시킵니다. 대부분의 필드는 새로운 객체(새 사용자)를 삽입할 때만 값을 채우고, 기본값을 사용합니다. 사용자 프로필에는 모든 정보가 채워지지 않을 수 있거나 사용자가 정보를 비공개로 유지할 수 있습니다. 이 경우, 기본값을 사용하여 누락된 정보나 비공개 정보를 처리함으로써 오류를 방지합니다.  

이러한 방식을 사용하면, 새로운 사용자가 등록될 때 필요한 필드를 기본값으로 채우고, 로그인 시마다 last_login을 업데이트하고 로그인 횟수를 추적할 수 있습니다. 또한 사용자 프로필에 누락된 정보나 비공개 정보가 있는 경우에도 오류가 발생하지 않도록 처리할 수 있습니다.

***

**auth.js**
```node.js
const passport = require('passport');
const LocalStrategy = require('passport-local');
const bcrypt = require('bcrypt');
const { ObjectID } = require('mongodb');
const GitHubStrategy = require('passport-github').Strategy;
require('dotenv').config();

module.exports = function (app, myDataBase) {
  passport.serializeUser((user, done) => {
      done(null, user._id);
  });
  passport.deserializeUser((id, done) => {
      myDataBase.findOne({ _id: new ObjectID(id) }, (err, doc) => {
          if (err) return console.error(err);
          done(null, doc);
      });
  });

  passport.use(new LocalStrategy((username, password, done) => {
    myDataBase.findOne({ username: username }, (err, user) => {
      console.log(`User ${username} attempted to log in.`);
      if (err) { return done(err); }
      if (!user) { return done(null, false); }
      if (!bcrypt.compareSync(password, user.password)) { 
          return done(null, false);
      }
      return done(null, user);
    });
  }));

  passport.use(new GitHubStrategy({
      clientID: process.env.GITHUB_CLIENT_ID,
      clientSecret: process.env.GITHUB_CLIENT_SECRET,
      callbackURL: 'https://boilerplate-advancednode-5.artistich.repl.co/auth/github/callback'
    },
    function (accessToken, refreshToken, profile, cb) {
      console.log(profile);
      //Database logic here with callback containing our user object
      myDataBase.findAndModify(
        { id: profile.id },
        {},
        {
          $setOnInsert: {
            id: profile.id,
            name: profile.displayName || 'John Doe',
            photo: profile.photos[0].value || '',
            email: Array.isArray(profile.emails) ? profile.emails[0].value : 'No public email',
            created_on: new Date(),
            provider: profile.provider || ''
          }, $set: {
            last_login: new Date()
          }, $inc: {
            login_count: 1
          }
        },
        { upsert: true, new: true },
        (err, doc) => {
          return cb(null, doc.value);
        }
      );
    }
  ));
}
```

