---
title: iOS 擴充功能
description: IOS 8 中引進，擴充功能所帶來的 iOS 標準的內容，例如在通知中心內，當使用者要求自訂的鍵盤，或當相片的 widget 編輯。 所有擴充功能會安裝在搭配容器應用程式，並從特定的擴充點，在主機應用程式會啟動。
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
ms.custom: xamu-video
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: af52db5f1add040af025f2134f0e9a7b3936f4f2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="ios-extensions"></a>iOS 擴充功能

_IOS 8 中引進，擴充功能所帶來的 iOS 標準的內容，例如在通知中心內，當使用者要求自訂的鍵盤，或當相片的 widget 編輯。所有擴充功能會安裝在搭配容器應用程式，並從特定的擴充點，在主機應用程式會啟動。_

> [!VIDEO https://youtube.com/embed/Sd0-ch9Udmk]

**藉由在 iOS 中，建立擴充功能[Xamarin 大學](https://university.xamarin.com/)**

擴充功能，推出 iOS 8，進行特製化`UIViewControllers`所帶來的標準內容內的 iOS 例如中**通知中心**，如要求使用者所執行的自訂鍵盤類型特製化輸入或其他內容，例如編輯的相片，擴充功能可以提供特殊效果的篩選器的位置。

所有擴充功能會隨搭配容器應用程式 （使用 64 位元統一的 Api 撰寫兩個項目），並從特定的擴充點，在主機應用程式完成啟用。 因為它們會使用現有的系統函數的增補，它們必須是 「 精實 」，且功能強大的高效能。 

本文涵蓋下列主題：

- [擴充點](#Extension-Points)-列出可用的擴充點的類型和可由每個點的延伸的型別。
- [限制](#Limitations)-延伸模組有許多限制，其中有些是所有類型通用的雖然其他類型的擴充功能可能會有特定限制，其使用方式。
- [將、 安裝、 以及執行擴充功能](#Distributing-Installing-and-Running-Extensions)-擴充功能會從容器應用程式，其，依次送出，並透過應用程式市集散發中散發。 此時，安裝應用程式一起散發的擴充功能，但使用者必須明確啟用每個擴充功能。 不同類型的擴充功能會啟用以不同的方式。
- [延伸模組的生命週期](#Extension-Lifecycle)-的擴充功能的`UIViewController`會具現化，並開始正常檢視控制器生命週期。 不過，不同於一般的應用程式中，延伸模組會載入、 執行和終止重複。
- [建立擴充](#Creating-an-Extension)-開發擴充功能，當您的方案會包含至少兩個專案： 容器應用程式，另一個專案用於容器的每個擴充功能提供。
- [逐步解說](#Walkthrough)-涵蓋如何建立簡單**今天**小工具擴充功能，提供其使用者介面使用分鏡腳本或程式碼中，安裝此擴充功能，並在 iOS 模擬器中測試。
- [主機應用程式與通訊](#Communicating-with-the-Host-App)-簡要討論主機應用程式擴充功能與通訊。
- [父應用程式與通訊](#Communicating-with-the-Parent-App)-簡要討論與延伸父應用程式進行通訊。
- [預防措施和考量](#Precautions-and-Considerations)-某些知道預防措施，以及應該列入考量，設計和實作的擴充功能時的考量的清單。
 

<a name="Extension-Points" />

## <a name="extension-points"></a>擴充點

|類型|描述|擴充點|主機應用程式|
|--- |--- |--- |--- |
|動作|特殊的編輯器或特定媒體類型的檢視器|`com.apple.ui-services`|任何|
|文件提供者|允許應用程式使用遠端的文件存放區|`com.apple.fileprovider-ui`|使用應用程式[UIDocumentPickerViewController](https://developer.xamarin.com/api/type/UIKit.UIDocumentPickerViewController/)|
|鍵盤|替代鍵盤|`com.apple.keyboard-service`|任何|
|編輯相片|相片管理和編輯|`com.apple.photo-editing`|Photos.app 編輯器|
|共用|共用與社交網路、 訊息處理服務等的資料。|`com.apple.share-services`|任何|
|今天|今天螢幕或通知中心上會出現 「 小工具 」|`com.apple.widget-extensions`|今天和通知中心|

[其他擴充點](~/ios/platform/introduction-to-ios10/index.md#app-extensions)iOS 10 中已加入。

<a name="Limitations" />

## <a name="limitations"></a>限制

延伸模組有許多限制，其中有些是通用所有類型 （如執行個體，再使用類型的延伸模組可以存取的相機或麥克風） 雖然其他類型的擴充功能可能會有特定限制，其使用方式 （例如，自訂鍵盤無法用於保護資料輸入欄位，例如密碼）。 

通用的限制如下：

- [健全狀況套件](~/ios/platform/healthkit.md)和[事件套件 UI](~/ios/platform/eventkit.md)架構皆不是使用
- 擴充功能無法使用[擴充背景模式](http://developer.xamarin.com/guides/cross-platform/application_fundamentals/backgrounding/part_3_ios_backgrounding_techniques/registering_applications_to_run_in_background/)
- 擴充功能無法存取裝置的相機或麥克風，（雖然它們可能會存取現有的媒體檔案）
- 擴充功能無法接收空中卸除的資料 （雖然它們可以傳輸透過無線方式卸除的資料）
- [UIActionSheet](https://developer.xamarin.com/api/type/UIKit.UIActionSheet/)和[UIAlertView](https://developer.xamarin.com/api/type/UIKit.UIAlertView/)無法使用; 必須使用延伸模組[UIAlertController](https://developer.xamarin.com/api/type/UIKit.UIAlertController/)
- 數個成員[uiapplication #](https://developer.xamarin.com/api/type/UIKit.UIApplication/)無法使用： [UIApplication.SharedApplication](https://developer.xamarin.com/api/property/UIKit.UIApplication.SharedApplication/)， `UIApplication.OpenURL`，`UIApplication.BeginIgnoringInteractionEvents`和 `UIApplication.EndIgnoringInteractionEvents`
- iOS 會強制執行今天的擴充功能上的 16 MB 記憶體使用限制。
- 根據預設鍵盤擴充功能不需要存取網路。 這會影響偵錯在裝置上 （這項限制不會強制執行在模擬器中），因為 Xamarin.iOS 需要網路存取偵錯才能運作。 您可要求網路存取設定`Requests Open Access`中專案的 Info.plist，以值`Yes`。 請參閱 Apple[自訂鍵盤指南](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html)如需有關鍵盤延伸限制。

如需個別的限制，請參閱 Apple[應用程式擴充功能的程式設計指南](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/)。

<a name="Distributing-Installing-and-Running-Extensions" />

## <a name="distributing-installing-and-running-extensions"></a>散發、 安裝和執行擴充功能

延伸模組會從散發容器應用程式，而，後者是提交和分散式透過應用程式存放區中。 此時，安裝應用程式一起散發的擴充功能，但使用者必須明確啟用每個擴充功能。 不同類型的擴充功能會啟用以不同的方式;數個需要使用者瀏覽至**設定**應用程式，並讓它們從該處。 其他人會在時間點的使用，例如傳送相片時，啟用共用擴充功能啟用。 

應用程式中使用延伸模組 （使用者遇到擴充點） 指**主機應用程式**，因為它是裝載在執行時的擴充功能的應用程式。 安裝擴充功能的應用程式已**容器應用程式**，因為它包含在安裝時的擴充功能的應用程式。  

一般而言，容器應用程式描述擴充功能，並會引導使用者進行啟用它的程序。

<a name="Extension-Lifecycle" />

## <a name="extension-lifecycle"></a>延伸模組的生命週期

擴充功能可以為單一一樣簡單[UIViewController](https://developer.xamarin.com/api/type/UIKit.UIViewController/)或更複雜的延伸模組，都沒有多個螢幕的 UI。 當使用者遇到_擴充點_(例如當共用映像)，它們將會有機會選擇從註冊的擴充點延伸。 

如果他們選擇其中一個應用程式的延伸模組，其`UIViewController`會具現化，並開始正常檢視控制器生命週期。 不過，不同於標準的應用程式，其中已暫停，但通常不會終止使用者完成與其互動時，延伸模組會載入、 執行和終止重複。

擴充功能可以透過其主機應用程式與通訊[NSExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/)物件。 一些擴充功能有接收結果的非同步回呼的作業。 會在背景執行緒上執行這些回呼和擴充功能必須將這點納入考量。比方說，藉由[NSObject.InvokeOnMainThread](https://developer.xamarin.com/api/member/Foundation.NSObject.InvokeOnMainThread/)如果他們想要更新的使用者介面。 請參閱[主機應用程式與通訊](#Communicating-with-the-Host-App)下面章節以取得詳細資料。

根據預設，擴充功能和其容器應用程式可以通訊，儘管一起安裝。 在某些情況下，容器應用程式本質上是空 「 運送 」 容器的目的提供安裝擴充功能之後。 不過，如果指定的情況下，容器應用程式和延伸模組可能會共用資源的一般區域。 此外，**今天延伸**可能會要求其容器應用程式中開啟 URL。 這種行為如下所示[發展倒數計時 Widget](http://github.com/xamarin/monotouch-samples/tree/master/ExtensionsDemo)。

<a name="Creating-an-Extension" />

## <a name="creating-an-extension"></a>建立擴充功能

擴充功能 （和其容器應用程式） 必須是 64 位元二進位檔案，並使用 Xamarin.iOS 建置[統一的 Api](http://developer.xamarin.com/guides/cross-platform/macios/unified)。 當開發擴充功能，您的方案將會包含至少兩個專案： 容器應用程式，另一個專案用於容器的每個擴充功能提供。 

<a name="Container-App-Project-Requirements" />

### <a name="container-app-project-requirements"></a>容器應用程式專案的需求

容器應用程式用來安裝擴充功能具有下列需求：

- 它必須維護擴充功能專案中的參考。   
- 它必須是完整的應用程式 （必須先啟動並執行成功），即使它不做任何動作超過提供的方式來安裝擴充功能。 
- 它必須是擴充功能的配套識別碼為基礎的配套識別碼專案 （請參閱下的一節，如需詳細資訊）。

<a name="Extension-Project-Requirements" />

### <a name="extension-project-requirements"></a>擴充專案需求

此外，擴充功能的專案具有下列需求：

- 它必須以其容器應用程式配套識別碼開頭的配套識別碼。 例如，如果容器的應用程式的配套識別碼`com.myCompany.ContainerApp`，可能會擴充功能的識別碼`com.myCompany.ContainerApp.MyExtension`: 

    ![](extensions-images/bundleidentifiers.png) 
- 它必須定義索引鍵`NSExtensionPointIdentifier`，使用適當的值 (例如`com.apple.widget-extension`如**今天**通知中心 widget)，請在其`Info.plist`檔案。
- 它也必須定義*任一*`NSExtensionMainStoryboard`索引鍵或`NSPrincipalClass`索引鍵中其`Info.plist`檔案具有適當的值：
    - 使用`NSExtensionMainStoryboard`鍵，以指定的分鏡腳本所呈現的主要 UI 延伸模組名稱 (減去`.storyboard`)。 例如，`Main`如`Main.storyboard`檔案。
    - 使用`NSPrincipalClass`鍵，以指定之類別的擴充功能啟動時將會初始化。 值必須符合**註冊**值您`UIViewController`: 

    ![](extensions-images/registerandprincipalclass.png)

特定類型的擴充功能可能有其他需求。 比方說，**今天**或**通知中心**擴充功能的主要類別必須實作[INCWidgetProviding](https://developer.xamarin.com/api/type/NotificationCenter.INCWidgetProviding/)。

> [!IMPORTANT]
> 如果您開始使用其中一個 Visual Studio 所提供適用於 Mac 的延伸模組範本專案，將提供大多數 （若非全部） 這些需求，並為您自動符合範本。

<a name="Walkthrough" />

## <a name="walkthrough"></a>逐步解說 

在下列逐步解說中，您將建立範例**今天**計算的日期和年份中的剩餘天數的 widget:

[![](extensions-images/carpediemscreenshot-sm.png "計算的日期和年份中的剩餘天數範例今天 widget")](extensions-images/carpediemscreenshot.png#lightbox)

<a name="Creating-the-Solution" />

### <a name="creating-the-solution"></a>建立方案

若要建立必要的方案，執行下列作業：

1. 首先，建立新的 iOS**單一檢視應用程式**專案，然後按一下**下一步**按鈕： 

    [![](extensions-images/today01.png "首先，建立新的 iOS、 單一檢視應用程式專案，然後按一下 [下一步] 按鈕")](extensions-images/today01.png#lightbox)
2. 呼叫專案`TodayContainer`按一下**下一步**按鈕： 

    [![](extensions-images/today02.png "呼叫專案 TodayContainer 並按一下 [下一步] 按鈕")](extensions-images/today02.png#lightbox)
3. 確認**專案名稱**和**SolutionName**按一下**建立**按鈕以建立方案： 

    [![](extensions-images/today03.png "請確認 SolutionName 與專案名稱，然後按一下 [建立] 按鈕，即可建立方案")](extensions-images/today03.png#lightbox)
4. 接下來，在**方案總管 中**，以滑鼠右鍵按一下方案，並加入新**iOS 延伸**從專案**今天延伸**範本： 

    [![](extensions-images/today04.png "接下來，在 [方案總管] 中，以滑鼠右鍵按一下方案，並從今天延伸範本加入新的 iOS 擴充功能專案")](extensions-images/today04.png#lightbox)
5. 呼叫專案`DaysRemaining`按一下**下一步**按鈕： 

    [![](extensions-images/today05.png "呼叫專案 DaysRemaining 並按一下 [下一步] 按鈕")](extensions-images/today05.png#lightbox)
6. 檢閱專案，然後按一下**建立** 按鈕來建立它： 

    [![](extensions-images/today06.png "檢閱專案，然後按一下 [建立] 按鈕，建立")](extensions-images/today06.png#lightbox)

所產生的解決方案現在應該有兩個專案，如下所示：

[![](extensions-images/today07.png "所產生的解決方案現在應該會有兩個專案，如下所示")](extensions-images/today07.png#lightbox)

<a name="Creating-the-Extension-User-Interface" />

### <a name="creating-the-extension-user-interface"></a>建立擴充功能使用者介面

接下來，您必須設計介面您**今天**widget。 這可能是可以使用分鏡腳本，或透過程式碼中建立 UI。 在詳細資料，將以下涵蓋這兩種方法。

<a name="Using-Storyboards" />

#### <a name="using-storyboards"></a>使用分鏡腳本

若要建置使用分鏡腳本 UI，執行下列作業：

1. 在**方案總管] 中**，連按兩下 [擴充功能專案`Main.storyboard`檔案，以開啟它進行編輯： 

    [![](extensions-images/today08.png "按兩下要開啟它進行編輯的延伸模組專案 Main.storyboard 檔案")](extensions-images/today08.png#lightbox)
2. 選取已自動加入至 UI 之範本的標籤，並賦予**名稱**`TodayMessage`中**Widget**  索引標籤**屬性總管**: 

    [![](extensions-images/today09.png "選取已自動加入至 UI 之範本的標籤，並給予其名稱 TodayMessage Widget 索引標籤的 [屬性總管] 中")](extensions-images/today09.png#lightbox)
3. 將變更儲存到分鏡腳本。

<a name="Using-Code" />

#### <a name="using-code"></a>使用程式碼

若要建置程式碼中的 UI，執行下列作業： 

1. 在**方案總管 中**，選取**DaysRemaining**專案中，加入新的類別並呼叫它`CodeBasedViewController`: 

    [![](extensions-images/code01.png "Aelect DaysRemaining 專案中，加入新的類別並呼叫它 CodeBasedViewController")](extensions-images/code01.png#lightbox)
2. 同樣地，在**方案總管] 中**，連按兩下 [擴充功能的`Info.plist`檔案，以開啟它進行編輯： 

    [![](extensions-images/code02.png "按兩下以開啟檔案進行編輯的擴充功能的 Info.plist 檔案")](extensions-images/code02.png#lightbox)
3. 選取**來源檢視**（從畫面底部），然後開啟`NSExtension`節點： 

    [![](extensions-images/code03.png "從畫面底部選取來源檢視，然後開啟 NSExtension 節點")](extensions-images/code03.png#lightbox)
4. 移除`NSExtensionMainStoryboard`索引鍵，並新增`NSPrincipalClass`值`CodeBasedViewController`: 

    [![](extensions-images/code04.png "移除 NSExtensionMainStoryboard 金鑰，並加入值 CodeBasedViewController NSPrincipalClass")](extensions-images/code04.png#lightbox)
5. 儲存您的變更。

接著，編輯`CodeBasedViewController.cs`檔案，並讓它看起來如下所示：

```csharp
using System;
using Foundation;
using UIKit;
using NotificationCenter;
using CoreGraphics;

namespace DaysRemaining
{
    [Register("CodeBasedViewContoller")]
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

請注意，`[Register("CodeBasedViewContoller")]`符合您為指定的值`NSPrincipalClass`上方。

<a name="Coding-the-Extension" />

### <a name="coding-the-extension"></a>撰寫程式碼擴充功能

所建立的使用者介面，以開啟 `TodayViewController.cs`或`CodeBasedViewController.cs`檔案 （用來建立上述使用者介面的方法的基礎） 的變更**ViewDidLoad**方法並看起來如下所示：

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

如果使用的程式碼使用者介面的方法，取代`// Insert code to power extension here...`從上方的新程式碼註解。 之後呼叫的基底實作 （和插入程式碼為主版本的標籤），此程式碼執行簡單的計算，若要取得的年份和還剩下多少天後的天數。 然後它會在標籤中顯示的訊息 (`TodayMessage`) UI 設計中所建立。

請注意，類似這個程序是撰寫應用程式的一般程序。 擴充功能的`UIViewController`除了延伸模組並沒有背景模式並不會暫停，當使用者完成，已為檢視控制站相同的生命週期，應用程式中使用它們。 相反地，擴充功能會重複初始化，並視需要取消配置。

<a name="Creating-the-Container-App-User-Interface" />

### <a name="creating-the-container-app-user-interface"></a>建立容器應用程式使用者介面

這個逐步解說中，容器應用程式只用於做為方法出貨，而且安裝擴充功能，並不提供它自己的任何功能。 編輯 TodayContainer`Main.storyboard`檔案，並加入一些文字定義的延伸模組的函式，以及如何安裝它：

[![](extensions-images/today10.png "編輯 TodayContainers Main.storyboard 檔案，並加入一些文字定義延伸模組函式和安裝方法")](extensions-images/today10.png#lightbox)

將變更儲存到分鏡腳本。

<a name="Testing-the-Extension" />

### <a name="testing-the-extension"></a>測試擴充功能

若要在 iOS 模擬器中測試您的擴充功能，請執行**TodayContainer**應用程式。 容器的主要檢視會顯示：

[![](extensions-images/run01.png "將會顯示主要檢視容器")](extensions-images/run01.png#lightbox)

接下來，叫用**首頁**按鈕在模擬器中，從開啟畫面頂端向下撥動**通知中心**，選取**今天**索引標籤上，按一下  **編輯**按鈕：

[![](extensions-images/run02.png "叫用 [首頁] 按鈕，在模擬器中，從頂端畫面開啟 [通知中心] 選取 [今日] 索引標籤，按一下 [編輯] 按鈕的向下撥動")](extensions-images/run02.png#lightbox)

新增**DaysRemaining**延伸**今天**檢視，並按一下**完成**按鈕：

[![](extensions-images/run03.png "DaysRemaining 延伸模組加入今日檢視，然後按一下 [完成] 按鈕")](extensions-images/run03.png#lightbox)

將加入新的 widget**今天**檢視結果才會顯示：

[![](extensions-images/run04.png "新的 widget 會加入今日檢視，並且會顯示結果")](extensions-images/run04.png#lightbox)

<a name="Communicating-with-the-Host-App" />

## <a name="communicating-with-the-host-app"></a>主機應用程式與通訊

範例今天先前建立的延伸模組不會與其主機應用程式通訊 (**今天**螢幕)。 如果該參數，它會使用[ExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/)屬性`TodayViewController`或`CodeBasedViewController`類別。 

針對會從其主機應用程式接收資料的擴充功能，資料是陣列的形式[NSExtensionItem](https://developer.xamarin.com/api/type/Foundation.NSExtensionItem/)物件儲存在[InputItems](https://developer.xamarin.com/api/property/Foundation.NSExtensionContext.InputItems/)屬性[ExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/)的擴充功能的`UIViewController`。

其他副檔名，例如相片編輯擴充功能可能會區分使用者完成或取消使用方式。 這將會收到信號回主機應用程式透過[CompleteRequest](https://developer.xamarin.com/api/member/Foundation.NSExtensionContext.CompleteRequest/)和[CancelRequest](https://developer.xamarin.com/api/member/Foundation.NSExtensionContext.CancelRequest/)方法[ExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/)屬性。

如需詳細資訊，請參閱 Apple[應用程式擴充功能的程式設計指南](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1)。

<a name="Communicating-with-the-Parent-App" />

## <a name="communicating-with-the-parent-app"></a>父應用程式與通訊

「應用程式群組」可讓不同的應用程式 (或應用程式及其擴充功能) 存取共用檔案儲存體位置。 「應用程式群組」可用於資料下列資料：

- [Apple Watch 設定](~/ios/watchos/app-fundamentals/settings.md)。
- [共用 NSUserDefaults](~/ios/app-fundamentals/user-defaults.md)。
- [共用檔案](~/ios/watchos/app-fundamentals/parent-app.md#files)。

如需詳細資訊，請參閱[應用程式群組](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)區段我們**功能使用**文件。

<a name="MobileCoreServices" />

## <a name="mobilecoreservices"></a>MobileCoreServices

當使用擴充功能，使用統一類型識別項 (UTI) 來建立及管理應用程式、 其他應用程式和/或服務之間交換的資料。

`MobileCoreServices.UTType`靜態類別會定義下列協助程式屬性，與 Apple`kUTType...`定義：

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

如需詳細資訊，請參閱[應用程式群組](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)區段我們**功能使用**文件。


<a name="Precautions-and-Considerations" />

## <a name="precautions-and-considerations"></a>預防措施和考量

延伸模組包含極少記憶體提供給他們的應用程式一樣。 它們應該執行快速且最少使用者和應用程式裝載於入侵。 不過，擴充功能也應該提供具有加上品牌的 UI，讓使用者識別擴充功能的開發人員使用的應用程式，或者其所屬的容器應用程式的不同，這種作法函式。

假設這些嚴格的需求，您應該只部署延伸模組已徹底測試並最佳化效能和記憶體耗用量。 

<a name="Summary" />

## <a name="summary"></a>總結

這份文件已涵蓋擴充功能，它們是什麼，擴充點以及 iOS 所加諸的擴充功能上的已知的限制的類型。 它也將討論建立、 散發、 安裝和執行擴充功能和擴充功能的生命週期。 它提供建立簡單的逐步解說**今天**widget 顯示兩種方式可以建立使用分鏡腳本或程式碼的小工具的 UI。 它示範如何在 iOS 模擬器中測試擴充功能。 最後，它簡要討論溝通主機應用程式和幾個預防措施和開發擴充功能時，應該採取的考量。 


## <a name="related-links"></a>相關連結

- [ContainerApp （範例）](https://developer.xamarin.com/samples/monotouch/intro-to-extensions)
- [建立 Xamarin.iOS 的擴充功能 （影片）](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
