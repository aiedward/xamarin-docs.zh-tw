---
title: 繫結 .JAR
description: 本逐步解說將提供逐步指示，說明如何從 Android 建立 Xamarin. Android JAVA 系結程式庫。JAR 檔案。
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: 2b8641a3b484da643457366605e9a5f33c97c3b5
ms.sourcegitcommit: d1980b2251999224e71c1289e4b4097595b7e261
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928577"
---
# <a name="binding-a-jar"></a>繫結 .JAR

> [!IMPORTANT]
> 我們目前正在調查 Xamarin 平臺上的自訂系結使用方式。 請填寫 [**這份問卷**](https://www.surveymonkey.com/r/KKBHNLT) ，以通知未來的開發工作。

_本逐步解說將提供逐步指示，說明如何從 Android 建立 Xamarin. Android JAVA 系結程式庫。JAR 檔案。_

## <a name="overview"></a>概觀

Android 社區提供許多您可能想要在應用程式中使用的 JAVA 程式庫。 這些 JAVA 程式庫通常會封裝在中。JAR (JAVA Archive) 格式，但您可以封裝。將它 JAR 至 *JAVA* 系結程式庫，讓 Xamarin. Android 應用程式可以使用其功能。 JAVA 系結程式庫的目的是要在中建立 Api。JAR 檔案可透過自動產生的程式碼包裝函式提供給 c # 程式碼使用。

Xamarin 工具可以從一或多個輸入產生系結程式庫。JAR 檔案。 系結程式庫 (。DLL 元件) 包含下列各項：

- 原始的內容。JAR 檔案 (s) 。

- Managed 可呼叫包裝函式 (MCW) ，也就是在中包裝對應 JAVA 類型的 c # 類型。JAR 檔案 (s) 。

產生的 MCW 程式碼會使用 JNI (JAVA 原生介面，) 將 API 呼叫轉送至基礎。JAR 檔案。 您可以建立任何的系結程式庫。原本以 Android 為目標的 JAR 檔案 (請注意，Xamarin 工具目前不支援) 非 Android JAVA 程式庫的系結。 您也可以選擇建立系結程式庫，而不包含的內容。JAR 檔案，讓 DLL 具有的相依性。JAR （執行時間）。

在本指南中，我們將逐步解說建立單一系結程式庫的基本概念。JAR 檔案。 我們將示範一個範例，其中所有專案都 &ndash; 是正確的，也就是不需要任何自訂或偵錯工具。
[使用中繼資料建立](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) 系結會提供更先進的案例範例，其中的系結程式不是完全自動的，而且需要一些手動介入。 如需使用基本程式碼範例) 的一般 JAVA 程式庫系結總覽 (，請參閱系結 [java 程式庫](~/android/platform/binding-java-library/index.md)。

## <a name="walkthrough"></a>逐步介紹

在下列逐步解說中，我們將建立 [Picasso](https://square.github.io/picasso/)的系結程式庫，這是熱門的 Android。提供影像載入和快取功能的 JAR。 我們將使用下列步驟來系結 **picasso-2** ，以建立可在 Xamarin. Android 專案中使用的新 .net 元件：

1. 建立新的 JAVA 系結程式庫專案。

2. 加入。JAR 檔案至專案。

3. 為設定適當的組建動作。JAR 檔案。

4. 選擇目標 framework。JAR 支援。

5. 建立系結程式庫。

建立系結程式庫之後，我們會開發一個小型的 Android 應用程式，以示範我們在系結程式庫中呼叫 Api 的能力。
在此範例中，我們想要存取 **picasso-2** 的方法：

```java
package com.squareup.picasso

public class Picasso
{
    ...
    public static Picasso with (Context context) { ... };
    ...
    public RequestCreator load (String path) { ... };
    ...
}
```

在產生 **picasso-2** 的系結程式庫之後，我們可以從 c # 呼叫這些方法。 例如：

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("https://mydomain.myimage.jpg")
    .Into (imageView);

```

### <a name="creating-the-bindings-library"></a>建立系結程式庫

開始進行下列步驟之前，請先下載 [picasso-2](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar)。

首先，建立新的系結程式庫專案。 在 Visual Studio for Mac 或 Visual Studio 中，建立新的方案，然後選取 [Android 系結連結 *庫* ] 範本。  (本逐步解說中的螢幕擷取畫面使用 Visual Studio，但 Visual Studio for Mac 非常類似。 ) 將方案命名為 **JarBinding** ：

[![建立 JarBinding 程式庫專案](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

範本包含一個 **jar** 資料夾，您可以在其中加入。JAR (s) 至系結程式庫專案。 在 [ **jar** ] 資料夾上按一下滑鼠右鍵，然後選取 [ **新增 > 現有專案** ：

[![加入現有專案](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

流覽至稍早下載的 **picasso-2** 檔案，選取它，然後按一下 [ **新增** ]：

[![選取 jar 檔案，然後按一下 [新增]](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

確認已成功將 **picasso-2.** ... x..。

[![Jar 已新增至專案](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

當您建立 JAVA 系結程式庫專案時，您必須指定是否要。JAR 會內嵌于系結程式庫中，或分開封裝。 若要這樣做，您可以指定下列其中一個 *組建動作* ：

- **EmbeddedJar** &ndash; ，.JAR 將內嵌在系結程式庫中。

- **InputJar** &ndash; ，.JAR 將與系結程式庫分開保存。

一般來說，您會使用 **EmbeddedJar** build 動作，以便執行。JAR 會自動封裝到系結程式庫中。 這是中最簡單的 JAVA 位元組程式碼選項 &ndash; 。JAR 會轉換成 Dex 位元組程式碼，且內嵌 (以及 Managed 可呼叫包裝函式) 至您的 APK。 如果您想要保留。JAR 與系結程式庫不同，您可以使用 **InputJar** 選項;不過，您必須確定。JAR 檔案可在執行應用程式的裝置上使用。

將 [組建] 動作設定為 **EmbeddedJar** ：

[![選取 EmbeddedJar 組建動作](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

接下來，開啟專案屬性來設定 *目標 Framework* 。 如果為，則為。JAR 會使用任何 Android Api，將目標 Framework 設定為的 API 層級。JAR 預期。 通常是的開發人員。JAR 檔案會指出 (或層級) 的 API 層級。JAR 與相容。
 (如需有關目標 Framework 設定和一般 Android API 層級的詳細資訊，請參閱 [瞭解 ANDROID Api 層級](~/android/app-fundamentals/android-api-levels.md)。 ) 

設定系結程式庫的目標 API 層級 (在此範例中，我們使用 API 層級 19) ：

[![設定為 API 19 的目標 API 層級](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)

最後，建立系結程式庫。 雖然可能會顯示一些警告訊息，但系結程式庫專案應該會成功建立並產生輸出。DLL 位於下列位置： **JarBinding/bin/Debug/JarBinding.dll**

### <a name="using-the-bindings-library"></a>使用系結程式庫

以使用此。在您的 Xamarin Android 應用程式中，執行下列動作：

1. 新增系結程式庫的參考。

2. 對進行呼叫。JAR 透過 Managed 可呼叫包裝函式。

在下列步驟中，我們將建立一個最基本的應用程式，使用系結程式庫在中下載並顯示影像。「繁重的工作」 `ImageView` 是由位於中的程式碼所完成。JAR 檔案。

首先，建立使用系結程式庫的新 Xamarin. Android 應用程式。 以滑鼠右鍵按一下方案，然後選取 [ **加入新專案** ]。將新專案命名為 **BindingTest** 。 我們要在與系結程式庫相同的解決方案中建立此應用程式，以簡化此逐步解說;不過，使用系結程式庫的應用程式可以改為位於不同的解決方案中：

[![新增 BindingTest 專案](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

以滑鼠右鍵按一下 **BindingTest** 專案的 [ **參考** ] 節點，然後選取 [ **加入參考** ]：

[![Right Add 參考](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

檢查稍早建立的 **JarBinding** 專案，然後按一下 **[確定]** ：

[![選取 JarBinding 專案](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

開啟 **BindingTest** 專案的 [ **參考** ] 節點，並確認 **JarBinding** 參考是否存在：

[![JarBinding 出現在 [參考] 底下](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

將 ( **BindingTest** 配置修改為 **主要. .Axml** ) ，使其具有單一 `ImageView` ：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <ImageView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/imageView" />
</LinearLayout>
```

將下列 `using` 語句新增至 **MainActivity.cs** ，可 &ndash; 讓您輕鬆地存取位於系結程式庫中以 JAVA 為基礎之類別的方法 `Picasso` ：

```csharp
using Com.Squareup.Picasso;
```

修改 `OnCreate` 方法，讓它使用 `Picasso` 類別從 URL 載入影像，並將它顯示在中 `ImageView` ：

```csharp
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);
        ImageView imageView = FindViewById<ImageView>(Resource.Id.imageView);

        // Use the Picasso jar library to load and display this image:
        Picasso.With (this)
            .Load ("https://i.imgur.com/DvpvklR.jpg")
            .Into (imageView);
    }
}
```

編譯並執行 **BindingTest** 專案。 應用程式將會啟動，並在短暫延遲之後 (視) 的網路條件而定，它應該下載並顯示類似下列螢幕擷取畫面的影像：

[![BindingTest 正在執行的螢幕擷取畫面](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

恭喜！ 您已成功系結 JAVA 程式庫。JAR 並在您的 Xamarin Android 應用程式中使用。

## <a name="summary"></a>摘要

在這個逐步解說中，我們建立了協力廠商的系結程式庫。JAR 檔案，將系結程式庫新增至最基本的測試應用程式，然後執行應用程式來確認 c # 程式碼可以呼叫位於的 JAVA 程式碼。JAR 檔案。

## <a name="related-links"></a>相關連結

- [建立 JAVA 系結程式庫 (影片) ](https://university.xamarin.com/classes#10090)
- [繫結 Java 程式庫](~/android/platform/binding-java-library/index.md)
