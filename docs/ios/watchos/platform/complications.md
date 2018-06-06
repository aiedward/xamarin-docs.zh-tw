---
title: watchOS Xamarin 的複雜性
description: 本文件說明如何使用 Xamarin 中 watchOS 複雜性。 它會討論如何將複雜的功能，撰寫複雜功能，而範本，並提供範例程式碼。
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/03/2017
ms.openlocfilehash: 3c69f65091e7d6c83afe34c6d8c06477cc5d133b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791835"
---
# <a name="watchos-complications-in-xamarin"></a>watchOS Xamarin 的複雜性

_watchOS 可讓開發人員撰寫自訂的監看式字體的複雜性_

此頁面說明不同類型的複雜性，以及如何將複雜功能加入至 watchOS 3 應用程式。

請注意，每個 watchOS 應用程式只能有一個複雜的問題。

請先閱讀[Apple 文件](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html)來判斷您的應用程式是否適用於複雜的功能。 有 5`CLKComplicationFamily`顯示可從中選擇的型別：

[![](complications-images/all-complications-sml.png "5 的 CLKComplicationFamily 類型： 小循環、 模組化小型、 模組化大型、 頂小型的實際大型")](complications-images/all-complications.png#lightbox)

應用程式可以實作一個樣式，或全部五個，根據所顯示的資料。
您也可以支援時間移動，為使用者開啟數位皇冠過去及/或未來的時間提供值。

<a name="adding" />

## <a name="adding-a-complication"></a>加入複雜功能

### <a name="configuration"></a>組態

可以加入至監看式應用程式，在建立期間，或手動新增至現有的方案的複雜性。

### <a name="add-new-project"></a>加入新的專案...

**加入新的專案...** 精靈包含核取方塊，將會自動建立複雜功能控制器類別及設定**Info.plist**檔案：

![](complications-images/file-new-project-sml.png "包含複雜功能的核取方塊")

### <a name="existing-projects"></a>現有的專案

若要新增的複雜功能加入現有的專案：

1. 建立新**ComplicationController.cs**類別檔案，以及實作`CLKComplicationDataSource`。
2. 設定應用程式的**Info.plist**來公開複雜功能和支援的複雜功能系列的身分識別。

以下更詳細說明這些步驟。

<a name="clkcomplicationcontroller" />

### <a name="clkcomplicationdatasource-class"></a>CLKComplicationDataSource 類別

下列 C# 範本包含最小必要的方法，並實作`CLKComplicationDataSource`。

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

請遵循[撰寫複雜功能](#writing)指示程式碼加入這個類別。

### <a name="infoplist"></a>Info.plist

監看式擴充功能的**Info.plist**檔案應該指定的名稱`CLKComplicationDataSource`和您想要支援的複雜功能系列：

[![](complications-images/complications-config-sml.png "複雜功能系列類型")](complications-images/complications-config.png#lightbox)

**資料來源類別**項目清單會顯示類別名稱的子類別`CLKComplicationDataSource`包含複雜邏輯的子類別。

## <a name="clkcomplicationdatasource"></a>CLKComplicationDataSource

所有複雜功能在單一類別，覆寫方法中都實作`CLKComplicationDataSource`抽象類別 (它會實作`ICLKComplicationDataSource`介面)。

### <a name="required-methods"></a>需要的方法

您必須實作下列方法來執行複雜功能：

- `GetPlaceholderTemplate` -傳回在設定期間，或是應用程式無法提供值時使用的靜態顯示。
- `GetCurrentTimelineEntry` -執行複雜功能時，計算的正確顯示。
- `GetSupportedTimeTravelDirections` -傳回從選項`CLKComplicationTimeTravelDirections`例如`None`， `Forward`， `Backward`，或`Forward | Backward`。

### <a name="privacy"></a>隱私權

顯示個人資料的複雜性

* `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen` 或 `HideOnLockScreen`

如果此方法傳回`HideOnLockScreen`複雜功能就會顯示可能是圖示或應用程式名稱 （以及不含任何資料），則監看式已被鎖定。

### <a name="updates"></a>更新

- `GetNextRequestedUpdateDate` 傳回當作業系統接下來應該查詢的應用程式更新的時間複雜性顯示資料。

您也可以從您的 iOS 應用程式強制更新。

### <a name="supporting-time-travel"></a>支援的時間移動

時間移動的支援是選擇性的並以控制由`GetSupportedTimeTravelDirections`方法。 如果它傳回`Forward`， `Backward`，或`Forward | Backward`則您必須實作下列方法

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing" />

## <a name="writing-a-complication"></a>撰寫複雜的功能

複雜性範圍從簡單的資料顯示複雜的映像和時間移動支援的資料轉譯中。 下列程式碼會示範如何建置簡單、 單一範本的複雜功能。

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>程式碼範例

此範例中只支援`UtilitarianLarge`範本，因此只會選取支援該類型的複雜功能的特定監看式表面上。 當*選取*複雜性上監看式中，它會顯示**我的複雜功能**及何時*執行*其顯示文字**分鐘_小時_**  （與時間部分）。

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

有許多不同的範本可用於每個複雜樣式。
**環形**範本可讓您顯示的進度樣式環形是要複雜功能，可用來以圖形方式顯示進度或一些其他的值。

[Apple 的 CLKComplicationTemplate 文件](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>循環小

這些範本類別名稱所有前面會加上`CLKComplicationTemplateCircularSmall`:

- **RingImage** -顯示使用進度環周圍的單一映像。
- **RingText** -顯示單行文字，使用進度環周圍。
- **SimpleImage** -只會顯示單一的小型影像。
- **SimpleText** -只會顯示文字的小型程式碼片段。
- **StackImage** -顯示的映像和一行文字，在彼此上方
- **StackText** -顯示兩行文字。

### <a name="modular-small"></a>模組化小

這些範本類別名稱所有前面會加上`CLKComplicationTemplateModularSmall`:

- **ColumnsText** -顯示小 （2 個資料列和 2 個資料行） 的文字值的方格。
- **RingImage** -顯示使用進度環周圍的單一映像。
- **RingText** -顯示單行文字，使用進度環周圍。
- **SimpleImage** -只會顯示單一的小型影像。
- **SimpleText** -只會顯示文字的小型程式碼片段。
- **StackImage** -顯示的映像和一行文字，在彼此上方
- **StackText** -顯示兩行文字。

### <a name="modular-large"></a>大型的模組化

這些範本類別名稱所有前面會加上`CLKComplicationTemplateModularLarge`:

- **資料行**-顯示 3 個資料列具有 2 個資料行，並選擇性地包含影像的每個資料列左邊的方格。
- **StandardBody** -顯示為粗體的標頭字串的純文字的兩個資料列。 標頭在左邊，可以選擇性地顯示影像。
- **資料表**-顯示為粗體的標頭字串 2 x 2 方格的下方的文字。 標頭在左邊，可以選擇性地顯示影像。
- **TallBody** -顯示為粗體的標頭字串較大字型單一文字行下方。

### <a name="utilitarian-small"></a>頂小

這些範本類別名稱所有前面會加上`CLKComplicationTemplateUtilitarianSmall`:

- **一般**位在單一行 （文字應該短） 中顯示的映像和一些文字。
- **RingImage** -顯示使用進度環周圍的單一映像。
- **RingText** -顯示單行文字，使用進度環周圍。
- **正方形**-顯示 （40px 或方形的 38 mm 或 42 mm Apple Watch 分別 44px） 的正方形映像。

### <a name="utilitarian-large"></a>頂大型

只有一個這種複雜性樣式範本： `CLKComplicationTemplateUtilitarianLargeFlat`。
它會顯示單一映像和一些文字，所有在單一行。



## <a name="related-links"></a>相關連結

- [Apple 文件](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [WWDC 視訊](https://developer.apple.com/videos/play/wwdc2015-209/)
