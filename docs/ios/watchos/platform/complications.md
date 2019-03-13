---
title: watchOS 在 Xamarin 中的複雜功能
description: 本文件說明如何使用 Xamarin watchOS 複雜。 它討論如何將複雜的功能，撰寫複雜功能 」，這是範本，並提供範例程式碼。
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/03/2017
ms.openlocfilehash: 85b0c9b0688e9fb310a8f427018a02fe629404bb
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117742"
---
# <a name="watchos-complications-in-xamarin"></a>watchOS 在 Xamarin 中的複雜功能

_watchOS 可讓開發人員可以撰寫自訂複雜監看式臉部_

此頁面說明不同類型的複雜性，以及如何將複雜功能新增至您的 watchOS 3 應用程式。

請注意，每個 watchOS 應用程式都只能有一個複雜的問題。

請先閱讀[Apple 文件](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html)來判斷您的應用程式是否適用於複雜功能。 有 5`CLKComplicationFamily`顯示可從中選擇的類型：

[![](complications-images/all-complications-sml.png "可用的 5 個 CLKComplicationFamily 類型： 循環小型、 模組化小型、 大型模組化、 頂小型、 頂大型")](complications-images/all-complications.png#lightbox)

應用程式可以實作一個樣式，或所有五個，根據所顯示的資料。
您也可以支援時間移動，為使用者開啟數位皇冠提供過去及/或未來的時間值。

<a name="adding" />

## <a name="adding-a-complication"></a>加入複雜功能

### <a name="configuration"></a>組態

可以加入至監看式應用程式在建立期間，或手動新增至現有的方案的複雜性。

### <a name="add-new-project"></a>加入新的專案...

**加入新的專案...** 精靈包含核取方塊，將會自動建立複雜度控制器類別及設定**Info.plist**檔案：

![](complications-images/file-new-project-sml.png "包含複雜功能的核取方塊")

### <a name="existing-projects"></a>現有的專案

若要新增至現有的專案的複雜功能：

1. 建立新**ComplicationController.cs**類別檔案，以及實作`CLKComplicationDataSource`。
2. 將應用程式設定**Info.plist**公開的複雜功能，與支援的複雜功能系列的身分識別。

在下面詳細說明這些步驟。

<a name="clkcomplicationcontroller" />

### <a name="clkcomplicationdatasource-class"></a>CLKComplicationDataSource 類別

下列C#範本包含的所需的最少方法，來實作`CLKComplicationDataSource`。

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
  public ComplicationController ()
  {
    }
  public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
    }
  public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
    }
  public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
    }
}
```

請遵循[撰寫複雜功能](#writing)加入至這個類別的程式碼的指示。

### <a name="infoplist"></a>Info.plist

監看式延伸模組**Info.plist**檔案應該指定的名稱`CLKComplicationDataSource`和您想要支援的複雜功能系列：

[![](complications-images/complications-config-sml.png "複雜功能系列類型")](complications-images/complications-config.png#lightbox)

**資料來源類別**項目清單會顯示類別名稱的子類別`CLKComplicationDataSource`子類別，其中包含您的複雜邏輯。

## <a name="clkcomplicationdatasource"></a>CLKComplicationDataSource

單一類別，覆寫方法中實作所有複雜功能`CLKComplicationDataSource`抽象類別 (它會實作`ICLKComplicationDataSource`介面)。

### <a name="required-methods"></a>必要的方法

您必須實作下列方法來執行複雜功能：

- `GetPlaceholderTemplate` -傳回靜態設定期間，或是應用程式無法提供值時使用的顯示。
- `GetCurrentTimelineEntry` -執行複雜功能時，計算正確顯示。
- `GetSupportedTimeTravelDirections` -傳回從選項`CLKComplicationTimeTravelDirections`這類`None`， `Forward`， `Backward`，或`Forward | Backward`。

### <a name="privacy"></a>隱私權

顯示個人資料的複雜功能

* `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen` 或 `HideOnLockScreen`

如果此方法傳回`HideOnLockScreen`複雜功能會顯示可能是圖示或應用程式名稱 （和不含任何資料），然後當鎖定監看式。

### <a name="updates"></a>更新

- `GetNextRequestedUpdateDate` 傳回當作業系統應該接下來會查詢的應用程式一次更新複雜功能顯示的資料。

您也可以從您的 iOS 應用程式強制更新。

### <a name="supporting-time-travel"></a>支援的時間移動

時間移動的支援是選擇性的並由控制`GetSupportedTimeTravelDirections`方法。 如果它傳回`Forward`， `Backward`，或`Forward | Backward`則您必須實作下列方法

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing" />

## <a name="writing-a-complication"></a>撰寫複雜功能

複雜性範圍從簡單的資料顯示複雜的映像和時間移動支援的資料轉譯中。 下列程式碼示範如何建置簡單、 單一範本的複雜功能。

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>程式碼範例

此範例中僅支援`UtilitarianLarge`範本，因此只能選取支援該類型的複雜功能的特定監看式表面上。 當*選取*複雜性上監看式，它會顯示**我的複雜功能**及何時*執行*則會顯示文字**分鐘_小時_**  （與時間的部分）。

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
    public ComplicationController ()
    {
    }
    public ComplicationController (IntPtr p) : base (p)
    {
    }
    public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
        CLKComplicationTimelineEntry entry = null;
    var complicationDisplay = "MINUTE " + DateTime.Now.Minute.ToString(); // text to display on watch face
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge)
        {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText(complicationDisplay); // dynamic display
            entry = CLKComplicationTimelineEntry.Create(NSDate.Now, textTemplate);
        } else {
            Console.WriteLine("Complication family timeline not supported (" + complication.Family + ")");
        }
        handler (entry);
    }
    public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
        CLKComplicationTemplate template = null;
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge) {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat ();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText ("MY COMPLICATION"); // static display
            template = textTemplate;
        } else {
            Console.WriteLine ("Complication family placeholder not not supported (" + complication.Family + ")");
        }
        handler (template);
    }
    public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
        handler (CLKComplicationTimeTravelDirections.None);
    }
}
```


<a name="templates" />

## <a name="complication-templates"></a>複雜功能的範本

有許多不同的範本適用於每個複雜功能樣式。
**環形**範本可讓您顯示的進度 style 環形是要複雜功能，可用來以圖形方式顯示進度或其他值。

[Apple CLKComplicationTemplate 文件](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>圓形 （小）

這些範本類別名稱的前面有`CLKComplicationTemplateCircularSmall`:

- **RingImage** -顯示使用進度環其周圍的單一映像。
- **RingText** -顯示一行文字，其周圍的進度環。
- **SimpleImage** -只會顯示一個小型的單一影像。
- **SimpleText** -只會顯示文字中的小型程式碼片段。
- **StackImage** -顯示的映像和一行文字，在彼此上方
- **StackText** -顯示兩行文字。

### <a name="modular-small"></a>模組化的小型

這些範本類別名稱的前面有`CLKComplicationTemplateModularSmall`:

- **ColumnsText** -顯示的文字值 （2 個資料列和 2 個資料行） 的小型格線。
- **RingImage** -顯示使用進度環其周圍的單一映像。
- **RingText** -顯示一行文字，其周圍的進度環。
- **SimpleImage** -只會顯示一個小型的單一影像。
- **SimpleText** -只會顯示文字中的小型程式碼片段。
- **StackImage** -顯示的映像和一行文字，在彼此上方
- **StackText** -顯示兩行文字。

### <a name="modular-large"></a>大型的模組化

這些範本類別名稱的前面有`CLKComplicationTemplateModularLarge`:

- **資料行**-顯示 3 個資料列，具有 2 個資料行，選擇性地包括映像的每個資料列左邊的方格。
- **StandardBody** -顯示為粗體標頭字串的純文字的兩個資料列。 標頭在左邊，可以選擇顯示映像。
- **資料表**-顯示為粗體標頭字串 2x2 方格其下方的文字。 標頭在左邊，可以選擇顯示映像。
- **TallBody** -顯示為粗體標頭字串大字型單一文字行下方。

### <a name="utilitarian-small"></a>取個實用的小

這些範本類別名稱的前面有`CLKComplicationTemplateUtilitarianSmall`:

- **一般**-顯示影像和某些文字，以單行 （文字應該短一點）。
- **RingImage** -顯示使用進度環其周圍的單一映像。
- **RingText** -顯示一行文字，其周圍的進度環。
- **正方形**-顯示方形的影像 （40px 或方形的 38 mm 或 42 mm Apple Watch 分別 44px）。

### <a name="utilitarian-large"></a>頂大型

沒有此複雜功能樣式的只有一個範本： `CLKComplicationTemplateUtilitarianLargeFlat`。
它會顯示單一映像和一些文字，全都在單一行。



## <a name="related-links"></a>相關連結

- [Apple 文件](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [WWDC 影片](https://developer.apple.com/videos/play/wwdc2015-209/)
