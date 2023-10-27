# Advanced Node and Express2

## 

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

