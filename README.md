## محتوى اليوم الخامس


## ال Zod



#### مقدمة في zod

تم تصميم zod ليكون مناسبًا للمطورين قدر الإمكان. الهدف هو القضاء على نوع البيانات المكررة. باستخدام zod ، تقوم بتعريف المدقق مرة واحدة وسيقوم Zod تلقائيًا بالاستدلال على نوع TypeScript الثابت. من السهل تكوين أنواع أبسط في هياكل بيانات معقدة.

#### بعض الجوانب الرائعة الأخرى:

- التبعيات الصفرية
- يعمل في Node.js وجميع المتصفحات الحديثة
- واجهة موجزة وقابلة للتسلسل
- يعمل مع جافا سكريبت عادي أيضًا! لا تحتاج إلى استخدام TypeScript.


#### متطلبات zod

- ال TypeScript 4.1+!


يجب عليك تمكين الوضع المتشدد في ملف tsconfig.json الخاص بك. هذا هو أفضل ممارسة لجميع مشاريع TypeScript.

      // tsconfig.json
      {
        // ...
        "compilerOptions": {
          // ...
          "strict": true
        }
      }
#### ال Node/npm

لتثبيت zod v8

    npm install zod       # npm
    yarn add zod          # yarn
    pnpm add zod          # pnpm

على عكس Node ، يعتمد Deno على عمليات استيراد عناوين URL المباشرة بدلاً من مدير الحزم مثل NPM. Zod متاح على deno.land/x. يمكن استيراد أحدث إصدار كما يلي:

      import { z } from "https://deno.land/x/zod/mod.ts";


يمكنك أيضًا تحديد إصدار معين:

      import { z } from "https://deno.land/x/zod@v3.16.1/mod.ts";


#### الاستخدام الأساسي

إنشاء مخطط سلسلة بسيط

     import { z } from "zod";

     // creating a schema for strings
     const mySchema = z.string();

     // parsing
     mySchema.parse("tuna"); // => "tuna"
     mySchema.parse(12); // => throws ZodError

     // "safe" parsing (doesn't throw error if validation fails)
     mySchema.safeParse("tuna"); // => { success: true; data: "tuna" }
     mySchema.safeParse(12); // => { success: false; error: ZodError }

إنشاء مخطط كائن

     import { z } from "zod";

     const User = z.object({
       username: z.string(),
     });

     User.parse({ username: "Ludwig" });

     // extract the inferred type
     type User = z.infer<typeof User>;
     // { username: string }
     
     
 #### Primitives
 
         import { z } from "zod";

       // primitive values
       z.string();
       z.number();
       z.bigint();
       z.boolean();
       z.date();

       // empty types
       z.undefined();
       z.null();
       z.void(); // accepts undefined

       // catch-all types
       // allows any value
       z.any();
       z.unknown();

       // never type
       // allows no values
       z.never();
       
       
  #### Literals
       
     const tuna = z.literal("tuna");
     const twelve = z.literal(12);
     const tru = z.literal(true);

     // retrieve literal value
     tuna.value; // "tuna"
 
 #### Strings
 يتضمن Zod عددًا قليلاً من عمليات التحقق الخاصة بالسلسلة.
 
       z.string().max(5);
       z.string().min(5);
       z.string().length(5);
       z.string().email();
       z.string().url();
       z.string().uuid();
       z.string().cuid();
       z.string().regex(regex);
       z.string().startsWith(string);
       z.string().endsWith(string);

       // trim whitespace
       z.string().trim();

      // deprecated, equivalent to .min(1)
      z.string().nonempty();

      // optional custom error message
      z.string().nonempty({ message: "Can't be empty" });
      
  يمكنك تخصيص بعض رسائل الخطأ الشائعة عند إنشاء مخطط سلسلة.
  
      const name = z.string({
      required_error: "Name is required",
      invalid_type_error: "Name must be a string",
    });
    
عند استخدام طرق التحقق من الصحة ، يمكنك تمرير وسيطة إضافية لتقديم رسالة خطأ مخصصة.

    z.string().min(5, { message: "Must be 5 or more characters long" });
    z.string().max(5, { message: "Must be 5 or fewer characters long" });
    z.string().length(5, { message: "Must be exactly 5 characters long" });
    z.string().email({ message: "Invalid email address" });
    z.string().url({ message: "Invalid url" });
    z.string().uuid({ message: "Invalid UUID" });
    z.string().startsWith("https://", { message: "Must provide secure URL" });
    z.string().endsWith(".com", { message: "Only .com domains allowed" });
    
 #### Numbers
 
 يمكنك تخصيص رسائل خطأ معينة عند إنشاء مخطط رقم.
 
     const age = z.number({
      required_error: "Age is required",
      invalid_type_error: "Age must be a number",
    });
    
 #### يتضمن Zod عددًا قليلاً من عمليات التحقق من صحة الأرقام المحددة.
 
      z.number().gt(5);
     z.number().gte(5); // alias .min(5)
     z.number().lt(5);
     z.number().lte(5); // alias .max(5)
     
     z.number().int(); // value must be an integer

     z.number().positive(); //     > 0
     z.number().nonnegative(); //  >= 0
     z.number().negative(); //     < 0
     z.number().nonpositive(); //  <= 0

     z.number().multipleOf(5); // Evenly divisible by 5. Alias .step(5)
     
     
     
#### اختياريًا ، يمكنك تمرير وسيطة ثانية لتقديم رسالة خطأ مخصصة.

      z.number().lte(5, { message: "this👏is👏too👏big" });

    
 #### NaNs

يمكنك تخصيص رسائل خطأ معينة عند إنشاء مخطط نانوي.

     const isNaN = z.nan({
       required_error: "isNaN is required",
       invalid_type_error: "isNaN must be not a number",
     });
     
#### Booleans

يمكنك تخصيص بعض رسائل الخطأ عند إنشاء مخطط منطقي.

     const isActive = z.boolean({
       required_error: "isActive is required",
       invalid_type_error: "isActive must be a boolean",
     });
     
#### Dates

استخدم z.date () للتحقق من صحة مثيلات التاريخ.

     z.date().safeParse(new Date()); // success: true
     z.date().safeParse("2022-01-12T00:00:00.000Z"); // success: false
     
     
يمكنك تخصيص بعض رسائل الخطأ عند إنشاء مخطط التاريخ.

     const myDateSchema = z.date({
       required_error: "Please select a date and time",
       invalid_type_error: "That's not a date!",
     });
     
يوفر Zod عددًا قليلاً من عمليات التحقق من الصحة الخاصة بالتاريخ.

     z.date().min(new Date("1900-01-01"), { message: "Too old" });
     z.date().max(new Date(), { message: "Too young!" });
     
#### Supporting date strings


لكتابة مخطط يقبل إما سلسلة التاريخ أو التاريخ ، استخدم z.preprocess.

     const dateSchema = z.preprocess((arg) => {
       if (typeof arg == "string" || arg instanceof Date) return new Date(arg);
     }, z.date());
     type DateSchema = z.infer<typeof dateSchema>;
     // type DateSchema = Date

     dateSchema.safeParse(new Date("1/12/22")); // success: true
     dateSchema.safeParse("2022-01-12T00:00:00.000Z"); // success: true


#### مثال لأنشاء تسجيل مستخدم جديد 

      import { object, string, TypeOf } from 'zod';

      export const registerSchema = object({
          body: object({
              firstname: string({
                  required_error: 'First name is required',
              }),
              lastname: string({
                  required_error: 'Last name is required',
              }),
              password: string({
                  required_error: 'Password is required',
              }).min(6),
              email: string({
                  required_error: 'Email is required',
              }).email('Please enter a valid email'),
          }),
      });

      export const loginSchema = object({
          body: object({
              email: string({ required_error: 'Email is required' }).email(
                  'Please enter a valid email'
              ),
              password: string({ required_error: 'Password is required' }).min(
                  6,
                  'Minimum 6 characters'
              ),
          }),
      });

      export type RegisterSchemaType = TypeOf<typeof registerSchema>['body'];
      export type LoginSchemaType = TypeOf<typeof loginSchema>['body'];
      
  في المثال اعلاه نلاحظ أن تم تحديد المدخلات ونوع المدخل string
  
  وفي المثال التالي طريقة اصطياد Errors
  
          const validateResource = (schema: AnyZodObject) => (
            req: Request,
            res: Response,
            next: NextFunction
        ) => {
            try {
                schema.parse({
                    body: req.body,
                    query: req.query,
                    params: req.params,
                });

                next();
            } catch (error: any) {
                res.status(400).json({
                    message: error.errors[0].message,
                    status: 400,
                });
            }
        };
  
  وكما نلاحظ ان  في  try تم تمرير مايحتوية في body وفي  حال الفشل فسوف ينتقل بشكل تلقائي لـcatch 
 
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
