---
title: Hello, Android 多重畫面：深度剖析
description: 在這份含有兩部分的指南中，會擴充在 Hello, Android 指南中所建立的 Phoneword 應用程式來處理第二個畫面。 過程中，會介紹基本 Android 應用程式建置組塊。 包含 Android 架構深入剖析，以協助您進一步了解 Android 應用程式結構和功能。
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E4150036-7760-4023-BD33-B7BDE7B7AF5B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: f1c19d43aa1f9010307df3fb954ac1029221ccd4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30767735"
---
# <a name="hello-android-multiscreen-deep-dive"></a>Hello, Android 多重畫面：深度剖析

_在這份含有兩部分的指南中，會擴充在《Hello, Android》指南中建立的 Phoneword 應用程式來處理第二個畫面。過程中，會介紹基本 Android 應用程式建置組塊。包含 Android 架構深入剖析，以協助您進一步了解 Android 應用程式結構和功能。_

## <a name="hello-android-multiscreen-deep-dive"></a>Hello, Android 多重畫面深度剖析

在 [Hello, Android 多重畫面快速入門](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-quickstart.md)中，您已建置並執行您的第一個多重畫面 Xamarin.Android 應用程式。
現在是時候更深入了解 Android 導覽和架構，讓您能夠建置更複雜的應用程式。

在本指南中，您將隨著介紹 Android「應用程式建置組塊」，而探索更進階的 Android 架構。 會解釋 Android 導覽與「意圖」，以及探索 Android 硬體導覽選項。 在您對於與作業系統和其他應用程式的應用程式關聯性發展更全面的檢視時，會仔細分析 Phoneword 應用程式的新增項目。


## <a name="android-architecture-basics"></a>Android 架構的基本概念

在 [Hello, Android 深度剖析](~/android/get-started/hello-android/hello-android-deepdive.md)中，您已了解 Android 應用程式是唯一的程式，因為它們缺少單一進入點。 相反地，作業系統 (或另一個應用程式) 會啟動任何一個應用程式的已註冊活動，接著啟動應用程式的程序。 這項對於 Android 架構的深入探討會藉由介紹 Android 應用程式建置組塊和其功能，加強您對於 Android 應用程式建構方式的了解。


### <a name="android-application-blocks"></a>Android 應用程式區塊

Android 應用程式包含特殊 Android 類別的集合，稱為「應用程式區塊」，配合任何數目的應用程式資源 - 影像、佈景主題、協助程式類別等 &ndash; 這些由稱為「Android 資訊清單」的 XML 檔案進行協調。

應用程式區塊形成了 Android 應用程式的骨幹，因為它們允許您執行通常無法使用一般類別完成的動作。 最重要的兩項區塊是「活動」和「服務」：

-   **活動** &ndash; 活動對應至具有使用者介面的螢幕，而且在概念上類似於 Web 應用程式中的網頁。 比方說，在新聞摘要的應用程式中，登入螢幕會是第一個活動，可捲動的新聞項目清單會是另一個活動，而每個項目的詳細資料頁面會是第三個。 您可以在[活動生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)指南中進一步了解活動。

-   **服務** &ndash; Android 服務支援活動的方式是接管長時間執行的工作，並在背景中執行它們。 服務沒有使用者介面，可用來處理未繫結至螢幕的工作 &ndash; 例如，在背景播放歌曲，或將相片上傳至伺服器。 如需服務的詳細資訊，請參閱[建立服務](~/android/app-fundamentals/services/index.md)和 [Android 服務](~/android/app-fundamentals/services/index.md)指南。


Android 應用程式可能無法使用所有類型的區塊，並通常會有一種類型的數個區塊。 例如，來自 [Hello, Android 快速入門](~/android/get-started/hello-android/hello-android-quickstart.md)的 Phoneword 應用程式由一個活動 (螢幕) 和某些資源檔所組成。 簡單的音樂播放器應用程式可能會有數個活動和一個服務，負責在應用程式在背景時播放音樂。

### <a name="intents"></a>意圖

在 Android 應用程式中的另一個基本概念是「意圖」。
Android 的設計圍繞著「最低權限原則」 &ndash; 應用程式只具有工作所需的區塊存取權，並且對於構成作業系統或其他應用程式的區塊具有有限的存取權。 同樣地，區塊會「鬆散偶合」 &ndash; 它們設計上對於其他區塊的知識極少且存取權有限制 (即使是屬於相同應用程式的區塊)。

為了進行通訊，應用程式區塊會來回傳送稱為「意圖」的非同步訊息。 意圖包含接收區塊的相關資訊，有時還會有某些資料。 從一個應用程式元件傳送的意圖會觸發在另一個應用程式元件中發生某件事，因而繫結兩個應用程式元件，並且允許使用者進行通訊。 藉由來回傳送意圖，您可以讓區塊協調複雜的動作，例如啟動相機應用程式以拍攝並儲存、收集位置資訊，或從一個螢幕巡覽至下一個螢幕。


### <a name="androidmanifestxml"></a>AndroidManifest.XML

當您將區塊新增至應用程式時，它會向特殊的 XML 檔案註冊，這個檔案稱為 **Android 資訊清單**。 資訊清單會追蹤應用程式中的所有應用程式區塊，以及版本需求、權限，以及連結的程式庫 &ndash; 作業系統需要知道的所有項目，以便您的應用程式能執行。 **Android 資訊清單**也適用於活動和意圖，以便控制哪些動作適用於指定活動。 Android 資訊清單的這些進階功能會在[使用 Android 資訊清單](~/android/platform/android-manifest.md)指南中介紹。

在單一螢幕版本的 Phoneword 應用程式中，只有一個活動、一個意圖，而且使用 `AndroidManifest.xml`， 以及如圖示等其他資源。 在多螢幕版本的 Phoneword 中，新增了額外的活動；它是從第一個使用意圖的活動啟動。 下一節探討意圖如何協助在 Android 應用程式中建立導覽。

## <a name="android-navigation"></a>Android 導覽

意圖用來巡覽不同的螢幕。 請深入了解這段程式碼，以查看意圖運作的方式，並了解它們在 Android 導覽中的角色。


### <a name="launching-a-second-activity-with-an-intent"></a>使用意圖啟動第二個活動

在 Phoneword 應用程式中，意圖用來啟動第二個螢幕 (活動)。 請開始建立意圖、傳入目前的「內容」 (`this`，參照目前的**內容**) 以及您要尋找的應用程式區塊類型 (`TranslationHistoryActivity`)：

```csharp
Intent intent = new Intent(this, typeof(TranslationHistoryActivity));
```

**內容**是全域應用程式環境相關資訊的介面 &ndash; 它可讓新建立的物件知道應用程式發生什麼事。 如果您將意圖視為訊息，您會提供訊息收件者的名稱 (`TranslationHistoryActivity`) 和收件者地址 (`Context`)。

Android 提供將簡單資料附加到意圖的選項 (會以不同方式處理複雜的資料)。 在 Phoneword 範例中，`PutStringArrayExtra` 用來將電話號碼清單附加到意圖，並且對意圖收件者呼叫 `StartActivity`。 完成的程式碼如下所示：

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", _phoneNumbers);
    StartActivity(intent);
};
```


## <a name="additional-concepts-introduced-in-phoneword"></a>Phoneword 中引入的其他概念

Phoneword 應用程式引入本指南未涵蓋的數個概念。 這些概念包括：

**字串資源** &ndash; 在 Phoneword 應用程式中，`TranslationHistoryButton` 的文字設為 `"@string/translationHistory"`。 `@string` 語法表示字串值會儲存在「字串資源檔」 **Strings.xml** 中。 `translationHistory` 字串的下列值已新增至 **Strings.xml**：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Call History</string>
</resources>
```

如需關字串資源和其他 Android 資源的詳細資訊，請參閱 [Android 資源指南](~/android/app-fundamentals/resources-in-android/index.md)。

**ListView 和 ArrayAdapter** &ndash; _ListView_ UI 元件提供簡單的方式來呈現資料列的捲動清單。 `ListView` 執行個體需要 _Adapter_ 提供資料列檢視中所含的資料給它。 下列程式碼行用來填入 `TranslationHistoryActivity` 的使用者介面：

```csharp
this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
```

Listview 和 Adapter 已超出本文件的範圍，但在 [Listview 和 Adapter](~/android/user-interface/layouts/list-view/index.md) 指南中有非常詳盡的介紹。
[在 ListView 中填入資料](~/android/user-interface/layouts/list-view/populating.md)特別說明如何使用內建的 `ListActivity` 和 `ArrayAdapter` 類別來建立和填入 `ListView` 而不定義自訂版面配置，如同在 Phoneword 範例中那樣。


## <a name="summary"></a>總結

恭喜您完成第一個多重畫面的 Android 應用程式！ 本指南介紹「Android 應用程式建置組塊」和「意圖」，並用它們建置多螢幕 Android 應用程式。 現在您已經具備可開始開發專屬 Xamarin.Android 應用程式的穩固基礎。

接下來，您將在[建置跨平台應用程式指南](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)中學習使用 Xamarin 建置跨平台應用程式。
