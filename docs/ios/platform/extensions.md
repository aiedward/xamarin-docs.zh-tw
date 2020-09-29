---
title: Xamarin.iOS 中的 iOS 延伸模組
description: 本檔說明延伸模組，這是 iOS 在標準內容中（例如，在通知中心內）所呈現的小工具。 它會討論如何建立擴充功能，並從父應用程式與其進行通訊。
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 05/12/2020
ms.openlocfilehash: d305f671ef68e9a1eded61936f3f32cc8769393f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436301"
---
# <a name="ios-extensions-in-xamarinios"></a>Xamarin 中的 iOS 擴充功能

> [!VIDEO https://youtube.com/embed/Sd0-ch9Udmk]

**在 iOS 中建立延伸模組影片**

在 iOS 8 中引進的延伸模組，是 `UIViewControllers` 由 ios 在標準內容（例如，在 **通知中心**內）所呈現的自訂鍵盤型別，是由使用者要求以執行特殊輸入或其他內容（例如編輯可提供特殊效果篩選的相片）的自訂鍵盤類型。

所有延伸模組都會與容器應用程式一起安裝 (使用64位整合 Api 撰寫的元素) ，並且會從主應用程式中的特定擴充點啟用。 而且，因為它們會用來做為現有系統功能的補充，所以它們必須是高效能、精簡且穩固的。

## <a name="extension-points"></a>擴充點

|類型|描述|擴充點|主機應用程式|
|--- |--- |--- |--- |
|動作|特定媒體類型的特殊編輯器或檢視器|`com.apple.ui-services`|任意|
|檔提供者|允許應用程式使用遠端檔存放區|`com.apple.fileprovider-ui`|使用[UIDocumentPickerViewController](xref:UIKit.UIDocumentPickerViewController)的應用程式|
|鍵盤|替代鍵盤|`com.apple.keyboard-service`|任意|
|相片編輯|相片操作與編輯|`com.apple.photo-editing`|相片應用程式編輯器|
|共用|與社交網路、郵件服務等共用資料。|`com.apple.share-services`|任意|
|今天|顯示于 Today 畫面或通知中心的「widget」|`com.apple.widget-extensions`|今天和通知中心|

[Ios 10](~/ios/platform/introduction-to-ios10/index.md#app-extensions)和[ios 12](~/ios/platform/introduction-to-ios12/index.md#notification-improvements)中已新增額外的擴充點。 您可以在 [IOS 應用程式延伸模組程式設計指南](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW2)中找到所有支援類型的完整資料表。

## <a name="limitations"></a>限制

延伸模組有許多限制，其中有些是通用於所有類型 (例如，沒有任何類型的延伸模組可以存取相機或麥克風) 而其他類型的延伸模組在其 (使用方式上可能具有特定限制，例如，自訂鍵盤不能用於安全的資料輸入欄位，例如，) 的密碼。

通用限制如下：

- 無法使用 [健康情況套件](~/ios/platform/healthkit.md) 和 [事件套件 UI](~/ios/platform/eventkit.md) 架構
- 延伸模組無法使用 [延伸的背景模式](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md)
- 延伸模組無法存取裝置的攝影機或麥克風 (但可以存取現有的媒體檔案) 
- 延伸模組無法接收空中捨棄資料 (雖然它們可以透過空氣傳送來傳輸資料) 
- [UIActionSheet](xref:UIKit.UIActionSheet) 和 [UIAlertView](xref:UIKit.UIAlertView) 無法使用;延伸模組必須使用 [UIAlertController](xref:UIKit.UIAlertController)
- 有數個 [UIApplication](xref:UIKit.UIApplication) 成員無法使用： [UIApplication. SharedApplication](xref:UIKit.UIApplication.SharedApplication)、 [UIApplication. OpenUrl](xref:UIKit.UIApplication.OpenUrl(Foundation.NSUrl))、 [UIApplication. BeginIgnoringInteractionEvents](xref:UIKit.UIApplication.BeginIgnoringInteractionEvents) 和 [UIApplication. EndIgnoringInteractionEvents](xref:UIKit.UIApplication.EndIgnoringInteractionEvents)
- iOS 會在現今的延伸模組上強制執行 16 MB 的記憶體使用量限制。
- 依預設，鍵盤擴充功能無法存取網路。 這會影響裝置上的調試 (不會在模擬器) 中強制執行限制，因為 Xamarin 需要網路存取，才能讓偵錯工具運作。 您可以將專案資訊中的值設定為，以要求網路存取。 `Requests Open Access` plist 至 `Yes` 。 如需鍵盤擴充功能限制的詳細資訊，請參閱 Apple 的 [自訂鍵盤指南](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html) 。

如需個別限制，請參閱 Apple 的 [應用程式延伸程式設計指南](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/)。

## <a name="distributing-installing-and-running-extensions"></a>發佈、安裝及執行擴充功能

擴充功能會從容器應用程式內散發，然後透過 App Store 提交和散發。 會在該時間點安裝與應用程式一起散發的擴充 (s) ，但是使用者必須明確啟用每個擴充功能。 不同類型的延伸模組會以不同的方式啟用;有幾個使用者需要流覽至 [ **設定** ] 應用程式，並從該處啟用。 有些則是在使用時啟用，例如在傳送相片時啟用共用延伸模組。

使用擴充功能的應用程式 (使用者遇到延伸模組的位置) 稱為 **主機應用程式**，因為它是在執行時裝載擴充功能的應用程式。 安裝延伸模組的應用程式是 **容器應用程式**，因為它是安裝時包含擴充功能的應用程式。  

一般而言，容器應用程式會描述擴充功能，並引導使用者完成啟用的程式。

## <a name="debug-and-release-versions-of-extensions"></a>擴充功能的偵錯工具和發行版本

執行應用程式擴充功能的記憶體限制明顯低於套用至前景應用程式的記憶體限制。 執行 iOS 的模擬器對於擴充功能的限制較少，而且您可以執行延伸模組而不會有任何問題。 不過，在裝置上執行相同的延伸模組可能會導致非預期的結果，包括延伸模組損毀或系統積極終止。 因此，請確定您已在裝置上建立並測試擴充功能，然後再寄出。

您應確定已將下列設定套用至容器專案和所有參考的擴充功能：

1. 在 **發行** 設定中建立應用程式封裝。
1. 在 [ **IOS 組建** 專案] 設定中，將 [連結 **器行為** ] 選項設定為 [ **僅連結 Framework Sdk** ] 或 [ **全部連結**]。
1. 在 **IOS Debug** 專案設定中，取消核取 [ **啟用調試** 程式和啟用程式碼 **剖析]** 選項。

## <a name="extension-lifecycle"></a>擴充功能生命週期

擴充功能可以像單一 [UIViewController](xref:UIKit.UIViewController) 或更複雜的延伸模組一樣簡單，以呈現多個 UI 畫面。 當使用者遇到 _延伸點_ 時 (例如) 共用映射時，他們就有機會從為該擴充點註冊的擴充功能中進行選擇。

如果他們選擇您的其中一個應用程式延伸模組，則會將其具現 `UIViewController` 化並開始一般 View Controller 生命週期。 不過，不同于一般應用程式（其會在使用者完成與其互動時暫停，但通常不會終止），會載入、執行擴充功能，然後重複終止。

擴充功能可以透過 [NSExtensionCoNtext](xref:Foundation.NSExtensionContext) 物件與其主機應用程式進行通訊。 有些擴充功能具有可接收非同步回呼結果的作業。 這些回呼將會在背景執行緒上執行，且延伸模組必須將這一點列入考慮;例如，如果想要更新使用者介面，請使用 [NSObject. InvokeOnMainThread](xref:Foundation.NSObject.InvokeOnMainThread*) 。 如需詳細資訊，請參閱下面的「 [與主機應用程式通訊](#communicating-with-the-host-app) 」一節。

依預設，擴充功能和其容器應用程式無法通訊，儘管會一起安裝。 在某些情況下，容器應用程式基本上是空的「出貨」容器，其目的是在安裝延伸模組之後提供服務。 不過，如果情況是由容器應用程式和延伸模組所決定，則可以從通用區域共用資源。 此外， **Today 延伸** 模組可能會要求其容器應用程式開啟 URL。 此行為會顯示在 [事件倒數小工具](https://github.com/xamarin/ios-samples/tree/master/intro-to-extensions)中。

## <a name="creating-an-extension"></a>建立延伸模組

擴充功能 (及其容器應用程式) 必須是64位二進位檔，並使用 Xamarin [整合 api](~/cross-platform/macios/unified/index.md)來建立。 開發延伸模組時，您的解決方案會包含至少兩個專案：容器應用程式和容器提供的每個擴充功能一個專案。

### <a name="container-app-project-requirements"></a>容器應用程式專案需求

用來安裝擴充功能的容器應用程式有下列需求：

- 它必須維護延伸模組專案的參考。   
- 它必須是完整的應用程式 (必須能夠順利啟動並執行) 即使沒有提供安裝擴充功能的方法。
- 它必須有一個套件組合識別碼，它是延伸模組專案套件組合識別碼的基礎 (請參閱下一節，以取得) 的詳細資料。

### <a name="extension-project-requirements"></a>擴充功能專案需求

此外，延伸模組的專案具有下列需求：

- 它必須有以容器應用程式套件組合識別碼開頭的套件組合識別碼。 例如，如果容器應用程式的套件組合識別碼為 `com.myCompany.ContainerApp` ，則擴充功能的識別碼可能是 `com.myCompany.ContainerApp.MyExtension` ：

  ![套件組合識別碼](extensions-images/bundleidentifiers.png)
- 它必須 `NSExtensionPointIdentifier` 使用適當的值來定義索引鍵， (例如 `com.apple.widget-extension` **今天** 的通知中心 widget) 的檔案 `Info.plist` 。
- 它也必須以*either*適當的值定義其檔案中的機 `NSExtensionMainStoryboard` 碼或機碼 `NSExtensionPrincipalClass` `Info.plist` ：
  - 您可以使用索引 `NSExtensionMainStoryboard` 鍵來指定腳本的名稱，以呈現延伸 (減號) 的主要 UI `.storyboard` 。 例如，檔案的 `Main` `Main.storyboard` 。
  - 您可以使用索引 `NSExtensionPrincipalClass` 鍵來指定啟動擴充功能時將初始化的類別。 此值必須符合的 **註冊** 值 `UIViewController` ：

  ![主體類別註冊](extensions-images/registerandprincipalclass.png)

特定類型的延伸模組可能會有其他需求。 比方說， **Today** 或 **通知中心** 延伸的主體類別必須執行 [INCWidgetProviding](xref:NotificationCenter.INCWidgetProviding)。

> [!IMPORTANT]
> 如果您使用 Visual Studio for Mac 所提供的延伸模組範本來啟動專案，大部分的 (如果不是全部) 這些需求將會由範本自動提供及符合。

## <a name="walkthrough"></a>逐步解說

在後續的逐步解說中，您將建立一個範例 **今日** 小工具，以計算一年中剩餘的日和天數：

[![此為今日範例的小工具，可計算一年中的日和剩餘天數](extensions-images/carpediemscreenshot-sm.png)](extensions-images/carpediemscreenshot.png#lightbox)

### <a name="creating-the-solution"></a>建立解決方案

若要建立必要的解決方案，請執行下列動作：

1. 首先，建立新的 iOS、 **單一視圖應用程式** 專案，然後按 [ **下一步]** 按鈕：

    [![首先，建立新的 iOS、單一視圖應用程式專案，然後按 [下一步] 按鈕](extensions-images/today01.png)](extensions-images/today01.png#lightbox)
2. 呼叫專案 `TodayContainer` ，然後按 [ **下一步]** 按鈕：

    [![呼叫專案 TodayContainer，然後按 [下一步] 按鈕](extensions-images/today02.png)](extensions-images/today02.png#lightbox)
3. 確認**專案名稱****和方案名稱，然後**按一下 [**建立**] 按鈕以建立方案：

    [![確認專案名稱和方案名稱，然後按一下 [建立] 按鈕以建立方案](extensions-images/today03.png)](extensions-images/today03.png#lightbox)
4. 接下來，在 [**方案總管**] 中，以滑鼠右鍵按一下方案，然後從**Today 延伸**模組範本新增**iOS 延伸**模組專案：

    [![接下來，在 [方案總管中，以滑鼠右鍵按一下方案，然後從 Today 延伸模組範本新增 iOS 延伸模組專案](extensions-images/today04.png)](extensions-images/today04.png#lightbox)
5. 呼叫專案 `DaysRemaining` ，然後按 [ **下一步]** 按鈕：

    [![呼叫專案 DaysRemaining，然後按 [下一步] 按鈕](extensions-images/today05.png)](extensions-images/today05.png#lightbox)
6. 檢查項目，然後按一下 [ **建立** ] 按鈕來建立它：

    [![檢查項目，然後按一下 [建立] 按鈕以建立它](extensions-images/today06.png)](extensions-images/today06.png#lightbox)

產生的解決方案現在應該有兩個專案，如下所示：

[![產生的解決方案現在應該有兩個專案，如下所示](extensions-images/today07.png)](extensions-images/today07.png#lightbox)

### <a name="creating-the-extension-user-interface"></a>建立擴充功能使用者介面

接下來，您將需要為您的 **今日** widget 設計介面。 您可以使用分鏡腳本或在程式碼中建立 UI 來完成這項工作。 以下將詳細說明這兩種方法。

#### <a name="using-storyboards"></a>使用分鏡腳本

若要使用分鏡腳本建立 UI，請執行下列動作：

1. 在 [ **方案總管**中，按兩下擴充功能專案的檔案 `Main.storyboard` 以開啟它進行編輯：

    [![按兩下擴充功能專案的主要分鏡腳本檔案以開啟它進行編輯](extensions-images/today08.png)](extensions-images/today08.png#lightbox)
2. 選取依範本自動新增至 UI 的標籤，並**Name** `TodayMessage` 在**屬性瀏覽器**的 [**小工具**] 索引標籤中指定其名稱：

    [![選取依範本自動新增至 UI 的標籤，並在屬性瀏覽器的 [小工具] 索引標籤中提供其名稱 TodayMessage](extensions-images/today09.png)](extensions-images/today09.png#lightbox)
3. 將變更儲存至腳本。

#### <a name="using-code"></a>使用程式碼

若要在程式碼中建立 UI，請執行下列動作：

1. 在 [ **方案總管**中，選取 [ **DaysRemaining** ] 專案，並加入新的類別，然後呼叫它 `CodeBasedViewController` ：

    [![Aelect DaysRemaining 專案，加入新的類別，並呼叫它 CodeBasedViewController](extensions-images/code01.png)](extensions-images/code01.png#lightbox)
2. 同樣地，在 **方案總管**中，按兩下擴充功能的檔案 `Info.plist` 以開啟它進行編輯：

    [![按兩下擴充功能 plist 檔案以開啟它進行編輯](extensions-images/code02.png)](extensions-images/code02.png#lightbox)
3. 從畫面底部選取 [ **來源視圖** (]) ，然後開啟 `NSExtension` 節點：

    [![從畫面底部選取來源視圖，然後開啟 [NSExtension] 節點](extensions-images/code03.png)](extensions-images/code03.png#lightbox)
4. 移除 `NSExtensionMainStoryboard` 金鑰，並 `NSExtensionPrincipalClass` 以下列值新增 `CodeBasedViewController` ：

    [![移除 NSExtensionMainStoryboard 索引鍵，並新增具有值 CodeBasedViewController 的 NSExtensionPrincipalClass](extensions-images/code04.png)](extensions-images/code04.png#lightbox)
5. 儲存變更。

接著，編輯檔案 `CodeBasedViewController.cs` ，使其看起來如下所示：

```csharp
using System;
using Foundation;
using UIKit;
using NotificationCenter;
using CoreGraphics;

namespace DaysRemaining
{
  [Register("CodeBasedViewController")]
  public class CodeBasedViewController : UIViewController, INCWidgetProviding
  {
    public CodeBasedViewController ()
    {
    }

    public override void ViewDidLoad ()
    {
      base.ViewDidLoad ();

      // Add label to view
      var TodayMessage = new UILabel (new CGRect (0, 0, View.Frame.Width, View.Frame.Height)) {
        TextAlignment = UITextAlignment.Center
      };

      View.AddSubview (TodayMessage);

      // Insert code to power extension here...

    }
  }
}
```

請注意， `[Register("CodeBasedViewController")]` 符合您為上述所指定的值 `NSExtensionPrincipalClass` 。

### <a name="coding-the-extension"></a>編寫延伸模組的程式碼

建立消費者介面之後，請 `TodayViewController.cs` `CodeBasedViewController.cs` 根據用來建立上述消費者介面) 的方法開啟或檔案 (，變更 **ViewDidLoad** 方法，使其看起來如下所示：

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();

  // Calculate the values
  var dayOfYear = DateTime.Now.DayOfYear;
  var leapYearExtra = DateTime.IsLeapYear (DateTime.Now.Year) ? 1 : 0;
  var daysRemaining = 365 + leapYearExtra - dayOfYear;

  // Display the message
  if (daysRemaining == 1) {
    TodayMessage.Text = String.Format ("Today is day {0}. There is one day remaining in the year.", dayOfYear);
  } else {
    TodayMessage.Text = String.Format ("Today is day {0}. There are {1} days remaining in the year.", dayOfYear, daysRemaining);
  }
}
```

如果使用以程式碼為基礎的消費者介面方法，請將 `// Insert code to power extension here...` 批註取代為上述的新程式碼。 在呼叫基底執行 (，並針對以程式碼為基礎的版本) 插入標籤之後，此程式碼會執行簡單的計算，以取得一年中的日期，以及剩餘的天數。 然後，它會在 `TodayMessage` 您于 UI 設計中建立 () 的標籤中顯示訊息。

請注意，此程式與撰寫應用程式的一般程式有何相似之處。 延伸模組的 `UIViewController` 生命週期與應用程式中的 View Controller 相同，但延伸模組沒有背景模式，且當使用者完成使用時不會暫停。 相反地，擴充功能會依需要重複進行初始化和解除配置。

### <a name="creating-the-container-app-user-interface"></a>建立容器應用程式使用者介面

針對此逐步解說，容器應用程式只是用來做為出貨及安裝延伸模組的方法，並不提供其本身的功能。 編輯 TodayContainer 的檔案 `Main.storyboard` ，並新增一些定義延伸模組功能的文字，以及如何安裝它：

[![編輯 TodayContainers 的主要分鏡腳本檔案，並新增一些定義延伸模組功能的文字，以及如何安裝它](extensions-images/today10.png)](extensions-images/today10.png#lightbox)

將變更儲存至腳本。

### <a name="testing-the-extension"></a>測試擴充功能

若要在 iOS 模擬器中測試您的延伸模組，請執行 **TodayContainer** 應用程式。 容器的主視圖將會顯示：

[![將會顯示容器主視圖](extensions-images/run01.png)](extensions-images/run01.png#lightbox)

接著，按下模擬器中的 [ **首頁** ] 按鈕，從畫面頂端向下滑動以開啟 [ **通知中心**]，選取 [ **Today** ] 索引標籤，然後按一下 [ **編輯** ] 按鈕：

[![點擊模擬器中的 [首頁] 按鈕，從畫面頂端向下滑動以開啟通知中心，選取 [Today] 索引標籤，然後按一下 [編輯] 按鈕](extensions-images/run02.png)](extensions-images/run02.png#lightbox)

將 **DaysRemaining** 延伸模組新增至 **Today** 視圖，然後按一下 [ **完成** ] 按鈕：

[![將 DaysRemaining 延伸模組新增至 Today 視圖，然後按一下 [完成] 按鈕](extensions-images/run03.png)](extensions-images/run03.png#lightbox)

新的 widget 將會新增至 **Today** 視圖，並顯示結果：

[![新的 widget 將會新增至 Today 視圖，而且會顯示結果](extensions-images/run04.png)](extensions-images/run04.png#lightbox)

## <a name="communicating-with-the-host-app"></a>與主機應用程式通訊

您在上面建立的今日範例延伸模組不會 ([ **today** ] 畫面) 來與其主機應用程式進行通訊。 如果有，則會使用或類別的 [ExtensionCoNtext](xref:Foundation.NSExtensionContext) 屬性 `TodayViewController` `CodeBasedViewController` 。

針對將從主應用程式接收資料的擴充功能，資料會以[NSExtensionItem](xref:Foundation.NSExtensionItem)物件陣列的形式儲存在延伸模組的[ExtensionCoNtext](xref:Foundation.NSExtensionContext)的[InputItems](xref:Foundation.NSExtensionContext.InputItems)屬性中 `UIViewController` 。

其他的延伸模組（例如照片編輯延伸模組）可能會區分使用者完成或取消使用情形。 這會透過[ExtensionCoNtext](xref:Foundation.NSExtensionContext)屬性的[CompleteRequest](xref:Foundation.NSExtensionContext.CompleteRequest*)和[CancelRequest](xref:Foundation.NSExtensionContext.CancelRequest*)方法，向主機應用程式發出信號。

如需詳細資訊，請參閱 Apple 的 [應用程式延伸程式設計指南](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1)。

## <a name="communicating-with-the-parent-app"></a>與父應用程式通訊

「應用程式群組」可讓不同的應用程式 (或應用程式及其擴充功能) 存取共用檔案儲存體位置。 「應用程式群組」可用於資料下列資料：

- [Apple Watch 設定](~/ios/watchos/app-fundamentals/settings.md)。
- [共用的 NSUserDefaults](~/ios/app-fundamentals/user-defaults.md)。
- [共用](~/ios/watchos/app-fundamentals/parent-app.md#files)檔案。

如需詳細資訊，請參閱**使用功能**檔的「[應用程式群組](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)」一節。

## <a name="mobilecoreservices"></a>MobileCoreServices

使用延伸模組時，請使用統一類型識別碼 (UTI) 來建立和操作應用程式、其他應用程式及/或服務之間交換的資料。

`MobileCoreServices.UTType`靜態類別會定義下列與 Apple 定義相關的 helper 屬性 `kUTType...` ：

- `kUTTypeAlembic` - `Alembic`
- `kUTTypeAliasFile` - `AliasFile`
- `kUTTypeAliasRecord` - `AliasRecord`
- `kUTTypeAppleICNS` - `AppleICNS`
- `kUTTypeAppleProtectedMPEG4Audio` - `AppleProtectedMPEG4Audio`
- `kUTTypeAppleProtectedMPEG4Video` - `AppleProtectedMPEG4Video`
- `kUTTypeAppleScript` - `AppleScript`
- `kUTTypeApplication` - `Application`
- `kUTTypeApplicationBundle` - `ApplicationBundle`
- `kUTTypeApplicationFile` - `ApplicationFile`
- `kUTTypeArchive` - `Archive`
- `kUTTypeAssemblyLanguageSource` - `AssemblyLanguageSource`
- `kUTTypeAudio` - `Audio`
- `kUTTypeAudioInterchangeFileFormat` - `AudioInterchangeFileFormat`
- `kUTTypeAudiovisualContent` - `AudiovisualContent`
- `kUTTypeAVIMovie` - `AVIMovie`
- `kUTTypeBinaryPropertyList` - `BinaryPropertyList`
- `kUTTypeBMP` - `BMP`
- `kUTTypeBookmark` - `Bookmark`
- `kUTTypeBundle` - `Bundle`
- `kUTTypeBzip2Archive` - `Bzip2Archive`
- `kUTTypeCalendarEvent` - `CalendarEvent`
- `kUTTypeCHeader` - `CHeader`
- `kUTTypeCommaSeparatedText` - `CommaSeparatedText`
- `kUTTypeCompositeContent` - `CompositeContent`
- `kUTTypeConformsToKey` - `ConformsToKey`
- `kUTTypeContact` - `Contact`
- `kUTTypeContent` - `Content`
- `kUTTypeCPlusPlusHeader` - `CPlusPlusHeader`
- `kUTTypeCPlusPlusSource` - `CPlusPlusSource`
- `kUTTypeCSource` - `CSource`
- `kUTTypeData` - `Database`
- `kUTTypeDelimitedText` - `DelimitedText`
- `kUTTypeDescriptionKey` - `DescriptionKey`
- `kUTTypeDirectory` - `Directory`
- `kUTTypeDiskImage` - `DiskImage`
- `kUTTypeElectronicPublication` - `ElectronicPublication`
- `kUTTypeEmailMessage` - `EmailMessage`
- `kUTTypeExecutable` - `Executable`
- `kUTExportedTypeDeclarationsKey` - `ExportedTypeDeclarationsKey`
- `kUTTypeFileURL` - `FileURL`
- `kUTTypeFlatRTFD` - `FlatRTFD`
- `kUTTypeFolder` - `Folder`
- `kUTTypeFont` - `Font`
- `kUTTypeFramework` - `Framework`
- `kUTTypeGIF` - `GIF`
- `kUTTypeGNUZipArchive` - `GNUZipArchive`
- `kUTTypeHTML` - `HTML`
- `kUTTypeICO` - `ICO`
- `kUTTypeIconFileKey` - `IconFileKey`
- `kUTTypeIdentifierKey` - `IdentifierKey`
- `kUTTypeImage` - `Image`
- `kUTImportedTypeDeclarationsKey` - `ImportedTypeDeclarationsKey`
- `kUTTypeInkText` - `InkText`
- `kUTTypeInternetLocation` - `InternetLocation`
- `kUTTypeItem` - `Item`
- `kUTTypeJavaArchive` - `JavaArchive`
- `kUTTypeJavaClass` - `JavaClass`
- `kUTTypeJavaScript` - `JavaScript`
- `kUTTypeJavaSource` - `JavaSource`
- `kUTTypeJPEG` - `JPEG`
- `kUTTypeJPEG2000` - `JPEG2000`
- `kUTTypeJSON` - `JSON`
- `kUTType3dObject` - `k3dObject`
- `kUTTypeLivePhoto` - `LivePhoto`
- `kUTTypeLog` - `Log`
- `kUTTypeM3UPlaylist` - `M3UPlaylist`
- `kUTTypeMessage` - `Message`
- `kUTTypeMIDIAudio` - `MIDIAudio`
- `kUTTypeMountPoint` - `MountPoint`
- `kUTTypeMovie` - `Movie`
- `kUTTypeMP3` - `MP3`
- `kUTTypeMPEG` - `MPEG`
- `kUTTypeMPEG2TransportStream` - `MPEG2TransportStream`
- `kUTTypeMPEG2Video` - `MPEG2Video`
- `kUTTypeMPEG4` - `MPEG4`
- `kUTTypeMPEG4Audio` - `MPEG4Audio`
- `kUTTypeObjectiveCPlusPlusSource` - `ObjectiveCPlusPlusSource`
- `kUTTypeObjectiveCSource` - `ObjectiveCSource`
- `kUTTypeOSAScript` - `OSAScript`
- `kUTTypeOSAScriptBundle` - `OSAScriptBundle`
- `kUTTypePackage` - `Package`
- `kUTTypePDF` - `PDF`
- `kUTTypePerlScript` - `PerlScript`
- `kUTTypePHPScript` - `PHPScript`
- `kUTTypePICT` - `PICT`
- `kUTTypePKCS12` - `PKCS12`
- `kUTTypePlainText` - `PlainText`
- `kUTTypePlaylist` - `Playlist`
- `kUTTypePluginBundle` - `PluginBundle`
- `kUTTypePNG` - `PNG`
- `kUTTypePolygon` - `Polygon`
- `kUTTypePresentation` - `Presentation`
- `kUTTypePropertyList` - `PropertyList`
- `kUTTypePythonScript` - `PythonScript`
- `kUTTypeQuickLookGenerator` - `QuickLookGenerator`
- `kUTTypeQuickTimeImage` - `QuickTimeImage`
- `kUTTypeQuickTimeMovie` - `QuickTimeMovie`
- `kUTTypeRawImage` - `RawImage`
- `kUTTypeReferenceURLKey` - `ReferenceURLKey`
- `kUTTypeResolvable` - `Resolvable`
- `kUTTypeRTF` - `RTF`
- `kUTTypeRTFD` - `RTFD`
- `kUTTypeRubyScript` - `RubyScript`
- `kUTTypeScalableVectorGraphics` - `ScalableVectorGraphics`
- `kUTTypeScript` - `Script`
- `kUTTypeShellScript` - `ShellScript`
- `kUTTypeSourceCode` - `SourceCode`
- `kUTTypeSpotlightImporter` - `SpotlightImporter`
- `kUTTypeSpreadsheet` - `Spreadsheet`
- `kUTTypeStereolithography` - `Stereolithography`
- `kUTTypeSwiftSource` - `SwiftSource`
- `kUTTypeSymLink` - `SymLink`
- `kUTTypeSystemPreferencesPane` - `SystemPreferencesPane`
- `kUTTypeTabSeparatedText` - `TabSeparatedText`
- `kUTTagClassFilenameExtension` - `TagClassFilenameExtension`
- `kUTTagClassMIMEType` - `TagClassMIMEType`
- `kUTTypeTagSpecificationKey` - `TagSpecificationKey`
- `kUTTypeText` - `Text`
- `kUTType3DContent` - `ThreeDContent`
- `kUTTypeTIFF` - `TIFF`
- `kUTTypeToDoItem` - `ToDoItem`
- `kUTTypeTXNTextAndMultimediaData` - `TXNTextAndMultimediaData`
- `kUTTypeUniversalSceneDescription` - `UniversalSceneDescription`
- `kUTTypeUnixExecutable` - `UnixExecutable`
- `kUTTypeURL` - `URL`
- `kUTTypeURLBookmarkData` - `URLBookmarkData`
- `kUTTypeUTF16ExternalPlainText` - `UTF16ExternalPlainText`
- `kUTTypeUTF16PlainText` - `UTF16PlainText`
- `kUTTypeUTF8PlainText` - `UTF8PlainText`
- `kUTTypeUTF8TabSeparatedText` - `UTF8TabSeparatedText`
- `kUTTypeVCard` - `VCard`
- `kUTTypeVersionKey` - `VersionKey`
- `kUTTypeVideo` - `Video`
- `kUTTypeVolume` - `Volume`
- `kUTTypeWaveformAudio` - `WaveformAudio`
- `kUTTypeWebArchive` - `WebArchive`
- `kUTTypeWindowsExecutable` - `WindowsExecutable`
- `kUTTypeX509Certificate` - `X509Certificate`
- `kUTTypeXML` - `XML`
- `kUTTypeXMLPropertyList` - `XMLPropertyList`
- `kUTTypeXPCService` - `XPCService`
- `kUTTypeZipArchive` - `ZipArchive`

請參閱下列範例：

```csharp
using MobileCoreServices;
...

NSItemProvider itemProvider = new NSItemProvider ();
itemProvider.LoadItem(UTType.PropertyList ,null, (item, err) => {
    if (err == null) {
        NSDictionary results = (NSDictionary )item;
        NSString baseURI =
results.ObjectForKey("NSExtensionJavaScriptPreprocessingResultsKey");
    }
});
```

如需詳細資訊，請參閱**使用功能**檔的「[應用程式群組](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)」一節。

## <a name="precautions-and-considerations"></a>預防措施與考慮

擴充功能的可用記憶體比應用程式更少。 它們預期會快速執行，並對使用者和其所裝載的應用程式進行最低的入侵。 不過，延伸模組也應為取用應用程式提供一種獨特且有用的函式，並使用品牌化 UI，讓使用者識別其所屬的延伸模組開發人員或容器應用程式。

基於這些嚴格的要求，您應該只部署已徹底測試並針對效能和記憶體耗用量優化的延伸模組。

## <a name="summary"></a>摘要

本檔涵蓋了延伸模組、其功能、延伸模組的類型，以及 iOS 所加諸的已知限制。 其中討論了如何建立、散發、安裝及執行延伸模組和延伸模組生命週期。 它提供了建立簡單的 [ **今日** ] 小工具的逐步解說，其中顯示兩種使用分鏡腳本或程式碼來建立 widget UI 的方式。 它會示範如何在 iOS 模擬器中測試擴充功能。 最後，它會簡短討論如何與主機應用程式通訊，以及開發延伸模組時應採取的一些預防措施和考慮。

## <a name="related-links"></a>相關連結

- [ContainerApp (範例) ](/samples/xamarin/ios-samples/intro-to-extensions)
- [在 Xamarin 中建立延伸模組 (影片) ](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
- [將 iOS 應用程式擴充功能的效率和效能優化](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/ExtensionCreation.html#//apple_ref/doc/uid/TP40014214-CH5-SW7)