---
title: 繫結 .JAR
description: 本演練提供了從 Android 創建 Xamarin.Android Java 綁定庫的分步說明。JAR 檔。
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: 59969abae739db1d9035ec31738c39a3912f47ae
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027766"
---
# <a name="binding-a-jar"></a>繫結 .JAR

_本演練提供了從 Android 創建 Xamarin.Android Java 綁定庫的分步說明。JAR 檔。_

## <a name="overview"></a>概觀

Android 社區提供了許多 JAVA 庫,您可能想要在應用中使用這些庫。 這些 Java 庫通常打包在 中。JAR(Java 存檔)格式,但您可以打包 。JAR 它在*JAVA 綁定庫中*,以便其功能可供 Xamarin.Android 應用使用。 Java 綁定庫的目的是在 中創建 API。通過自動生成的代碼包裝器向C# 程式碼提供JAR檔。

Xamarin 工具可以從一個或多個輸入生成綁定庫。JAR 檔。 繫結庫 (.DLL 程式集) 包含以下內容: 

- 原始內容 。JAR 檔。

- 託管可調用包裝器 (MCW),它們是 C# 類型,在 中包裝相應的 JAVA 類型。JAR 檔。

產生的 MCW 程式碼使用 JNI(Java 本機介面)將 API 呼叫轉至基礎 。JAR 檔。 可以為任何創建綁定庫。JAR 檔案最初被定位為與 Android 一起使用(請注意,Xamarin 工具當前不支援非 Android Java 庫的綁定)。 您還可以選擇構建綁定庫,而不包括 的內容。JAR 檔,以便 DLL 對的依賴項。運行時的 JAR。

在本指南中,我們將逐步介紹為單個 創建綁定庫的基礎知識。JAR 檔。 我們將用一個示例來說明一切進展順利&ndash;,即不需要自定義或調試綁定。 
[使用元數據創建綁定](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)提供了一個更高級方案的示例,其中綁定過程不是完全自動的,需要一些手動干預。 有關 Java 函式庫綁定的概述(使用基本代碼範例),請參閱綁定[Java 函式庫](~/android/platform/binding-java-library/index.md)。 

## <a name="walkthrough"></a>逐步介紹

在下面的演練中,我們將為[畢卡索](https://square.github.io/picasso/)創建一個綁定庫,一個流行的Android。提供圖像載入和緩存功能的 JAR。 我們將使用以下步驟綁定**畢卡索-2.x.x.jar**以建立一個新的 .NET 程式集,我們可以在 Xamarin.Android 專案中使用: 

1. 創建新的 JAVA 綁定庫專案。

2. 新增 。JAR 檔案到專案。

3. 為設置相應的生成操作。JAR 檔。

4. 選擇的目標框架。JAR 支援。

5. 生成綁定庫。

創建綁定庫後,我們將開發一個小型 Android 應用程式,演示我們在綁定庫中調用 API 的能力。 此範例中,我們希望造**訪卡索-2.x.x.jar 的方法**:

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

為**畢卡索生成**綁定庫后,我們可以從 C# 調用這些方法。 例如：

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```

### <a name="creating-the-bindings-library"></a>建立繫結庫

在開始以下步驟之前,請下載[畢卡索-2.x.x.jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar)。

首先,創建新的綁定庫專案。 在 Mac 或 Visual Studio 的可視化工作室中,創建新解決方案並選擇*Android 綁定庫*範本。 (本演練中的螢幕截圖使用 Visual Studio,但 Mac 的可視化工作室非常相似。命名解決方案**JarBinding**: 

[![建立 JarBinding 函式庫專案](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

這個樣本包含**Jars**資料夾,您可以在其中新增 。JAR(s) 到綁定庫專案。 右鍵按下**Jars**資料夾並選擇 **「新增>現有專案**: 

[![新增現有項目](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

導覽到較早下載的**畢卡索-2.x.x.jar**檔案,選擇它,然後按下「**新增**: 

[![選擇 jar 檔,然後單擊"添加"](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

認證**畢卡索檔案**是否已成功新增到專案中: 

[![Jar 新增到項目中](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

創建 JAVA 綁定庫專案時,必須指定 是否。JAR 將嵌入到綁定庫中或單獨打包。 此,請指定以下*產生操作*之一: 

- **嵌入Jar。** &ndash;JAR 將嵌入到綁定庫中。

- **輸入Jar。** &ndash;JAR將與綁定庫分開。

通常,使用**EmbeddedJar**產生操作,以便 .JAR 會自動打包到綁定庫中。 這是 中最&ndash;簡單的 JAVA 位元組碼選項。JAR被轉換為 Dex 位元組碼,並嵌入(以及託管可呼叫包裝器)到您的 APK 中。 如果要保留 。JAR 獨立於綁定庫,您可以使用 **「輸入Jar」** 選項;使用「輸入Jar」選項。但是,必須確保.JAR 檔在運行應用的設備上可用。 

將產生操作設定為**嵌入式Jar**: 

[![選擇嵌入式Jar產生作業](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

讓您開啟項目屬性以設定*目標框架*。 如果 .JAR 使用任何 Android API,將目標框架設置為的 API 級別。JAR 期望。 通常, 的開發人員。JAR 檔案將指示的 API 等級(或等級) 。JAR 相容。 (有關目標框架設定和 Android API 等級的詳細資訊,請參閱瞭解[Android API 級別](~/android/app-fundamentals/android-api-levels.md)。

設定繫結庫的目標 API 等級(在此範例中,我們使用 API 級別 19): 

[![目標 API 等級設定為 API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)

最後,構建綁定庫。 儘管可能會顯示一些警告訊息,但綁定庫專案應成功產生並產生輸出 。DLL 在以下位置 **:Jar綁定/bin/除錯/JarBinding.dll**

### <a name="using-the-bindings-library"></a>使用繫結庫

要使用此 。DLL 在 Xamarin.Android 應用程式中,執行以下操作:

1. 添加對綁定庫的引用。

2. 將呼叫到 。通過託管可調用包裝器 JAR。 

在以下步驟中,我們將創建一個最小應用程式,使用綁定庫在`ImageView`中下載和顯示圖像。"重提"由駐留在 中的代碼完成。JAR 檔。 

首先,創建一個新的 Xamarin.Android 應用程式,該應用程式使用綁定庫。 右鍵按下「解決方案」並選擇「**添加新專案**」;具新項目**繫結測試**。 我們正在與綁定庫在同一解決方案中創建此應用程式,以便簡化本演練;但是,使用綁定庫的應用可以駐留在不同的解決方案中: 

[![新增繫結測試項目](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

右鍵按一下**綁定測試**專案的**參考**節點,然後選擇 **"添加引用..."**

[![右新增參考](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

檢查之前建立的**JarBinding**專案,然後按下 **「確定**」

[![選擇 Jar 繫結項目](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

開啟**繫結測試**專案的**參考**節點,並驗證**JarBinding**引用是否存在: 

[![JarBinding 顯示在「引用」下](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

變更**繫結測試**佈局 (**Main.axml**`ImageView`), 以便有一個 :

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

將以下`using`語句添加到**MainActivity.cs**&ndash;這樣可以輕鬆地存取綁定庫中`Picasso`基於 Java 的類別的方法:

```csharp
using Com.Squareup.Picasso;
```

變更`OnCreate`方法,以便它使用`Picasso`類別從網址載入影像`ImageView`, 並顯示它: 

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

編譯並運行**綁定測試**專案。 該應用程式將啟動,並在短暫的延遲後(取決於網路條件),它應該下載並顯示類似於以下螢幕截圖的圖像:

[![繫結測試執行的螢幕擷圖](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

恭喜！ 您已成功綁定 Java 庫。JAR,並在您的Xamarin.安卓應用程式中使用它。

## <a name="summary"></a>總結

在本演練中,我們為第三方創建了綁定庫。JAR 檔,將綁定庫添加到最小測試應用,然後運行該應用以驗證我們的 C# 代碼是否可以調用駐留在 中的 Java 代碼。JAR 檔。 

## <a name="related-links"></a>相關連結

- [建構 Java 結合函式庫(視訊)](https://university.xamarin.com/classes#10090)
- [繫結 Java 程式庫](~/android/platform/binding-java-library/index.md)
