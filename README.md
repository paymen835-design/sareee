# السريع ون - تطبيق توصيل الطعام

## نظرة عامة
تطبيق توصيل طعام شامل يتضمن:
- تطبيق العملاء
- لوحة تحكم المديرين
- تطبيق السائقين
- نظام إدارة المطاعم والطلبات

## إعداد المشروع

### 1. متطلبات النظام
- Node.js 18+ 
- PostgreSQL (Neon Database)
- Supabase (للتخزين فقط)

### 2. إعداد قاعدة البيانات

#### أ. إعداد Neon Database
1. إنشاء حساب في [Neon](https://neon.tech)
2. إنشاء قاعدة بيانات جديدة
3. نسخ `DATABASE_URL` من لوحة تحكم Neon

#### ب. إعداد Supabase للتخزين
1. إنشاء حساب في [Supabase](https://supabase.com)
2. إنشاء مشروع جديد
3. الذهاب إلى Settings > API
4. نسخ:
   - `Project URL` (SUPABASE_URL)
   - `service_role` key (SUPABASE_SERVICE_ROLE_KEY)
   - `anon public` key (VITE_SUPABASE_ANON_KEY)

### 3. إعداد متغيرات البيئة
```bash
cp .env.example .env
```

تحديث ملف `.env` بالقيم الصحيحة:
```env
# قاعدة البيانات الرئيسية (Neon)
DATABASE_URL="postgresql://username:password@ep-xxx.us-east-1.aws.neon.tech/dbname"

# Supabase للتخزين فقط
SUPABASE_URL="https://your-project.supabase.co"
SUPABASE_SERVICE_ROLE_KEY="your-service-role-key"
VITE_SUPABASE_URL="https://your-project.supabase.co"
VITE_SUPABASE_ANON_KEY="your-anon-key"

# إعدادات الخادم
PORT=5000
NODE_ENV=development
SESSION_SECRET="your-secure-session-secret"
DEFAULT_ADMIN_PASSWORD="your-secure-admin-password"
```

### 4. تثبيت التبعيات وإعداد قاعدة البيانات
```bash
# تثبيت التبعيات
npm install

# إنشاء جداول قاعدة البيانات
npm run db:generate
npm run db:push

# إضافة البيانات الافتراضية
npm run db:setup
```

### 5. تشغيل التطبيق
```bash
# للتطوير
npm run dev

# للإنتاج
npm run build
npm start
```

## نظام المصادقة المبسط

### تسجيل الدخول للمديرين
- **الرابط**: `/admin-login`
- **البيانات الافتراضية**:
  - البريد الإلكتروني: `admin@alsarie-one.com`
  - كلمة المرور: `777146387`

### تسجيل الدخول للسائقين
- **الرابط**: `/driver-login`
- **البيانات الافتراضية**:
  - رقم الهاتف: `+967771234567`
  - كلمة المرور: `777146387`

## نظام رفع الصور

### إعداد Supabase Storage
1. **إنشاء Buckets تلقائياً**: يتم إنشاء buckets التخزين تلقائياً عند بدء الخادم
2. **Buckets المستخدمة**:
   - `restaurant-images`: صور المطاعم
   - `menu-item-images`: صور الوجبات
   - `offer-images`: صور العروض
   - `category-images`: صور التصنيفات
   - `general-images`: صور عامة

### كيفية عمل النظام
1. **رفع الصورة**: العميل يرفع الصورة عبر `/api/images/upload`
2. **معالجة الخادم**: الخادم يستلم الصورة ويرفعها إلى Supabase
3. **الحصول على الرابط**: Supabase يعيد رابط عام للصورة
4. **حفظ في قاعدة البيانات**: يتم حفظ الرابط في قاعدة بيانات Neon

### مميزات النظام
- ✅ رفع آمن للصور
- ✅ تحسين تلقائي للصور
- ✅ حذف الصور القديمة
- ✅ دعم صيغ متعددة (JPG, PNG, WebP, GIF)
- ✅ حد أقصى 5MB للملف الواحد
- ✅ معاينة فورية للصور

## هيكل المشروع

```
├── client/                 # تطبيق العميل (React)
│   ├── src/
│   │   ├── components/     # المكونات المشتركة
│   │   ├── pages/         # صفحات التطبيق
│   │   ├── context/       # إدارة الحالة
│   │   └── lib/           # مكتبات مساعدة
├── server/                # خادم Express
│   ├── routes/           # مسارات API
│   ├── auth.ts           # نظام المصادقة
│   ├── supabase.ts       # إعدادات Supabase
│   └── imageUpload.ts    # نظام رفع الصور
├── shared/               # الملفات المشتركة
│   └── schema.ts         # مخططات قاعدة البيانات
└── drizzle/             # ملفات الهجرة
```

## الأمان والحماية

### نظام المصادقة
- مصادقة مبسطة بدون تشفير معقد
- التحقق من البيانات مباشرة من قاعدة البيانات
- رموز مميزة بسيطة للجلسات

### حماية الصور
- التحقق من نوع الملف
- حد أقصى لحجم الملف
- رفع آمن إلى Supabase Storage
- حذف تلقائي للصور غير المستخدمة

## استكشاف الأخطاء

### مشاكل شائعة وحلولها

#### 1. خطأ في الاتصال بقاعدة البيانات
```bash
# التحقق من DATABASE_URL
echo $DATABASE_URL

# إعادة تشغيل قاعدة البيانات
npm run db:push
```

#### 2. خطأ في رفع الصور
```bash
# التحقق من إعدادات Supabase
echo $SUPABASE_URL
echo $SUPABASE_SERVICE_ROLE_KEY

# إعادة إنشاء buckets
# سيتم إنشاؤها تلقائياً عند إعادة تشغيل الخادم
```

#### 3. خطأ في تسجيل الدخول
- التأكد من وجود المدير في قاعدة البيانات
- التحقق من كلمة المرور الافتراضية: `777146387`
- التأكد من تشغيل `npm run db:setup`

## المساهمة
1. Fork المشروع
2. إنشاء branch جديد للميزة
3. Commit التغييرات
4. Push إلى Branch
5. إنشاء Pull Request

## الترخيص
MIT License