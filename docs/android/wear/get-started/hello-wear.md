---
title: Hello, Wear
description: 建立第一個 Android Wear 應用程式，並在 Wear 模擬器或裝置上執行。 本逐步解說提供建立小型的 Android Wear 專案處理按下按鈕後，按一下計數器會顯示在 Wear 裝置的逐步指示。 它會說明如何使用 Wear 模擬器或在 Wear 裝置透過藍牙連線到在 Android 手機的應用程式進行偵錯。 它也會提供適用於 Android Wear 的一組偵錯的祕訣。
ms.prod: xamarin
ms.assetid: 86BCD0E7-E9DC-40F1-9B44-887BC51BB48D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/10/2018
ms.openlocfilehash: a8e27063040ff91f72a1cbf932b1b277a5dee63d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61277317"
---
# <a name="hello-wear"></a>Hello, Wear

_建立第一個 Android Wear 應用程式，並在 Wear 模擬器或裝置上執行。本逐步解說提供建立小型的 Android Wear 專案處理按下按鈕後，按一下計數器會顯示在 Wear 裝置的逐步指示。它會說明如何使用 Wear 模擬器或在 Wear 裝置透過藍牙連線到在 Android 手機的應用程式進行偵錯。它也會提供適用於 Android Wear 的一組偵錯的祕訣。_

![穿戴式應用程式的螢幕擷取畫面，在本教學課程中完成](hello-wear-images/example.png)

## <a name="your-first-wear-app"></a>您的第一個穿戴式應用程式

請遵循下列步驟來建立第一個 Xamarin.Android Wear 應用程式：

### <a name="1-create-a-new-android-project"></a>1.建立新的 Android 專案

建立新**Android Wear 的應用程式**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![在 [新增專案] 對話方塊中建立新 Android Wear 的應用程式](hello-wear-images/vs/new-solution-sml.w157.png)](hello-wear-images/vs/new-solution.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![在 [新增解決方案] 對話方塊中建立新 Android Wear 的應用程式](hello-wear-images/xs/new-solution-sml.png)](hello-wear-images/xs/new-solution.png#lightbox)

-----


此範本會自動包含**Xamarin Android 到穿戴程式庫**NuGet （和相依性） 因此您必須存取 Wear 特定小工具。 如果您沒有看到 Wear 範本，請檢閱[安裝和設定](~/android/wear/get-started/installation.md)再次確認您已安裝支援的 Android SDK 的指南。 

### <a name="2-choose-the-correct-target-framework"></a>2.選擇正確**目標 Framework**

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

請確認**最低 Android 目標**設為**Android 5.0 (Lollipop)** 或更新版本： 

[![在 Visual Studio 的 Android 5.0 中設定目標 Framework](hello-wear-images/vs/target-framework-sml.png)](hello-wear-images/vs/target-framework.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

請確定目標 framework 設定為**Android 5.0 (Lollipop)** 或更新版本：

[![在 Visual Studio 的 Android 5.0 設定目標 Framework for Mac](hello-wear-images/xs/target-framework-sml.png)](hello-wear-images/xs/target-framework.png#lightbox)

-----

如需有關如何設定目標 framework 的詳細資訊，請參閱 <<c0> [ 了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)。


### <a name="3-edit-the-mainaxml-layout"></a>3.編輯**Main.axml**版面配置

設定版面配置，以包含`TextView`和`Button`範例： 

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

### <a name="4-edit-the-mainactivitycs-source"></a>4.編輯**MainActivity.cs**來源

新增遞增計數器，並按一下按鈕時顯示的程式碼： 

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

### <a name="5-setup-an-emulator-or-device"></a>5.在模擬器或裝置的安裝程式

下一個步驟是設定來部署和執行應用程式在模擬器或裝置。 如果您尚不熟悉部署和執行的程序的 Xamarin.Android 應用程式在一般情況下，請參閱[Hello，Android 快速入門](~/android/get-started/hello-android/hello-android-quickstart.md)。

如果您沒有 Android Wear 的裝置，例如 Android Wear Smartwatch，您就可以在模擬器上執行應用程式。 如需 Wear 應用程式在模擬器上的偵錯資訊，請參閱[偵錯的模擬器上的 Android Wear](~/android/wear/deploy-test/debug-on-emulator.md)。

如果您有 Android Wear 的裝置，例如 Android Wear Smartwatch 時，您可以執行應用程式，而不是使用模擬器在裝置上。 如需有關在 Wear 裝置上偵錯的詳細資訊，請參閱[穿戴式裝置上偵錯](~/android/wear/deploy-test/debug-on-device.md)。


### <a name="6-run-the-android-wear-app"></a>6.執行 Android Wear 應用程式

Android Wear 裝置應該會出現在裝置下拉式功能表中。 請務必在您開始偵錯之前，先選擇正確的 Android 穿戴式裝置或 AVD。 選取裝置之後, 按一下 應用程式部署至模擬器或裝置的 播放 按鈕。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![在 Visual Studio [裝置] 功能表中選擇 Wear AVD](hello-wear-images/vs/choose-wear-sim.png)](hello-wear-images/vs/choose-wear-sim.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![在 Visual Studio 中選擇 Wear AVD，Mac 裝置功能表](hello-wear-images/xs/choose-wear-sim.png)](hello-wear-images/xs/choose-wear-sim.png#lightbox)

-----

您可能會看到**一下...** 訊息 （或一些其他的插入式螢幕） 先： 

![觀看模擬器顯示一下...](hello-wear-images/please-wait.png)

如果您使用監看式模擬器時，可能需要一段時間才能啟動應用程式。 當您使用藍芽時，花更多的時間，比透過 USB 部署應用程式。 （例如，需要大約 5 分鐘的時間要將此應用程式部署至藍芽連線至 Nexus 5 電話 LG G 監看式。）

應用程式已成功部署之後，請在 Wear 裝置的畫面應該會顯示類似下列畫面：

[![初始畫面穿戴式應用程式](hello-wear-images/mainactivity-screen.png)](hello-wear-images/mainactivity-screen.png#lightbox)

點選  **CLICK ME ！** 按鈕表面的在 Wear 裝置和與每次點選計數遞增值，請參閱：

[![Wear 螢幕擷取畫面之後 3 按下的應用程式](hello-wear-images/mainactivity-counts.png)](hello-wear-images/mainactivity-counts.png#lightbox)


## <a name="next-steps"></a>後續步驟

請參閱[Wear 範例](https://developer.xamarin.com/samples/android/Android%20Wear/)包括 Android Wear 的應用程式與隨附的 Phone 應用程式。

當您準備好發佈您的應用程式時，請參閱[使用 封裝](~/android/wear/deploy-test/packaging.md)。


## <a name="related-links"></a>相關連結

- [按一下 我的應用程式 （範例）](https://developer.xamarin.com/samples/monodroid/wear/WearTest/)
