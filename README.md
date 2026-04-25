# Shagaf — Zone Management System

نظام إدارة الزونات والمدربات لشركة شغف، يدعم عدة موظفين بصلاحيات مختلفة.

## التشغيل لأول مرة

1. افتح `index.html` في المتصفح (أو ارفعه على أي استضافة ساكنة).
2. سجل الدخول بـ:
   - **اسم المستخدم:** `shagaf2024`
   - **كلمة المرور:** `shagaf2024`
3. أول دخول راح ينشئ حساب الأدمن في Firebase ويحفظ البيانات الافتراضية تلقائياً.
4. **مهم:** غيّر كلمة مرور الأدمن من Firebase Console بعد أول دخول.

## إضافة موظف جديد

من تبويب **مستخدمين** → **إضافة مستخدم**:
- اسم مستخدم (للدخول)
- كلمة مرور (٦ أحرف على الأقل)
- اسم بالعربي/الإنجليزي
- اختر الصلاحيات المناسبة

الموظف الجديد يقدر يسجل دخول مباشرة باسم المستخدم وكلمة المرور.

## كيف يعمل النظام

- **المصادقة:** Firebase Authentication (Email/Password) — نولّد إيميل من اسم المستخدم: `username@shagaf.local`
- **قاعدة البيانات:** Cloud Firestore — كل البيانات تحت مجموعة `app`
- **مشاركة:** كل الموظفين يشتغلون على نفس البيانات، التحديث يظهر بعد إعادة تحميل الصفحة

## بنية البيانات في Firestore

```
app/zones    -> {list:[...]}
app/coaches  -> {list:[...]}
app/teams    -> {list:[...]}
app/users    -> {map:{username:userObj,...}}
app/cities   -> {list:[...]}
app/config   -> {currentCity, lang, pricing, emoji_system}
app/history  -> {list:[...]}
```

> ملاحظة: كائن `userObj` لا يحتوي على كلمة المرور — كلمات المرور محفوظة في Firebase Auth فقط (مشفرة).

## قواعد الأمان

ملف `firestore.rules` يحتوي على القواعد الحالية. لتطبيقها:

1. افتح [Firebase Console](https://console.firebase.google.com/project/shagaf-system/firestore/rules)
2. الصق محتوى الملف
3. اضغط **Publish**

## الصلاحيات المتاحة

| المفتاح | الوصف |
|---------|-------|
| `view_map` | عرض الخريطة والبحث |
| `view_coaches` / `edit_coaches` / `delete_coaches` | المدربات |
| `view_zones` / `edit_zones` / `delete_zones` | الزونات |
| `view_stats` | عرض الإحصائيات |
| `view_emojis` / `edit_emojis` | الإيموجي |
| `view_prices` / `edit_prices` | الأسعار |
| `view_cities` / `edit_cities` | المدن |
| `import_export` | استيراد/تصدير البيانات |
| `view_history` / `clear_history` | سجل التعديلات |
| `manage_teams` | إدارة الفرق |
| `manage_users` | إدارة المستخدمين |

دور **admin** يحصل على كل الصلاحيات تلقائياً.

## ملاحظات تقنية

- **حذف موظف من النظام:** يلغي وصوله للتطبيق (يحذف من `app/users`) لكن حساب Firebase Auth الخاص به يبقى. لحذفه نهائياً يحتاج المسؤول يدخل [Firebase Console → Authentication](https://console.firebase.google.com/project/shagaf-system/authentication/users) ويحذفه يدوياً.
- **تغيير كلمة مرور موظف من قِبل المدير:** يحتاج Cloud Functions (مش متوفر حالياً). الحل المؤقت: المدير يحذف الموظف ويعيد إضافته بكلمة مرور جديدة، أو يستخدم زر "نسيت كلمة المرور" مستقبلاً.
- **كلمة مرور الموظف المنسية:** الحل الحالي الوحيد هو الحذف وإعادة الإضافة من المدير.
