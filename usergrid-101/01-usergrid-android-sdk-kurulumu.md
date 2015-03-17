# Usergrid Android SDK'sının Kurulumu
Bu eğitim içeriğinde Android ortamı için Usergrid SDK'sı kullanarak Usergrid'in fonksiyonlarını Android uygulamanıza nasıl entegre edeceğiniz açıklanmaktadır.

## Başlamadan Önce
Usergrid Android SDK'sını kullanabilmek için aşağıdakiler gerekir:

Turkcell Usergrid hizmetimizi kullanmak için Geleceği Yazanlar hesabınızla oturum açmanız ya da hesabınız yoksa yeni bir hesap oluşturmanız yeterlidir. Turkcell Usergrid hizmetlerini kullanmak için SDK'yı kurmanız gerekmemektedir ancak bir mobil uygulama oluştururken SDK'ya ihtiyacınız olacak.

Geleceği Yazanlar'da oturum açıp [Turkcell Usergrid Yönetici Portalı](https://mobildb.turkcell.com.tr/portal/)'na girince korumalı bir "oyun alanı"nda denemeler yapabilmeniz için sandbox (kum havuzu) isimli bir uygulamanın geldiğini göreceksiniz. 

![](https://gelecegiyazanlar.turkcell.com.tr/sites/default/files/icerik/sandbox_uygulama.jpg)

Bu uygulamada tüm kimlik doğrulama işleri kapalı geldiği için bol bol deneme yapmanız ve Usergrid'i hızlıca öğrenmek için kullanmanız tavsiye edilir.

Android için ilk defa uygulama geliştiriyorsanız aşağıdakilere ihtiyaç duyacaksınız:
- **Eclipse IDE:** Biliyorsunuz ki Eclipse IDE Android uygulama geliştirmenin tek yolu değildir ancak işinizi kolaylaştıracak araçlar içerir. Eclipse IDE'nin kurulumuyla ilgili Android 101 eğitimlerindeki "Eclipse IDE'sinin Kullanımı" konulu eğitim içeriğine bakabilirsiniz.
- **Android SDK:** Android uygulama geliştirme araçları içermektedir. Kurmadıysanız Android 201 eğitimlerindeki "ADT Geliştirme Ortamı'nın Kurulması" ve "IDE'nin Tanıtılması ve Android Bileşenlerinin Eklenmesi" eğitimlerine bakabilirsiniz.

## Usergrid Android SDK'sını indirin

Android için Usergrid SDK'sını [buradan](http://www.apigee.com/docs/content/android-sdk-redirect) indirin ve ZIP dosyasını açın. ZIP dosyasını açtığınızda aşağıdaki yapıyı göreceksiniz:

- **/lib**: Projenizin "Build path"ine eklemek için SDK'nın jar dosyaları
- **/new-project-template**: Usergrid özelliklerini destekleyen uygulamalar geliştirmek için gerekli SDK dosyalarını entegre eden basit bir şablon proje
- **/samples**: Usergrid özelliklerini keşfetmek için çalıştırabileceğiniz örnek uygulamalar
- **/source**: Usergrid SDK'sının kaynak kodu


## SDK'yı projenize entegre edin

Usergrid özelliklerini uygulamanıza entegre etmek için işe Usergrid SDK'sını projenize ekleyerek başlayabilirsiniz. Halihazırda bir Android projeniz varsa, normalde yapacağınız gibi Usergrid SDK'sını da projenize entegre edebilirsiniz.

Android 4.0 (veya sonraki sürüm) projelerinde **apigee-android-<version>.jar** dosyasını projenizde /libs klasörüne kopyalayın.

**Yeni proje için:**

Yeni çalışmaya başlıyorsanız (projeniz yoksa) SDK içinde bulunan proje şablonunu kullanarak başlayabilirsiniz. Şablonda SDK özellikleri için destek de yer almaktadır. Proje şablonu şu dizinde olmalı: ```<sdk_root>/new-project-template```

## AndroidManifest.xml'deki izinleri güncelleyin
Aşağıdaki İnternet izinlerini, hali hazırda eklenmemişse, uygulamanızın AndroidManifest.mxl dosyasına ekleyin. INTERNET dışında tüm diğer izinler ihtiyaç duyuldukça eklenebilir.

```
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```

## SDK'nın ilk ayarlamalarını yapın
Usergrid Uygulama Hizmetleri SDK'sının ilk ayarlarını yapmak için ```ApigeeClient``` sınıfını örneklemeniz (instantiate) gerekir. Bu adımı gerçekleştirmek için çeşitli yöntemler mevcuttur ancak biz aşağıdakini öneriyoruz:

1. [Application](developer.android.com/reference/android/app/Application.html) sınıfından türemiş bir alt sınıf oluşturun ve ApigeeClient sınıfı tipinde bir değişkeni de üye değişken olarak ekleyin. Bu üye için getter/setter tanımlamayı da ihmal etmeyin.
```
import android.app.Application;

import com.apigee.sdk.ApigeeClient;

public class UsergridGirisApplication extends Application {
    private ApigeeClient mDataClient;

    public UsergridGirisApplication() {
    }

    public ApigeeClient getmDataClient() {
        return mDataClient;
    }

    public void setmDataClient(ApigeeClient mDataClient) {
        this.mDataClient = mDataClient;
    }
}
```
2. Oluşturduğunuz bu Application alt sınıfını AndroidManifest.xml'de de ilan edin. Örneğin:
```
<application
        android:allowBackup="true"
        android:name=".UsergridGirisApplication"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.example.usergridgiris.MainActivity"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
```
3. İlk Activity sınıfınızdaki onCreate metodunda ApigeeClient sınıfını örnekleyin ve ilk ayarlarını yapın:
```
import com.apigee.sdk.ApigeeClient;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);        
    
    String ORGNAME = "organizasyon_adi";
    String APPNAME = "uygulama_adi";
    
    ApigeeClient apigeeClient = new ApigeeClient(ORGNAME,APPNAME,this.getBaseContext());

    //Kendi application nesnemizden ApigeeClient'i alalım
    girisApp = (UsergridGirisApplication) getApplication();
    girisApp.setApigeeClient(apigeeClient);            
}
```
Bu, ApigeeClient'ın Application sınıfınız için kullanılabilir olmasını sağlayacaktır.