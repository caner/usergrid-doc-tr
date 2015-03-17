# Android için Turkcell Usergrid'e Özel Ayarlar

Usergrid Android SDK'sını indirip projenize ekledikten sonra, Turkcell Usergrid'ini kullanacağınız için yapmanız gereken bazı değişiklikler var:

1. DataClient nesnesini Turkcell Usergrid'e özel ayarlamak (Gerekli)
2. Özel Application sınıfı oluşturmak (İsteğe bağlı)


## DataClient nesnesini Turkcell Usergrid'e özel ayarlamak
Usergrid'in özelliklerini kullanabilmeniz için [DataClient](http://sdk.apigee.com/docs/reference/sdk/android/com/apigee/sdk/data/client/DataClient.html) sınıfını örneklemeniz (instantiate) gerekir. Her şey ```DataClient``` etrafında döner. ```DataClient```, Usergrid API ile yapılabilen birçok işlemi Android uygulamasında yapabilmeniz için metotlar sunar.

Turkcell Usergrid'ini kullanmak için DataClient sınıfını çok küçük değişikliklerle özelleştirmeniz gerekir. Normalde
```DataClient client=new DataClient();```
ile DataClient'i örneklediğinizde API_URL olarak belirlenen ve Usergrid sunucusunu işaret eden yol "api.usergrid.com" olur. Bunu Turkcell Usergrid'e uygun hale getirmek için "client" nesnesi üzerinde 
```client.setApiUrl("https://api.turkcell.com.tr/usergrid");```
şeklinde değişiklik yapmanız yeterli. İçinde organizasyon, uygulama ve API_URL bilgisi olan bir yapılandırıcı örneği şöyle olabilir:
```DataClient client = new DataClient("gy","kitaplik","https://api.turkcell.com.tr/usergrid",this);```
Bir kere DataClient'i oluşturduktan sonra istediğiniz yerde kullanabilirsiniz.

> **NOT:** Son parametre olan ```this```, [Context](https://developer.android.com/reference/android/content/Context.html) tipindeki parametredir. DataClient'i [Activity](https://developer.android.com/reference/android/app/Activity.html) içinde oluşturuyorsanız Activity, [Service](https://developer.android.com/reference/android/app/Service.html) içinde oluşturuyorsanız Service veya [Fragment](https://developer.android.com/reference/android/app/Fragment.html) içinde oluşturuyorsanız Fragment Context'ine erişirsiniz ve o context kapsamında DataClient ile çalışırsınız. Uygulama çapında bir Context'e ulaşıp DataClient'i her yerde kullanmak için "Özel Application Sınıfı Oluşturmak" maddesine bakabilirsiniz.

## Özel Application Sınıfı Oluşturmak
DataClient sınıfına çok sık ihtiyacınız olabileceği için bu sınıftan ürettiğiniz nesneyi, uygulamanın her türlü bağlamında (context) erişilebilecek bir yere almanız işlerinizi epey kolaylaştıracaktır. Bu çapta bir [Context](https://developer.android.com/reference/android/content/Context.html)'e uygulama süreci (process) boyunca ulaşabilmek için [Application](https://developer.android.com/reference/android/app/Application.html) sınıfından bir sınıf türetmelisiniz. Böylece gerektiğinde [getApplicationContext()](https://developer.android.com/reference/android/content/Context.html#getApplicationContext()) metodu ile uygulama genelinde bağlama ulaşabilirsiniz.

Android'te Application sınıfından bir sınıf türetmek çok kolay:
```
import android.app.Application;

public class KitaplikApplication extends Application {

}
```

Bu sınıfta tanımladığınız alanlara (local field) Android uygulamanızın her yerinden erişebilirsiniz. Android işletim sisteminin uygulamanız için çalıştırdığı iş sürecinde (process) bu sınıfın örneği daima yer alır. Dolayısıyla [Activity](https://developer.android.com/reference/android/app/Activity.html) veya [Fragment](https://developer.android.com/reference/android/app/Fragment.html)'tan buraya erişebildiğiniz gibi bir [Service](https://developer.android.com/reference/android/app/Service.html)'ten de erişebilirsiniz.


**NOT**: Oluşturduğunuz bu yeni Application alt sınıfını AndroidManifest.xml'de <application> elemanında da ilan etmeyi unutmamalısınız. Örneğin:
```
<application
        android:name=".KitaplikApplication"
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme">
```
Şimdi biraz daha özelleştirilmiş bir KitaplikApplication sınıfı oluşturalım:

```
import java.util.ArrayList;

import org.tcellgy.yuztemelesereclipse.model.Kitap;

import android.app.Application;
import android.content.Context;

import com.apigee.sdk.data.client.DataClient;

public class KitaplikApplication extends Application{

	private static final String TCELLUSERGRID_API_URL = "https://api.turkcell.com.tr/usergrid";
	private static final String ORGNAME = "gy";
	private static final String APPNAME = "kitaplik";
	private DataClient mDataClient;

	public ArrayList getKitapList() {
		final ArrayList mKitaps = new ArrayList<>();

        ...

		return mKitaps;
	}

	public void initClient(Context _context) {
		this.mDataClient = new DataClient(ORGNAME, APPNAME,
				TCELLUSERGRID_API_URL, _context);
	}

	public DataClient getDataClient() {
		if (null != this.mDataClient) {
			return this.mDataClient;
		}
		return null;
	}
}
```

Bu örnekteki ```initClient()``` ve ```getDataClient()``` metotları önemli. initClient() metodunda Turkcell Usergrid'ine özel ```DataClient```'i oluşturuyoruz. ```getDataClient()``` metodunu ise daha sonra uygulamanızın istediğiniz yerinden çağırabilirsiniz. Örneğin:

```
import android.app.Activity;
import android.os.Bundle;
import android.util.Log;

import com.apigee.sdk.data.client.DataClient;
import com.apigee.sdk.data.client.callbacks.ApiResponseCallback;
import com.apigee.sdk.data.client.response.ApiResponse;

public class MainActivity extends Activity {


	private KitaplikApplication application;
	private static final String TAG = "MainActivity";

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);

		application = (KitaplikApplication) getApplication();

		if (application.getDataClient() != null) {
			getKitaplar();
		}

	}

	private void getKitaplar() {
		DataClient client = application.getDataClient();
		client.getEntitiesAsync("roman", "*", new ApiResponseCallback() {
			
			@Override
			public void onException(Exception _arg0) {
				Log.e(TAG, "romanlar gelmedi. bir hata var");
			}
			
			@Override
			public void onResponse(ApiResponse _arg0) {
				Log.v(TAG, "romanlar getirildi");
			}
		});
	}
}
```

**UYARI**: Usergrid Android SDK'sı çoğu işlemi İnternet'e bağlanarak yapar. Bu nedenle AndroidManifest.xml dosyasında gerekli izinleri tanımlamalıyız:

```<uses-permission android:name="android.permission.INTERNET"/>```