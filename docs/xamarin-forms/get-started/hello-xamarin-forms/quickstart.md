---
title: Xamarin.Forms Quickstart
ms.topic: article
ms.prod: xamarin
ms.assetid: 3f2f9c2d-d204-43bc-8c8a-a55ce1e6d2c8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/06/2018
ms.openlocfilehash: 7ce674ea38bc847bc9064a5a61113900a45b991d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinforms-quickstart"></a>Xamarin.Forms Quickstart

本逐步解說示範如何建立可將使用者輸入的英數字元電話號碼轉譯成數字電話號碼，然後再撥打該號碼的應用程式。 最終的應用程式如下所示：

[![](quickstart-images/intro-app-examples-sml.png "Phoneword 應用程式")](quickstart-images/intro-app-examples.png "Phoneword 應用程式")

建立 Phoneword 應用程式，如下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 [開始] 畫面中，啟動 Visual Studio。 這樣會開啟 [開始] 頁面：

  ![](quickstart-images/vs/start-page.png "Visual Studio")

1. 在 Visual Studio 中，按一下 [建立新專案] 以建立新的專案：

  ![](quickstart-images/vs/new-solution.png "新增專案")

1. 在 [新增專案] 對話方塊中，按一下 [跨平台]、選取 [跨平台應用程式 (Xamarin.Forms)] 範本、將 [名稱] 和 [方案名稱] 設定為 `Phoneword`、選擇專案的適當位置，然後按一下 [確定] 按鈕：

  ![](quickstart-images/vs/new-project.png "跨平台專案範本")

1. 在 [新增跨平台應用程式] 對話方塊中，按一下 [空白應用程式]、選取 [Xamarin.Forms] 作為 [UI 技術]、選取 [.NET Standard] 作為 [程式碼共用策略]，然後按一下 [確定] 按鈕：

  ![](quickstart-images/vs/new-app.png "新增跨平台應用程式")

1. 在 [方案總管] 的 **Phoneword** 專案中，按兩下 **MainPage.xaml**，將其開啟：

  ![](quickstart-images/vs/open-mainpage-xaml.png "開啟 MainPage.xaml")

1. 在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會以宣告的方式定義頁面的使用者介面：

        <?xml version="1.0" encoding="UTF-8"?>
        <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                           x:Class="Phoneword.MainPage">
            <ContentPage.Padding>
                <OnPlatform x:TypeArguments="Thickness">
                    <On Platform="iOS" Value="20, 40, 20, 20" />
                    <On Platform="Android, WinPhone, Windows" Value="20" />
                </OnPlatform>
            </ContentPage.Padding>
            <StackLayout>
              <Label Text="Enter a Phoneword:" />
              <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
              <Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
              <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
            </StackLayout>
        </ContentPage>

  按下 **CTRL+S** 以將變更儲存到 **MainPage.xaml**，然後關閉檔案。

1. 在 [方案總管] 中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs**，將其開啟：

  ![](quickstart-images/vs/open-mainpage-codebehind.png "開啟 MainPage.xaml.cs")

1. 在 **MainPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 系統將會執行 `OnTranslate` 和 `OnCall` 方法，以回應要在使用者介面中分別按一下的 [轉譯] 和 [撥號] 按鈕：

        using System;
        using Xamarin.Forms;

        namespace Phoneword
        {
            public partial class MainPage : ContentPage
            {
                string translatedNumber;

                public MainPage ()
                {
                    InitializeComponent ();
                }

                void OnTranslate (object sender, EventArgs e)
                {
                    translatedNumber = Core.PhonewordTranslator.ToNumber (phoneNumberText.Text);
                    if (!string.IsNullOrWhiteSpace (translatedNumber)) {
                        callButton.IsEnabled = true;
                        callButton.Text = "Call " + translatedNumber;
                    } else {
                        callButton.IsEnabled = false;
                        callButton.Text = "Call";
                    }
                }

                async void OnCall (object sender, EventArgs e)
                {
                    if (await this.DisplayAlert (
                            "Dial a Number",
                            "Would you like to call " + translatedNumber + "?",
                            "Yes",
                            "No")) {
                        var dialer = DependencyService.Get<IDialer> ();
                        if (dialer != null)
                            dialer.Dial (translatedNumber);
                    }
                }
            }
        }

  > [!NOTE]
> **注意**：此時嘗試建置應用程式將會導致發生錯誤，而將在稍後修正。

  按下 **CTRL+S** 以將變更儲存到 **MainPage.xaml.cs**，然後關閉檔案。

1. 在 [方案總管] 中展開 **App.xaml**，然後按兩下 **App.xaml.cs**，將其開啟：

  ![](quickstart-images/vs/open-app-class.png "開啟 App.xaml.cs")

1. 在 **App.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 `App` 建構函式會將 `MainPage` 類別設定為將在應用程式啟動時顯示的頁面：

        using Xamarin.Forms;
        using Xamarin.Forms.Xaml;

        [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
        namespace Phoneword
        {
            public partial class App : Application
            {
                public App()
                {
                    InitializeComponent();
                    MainPage = new MainPage();
                }

                protected override void OnStart()
                {
                    // Handle when your app starts
                }

                protected override void OnSleep()
                {
                    // Handle when your app sleeps
                }

                protected override void OnResume()
                {
                    // Handle when your app resumes
                }
            }
        }

  按下 **CTRL+S** 以將變更儲存到 **App.xaml.cs**，然後關閉檔案。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 **Phoneword** 專案，然後選取 [加入] > [新項目]：

  ![](quickstart-images/vs/add-new-item.png "加入新項目")

1. 在 [加入新項目] 對話方塊中，選取 [Visual C#] > [程式碼] > [類別]、將新檔案命名為 **PhoneTranslator**，然後按一下 [新增] 按鈕：

  ![](quickstart-images/vs/add-translator-class.png "加入新類別")

1. 在 **PhoneTranslator.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會將電話文字轉譯成電話號碼：

        using System.Text;

        namespace Core
        {
            public static class PhonewordTranslator
            {
                public static string ToNumber(string raw)
                {
                    if (string.IsNullOrWhiteSpace(raw))
                        return null;

                    raw = raw.ToUpperInvariant();

                    var newNumber = new StringBuilder();
                    foreach (var c in raw)
                    {
                        if (" -0123456789".Contains(c))
                            newNumber.Append(c);
                        else
                        {
                            var result = TranslateToNumber(c);
                            if (result != null)
                                newNumber.Append(result);
                            // Bad character?
                            else
                                return null;
                        }
                    }
                    return newNumber.ToString();
                }

                static bool Contains(this string keyString, char c)
                {
                    return keyString.IndexOf(c) >= 0;
                }

                static readonly string[] digits = {
                    "ABC", "DEF", "GHI", "JKL", "MNO", "PQRS", "TUV", "WXYZ"
                };

                static int? TranslateToNumber(char c)
                {
                    for (int i = 0; i < digits.Length; i++)
                    {
                        if (digits[i].Contains(c))
                            return 2 + i;
                    }
                    return null;
                }
            }
        }

  按下 **CTRL+S**，將變更儲存到 **PhoneTranslator.cs**，然後關閉檔案。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 **Phoneword** 專案，然後選取 [加入] > [新項目]：

  ![](quickstart-images/vs/add-new-item.png "加入新項目")

1. 在 [加入新項目] 對話方塊中，選取 [Visual C#] > [程式碼] > [介面]、將新檔案命名為 **IDialer**，然後按一下 [新增] 按鈕：

  ![](quickstart-images/vs/add-idialer-interface.png "加入新介面")

1. 在 **IDialer.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會定義必須在每個平台上實作的 `Dial` 方法，才能撥打轉譯的電話號碼：

        namespace Phoneword
        {
            public interface IDialer
            {
                bool Dial(string number);
            }
        }

  按下 **CTRL+S**，將變更儲存到 **IDialer.cs**，然後關閉檔案。

  > [!NOTE]
> 現已完成應用程式的通用程式碼。 平台專屬的電話撥號員程式碼現在會當作 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) 實作。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 **Phoneword.iOS** 專案，然後選取 [加入] > [新項目]：

  ![](quickstart-images/vs/add-new-item-ios.png "加入新項目")

1. 在 [加入新項目] 對話方塊中，選取 [Apple] > [程式碼] > [類別]、將新檔案命名為 **PhoneDialer**，然後按一下 [新增] 按鈕：

  ![](quickstart-images/vs/new-phone-dialer-ios.png "加入新類別")

1. 在 **PhoneDialer.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會建立將在 iOS 平台上使用的 <code>Dial</code> 方法，才能撥打轉譯的電話號碼：

        using Foundation;
        using Phoneword.iOS;
        using UIKit;
        using Xamarin.Forms;

        [assembly: Dependency(typeof(PhoneDialer))]
        namespace Phoneword.iOS
        {
            public class PhoneDialer : IDialer
            {
                public bool Dial(string number)
                {
                    return UIApplication.SharedApplication.OpenUrl (
                        new NSUrl ("tel:" + number));
                }
            }
        }

  按下 **CTRL+S**，將變更儲存到 **PhoneDialer.cs**，然後關閉檔案。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 **Phoneword.Android** 專案，然後選取 [加入] > [新項目]：

  ![](quickstart-images/vs/add-new-item-android.png "加入新項目")

1. 在 [加入新項目] 對話方塊中，選取 [Visual C#] > [Android] > [類別]、將新檔案命名為 **PhoneDialer**，然後按一下 [新增] 按鈕：

  ![](quickstart-images/vs/new-phone-dialer-android.png "加入新類別")

1. 在 **PhoneDialer.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會建立將在 Android 平台上使用的 `Dial` 方法，才能撥打轉譯的電話號碼：

        using Android.Content;
        using Android.Telephony;
        using Phoneword.Droid;
        using System.Linq;
        using Xamarin.Forms;
        using Uri = Android.Net.Uri;

        [assembly: Dependency(typeof(PhoneDialer))]
        namespace Phoneword.Droid
        {
            public class PhoneDialer : IDialer
            {
                public bool Dial(string number)
                {
                    var context = MainActivity.Instance;
                    if (context == null)
                        return false;

                    var intent = new Intent (Intent.ActionCall);
                    intent.SetData (Uri.Parse ("tel:" + number));

                    if (IsIntentAvailable (context, intent)) {
                        context.StartActivity (intent);
                        return true;
                    }

                    return false;
                }

                public static bool IsIntentAvailable(Context context, Intent intent)
                {
                    var packageManager = context.PackageManager;

                    var list = packageManager.QueryIntentServices (intent, 0)
                        .Union (packageManager.QueryIntentActivities (intent, 0));

                    if (list.Any ())
                        return true;

                    var manager = TelephonyManager.FromContext (context);
                    return manager.PhoneType != PhoneType.None;
                }
            }
        }

  按下 **CTRL+S**，將變更儲存到 **PhoneDialer.cs**，然後關閉檔案。

1. 在 [方案總管] 的 [Phoneword.Android] 專案中，按兩下 [MainActivity.cs] 來開啟它、移除所有範本程式碼，然後以下列程式碼取代：

        using Android.App;
        using Android.Content.PM;
        using Android.OS;

        namespace Phoneword.Droid
        {
            [Activity(Label = "Phoneword", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
            public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
            {
                internal static MainActivity Instance { get; private set; }

                protected override void OnCreate(Bundle bundle)
                {
                    TabLayoutResource = Resource.Layout.Tabbar;
                    ToolbarResource = Resource.Layout.Toolbar;

                    base.OnCreate(bundle);

                    Instance = this;
                    global::Xamarin.Forms.Forms.Init(this, bundle);
                    LoadApplication(new App());
                }
            }
        }  

  按下 **CTRL+S** 來儲存對 **MainActivity.cs** 所做的變更，然後關閉檔案。

1. 在 [方案總管] 的 **Phoneword.Android** 專案中，按兩下 [屬性]，然後選取 [Android 資訊清單] 索引標籤：

  ![](quickstart-images/vs/android-manifest.png "開啟 Android 資訊清單")

1. 在 [必要權限] 區段中，啟用 **CALL_PHONE** 權限。 這可為應用程式提供撥打電話的權限：

  ![](quickstart-images/vs/android-manifest-changed.png "啟用 CallPhone 權限")

  按下 **CTRL+S**，將變更儲存到資訊清單，然後關閉檔案。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 **Phoneword.UWP** 專案，然後選取 [加入] > [新項目]：

  ![](quickstart-images/vs/add-new-item-uwp.png "加入新項目")

1. 在 [加入新項目] 對話方塊中，選取 [Visual C#] > [程式碼] > [類別]、將新檔案命名為 **PhoneDialer**，然後按一下 [新增] 按鈕：

  ![](quickstart-images/vs/new-phone-dialer-uwp.png "加入新類別")

1. 在 **PhoneDialer.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會建立將在通用 Windows 平台上使用的 `Dial` 方法和 Helper 方法，才能撥打轉譯的電話號碼：

        using Phoneword.UWP;
        using System;
        using System.Threading.Tasks;
        using Windows.ApplicationModel.Calls;
        using Windows.UI.Popups;
        using Xamarin.Forms;

        [assembly: Dependency(typeof(PhoneDialer))]
        namespace Phoneword.UWP
        {
            public class PhoneDialer : IDialer
            {
                bool dialled = false;

                public bool Dial(string number)
                {
                    DialNumber(number);
                    return dialled;
                }

                async Task DialNumber(string number)
                {
                    var phoneLine = await GetDefaultPhoneLineAsync();
                    if (phoneLine != null)
                    {
                        phoneLine.Dial(number, number);
                        dialled = true;
                    }
                    else
                    {
                        var dialog = new MessageDialog("No line found to place the call");
                        await dialog.ShowAsync();
                        dialled = false;
                    }
                }

                async Task<PhoneLine> GetDefaultPhoneLineAsync()
                {
                    var phoneCallStore = await PhoneCallManager.RequestStoreAsync();
                    var lineId = await phoneCallStore.GetDefaultLineAsync();
                    return await PhoneLine.FromIdAsync(lineId);
                }
            }
        }

  按下 **CTRL+S**，將變更儲存到 **PhoneDialer.cs**，然後關閉檔案。

1. 在 [方案總管] 的 **Phoneword.UWP** 專案中，以滑鼠右鍵按一下 [參考]，然後選取 [加入參考]：

  ![](quickstart-images/vs/uwp-add-reference.png "加入參考")

1. 在 [參考管理員] 對話方塊中，選取 [通用 Windows] > [延伸模組] > [UWP 的 Windows Mobile 延伸模組]，然後按一下 [確定] 按鈕：

  ![](quickstart-images/vs/uwp-add-reference-extensions.png "新增 UWP 的 Windows Mobile 延伸模組")

1. 在 [方案總管] 的 **Phoneword.UWP** 專案中，按兩下 **Package.appxmanifest**：

  ![](quickstart-images/vs/uwp-manifest.png "開啟 UWP 資訊清單")

1. 在 [功能] 頁面上，啟用 [通話] 功能。 這可為應用程式提供撥打電話的權限：

  ![](quickstart-images/vs/uwp-manifest-changed.png "啟用通話功能")

  按下 **CTRL+S**，將變更儲存到資訊清單，然後關閉檔案。

1. 在 Visual Studio 中，選取 [建置] > [建置方案] 功能表項目 (或按下 **CTRL+SHIFT+B**)。 系統將建置應用程式，且 Visual Studio 狀態列中將會出現成功訊息：

  ![](quickstart-images/vs/build-successful.png "建置成功")

  如果發生錯誤，請重複上述步驟並更正任何錯誤，直到應用程式建置成功為止。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 **Phoneword.UWP** 專案，然後選取 [設定為啟始專案]：

  ![](quickstart-images/vs/uwp-set-as-startup-project.png "設定為啟始專案")

1. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕) 以啟動應用程式：

  ![](quickstart-images/vs/start.png "Visual Studio 工具列")
  ![](quickstart-images/vs/phone-result-uwp.png "Phoneword 應用程式 UWP")

1. 在 [方案總管] 中，以滑鼠右鍵按一下 **Phoneword.Android** 專案，然後選取 [設定為啟始專案]。
1. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕) 以啟動 Android 模擬器內的應用程式。
1. 如果您有 iOS 裝置，而且符合 Xamarin.Forms 開發的 Mac 系統需求，請使用類似的技術將應用程式部署至 iOS 裝置。 或者，您也可以將應用程式部署至 [iOS 遠端模擬器](~/tools/ios-simulator.md)。

  注意：所有模擬器都不支援通話功能。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 啟動 Visual Studio for Mac，然後在起始頁面上按一下 [新增專案] 以建立新專案：

  ![](quickstart-images/xs/new-solution.png "新增方案")

1. 在 [為新專案選擇範本] 對話方塊中，按一下 [多平台] > [應用程式]、選取 [空白的 Forms App] 範本，然後按 [下一步] 按鈕：

  ![](quickstart-images/xs/choose-template.png "選擇範本")

1. 在 [設定空白的 Forms App] 對話方塊中，將新的應用程式命名為 `Phoneword`、確認已選取 [使用可攜式類別庫] 選項按鈕、確定已選取 [將 XAML 用於使用者介面檔案] 核取方塊，然後按 [下一步] 按鈕：

  ![](quickstart-images/xs/configure-app.png "設定表單應用程式")

1. 在 [設定新的空白 Forms App] 對話方塊中，將 [方案名稱] 和 [專案名稱] 設定為 `Phoneword`、選擇專案的合適位置，然後按一下 [建立] 按鈕以建立專案：

  ![](quickstart-images/xs/configure-project.png "設定表單專案")

1. 在 [Solution Pad] 中，選取 **Phoneword** 專案、按一下滑鼠右鍵，然後選取 [新增] > [新增檔案]：

  ![](quickstart-images/xs/add-new-file.png "加入新檔案")

1. 在 [新增檔案] 對話方塊中，選取 [表單] > [Form ContentPage XAML]、將新檔案命名為 **MainPage**，然後按一下 [新增] 按鈕。 這會將名為 **MainPage** 的頁面新增至專案：

  ![](quickstart-images/xs/add-mainpage-class.png "加入新的 ContentPage")

1. 在 [Solution Pad] 中，按兩下 **MainPage.xaml** 以將其開啟：

  ![](quickstart-images/xs/open-mainpage-xaml.png "開啟 MainPage.xaml")

1. 在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會以宣告的方式定義頁面的使用者介面：

        <?xml version="1.0" encoding="UTF-8"?>
        <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                           x:Class="Phoneword.MainPage">
            <ContentPage.Padding>
                <OnPlatform x:TypeArguments="Thickness">
                    <On Platform="iOS" Value="20, 40, 20, 20" />
                    <On Platform="Android, WinPhone, Windows" Value="20" />
                </OnPlatform>
            </ContentPage.Padding>
            <StackLayout>
              <Label Text="Enter a Phoneword:" />
              <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
              <Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
              <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
            </StackLayout>
        </ContentPage>

  選擇 [檔案] > [儲存] (或按下 **&#8984; + S**) 以將變更儲存到 **MainPage.xaml**，然後關閉檔案。

1. 在 [Solution Pad] 中，按兩下 **MainPage.xaml.cs** 以將其開啟：

  ![](quickstart-images/xs/open-mainpage-codebehind.png "開啟 MainPage.xaml.cs")

1. 在 **MainPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 系統將會執行 `OnTranslate` 和 `OnCall` 方法，以回應要在使用者介面中分別按一下的 [轉譯] 和 [撥號] 按鈕：

        using System;
        using Xamarin.Forms;

        namespace Phoneword
        {
            public partial class MainPage : ContentPage
            {
                string translatedNumber;

                public MainPage ()
                {
                    InitializeComponent ();
                }

                void OnTranslate (object sender, EventArgs e)
                {
                    translatedNumber = Core.PhonewordTranslator.ToNumber (phoneNumberText.Text);
                    if (!string.IsNullOrWhiteSpace (translatedNumber)) {
                        callButton.IsEnabled = true;
                        callButton.Text = "Call " + translatedNumber;
                    } else {
                        callButton.IsEnabled = false;
                        callButton.Text = "Call";
                    }
                }

                async void OnCall (object sender, EventArgs e)
                {
                    if (await this.DisplayAlert (
                            "Dial a Number",
                            "Would you like to call " + translatedNumber + "?",
                            "Yes",
                            "No")) {
                        var dialer = DependencyService.Get<IDialer> ();
                        if (dialer != null)
                            dialer.Dial (translatedNumber);
                    }
                }
            }
        }

  > [!NOTE]
> **注意**：此時嘗試建置應用程式將會導致發生錯誤，而將在稍後修正。

  選擇 [檔案] > [儲存] (或按下 **&#8984; + S**) 以將變更儲存到 **MainPage.xaml.cs**，然後關閉檔案。

1. 在 [Solution Pad] 中，按兩下 **App.xaml.cs** 以將其開啟：

  ![](quickstart-images/xs/open-app-class.png "開啟 App.xaml.cs")

1. 在 **App.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 `App` 建構函式會將 `MainPage` 類別設定為將在應用程式啟動時顯示的頁面：

        using Xamarin.Forms;
        using Xamarin.Forms.Xaml;

        [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
        namespace Phoneword
        {
            public partial class App : Application
            {
                public App()
                {
                    InitializeComponent();
                    MainPage = new MainPage();
                }

                protected override void OnStart()
                {
                    // Handle when your app starts
                }

                protected override void OnSleep()
                {
                    // Handle when your app sleeps
                }

                protected override void OnResume()
                {
                    // Handle when your app resumes
                }
            }
        }

  選擇 [檔案] > [儲存] (或按下 **&#8984; + S**)，將變更儲存到 **Phoneword.cs**，然後關閉檔案。

1. 在 [Solution Pad] 中，選取 **Phoneword** 專案、按一下滑鼠右鍵，然後選取 [新增] > [新增檔案]：

  ![](quickstart-images/xs/add-new-translator-file.png "加入新檔案")

1. 在 [新增檔案] 對話方塊中，選取 [一般] > [空的類別]、將新檔案命名為 **PhoneTranslator**，然後按一下 [新增] 按鈕：

  ![](quickstart-images/xs/add-translator-class.png "加入新類別")

1. 在 **PhoneTranslator.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會將電話文字轉譯成電話號碼：

        using System.Text;

        namespace Core
        {
            public static class PhonewordTranslator
            {
                public static string ToNumber(string raw)
                {
                    if (string.IsNullOrWhiteSpace(raw))
                        return null;

                    raw = raw.ToUpperInvariant();

                    var newNumber = new StringBuilder();
                    foreach (var c in raw)
                    {
                        if (" -0123456789".Contains(c))
                            newNumber.Append(c);
                        else
                        {
                            var result = TranslateToNumber(c);
                            if (result != null)
                                newNumber.Append(result);
                            // Bad character?
                            else
                                return null;
                        }
                    }
                    return newNumber.ToString();
                }

                static bool Contains(this string keyString, char c)
                {
                    return keyString.IndexOf(c) >= 0;
                }

                static readonly string[] digits = {
                    "ABC", "DEF", "GHI", "JKL", "MNO", "PQRS", "TUV", "WXYZ"
                };

                static int? TranslateToNumber(char c)
                {
                    for (int i = 0; i < digits.Length; i++)
                    {
                        if (digits[i].Contains(c))
                            return 2 + i;
                    }
                    return null;
                }
            }
        }

  選擇 [檔案] > [儲存] (或按下 **&#8984; + S**)，將變更儲存到 **PhoneTranslator.cs**，然後關閉檔案。

1. 在 [Solution Pad] 中，選取 **Phoneword** 專案、按一下滑鼠右鍵，然後選取 [新增] > [新增檔案]：

  ![](quickstart-images/xs/add-new-interface.png "加入新檔案")

1. 在 [新增檔案] 對話方塊中，選取 [一般] > [空的介面]、將新檔案命名為 **IDialer**，然後按一下 [新增] 按鈕：

  ![](quickstart-images/xs/add-idialer-interface.png "加入新介面")

1. 在 **IDialer.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會定義必須在每個平台上實作的 `Dial` 方法，才能撥打轉譯的電話號碼：

        namespace Phoneword
        {
            public interface IDialer
            {
                bool Dial(string number);
            }
        }

  選擇 [檔案] > [儲存] (或按下 **&#8984; + S**)，將變更儲存到 **IDialer.cs**，然後關閉檔案。

  > [!NOTE]
> 現已完成應用程式的通用程式碼。 平台專屬的電話撥號員程式碼現在會當作 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) 實作。

1. 在 [Solution Pad] 中，選取 **Phoneword.iOS** 專案、按一下滑鼠右鍵，然後選取 [新增] > [新增檔案]：

  ![](quickstart-images/xs/add-new-file-ios.png "加入新檔案")

1. 在 [新增檔案] 對話方塊中，選取 [一般] > [空的類別]、將新檔案命名為 **PhoneDialer**，然後按一下 [新增] 按鈕：

  ![](quickstart-images/xs/new-phonedialer-ios.png "加入新類別")

1. 在 **PhoneDialer.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會建立將在 iOS 平台上使用的 `Dial` 方法，才能撥打轉譯的電話號碼：

        using Foundation;
        using Phoneword.iOS;
        using UIKit;
        using Xamarin.Forms;

        [assembly: Dependency(typeof(PhoneDialer))]
        namespace Phoneword.iOS
        {
            public class PhoneDialer : IDialer
            {
                public bool Dial(string number)
                {
                    return UIApplication.SharedApplication.OpenUrl (
                        new NSUrl ("tel:" + number));
                }
            }
        }

  選擇 [檔案] > [儲存] (或按下 **&#8984; + S**)，將變更儲存到 **PhoneDialer.cs**，然後關閉檔案。

1. 在 [Solution Pad] 中，選取 **Phoneword.Droid** 專案、按一下滑鼠右鍵，然後選取 [新增] > [新增檔案]：

  ![](quickstart-images/xs/add-new-file-android.png "加入新檔案")

1. 在 [新增檔案] 對話方塊中，選取 [一般] > [空的類別]、將新檔案命名為 **PhoneDialer**，然後按一下 [新增] 按鈕：

  ![](quickstart-images/xs/new-phonedialer-android.png "加入新類別")

1. 在 **PhoneDialer.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會建立將在 Android 平台上使用的 `Dial` 方法，才能撥打轉譯的電話號碼：

        using Android.Content;
        using Android.Telephony;
        using Phoneword.Droid;
        using System.Linq;
        using Xamarin.Forms;
        using Uri = Android.Net.Uri;

        [assembly: Dependency(typeof(PhoneDialer))]
        namespace Phoneword.Droid
        {
            public class PhoneDialer : IDialer
            {
                public bool Dial(string number)
                {
                    var context = MainActivity.Instance;
                    if (context == null)
                        return false;

                    var intent = new Intent (Intent.ActionCall);
                    intent.SetData (Uri.Parse ("tel:" + number));

                    if (IsIntentAvailable (context, intent)) {
                        context.StartActivity (intent);
                        return true;
                    }

                    return false;
                }

                public static bool IsIntentAvailable(Context context, Intent intent)
                {
                    var packageManager = context.PackageManager;

                    var list = packageManager.QueryIntentServices (intent, 0)
                        .Union (packageManager.QueryIntentActivities (intent, 0));

                    if (list.Any ())
                        return true;

                    var manager = TelephonyManager.FromContext (context);
                    return manager.PhoneType != PhoneType.None;
                }
            }
        }

  選擇 [檔案] > [儲存] (或按下 **&#8984; + S**)，將變更儲存到 **PhoneDialer.cs**，然後關閉檔案。

1. 在 [Solution Pad] 的 [Phoneword.Droid] 專案中，按兩下 [MainActivity.cs] 來開啟它、移除所有範本程式碼，然後以下列程式碼取代：

        using Android.App;
        using Android.Content.PM;
        using Android.OS;

        namespace Phoneword.Droid
        {
            [Activity(Label = "Phoneword", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
            public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
            {
                internal static MainActivity Instance { get; private set; }

                protected override void OnCreate(Bundle bundle)
                {
                    TabLayoutResource = Resource.Layout.Tabbar;
                    ToolbarResource = Resource.Layout.Toolbar;

                    base.OnCreate(bundle);

                    Instance = this;
                    global::Xamarin.Forms.Forms.Init(this, bundle);
                    LoadApplication(new App());
                }
            }
        }        

  選擇 [檔案] > [儲存] (或按下 **&#8984; + S**) 以儲存對 **MainActivity.cs**所做的變更，然後關閉檔案。

1. 在 **Solution Pad** 中，展開 [Properties] 資料夾，然後按兩下 **AndroidManifest.xml** 檔案：

  ![](quickstart-images/xs/android-manifest.png "開啟 Android 資訊清單")

1. 在 [必要權限] 區段中，啟用 **CallPhone** 權限。 這可為應用程式提供撥打電話的權限：

  ![](quickstart-images/xs/android-manifest-changed.png "啟用 CallPhone 權限")

  選擇 [檔案] > [儲存] (或按下 **&#8984; + S**)，將變更儲存到 **AndroidManifest.xml**，然後關閉檔案。

1. 在 **Solution Pad** 中，從 **Phoneword** 專案移除 **PhonewordPage** 類別。 建立專案，以及不再需要專案時，會自動新增此頁面。
1. 在 Visual Studio for Mac 中，選取 [建置] > [全部建置] 功能表項目 (或按下 **&#8984; + B**)。 系統將建置應用程式，且 Visual Studio for Mac 工具列中將會出現成功訊息。

  ![](quickstart-images/xs/build-successful.png "建置成功")

1. 如果發生錯誤，請重複上述步驟並更正任何錯誤，直到應用程式建置成功為止。
1. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕) 以啟動 iOS 模擬器內的應用程式：

  ![](quickstart-images/xs/start.png "Visual Studio for Mac 工具列")
  ![](quickstart-images/xs/phoneword-result-ios.png "iOS 模擬器")

  注意：iOS 模擬器不支援通話功能。

1. 在 [Solution Pad] 中，選取 **Phoneword.Droid** 專案、按一下滑鼠右鍵，然後選取 [設定為啟始專案]：

  ![](quickstart-images/xs/set-startup-project.png "設定為啟始專案")

1. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕) 以啟動 Android 模擬器內的應用程式：

  ![](quickstart-images/xs/phoneword-result-android.png "Android 模擬器")

  注意：Android 模擬器不支援通話功能。

-----

恭喜您完成 Xamarin.Forms 應用程式。 本指南中的[下一個主題](~/xamarin-forms/get-started/hello-xamarin-forms/deepdive.md)會檢閱此逐步解說中所採取的步驟，以了解使用 Xamarin.Forms 開發應用程式的基本概念。


## <a name="related-links"></a>相關連結

- [透過 DependencyService 存取原生功能](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
- [Phoneword (範例)](https://developer.xamarin.com/samples/xamarin-forms/Phoneword/)
