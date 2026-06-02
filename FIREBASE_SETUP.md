# 🔧 دليل إعداد Firebase للنظام

## الخطوة 1: إنشاء مشروع Firebase

1. اذهب إلى [Firebase Console](https://console.firebase.google.com/)
2. اضغط على **"إنشاء مشروع جديد"** أو **"Create a project"**
3. أدخل اسم المشروع (مثلاً: `exam-system`)
4. اختر البلد وأكمل الخطوات

## الخطوة 2: الحصول على بيانات الاتصال

بعد إنشاء المشروع:

1. اذهب إلى **⚙️ Project Settings** (إعدادات المشروع)
2. اختر التبويب **"Your apps"** (تطبيقاتك)
3. اضغط على **"Web"** (الويب) أو أضف تطبيق ويب جديد
4. انسخ البيانات التالية:
   - `apiKey`
   - `authDomain`
   - `projectId`
   - `storageBucket`
   - `messagingSenderId`
   - `appId`

## الخطوة 3: تحديث بيانات Firebase في الملف

افتح ملف `exam_system.html` وابحث عن هذا الجزء:

```javascript
const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "YOUR_PROJECT.firebaseapp.com",
    projectId: "YOUR_PROJECT_ID",
    storageBucket: "YOUR_PROJECT.appspot.com",
    messagingSenderId: "YOUR_SENDER_ID",
    appId: "YOUR_APP_ID"
};
```

استبدل القيم بالبيانات التي نسختها:

```javascript
const firebaseConfig = {
    apiKey: "AIzaSyDxxxxxxxxxxxxxxxxxx",
    authDomain: "exam-system-xxxxx.firebaseapp.com",
    projectId: "exam-system-xxxxx",
    storageBucket: "exam-system-xxxxx.appspot.com",
    messagingSenderId: "1234567890",
    appId: "1:1234567890:web:abcdef123456"
};
```

## الخطوة 4: إعداد Firestore Database

1. في Firebase Console، اختر **Firestore Database** من القائمة الجانبية
2. اضغط على **"Create Database"** (إنشاء قاعدة بيانات)
3. اختر **"Start in test mode"** (البداية في وضع الاختبار)
4. اختر موقع جغرافي قريب منك
5. اضغط **"Create"**

### إعداد القواعد (Rules)

بعد إنشاء Firestore، اذهب إلى تبويب **Rules** وضع القواعد التالية:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /exam_results/{document=**} {
      allow create: if true;
      allow read, write, delete: if true;
    }
    match /exams/{document=**} {
      allow create: if true;
      allow read: if true;
    }
  }
}
```

⚠️ **ملاحظة أمان:** هذه القواعد للاختبار والتطوير فقط. قبل النشر في الإنتاج، استخدم قواعد أكثر أماناً.

## الخطوة 5: تفعيل المصادقة المجهولة

1. في Firebase Console، اختر **Authentication**
2. اذهب إلى تبويب **Sign-in method**
3. فعّل **Anonymous** (تسجيل دخول مجهول)

## الخطوة 6: الاختبار

1. افتح ملف `exam_system.html` في المتصفح
2. أضف بعض الأسئلة
3. ابدأ الامتحان وأنهه
4. يجب أن ترى رسالة "تم حفظ النتيجة بنجاح"
5. في Firebase Console، اذهب إلى Firestore وستجد جمع بيانات `exam_results`

## 📊 البيانات المحفوظة

عند انتهاء الطالب من الامتحان، يتم حفظ:

```json
{
  "studentName": "أحمد محمد علي",
  "examTitle": "اختبار اللغة العربية",
  "score": 85,
  "correct": 17,
  "wrong": 3,
  "skipped": 0,
  "total": 20,
  "totalTime": 600,
  "timestamp": "2024-01-15T10:30:00Z",
  "answers": [0, 1, true, "إجابة", ...],
  "questions": [...]
}
```

## 🔍 عرض النتائج

اضغط على زر **"📊 عرض جميع النتائج"** لعرض جميع النتائج المسجلة.

## 🛠️ الدوال المتاحة

### حفظ النتيجة
```javascript
saveResultToFirebase(resultData);
```

### استرجاع جميع النتائج
```javascript
const allResults = await getAllResults();
```

### استرجاع نتائج طالب معين
```javascript
const studentResults = await getStudentResults('أحمد محمد علي');
```

### حذف نتيجة
```javascript
deleteResultFromFirebase(resultId);
```

### تحديث نتيجة
```javascript
updateResultInFirebase(resultId, { score: 90 });
```

## ⚠️ حل المشاكل

### المشكلة: "PERMISSION_DENIED"
**الحل:** تحقق من قواعل Firestore وتأكد أنها تسمح بالقراءة والكتابة.

### المشكلة: لا يظهر "تم حفظ النتيجة"
**الحل:** 
- تحقق من اتصالك بالإنترنت
- تأكد من تحديث `firebaseConfig` بالبيانات الصحيحة
- افتح Console (F12) وابحث عن الأخطاء

### المشكلة: "Firebase is not defined"
**الحل:** تأكد أن سكريبتات Firebase محملة بشكل صحيح في الملف.

## 📱 الوصول للبيانات من تطبيق آخر

يمكنك عمل لوحة تحكم منفصلة لعرض نتائج جميع الطلاب:

```html
<button onclick="loadAllStudentResults()">عرض النتائج</button>

<script>
async function loadAllStudentResults() {
    const results = await getAllResults();
    console.log('جميع النتائج:', results);
    
    // عرض البيانات في جدول
    results.forEach(result => {
        console.log(`${result.studentName}: ${result.score}%`);
    });
}
</script>
```

---

✅ تم! الآن يمكنك استخدام النظام مع Firebase لحفظ واسترجاع البيانات تلقائياً!