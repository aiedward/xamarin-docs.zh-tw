---
title: "Hello 損耗"
description: "建立第一個 Android 戴上應用程式和損耗模擬器或裝置上執行。 本逐步解說提供建立小型處理按鈕按一下動作，並按一下計數器顯示損耗裝置上的 Android 穿專案的逐步指示。 它會說明如何偵錯使用損耗模擬器或 Android 手機連接藍芽損耗裝置的應用程式。 它也提供 Android 有一組的偵錯秘訣。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 86BCD0E7-E9DC-40F1-9B44-887BC51BB48D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 8eed2d6b825a6e6dd7e956bf901246b9a630081a
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="hello-wear"></a>Hello 損耗

_建立第一個 Android 戴上應用程式和損耗模擬器或裝置上執行。本逐步解說提供建立小型處理按鈕按一下動作，並按一下計數器顯示損耗裝置上的 Android 穿專案的逐步指示。它會說明如何偵錯使用損耗模擬器或 Android 手機連接藍芽損耗裝置的應用程式。它也提供 Android 有一組的偵錯秘訣。_

![損耗應用程式的螢幕擷取畫面，才能完成本教學課程](hello-wear-images/example.png)

## <a name="your-first-wear-app"></a>第一個損耗應用程式

請遵循下列步驟來建立第一個 Xamarin.Android 損耗應用程式：

### <a name="1-create-a-new-android-project"></a>1.建立新的 Android 專案

建立新**戴上的 Android 應用程式**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![在 [新增專案] 對話方塊建立新 Android 戴上的應用程式](hello-wear-images/vs/new-solution-sml.png)](hello-wear-images/vs/new-solution.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![建立新方案對話方塊中的新 Android 戴上的應用程式](hello-wear-images/xs/new-solution-sml.png)](hello-wear-images/xs/new-solution.png#lightbox)

-----


此範本會自動包含**Xamarin Android 穿戴程式庫**NuGet （和相依性），您就可以存取特定損耗的 widget。 如果您沒有看到損耗範本，請檢閱[安裝和設定](~/android/wear/get-started/installation.md)再次確認您已安裝支援的 Android SDK 的指南。 

### <a name="2-choose-the-correct-target-framework"></a>2.選擇正確**目標 Framework**

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

請確認**最小值 Android 目標**設**Android 5.0 （棒棒糖符號）**或更新版本： 

[![將目標 Framework 設定為 Visual Studio 中的 Android 5.0](hello-wear-images/vs/target-framework-sml.png)](hello-wear-images/vs/target-framework.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

請確定目標架構設為**Android 5.0 （棒棒糖符號）**或更新版本：

[![設定 Android 5.0 Visual Studio 中的目標 Framework for Mac](hello-wear-images/xs/target-framework-sml.png)](hello-wear-images/xs/target-framework.png#lightbox)

-----

如需有關如何設定目標 framework 的詳細資訊，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)。


### <a name="3-edit-the-mainaxml-layout"></a>3.編輯**Main.axml**版面配置

設定配置以包含`TextView`和`Button`範例： 

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent">
  <ScrollView
    android:id="@+id/scroll"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:background="#000000"
    android:fillViewport="true">
    <LinearLayout
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:orientation="vertical">
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:text="Main Activity"
        android:textSize="36sp"
        android:textColor="#006600" />
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:textColor="#cccccc"
        android:id="@+id/result" />
      <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="showNotification"
        android:text="Click Me!"
        android:id="@+id/click_button" />
    </LinearLayout>
  </ScrollView>
</FrameLayout>
```

### <a name="4-edit-the-mainactivitycs-source"></a>4.編輯**Weatherapp**來源

加入程式碼來遞增計數器，並按下按鈕時顯示： 

```csharp
[Activity (Label = "WearTest", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
  int count = 1;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    Button button = FindViewById<Button> (Resource.Id.click_button);
    TextView text = FindViewById<TextView> (Resource.Id.result);

    button.Click += delegate {
      text.Text = string.Format ("{0} clicks!", count++);
    };
  }
}
```

### <a name="5-setup-an-emulator-or-device"></a>5.安裝程式在模擬器或裝置

下一個步驟是設定來部署和執行應用程式在模擬器或裝置。 如果您尚不熟悉的程序部署和執行的 Xamarin.Android 應用程式一般情況下，請參閱[Hello，Android 快速入門](~/android/get-started/hello-android/hello-android-quickstart.md)。

如果您沒有 Android 戴上的裝置，例如 Android 穿 Smartwatch，您可以在模擬器上執行應用程式。 如需損耗應用程式在模擬器上的偵錯資訊，請參閱[偵錯的模擬器上的 Android 穿](~/android/wear/deploy-test/debug-on-emulator.md)。

如果您有例如 Android 穿 Smartwatch 戴上的 Android 裝置，您可以執行應用程式，而不是使用模擬器的裝置上。 如需損耗裝置上偵錯的詳細資訊，請參閱[戴上的裝置上偵錯](~/android/wear/deploy-test/debug-on-device.md)。


### <a name="6-run-the-android-wear-app"></a>6.執行 Android 損耗應用程式

Android 穿裝置應該會出現在裝置下拉式功能表。 請務必選擇正確的設定有的 Android 裝置或 AVD 之前您開始偵錯。 選取裝置之後, 按一下 [開始] 按鈕，以將應用程式部署至模擬器或裝置。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![在 Visual Studio 裝置功能表中選擇穿 AVD](hello-wear-images/vs/choose-wear-sim.png)](hello-wear-images/vs/choose-wear-sim.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![在 Visual Studio 中選擇穿 AVD Mac 裝置功能表](hello-wear-images/xs/choose-wear-sim.png)](hello-wear-images/xs/choose-wear-sim.png#lightbox)

-----

您可能會看到**一分鐘時間...**在第一個訊息 （或其他插入的螢幕）： 

![監看模擬器會顯示一分鐘時間...](hello-wear-images/please-wait.png)

如果您使用監看式模擬器時，它可以需要一些時間才能啟動應用程式。 當您使用藍芽時，花費更多時間來部署應用程式，透過 USB 方式。 （比方說，它需要大約 5 分鐘將此應用程式部署至藍芽連線到 Nexus 5 電話 LG G 監看式）。

應用程式已成功部署之後，損耗裝置的畫面應該會顯示螢幕，如下所示：

[![損耗應用程式的初始畫面](hello-wear-images/mainactivity-screen.png)](hello-wear-images/mainactivity-screen.png#lightbox)

點選**按一下 我 ！** 按鈕表面損耗裝置和每個點選計數遞增值，請參閱：

[![有螢幕擷取畫面 3 按下後的應用程式](hello-wear-images/mainactivity-counts.png)](hello-wear-images/mainactivity-counts.png#lightbox)


## <a name="next-steps"></a>後續步驟

簽出[穿範例](https://developer.xamarin.com/samples/android/Android%20Wear/)包括 Android 有附屬 Phone 應用程式的應用程式。

當您準備好散發應用程式時，請參閱[使用封裝](~/android/wear/deploy-test/packaging.md)。


## <a name="related-links"></a>相關連結

- [按一下 我的應用程式 （範例）](https://developer.xamarin.com/samples/monodroid/wear/WearTest/)
