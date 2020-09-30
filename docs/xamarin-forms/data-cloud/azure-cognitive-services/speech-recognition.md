---
title: 使用語音服務 API 的語音辨識
description: 本文說明如何使用 Azure 語音服務 API，將語音轉譯到應用程式中的文字 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 01/14/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e5888e77948bef0fbb60a4e0068cbedf2d2a50e7
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555381"
---
# <a name="speech-recognition-using-azure-speech-service"></a>使用 Azure 語音服務的語音辨識

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-cognitivespeechservice)

Azure 語音服務是雲端式 API，提供下列功能：

- **語音轉換文字** 轉譯音訊檔或串流至文字。
- **文字到語音** 轉換會將輸入文字轉換成類似人類的合成語音。
- **語音翻譯** 可讓語音轉換文字和語音轉換語音進行即時、多語言的翻譯。
- **語音助理** 可為應用程式建立類似人類的對話介面。

本文說明如何 Xamarin.Forms 使用 Azure 語音服務在範例應用程式中執行語音轉換文字。 下列螢幕擷取畫面顯示 iOS 和 Android 上的範例應用程式：

[![IOS 和 Android 上範例應用程式的螢幕擷取畫面](speech-recognition-images/speech-recognition-cropped.png)](speech-recognition-images/speech-recognition.png#lightbox "IOS 和 Android 上範例應用程式的螢幕擷取畫面")

## <a name="create-an-azure-speech-service-resource"></a>建立 Azure 語音服務資源

Azure 語音服務是 Azure 認知服務的一部分，可為影像辨識、語音辨識和轉譯，以及 Bing 搜尋等工作提供雲端式 Api。 如需詳細資訊，請參閱 [什麼是 Azure 認知服務？](/azure/cognitive-services/welcome)。

範例專案需要在您的 Azure 入口網站中建立 Azure 認知服務資源。 您可以建立單一服務（例如語音服務）或多服務資源的認知服務資源。 建立語音服務資源的步驟如下：

1. 登入您的 [Azure 入口網站](https://portal.azure.com)。
1. 建立多服務或單一服務資源。
1. 取得資源的 API 金鑰和區域資訊。
1. 更新範例 **Constants.cs** 檔。

如需建立資源的逐步指南，請參閱 [建立認知服務資源](/azure/cognitive-services/cognitive-services-apis-create-account)。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。 一旦您擁有帳戶，就可以在免費層建立單一服務資源，以試用服務。

## <a name="configure-your-app-with-the-speech-service"></a>使用語音服務來設定您的應用程式

建立認知服務資源之後，您可以使用 Azure 資源中的區域和 API 金鑰來更新 **Constants.cs** 檔案：

```csharp
public static class Constants
{
    public static string CognitiveServicesApiKey = "YOUR_KEY_GOES_HERE";
    public static string CognitiveServicesRegion = "westus";
}
```

## <a name="install-nuget-speech-service-package"></a>安裝 NuGet 語音服務套件

範例應用程式會使用 **CognitiveServices** NuGet 套件連接到 Azure 語音服務。 在共用的專案和每個平臺專案中安裝此 NuGet 套件。

## <a name="create-an-imicrophoneservice-interface"></a>建立 IMicrophoneService 介面

每個平臺都需要存取麥克風的許可權。 範例專案會 `IMicrophoneService` 在共用的專案中提供介面，並使用 Xamarin.Forms `DependencyService` 來取得介面的平臺執行。

```csharp
public interface IMicrophoneService
{
    Task<bool> GetPermissionAsync();
    void OnRequestPermissionResult(bool isGranted);
}
```

## <a name="create-the-page-layout"></a>建立頁面配置

範例專案會在 **MainPage .xaml** 檔案中定義基本的頁面配置。 主要版面配置元素是開始轉譯程式的 `Button` 、 `Label` 包含轉譯文字的，以及 `ActivityIndicator` 要在轉譯進行時顯示的：

```xaml
<ContentPage ...>
    <StackLayout>
        <Frame ...>
            <ScrollView x:Name="scroll"
                        ...>
                <Label x:Name="transcribedText"
                       ... />
            </ScrollView>
        </Frame>

        <ActivityIndicator x:Name="transcribingIndicator"
                           IsRunning="False" />
        <Button x:Name="transcribeButton"
                ...
                Clicked="TranscribeClicked"/>
    </StackLayout>
</ContentPage>
```

## <a name="implement-the-speech-service"></a>執行語音服務

**MainPage.xaml.cs**程式碼後端檔案包含從 Azure 語音服務傳送音訊和接收轉譯文字的所有邏輯。

此函 `MainPage` 式會從取得介面的實例 `IMicrophoneService` `DependencyService` ：

```csharp
public partial class MainPage : ContentPage
{
    SpeechRecognizer recognizer;
    IMicrophoneService micService;
    bool isTranscribing = false;

    public MainPage()
    {
        InitializeComponent();

        micService = DependencyService.Resolve<IMicrophoneService>();
    }

    // ...
}
```

`TranscribeClicked`呼叫實例時，會呼叫方法 `transcribeButton` ：

```csharp
async void TranscribeClicked(object sender, EventArgs e)
{
    bool isMicEnabled = await micService.GetPermissionAsync();

    // EARLY OUT: make sure mic is accessible
    if (!isMicEnabled)
    {
        UpdateTranscription("Please grant access to the microphone!");
        return;
    }

    // initialize speech recognizer 
    if (recognizer == null)
    {
        var config = SpeechConfig.FromSubscription(Constants.CognitiveServicesApiKey, Constants.CognitiveServicesRegion);
        recognizer = new SpeechRecognizer(config);
        recognizer.Recognized += (obj, args) =>
        {
            UpdateTranscription(args.Result.Text);
        };
    }

    // if already transcribing, stop speech recognizer
    if (isTranscribing)
    {
        try
        {
            await recognizer.StopContinuousRecognitionAsync();
        }
        catch(Exception ex)
        {
            UpdateTranscription(ex.Message);
        }
        isTranscribing = false;
    }

    // if not transcribing, start speech recognizer
    else
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            InsertDateTimeRecord();
        });
        try
        {
            await recognizer.StartContinuousRecognitionAsync();
        }
        catch(Exception ex)
        {
            UpdateTranscription(ex.Message);
        }
        isTranscribing = true;
    }
    UpdateDisplayState();
}
```

`TranscribeClicked` 方法會執行下列動作：

1. 檢查應用程式是否有權存取麥克風，如果沒有，則會提早結束。
1. 建立類別的實例（ `SpeechRecognizer` 如果尚未存在）。
1. 如果正在進行，則會停止持續轉譯。
1. 插入時間戳記，並開始進行連續轉譯（如果不在進行中）。
1. 通知應用程式根據新的應用程式狀態來更新其外觀。

類別方法的其餘部分 `MainPage` 是顯示應用程式狀態的協助程式：

```csharp
void UpdateTranscription(string newText)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (!string.IsNullOrWhiteSpace(newText))
        {
            transcribedText.Text += $"{newText}\n";
        }
    });
}

void InsertDateTimeRecord()
{
    var msg = $"=================\n{DateTime.Now.ToString()}\n=================";
    UpdateTranscription(msg);
}

void UpdateDisplayState()
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (isTranscribing)
        {
            transcribeButton.Text = "Stop";
            transcribeButton.BackgroundColor = Color.Red;
            transcribingIndicator.IsRunning = true;
        }
        else
        {
            transcribeButton.Text = "Transcribe";
            transcribeButton.BackgroundColor = Color.Green;
            transcribingIndicator.IsRunning = false;
        }
    });
}
```

方法會將 `UpdateTranscription` 提供的寫入 `newText` `string` 至名為的專案 `Label` `transcribedText` 。 它會強制在 UI 執行緒上進行這項更新，因此可以從任何內容呼叫，而不會造成例外狀況。 會將 `InsertDateTimeRecord` 目前的日期和時間寫入至 `transcribedText` 實例，以標示新轉譯的開始。 最後，此 `UpdateDisplayState` 方法會更新 `Button` 和 `ActivityIndicator` 元素，以反映轉譯是否正在進行中。

## <a name="create-platform-microphone-services"></a>建立平臺麥克風服務

應用程式必須具有可收集語音資料的麥克風存取權。 `IMicrophoneService`介面必須 `DependencyService` 在每個平臺上的上執行和註冊，應用程式才能運作。

### <a name="android"></a>Android

範例專案會定義 `IMicrophoneService` 稱為 Android 的實作為 `AndroidMicrophoneService` ：

```csharp
[assembly: Dependency(typeof(AndroidMicrophoneService))]
namespace CognitiveSpeechService.Droid.Services
{
    public class AndroidMicrophoneService : IMicrophoneService
    {
        public const int RecordAudioPermissionCode = 1;
        private TaskCompletionSource<bool> tcsPermissions;
        string[] permissions = new string[] { Manifest.Permission.RecordAudio };

        public Task<bool> GetPermissionAsync()
        {
            tcsPermissions = new TaskCompletionSource<bool>();

            if ((int)Build.VERSION.SdkInt < 23)
            {
                tcsPermissions.TrySetResult(true);
            }
            else
            {
                var currentActivity = MainActivity.Instance;
                if (ActivityCompat.CheckSelfPermission(currentActivity, Manifest.Permission.RecordAudio) != (int)Permission.Granted)
                {
                    RequestMicPermissions();
                }
                else
                {
                    tcsPermissions.TrySetResult(true);
                }

            }

            return tcsPermissions.Task;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            tcsPermissions.TrySetResult(isGranted);
        }

        void RequestMicPermissions()
        {
            if (ActivityCompat.ShouldShowRequestPermissionRationale(MainActivity.Instance, Manifest.Permission.RecordAudio))
            {
                Snackbar.Make(MainActivity.Instance.FindViewById(Android.Resource.Id.Content),
                        "Microphone permissions are required for speech transcription!",
                        Snackbar.LengthIndefinite)
                        .SetAction("Ok", v =>
                        {
                            ((Activity)MainActivity.Instance).RequestPermissions(permissions, RecordAudioPermissionCode);
                        })
                        .Show();
            }
            else
            {
                ActivityCompat.RequestPermissions((Activity)MainActivity.Instance, permissions, RecordAudioPermissionCode);
            }
        }
    }
}
```

`AndroidMicrophoneService`具有下列功能：

1. `Dependency`屬性會向註冊類別 `DependencyService` 。
1. `GetPermissionAsync`方法會根據 Android SDK 版本檢查是否需要許可權，而且如果尚未授與許可權，則會呼叫 `RequestMicPermissions` 。
1. `RequestMicPermissions` `Snackbar` 如果需要基本原理，方法會使用類別來要求使用者的許可權，否則會直接要求音訊錄製許可權。
1. `OnRequestPermissionResult`當使用者已回應許可權要求時，就會呼叫方法並 `bool` 產生結果。

`MainActivity`自訂類別以 `AndroidMicrophoneService` 在許可權要求完成時更新實例：

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    IMicrophoneService micService;
    internal static MainActivity Instance { get; private set; }
    
    protected override void OnCreate(Bundle savedInstanceState)
    {
        Instance = this;
        // ...
        micService = DependencyService.Resolve<IMicrophoneService>();
    }
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Android.Content.PM.Permission[] grantResults)
    {
        // ...
        switch(requestCode)
        {
            case AndroidMicrophoneService.RecordAudioPermissionCode:
                if (grantResults[0] == Permission.Granted)
                {
                    micService.OnRequestPermissionResult(true);
                }
                else
                {
                    micService.OnRequestPermissionResult(false);
                }
                break;
        }
    }
}
```

`MainActivity`類別會定義名為的靜態參考，此參考 `Instance` 是物件要求許可權時的必要項 `AndroidMicrophoneService` 。 `OnRequestPermissionsResult` `AndroidMicrophoneService` 當使用者核准或拒絕許可權要求時，它會覆寫方法以更新物件。

最後，Android 應用程式必須包含在 **AndroidManifest.xml** 檔案中錄製音訊的許可權：

```xml
<manifest ...>
    ...
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
</manifest>
```

### <a name="ios"></a>iOS

範例專案會定義 `IMicrophoneService` iOS 的執行，稱為 `iOSMicrophoneService` ：

```csharp
[assembly: Dependency(typeof(iOSMicrophoneService))]
namespace CognitiveSpeechService.iOS.Services
{
    public class iOSMicrophoneService : IMicrophoneService
    {
        TaskCompletionSource<bool> tcsPermissions;

        public Task<bool> GetPermissionAsync()
        {
            tcsPermissions = new TaskCompletionSource<bool>();
            RequestMicPermission();
            return tcsPermissions.Task;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            tcsPermissions.TrySetResult(isGranted);
        }

        void RequestMicPermission()
        {
            var session = AVAudioSession.SharedInstance();
            session.RequestRecordPermission((granted) =>
            {
                tcsPermissions.TrySetResult(granted);
            });
        }
    }
}
```

`iOSMicrophoneService`具有下列功能：

1. `Dependency`屬性會向註冊類別 `DependencyService` 。
1. `GetPermissionAsync`方法會呼叫 `RequestMicPermissions` 以要求裝置使用者的許可權。
1. `RequestMicPermissions`方法會使用共用的 `AVAudioSession` 實例來要求記錄許可權。
1. `OnRequestPermissionResult`方法會 `TaskCompletionSource` 使用提供的值來更新實例 `bool` 。

最後，iOS 應用程式 **資訊 plist** 必須包含訊息，告知使用者應用程式要求存取麥克風的原因。 編輯 plist 檔案，以在元素內包含下列標記 `<dict>` ：

```xml
<plist>
    <dict>
        ...
        <key>NSMicrophoneUsageDescription</key>
        <string>Voice transcription requires microphone access</string>
    </dict>
</plist>
```

### <a name="uwp"></a>UWP

範例專案會定義 `IMicrophoneService` UWP 的執行，稱為 `UWPMicrophoneService` ：

```csharp
[assembly: Dependency(typeof(UWPMicrophoneService))]
namespace CognitiveSpeechService.UWP.Services
{
    public class UWPMicrophoneService : IMicrophoneService
    {
        public async Task<bool> GetPermissionAsync()
        {
            bool isMicAvailable = true;
            try
            {
                var mediaCapture = new MediaCapture();
                var settings = new MediaCaptureInitializationSettings();
                settings.StreamingCaptureMode = StreamingCaptureMode.Audio;
                await mediaCapture.InitializeAsync(settings);
            }
            catch(Exception ex)
            {
                isMicAvailable = false;
            }

            if(!isMicAvailable)
            {
                await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-microphone"));
            }

            return isMicAvailable;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            // intentionally does nothing
        }
    }
}
```

`UWPMicrophoneService`具有下列功能：

1. `Dependency`屬性會向註冊類別 `DependencyService` 。
1. `GetPermissionAsync`方法會嘗試初始化 `MediaCapture` 實例。 如果失敗，則會啟動使用者要求以啟用麥克風。
1. `OnRequestPermissionResult`有方法可滿足介面，但 UWP 執行不需要這個方法。

最後，UWP **Package. package.appxmanifest** 必須指定應用程式使用麥克風。 按兩下 package.appxmanifest 檔案，然後在 Visual Studio 2019 的 [**功能**] 索引標籤上選取 [**麥克風**] 選項：

[![Visual Studio 2019 中資訊清單的螢幕擷取畫面](speech-recognition-images/package-manifest-cropped.png)](speech-recognition-images/package-manifest.png#lightbox "Visual Studio 2019 中資訊清單的螢幕擷取畫面")

## <a name="test-the-application"></a>測試應用程式

執行應用程式，然後按一下 [ **轉譯** ] 按鈕。 應用程式應該會要求麥克風存取權，並開始進行轉譯程式。 `ActivityIndicator`將會建立動畫，顯示轉譯為使用中狀態。 當您說話時，應用程式會將音訊資料串流至 Azure 語音服務資源，其會以轉譯的文字回應。 轉譯的文字將會出現在收到的專案中 `Label` 。

> [!NOTE]
> Android 模擬器無法載入和初始化語音服務程式庫。 針對 Android 平臺，建議您在實體裝置上進行測試。

## <a name="related-links"></a>相關連結

- [Azure 語音服務範例](/samples/xamarin/xamarin-forms-samples/webservices-cognitivespeechservice)
- [Azure 語音服務總覽](/azure/cognitive-services/speech-service/overview)
- [建立認知服務資源](/azure/cognitive-services/cognitive-services-apis-create-account)
- [快速入門：從麥克風辨識語音](/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone)