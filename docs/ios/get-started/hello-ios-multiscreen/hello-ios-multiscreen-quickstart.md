---
title: Hello, iOS 多重畫面 – 快速入門
description: 本文件示範如何擴充 Phoneword 範例應用程式以新增第二個畫面，說明模型-檢視-控制器、iOS 巡覽和一路以來的其他核心 iOS 開發概念。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: d72e6230-c9ee-4bee-90ec-877d256821aa
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: c89a3616bfa239ba919ae9750082bcef48c9f890
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73023365"
---
# <a name="hello-ios-multiscreen--quickstart"></a>Hello, iOS 多重畫面 – 快速入門

逐步解說的這個部分會在 Phoneword 應用程式中新增第二個畫面，其中顯示使用應用程式撥打的電話號碼歷程記錄。 最終的應用程式將具有第二個畫面來顯示通話記錄，如下列螢幕擷取畫面所示：

[![](hello-ios-multiscreen-quickstart-images/00.png "The final application will have a second screen that displays the call history, as illustrated by this screenshot")](hello-ios-multiscreen-quickstart-images/00.png#lightbox)

[隨附的深度剖析](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md)將檢閱已建置的應用程式並討論架構、巡覽、以及過程中遇到的其他 iOS 新概念。

## <a name="requirements"></a>需求

本指南會從 Hello, iOS 文件未完成的地方繼續，而且必須完成 [Hello, iOS 快速入門](~/ios/get-started/hello-ios/index.md)。 從 [Hello, iOS 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios)下載完整版的 Phoneword 應用程式。

::: zone pivot="macos"

## <a name="walkthrough-on-macos"></a>macOS 的逐步解說

本逐步解說將會在 **Phoneword** 應用程式中新增 [通話記錄] 畫面。

1. 在 Visual Studio for Mac 中開啟 **Phoneword** 應用程式。 如有必要,從[Hello,iOS 演練](~/ios/get-started/hello-ios/index.md)指南完成的電話文字應用程式可以[從這裡](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios)下載。

2. 從 **Solution Pad** 開啟 **Main.storyboard** 檔案：

    ![](hello-ios-multiscreen-quickstart-images/02new.png "The Main.storyboard in the iOS Designer")

3. 將**導覽控制器**從**工具匣**拖到設計圖面上(您可能需要縮小以在設計圖面上安裝所有這些功能!

    ![](hello-ios-multiscreen-quickstart-images/03new.png "Drag a navigation controller from the Toolbox onto the design surface")

4. 將**無源 segue(** 即單一檢視控制器左側的灰色箭頭)拖曳到**導航控制器**以更改應用程式的起始點:

    ![](hello-ios-multiscreen-quickstart-images/04new.png "Drag the Sourceless Segue to the navigation controller to change the starting point of the application")

5. 以按下底部列選擇現有**根檢視控制器**,然後按 **「刪除」** 將其從設計圖面中刪除。
然後,將**Phoneword**場景移到**導航控制器**旁邊:

    ![](hello-ios-multiscreen-quickstart-images/05new.png "Move the Phoneword scene next to the navigation controller")

6. 將**檢視控制器**設定為導覽控制器的**根檢視控制器**。 按下**Ctrl**鍵,按下**導航控制器**內部。 應該會出現藍色線條。 然後,仍然按住**Ctrl**鍵,從**導航控制器**拖動到**Phoneword**場景並釋放。 這稱為「Ctrl 拖曳」__：

    ![](hello-ios-multiscreen-quickstart-images/06.png "Drag from the navigation controller to the Phoneword scene and release")

7. 從 popover，將關聯性設為 [根]****：

    ![](hello-ios-multiscreen-quickstart-images/07new.png "Setting the relationship to Root")

    **檢視控制器**現在是**導覽控制器的根檢視控制器:**

    ![](hello-ios-multiscreen-quickstart-images/08.png "The ViewController is now the navigation controllers Root view controller")

8. 按兩下 **Phoneword** 畫面的 [標題]**** 列，然後將**標題**變更為 **Phoneword**：

    ![](hello-ios-multiscreen-quickstart-images/09.png "Change the Title to 'Phoneword'")

9. 從 [工具箱]**** 拖曳 [按鈕]****，並放置於 [通話按鈕]**** 下方。 拖曳控點，讓新的 [按鈕]**** 與 [通話按鈕]**** 等寬：

    ![](hello-ios-multiscreen-quickstart-images/10new.png "Make the new Button the same width as the Call Button")

10. 屬性**鍵盤**中,將按鈕**的名稱**更改為 **「呼叫歷史記錄」按鈕**,並將 **「標題**」 更改為**通話記錄**「:

    ![](hello-ios-multiscreen-quickstart-images/11new.png "Change the Name of the Button to CallHistoryButton and change the Title to Call History")

11. 建立 [通話記錄]**** 畫面。 從**工具箱**中,將**表視圖控制器**拖曳到設計圖面上:

    ![](hello-ios-multiscreen-quickstart-images/12new.png "Drag a table view controller onto the design surface")

12. 接下來,通過單擊場景底部的黑色條,選擇**表視圖控制器**。 屬性**的顏色**,將**表檢視控制器的**類別變更為`CallHistoryController`,然後按**Enter**:

    ![](hello-ios-multiscreen-quickstart-images/13new.png "Change the table view controllers class to CallHistoryController")

    iOS 設計工具將產生稱為 `CallHistoryController` 的自訂支援類別，用來管理此畫面的內容檢視階層。 **CallHistoryController.cs** 檔案將出現在 **Solution Pad** 中：

    ![](hello-ios-multiscreen-quickstart-images/14new.png "The CallHistoryController.cs file in the Solution Pad")

13. 按兩下 **CallHistoryController.cs** 檔案加以開啟，並使用下列程式碼來取代內容：
    
    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.Collections.Generic;

    namespace Phoneword_iOS
    {
        public partial class CallHistoryController : UITableViewController
        {
            public List<string> PhoneNumbers { get; set; }

            static NSString callHistoryCellId = new NSString ("CallHistoryCell");

            public CallHistoryController (IntPtr handle) : base (handle)
            {
                TableView.RegisterClassForCellReuse (typeof(UITableViewCell), callHistoryCellId);
                TableView.Source = new CallHistoryDataSource (this);
                PhoneNumbers = new List<string> ();
            }

            class CallHistoryDataSource : UITableViewSource
            {
                CallHistoryController controller;

                public CallHistoryDataSource (CallHistoryController controller)
                {
                    this.controller = controller;
                }

                public override nint RowsInSection (UITableView tableView, nint section)
                {
                    return controller.PhoneNumbers.Count;
                }

                public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
                {
                    var cell = tableView.DequeueReusableCell (CallHistoryController.callHistoryCellId);

                    int row = indexPath.Row;
                    cell.TextLabel.Text = controller.PhoneNumbers [row];
                    return cell;
                }
            }
        }
    }
    ```

    儲存應用程式 (**⌘ + s**) 並建置該檔案 (**⌘ + b**)，以確保沒有任何錯誤。

14. 在**電話字**場景和**呼叫歷史記錄**場景之間創建_一個分段_(過渡)。
  在 **"電話"場景中**,選擇"**呼叫歷史記錄"按鈕**,然後從 **"按鈕**"拖動到**呼叫歷史記錄**場景:

    ![](hello-ios-multiscreen-quickstart-images/15.png "Ctrl-drag from the Button to the Call History scene")

    從**操作 Segue**彈出器中,選擇 **"顯示"**

    iOS 設計工具將在兩個場景之間新增一個 Segue：

    ![](hello-ios-multiscreen-quickstart-images/17new.png "The Segue between the two scenes")

15. 以選擇場景底部的黑色條,並將**檢視控制器「標題」** 變更為「**屬性鍵盤**中的**呼叫歷史紀錄**」,向**表視圖控制器**新增**標題**:

    ![](hello-ios-multiscreen-quickstart-images/18new.png "Change the view controller title to Call History in the Properties Pad")

16. 執行應用程式時，**通話記錄按鈕**將會開啟 [通話記錄]**** 畫面，但資料表檢視會是空白，這是因為沒有可追蹤和顯示電話號碼的程式碼。

    此應用程式會將電話號碼儲存為字串清單。

    在`using``System.Collections.Generic` **ViewController**的頂端加入指令 :

    ```csharp
    using System.Collections.Generic;
    ```

17. 使用下列程式碼來修改 `ViewController` 類別：

    ```csharp
    using System;
    using System.Collections.Generic;
    using Foundation;
    using UIKit;

    namespace Phoneword_iOS
    {
      public partial class ViewController : UIViewController
      {
        string translatedNumber = "";

        public List<string> PhoneNumbers { get; set; }

        protected ViewController(IntPtr handle) : base(handle)
        {
          //initialize list of phone numbers called for Call History screen
          PhoneNumbers = new List<string>();
        }

        public override void ViewDidLoad()
        {
          base.ViewDidLoad();
          // Perform any additional setup after loading the view, typically from a nib.

          TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
            // Convert the phone number with text to a number
            // using PhoneTranslator.cs
            translatedNumber = PhoneTranslator.ToNumber(
              PhoneNumberText.Text);

            // Dismiss the keyboard if text field was tapped
            PhoneNumberText.ResignFirstResponder();

            if (translatedNumber == "")
            {
              CallButton.SetTitle("Call ", UIControlState.Normal);
              CallButton.Enabled = false;
            }
            else
            {
              CallButton.SetTitle("Call " + translatedNumber,
                UIControlState.Normal);
              CallButton.Enabled = true;
            }
          };

          CallButton.TouchUpInside += (object sender, EventArgs e) => {

            //Store the phone number that we're dialing in PhoneNumbers
            PhoneNumbers.Add(translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl("tel:" + translatedNumber);

            // otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl(url))
            {
              var alert = UIAlertController.Create("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
              alert.AddAction(UIAlertAction.Create("Ok", UIAlertActionStyle.Default, null));
              PresentViewController(alert, true, null);
            }
          };
        }

        public override void PrepareForSegue(UIStoryboardSegue segue, NSObject sender)
        {
          base.PrepareForSegue(segue, sender);

          // set the view controller that’s powering the screen we’re
          // transitioning to

          var callHistoryController = segue.DestinationViewController as CallHistoryController;

          //set the table view controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryController != null)
          {
            callHistoryController.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```

    這裡有幾件事會發生：

    - 變數 `translatedNumber` 已從 `ViewDidLoad` 方法移到「類別層級變數」__。
    - 已修改 **CallButton** 程式碼，透過呼叫 `PhoneNumbers.Add(translatedNumber)` 來將撥打的號碼新增至電話號碼清單。
    - 已新增 `PrepareForSegue` 方法。

    儲存並建置應用程式，以確定沒有任何錯誤。

18. 按 **「開始」** 按鈕在**iOS 模擬器**內啟動應用程式:

    ![](hello-ios-multiscreen-quickstart-images/19.png "Press the Start button to launch the application inside the iOS Simulator")

恭喜您完成第一個多重畫面的 Xamarin.iOS 應用程式！

::: zone-end
::: zone pivot="windows"

## <a name="walkthrough-on-windows"></a>Windows 的逐步解說

本逐步解說將會在 **Phoneword** 應用程式中新增 [通話記錄] 畫面。

1. 在 Visual Studio 中開啟 **Phoneword** 應用程式。 如有必要，可從 [Hello, iOS 逐步解說](~/ios/get-started/hello-ios/index.md)指南下載[完整的 Phoneword 應用程式](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios)。 請記住，需要連線到 [Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)，才能使用 iOS 設計工具和 iOS 模擬器。

2. 從編輯使用者介面開始。 從 [方案總管]**** 中開啟 **Main.storyboard** 檔案，確定已將 [檢視方式]**** 設為 [iPhone 6]__：

    ![](hello-ios-multiscreen-quickstart-images/image1.png "The Main.storyboard in the iOS Designer")

3. 將**導覽控制器**從**工具匣**拖到設計圖面上:

    ![](hello-ios-multiscreen-quickstart-images/image2.png "Drag a navigation controller from the Toolbox onto the design surface")

4. 將**無源 Segue(** 即**Phoneword**場景左側的灰色箭頭)從**Phoneword**場景拖動到**導航控制器**以更改應用程式的起始點:

    ![](hello-ios-multiscreen-quickstart-images/image3.png "Drag the Sourceless Segue to the navigation controller to change the starting point of the application")

5. 以按下黑色條選擇 **「根檢視」控制器**,然後按 **「刪除」** 將其從設計圖面中刪除。
  然後,將**Phoneword**場景移到**導航控制器**旁邊:

    ![](hello-ios-multiscreen-quickstart-images/image4.png "Move the Phoneword scene next to the navigation controller")

6. 將**檢視控制器**設置為導航控制器的根檢視控制器。 按下 **Ctrl** 鍵，並在 [瀏覽控制器]**** 內部按一下。 應該會出現藍色線條。 然後,仍然按住**Ctrl**鍵,從**導航控制器**拖動到**Phoneword**場景並釋放。 這稱為「Ctrl 拖曳」__：

    ![](hello-ios-multiscreen-quickstart-images/image5.png "Drag from the navigation controller to the Phoneword scene and release")

7. 從 popover，將關聯性設為 [根]****：

    ![](hello-ios-multiscreen-quickstart-images/image6.png "Set the relationship to Root")

    **視圖控制器**現在是我們的**導航控制器的根視圖控制器。**

8. 按兩下 **Phoneword** 畫面的 [標題]**** 列，然後將**標題**變更為 **Phoneword**：

    ![](hello-ios-multiscreen-quickstart-images/image7.png "Change the Title to Phoneword")

9. 從 [工具箱]**** 拖曳 [按鈕]****，並放置於 [通話按鈕]**** 下方。 拖曳控點，讓新的 [按鈕]**** 與 [通話按鈕]**** 等寬：

    ![](hello-ios-multiscreen-quickstart-images/image8.png "Make the new Button the same width as the Call Button")

10. 在**屬性總管**中，將**按鈕**的**名稱**變更為 `CallHistoryButton`，並將**標題**變更為**通話記錄**：

    ![](hello-ios-multiscreen-quickstart-images/image9.png "Change the Name of the Button to 'CallHistoryButton' and the Title to 'Call History'")

11. 建立 [通話記錄]**** 畫面。 從**工具箱**中,將**表視圖控制器**拖曳到設計圖面上:

    ![](hello-ios-multiscreen-quickstart-images/image10.png "Drag a table view controller onto the design surface")

12. 點選場景底部的黑色條,選擇**表視圖控制器**。 屬性**資源管理員**中,將**表視圖控制器的**類別變更為`CallHistoryController`,然後按**Enter**:

    ![](hello-ios-multiscreen-quickstart-images/image11.png "Change the table view controllers class to CallHistoryController")

    iOS 設計工具將產生稱為 `CallHistoryController` 的自訂支援類別，用來管理此畫面的內容檢視階層。 **CallHistoryController.cs** 檔案將出現在**方案總管**中：

    ![](hello-ios-multiscreen-quickstart-images/image12.png "The CallHistoryController.cs file in the Solution Explorer")

13. 按兩下 **CallHistoryController.cs** 檔案加以開啟，並使用下列程式碼來取代內容：

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.Collections.Generic;

    namespace Phoneword
    {
        public partial class CallHistoryController : UITableViewController
        {
            public List<String> PhoneNumbers { get; set; }

            static NSString callHistoryCellId = new NSString ("CallHistoryCell");

            public CallHistoryController (IntPtr handle) : base (handle)
            {
                TableView.RegisterClassForCellReuse (typeof(UITableViewCell), callHistoryCellId);
                TableView.Source = new CallHistoryDataSource (this);
                PhoneNumbers = new List<string> ();
            }

            class CallHistoryDataSource : UITableViewSource
            {
                CallHistoryController controller;

                public CallHistoryDataSource (CallHistoryController controller)
                {
                    this.controller = controller;
                }

                // Returns the number of rows in each section of the table
                public override nint RowsInSection (UITableView tableView, nint section)
                {
                    return controller.PhoneNumbers.Count;
                }

                public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
                {
                    var cell = tableView.DequeueReusableCell (CallHistoryController.callHistoryCellId);

                    int row = indexPath.Row;
                    cell.TextLabel.Text = controller.PhoneNumbers [row];
                    return cell;
                }
            }
        }
    }
    ```

    儲存應用程式並建置該檔案，以確保沒有任何錯誤。 現在可以略過任何建置警告。

14. 在**電話字**場景和**呼叫歷史記錄**場景之間創建_一個分段_(過渡)。
  在 **"電話"場景中**,選擇"**呼叫歷史記錄"按鈕**,然後從 **"按鈕****"拖動**到**呼叫歷史記錄**場景:

    ![](hello-ios-multiscreen-quickstart-images/image13.png "Ctrl-drag from the Button to the Call History scene")

    從 [動作 Segue]**** popover，選取 [顯示]****：

    ![](hello-ios-multiscreen-quickstart-images/image14.png "Select Show as the segue type")

    iOS 設計工具將在兩個場景之間新增一個 Segue：

    ![](hello-ios-multiscreen-quickstart-images/image15.png "The Segue between the two scenes")

15. 以選擇場景底部的黑色條,並將**檢視控制器>"標題**'變更為「**屬性資源管理員**」**的呼叫歷史記錄**,向**表視圖控制器**加入**標題**:

    ![](hello-ios-multiscreen-quickstart-images/image16.png "Change the view controller Title to Call History")

16. 執行應用程式時，**通話記錄按鈕**將會開啟 [通話記錄]**** 畫面，但資料表檢視會是空白，這是因為沒有可追蹤和顯示電話號碼的程式碼。

    此應用程式會將電話號碼儲存為字串清單。

    在 **ViewController** 頂端新增 `System.Collections.Generic` 的 `using` 指示詞：

    ```csharp
    using System.Collections.Generic;
    ```

17. 使用下列程式碼來修改 `ViewController` 類別：

    ```csharp
    using System;
    using System.Collections.Generic;
    using Foundation;
    using UIKit;

    namespace Phoneword_iOS
    {
      public partial class ViewController : UIViewController
      {
        string translatedNumber = "";

        public List<string> PhoneNumbers { get; set; }

        protected ViewController(IntPtr handle) : base(handle)
        {
          //initialize list of phone numbers called for Call History screen
          PhoneNumbers = new List<string>();
        }

        public override void ViewDidLoad()
        {
          base.ViewDidLoad();
          // Perform any additional setup after loading the view, typically from a nib.

          TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
            // Convert the phone number with text to a number
            // using PhoneTranslator.cs
            translatedNumber = PhoneTranslator.ToNumber(
              PhoneNumberText.Text);

            // Dismiss the keyboard if text field was tapped
            PhoneNumberText.ResignFirstResponder();

            if (translatedNumber == "")
            {
              CallButton.SetTitle("Call ", UIControlState.Normal);
              CallButton.Enabled = false;
            }
            else
            {
              CallButton.SetTitle("Call " + translatedNumber,
                UIControlState.Normal);
              CallButton.Enabled = true;
            }
          };

          CallButton.TouchUpInside += (object sender, EventArgs e) => {

            //Store the phone number that we're dialing in PhoneNumbers
            PhoneNumbers.Add(translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl("tel:" + translatedNumber);

            // otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl(url))
            {
              var alert = UIAlertController.Create("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
              alert.AddAction(UIAlertAction.Create("Ok", UIAlertActionStyle.Default, null));
              PresentViewController(alert, true, null);
            }
          };
        }

        public override void PrepareForSegue(UIStoryboardSegue segue, NSObject sender)
        {
          base.PrepareForSegue(segue, sender);

          // set the view controller that’s powering the screen we’re
          // transitioning to

          var callHistoryController = segue.DestinationViewController as CallHistoryController;

          //set the table view controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryController != null)
          {
            callHistoryController.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```

    這裡有幾件事會發生
    - 已將變數 `translatedNumber` 從 `ViewDidLoad` 方法移至「類別層級變數」__。
    - 已修改 **CallButton** 程式碼，透過呼叫 `PhoneNumbers.Add(translatedNumber)` 來將撥打的號碼新增至電話號碼清單
    - 已新增 `PrepareForSegue` 方法

    儲存並建置應用程式，以確定沒有任何錯誤。

    儲存並建置應用程式，以確定沒有任何錯誤。

18. 按 [啟動]**** 按鈕，在 **iOS 模擬器**內啟動應用程式：

    ![](hello-ios-multiscreen-quickstart-images/19.png "The first screen of the sample app")

恭喜您完成第一個多重畫面的 Xamarin.iOS 應用程式！

::: zone-end

應用程式現在可以使用分鏡腳本 Segue 和程式碼來處理瀏覽。 現在是時候在 [Hello, iOS 深度剖析](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md)中仔細分析我們剛了解到的工具與技能。

## <a name="related-links"></a>相關連結

- [Hello, iOS (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios) \(英文\)
- [iOS 人性化介面指導方針](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [iOS 佈建入口網站](https://developer.apple.com/ios/manage/overview/index.action)
