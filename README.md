
# BnaSDK Cordova *0.2.6*

O BNA-SDK é um projeto Ericsson para coleta de dados celulares baseados em localização GPS.

## Getting Started

Para iniciar, é necessário obter o `cordova-plugin-bna` que deverá ser disponibilizado pela Ericsson.

### Prerequisites

A fim de manter a privacidade, a Ericsson não se utiliza de repositórios como NPM, Bintray, Sonatype, Jitpack, etc.
Por este motivo, é necessário realizar, apenas nesta versão, a configuração do ambiente para total funcionamento
da biblioteca.

O BnaSDK oferece suporte a partir do Android SDK 16.
Por este motivo, sua aplicação deverá ter suporte mínimo para versão 16 do Android SDK.

```
android {
	...
    minSdkVersion 16
    ...
}
```

**ATENÇÃO:**
Se houver conflito nas versões das bibliotecas com.android.support, 

![All com.android.support libraries problem](https://cdn-images-1.medium.com/max/1000/1*vx7iP2SD6IlTrAoCJCr3oQ.png)
apenas adicione a versão mais recente da biblioteca com.android.design.

Até o release desta versão, a versão mais recente é:

```
implementation 'com.android.support:design:27.1.1'
```

Se houver problemas com MultiDex devido ao limite de quantidade de métodos ter sido ultrapassada, siga este procedimento para habilitar o suporte a **MultiDex** clicando [AQUI](https://developer.android.com/studio/build/multidex.html)

Adicione ao seu arquivo `AndroidManifest.xml` os seguintes meta-dados:

```
<application>
...
    <meta-data
        android:name="com.ericsson.bna.application.name"
        android:value="Your App Name"/>

    <meta-data
        android:name="com.ericsson.bna.partner.name"
        android:value="Your Partner Name"/>
    
    <meta-data
        android:name="com.ericsson.bna.application.domain"
        android:value="Your App Domain"/>
    
    <meta-data
        android:name="com.ericsson.bna.application.key"
        android:value="Your App Key"/>
</application>
```

**ATENÇÃO:**
Se sua aplicação  é distribuída para domínios diferentes, identifique o domínio informando este meta-dado: `com.ericsson.bna.application.domain` no arquivo `AndroidManifest.xml`, conforme exemplificado acima.

Para mais detalhes a respeito do Domínio de Aplicação, contate a Ericsson.

### Installing and Running BNA Service

Após configurado o ambiente, baixe e instale o plugin `cordova-plugin-bna` no projeto.

Siga estes passos:

Baixe o plugin e mova a pasta para o diretório desejado.
Exemplo: C\Users\Me\Desktop ou /home/me/desktop/

Acesse a pasta raiz do seu projeto pelo prompt de comando e utilize o comando:

`cordova plugin add /diretorio/do/plugin/BNASdkCordova/`

***Caso esteja usando outra distribuição como, por exemplo, PhoneGap execute o comando equivalente***

Em seguida use o comando para construir a aplicação:

`cordova clean android`
`cordova build android`

**Após o build da aplicação com sucesso, continue com os passos a seguir.**

Na classe que herda de `Application` inicie o BnaSDK:

```
@Override
public class MyApp extends Application {
    @Override
    public void onCreate() {
        super.onCreate();  
        BnaSDK.create(getApplicationContext());  
    }
}
```


Com isso você configura todos os serviços do BNA para a sua aplicação, mas, não inicia.
Para iniciar, chame o método `go(Context aContext)` na sua Activity principal.

**A partir daqui utilize as classes equivalentes do Cordova ou da sua distribuição. Ex.: *CordovaActivity***
```
public class MainActivity extends AppCompatActivity {
  
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
  
        BnaSDK.instance().go(getApplicationContext());  
    }
    
}
```

**ATENÇÃO:**
Caso sua aplicação ofereça suporte a versões Marshmallow ou superior, solicite as permissões em tempo de execução:

```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
  
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
        if ((  
            (ActivityCompat.checkSelfPermission(getApplicationContext(), Manifest.permission.READ_PHONE_STATE)) +  
            (ActivityCompat.checkSelfPermission(getApplicationContext(), Manifest.permission.ACCESS_COARSE_LOCATION)) +  
            (ActivityCompat.checkSelfPermission(getApplicationContext(), Manifest.permission.ACCESS_FINE_LOCATION)) +  
            (ActivityCompat.checkSelfPermission(getApplicationContext(), Manifest.permission.RECORD_AUDIO)) +  
            (ActivityCompat.checkSelfPermission(getApplicationContext(), Manifest.permission.WRITE_EXTERNAL_STORAGE)) +  
            (ActivityCompat.checkSelfPermission(getApplicationContext(), Manifest.permission.READ_CONTACTS))  
        ) != PackageManager.PERMISSION_GRANTED) {  
             ActivityCompat.requestPermissions(this, new String[]{
			Manifest.permission.READ_PHONE_STATE,  
    			Manifest.permission.ACCESS_COARSE_LOCATION,
    			Manifest.permission.ACCESS_FINE_LOCATION,
			Manifest.permission.RECORD_AUDIO,  
			Manifest.permission.WRITE_EXTERNAL_STORAGE,  
    			Manifest.permission.READ_CONTACTS
  		}, 1992);
	} else {
            startSDK();  
        }  
    } else {
        startSDK();  
  }
}

@Override
public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        
    // Verifique se todas as permissões foram concedidas e inicie o serviço BNA
    startSDK();
}

private void startSDK() {
    BnaSDK.instance().go(getApplicationContext());
}
```

**ATENÇÃO:**
Caso o usuário negue alguma permissão, verifique novamente se é possível solicitar quando a aplicação for executada pois algumas features do serviço BNA irão apenas funcionar se todas as permissões forem concedidas.

Caso o usuário não permita a localização GPS, por exemplo, o serviço do BNA não deverá ser iniciado. E, caso esteja em execução, deverá ser parado utilizando o método `stop(Context aContext)`.

Sempre que possível, requisite as permissões negadas para que o seu serviço BNA obtenha coletas de dados corretamente.

Para saber mais a respeito de Requisição de Permissões Android, clique [AQUI](https://developer.android.com/training/permissions/requesting.html)

## Built With

* [RxJava](https://github.com/ReactiveX/RxJava) - Reactive Extensions for the JVM used
* [ORMLite](http://ormlite.com/) - Object Relational Mapping used
* [Google PubSub](https://cloud.google.com/pubsub/) - Used to ingest event streams
* [Android Job](https://github.com/evernote/android-job) - Used to Jobs Management

## Authors

* **Bruno Freitas** - [GitHub](http://brfreitas.github.io/)
* **Pedro Moura** - [GitHub](https://github.com/pedrodimoura)
* **André Lucas**  - [GitHub](https://github.com/andrelucasti)
