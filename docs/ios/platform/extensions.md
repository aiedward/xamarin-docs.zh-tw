---
title: 在 Xamarin 中的 iOS 擴充功能
description: 本檔說明延伸模組，這些擴充功能是 iOS 在標準內容（例如，在通知中心內）所呈現的小工具。 它會討論如何建立擴充功能，並從父應用程式與其通訊。
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
ms.custom: xamu-video
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: 5995ba06873b2fb5f75c593fbc7136806e50d982
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "70290595"
---
# <a name="ios-extensions-in-xamarinios"></a>在 Xamarin 中的 iOS 擴充功能

> [!VIDEO https://youtube.com/embed/Sd0-ch9Udmk]

**在 iOS 中建立擴充功能影片**

Ios 8 中引進的延伸模組是由 ios `UIViewControllers`在標準內容（例如，在**通知中心**內）提供的特製化，如同使用者執行特定輸入或其他內容所要求的自訂鍵盤類型如同編輯可提供特殊效果篩選的相片。

所有延伸模組會與容器應用程式一起安裝（同時使用64位整合 Api 撰寫的兩個元素），並從主機應用程式中的特定擴充點啟用。 而且因為它們會用來做為現有系統函數的補充，所以它們必須是高效能、精簡且健全。 

## <a name="extension-points"></a>擴充點

|類型|描述|擴充點|主機應用程式|
|--- |--- |--- |--- |
|動作|特定媒體類型的特殊編輯器或檢視器|`com.apple.ui-services`|Any|
|檔提供者|允許應用程式使用遠端檔存放區|`com.apple.fileprovider-ui`|使用[UIDocumentPickerViewController](xref:UIKit.UIDocumentPickerViewController)的應用程式|
|鍵盤|替代鍵盤|`com.apple.keyboard-service`|Any|
|相片編輯|相片操作和編輯|`com.apple.photo-editing`|相片. 應用程式編輯器|
|共用|與社交網路、訊息服務等共用資料。|`com.apple.share-services`|Any|
|今天|出現在 [Today] 畫面或 [通知中心] 的「widget」|`com.apple.widget-extensions`|今天和通知中心|

IOS 10 中新增了[額外的擴充點](~/ios/platform/introduction-to-ios10/index.md#app-extensions)。

## <a name="limitations"></a>限制

延伸模組有許多限制，其中有些是通用於所有類型（例如，沒有任何類型的延伸模組可以存取相機或麥克風），而其他類型的延伸模組對其使用方式可能有特定限制（例如，自訂鍵盤）。不能用於安全的資料輸入欄位，例如用於密碼）。 

通用限制如下：

- [健康情況套件](~/ios/platform/healthkit.md)和[事件套件 UI](~/ios/platform/eventkit.md)架構無法使用
- 延伸模組無法使用[延伸背景模式](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md)
- 延伸模組無法存取裝置的相機或麥克風（雖然它們可以存取現有的媒體檔案）
- 延伸模組無法接收空氣放置資料（雖然可以透過空中傳輸資料）
- 無法使用[UIActionSheet](xref:UIKit.UIActionSheet)和[UIAlertView](xref:UIKit.UIAlertView) ;延伸模組必須使用[UIAlertController](xref:UIKit.UIAlertController)
- [UIApplication](xref:UIKit.UIApplication)有數個成員無法使用：[UIApplication. SharedApplication](xref:UIKit.UIApplication.SharedApplication)、 [UIApplication、OpenUrl](xref:UIKit.UIApplication.OpenUrl(Foundation.NSUrl))、 [UIApplication](xref:UIKit.UIApplication.BeginIgnoringInteractionEvents)和[BeginIgnoringInteractionEvents.](xref:UIKit.UIApplication.EndIgnoringInteractionEvents) UIApplication
- iOS 會在現今的延伸模組上強制執行 16 MB 的記憶體使用量限制。
- 根據預設，鍵盤擴充功能沒有網路的存取權。 這會影響裝置上的偵錯工具（此限制不會在模擬器中強制執行），因為 Xamarin。 iOS 需要網路存取權才能進行偵測。 藉由將專案 Info 中的`Requests Open Access`值設定為`Yes`，即可要求網路存取。 如需鍵盤擴充功能限制的詳細資訊，請參閱 Apple 的[自訂鍵盤指南](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html)。

如需個別限制，請參閱 Apple 的[應用程式擴充程式設計指南](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/)。

## <a name="distributing-installing-and-running-extensions"></a>散發、安裝及執行延伸模組

延伸模組會從容器應用程式中散發，然後透過 App Store 提交和散發。 應用程式所散發的延伸模組會安裝在該時間點，但是使用者必須明確啟用每個擴充功能。 不同類型的延伸模組會以不同的方式啟用;有幾個要求使用者流覽至 [**設定**] 應用程式，並從該處加以啟用。 雖然其他人會在使用時啟用，例如在傳送相片時啟用共用延伸模組。 

使用延伸模組的應用程式（使用者遇到擴充點的位置）稱為**主機應用程式**，因為它是在執行時裝載擴充功能的應用程式。 安裝擴充功能的應用程式是**容器應用程式**，因為它是安裝時包含擴充功能的應用程式。  

一般而言，容器應用程式會描述延伸模組，並引導使用者完成啟用該擴充功能的過程。

## <a name="extension-lifecycle"></a>延伸模組生命週期

擴充功能可以像單一[UIViewController](xref:UIKit.UIViewController)或更複雜的延伸模組一樣簡單，這些擴充功能會呈現多個 UI 畫面。 當使用者遇到_擴充點_時（例如共用影像時），他們將有機會從為該擴充點註冊的擴充功能中選擇。 

如果他們選擇您的其中一個應用程式延伸模組`UIViewController` ，則會具現化並開始一般的 View Controller 生命週期。 不過，不同于一般應用程式，當使用者完成與其互動時，會將擴充功能載入、執行，然後重複終止。

擴充功能可以透過[NSExtensionCoNtext](xref:Foundation.NSExtensionContext)物件與其主機應用程式通訊。 某些擴充功能具有會以結果接收非同步回呼的作業。 這些回呼將會在背景執行緒上執行，而且延伸模組必須考慮這一點;例如，如果想要更新使用者介面，請使用[NSObject. InvokeOnMainThread。](xref:Foundation.NSObject.InvokeOnMainThread*) 如需詳細資訊，請參閱下面的[與主機應用程式通訊](#communicating-with-the-host-app)一節。

根據預設，雖然延伸模組及其容器應用程式會一起安裝，但無法進行通訊。 在某些情況下，容器應用程式基本上是空的「出貨」容器，其目的是在安裝擴充功能之後提供。 不過，如果環境規定，容器應用程式和延伸模組可能會共用來自通用區域的資源。 此外，「**今日」延伸**模組可能會要求其容器應用程式開啟 URL。 此行為會顯示在[事件倒數 Widget](https://github.com/xamarin/ios-samples/tree/master/intro-to-extensions)中。

## <a name="creating-an-extension"></a>建立擴充功能

延伸模組（及其容器應用程式）必須是64位的二進位檔，並使用 Xamarin iOS[整合 api](~/cross-platform/macios/unified/index.md)建立。 開發擴充功能時，您的解決方案會包含至少兩個專案：容器應用程式和容器所提供的每個延伸模組一個專案。

### <a name="container-app-project-requirements"></a>容器應用程式專案需求

用來安裝延伸模組的容器應用程式具有下列需求：

- 它必須維護延伸模組專案的參考。   
- 它必須是完整的應用程式（必須能夠順利啟動並執行），即使它不是提供安裝擴充功能的方法也一樣。 
- 它必須有一個套件組合識別碼，這是擴充功能專案的套件組合識別碼的基礎（如需詳細資訊，請參閱下一節）。

### <a name="extension-project-requirements"></a>擴充功能專案需求

此外，擴充功能的專案具有下列需求：

- 它必須具有以容器應用程式的套件組合識別碼開頭的套件組合識別碼。 例如，如果容器應用程式的套件組合識別碼為`com.myCompany.ContainerApp`，延伸模組的識別碼可能是： `com.myCompany.ContainerApp.MyExtension` 

  ![](extensions-images/bundleidentifiers.png) 
- 它必須`NSExtensionPointIdentifier`在其`com.apple.widget-extension` 檔案中定義具有適當值的金鑰（例如今日通知中心widget的`Info.plist` ）。
- 它也必須在其`NSExtensionMainStoryboard` `NSExtensionPrincipalClass` `Info.plist`檔案中以*適當的值定義金鑰*或金鑰：
  - 使用索引`.storyboard`鍵來指定呈現延伸模組之主要 UI 的分鏡腳本名稱（減號）。 `NSExtensionMainStoryboard` 例如， `Main` `Main.storyboard`針對檔案。
  - 使用索引`NSExtensionPrincipalClass`鍵來指定將在啟動擴充功能時初始化的類別。 此值必須符合的**Register**值`UIViewController`： 

  ![](extensions-images/registerandprincipalclass.png)

特定類型的擴充功能可能會有額外的需求。 例如， **Today**或**通知中心**延伸的主體類別必須執行[INCWidgetProviding](xref:NotificationCenter.INCWidgetProviding)。

> [!IMPORTANT]
> 如果您使用 Visual Studio for Mac 提供的延伸模組範本來啟動您的專案，則範本會自動提供這些需求，並為您滿足。

## <a name="walkthrough"></a>逐步解說 

在下列逐步解說中，您將建立一個範例**今日**widget，以計算一年中剩餘的日和天數：

[![](extensions-images/carpediemscreenshot-sm.png "此為今日範例的小工具，會計算年中剩餘的日和天數")](extensions-images/carpediemscreenshot.png#lightbox)

### <a name="creating-the-solution"></a>建立解決方案

若要建立所需的解決方案，請執行下列動作：

1. 首先，建立新的 iOS、**單一視圖應用程式**專案，然後按 [**下一步]** 按鈕： 

    [![](extensions-images/today01.png "首先，建立新的 iOS、單一視圖應用程式專案，然後按 [下一步] 按鈕")](extensions-images/today01.png#lightbox)
2. 呼叫專案`TodayContainer` ，然後按 [**下一步]** 按鈕： 

    [![](extensions-images/today02.png "呼叫專案 TodayContainer，然後按 [下一步] 按鈕")](extensions-images/today02.png#lightbox)
3. 確認**專案名稱**和解決**解決方案，然後按一下**[**建立**] 按鈕以建立方案： 

    [![](extensions-images/today03.png "確認專案名稱和解決解決方案，然後按一下 [建立] 按鈕以建立方案")](extensions-images/today03.png#lightbox)
4. 接下來，在 **方案總管**中，以滑鼠右鍵按一下方案，然後從 **今日擴充**功能 範本新增**iOS 擴充**功能專案： 

    [![](extensions-images/today04.png "接下來，在 [方案總管中，以滑鼠右鍵按一下方案，然後從 今日擴充功能] 範本新增 iOS 擴充功能專案")](extensions-images/today04.png#lightbox)
5. 呼叫專案`DaysRemaining` ，然後按 [**下一步]** 按鈕： 

    [![](extensions-images/today05.png "呼叫專案 DaysRemaining，然後按 [下一步] 按鈕")](extensions-images/today05.png#lightbox)
6. 檢查項目，然後按一下 [**建立**] 按鈕以建立它： 

    [![](extensions-images/today06.png "檢查項目，然後按一下 [建立] 按鈕加以建立")](extensions-images/today06.png#lightbox)

產生的解決方案現在應該有兩個專案，如下所示：

[![](extensions-images/today07.png "產生的解決方案現在應該有兩個專案，如下所示")](extensions-images/today07.png#lightbox)

### <a name="creating-the-extension-user-interface"></a>建立擴充功能使用者介面

接下來，您必須為您的**今日**widget 設計介面。 這可以使用分鏡腳本來完成，或是在程式碼中建立 UI。 下面將詳細說明這兩種方法。

#### <a name="using-storyboards"></a>使用分鏡腳本

若要使用分鏡腳本建立 UI，請執行下列動作：

1. 在 **方案總管**中，按兩下擴充功能專案的`Main.storyboard`檔案以開啟它進行編輯： 

    [![](extensions-images/today08.png "按兩下延伸模組專案的主要分鏡腳本檔案，將其開啟以供編輯")](extensions-images/today08.png#lightbox)
2. 選取依範本自動新增至 UI 的標籤，並在 [**屬性] Explorer**的 [ **Widget** ] 索引標籤中指定其**名稱** `TodayMessage` ： 

    [![](extensions-images/today09.png "選取已自動加入至 UI 之範本的標籤，並給予其名稱 TodayMessage Widget 索引標籤的 [屬性總管] 中")](extensions-images/today09.png#lightbox)
3. 將變更儲存至分鏡腳本。

#### <a name="using-code"></a>使用程式碼

若要在程式碼中建立 UI，請執行下列動作： 

1. 在**方案總管 中**，選取**DaysRemaining**專案中，加入新的類別並呼叫它`CodeBasedViewController`: 

    [![](extensions-images/code01.png "Aelect DaysRemaining 專案、新增類別並呼叫它 CodeBasedViewController")](extensions-images/code01.png#lightbox)
2. 同樣地，在**方案總管**中，按兩下擴充功能的`Info.plist`檔案以開啟它進行編輯： 

    [![](extensions-images/code02.png "按兩下 [擴充功能] [plist 檔案]，將其開啟以供編輯")](extensions-images/code02.png#lightbox)
3. 選取**來源視圖**（從畫面底部）並開啟`NSExtension`節點： 

    [![](extensions-images/code03.png "從畫面底部選取來源視圖，然後開啟 [NSExtension] 節點")](extensions-images/code03.png#lightbox)
4. 移除機`NSExtensionPrincipalClass`碼，並新增具有下列值`CodeBasedViewController`的： `NSExtensionMainStoryboard` 

    [![](extensions-images/code04.png "移除 NSExtensionMainStoryboard 索引鍵，並使用值 CodeBasedViewController 新增 NSExtensionPrincipalClass")](extensions-images/code04.png#lightbox)
5. 儲存您的變更。

接著，編輯`CodeBasedViewController.cs`檔案，使其看起來如下所示：

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

請注意，會`NSExtensionPrincipalClass` 符合您為上述指定的值。`[Register("CodeBasedViewController")]`

### <a name="coding-the-extension"></a>撰寫延伸模組的程式碼

建立使用者介面後，開啟`TodayViewController.cs` `CodeBasedViewController.cs`或檔案（根據用來建立上述使用者介面的方法），變更**ViewDidLoad**方法，使其看起來如下所示：

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

如果使用以程式碼為基礎的使用者介面方法`// Insert code to power extension here...` ，請使用上述的新程式碼取代批註。 呼叫基底實作為（並插入程式碼型版本的標籤）之後，此程式碼會執行簡單的計算來取得一年中的日期，以及剩餘的天數。 然後，它會在您于 UI 設計`TodayMessage`中建立的標籤（）中顯示訊息。

請注意，此程式與撰寫應用程式的一般進程有何相似之處。 延伸`UIViewController`模組的生命週期與應用程式中的視圖控制器相同，但擴充功能沒有背景模式，而且在使用者完成使用時不會暫停。 相反地，延伸模組會依需要重複初始化和解除配置。

### <a name="creating-the-container-app-user-interface"></a>建立容器應用程式使用者介面

在此逐步解說中，容器應用程式只是用來做為送出和安裝延伸模組的方法，並不提供自己的功能。 編輯 TodayContainer 的`Main.storyboard`檔案，並新增一些定義延伸模組功能的文字，以及如何安裝它：

[![](extensions-images/today10.png "編輯 TodayContainers 的主要分鏡腳本檔案，並新增一些定義擴充功能函式及其安裝方式的文字")](extensions-images/today10.png#lightbox)

將變更儲存至分鏡腳本。

### <a name="testing-the-extension"></a>測試延伸模組

若要在 iOS 模擬器中測試您的擴充功能，請執行**TodayContainer**應用程式。 將會顯示容器的主視圖：

[![](extensions-images/run01.png "將會顯示 [容器] 主視圖")](extensions-images/run01.png#lightbox)

接下來，按下模擬器中的 [**首頁**] 按鈕，從畫面頂端向下滑動以開啟 [**通知中心**]，選取 [**今天**] 索引標籤，然後按一下 [**編輯**] 按鈕：

[![](extensions-images/run02.png "點擊模擬器中的 [首頁] 按鈕，從畫面頂端向下滑動以開啟 [通知中心]，選取 [今天] 索引標籤，然後按一下 [編輯] 按鈕")](extensions-images/run02.png#lightbox)

將 [ **DaysRemaining** ] 延伸模組新增至 [**今日**] 視圖，然後按一下 [**完成**] 按鈕：

[![](extensions-images/run03.png "將 [DaysRemaining] 延伸模組新增至 [今日] 視圖，然後按一下 [完成] 按鈕")](extensions-images/run03.png#lightbox)

新的 widget 會新增至 [**今日**] 視圖，並顯示結果：

[![](extensions-images/run04.png "新的 widget 會新增至 [今日] 視圖，而結果會顯示出來")](extensions-images/run04.png#lightbox)

## <a name="communicating-with-the-host-app"></a>與主機應用程式通訊

您在上面建立的「今日」範例不會與其主機應用程式通訊（[**今天**] 畫面）。 如果已執行，則會使用`TodayViewController`或`CodeBasedViewController`類別的[ExtensionCoNtext](xref:Foundation.NSExtensionContext)屬性。 

對於將從其主機應用程式接收資料的擴充功能，資料是以[NSExtensionItem](xref:Foundation.NSExtensionItem)物件陣列的形式儲存在延伸`UIViewController`模組之[ExtensionCoNtext](xref:Foundation.NSExtensionContext)的[InputItems](xref:Foundation.NSExtensionContext.InputItems)屬性中。

其他延伸模組，例如相片編輯延伸模組，可以區別使用者完成或取消使用方式。 這會透過[ExtensionCoNtext](xref:Foundation.NSExtensionContext)屬性的[CompleteRequest](xref:Foundation.NSExtensionContext.CompleteRequest*)和[CancelRequest](xref:Foundation.NSExtensionContext.CancelRequest*)方法，向主機應用程式發出信號。

如需詳細資訊，請參閱 Apple 的[應用程式擴充程式設計指南](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1)。

## <a name="communicating-with-the-parent-app"></a>與父應用程式通訊

「應用程式群組」可讓不同的應用程式 (或應用程式及其擴充功能) 存取共用檔案儲存體位置。 「應用程式群組」可用於資料下列資料：

- [Apple Watch 設定](~/ios/watchos/app-fundamentals/settings.md)。
- [共用使用 nsuserdefaults](~/ios/app-fundamentals/user-defaults.md)。
- [共用](~/ios/watchos/app-fundamentals/parent-app.md#files)檔案。

如需詳細資訊，請參閱**使用功能**檔的[應用程式群組](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)一節。

## <a name="mobilecoreservices"></a>MobileCoreServices

使用延伸模組時，請使用「統一類型識別碼」（UTI）來建立和操作在應用程式、其他應用程式和/或服務之間交換的資料。

靜態類別會定義下列與`kUTType...` Apple 定義相關的 helper 屬性： `MobileCoreServices.UTType`

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

請參閱下列範例:

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

如需詳細資訊，請參閱**使用功能**檔的[應用程式群組](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)一節。

## <a name="precautions-and-considerations"></a>預防措施與考慮

擴充功能具有比應用程式更少的可用記憶體。 它們預期會快速執行，並對使用者和裝載的應用程式進行最少入侵。 不過，延伸模組也應該為取用應用程式提供特殊、實用的函式，並搭配有品牌的 UI，讓使用者能夠識別其所屬的延伸模組開發人員或容器應用程式。

基於這些嚴格的需求，您應該只部署已徹底測試並針對效能和記憶體耗用量優化的延伸模組。 

## <a name="summary"></a>總結

本檔涵蓋延伸模組、其功能、擴充點類型，以及由 iOS 所加諸的已知限制。 它討論了如何建立、散發、安裝和執行延伸模組，以及擴充功能的生命週期。 它提供了逐步解說，說明如何建立一個簡單的「**今日**」 widget，其中顯示使用分鏡腳本或程式碼建立 widget UI 的兩種方式。 它示範了如何在 iOS 模擬器中測試延伸模組。 最後，它會簡短討論與主機應用程式通訊，以及開發擴充功能時應採取的一些預防措施和考慮。 

## <a name="related-links"></a>相關連結

- [ContainerApp （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/intro-to-extensions)
- [在 Xamarin 中建立擴充功能（影片）](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
