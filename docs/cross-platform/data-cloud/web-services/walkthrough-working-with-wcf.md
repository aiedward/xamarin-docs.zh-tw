---
title: "逐步解說-使用 WCF"
description: "這個逐步解說將說明如何使用 Xamarin 建置行動應用程式可以使用 WCF web 服務使用 BasicHttpBinding 類別。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D0E83342-2E79-4D25-BD57-43718F5628C4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/17/2018
ms.openlocfilehash: ee39851070be2f302125162400d2214c732faeec
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2018
---
# <a name="walkthrough---working-with-wcf"></a>逐步解說-使用 WCF

_這個逐步解說將說明如何使用 Xamarin 建置行動應用程式可以使用 WCF web 服務使用 BasicHttpBinding 類別。_


它是要能夠與後端系統通訊的行動應用程式的常見的需求。 有許多選項和後端架構，其中的一個選項[Windows Communication Foundation](http://msdn.microsoft.com/en-us/library/ms731082.aspx) (WCF)。 本逐步解說將提供範例 Xamarin 的行動應用程式如何使用 WCF 服務使用`BasicHttpBinding`類別。 逐步解說包含下列主題：

1.  **建立 WCF 服務**-在這一節中，我們將建立有兩種方法非常基本的 WCF 服務。 第一個的方法會將字串參數，而另一個方法會接受 C# 物件。 本節也將討論如何設定以允許遠端存取的 WCF 服務開發人員的工作站。
1.  **建立 Xamarin.Android 應用程式**-一旦建立 WCF 服務之後，我們將建立簡單的 Xamarin.Android 應用程式將會使用 WCF 服務。 本節將討論如何建立 WCF 服務 proxy 類別，以促進使用 WCF 服務的通訊。
1.  **建立 Xamarin.iOS 應用程式**-本教學課程的最後一個部分會建立簡單的 Xamarin.iOS 應用程式將會使用 WCF 服務。

<a name="Requirements" />

## <a name="requirements"></a>需求

本逐步解說假設您已熟悉建立和使用 WCF 服務。

<a name="Creating_a_WCF_Service" />

## <a name="creating-a-wcf-service"></a>建立 WCF 服務

我們之前的第一個工作是建立行動應用程式與通訊的 WCF 服務。

1. 啟動 Visual Studio 2017，並建立新的專案。
1. 在**新專案**對話方塊中，選取**WCF > WCF 服務程式庫**範本，並將方案命名`HelloWorldService`:

    ![](walkthrough-working-with-wcf-images/new-wcf-service.png "建立新的 WCF 服務程式庫")

1. 在**方案總管 中**，加入新的類別，名為`HelloWorldData`至專案：

    ```csharp
        using System.Runtime.Serialization;

        namespace HelloWorldService
        {
            [DataContract]
            public class HelloWorldData
            {
                [DataMember]
                public bool SayHello { get; set; }

                [DataMember]
                public string Name { get; set; }

                public HelloWorldData()
                {
                    Name = "Hello ";
                    SayHello = false;
                }
            }
        }
    ```


1. 在**方案總管 中**，重新命名`IService1.cs`至`IHelloWorldService.cs`，並重新命名`Service1.cs`至`HelloWorldService.cs`。
1. 在**方案總管 中**，開啟`IHelloWorldService.cs`並取代為下列程式碼的程式碼：

    ```csharp
        using System.ServiceModel;

        namespace HelloWorldService
        {
            [ServiceContract]
            public interface IHelloWorldService
            {
                [OperationContract]
                string SayHelloTo(string name);

                [OperationContract]
                HelloWorldData GetHelloData(HelloWorldData helloWorldData);
            }
        }
    ```
  
    這項服務提供兩種方法，一個可接受的字串參數，而另一個會採用.NET 物件。

1. 在**方案總管 中**，開啟`HelloWorldService.cs`並取代為下列程式碼的程式碼：

    ```csharp
        using System;

        namespace HelloWorldService
        {
            public class HelloWorldService : IHelloWorldService
            {
                public HelloWorldData GetHelloData(HelloWorldData helloWorldData)
                {
                    if (helloWorldData == null)
                        throw new ArgumentException("helloWorldData");

                    if (helloWorldData.SayHello)
                        helloWorldData.Name = "Hello World to {helloWorldData.Name}";

                    return helloWorldData;
                }

                public string SayHelloTo(string name)
                {
                    return "Hello World to you, {name}";
                }
            }
        }
    ```

1. 在**方案總管 中**，開啟`App.config`，更新`name`屬性`<service>` 節點，`contract`屬性`<endpoint>` 節點，和`baseAddress`屬性`<add>`節點：

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            ...
            <services>
              <service name="HelloWorldService.HelloWorldService">
                <endpoint address="" binding="basicHttpBinding" contract="HelloWorldService.IHelloWorldService">
                  <identity>
                    <dns value="localhost" />
                  </identity>
                </endpoint>
                <endpoint address="mex" binding="mexHttpBinding" contract="IMetadataExchange" />
                <host>
                  <baseAddresses>
                    <add baseAddress="http://localhost:8733/Design_Time_Addresses/HelloWorldService/" />
                  </baseAddresses>
                </host>
              </service>
            </services>
            ...
        </configuration>
    ```

1. 建置並執行 WCF 服務。 服務會裝載的 WCF 測試用戶端：

    ![](walkthrough-working-with-wcf-images/hosted-wcf-service.png "測試用戶端中執行的 WCF 服務")

1. 使用 WCF 測試用戶端，執行，啟動瀏覽器並瀏覽至 WCF 服務的端點：

    ![](walkthrough-working-with-wcf-images/wcf-service-browser.png "WCF 服務瀏覽器資訊 頁面")

> [!IMPORTANT]
> 下一節時，才需要，如果您需要接受遠端連線的 Windows 10 的工作站上。 如果您有一個替代的平台，用來部署 WCF 服務，可以略過一節。

<a name="Allow_Remote_Access_to_IIS_Express" />

## <a name="configuring-remote-access-to-iis-express"></a>為 IIS Express 設定 「 遠端存取

當連線只能來自本機電腦，則裝載在本機 WCF 已足夠。 不過，遠端裝置 （例如 Android 裝置或 iPhone） 不需要本機 WCF 服務的任何存取。 因此，本節會說明如何設定成接受遠端連接的 Windows 10 和 IIS Express:

1.  **接受遠端連線設定 IIS Express** -這個步驟包含編輯的 IIS Express 來接受特定的連接埠上的遠端連線設定檔，然後設定 IIS Express 才能接受連入流量的規則。
1.  **加入 Windows 防火牆的例外狀況**-您必須開啟連接埠通過 Windows 防火牆遠端應用程式可以用來與 WCF 服務進行通訊。

    您必須知道您的工作站的 IP 位址。 為了執行此範例假設我們工作站的 IP 位址 192.168.1.143。

1. 讓我們先設定 IIS Express 接聽外部的要求。 我們這樣可以編輯組態檔的 IIS Express 在`[solutiondirectory]\.vs\config\applicationhost.config`，如下列螢幕擷取畫面所示：

    [![](walkthrough-working-with-wcf-images/image05.png "我們這樣可以編輯組態檔在 solutiondirectory.vsconfigapplicationhost.config，IIS Express 中這個螢幕擷取畫面所示")](walkthrough-working-with-wcf-images/image05.png#lightbox)


    找出`site`名稱項目`HelloWorldWcfHost`。 它看起來應該類似下列的 XML 程式碼片段：

    ```xml
        <site name="HelloWorldWcfHost" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="\\vmware-host\Shared Folders\tom\work\xamarin\code\private-samples\webservices\HelloWorld\HelloWorldWcfHost" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:8733:localhost" />
            </bindings>
        </site>
    ```
 
    我們需要加入另一個`binding`開放連接埠 8734 外部流量。 加入下列 XML 以`bindings`項目，取代您自己的 IP 位址的 IP 位址：

    ```xml
    <binding protocol="http" bindingInformation="*:8734:192.168.1.143" />
    ```
    
    這會設定 IIS Express 接受來自任何外部 IP 位址的電腦上的連接埠 8734 上的遠端 IP 位址的 HTTP 流量。 上述程式碼片段假設執行 IIS Express 的電腦的 IP 位址是 192.168.1.143。 變更之後，請`bindings`項目應該看起來如下：

    ```xml
        <site name="HelloWorldWcfHost" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="\\vmware-host\Shared Folders\tom\work\xamarin\code\private-samples\webservices\HelloWorld\HelloWorldWcfHost" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:8733:localhost" />
                <binding protocol="http" bindingInformation="*:8734:192.168.1.143" />
            </bindings>
        </site>
    ```

1. 接下來，我們必須設定 IIS Express 接受 8734 連接埠上的連入連線。 啟動系統管理命令提示字元中，及執行此命令：

    `> netsh http add urlacl url=http://192.168.1.143:9608/ user=everyone`

1. 最後一個步驟是設定 Windows 防火牆以允許外部連接埠 8734 上的流量。 系統管理命令提示字元中，執行下列命令：

    `> netsh advfirewall firewall add rule name="IISExpressXamarin" dir=in protocol=tcp localport=8734 profile=private remoteip=localsubnet action=allow`

    此命令將 Windows 10 工作站相同的子網路上允許從所有裝置的連接埠 8734 的連入流量。

您已建立非常基本的 WCF 服務裝載於 IIS Express，將會接受從其他裝置或我們的子網路上電腦的連入連線。 您可以測試此外執行應用程式及瀏覽`http://localhost:8733/Design_Time_Addresses/HelloWorldService/`在工作站上和`http://192.168.1.143:8734/Design_Time_Addresses/HelloWorldService/`從子網路上的另一部電腦。

若要允許 IIS Express 保持執行，並提供服務，請關閉**編輯後繼續**選項*專案屬性 > Web > 偵錯工具*。

## <a name="creating-a-proxy-for-the-web-service"></a>建立 Web 服務 Proxy

必須先為 WCF 服務，建立 web 服務 proxy，應用程式可以取用的服務。 執行下列工作即可達成這點：

1. 加入名為的.NET 標準類別庫`HelloWorldServiceProxy`，並刪除任何專案中的類別。
1. 執行`HelloWorldService`專案。
1. 與`HelloWorldService`執行專案中，加入新**已連接服務**加入專案中，使用**Microsoft WCF Web 服務參考提供者**。
1. 在**服務端點** 索引標籤**設定 WCF Web 服務參考** 對話方塊中，按一下 **探索**按鈕，刪除`mex`從的 偵測到結尾中的端點**URI**下拉式清單中，輸入`HelloWorldServiceProxy`為**命名空間**，按一下**下一步** 按鈕。
1. 在**資料類型選項** 索引標籤**設定 WCF Web 服務參考** 對話方塊中，接受預設值，依序按一下**下一步** 按鈕。
1. 在**用戶端選項** 索引標籤**設定 WCF Web 服務參考** 對話方塊中，確定**公用**核取方塊已選取，然後按一下**完成**  按鈕。
1. 建置`HelloWorldServiceProxy`專案。

> [!NOTE]
> 建立使用 Microsoft WCF Web 服務參考提供者，在 Visual Studio 2017 proxy 的替代方式是使用 ServiceModel Metadata Utility Tool (svcutil.exe)。 如需詳細資訊，請參閱[ServiceModel Metadata Utility Tool (Svcutil.exe)](https://docs.microsoft.com/en-us/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)。

<a name="Creating_a_Xamarin_Android_Application" />

## <a name="creating-a-xamarinandroid-application"></a>建立 Xamarin.Android 應用程式

WCF 服務 proxy 以供 Xamarin.Android 應用程式，如下所示：

1. 在 Visual Studio 中，將新的 Android 專案加入方案並將其命名`HelloWorld.Android`。
1. 在`HelloWorld.Android`專案中，將參考加入`HelloWorldServiceProxy`專案，以及參考`System.ServiceModel`命名空間。
1. 在**方案總管 中**，開啟`Resources/layout/main.axml`，並以下列 XML 取代現有的 XML:

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                  android:orientation="vertical"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent">
            <LinearLayout
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="0px"
                    android:layout_weight="1">
                <Button
                        android:id="@+id/sayHelloWorldButton"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:text="@string/say_hello_world" />
                <TextView
                        android:text="Large Text"
                        android:textAppearance="?android:attr/textAppearanceLarge"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:id="@+id/sayHelloWorldTextView" />
            </LinearLayout>
            <LinearLayout
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="0px"
                    android:layout_weight="1">
                <Button
                        android:id="@+id/getHelloWorldDataButton"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:text="@string/get_hello_world_data" />
                <TextView
                        android:text="Large Text"
                        android:textAppearance="?android:attr/textAppearanceLarge"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:id="@+id/getHelloWorldDataTextView" />
            </LinearLayout>
        </LinearLayout>
    ```
    
    下列螢幕擷取畫面顯示在設計工具中的 UI:

    [![](walkthrough-working-with-wcf-images/image09.png "這是此 UI 設計工具中的外觀的螢幕擷取畫面")](walkthrough-working-with-wcf-images/image09.png#lightbox)
    
1. 在**方案總管 中**，開啟`Resources/values/Strings.xml`並加入下列 XML:

    ```xml
    <string name="say_hello_world">Say Hello World</string>
    <string name="get_hello_world_data">Get Hello World data</string>
    ```
    
1. 在**方案總管 中**，開啟`MainActivity.cs`，並以下列程式碼取代現有的程式碼：

    ```csharp
        [Activity(Label = "HelloWorld.Android", MainLauncher = true)]
        public class MainActivity : Activity
        {
            static readonly EndpointAddress Endpoint = new EndpointAddress("<insert_WCF_service_endpoint_here>");

            HelloWorldServiceClient _client;
            Button _getHelloWorldDataButton;
            TextView _getHelloWorldDataTextView;
            Button _sayHelloWorldButton;
            TextView _sayHelloWorldTextView;
            ...
        }
    ```

    取代`<insert_WCF_service_endpoint_here>`與 WCF 端點的位址。

1. 在`MainActivity.cs`，修改`OnCreate`方法，使其包含下列程式碼：

    ```csharp
        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(bundle);

            SetContentView(Resource.Layout.Main);

            InitializeHelloWorldServiceClient();

            // This button will invoke the GetHelloWorldData - the method that takes a C# object as a parameter.
            _getHelloWorldDataButton = FindViewById<Button>(Resource.Id.getHelloWorldDataButton);
            _getHelloWorldDataButton.Click += GetHelloWorldDataButtonOnClick;
            _getHelloWorldDataTextView = FindViewById<TextView>(Resource.Id.getHelloWorldDataTextView);

            // This button will invoke SayHelloWorld - this method takes a simple string as a parameter.
            _sayHelloWorldButton = FindViewById<Button>(Resource.Id.sayHelloWorldButton);
            _sayHelloWorldButton.Click += SayHelloWorldButtonOnClick;
            _sayHelloWorldTextView = FindViewById<TextView>(Resource.Id.sayHelloWorldTextView);
        }
    ```
    
    上述程式碼初始化類別的執行個體變數，以及某些事件處理常式繫結在一起。

1. 在`MainActivity.cs`，具現化用戶端 proxy 類別加入下列兩種方法：

    ```csharp
        void InitializeHelloWorldServiceClient()
        {
            BasicHttpBinding binding = CreateBasicHttpBinding();
            _client = new HelloWorldServiceClient(binding, Endpoint);
        }

        static BasicHttpBinding CreateBasicHttpBinding()
        {
            BasicHttpBinding binding = new BasicHttpBinding
            {
                Name = "basicHttpBinding",
                MaxBufferSize = 2147483647,
                MaxReceivedMessageSize = 2147483647
            };

            TimeSpan timeout = new TimeSpan(0, 0, 30);
            binding.SendTimeout = timeout;
            binding.OpenTimeout = timeout;
            binding.ReceiveTimeout = timeout;
            return binding;
        }
    ```
    
    上述程式碼會具現化，並初始化`HelloWorldServiceClient`物件。

1. 在`MainActivity.cs`，甚至為加入處理常式中的兩個按鈕`Activity`:

    ```csharp
        async void GetHelloWorldDataButtonOnClick(object sender, EventArgs e)
        {
            var data = new HelloWorldData
            {
                Name = "Mr. Chad",
                SayHello = true
            };

            _getHelloWorldDataTextView.Text = "Waiting for WCF...";
            HelloWorldData result;
            try
            {
                result = await _client.GetHelloDataAsync(data);
                _getHelloWorldDataTextView.Text = result.Name;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }

        async void SayHelloWorldButtonOnClick(object sender, EventArgs e)
        {
            _sayHelloWorldTextView.Text = "Waiting for WCF...";
            try
            {
                var result = await _client.SayHelloToAsync("Kilroy");
                _sayHelloWorldTextView.Text = result;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
    ```
  
1. 執行應用程式，請確定 WCF 服務正在執行，然後按一下兩個按鈕。 應用程式將 WCF 以非同步方式呼叫，但前提是`Endpoint`欄位已正確設定：

    [![](walkthrough-working-with-wcf-images/image08.png "應該收到郵件 30 秒內回應，從每個 WCF 方法，和我們的應用程式看起來應該類似這個螢幕擷取畫面")](walkthrough-working-with-wcf-images/image08.png#lightbox)

<a name="Creating_a_Xamarin_iOS_Application" />

## <a name="creating-a-xamarinios-application"></a>建立 Xamarin.iOS 應用程式

WCF 服務 proxy 以供 Xamarin.iOS 應用程式，如下所示：

1. 在 Visual Studio 中，加入新的 iPhone**單一檢視應用程式**專案加入方案並將其命名`HelloWorld.iOS`。
1. 在`HelloWorld.iOS`專案中，將參考加入`HelloWorldServiceProxy`專案，以及參考`System.ServiceModel`命名空間。
1. 在**方案總管 中**，按兩下`Main.storyboard`iOS 設計工具中開啟檔案。 然後，加入下列`UIButton`和`UITextView`控制項：

    ||名稱|標題|
    |--- |--- |--- |
    |`UIButton`|`sayHelloWorldButton`|說出"Hello，World"|
    |`UITextView`|`sayHelloWorldText`||
    |`UIButton`|`getHelloWorldDataButton`|取得"Hello，World"的資料|
    |`UITextView`|`getHelloWorldDataText`||

    在之後加入的控制項，UI 應類似於下列螢幕擷取畫面：

    [![](walkthrough-working-with-wcf-images/image12.png "在之後加入的控制項，UI 應該類似這個螢幕擷取畫面")](walkthrough-working-with-wcf-images/image12.png#lightbox)

1. 在**方案總管 中**，開啟`ViewController.cs`並加入下列程式碼：

    ```xml
        public partial class ViewController : UIViewController
        {
            static readonly EndpointAddress Endpoint = new EndpointAddress("<insert_WCF_service_endpoint_here>");
            HelloWorldServiceClient _client;
            ...
        }
    ```
  
    取代`<insert_WCF_service_endpoint_here>`與 WCF 端點的位址。

1. 在`ViewController.cs`，更新`ViewDidLoad`方法，使它類似如下所示：

    ```csharp
        public override void ViewDidLoad()
        {
            base.ViewDidLoad();
            InitializeHelloWorldServiceClient();

            getHelloWorldDataButton.TouchUpInside += GetHelloWorldDataButton_TouchUpInside;
            sayHelloWorldButton.TouchUpInside += SayHelloWorldButton_TouchUpInside;
        }
    ```
  
1. 在`ViewController.cs`，新增`InitializeHelloWorldServiceClient`和`CreateBasicHttpBinding`方法：

    ```csharp
        void InitializeHelloWorldServiceClient()
        {
            BasicHttpBinding binding = CreateBasicHttpBinding();
            _client = new HelloWorldServiceClient(binding, Endpoint);
        }

        static BasicHttpBinding CreateBasicHttpBinding()
        {
            BasicHttpBinding binding = new BasicHttpBinding
            {
                Name = "basicHttpBinding",
                MaxBufferSize = 2147483647,
                MaxReceivedMessageSize = 2147483647
            };

            TimeSpan timeout = new TimeSpan(0, 0, 30);
            binding.SendTimeout = timeout;
            binding.OpenTimeout = timeout;
            binding.ReceiveTimeout = timeout;
            return binding;
        }
    ```
  
1. 在`ViewController.cs`，加入事件處理常式`TouchUpInside`對這兩個事件`UIButton`執行個體：

    ```csharp
        async void GetHelloWorldDataButton_TouchUpInside(object sender, EventArgs e)
        {
            getHelloWorldDataText.Text = "Waiting for WCF...";
            var data = new HelloWorldData
            {
                Name = "Mr. Chad",
                SayHello = true
            };

            HelloWorldData result;
            try
            {
                result = await _client.GetHelloDataAsync(data);
                getHelloWorldDataText.Text = result.Name;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }

        async void SayHelloWorldButton_TouchUpInside(object sender, EventArgs e)
        {
            sayHelloWorldText.Text = "Waiting for WCF...";
            try
            {
                var result = await _client.SayHelloToAsync("Kilroy");
                sayHelloWorldText.Text = result;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
    ```

1. 執行應用程式，請確定 WCF 服務正在執行，然後按一下兩個按鈕。 應用程式將 WCF 以非同步方式呼叫，但前提是`Endpoint`欄位已正確設定：

    [![](walkthrough-working-with-wcf-images/image10.png "應該收到郵件 30 秒內回應，從每個 WCF 方法，和我們的應用程式應該看起來像下列螢幕擷取畫面")](walkthrough-working-with-wcf-images/image10.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>總結

本教學課程涵蓋如何使用 Xamarin.Android 和 Xamarin.iOS 行動應用程式中的 WCF 服務使用。 它示範了如何建立 WCF 服務，並說明如何設定為接受來自遠端裝置連線的 Windows 10 和 IIS Express。 接著說明如何產生 WCF proxy 用戶端，並示範如何在 Xamarin.Android 和 Xamarin.iOS 應用程式中使用 proxy 用戶端。


## <a name="related-links"></a>相關連結

- [HelloWorld （範例）](https://developer.xamarin.com/samples/mobile/WCF-Walkthrough/)
- [開發 WCF 服務導向應用程式](https://docs.microsoft.com/en-us/dotnet/framework/wcf/index)
- [如何： 建立 Windows Communication Foundation 用戶端](https://docs.microsoft.com/en-us/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [ServiceModel Metadata Utility Tool (svcutil.exe)](https://docs.microsoft.com/en-us/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
