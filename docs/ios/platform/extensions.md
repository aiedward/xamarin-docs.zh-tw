---
title: iOS 在 Xamarin.iOS 中的延伸模組
description: 本文件說明擴充功能，其為 iOS，例如通知中心內的標準內容中所顯示的小工具。 它討論如何建立擴充功能，並從父應用程式與其通訊。
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 25b27765a35310c5cdbaf5ae19902b1d19eff6ea
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61321908"
---
# <a name="ios-extensions-in-xamarinios"></a>在 Xamarin.iOS 中的 iOS 延伸模組

> [!VIDEO https://youtube.com/embed/Sd0-ch9Udmk]

**藉由建立延伸模組在 iOS 中， [Xamarin University](https://university.xamarin.com/)**

專門擴充功能，為 iOS 8 中引進`UIViewControllers`，會依標準的內容內的 iOS 滿足下列條件為內**通知中心**，如使用者所要求執行自訂的鍵盤類型特製化輸入或其他內容，例如編輯的相片的延伸模組可以在其中提供的特殊效果的篩選器。

所有擴充功能會隨搭配容器應用程式 （使用 64 位元 Unified Api 所撰寫的這兩個項目），並從特定的擴充點，在主機應用程式會啟動。 而且，因為它們會使用現有的系統函數的增補，必須是精簡，且功能強大的高效能。 

## <a name="extension-points"></a>擴充點

|類型|描述|擴充點|主機應用程式|
|--- |--- |--- |--- |
|動作|特製化的編輯器或特定媒體類型的檢視器|`com.apple.ui-services`|任何|
|文件提供者|可讓應用程式以使用遠端的文件存放區|`com.apple.fileprovider-ui`|使用應用程式[UIDocumentPickerViewController](xref:UIKit.UIDocumentPickerViewController)|
|鍵盤|替代鍵盤|`com.apple.keyboard-service`|任何|
|相片編輯|相片操作和編輯|`com.apple.photo-editing`|Photos.app 編輯器|
|共用|共用資料與傳訊服務等社交網路。|`com.apple.share-services`|任何|
|今天|通知中心的 [Today] 畫面上顯示 「 widget 」|`com.apple.widget-extensions`|今天和通知中心|

[其他擴充點](~/ios/platform/introduction-to-ios10/index.md#app-extensions)已新增 ios 10。

## <a name="limitations"></a>限制

延伸模組有幾項限制，其中有些是通用所有類型 （如執行個體，任何類型的延伸模組可以存取的相機或麥克風） 而其他類型的擴充功能可能會有特定限制，其使用方式 （例如自訂鍵盤不能用於保護資料輸入欄位，例如密碼）。 

通用的限制如下：

- [健全狀況 Kit](~/ios/platform/healthkit.md)並[事件套件 UI](~/ios/platform/eventkit.md)架構不提供
- 擴充功能無法使用[擴充背景模式](https://developer.xamarin.com/guides/cross-platform/application_fundamentals/backgrounding/part_3_ios_backgrounding_techniques/registering_applications_to_run_in_background/)
- 延伸模組無法存取裝置的相機或麥克風，（雖然他們可以存取現有的媒體檔案）
- 延伸模組無法接收空氣卸除的資料 （雖然它們可以傳輸透過無線方式卸除的資料）
- [UIActionSheet](xref:UIKit.UIActionSheet)並[UIAlertView](xref:UIKit.UIAlertView)無法使用; 延伸模組必須使用[UIAlertController](xref:UIKit.UIAlertController)
- 數個成員[uiapplication #](xref:UIKit.UIApplication)無法使用：[UIApplication.SharedApplication](xref:UIKit.UIApplication.SharedApplication)， [UIApplication.OpenUrl](xref:UIKit.UIApplication.OpenUrl(Foundation.NSUrl))， [UIApplication.BeginIgnoringInteractionEvents](xref:UIKit.UIApplication.BeginIgnoringInteractionEvents)和[UIApplication.EndIgnoringInteractionEvents](xref:UIKit.UIApplication.EndIgnoringInteractionEvents)
- iOS 會強制執行今天的延伸模組的 16 MB 記憶體使用量限制。
- 預設鍵盤擴充功能不需要存取網路。 這會影響偵錯在裝置上 （這項限制不會強制執行在模擬器中），因為 Xamarin.iOS 進行偵錯運作需要網路存取。 就可以要求網路存取設定`Requests Open Access`至專案的 Info.plist 中的值`Yes`。 請參閱 Apple[自訂鍵盤指南](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html)如需鍵盤擴充功能限制的詳細資訊。

如需個別的限制，請參閱 Apple[應用程式擴充功能程式設計指南](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/)。

## <a name="distributing-installing-and-running-extensions"></a>散發、 安裝及執行擴充功能

延伸模組會從散發內這，接著提交並透過 App Store 散發的容器應用程式。 分散式應用程式延伸模組會安裝到那時，但使用者必須明確啟用每個延伸模組。 不同類型的延伸模組會啟用以不同的方式;數個會要求使用者瀏覽至**設定**應用程式，並從該處啟用它們。 雖然其他人會啟用在時間點的使用，例如傳送相片時，啟用共用延伸模組。 

應用程式中使用擴充功能 （在使用者遇到擴充點） 指**主機應用程式**，因為它是裝載擴充功能時，它會執行的應用程式。 安裝擴充功能的應用程式**容器應用程式**，因為它是安裝時，所包含的擴充功能的應用程式。  

通常，容器應用程式說明擴充功能，並逐步完成啟用程序的使用者。

## <a name="extension-lifecycle"></a>延伸模組的生命週期

延伸模組可以非常簡單，為單一[UIViewController](xref:UIKit.UIViewController)或更複雜的延伸模組，都沒有多個畫面的 UI。 當使用者遇到_擴充點_(例如當共用映像)，他們將有機會選擇註冊該擴充點的擴充功能。 

如果他們選擇其中一個應用程式的延伸模組，其`UIViewController`會具現化，並開始正常的檢視控制器生命週期。 不過，不同於一般的應用程式，這會暫停，但通常不會終止使用者完成與其進行互動時，延伸模組會載入、 執行和重複，然後終止。

擴充功能可以透過其主應用程式與通訊[NSExtensionContext](xref:Foundation.NSExtensionContext)物件。 有些延伸模組有接收結果的非同步回呼的作業。 這些回呼會在背景執行緒上執行和擴充功能必須將此列入考量。比方說，是藉由使用[NSObject.InvokeOnMainThread](xref:Foundation.NSObject.InvokeOnMainThread*)如果他們想要更新的使用者介面。 請參閱[主機應用程式與通訊](#communicating-with-the-host-app)節以取得詳細資料。

根據預設，擴充功能和其容器應用程式可以通訊，儘管一起安裝。 在某些情況下，容器應用程式本質上是空"shipping"容器安裝擴充功能之後，會提供其用途。 不過，如果指定的情況下，容器應用程式和延伸模組可能共用資源從常見的區域。 此外，**今天擴充功能**可能會要求它的容器應用程式，以開啟 URL。 此行為所示[發展倒數小工具](https://github.com/xamarin/monotouch-samples/tree/master/ExtensionsDemo)。

## <a name="creating-an-extension"></a>建立擴充功能

擴充功能 （和其容器應用程式） 必須是 64 位元二進位檔案，且使用 Xamarin.iOS 建置[Unified Api](https://developer.xamarin.com/guides/cross-platform/macios/unified)。 在開發擴充功能時，您的解決方案將包含至少兩個專案： 容器應用程式，另一個專案容器的每個擴充功能提供。 

### <a name="container-app-project-requirements"></a>容器應用程式的專案需求

用來安裝擴充功能的容器應用程式必須符合下列需求：

- 它必須維護延伸模組專案的參考。   
- 它必須是完整的應用程式 （必須是能夠啟動並順利執行），即使它沒有多個可用來安裝擴充功能。 
- 它必須是擴充功能的配套識別碼的基礎套件組合識別碼專案 （請參閱下的一節，如需詳細資訊）。

### <a name="extension-project-requirements"></a>延伸模組專案需求

此外，擴充功能的專案具有下列需求：

- 您必須以其容器應用程式套件組合識別碼開頭的套件組合識別碼。 例如，如果容器應用程式的套件組合識別碼`com.myCompany.ContainerApp`，可能的延伸模組識別碼`com.myCompany.ContainerApp.MyExtension`: 

    ![](extensions-images/bundleidentifiers.png) 
- 它必須定義索引鍵`NSExtensionPointIdentifier`，使用適當的值 (例如`com.apple.widget-extension`如**今天**通知中心小工具)，請在其`Info.plist`檔案。
- 它也必須定義*任一*`NSExtensionMainStoryboard`金鑰或`NSExtensionPrincipalClass`中的索引鍵其`Info.plist`檔案使用適當的值：
    - 使用`NSExtensionMainStoryboard`索引鍵，指定分鏡腳本所呈現的主要 UI 延伸模組的名稱 (減去`.storyboard`)。 例如，`Main`針對`Main.storyboard`檔案。
    - 使用`NSExtensionPrincipalClass`索引鍵來指定將在擴充功能啟動時初始化的類別。 此值必須符合**註冊**的值您`UIViewController`: 

    ![](extensions-images/registerandprincipalclass.png)

特定類型的延伸模組可能有其他需求。 比方說，**今天**或是**通知中心**延伸模組的主要類別必須實作[INCWidgetProviding](xref:NotificationCenter.INCWidgetProviding)。

> [!IMPORTANT]
> 如果您開始使用其中一個 Visual Studio for Mac 所提供的延伸模組範本的專案時，會提供大部分 （若非全部） 這些需求，並自動符合您的範本。

## <a name="walkthrough"></a>逐步解說 

在下列的逐步解說中，您將建立範例**今天**小工具會計算的日期和年份中的剩餘天數：

[![](extensions-images/carpediemscreenshot-sm.png "計算的日期和年份中的剩餘天數範例今天小工具")](extensions-images/carpediemscreenshot.png#lightbox)

### <a name="creating-the-solution"></a>建立解決方案

若要建立必要的方案，執行下列作業：

1. 首先，建立新的 iOS**單一檢視應用程式**專案，然後按一下**下一步**按鈕： 

    [![](extensions-images/today01.png "首先，建立新的 iOS、 單一檢視應用程式專案，然後按 [下一步] 按鈕")](extensions-images/today01.png#lightbox)
2. 呼叫專案`TodayContainer`，按一下 [**下一步]** 按鈕： 

    [![](extensions-images/today02.png "呼叫 TodayContainer 專案，然後按一下 [下一步] 按鈕")](extensions-images/today02.png#lightbox)
3. 請確認**專案名稱**並**SolutionName** ，按一下 **建立**按鈕，以建立方案： 

    [![](extensions-images/today03.png "確認 SolutionName 與專案名稱，然後按一下 [建立] 按鈕，以建立方案")](extensions-images/today03.png#lightbox)
4. 接下來，在**方案總管**，以滑鼠右鍵按一下方案，並加入新**iOS 延伸模組**從專案**今天擴充功能**範本： 

    [![](extensions-images/today04.png "接下來，在 [方案總管] 中，以滑鼠右鍵按一下方案，並從目前的延伸模組範本新增新的 iOS 延伸模組專案")](extensions-images/today04.png#lightbox)
5. 呼叫專案`DaysRemaining`，按一下 [**下一步]** 按鈕： 

    [![](extensions-images/today05.png "呼叫 DaysRemaining 專案，然後按一下 [下一步] 按鈕")](extensions-images/today05.png#lightbox)
6. 檢閱專案，然後按一下**建立** 按鈕來建立它： 

    [![](extensions-images/today06.png "檢閱專案，然後按一下 [建立] 按鈕來建立它")](extensions-images/today06.png#lightbox)

所產生的方案現在應該有兩個專案，如下所示：

[![](extensions-images/today07.png "所產生的方案現在應該有兩個專案，如下所示")](extensions-images/today07.png#lightbox)

### <a name="creating-the-extension-user-interface"></a>建立擴充功能使用者介面

接下來，您必須設計的介面您**今天**小工具。 可能是這可以使用分鏡腳本，或在程式碼中建立 UI。 這兩種方法將會詳細說明如下。

#### <a name="using-storyboards"></a>使用分鏡腳本

若要建置使用分鏡腳本 UI，執行下列作業：

1. 在 **方案總管 中**，連按兩下 擴充功能專案的`Main.storyboard`檔案，以開啟它進行編輯： 

    [![](extensions-images/today08.png "按兩下以開啟它進行編輯的延伸模組專案 Main.storyboard 檔案")](extensions-images/today08.png#lightbox)
2. 選取已自動加入至 UI 之範本的標籤，並為它提供**名稱**`TodayMessage`中**Widget**索引標籤**屬性總管**: 

    [![](extensions-images/today09.png "選取已自動加入至 UI 之範本的標籤，並給予其名稱 TodayMessage Widget 索引標籤的 [屬性總管] 中")](extensions-images/today09.png#lightbox)
3. 將變更儲存到分鏡腳本。

#### <a name="using-code"></a>使用程式碼

若要建置的 UI 程式碼中，執行下列作業： 

1. 在**方案總管 中**，選取**DaysRemaining**專案中，加入新的類別並呼叫它`CodeBasedViewController`: 

    [![](extensions-images/code01.png "Aelect DaysRemaining 專案中，加入新的類別，並取名為 CodeBasedViewController")](extensions-images/code01.png#lightbox)
2. 同樣地，在**方案總管**，連按兩下 延伸模組的`Info.plist`檔案，以開啟它進行編輯： 

    [![](extensions-images/code02.png "按兩下以開啟它進行編輯的延伸模組 Info.plist 檔案")](extensions-images/code02.png#lightbox)
3. 選取 **原始碼檢視**（從畫面底部），然後開啟`NSExtension`節點： 

    [![](extensions-images/code03.png "從畫面底部選取 [來源] 檢視，並開啟 NSExtension 節點")](extensions-images/code03.png#lightbox)
4. 移除`NSExtensionMainStoryboard`鍵，並新增`NSExtensionPrincipalClass`的值`CodeBasedViewController`: 

    [![](extensions-images/code04.png "移除 NSExtensionMainStoryboard 索引鍵，然後新增具有值 CodeBasedViewController NSExtensionPrincipalClass")](extensions-images/code04.png#lightbox)
5. 儲存您的變更。

接著，編輯`CodeBasedViewController.cs`檔案，並使它看起來如下：

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

請注意，`[Register("CodeBasedViewController")]`符合您指定的值`NSExtensionPrincipalClass`上方。

### <a name="coding-the-extension"></a>撰寫程式碼擴充功能

建立使用者介面後，開啟`TodayViewController.cs`或`CodeBasedViewController.cs`檔案 （用來建立上述的使用者介面的方法的基礎） 的變更**ViewDidLoad**方法，使它看起來如下所示：

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

如果使用程式碼型使用者介面的方法，取代`// Insert code to power extension here...`與新的程式碼，從上方的註解。 之後呼叫的基底實作 （和插入的程式碼架構版本的標籤），此程式碼會執行簡單的計算，若要取得的年份和剩餘多少天的日期。 然後它會在標籤中顯示的訊息 (`TodayMessage`) 您在 UI 設計中建立。

請注意如何類似此程序是撰寫應用程式的一般程序。 延伸模組的`UIViewController`在應用程式中，具有相同的生命週期，為檢視控制器，但延伸模組沒有背景模式，而且不會暫停，當使用者已完成使用它們。 相反地，擴充功能會重複地初始化，並解除配置所需。

### <a name="creating-the-container-app-user-interface"></a>建立容器應用程式使用者介面

此逐步解說中，容器應用程式只做為方法用來交付及安裝擴充功能，並不提供其本身的任何功能。 編輯 TodayContainer`Main.storyboard`檔案，並加入一些文字定義延伸模組的函式，以及如何加以安裝：

[![](extensions-images/today10.png "編輯 TodayContainers Main.storyboard 檔案，並加入一些文字定義的延伸模組函式，以及如何將它安裝")](extensions-images/today10.png#lightbox)

將變更儲存到分鏡腳本。

### <a name="testing-the-extension"></a>測試延伸模組

若要在 iOS 模擬器中測試您的延伸模組，請執行**TodayContainer**應用程式。 容器的主要檢視會顯示：

[![](extensions-images/run01.png "主要檢視會顯示容器")](extensions-images/run01.png#lightbox)

接下來，點擊**首頁**按鈕，在模擬器中，從開啟畫面頂端向下撥動**通知中心**，選取**今天**索引標籤，然後按一下**編輯**按鈕：

[![](extensions-images/run02.png "叫用在模擬器中，向下撥動以開啟通知中心，選取 [今天] 索引標籤，然後按一下 [編輯] 按鈕螢幕頂端的 [首頁] 按鈕")](extensions-images/run02.png#lightbox)

新增**DaysRemaining**延伸模組**今天**檢視，然後按一下**完成**按鈕：

[![](extensions-images/run03.png "今日檢視新增 DaysRemaining 延伸模組，然後按一下 [完成] 按鈕")](extensions-images/run03.png#lightbox)

將會加入新的 widget**今天**檢視結果才會顯示：

[![](extensions-images/run04.png "新增小工具會加入 [今日] 檢視，而且會顯示結果")](extensions-images/run04.png#lightbox)

## <a name="communicating-with-the-host-app"></a>與主應用程式通訊

今天您先前建立的延伸模組的範例不會和其主機應用程式的通訊 (**今天**螢幕)。 如果有，它會使用[ExtensionContext](xref:Foundation.NSExtensionContext)屬性`TodayViewController`或`CodeBasedViewController`類別。 

會從其主機應用程式接收資料的擴充功能，資料會位於陣列的形式[NSExtensionItem](xref:Foundation.NSExtensionItem)中儲存的物件[InputItems](xref:Foundation.NSExtensionContext.InputItems)屬性[ExtensionContext](xref:Foundation.NSExtensionContext)延伸模組的`UIViewController`。

其他擴充功能，例如相片編輯擴充功能，可能會區分使用者完成或取消使用方式。 這將會收到信號給主機應用程式，透過[CompleteRequest](xref:Foundation.NSExtensionContext.CompleteRequest*)並[CancelRequest](xref:Foundation.NSExtensionContext.CancelRequest*)方法[ExtensionContext](xref:Foundation.NSExtensionContext)屬性。

如需詳細資訊，請參閱 Apple[應用程式擴充功能程式設計指南](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1)。

## <a name="communicating-with-the-parent-app"></a>父應用程式與通訊

「應用程式群組」可讓不同的應用程式 (或應用程式及其擴充功能) 存取共用檔案儲存體位置。 「應用程式群組」可用於資料下列資料：

- [Apple Watch 設定](~/ios/watchos/app-fundamentals/settings.md)。
- [共用 NSUserDefaults](~/ios/app-fundamentals/user-defaults.md)。
- [共用檔案](~/ios/watchos/app-fundamentals/parent-app.md#files)。

如需詳細資訊，請參閱[應用程式群組](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)一節我們**使用功能**文件。

## <a name="mobilecoreservices"></a>MobileCoreServices

當使用延伸模組，請使用統一的型別識別項 (UTI) 來建立及管理應用程式、 其他應用程式和/或服務之間交換的資料。

`MobileCoreServices.UTType`靜態類別會定義下列協助程式屬性與 Apple 的`kUTType...`定義：

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

下列範例，請參閱：

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

如需詳細資訊，請參閱[應用程式群組](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)一節我們**使用功能**文件。

## <a name="precautions-and-considerations"></a>預防措施和考量

延伸模組有提供給他們的應用程式要比更少記憶體。 它們應該執行快速並給使用者，與裝載於應用程式的最小入侵。 不過，擴充功能也應該提供特殊且實用的函式，加上品牌的 ui 可讓使用者識別擴充功能的開發人員使用的應用程式或其所屬的容器應用程式。

指定這些緊密的需求，您應該只部署已徹底測試並最佳化效能和記憶體耗用量的延伸模組。 

## <a name="summary"></a>總結

本文件涵蓋延伸模組、 認證、 有什麼類型的擴充點以及 iOS 所加諸於延伸模組的已知的限制。 它討論建立、 散發、 安裝和執行擴充功能和擴充生命週期。 它提供建立簡單的逐步解說**今天**widget，顯示兩種方式可以建立小工具的 UI 使用分鏡腳本] 或 [程式碼。 它示範了如何在 iOS 模擬器中測試延伸模組。 最後，它簡短討論與主機應用程式和一些預防措施和開發擴充功能時應採取的考量進行通訊。 

## <a name="related-links"></a>相關連結

- [ContainerApp （範例）](https://developer.xamarin.com/samples/monotouch/intro-to-extensions)
- [建立在 Xamarin.iOS 中的擴充功能 （影片）](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
