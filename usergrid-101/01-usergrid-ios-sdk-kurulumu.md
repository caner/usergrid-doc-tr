# Usergrid iOS SDK'sının Kurulumu

Aşağıda, Turkcell Usergrid fonksiyonlarını iOS ortamında kullanabilmek için yazılmış Usergrid SDK'sını kendi uygulamanıza nasıl entegre edeceğiniz açıklanmaktadır.

## Başlamadan Önce
Usergrid iOS SDK'sının kullanımı için aşağıdakiler gereklidir:

Turkcell Usergrid hizmetimizi kullanmak için Geleceği Yazanlar hesabınızla oturum açmanız ya da hesabınız yoksa [yeni bir hesap](gelecegiyazanlar.turkcell.com.tr/user/register) oluşturmanız yeterlidir. Esasen Usergrid iOS SDK'sını kurmak zorunda değilsiniz ancak Turkcell Usergrid hizmetlerini kullanan bir mobil uygulama oluştururken SDK'ya ihtiyacınız olacak.

Geleceği Yazanlar'da oturum açıp [Turkcell Usergrid Yönetici Portalı](mobildb.turkcell.com.tr/portal/)'na girince korumalı bir "oyun alanı"nda denemeler yapabilmeniz için sandbox (kum havuzu) isimli bir uygulamanın geldiğini göreceksiniz. 

![](https://gelecegiyazanlar.turkcell.com.tr/sites/default/files/icerik/sandbox_uygulama.jpg)

Bu uygulamada tüm kimlik doğrulama işleri kapalı geldiği için bol bol deneme yapmanız ve Usergrid'i hızlıca öğrenmek için kullanmanız tavsiye edilir.

## Usergrid iOS SDK'sını İndirin
Usergrid iOS SDK'sını [buradan](www.apigee.com/docs/content/ios-sdk-redirect) indirin ve inen ZIP dosyasını ayıklayın. ZIP dosyasını ayıkladığınızda aşağıdaki yapıyı göreceksiniz:


- **/lib** Projenizi içeri taşımak (import) için SDK çerçevesi
- **/new-project-template**	Usergrid özelliklerini destekleyen uygulamalar geliştirmek için gerekli SDK dosyalarını entegre eden basit bir şablon proje
- **/samples**	Usergrid özelliklerini keşfetmek için çalıştırabileceğiniz örnek uygulamalar
- **/source**	Sınıfların fonksiyonlarına göre gruplandırıldığı SDK için kaynak kodu


## ApigeeiOSSDK.framework'ü entegre edin

**Var olan proje için**

Zaten Xcode iOS projeniz bulunuyorsa, normalde yapacağınız gibi projenize ekleyin:

1. SDK geliştirme çatısının yerini belirleyin ve projenize ekleyin. Örneğin dosyayı aşağıdaki yolda bulacaksınız:
```sdk_root>/bin/ApigeeiOSSDK.framework```
2. Project Navigator (Proje Gezgini) bölümünde proje dosyanıza ve ardından Build Phases (Oluşturma Aşamaları) sekmesine tıklayın. Ardından Link Binary With Libraries bölümünü genişletin.
3. Apigee iOS SDK'sını projenizle birleştirin:
	3. ApigeeiOSSDK.framework'ü Xcode'un oluşturduğu Frameworks grubuna sürükleyin
	ya da
	3. Link Binary With Libraries bölümünün altındaki "+" düğmesine tıklayın ve Add Other'ı seçin.
	3. ApigeeiOSSDK.framework'ü içeren dizine gidin ve ApigeeiOSSDK.framework klasörünü seçin.

**Yeni proje için**

Yeni çalışmaya başlıyorsanız (projeniz yoksa) SDK içinde bulunan proje şablonunu kullanarak başlayabilirsiniz. SDK'nın sunduğu özellikleri (feature) içeren şablonlar yer almaktadır.

1. Proje şablonunun yerini açılan SDK'da tespit edin. Aşağıdaki dizinde olması gerekmektedir:
```sdk_root>/new-project-template```
2. Proje şablonu dizininde Apigee App Services iOS Template.xcodeproj dizinindeki proje şablonunu açın.
3. README dosyasına bakarak şablon hakkında bilgi edinin.

## Gerekli iOS geliştirme çatılarını ekleyin

Aşağıdaki iOS geliştirme çatılarının (framework) projenizin parçası olduğundan emin olun. Bunları eklemek için Link Binary With Libraries grubunun altındaki "+" düğmesine tıklayın, Xcode tarafından bulunan ve eklemek istediğiniz geliştirme çatısını seçin ve Add düğmesine tıklayın.

- CoreLocation.framework
- CoreTelephony.framework
- Security.framework
- SystemConfiguration.framework
- UIKit.framework
- QuartzCore.framework

## "Other Linker Flags" Değerini Güncelleyin

Build Settings (İnşa Ayarları) panelinde Linking bölümünde "Other Linker Flags" kısmına aşağıdakini ekleyin:

```-ObjC -all_load```

Bu değerin hem DEBUG hem RELEASE inşa ayarında yapıldığına emin olmalısınız.

## SDK'yı başlatın

ApigeeClient sınıfı SDK'yı başlatır. Bunu yapmak için Usergrid Yönetici Portalı'nda yer alan uygulama ve organizasyon adı bilgisi gerekmektedir.

1. SDK'yı içeri aktarın:
SDK'yı içeri aktarmak etmek için aşağıdakini kaynak kodunuza ekleyin:
```​#import <ApigeeiOSSDK/Apigee.h>```
2. AppDelegate.h dosyasına aşağıdaki özellikleri girin:
```@property (strong, nonatomic) ApigeeClient *apigeeClient; //SDK'yı başlatacak nesne
@property (strong, nonatomic) ApigeeDataClient *dataClient;	//Verilerle ilgili metotlar için iOS tarafındaki veri nesnesi```