---
title: 繫結。JAR
description: 本逐步解說提供建立 Xamarin.Android Java 繫結程式庫從 Android 的逐步指示。JAR 檔案。
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: 3c84b29807fd4a181ed867095645005bf9ba4df0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119990"
---
# <a name="binding-a-jar"></a>繫結。JAR

_本逐步解說提供建立 Xamarin.Android Java 繫結程式庫從 Android 的逐步指示。JAR 檔案。_

## <a name="overview"></a>總覽

Android 的社群提供了許多您可能想要在您的應用程式的 Java 程式庫。 下列 Java 程式庫通常會封裝中。JAR （Java 封存檔） 格式，但是您可以將封裝。在 JAR *Java 繫結程式庫*使其功能適用於 Xamarin.Android 應用程式。 Java 繫結程式庫的目的是進行中的 Api。若要使用的 JAR 檔案C#透過自動產生的程式碼包裝函式的程式碼。

Xamarin 工具可以產生繫結程式庫，從一或多個輸入。JAR 檔案。 繫結程式庫 (。DLL 組件） 包含下列內容： 

-   原始的內容。JAR 檔案。

-   受管理可呼叫包裝函式 (MCW)，也就是C#型別對應的 Java 型別內該換行。JAR 檔案。

產生的 MCW 程式碼使用您的 API 呼叫轉送給 JNI (Java Native Interface)，基礎。JAR 檔案。 您可以建立任何繫結程式庫。搭配 Android （請注意，Xamarin 工具目前並不支援非 Android Java 程式庫的繫結） 原本成為目標的 JAR 檔案。 您也可以選擇建立繫結程式庫，但不包含的內容。使 DLL 具有相依性 JAR 檔案。在執行階段的 JAR。

本指南中，我們會逐步建立單一繫結程式庫的基本概念。JAR 檔案。 我們將說明使用一切正確的範例&ndash;也就是任何自訂或是偵錯的繫結需要的情況。 
[建立繫結使用的中繼資料](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)提供繫結程序不是完全自動，且一定程度的手動介入的情況下是必要的更進階案例的範例。 如需 Java 程式庫與繫結在一般情況下 （基本的程式碼範例） 的概觀，請參閱 <<c0> [ 繫結 Java 程式庫](~/android/platform/binding-java-library/index.md)。 

 
## <a name="walkthrough"></a>逐步解說

在下列的逐步解說中，我們將建立的繫結程式庫[畢加索](http://square.github.io/picasso/)，受歡迎的 Android。提供映像載入和快取功能的 JAR。 我們將使用下列步驟來繫結**畢加索 2.x.x.jar** Xamarin.Android 專案中建立新的.NET 組件，我們可以使用： 

1. 建立新的 Java 繫結程式庫專案。

2. 新增。JAR 檔案加入專案。

3. 設定適當的建置動作。JAR 檔案。

4. 選擇目標 framework。支援的 JAR。

5. 建立繫結程式庫。

一旦我們建立了繫結程式庫，我們會開發小型的 Android 應用程式，示範我們在繫結程式庫呼叫 Api 的能力。 在此範例中，我們想要存取的方法**畢加索 2.x.x.jar**:

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

我們產生的繫結程式庫之後**畢加索 2.x.x.jar**，我們可以呼叫這些方法，從C#。 例如: 

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```


### <a name="creating-the-bindings-library"></a>建立繫結程式庫

之前開始執行下列步驟，請下載[畢加索 2.x.x.jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar)。

首先，建立新的繫結程式庫專案。 在 Visual Studio for Mac 或 Visual Studio 中，建立新的方案並選取*Android 繫結程式庫*範本。 （在本逐步解說的螢幕擷取畫面使用 Visual Studio 中，但 Visual Studio for Mac 是非常類似）。將方案命名為**JarBinding**: 

[![建立 JarBinding 程式庫專案](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

此範本包括**Jar**資料夾，您新增程式。JAR(s) 繫結程式庫專案。 以滑鼠右鍵按一下**Jar**資料夾，然後選取**新增 > 現有項目**: 

[![加入現有項目](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

瀏覽至**畢加索 2.x.x.jar**稍早下載的檔案，然後按一下**新增**: 

[![選取 jar 檔案，然後按一下 [新增]](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

確認**畢加索 2.x.x.jar**檔案已成功加入至專案： 

[![Jar 新增至專案](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

當您建立 Java 繫結程式庫專案時，您必須指定是否。JAR 是內嵌於繫結程式庫或個別封裝。 若要這樣做，您指定下列其中一種*建置動作*: 

-   **EmbeddedJar** &ndash; 。JAR 會內嵌在繫結程式庫中。

-   **InputJar** &ndash; 。JAR 會保持分開繫結程式庫。

一般而言，您可以使用**EmbeddedJar**建置動作，如此。JAR 會自動封裝到繫結程式庫中。 這是最簡單的選項&ndash;Java 位元組程式碼中的。JAR 會轉換成 Dex 位元組程式碼，並將您的 APK 內嵌 （以及管理可呼叫包裝函式）。 如果您想要保留。JAR 分開繫結程式庫，您可以使用**InputJar**選項; 不過，您必須確定。在執行您的應用程式的裝置上使用 JAR 檔案。 

將建置動作設定為**EmbeddedJar**: 

[![選取 EmbeddedJar 建置動作](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

接下來，開啟 專案屬性來設定*目標 Framework*。 如果。JAR 使用任何 Android Api，將目標架構設為 API 層級。預期的 JAR。 一般而言，開發人員。JAR 檔案會指出哪一個 API 層級 （或層級），。JAR 位於與相容。 (如需有關的目標架構設定和一般的 Android API 層級的詳細資訊，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)。)

設定目標 API 層級您繫結程式庫 （在此範例中，我們會使用 API level 19）： 

[![目標 API 層級設定為 API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)


最後，建立繫結程式庫。 雖然某些警告訊息可能會顯示，繫結程式庫專案應建置成功，而且產生的輸出。在下列位置的 DLL: **JarBinding/bin/Debug/JarBinding.dll**
    


### <a name="using-the-bindings-library"></a>使用繫結程式庫

若要使用這個應用程式。在您的 Xamarin.Android 應用程式中的 DLL 執行下列作業：

1.  新增繫結程式庫的參考。

2.  對進行呼叫。透過受管理的可呼叫包裝函式的 JAR。 

在下列步驟中，我們將建立使用繫結程式庫來下載及顯示中的映像的最小應用程式`ImageView`; 「 繁重的工作 」 由位於程式碼。JAR 檔案。 

首先，建立新的 Xamarin.Android 應用程式使用繫結程式庫。 以滑鼠右鍵按一下方案，然後選取**加入新的專案**; 將新專案命名**BindingTest**。 我們要作為繫結程式庫相同的方案中建立此應用程式，為了簡化本逐步解說中;不過，使用繫結程式庫的應用程式反而可以位於不同的解決方案中： 

[![加入新的 BindingTest 專案](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

以滑鼠右鍵按一下**參考**節點**BindingTest**專案，然後選取**加入參考...**:

[![權限加入參考](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

請檢查**JarBinding**稍早建立的專案，然後按一下**確定**:

[![選取 JarBinding 專案](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

開啟**參考**節點**BindingTest**專案，然後確認**JarBinding**參考存在於： 

[![JarBinding 之下的參考](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

修改**BindingTest**版面配置 (**Main.axml**)，使其具有單一`ImageView`:

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

新增下列`using`陳述式來**MainActivity.cs** &ndash;這讓您能夠輕鬆存取以 Java 為基礎的方法`Picasso`位於繫結程式庫中的類別：

```csharp
using Com.Squareup.Picasso;
```

修改`OnCreate`方法，因此它會使用`Picasso`類別，可從 URL 載入影像，並顯示在`ImageView`: 

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

編譯並執行**BindingTest**專案。 應用程式將會啟動，並在短暫的延遲 （取決於網路狀況） 之後, 應該下載並顯示類似下列螢幕擷取畫面影像：

[![螢幕擷取畫面的 BindingTest 執行](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

恭喜您！ 您已成功繫結 Java 程式庫。JAR，並將它用在您的 Xamarin.Android 應用程式。
 
 
## <a name="summary"></a>總結

在本逐步解說中，我們建立的第三方繫結程式庫。JAR 檔案，將繫結程式庫加入最少的測試應用程式中，然後執行應用程式以確認我們C#程式碼可以呼叫中的 Java 程式碼。JAR 檔案。 



## <a name="related-links"></a>相關連結

- [建置 Java 繫結程式庫 （影片）](https://university.xamarin.com/classes#10090)
- [繫結 Java 程式庫](~/android/platform/binding-java-library/index.md)
