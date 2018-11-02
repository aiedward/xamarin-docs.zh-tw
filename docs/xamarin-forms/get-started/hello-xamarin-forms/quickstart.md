---
title: Xamarin.Forms Quickstart
description: 本文說明如何建立可將使用者輸入的英數字元電話號碼轉譯成數字電話號碼，然後再撥打該號碼的應用程式。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 3f2f9c2d-d204-43bc-8c8a-a55ce1e6d2c8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/13/2018
ms.openlocfilehash: fd9b3032166470a960e97f1754d2133a5ef22631
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109363"
---
# <a name="xamarinforms-quickstart"></a>Xamarin.Forms Quickstart

本逐步解說示範如何建立可將使用者輸入的英數字元電話號碼轉譯成數字電話號碼，然後再撥打該號碼的應用程式。 最終的應用程式如下所示：

[![](quickstart-images/intro-app-examples-sml.png "Phoneword 應用程式")](quickstart-images/intro-app-examples.png#lightbox "Phoneword 應用程式")

::: zone pivot="windows"

## <a name="get-started-with-visual-studio"></a>Visual Studio 使用者入門

1. 在 [開始] 畫面中，啟動 Visual Studio。 這樣會開啟 [開始] 頁面：

    ![](quickstart-images/vs/start-page.png "Visual Studio")

2. 在 Visual Studio 中，按一下 [建立新專案] 以建立新的專案：

    ![](quickstart-images/vs/new-solution.png "新增專案")

3. 在 [新增專案] 對話方塊中，按一下 [跨平台]、選取 [行動應用程式 (Xamarin.Forms)] 範本、將 [名稱] 設定為 **Phoneword**、選擇專案的適當位置，然後按一下 [確定] 按鈕：

    ![](quickstart-images/vs/new-project.w157.png "跨平台專案範本")

    > [!NOTE]
    > 本快速入門中的 C# 和 XAML 程式碼片段要求將解決方案命名為 **Phoneword**。
    > 使用不同的解決方案名稱，會導致當您將這些指示中的程式碼複製到專案時，產生許多的建置錯誤。

4. 在 [新增跨平台應用程式] 對話方塊中，按一下 [空白應用程式]，並選取 [.NET Standard] 作為 [程式碼共用策略]，然後按一下 [確定] 按鈕：

    ![](quickstart-images/vs/new-app.png "新增跨平台應用程式")

5. 在 [方案總管] 的 **Phoneword** 專案中，按兩下 **MainPage.xaml**，將其開啟：

    ![](quickstart-images/vs/open-mainpage-xaml.png "開啟 MainPage.xaml")

6. 在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會以宣告的方式定義頁面的使用者介面：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.MainPage">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <Label Text="Enter a Phoneword:" />
          <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
          <Button Text="Translate" Clicked="OnTranslate" />
          <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
        </StackLayout>
    </ContentPage>
    ```

    按下 **CTRL+S** 以將變更儲存到 **MainPage.xaml**，然後關閉檔案。

7. 在 [方案總管] 中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs**，將其開啟：

    ![](quickstart-images/vs/open-mainpage-codebehind.png "開啟 MainPage.xaml.cs")

8. 在 **MainPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 系統將會執行 `OnTranslate` 和 `OnCall` 方法，以回應要在使用者介面中分別按一下的 [轉譯] 和 [撥號] 按鈕：

    ```csharp
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
                translatedNumber = PhonewordTranslator.ToNumber (phoneNumberText.Text);
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
    ```

    > [!NOTE]
    > 此時嘗試建置應用程式將會導致發生錯誤，而將在稍後修正。

    按下 **CTRL+S** 以將變更儲存到 **MainPage.xaml.cs**，然後關閉檔案。

9. 在 [方案總管] 中，以滑鼠右鍵按一下 **Phoneword** 專案，然後選取 [加入] > [新項目]：

    ![](quickstart-images/vs/add-new-item.png "加入新項目")

10. 在 [加入新項目] 對話方塊中，選取 [Visual C#] > [程式碼] > [類別]、將新檔案命名為 **PhoneTranslator**，然後按一下 [新增] 按鈕：

    ![](quickstart-images/vs/add-translator-class.w157.png "加入新類別")

11. 在 **PhoneTranslator.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會將電話文字轉譯成電話號碼：

    ```csharp
    using System.Text;

    namespace Phoneword
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
    ```

    按下 **CTRL+S**，將變更儲存到 **PhoneTranslator.cs**，然後關閉檔案。

12. 在 [方案總管] 中，以滑鼠右鍵按一下 **Phoneword** 專案，然後選取 [加入] > [新項目]：

    ![](quickstart-images/vs/add-new-item.png "加入新項目")

13. 在 [加入新項目] 對話方塊中，選取 [Visual C#] > [程式碼] > [介面]、將新檔案命名為 **IDialer**，然後按一下 [新增] 按鈕：

    ![](quickstart-images/vs/add-idialer-interface.w157.png "加入新介面")

14. 在 **IDialer.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會定義必須在每個平台上實作的 `Dial` 方法，才能撥打轉譯的電話號碼：

    ```csharp
    namespace Phoneword
    {
        public interface IDialer
        {
            bool Dial(string number);
        }
    }
    ```

    按下 **CTRL+S**，將變更儲存到 **IDialer.cs**，然後關閉檔案。

    > [!NOTE]
    > 現已完成應用程式的通用程式碼。 平台專屬的電話撥號員程式碼現在會當作 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) 實作。

15. 在 [方案總管] 中，以滑鼠右鍵按一下 **Phoneword.iOS** 專案，然後選取 [加入] > [新項目]：

    ![](quickstart-images/vs/add-new-item-ios.png "加入新項目")

16. 在 [加入新項目] 對話方塊中，選取 [Apple] > [程式碼] > [類別]、將新檔案命名為 **PhoneDialer**，然後按一下 [新增] 按鈕：

    ![](quickstart-images/vs/new-phone-dialer-ios.w157.png "加入新類別")

17. 在 **PhoneDialer.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會建立將在 iOS 平台上使用的 <code>Dial</code> 方法，才能撥打轉譯的電話號碼：

    ```csharp
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
    ```

    按下 **CTRL+S**，將變更儲存到 **PhoneDialer.cs**，然後關閉檔案。

18. 在 [方案總管] 中，以滑鼠右鍵按一下 **Phoneword.Android** 專案，然後選取 [加入] > [新項目]：

    ![](quickstart-images/vs/add-new-item-android.png "加入新項目")

19. 在 [加入新項目] 對話方塊中，選取 [Visual C#] > [Android] > [類別]、將新檔案命名為 **PhoneDialer**，然後按一下 [新增] 按鈕：

    ![](quickstart-images/vs/new-phone-dialer-android.w157.png "加入新類別")

20. 在 **PhoneDialer.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會建立將在 Android 平台上使用的 `Dial` 方法，才能撥打轉譯的電話號碼：

    ```csharp
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

                var intent = new Intent (Intent.ActionDial);
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
    ```

    請注意，此程式碼假設您是使用最新的 Android API。 按下 **CTRL+S**，將變更儲存到 **PhoneDialer.cs**，然後關閉檔案。

21. 在 [方案總管] 的 [Phoneword.Android] 專案中，按兩下 [MainActivity.cs] 來開啟它、移除所有範本程式碼，然後以下列程式碼取代：

    ```csharp
    using Android.App;
    using Android.Content.PM;
    using Android.OS;

    namespace Phoneword.Droid
    {
        [Activity(Label = "Phoneword", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true,
                  ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
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
    ```

    按下 **CTRL+S** 來儲存對 **MainActivity.cs** 所做的變更，然後關閉檔案。

22. 在 [方案總管] 的 **Phoneword.Android** 專案中，按兩下 [屬性]，然後選取 [Android 資訊清單] 索引標籤：

    ![](quickstart-images/vs/android-manifest.png "開啟 Android 資訊清單")

23. 在 [必要權限] 區段中，啟用 **CALL_PHONE** 權限。 這可為應用程式提供撥打電話的權限：

    ![](quickstart-images/vs/android-manifest-changed.png "啟用 CallPhone 權限")

    按下 **CTRL+S**，將變更儲存到資訊清單，然後關閉檔案。

24. 以滑鼠右鍵按一下 Android 應用程式專案，然後選擇 [設定為啟始專案]。

25. 使用「綠色箭號」工具列按鈕執行 Android 應用程式，或從功能表選取 [偵錯] > [開始偵錯]。

    > [!WARNING]
    > 所有模擬器都不支援通話功能，因此該功能可能無法運作。

26. 如果您有 iOS 裝置，而且符合 Xamarin.Forms 開發的 Mac 系統需求，請使用類似的技術將應用程式部署至 iOS 裝置。 或者，您也可以將應用程式部署至 [iOS 遠端模擬器](~/tools/ios-simulator/index.md)。

::: zone-end
::: zone pivot="macos"

## <a name="get-started-with-visual-studio-for-mac"></a>Visual Studio for Mac 使用者入門

1. 啟動 Visual Studio for Mac，然後在起始頁面上按一下 [新增專案] 以建立新專案：

    ![](quickstart-images/xs/new-solution.png "新增方案")

2. 在 [為新專案選擇範本] 對話方塊中，按一下 [多平台] > [應用程式]、選取 [空白的 Forms App] 範本，然後按 [下一步] 按鈕：

    ![](quickstart-images/xs/choose-template.png "選擇範本")

3. 在 [設定空白的表單應用程式] 對話方塊中，將新的應用程式命名為 **Phoneword**、確保已選取 [使用 .NET Standard] 選項按鈕，然後按一下 [下一步] 按鈕：

    ![](quickstart-images/xs/configure-app.png "設定表單應用程式")

4. 在 [設定新的空白表單應用程式] 對話方塊中，將 [方案名稱] 和 [專案名稱] 設定為 **Phoneword**、選擇專案的合適位置，然後按一下 [建立] 按鈕以建立專案：

    ![](quickstart-images/xs/configure-project.png "設定表單專案")

    > [!NOTE]
    > 本快速入門中的 C# 和 XAML 程式碼片段要求將解決方案命名為 **Phoneword**。
    > 使用不同的解決方案名稱，會導致當您將這些指示中的程式碼複製到專案時，產生許多的建置錯誤。

5. 在 [Solution Pad] 中，按兩下 **MainPage.xaml** 以將其開啟：

    ![](quickstart-images/xs/open-mainpage-xaml.png "開啟 MainPage.xaml")

6. 在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會以宣告的方式定義頁面的使用者介面：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.MainPage">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <Label Text="Enter a Phoneword:" />
          <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
          <Button Text="Translate" Clicked="OnTranslate" />
          <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
        </StackLayout>
    </ContentPage>
    ```

    選擇 [檔案] > [儲存] (或按下 **&#8984; + S**) 以將變更儲存到 **MainPage.xaml**，然後關閉檔案。

7. 在 [Solution Pad] 中，按兩下 **MainPage.xaml.cs** 以將其開啟：

    ![](quickstart-images/xs/open-mainpage-codebehind.png "開啟 MainPage.xaml.cs")

8. 在 **MainPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 系統將會執行 `OnTranslate` 和 `OnCall` 方法，以回應要在使用者介面中分別按一下的 [轉譯] 和 [撥號] 按鈕：

    ```csharp
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
                translatedNumber = PhonewordTranslator.ToNumber (phoneNumberText.Text);
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
    ```

    > [!NOTE]
    > 此時嘗試建置應用程式將會導致發生錯誤，而將在稍後修正。

    選擇 [檔案] > [儲存] (或按下 **&#8984; + S**) 以將變更儲存到 **MainPage.xaml.cs**，然後關閉檔案。

9. 在 [Solution Pad] 中，選取 **Phoneword** 專案、按一下滑鼠右鍵，然後選取 [新增] > [新增檔案]：

    ![](quickstart-images/xs/add-new-translator-file.png "加入新檔案")

10. 在 [新增檔案] 對話方塊中，選取 [一般] > [空的類別]、將新檔案命名為 **PhoneTranslator**，然後按一下 [新增] 按鈕：

    ![](quickstart-images/xs/add-translator-class.png "加入新類別")

11. 在 **PhoneTranslator.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會將電話文字轉譯成電話號碼：

    ```csharp
    using System.Text;

    namespace Phoneword
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
    ```

    選擇 [檔案] > [儲存] (或按下 **&#8984; + S**)，將變更儲存到 **PhoneTranslator.cs**，然後關閉檔案。

12. 在 [Solution Pad] 中，選取 **Phoneword** 專案、按一下滑鼠右鍵，然後選取 [新增] > [新增檔案]：

    ![](quickstart-images/xs/add-new-interface.png "加入新檔案")

13. 在 [新增檔案] 對話方塊中，選取 [一般] > [空的介面]、將新檔案命名為 **IDialer**，然後按一下 [新增] 按鈕：

    ![](quickstart-images/xs/add-idialer-interface.png "加入新介面")

14. 在 **IDialer.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會定義必須在每個平台上實作的 `Dial` 方法，才能撥打轉譯的電話號碼：

    ```csharp
    namespace Phoneword
    {
        public interface IDialer
        {
            bool Dial(string number);
        }
    }
    ```
    選擇 [檔案] > [儲存] (或按下 **&#8984; + S**)，將變更儲存到 **IDialer.cs**，然後關閉檔案。

    > [!NOTE]
    > 現已完成應用程式的通用程式碼。 平台專屬的電話撥號員程式碼現在會當作 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) 實作。

15. 在 [Solution Pad] 中，選取 **Phoneword.iOS** 專案、按一下滑鼠右鍵，然後選取 [新增] > [新增檔案]：

    ![](quickstart-images/xs/add-new-file-ios.png "加入新檔案")

16. 在 [新增檔案] 對話方塊中，選取 [一般] > [空的類別]、將新檔案命名為 **PhoneDialer**，然後按一下 [新增] 按鈕：

    ![](quickstart-images/xs/new-phonedialer-ios.png "加入新類別")

17. 在 **PhoneDialer.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會建立將在 iOS 平台上使用的 `Dial` 方法，才能撥打轉譯的電話號碼：

    ```csharp
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
    ```

    選擇 [檔案] > [儲存] (或按下 **&#8984; + S**)，將變更儲存到 **PhoneDialer.cs**，然後關閉檔案。

18. 在 [Solution Pad] 中，選取 **Phoneword.Droid** 專案、按一下滑鼠右鍵，然後選取 [新增] > [新增檔案]：

    ![](quickstart-images/xs/add-new-file-android.png "加入新檔案")

19. 在 [新增檔案] 對話方塊中，選取 [一般] > [空的類別]、將新檔案命名為 **PhoneDialer**，然後按一下 [新增] 按鈕：

    ![](quickstart-images/xs/new-phonedialer-android.png "加入新類別")

20. 在 **PhoneDialer.cs** 中，移除所有範本程式碼，並取代為下列程式碼。 此程式碼會建立將在 Android 平台上使用的 `Dial` 方法，才能撥打轉譯的電話號碼：

    ```csharp
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

                var intent = new Intent (Intent.ActionDial);
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
    ```

    請注意，此程式碼假設您是使用最新的 Android API。 選擇 [檔案] > [儲存] (或按下 **&#8984; + S**)，將變更儲存到 **PhoneDialer.cs**，然後關閉檔案。

21. 在 [Solution Pad] 的 [Phoneword.Droid] 專案中，按兩下 [MainActivity.cs] 來開啟它、移除所有範本程式碼，然後以下列程式碼取代：

    ```csharp
    using Android.App;
    using Android.Content.PM;
    using Android.OS;

    namespace Phoneword.Droid
    {
        [Activity(Label = "Phoneword", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true,
                  ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
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
    ```

    選擇 [檔案] > [儲存] (或按下 **&#8984; + S**) 以儲存對 **MainActivity.cs**所做的變更，然後關閉檔案。

22. 在 **Solution Pad** 中，展開 [Properties] 資料夾，然後按兩下 **AndroidManifest.xml** 檔案：

    ![](quickstart-images/xs/android-manifest.png "開啟 Android 資訊清單")

23. 在 [必要權限] 區段中，啟用 **CallPhone** 權限。 這可為應用程式提供撥打電話的權限：

    ![](quickstart-images/xs/android-manifest-changed.png "啟用 CallPhone 權限")

    選擇 [檔案] > [儲存] (或按下 **&#8984; + S**)，將變更儲存到 **AndroidManifest.xml**，然後關閉檔案。

24. 在 Visual Studio for Mac 中，選取 [建置] > [全部建置] 功能表項目 (或按下 **&#8984; + B**)。 系統將建置應用程式，且 Visual Studio for Mac 工具列中將會出現成功訊息。

    ![](quickstart-images/xs/build-successful.png "建置成功")

25. 如果發生錯誤，請重複上述步驟並更正任何錯誤，直到應用程式建置成功為止。
26. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕) 以啟動 iOS 模擬器內的應用程式：

    ![](quickstart-images/xs/start.png "Visual Studio for Mac 工具列")
    ![](quickstart-images/xs/phoneword-result-ios.png "iOS 模擬器")

    注意：iOS 模擬器不支援通話功能。

27. 在 [Solution Pad] 中，選取 **Phoneword.Droid** 專案、按一下滑鼠右鍵，然後選取 [設定為啟始專案]：

    ![](quickstart-images/xs/set-startup-project.png "設定為啟始專案")

28. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕) 以啟動 Android 模擬器內的應用程式：

    ![](quickstart-images/xs/phoneword-result-android.png "Android 模擬器")

    > [!WARNING]
    > Android 模擬器不支援通話功能。

::: zone-end

恭喜您完成 Xamarin.Forms 應用程式。 本指南中的[下一個主題](~/xamarin-forms/get-started/hello-xamarin-forms/deepdive.md)會檢閱此逐步解說中所採取的步驟，以了解使用 Xamarin.Forms 開發應用程式的基本概念。

## <a name="related-links"></a>相關連結

- [透過 DependencyService 存取原生功能](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
- [Phoneword (範例)](https://developer.xamarin.com/samples/xamarin-forms/Phoneword/)
