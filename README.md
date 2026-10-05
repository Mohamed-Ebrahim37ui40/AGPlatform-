# Agro University Platform

منصة زراعية أوفلاين قابلة للتثبيت (PWA) تجمع ملخصات محاضرات **PepsiCo Agro University**.

## الميزات

- ✅ تعمل **أوفلاين** بالكامل بعد أول زيارة
- ✅ قابلة للتثبيت كتطبيق على الهاتف أو الكمبيوتر (PWA)
- ✅ بحث وتصنيف حسب الموضوع
- ✅ عارض داخلي للمحاضرات بدون مغادرة الصفحة
- ✅ قائمة المحاضرات من ملف `lectures.json` — سهلة التحديث

## التشغيل السريع

1. افتح المجلد `agro-platform` بأي خادم محلي بسيط، مثلاً:
   ```bash
   cd agro-platform
   python3 -m http.server 8080
   ```
2. افتح المتصفح على: `http://localhost:8080`
3. من قائمة المتصفح اختر **تثبيت التطبيق** / **Add to Home Screen**

> ملاحظة: ميزات التثبيت والـ Service Worker تحتاج خادماً محلياً أو HTTPS (لا تعمل من `file://`).

## إضافة محاضرة جديدة تلقائياً من GitHub

### الطريقة الموصى بها (يدوية بسيطة وسريعة)

1. ضع ملف HTML الجديد داخل مجلد `lectures/`
2. أضف عنصراً جديداً في `lectures.json`:

```json
{
  "id": "unique-id",
  "title": "عنوان المحاضرة",
  "subtitle": "وصف قصير",
  "file": "اسم_الملف.html",
  "category": "أحد التصنيفات الموجودة",
  "tags": ["وسم1", "وسم2"],
  "speaker": "اسم المتحدث",
  "icon": "🌱"
}
```

3. ادفع التغييرات إلى GitHub.
4. إذا استخدمت **GitHub Pages**، المنصة تتحدث تلقائياً بعد الدمج.
5. داخل المنصة اضغط زر **🔄 تحديث** لتحميل القائمة الجديدة.

### التصنيفات الحالية

- استراتيجية  
- بذور  
- أصناف  
- إنتاج  
- تغذية وري  
- تخزين وجودة  
- أمراض وآفات  
- تكنولوجيا  
- تدريب  

يمكنك إضافة تصنيف جديد بحرية — سيظهر تلقائياً في الفلاتر.

### ربط GitHub Pages (اختياري)

1. ادفع مجلد `agro-platform` إلى مستودع GitHub.
2. من Settings → Pages اختر الفرع والمجلد `/` أو `/docs`.
3. المنصة تصبح متاحة على: `https://USERNAME.github.io/REPO/`
4. أي ملف HTML جديد + تحديث `lectures.json` يظهر للجميع بعد الـ push.

### أتمتة كاملة (متقدمة)

يمكنك إضافة GitHub Action يقوم بمسح مجلد `lectures/` ويُحدّث `lectures.json` تلقائياً عند كل push. مثال مبسط:

```yaml
# .github/workflows/update-lectures.yml
name: Update lectures list
on:
  push:
    paths: ['lectures/**']
jobs:
  update:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Rebuild lectures.json
        run: |
          # سكربت بايثون أو node يقرأ الملفات ويكتب JSON
          python scripts/rebuild_lectures.py
      - uses: stefanzweifel/git-auto-commit-action@v5
        with:
          commit_message: "chore: auto-update lectures.json"
```

## هيكل المجلدات

```
agro-platform/
├── index.html          ← الصفحة الرئيسية (PWA)
├── lectures.json       ← قائمة المحاضرات (المصدر الوحيد للقائمة)
├── manifest.json       ← إعدادات التطبيق
├── sw.js               ← Service Worker للأوفلاين
├── icons/              ← أيقونات التثبيت
├── lectures/           ← ملفات HTML للمحاضرات
└── README.md
```

## ملاحظات

- كل محاضرة هي ملف HTML مستقل يعمل أوفلاين.
- الـ Service Worker يخزّن الملفات تلقائياً عند زيارتها لأول مرة.
- زر **تحديث** يعيد تحميل `lectures.json` من الشبكة (إن توفرت) ثم يستخدم الكاش.
