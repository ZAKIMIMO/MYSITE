# دليل بناء تطبيق ShopInk للأندرويد

## المتطلبات الأساسية

### 1. تثبيت Android Studio
- تحميل وتثبيت Android Studio من الموقع الرسمي
- تثبيت Android SDK (API Level 33 أو أحدث)
- تثبيت Android Build Tools
- إعداد متغيرات البيئة ANDROID_HOME و ANDROID_SDK_ROOT

### 2. تثبيت Java Development Kit (JDK)
- تثبيت JDK 11 أو أحدث
- إعداد متغير البيئة JAVA_HOME

### 3. تثبيت Node.js و npm
- تثبيت Node.js الإصدار 16 أو أحدث
- التأكد من تثبيت npm

## خطوات بناء التطبيق

### الخطوة 1: إعداد المشروع
```bash
# الانتقال إلى مجلد المشروع
cd shopink-app

# تثبيت التبعيات
npm install

# تثبيت Capacitor
npm install @capacitor/core @capacitor/cli @capacitor/android

# تهيئة Capacitor
npx cap init ShopInk com.shopink.app
```

### الخطوة 2: بناء التطبيق للويب
```bash
# بناء التطبيق للإنتاج
npm run build

# نسخ الملفات إلى Capacitor
npx cap copy
```

### الخطوة 3: إضافة منصة الأندرويد
```bash
# إضافة منصة الأندرويد
npx cap add android

# مزامنة التغييرات
npx cap sync android
```

### الخطوة 4: فتح المشروع في Android Studio
```bash
# فتح المشروع في Android Studio
npx cap open android
```

### الخطوة 5: بناء APK في Android Studio

#### أ. إعداد التوقيع (للنشر)
1. في Android Studio، اذهب إلى Build > Generate Signed Bundle / APK
2. اختر APK
3. إنشاء keystore جديد أو استخدام موجود
4. ملء معلومات التوقيع:
   - Key store path: مسار ملف keystore
   - Key store password: كلمة مرور keystore
   - Key alias: اسم المفتاح
   - Key password: كلمة مرور المفتاح

#### ب. بناء APK
1. اختر build variant: release
2. اختر destination folder
3. انقر على Finish

### الخطوة 6: اختبار APK
```bash
# تثبيت APK على جهاز أو محاكي
adb install app-release.apk

# أو استخدام Android Studio لتثبيت مباشر
```

## ملفات مهمة للنشر

### 1. AndroidManifest.xml
موقع: `android/app/src/main/AndroidManifest.xml`

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.shopink.app">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme"
        android:usesCleartextTraffic="true">

        <activity
            android:exported="true"
            android:launchMode="singleTask"
            android:name="com.shopink.app.MainActivity"
            android:theme="@style/AppTheme.NoActionBarLaunch">

            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>

        </activity>

        <provider
            android:name="androidx.core.content.FileProvider"
            android:authorities="${applicationId}.fileprovider"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/file_paths"></meta-data>
        </provider>
    </application>

    <!-- Permissions -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />

</manifest>
```

### 2. build.gradle (Module: app)
موقع: `android/app/build.gradle`

```gradle
android {
    namespace "com.shopink.app"
    compileSdk 34

    defaultConfig {
        applicationId "com.shopink.app"
        minSdk 22
        targetSdk 34
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
        aaptOptions {
             // Files and dirs to omit from the packaged assets dir, modified to accommodate modern web apps.
             // Default: https://android.googlesource.com/platform/frameworks/base/+/282e181b58cf72b6ca770dc7ca5f91f135444502/tools/aapt/AaptAssets.cpp#61
            ignoreAssetsPattern '!.svn:!.git:!.ds_store:!*.scc:.*:!CVS:!thumbs.db:!picasa.ini:!*~'
        }
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
}
```

### 3. الأيقونات والموارد
- أيقونة التطبيق: `android/app/src/main/res/mipmap-*/ic_launcher.png`
- شاشة البداية: `android/app/src/main/res/drawable/splash.png`
- الألوان: `android/app/src/main/res/values/colors.xml`
- النصوص: `android/app/src/main/res/values/strings.xml`

## نصائح مهمة

### 1. الأمان
- استخدم HTTPS فقط في الإنتاج
- احم ملف keystore بكلمة مرور قوية
- لا تشارك معلومات التوقيع

### 2. الأداء
- فعل ProGuard للتطبيقات الإنتاجية
- اضغط الصور والموارد
- اختبر على أجهزة مختلفة

### 3. التوافق
- اختبر على إصدارات أندرويد مختلفة
- تأكد من دعم الشاشات المختلفة
- اختبر على أجهزة بمواصفات منخفضة

## استكشاف الأخطاء

### مشاكل شائعة:
1. **خطأ في بناء Gradle**: تحقق من إصدارات SDK
2. **مشاكل في التوقيع**: تأكد من صحة معلومات keystore
3. **مشاكل في الأذونات**: تحقق من AndroidManifest.xml
4. **مشاكل في الشبكة**: تأكد من إعدادات CORS

### سجلات مفيدة:
```bash
# عرض سجلات الجهاز
adb logcat

# عرض الأجهزة المتصلة
adb devices

# تثبيت APK
adb install -r app-release.apk
```

## الخطوات التالية للنشر في Google Play

1. إنشاء حساب مطور في Google Play Console
2. رفع APK الموقع
3. ملء معلومات التطبيق
4. إضافة لقطات الشاشة والوصف
5. إعداد سياسة الخصوصية
6. إرسال للمراجعة

---

**ملاحظة**: هذا الدليل يغطي الخطوات الأساسية. قد تحتاج إلى تخصيصات إضافية حسب متطلبات التطبيق المحددة.

