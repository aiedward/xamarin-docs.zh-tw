---
title: Hello, Android：深度剖析
description: 在這份含有兩部分的指南中，您將會建置您的第一個 Xamarin.Android 應用程式，以及了解使用 Xamarin 進行 Android 應用程式開發的基本知識。 在過程中，將會為您介紹建置和部署 Xamarin.Android 應用程式所需的工具、概念和步驟。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: EF0E110B-20EA-43F6-9476-1A0F41AFD298
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/05/2018
ms.openlocfilehash: cdd0a792a76fa411fdc010c3d662a3d1acc2f373
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108806"
---
# <a name="hello-android-deep-dive"></a>Hello, Android：深度剖析

_在這份含有兩部分的指南中，您將會建置您的第一個 Xamarin.Android 應用程式，以及了解使用 Xamarin 進行 Android 應用程式開發的基本知識。在此過程中，將會為您介紹建置和部署 Xamarin.Android 應用程式所需的工具、概念和步驟。_

在 [Hello, Android 快速入門](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-quickstart.md)中，您已建置並執行您的第一個 Xamarin.Android 應用程式。 現在是時候更深入了解 Android 應用程式的運作方式，讓您能夠建置更複雜的程式。 本指南會檢閱您在 Hello, Android 逐步解說中所採取的步驟，讓您可以了解所執行的作業，並開始對 Android 應用程式開發有基本了解。

本指南會談到下列主題：

::: zone pivot="windows"

- **Visual Studio 簡介** &ndash; 介紹 Visual Studio，並建立新的 Xamarin.Android 應用程式。

- **Xamarin.Android 應用程式的結構** - 導覽 Xamarin.Android 應用程式的基本組件。

- **應用程式基礎與架構基本概念** &ndash; 活動、Android 資訊清單及 Android 開發之一般類別的簡介。

- **使用者介面 (UI)** &ndash; 使用 Android Designer 來建立使用者介面。

- **活動與活動開發週期** &ndash; 活動開發週期及以程式碼連接使用者介面的簡介。

- **測試、部署及完成修飾** &ndash; 透過測試、部署、產生作品等建議來完成應用程式。

::: zone-end
::: zone pivot="macos"

- **Visual Studio for Mac 簡介** &ndash; 介紹 Visual Studio for Mac，並建立新的 Xamarin.Android 應用程式。

- **Xamarin.Android 應用程式的結構** &ndash; 導覽 Xamarin.Android 應用程式的基本組件。

- **應用程式基礎與架構基本概念** &ndash; 活動、Android 資訊清單及 Android 開發的一般類別簡介。

- **使用者介面 (UI)** &ndash; 使用 Android Designer 來建立使用者介面。

- **活動與活動開發週期** &ndash; 活動開發週期及以程式碼連接使用者介面的簡介。

- **測試、部署及完成修飾** &ndash; 透過測試、部署、產生作品等建議來完成應用程式。

::: zone-end

本指南將協助您發展建置單一畫面之 Android 應用程式所需的技能與知識。 完成之後，您應該能夠了解 Xamarin.Android 應用程式的不同組件，以及如何將它們結合在一起。

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Visual Studio 簡介

Visual Studio 是 Microsoft 所提供之功能強大的 IDE。 其中包含完全整合的視覺化設計工具、含有重構工具的文字編輯器、組件瀏覽器、原始程式碼整合等。 在本指南中，您將了解如何搭配 Xamarin 外掛程式使用一些基本的 Visual Studio 功能。

Visual Studio 會將程式碼組織成「方案」和「專案」。 方案是可以容納一或多個專案的容器。 專案可以是應用程式 (例如 iOS 或 Android 應用程式)、支援程式庫、測試應用程式等。 在 **Phoneword** 應用程式中，您已使用 **Android 應用程式**範本新增 Android 專案至在 [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) 指南中建立的 **Phoneword** 方案。

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Visual Studio for Mac 簡介

Visual Studio for Mac 是免費的開放原始碼 IDE，類似 Visual Studio。 其中包含完全整合的視覺化設計工具、具有重構工具的文字編輯器、組件瀏覽器、原始程式碼整合等。 在本指南中，您將了解如何使用一些基本的 Visual Studio for Mac 功能。 如果您還不熟悉 Visual Studio for Mac，您可能想要參閱更深入的 [Visual Studio for Mac 簡介](https://docs.microsoft.com/visualstudio/mac/)。

Visual Studio for Mac 遵循 Visual Studio 的做法，將程式碼組織成「方案」和「專案」。 方案是可以容納一或多個專案的容器。 專案可以是應用程式 (例如 iOS 或 Android 應用程式)、支援程式庫、測試應用程式等。 在 **Phoneword** 應用程式中，您已使用 **Android 應用程式**範本新增 Android 專案至在 [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) 指南中建立的 **Phoneword** 方案。

::: zone-end

<a name="anatomy" />

## <a name="anatomy-of-a-xamarinandroid-application"></a>Xamarin.Android 應用程式的結構

::: zone pivot="windows"

下列螢幕擷取畫面會列出方案的內容。 這是方案總管，其中包含目錄結構以及與方案建立關聯的所有檔案：

[![方案總管](hello-android-deepdive-images/vs/02-solution-structure-sml.png)](hello-android-deepdive-images/vs/02-solution-structure.png#lightbox)

::: zone-end
::: zone pivot="macos"

下列螢幕擷取畫面會列出方案的內容。 這是 Solution Pad，其中包含目錄結構以及與方案建立關聯的所有檔案：

[![Solution Pad](hello-android-deepdive-images/xs/02-solution-structure-sml.png)](hello-android-deepdive-images/xs/02-solution-structure.png#lightbox)

::: zone-end

已建立名為 **Phoneword** 的方案，並將名為 **Phoneword** 的 Android 專案放在其中。

檢視專案內的所有項目，查看每個資料夾及其用途：

- **Properties** &ndash; 包含 [AndroidManifest.xml](~/android/platform/android-manifest.md) 檔案，其中描述 Xamarin.Android 應用程式的所有需求，包括名稱、版本號碼和權限。 **Properties** 資料夾也包含 [AssemblyInfo.cs](xref:Microsoft.VisualBasic.ApplicationServices.AssemblyInfo)，也就是 .NET 組件中繼資料檔案。 建議您在此檔案填入您應用程式的一些基本資訊。

- **References** &ndash; 包含建置及執行應用程式所需的組件。 如果您展開 References 目錄，您將會看到對 .NET 組件的參考 (例如 [System](xref:System)、System.Core 和 [System.Xml](xref:System.Xml))，以及對 Xamarin 之 Mono.Android 組件的參考。

- **Assets** &ndash; 包含應用程式需要執行的檔案，包括字型、本機資料檔案和文字檔。 此處包含的檔案可透過產生的 `Assets` 類別來存取。 如需 Android 資產的詳細資訊，請參閱 Xamarin 的[使用 Android 資產](~/android/app-fundamentals/resources-in-android/android-assets.md)指南。

- **Resources** &ndash; 包含應用程式資源，例如字串、影像和配置。 您可以透過產生的 `Resource` 類別來存取程式碼中的這些資源。 [Android 資源](~/android/app-fundamentals/resources-in-android/index.md)指南提供有關 **Resources** 目錄的更多詳細資料。 此應用程式範本也包含 **AboutResources.txt** 檔案中資源的精簡指南。

### <a name="resources"></a>資源

**Resources** 目錄包含四個資料夾 **drawable**、**layout**、**mipmap** 和 **values**，以及名為 **Resource.designer.cs** 的檔案。

下表摘要說明這些項目：

- **drawable** &ndash; drawable 目錄包含 [Drawable Resources](http://developer.android.com/guide/topics/resources/drawable-resource.html) (可繪製資源)，例如影像和點陣圖。

- **mipmap** &ndash; mipmap 目錄包含不同啟動程式圖示密度的可繪製檔案。 在預設範本中，drawable 目錄包含應用程式圖示檔 **Icon.png**。


::: zone pivot="windows"

- **layout** &ndash; layout 目錄包含 _Android Designer 檔案_ (.axml)，其中定義每個畫面或活動的使用者介面。 此範本會建立一個預設配置，稱為 **activity_main.axml**。

::: zone-end
::: zone pivot="macos"

- **layout** &ndash; layout 目錄包含 _Android Designer 檔案_ (.axml)，其中定義每個畫面或活動的使用者介面。 此範本會建立一個預設配置，稱為 **Main.axml**。

::: zone-end

- **values** &ndash; 此目錄包含 XML 檔案，其中存放了字串、整數和色彩等簡單實值。 此範本會建立一個存放字串值的檔案，稱為 **Strings.xml**。

- **Resource.designer.cs** &ndash; 也稱為 `Resource` 類別，此檔案是保留指派給每個資源之唯一識別碼的部分類別。 它是由 Xamarin.Android 工具自動建立，並會在必要時重新產生。 請勿手動編輯此檔案，因為 Xamarin.Android 會覆寫任何手動變更。

## <a name="app-fundamentals-and-architecture-basics"></a>應用程式基礎與架構基本概念

Android 應用程式沒有單一進入點；換句話說，作業系統不會在應用程式中呼叫任何一行程式碼來啟動應用程式。 相反地，當 Android 具現化其中一個類別時，就會啟動應用程式，在這段期間，Android 會將整個應用程式的處理序載入記憶體。

這項 Android 特有的功能在設計複雜的應用程式或與 Android 作業系統互動時會非常實用。 不過，在處理 **Phoneword** 應用程式之類的基本案例時，這些選項也會使 Android 變得複雜。 因此，Android 架構的瀏覽分割成兩部分。 本指南所剖析的應用程式使用了 Android 應用程式的最常見進入點：第一個畫面。 在 [Hello, Android 多重畫面](~/android/get-started/hello-android-multiscreen/index.md)中，將探索 Android 架構的整體複雜度，並討論啟動應用程式的不同方式。

### <a name="phoneword-scenario---starting-with-an-activity"></a>Phoneword 案例 - 開始使用活動

當您第一次在模擬器或裝置中開啟 **Phoneword** 應用程式時，作業系統會建立第一個「活動」。 活動是對應至單一應用程式畫面的特殊 Android 類別，負責繪製及提供使用者介面。 當 Android 應用程式的第一個活動時，它會載入整個應用程式：

[![活動載入](hello-android-deepdive-images/01-activity-load-sml.png)](hello-android-deepdive-images/01-activity-load.png#lightbox)

由於不是直線通過 Android 應用程式 (您可以從數個點啟動應用程式)，因此 Android 提供獨特的方法來追蹤組成應用程式的類別和檔案。 在 **Phoneword** 範例中，組成應用程式的所有組件都會向特殊的 XML 檔案 (稱為 **Android 資訊清單**) 註冊。 **Android 資訊清單**的角色在於追蹤應用程式的內容、屬性和權限，並公開給 Android 作業系統。 您可以將 **Phoneword** 應用程式想成是單一活動 (畫面)，以及透過 Android 資訊清單檔案繫結在一起的資源和協助程式檔案集合，如下圖所示：

[![資源協助程式](hello-android-deepdive-images/02-resources-helpers-sml.png)](hello-android-deepdive-images/02-resources-helpers.png#lightbox)

接下來的章節將探索 **Phoneword** 應用程式各組件之間的關聯性，這應該可讓您進一步了解上圖。 此探索從使用者介面開始，並討論 Android Designer 和配置檔案。

## <a name="user-interface"></a>使用者介面

::: zone pivot="windows"

**activity_main.axml** 是應用程式中第一個畫面的使用者介面配置檔案。 .axml 表示這是 Android Designer 檔案 (AXML 代表 *Android XML*)。 名稱 *Main* 從 Android 的觀點來看是任意名稱 &ndash; 配置檔案可能已有其他名稱。 當您在 IDE 中開啟 **activity_main.axml** 時，會顯示 Android 配置檔案的視覺化編輯器，稱為 *Android Designer*：

[![Android Designer](hello-android-deepdive-images/vs/03-android-designer-sml.png "Android Designer")](hello-android-deepdive-images/vs/03-android-designer.png#lightbox)

在 **Phoneword** 應用程式中，**TranslateButton** 的識別碼會設定為 `@+id/TranslateButton`：

[![TranslateButton 識別碼設定](hello-android-deepdive-images/vs/04-translatebutton-sml.png "TranslateButton 識別碼設定")](hello-android-deepdive-images/vs/04-translatebutton.png#lightbox)

::: zone-end
::: zone pivot="macos"

**Main.axml** 是應用程式中第一個畫面的使用者介面配置檔案。 .axml 表示這是 Android Designer 檔案 (AXML 代表 *Android XML*)。 名稱 *Main* 從 Android 的觀點來看是任意名稱 &ndash; 配置檔案可能已有其他名稱。 當您在 IDE 中開啟 **Main.axml** 時，會顯示 Android 配置檔案的視覺化編輯器，稱為 *Android Designer*：

[![Android Designer](hello-android-deepdive-images/xs/03-android-designer-sml.png)](hello-android-deepdive-images/xs/03-android-designer.png#lightbox)

在 **Phoneword** 應用程式中，**TranslateButton** 的識別碼會設定為 `@+id/TranslateButton`：

[![TranslateButton 識別碼設定](hello-android-deepdive-images/xs/04-translatebutton-sml.png)](hello-android-deepdive-images/xs/04-translatebutton.png#lightbox)

::: zone-end

當您設定 **TranslateButton** 的 `id` 屬性時，Android Designer 會將 **TranslateButton** 控制項對應至 `Resource` 類別，並為其指派 `TranslateButton` 的「資源識別碼」。 此視覺控制項與類別的對應可讓您尋找並使用 **TranslateButton** 及應用程式程式碼中的其他控制項。 這在您分析提供控制項的程式碼時將會更詳細地說明。 您目前只需要知道控制項的程式碼表示已透過 `id` 屬性連結至設計工具中控制項的視覺表示。

### <a name="source-view"></a>原始碼檢視

設計介面上定義的所有內容都會轉譯為 XML 以供 Xamarin.Android 使用。 Android Designer 提供原始碼檢視，其中包含從視覺化設計工具所產生的 XML。 您可以切換至設計工具檢視左下方的 [原始碼] 面板來檢視此 XML，如以下螢幕擷取畫面所示：

::: zone pivot="windows"

[![設計工具的原始碼檢視](hello-android-deepdive-images/vs/05-source-view-sml.png "設計工具的原始碼檢視")](hello-android-deepdive-images/vs/05-source-view.png#lightbox)

::: zone-end
::: zone pivot="macos"

[![設計工具的原始碼檢視](hello-android-deepdive-images/xs/05-source-view-sml.png)](hello-android-deepdive-images/xs/05-source-view.png#lightbox)

::: zone-end

此 XML 原始程式碼應該包含**文字 (大型)**、**純文字**和兩個**按鈕**項目。 如需 Android Designer 的更深入導覽，請參閱 Xamarin Android [Designer 概觀](~/android/user-interface/android-designer/index.md)指南。

現在您已了解使用者介面之視覺部分背後的工具和概念。 接下來，您可以跳到提供使用者介面的程式碼，並探索活動與活動開發週期。

## <a name="activities-and-the-activity-lifecycle"></a>活動與活動開發週期

`Activity` 類別包含提供使用者介面的程式碼。
活動負責回應使用者互動，以及打造動態的使用者體驗。
本節將介紹 `Activity` 類別、討論活動開發週期，並剖析 **Phoneword** 應用程式中提供使用者介面的程式碼。

### <a name="activity-class"></a>活動類別

**Phoneword** 應用程式只有一個畫面 (活動)。 提供畫面的類別稱為 `MainActivity`，並位於 **MainActivity.cs** 檔案中。 名稱 `MainActivity` 在 Android 中沒有特殊意義 &ndash; 雖然慣例是將應用程式中的第一個活動命名為 `MainActivity`，但 Android 並不在乎是否將它命名為其他名稱。

當您開啟 **MainActivity.cs** 時，您會看到 `MainActivity` 類別為 `Activity` 類別的「子類別」，而且活動會以 [Activity](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) 屬性裝飾：

```csharp
[Activity (Label = "Phone Word", MainLauncher = true)]
public class MainActivity : Activity
{
  ...
}
```

`Activity` 屬性會向 Android 資訊清單註冊活動；這可讓 Android 知道此類別是受此資訊清單管理之 **Phoneword** 應用程式的一部分。 `Label` 屬性會設定將顯示在畫面頂端的文字。

`MainLauncher` 屬性會告知 Android 在應用程式啟動時顯示此活動。 當您將更多活動 (畫面) 新增至應用程式時，此屬性會變得很重要，如 [Hello, Android 多重畫面](~/android/get-started/hello-android-multiscreen/index.md)指南所述。

現在您已了解 `MainActivity` 的基本概念，接下來可以閱讀「活動開發週期」簡介來深入探索活動程式碼。

### <a name="activity-lifecycle"></a>活動開發週期

在 Android 中，活動會根據其與使用者的互動，經過開發週期的不同階段。 您可以建立、啟動、暫停、繼續和終結活動，以及執行其他動作。 `Activity` 類別包含系統在畫面開發週期的特定點上所呼叫的方法。 下圖說明活動的典型開發週期，以及一些對應的開發週期方法：

[![活動開發週期](hello-android-deepdive-images/04-lifecycle-sml.png)](hello-android-deepdive-images/04-lifecycle.png#lightbox)

藉由覆寫 `Activity` 開發週期方法，您就可以控制活動的載入方式、回應使用者的方式，甚至是它從裝置螢幕消失之後所發生的情況。 例如，您可以覆寫上圖中的開發週期方法，來執行一些重要的工作：

- **OnCreate** &ndash; 建立檢視、初始化變數，並執行其他必須完成才能讓使用者看到活動的準備工作。 此方法只會在活動載入記憶體時呼叫一次。 

- **OnResume** &ndash; 執行必須在每次活動回到裝置螢幕時進行的任何工作。

- **OnPause** &ndash; 執行必須在每次活動離開裝置螢幕時進行的任何工作。

當您將自訂程式碼新增至 `Activity` 中的開發週期方法時，您會「覆寫」開發週期方法的「基底實作」。 您可以進入現有的開發週期方法 (其中已有一些附加的程式碼)，然後以自己的程式碼擴充該方法。 您可以從方法內部呼叫基底實作，以確保原始程式碼在您的新程式碼之前執行。 下一節將說明此範例。

活動開發週期是 Android 中很重要且複雜的一部分。 如果您想要在完成_使用者入門_系列之後深入了解活動，請閱讀[活動開發週期](~/android/app-fundamentals/activity-lifecycle/index.md)指南。 在本指南中，下一個重點會放在活動開發週期的第一個階段 `OnCreate`。

### <a name="oncreate"></a>OnCreate

Android 會在建立活動時 (呈現畫面給使用者之前)，呼叫 `Activity` 的 `OnCreate` 方法。 您可以覆寫 `OnCreate` 開發週期方法來建立檢視，並準備好您的活動以回應使用者：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);
    // Additional setup code will go here
}
```

::: zone pivot="windows"

在 **Phoneword** 應用程式中，`OnCreate` 的第一項工作是載入在 Android Designer 中建立的使用者介面。 若要載入 UI，請呼叫 `SetContentView` 並傳遞配置檔案的「資源配置名稱」：**activity_main.axml**。 此配置位於 `Resource.Layout.activity_main` 中：

```csharp
SetContentView (Resource.Layout.activity_main);
```

當 `MainActivity` 啟動時，它會根據 **activity_main.axml** 檔案的內容來建立檢視。

::: zone-end
::: zone pivot="macos"

在 **Phoneword** 應用程式中，`OnCreate` 的第一項工作是載入在 Android Designer 中建立的使用者介面。 若要載入 UI，請呼叫 `SetContentView` 並傳遞配置檔案的「資源配置名稱」：**Main.axml**。 此配置位於 `Resource.Layout.Main` 中：

```csharp
SetContentView (Resource.Layout.Main);
```

當 `MainActivity` 啟動時，它會根據 **Main.axml** 檔案的內容來建立檢視。 請注意，配置檔案名稱會對應到活動名稱 &ndash; *Main*.axml 是「主要」活動的配置。 從 Android 的觀點來看這並不必要，但當您開始將更多畫面新增至應用程式時，您會發現此命名慣例可更輕鬆地配對程式碼檔案和配置檔案。

::: zone-end

配置檔案備妥之後，您可以開始查閱控制項。
若要查閱控制項，請呼叫 `FindViewById` 並傳入控制項的資源識別碼：

```csharp
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneWord);
```

現在您的配置檔案中已有控制項的參考，您可以開始撰寫其程式來回應使用者互動。

### <a name="responding-to-user-interaction"></a>回應使用者互動

在 Android 中，`Click` 事件會接聽使用者的觸控。 在此應用程式中，使用 Lambda 來處理 `Click` 事件，但也可改用委派或具名的事件處理常式。 最後的 **TranslateButton** 程式碼如下： 

```csharp
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

## <a name="testing-deployment-and-finishing-touches"></a>測試、部署及最後的修飾

Visual Studio for Mac 和 Visual Studio 都會提供許多選項來測試和部署應用程式。 本節涵蓋偵錯選項、示範在裝置上測試應用程式，並介紹用來為不同螢幕密度建立自訂應用程式圖示的工具。

### <a name="debugging-tools"></a>偵錯工具

應用程式程式碼中的問題可能很難診斷。 若要協助診斷複雜的程式碼問題，您可以[設定中斷點](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)、[逐步執行程式碼](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code)或[將資訊輸出至記錄視窗](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window)。

### <a name="deploy-to-a-device"></a>部署到裝置

模擬器是部署和測試應用程式的一個不錯起點，但使用者並不會在模擬器中使用最後的應用程式。 最好及早且經常地在實際裝置上測試應用程式。

您必須先設定 Android 裝置以進行開發，才能使用它來測試應用程式。 [設定裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)指南提供準備好裝置以進行開發的完整指示。

::: zone pivot="windows"

設定裝置之後，您可以部署到該裝置，方法是將它插入、從 [選取裝置] 對話方塊中選取它，然後啟動應用程式：

![選取偵錯裝置](hello-android-deepdive-images/vs/06-select-device.png "選取偵錯裝置")

::: zone-end
::: zone pivot="macos"

設定裝置之後，您可以部署到該裝置，方法是將它插入、按下 [Start (Play)] (啟動 (播放))、從 [選取裝置] 對話方塊中選取它，然後按下 [確定]：

[![選取偵錯裝置](hello-android-deepdive-images/xs/06-select-device-sml.png)](hello-android-deepdive-images/xs/06-select-device.png#lightbox)

::: zone-end

這會啟動裝置上的應用程式：

[![輸入 Phoneword](hello-android-deepdive-images/05-enter-phoneword-sml.png)](hello-android-deepdive-images/05-enter-phoneword.png#lightbox)

### <a name="set-icons-for-different-screen-densities"></a>設定不同螢幕密度的圖示

Android 裝置的螢幕大小和解析度不一，並非所有影像在所有螢幕上都看起來正常。 例如，以下是高密度 Nexus 5 上之低密度圖示的螢幕擷取畫面。 注意它與周圍的圖示相較下有多模糊：

[![模糊的圖示](hello-android-deepdive-images/06-blurry-icon-sml.png)](hello-android-deepdive-images/06-blurry-icon.png#lightbox)

有鑑於此，最好是將不同解析度的圖示新增至 **Resources** 資料夾。 Android 提供不同版本的 **mipmap** 資料夾來處理不同密度的啟動程式圖示：*mdpi* 適用於中密度、*hdpi* 適用於高密度，而 *xhdpi*、*xxhdpi*、*xxxhdpi* 則適用於非常高密度的螢幕。 不同大小的圖示會存放在適當的 **mipmap-** 資料夾中：

::: zone pivot="windows"

![mipmap 資料夾](hello-android-deepdive-images/vs/07-mipmap-folders.png "mipmap 資料夾")

::: zone-end
::: zone pivot="windows"

[![Mipmap 資料夾](hello-android-deepdive-images/xs/07-mipmap-folders-sml.png)](hello-android-deepdive-images/xs/07-mipmap-folders.png#lightbox)

::: zone-end

Android 會選擇具有適當密度的圖示：

[![適當密度的圖示](hello-android-deepdive-images/07-appropriate-density-sml.png)](hello-android-deepdive-images/07-appropriate-density.png#lightbox)

### <a name="generate-custom-icons"></a>產生自訂圖示

不是每個人都有設計工具可用來建立應用程式需要凸顯的自訂圖示和啟動影像。以下是數種用來產生自訂應用程式作品的替代方法：

::: zone pivot="windows"

- [Android Asset Studio](http://romannurik.github.io/AndroidAssetStudio/index.html) &ndash; 此 Web 架構的瀏覽器內產生器適用於所有類型的 Android 圖示，並連結至其他有用的社群工具。 在 Google Chrome 中的效果最佳。

- Visual Studio &ndash; 您可以使用此產品，直接在 IDE 中為您的應用程式建立簡單圖示集。

- [Glyphish](http://www.glyphish.com/) &ndash; 高品質的預先建置圖示集，可供免費下載及購買。

- [Fiverr](http://www.fiverr.com/) &ndash; 從各種不同的設計工具進行選擇來建立您適用的圖示集，從美金 $5 元開始。 可能時好時壞，但如果您需要動態設計的圖示，這會是不錯的資源。

::: zone-end
::: zone pivot="macos"

- [Android Asset Studio](http://romannurik.github.io/AndroidAssetStudio/index.html) &ndash; 此 Web 架構的瀏覽器內產生器適用於所有類型的 Android 圖示，並連結至其他有用的社群工具。 在 Google Chrome 中的效果最佳。

- [Sketch 3](https://itunes.apple.com/us/app/sketch/id852320343?mt=12) &ndash; Sketch 是一個用來設計使用者介面、圖示等內容的 Mac 應用程式。 這是用來設計 Xamarin 應用程式圖示和啟動影像集的應用程式。 App Store 上目前提供 Sketch 3，成本大約美金 $80 元。 您也可以試用免費的 [SketchTool](http://bohemiancoding.com/sketch/tool/)。

- [Pixelmator](http://www.pixelmator.com/) &ndash; 適用於 Mac 的多用途影像編輯應用程式，成本大約美金 $30 元。

- [Glyphish](http://www.glyphish.com/) &ndash; 高品質的預先建置圖示集，可供免費下載及購買。

- [Fiverr](http://www.fiverr.com/) &ndash; 從各種不同的設計工具進行選擇來建立您適用的圖示集，從美金 $5 元開始。 可能時好時壞，但如果您需要動態設計的圖示，這會是不錯的資源。

::: zone-end

如需圖示大小和需求的詳細資訊，請參閱 [Android 資源](~/android/app-fundamentals/resources-in-android/index.md)指南。

::: zone pivot="macos"

### <a name="adding-google-play-services-packages"></a>新增 Google Play Services 套件

_Google Play 服務_是一組附加元件程式庫，可讓 Android 開發人員利用來自 Google 的最新功能，例如 Google 地圖、Google 雲端通訊和應用程式內結帳。
先前，所有 Google Play 服務程式庫的繫結都是由 Xamarin 以單一套件形式提供 &ndash; 從 Visual Studio for Mac 開始，您可以使用新的 [專案] 對話方塊，來選取要加入應用程式的 Google Play 服務套件。

若要新增一或多個 Google Play 服務程式庫，請以滑鼠右鍵按一下您專案樹狀目錄中的 [套件] 節點，然後按一下 [Add Google Play Service...] (新增 Google Play 服務...)：

[![新增 Google Play 服務](hello-android-deepdive-images/xs/08-add-google-play-services-sml.png)](hello-android-deepdive-images/xs/08-add-google-play-services.png#lightbox)

當 [Add Google Play Services] (新增 Google Play 服務) 對話方塊出現時，選取您想要新增至專案的套件 (Nuget)：

[![選取套件](hello-android-deepdive-images/xs/09-add-dialog-sml.png)](hello-android-deepdive-images/xs/09-add-dialog.png#lightbox)

當您選取服務並按一下 [新增套件] 時，Visual Studio for Mac 會下載並安裝您選取的套件及其所需的任何相依 Google Play 服務套件。 在某些情況下，您可能會看到 [接受授權] 對話方塊，您必須按一下 [接受] 才能安裝套件：

[![接受授權](hello-android-deepdive-images/xs/10-license-acceptance-sml.png)](hello-android-deepdive-images/xs/10-license-acceptance.png#lightbox)

::: zone-end

## <a name="summary"></a>總結

恭喜您！ 您現在應該已充分了解 Xamarin.Android 應用程式的元件，以及建立應用程式所需的工具。

在_使用者入門_系列的下一個教學課程中，您將會展開應用程式來處理多重畫面，並探索更進階的 Android 架構和概念。
