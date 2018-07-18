---
title: 繫結。JAR
description: 本逐步解說提供建立從 Android Xamarin.Android Java 繫結程式庫的逐步指示。JAR 檔案。
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/11/2018
ms.openlocfilehash: 2d9f2198dbb88e7614944ac73729a4e6eca42647
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798375"
---
# <a name="binding-a-jar"></a>繫結。JAR

_本逐步解說提供建立從 Android Xamarin.Android Java 繫結程式庫的逐步指示。JAR 檔案。_

## <a name="overview"></a>總覽

Android 社群提供許多您可能想要使用您的應用程式中的 Java 程式庫。 下列 Java 程式庫通常被封裝中。JAR （Java 封存檔） 格式，但是您可以將封裝。在 JAR *Java 繫結的程式庫*使其功能都是供 Xamarin.Android 應用程式。 Java 繫結的程式庫的目的是要進行中的 Api。JAR 檔案提供給透過自動產生的程式碼包裝函式的 C# 程式碼。

Xamarin 工具可以從一或多個輸入產生的繫結的程式庫。JAR 檔案。 繫結的程式庫 (。DLL 的組件） 包含下列內容： 

-   原始內容。JAR 檔案。

-   Managed 可呼叫包裝函式 (MCW)，也就是 C# 類型對應的 Java 類型內的換行。JAR 檔案。

產生的 MCW 程式碼使用您的 API 呼叫轉送給 JNI （Java 原生介面），基礎。JAR 檔案。 您可以針對任何建立繫結的程式庫。JAR 檔案原本成為目標來搭配 Android （請注意，Xamarin 工具目前不支援非 Android Java 文件庫的繫結）。 您也可以選擇建置繫結庫，但不包含的內容。JAR 檔案，以便 DLL 具有相依性。在執行階段的 JAR。

本指南中，我們將逐步完成建立單一繫結文件庫的基本概念。JAR 檔案。 我們將說明與範例的所有項目會直接&ndash;也就是沒有任何自訂或偵錯的繫結需要的地方。 
[建立繫結使用的中繼資料](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)提供的繫結程序並非完全自動，而且一定程度的手動介入的情況下會需要更進階的案例範例。 如需 Java 文件庫繫結的一般情況下 （使用基本的程式碼範例） 的概觀，請參閱[繫結 Java 文件庫](~/android/platform/binding-java-library/index.md)。 

 
## <a name="walkthrough"></a>逐步解說

在下列逐步解說中，我們將建立之繫結庫[畢加索](http://square.github.io/picasso/)，常用的 Android。提供映像載入和快取功能的 JAR。 我們將使用下列步驟來繫結**畢加索 2.x.x.jar** Xamarin.Android 專案中建立新的.NET 組件，我們可以使用： 

1. 建立新的 Java 繫結的程式庫專案。

2. 新增。JAR 檔案加入專案中。

3. 設定適當的建置動作。JAR 檔案。

4. 選擇目標架構。支援的 JAR。

5. 建立繫結的程式庫。

一旦我們建立了繫結的程式庫，我們將開發小型的 Android 應用程式，示範我們呼叫繫結的文件庫中的應用程式開發介面的能力。 在此範例中，我們想要存取的方法**畢加索 2.x.x.jar**:

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

```csharp
After we generate a Bindings Library for **picasso-2.x.x.jar**,
we can call these methods from C#. For example:

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```


### <a name="creating-the-bindings-library"></a>建立繫結的程式庫

然後再開始執行下列步驟，請下載[畢加索 2.x.x.jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar)。

首先，建立新的繫結的程式庫專案。 在 Visual Studio for Mac 或 Visual Studio，請建立新的方案，然後選取*Android 繫結的程式庫*範本。 （在本逐步解說螢幕擷取畫面使用 Visual Studio 中，但 Visual Studio for Mac 是非常類似）。將方案命名**JarBinding**: 

[![建立 JarBinding 程式庫專案](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

此範本包括**Jar**資料夾，您新增程式。JAR(s) 繫結庫專案。 以滑鼠右鍵按一下**Jar**資料夾，然後選取**新增 > 現有項目**: 

[![加入現有項目](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

瀏覽至**畢加索 2.x.x.jar**先前下載的檔案、 選取它，然後按一下**新增**: 

[![選取 jar 檔案，然後按一下 [新增]](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

確認**畢加索 2.x.x.jar**檔案已成功加入至專案： 

[![Jar 加入至專案](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

當您建立 Java 繫結的程式庫專案時，您必須指定是否。JAR 是內嵌於繫結的程式庫或個別封裝。 若要這樣做，請您指定下列其中一種*建置動作*: 

-   **EmbeddedJar** &ndash; 。JAR 會內嵌在繫結的程式庫。

-   **InputJar** &ndash; 。JAR 會保持分開繫結的程式庫。

通常，您會使用**EmbeddedJar**建置動作，如此。JAR 自動封裝成繫結的程式庫中。 這是最簡單的選項&ndash;Java 位元組程式碼中的。JAR 會轉換成 Dex 位元組程式碼，而將您的 APK 內嵌 （以及管理可呼叫包裝函式）。 如果您想要保留。JAR 分開繫結的程式庫，您可以使用**InputJar**選項; 不過，您必須確定。執行您的應用程式在裝置上使用 JAR 檔案。 

建置動作設定為**EmbeddedJar**: 

[![選取 EmbeddedJar 建置動作](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

接下來，開啟 專案屬性，以設定*目標 Framework*。 如果。JAR 會使用任何的 Android 應用程式開發介面，將目標 Framework 設定為應用程式開發介面層級。預期的 JAR。 一般而言，開發人員。JAR 檔會指出哪些應用程式開發介面層級 （或層級） 的。JAR 與都相容。 (有關的目標架構設定和 Android API 層級的一般詳細資訊，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)。)

設定繫結庫層級目標應用程式開發介面 （在此範例中，我們會使用應用程式開發介面層級 19）： 

[![目標應用程式開發介面層級設定為應用程式開發介面 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)


最後，建立繫結的程式庫。 雖然某些警告訊息可能會顯示，繫結的程式庫專案應該會順利建置，而且產生的輸出。在下列位置的 DLL: **JarBinding/bin/Debug/JarBinding.dll**
    


### <a name="using-the-bindings-library"></a>使用繫結的程式庫

若要使用這種情況。DLL 中 Xamarin.Android 應用程式，然後執行下列作業：

1.  新增繫結的程式庫的參考。

2.  進行呼叫。JAR 到受管理的可呼叫包裝函式。 

在下列步驟中，我們將建立最小的應用程式以下載和顯示中的映像中使用繫結庫`ImageView`;"困難 」 由位於程式碼。JAR 檔案。 

首先，建立新的 Xamarin.Android 應用程式使用的繫結的程式庫。 以滑鼠右鍵按一下方案，然後選取**加入新的專案**; 新專案命名為**BindingTest**。 正在以簡化此逐步解說中，為繫結的程式庫相同的方案中建立此應用程式不過，使用繫結的程式庫應用程式無法，相反地，位於不同的解決方案： 

[![加入新的 BindingTest 專案](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

以滑鼠右鍵按一下**參考**節點**BindingTest**專案，然後選取**加入參考...**:

[![權限加入參考](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

請檢查**JarBinding**稍早建立的專案，然後按一下**確定**:

[![選取 JarBinding 專案](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

開啟**參考**節點**BindingTest**專案，並確認**JarBinding**參考存在於： 

[![JarBinding 之下的參考](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

修改**BindingTest**配置 (**Main.axml**)，使其具有單一`ImageView`:

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

加入下列`using`陳述式來**Weatherapp** &ndash;這樣做可輕鬆地存取 Java 為基礎的方法`Picasso`位於繫結的程式庫中的類別：

```csharp
using Com.Squareup.Picasso;
```

修改`OnCreate`方法，使其使用`Picasso`類別，可從 URL 載入影像，並顯示在`ImageView`: 

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
            .Load ("http://i.imgur.com/DvpvklR.jpg")
            .Into (imageView);
    }
}
```

編譯及執行**BindingTest**專案。 應用程式會啟動，並在短暫的延遲 （取決於網路狀況） 之後, 應該下載並顯示類似下列的螢幕擷取畫面影像：

[![螢幕擷取畫面的 BindingTest 執行](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

恭喜您！ 您已成功地繫結 Java 文件庫。JAR 和 Xamarin.Android 應用程式中使用它。
 
 
## <a name="summary"></a>總結

在本逐步解說中，我們會建立繫結的程式庫第三方。JAR 檔案，加入的基本測試應用程式中，繫結的程式庫，並且再執行應用程式，以確認我們 C# 程式碼可以呼叫位於的 Java 程式碼。JAR 檔案。 



## <a name="related-links"></a>相關連結

- [建置 Java 繫結程式庫 （影片）](https://university.xamarin.com/classes#10090)
- [繫結 Java 程式庫](~/android/platform/binding-java-library/index.md)
