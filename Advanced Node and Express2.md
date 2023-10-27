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

OAuth는 외부 서비스 (예: GitHub)를 사용하여 사용자 인증 및 권한 부여를 처리하는 프로토콜입니다. 다음은 각 단계에 대한 설명입니다:  

1. 사용자가 버튼 또는 링크를 클릭하여 서비스 (예: GitHub)의 인증을 요청하는 경로로 이동합니다. 이 경로는 Passport 또는 다른 OAuth 라이브러리를 사용하여 구현됩니다.
2. 이 경로에서는 passport.authenticate('github')와 같은 메서드를 호출하여 사용자를 외부 서비스 (GitHub)의 인증 페이지로 리디렉션합니다. 사용자는 외부 서비스에서 로그인하거나 로그인되어 있지 않다면 로그인하게 됩니다.
3. 사용자가 외부 서비스 (GitHub)에 로그인하면 해당 서비스는 앱의 권한 요청을 확인하고, 사용자에게 자신의 프로필 정보에 대한 액세스를 승인할지 묻는 화면을 표시합니다.
4. 사용자가 권한을 승인하면 외부 서비스는 사용자를 앱으로 다시 리디렉션하고, 특정 콜백 URL에 사용자 프로필 정보와 함께 리디렉션합니다.
5. 앱은 이러한 프로필 정보를 받아와서 사용자가 이미 데이터베이스에 저장되어 있는지 확인합니다. 사용자가 새로운 사용자일 경우, 해당 프로필 정보를 데이터베이스에 저장하고 사용자를 인증한 후, 필요한 작업을 수행합니다.

***

- OAuth를 사용할 때, 앱이 외부 서비스 (예: GitHub)와 통신하려면 최소한 "클라이언트 ID"와 "클라이언트 비밀"이라는 인증 정보가 필요합니다. 이 정보는 앱이 인증 요청이 유효한 것인지를 확인하고, 요청이 어느 앱에서 온 것인지 확인하는 데 사용됩니다. 이 클라이언트 ID와 클라이언트 비밀은 외부 서비스로부터 얻으며, 각 앱에 고유합니다. 이 정보는 공개되어서는 안 되며, 공개 저장소에 업로드하거나 코드에 직접 포함시키면 안 됩니다.
- 클라이언트 ID와 클라이언트 비밀은 앱을 식별하고 서비스에 대한 액세스 권한을 부여하는 데 필요한 것으로, 앱과 서비스 간의 신뢰성을 보장하기 위해 사용됩니다.

***

