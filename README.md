## محتوى اليوم الخامس


## express-validator

عبارة عن مجموعة من البرامج الوسيطة express.js التي تلتف بمدقق Validator.js لوظائف الفانكشن

## طريقة التثبيت express-validator


نقوم بتثبيته بستخدام npm بالبداية يجب ان نتأكد من اصدار Node.js 8  فما فوق

      npm install  express-validator


## خطوات البدء

من المستحسن أن تكون لديك معرفة أساسية بوحدة express.js قبل البدء بالخطوات.

#### لنبدأ بكتابة مسار أساسي لإنشاء مستخدم في قاعدة البيانات:

    const express = require('express');
    const app = express();

    app.use(express.json());
    app.post('/user', (req, res) => {
    User.create({
     username: req.body.username,
     password: req.body.password,
    }).then(user => res.json(user));
      });

#### بعد ذلك ، ستحتاج إلى التأكد من التحقق من صحة الإدخال والإبلاغ عن أي أخطاء قبل إنشاء المستخدم:

    // ...rest of the initial code omitted for simplicity.
    const { body, validationResult } = require('express-validator');

    app.post(
     '/user',
    
    // username must be an email
    body('username').isEmail(),
    
    // password must be at least 5 chars long
    body('password').isLength({ min: 5 }),
    (req, res) => {
    
    // Finds the validation errors in this request and wraps them in an object with handy functions
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }

     User.create({
      username: req.body.username,
      password: req.body.password,
     }).then(user => res.json(user));
     },
    );
    
 #### في حال تم تقديم طلب يتضمن حقول اسم مستخدم أو كلمة مرور غير صالحة ، سيستجيب السيرفر الى هذا الكود:
    
      {
     "errors": [
      {
       "location": "body",
       "msg": "Invalid value",
       "param": "username"
      }
     ]
    }
