---
title: Hello, Wear
description: 建立您的第一個 Android 磨損應用程式，並在磨損模擬器或裝置上執行。 本逐步解說提供逐步指示，說明如何建立小型的 Android 磨損專案來處理按鈕的點擊，並在磨損裝置上顯示 click 計數器。 它說明如何使用磨損模擬器或透過藍牙連線到 Android 手機的磨損裝置來對應用程式進行偵錯工具。 它也提供一組適用于 Android 磨損的偵錯工具提示。
ms.prod: xamarin
ms.assetid: 86BCD0E7-E9DC-40F1-9B44-887BC51BB48D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/10/2018
ms.openlocfilehash: 8b640e867e3f7adb6960ecda557ac1e410ec5799
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456933"
---
# <a name="hello-wear"></a>Hello, Wear

_建立您的第一個 Android 磨損應用程式，並在磨損模擬器或裝置上執行。本逐步解說提供逐步指示，說明如何建立小型的 Android 磨損專案來處理按鈕的點擊，並在磨損裝置上顯示 click 計數器。它說明如何使用磨損模擬器或透過藍牙連線到 Android 手機的磨損裝置來對應用程式進行偵錯工具。它也提供一組適用于 Android 磨損的偵錯工具提示。_

![本教學課程中要完成的磨損應用程式螢幕擷取畫面](hello-wear-images/example.png)

## <a name="your-first-wear-app"></a>您的第一個磨損應用程式

遵循下列步驟來建立您的第一個 Xamarin. Android 磨損應用程式：

### <a name="1-create-a-new-android-project"></a>1. 建立新的 Android 專案

建立新的 **Android 磨損應用程式**：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![在 [新增專案] 對話方塊中建立新的 Android 磨損應用程式](hello-wear-images/vs/new-solution-sml.w157.png)](hello-wear-images/vs/new-solution.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![在 [新增方案] 對話方塊中建立新的 Android 磨損應用程式](hello-wear-images/xs/new-solution-sml.png)](hello-wear-images/xs/new-solution.png#lightbox)

-----

此範本會自動包含 **Xamarin Android 穿戴式程式庫** NuGet (和相依性) ，讓您可以存取特定的小工具。 如果您沒有看到磨損範本，請參閱 [安裝和設定](~/android/wear/get-started/installation.md) 指南，以再次確認您已安裝支援的 Android SDK。 

### <a name="2-choose-the-correct-target-framework"></a>2. 選擇正確的 **目標 Framework**

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

確定 [ **最小 android 至目標** ] 設定為 [ **Android 5.0 (棒) ** 或更新版本： 

[![在 Visual Studio 中將目標 Framework 設定為 Android 5。0](hello-wear-images/vs/target-framework-sml.png)](hello-wear-images/vs/target-framework.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

確定目標 framework 設定為 **Android 5.0 (棒) ** 或更新版本：

[![在 Visual Studio for Mac 中將目標 Framework 設定為 Android 5。0](hello-wear-images/xs/target-framework-sml.png)](hello-wear-images/xs/target-framework.png#lightbox)

-----

如需設定目標 framework 的詳細資訊，請參閱 [瞭解 ANDROID API 層級](~/android/app-fundamentals/android-api-levels.md)。

### <a name="3-edit-the-mainaxml-layout"></a>3. 編輯 **主要的 .axml** 版面配置

將版面配置設定為包含 `TextView` 範例的和 `Button` ： 

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

### <a name="4-edit-the-mainactivitycs-source"></a>4. 編輯 **MainActivity.cs** 來源

新增程式碼以遞增計數器，並在按下按鈕時顯示它： 

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

### <a name="5-setup-an-emulator-or-device"></a>5. 設定模擬器或裝置

下一步是設定模擬器或裝置，以部署和執行應用程式。 如果您還不熟悉部署和執行 Xamarin Android 應用程式的程式，請參閱 [Hello，Android 快速入門](~/android/get-started/hello-android/hello-android-quickstart.md)。

如果您沒有 Android 磨損裝置（例如 Android 磨損智慧型手錶），可以在模擬器上執行應用程式。 如需有關在模擬器上偵測損耗應用程式的詳細資訊，請參閱 [在模擬器上進行 Android 磨損的調試](~/android/wear/deploy-test/debug-on-emulator.md)程式。

如果您有 android 磨損裝置，例如 Android 磨損智慧型手錶，您可以在裝置上執行應用程式，而不是使用模擬器。 如需在磨損裝置上進行偵錯工具的詳細資訊，請參閱 [在磨損裝置上的調試](~/android/wear/deploy-test/debug-on-device.md)程式。

### <a name="6-run-the-android-wear-app"></a>6. 執行 Android 磨損應用程式

Android 磨損裝置應該會出現在裝置下拉式功能表中。 開始進行調試之前，請務必選擇正確的 Android 磨損裝置或 AVD。 選取裝置之後，請按一下 [播放] 按鈕，將應用程式部署至模擬器或裝置。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![在 Visual Studio 裝置功能表中選擇磨損 AVD](hello-wear-images/vs/choose-wear-sim.png)](hello-wear-images/vs/choose-wear-sim.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![在 Visual Studio for Mac 裝置功能表中選擇磨損 AVD](hello-wear-images/xs/choose-wear-sim.png)](hello-wear-images/xs/choose-wear-sim.png#lightbox)

-----

您可能會在一開始就看到一 **分鐘** 的 ... 訊息 (或其他插入式畫面) ： 

![Watch 模擬器只會顯示一分鐘 .。。](hello-wear-images/please-wait.png)

如果您使用 watch 模擬器，則可能需要一段時間才能啟動應用程式。 當您使用 Bluetooth 時，部署應用程式所需的時間會比透過 USB 更多。  (例如，將此應用程式部署至 LG G Watch （即藍牙連線至第5部手機）需要大約5分鐘的時間。 ) 

成功部署應用程式之後，磨損裝置的畫面應該會顯示如下的畫面：

[![磨損應用程式的初始畫面](hello-wear-images/mainactivity-screen.png)](hello-wear-images/mainactivity-screen.png#lightbox)

點擊 [**按我！** ] 進入磨損裝置的臉部，並查看每個分的計數增量：

[![按一下3次後，磨損應用程式的螢幕擷取畫面](hello-wear-images/mainactivity-counts.png)](hello-wear-images/mainactivity-counts.png#lightbox)

## <a name="next-steps"></a>後續步驟

查看包含隨附電話應用程式的 [損耗範例](/samples/browse/?products=xamarin&term=Xamarin.Android%2bwear) （包括 Android 磨損應用程式）。

當您準備好散發應用程式時，請參閱 [使用封裝](~/android/wear/deploy-test/packaging.md)。

## <a name="related-links"></a>相關連結

- [按一下 [我的應用程式 (範例) ](/samples/xamarin/monodroid-samples/wear-weartest)