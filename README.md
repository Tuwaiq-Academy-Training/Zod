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


## يتم تجميع رموز حالة استجابة HTTP في خمس فئات

| الرمز  | الحالة |
|:---:|:------:|
|  1XX  |رموز الحالة: طلبات إعلامية |--- |
|  2XX   |رموز الحالة: الطلبات الناجحة|
|  3XX   | رموز الحالة: عمليات إعادة التوجيه|
|  4XX   |رموز الحالة: أخطاء العميل|
|  5XX   |رموز الحالة: أخطاء الخادم|

## رمز الحال  1XX طلبات اعلامية

 
| الرمز  | الحالة |
|:---:|:------:|
|  100   |متابعة: كل شيء على ما يرام حتى الآن ويجب على العميل متابعة الطلب أو تجاهله إذا كان قد انتهى بالفعل. |--- |
|  101   | تبديل البروتوكولات: البروتوكول الذي يقوم الخادم بالتبديل إليه حسب طلب العميل الذي أرسل الرسالة بما في ذلك عنوان طلب الترقية|
|  102   | قيد المعالجة: وافق الخادم على الطلب الكامل ، لكنه لا يزال قيد المعالجة.|
|  103   |تلميحات مبكرة: السماح لوكيل المستخدم ببدء تحميل الموارد مسبقًا أثناء تحضير الخادم للاستجابة.|


## رمز الحالة 2XX الطلبات الناجحة

| الرمز  | الحالة |
|:---:|:------:|
|  200   |حسنًا: طلب ناجح. |--- |
|  201   |تم الإنشاء: أقر الخادم بالمورد الذي تم إنشاؤه|
|  202   | مقبول: تم استلام طلب العميل ولكن الخادم لا يزال يقوم بمعالجته.|
|  203   |معلومات غير موثوقة: الاستجابة التي أرسلها الخادم إلى العميل ليست هي نفسها عندما أرسلها الخادم.|
|  204   |لا يوجد محتوى: قام الخادم بمعالجة الطلب ولكنه لا يقدم أي محتوى.|
|  205   |إعادة تعيين المحتوى: يجب على العميل تحديث نموذج المستند|
|  206   |المحتوى الجزئي: يرسل الخادم جزءًا فقط من المورد.|
|  207   |الحالة المتعددة: نص الرسالة التالي هو افتراضيًا رسالة XML ويمكن أن يحتوي على عدد من رموز الاستجابة المنفصلة.|

## رمز الحالة 3XX عمليات اعادة التوجية

| الرمز  | الحالة |
|:---:|:------:|
|  300   |اختيارات متعددة: الطلب الذي قدمه العميل له عدة استجابات محتملة. |--- |
|  301    | تم النقل بشكل دائم: يخبر الخادم العميل أن المورد الذي يبحثون عنه قد تم نقله بشكل دائم إلى عنوان URL آخر. سيتم إعادة توجيه جميع المستخدمين والروبوتات إلى عنوان URL الجديد. إنه رمز حالة مهم جدًا لتحسين محركات البحث.|
|  302   |  تم العثور عليها: تم نقل موقع ويب أو صفحة إلى عنوان URL مختلف مؤقتًا. إنه رمز حالة آخر يتعلق بـ SEO.|
|  303   | انظر أخرى: يخبر هذا الرمز العميل أن الخادم لا يعيد توجيههم إلى المورد المطلوب ولكن إلى صفحة أخرى.|
|  304   |غير معدّل: لم يتم تغيير المورد المطلوب منذ الإرسال السابق.|
|  305   |استخدام الوكيل: لا يمكن للعميل الوصول إلى المورد المطلوب إلا من خلال وكيل تم تقديمه في الاستجابة.|
|  307   |إعادة توجيه مؤقتة: يخبر الخادم العميل أن المورد الذي يبحث عنه قد تمت إعادة توجيهه مؤقتًا إلى عنوان URL آخر. إنها ذات صلة بأداء تحسين محركات البحث.|
|  308   |إعادة التوجيه الدائم: يخبر الخادم العميل أن المورد الذي يبحث عنه قد تمت إعادة توجيهه مؤقتًا إلى عنوان URL آخر. |

## رمز الحالة 4XX  أخطاء العميل 

| الرمز  | الحالة |
|:---:|:------:|
|  400   |طلب خاطئ: يرسل العميل طلبًا يحتوي على بيانات غير كاملة أو بيانات سيئة الإنشاء أو بيانات غير صالحة. |--- |
|  401   |غير مصرح به: التفويض مطلوب للعميل للوصول إلى المورد المطلوب.|
|  403   |ممنوع: المورد الذي يحاول العميل الوصول إليه محظور.|
|  404   |غير موجود: يمكن الوصول إلى الخادم ، ولكن الصفحة المحددة التي يبحث عنها العميل ليست كذلك.|
|  405   |الطريقة غير مسموح بها: استلم الخادم الطلب وتعرف عليه ، لكنه رفض طريقة الطلب المحددة.|
|  406   | غير مقبول: لا يدعم موقع الويب أو تطبيق الويب طلب العميل ببروتوكول معين.|
|  407   |مطلوب مصادقة الوكيل: رمز الحالة هذا مشابه لـ 401 غير مصرح به. الاختلاف الوحيد هو أن التفويض يجب أن يتم بواسطة وكيل.|
|  408   |مهلة الطلب: انتهت صلاحية الطلب الذي أرسله العميل إلى خادم موقع الويب.|
|  409   | التعارض: يتعارض الطلب الذي تم إرساله مع العمليات الداخلية للخادم.|
|  410   | ذهب: تم ​​مسح المورد الذي يريد العميل الوصول إليه نهائيًا.

## رمز الحالة 5XX أخطاء الخادم 

| الرمز  | الحالة |
|:---:|:------:|
|  500  |خطأ داخلي في الخادم: واجه الخادم موقفًا لا يمكنه التعامل معه أثناء معالجة طلب العميل. |--- |
|  501   | لم يتم التنفيذ: لا يعرف الخادم طريقة الطلب التي أرسلها العميل أو يمكنه حلها.|
|  502   | مدخل غير صالح: كان الخادم يعمل كبوابة أو خادم وكيل وتلقى رسالة غير صالحة من خادم وارد.|
|  503   |الخدمة غير متوفرة: قد يكون الخادم معطلاً ولا يمكن معالجة طلب العميل. يعد رمز حالة HTTP أحد أكثر مشكلات الخادم شيوعًا التي يمكنك مواجهتها على الويب.|
|  511   | مطلوب مصادقة الشبكة: يحتاج العميل إلى المصادقة على الشبكة قبل أن يتمكن من الوصول إلى المورد.|
