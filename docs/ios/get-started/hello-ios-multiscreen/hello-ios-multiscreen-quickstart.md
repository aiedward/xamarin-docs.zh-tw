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
ms.openlocfilehash: 397488954ac99ac4d9ef05a5f8005c0f8771b6dc
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603122"
---
# <a name="hello-ios-multiscreen--quickstart"></a>Hello, iOS 多重畫面 – 快速入門
> [!WARNING]
> IOS 設計工具在 Visual Studio 2019 16.8 版和 Visual Studio 2019 for Mac 版本8.8 中已淘汰，並已在 Visual Studio 2019 版本16.9 和 Visual Studio for Mac 版本8.9 中移除。
> 建立 iOS 使用者介面的建議方式是直接在執行 Xcode 介面產生器的 Mac 上。 如需詳細資訊，請參閱 [使用 Xcode 設計使用者介面](~/ios/user-interface/storyboards/index.md)。 

逐步解說的這個部分會在 Phoneword 應用程式中新增第二個畫面，其中顯示使用應用程式撥打的電話號碼歷程記錄。 最終的應用程式將具有第二個畫面來顯示通話記錄，如下列螢幕擷取畫面所示：

[![最終的應用程式將會有第二個畫面來顯示通話記錄，如下列螢幕擷取畫面所示](hello-ios-multiscreen-quickstart-images/00.png)](hello-ios-multiscreen-quickstart-images/00.png#lightbox)

[隨附的深度剖析](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md)將檢閱已建置的應用程式並討論架構、巡覽、以及過程中遇到的其他 iOS 新概念。

## <a name="requirements"></a>規格需求

本指南會從 Hello, iOS 文件未完成的地方繼續，而且必須完成 [Hello, iOS 快速入門](~/ios/get-started/hello-ios/index.md)。 從 [Hello, iOS 範例](/samples/xamarin/ios-samples/hello-ios)下載完整版的 Phoneword 應用程式。

::: zone pivot="macos"

## <a name="walkthrough-on-macos"></a>macOS 的逐步解說

本逐步解說將會在 **Phoneword** 應用程式中新增 [通話記錄] 畫面。

1. 在 Visual Studio for Mac 中開啟 **Phoneword** 應用程式。 如有必要，您可以從[這裡](/samples/xamarin/ios-samples/hello-ios)下載來自[Hello，iOS 逐步](~/ios/get-started/hello-ios/index.md)解說指南的完整 Phoneword 應用程式。

2. 從 **Solution Pad** 開啟 **Main.storyboard** 檔案：

    ![iOS 設計工具中的 Main.storyboard](hello-ios-multiscreen-quickstart-images/02new.png)

3. 將 [ **流覽控制器** ] 從 [ **工具箱** ] 拖曳到設計介面上 (您可能需要在設計介面上縮小以符合這些內容！ ) ：

    ![將 [流覽控制器] 從 [工具箱] 拖曳至設計介面](hello-ios-multiscreen-quickstart-images/03new.png)

4. 將 **segue segue** (是 [單一視圖控制器] 左側的灰色箭號) 至 [ **流覽控制器** ]，以變更應用程式的起點：

    ![將 Segue Segue 拖曳到流覽控制器，以變更應用程式的起點](hello-ios-multiscreen-quickstart-images/04new.png)

5. 按一下底部列來選取現有的 **根視圖控制器** ，然後按下 [ **刪除** ]，從設計介面中移除它。
然後，將 **Phoneword** 場景移至 **流覽控制器** 旁邊：

    ![將 Phoneword 場景移至流覽控制器旁邊](hello-ios-multiscreen-quickstart-images/05new.png)

6. 將 **ViewController** 設定為流覽控制器的 **根視圖控制器**。 按下 **Ctrl** 鍵，然後按一下 **流覽控制器** 內部。 應該會出現藍色線條。 然後，仍按住 **Ctrl** 鍵，從 **流覽控制器** 拖曳至 **Phoneword** 場景和發行。 這稱為「Ctrl 拖曳」：

    ![從流覽控制器拖曳至 Phoneword 場景和版本](hello-ios-multiscreen-quickstart-images/06.png)

7. 從 popover，將關聯性設為 [根]：

    ![將關聯性設為 [根]](hello-ios-multiscreen-quickstart-images/07new.png)

    **ViewController** 現在是 **流覽控制器的根視圖控制器：**

    ![ViewController 現在是流覽控制器的根視圖控制器](hello-ios-multiscreen-quickstart-images/08.png)

8. 按兩下 **Phoneword** 畫面的 [標題] 列，然後將 **標題** 變更為 **Phoneword**：

    ![將標題變更為 Phoneword](hello-ios-multiscreen-quickstart-images/09.png)

9. 從 [工具箱] 拖曳 [按鈕]，並放置於 [通話按鈕] 下方。 拖曳控點，讓新的 [按鈕] 與 [通話按鈕] 等寬：

    ![讓新的 [按鈕] 與 [通話按鈕] 等寬](hello-ios-multiscreen-quickstart-images/10new.png)

10. 在 **Properties Pad** 中，將按鈕 **的名稱** 變更為 **CallHistoryButton** ，並將 **標題** 變更為 **通話記錄**：

    ![將按鈕的名稱變更為 CallHistoryButton，並將標題變更為通話記錄](hello-ios-multiscreen-quickstart-images/11new.png)

11. 建立 [通話記錄] 畫面。 從 [ **工具箱**] 將 [ **資料表視圖控制器** ] 拖曳至設計介面：

    ![將 [資料表視圖控制器] 拖曳至設計介面](hello-ios-multiscreen-quickstart-images/12new.png)

12. 接著，按一下場景底部的黑色列來選取 [ **資料表視圖控制器** ]。 在 [ **屬性] 面板** 中，將 [ **資料表視圖控制器] 的** 類別變更為， `CallHistoryController` 然後按下 **enter**：

    ![將資料表視圖控制器類別變更為 Callhistorycontroller.cs](hello-ios-multiscreen-quickstart-images/13new.png)

    iOS 設計工具將產生稱為 `CallHistoryController` 的自訂支援類別，用來管理此畫面的內容檢視階層。 **CallHistoryController.cs** 檔案將出現在 **Solution Pad** 中：

    ![Solution Pad 中的 CallHistoryController.cs 檔案](hello-ios-multiscreen-quickstart-images/14new.png)

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

14. 在 **Phoneword** 場景和 **通話記錄** 場景之間建立 _segue_ (轉換) 。
  在 **Phoneword 場景** 中，選取 [ **通話記錄] 按鈕** ，然後從 **按鈕** 拖曳至 **通話記錄** 場景：

    ![Ctrl-從按鈕拖曳至通話記錄場景](hello-ios-multiscreen-quickstart-images/15.png)

    從 [**動作 Segue** ] popover 中，選取 [**顯示**]

    iOS 設計工具將在兩個場景之間新增一個 Segue：

    ![兩個場景之間的 Segue](hello-ios-multiscreen-quickstart-images/17new.png)

15. 選取場景底部的黑色列，並在 [**屬性] 面板** 中將 **視圖控制器標題** 變更為 **呼叫歷程記錄**，以將 **標題** 加入至 **表格視圖控制器**：

    ![在 Properties Pad 中，將 view controller 標題變更為通話記錄](hello-ios-multiscreen-quickstart-images/18new.png)

16. 執行應用程式時，**通話記錄按鈕** 將會開啟 [通話記錄] 畫面，但資料表檢視會是空白，這是因為沒有可追蹤和顯示電話號碼的程式碼。

    此應用程式會將電話號碼儲存為字串清單。

    `using` `System.Collections.Generic` 在 **ViewController** 的頂端加入指示詞：

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

    - 變數 `translatedNumber` 已從 `ViewDidLoad` 方法移到「類別層級變數」。
    - 已修改 **CallButton** 程式碼，透過呼叫 `PhoneNumbers.Add(translatedNumber)` 來將撥打的號碼新增至電話號碼清單。
    - 已新增 `PrepareForSegue` 方法。

    儲存並建置應用程式，以確定沒有任何錯誤。

18. 按下 [ **開始** ] 按鈕，以在 **iOS** 模擬器內啟動應用程式：

    ![按下 [開始] 按鈕，以在 iOS 模擬器內啟動應用程式](hello-ios-multiscreen-quickstart-images/19.png)

恭喜您完成第一個多重畫面的 Xamarin.iOS 應用程式！

::: zone-end
::: zone pivot="windows"

## <a name="walkthrough-on-windows"></a>Windows 的逐步解說

本逐步解說將會在 **Phoneword** 應用程式中新增 [通話記錄] 畫面。

1. 在 Visual Studio 中開啟 **Phoneword** 應用程式。 如有必要，可從 [Hello, iOS 逐步解說](~/ios/get-started/hello-ios/index.md)指南下載[完整的 Phoneword 應用程式](/samples/xamarin/ios-samples/hello-ios)。 請記住，需要連線到 [Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)，才能使用 iOS 設計工具和 iOS 模擬器。

2. 從編輯使用者介面開始。 從 [方案總管] 中開啟 **Main.storyboard** 檔案，確定已將 [檢視方式] 設為 [iPhone 6]：

    ![iOS 設計工具中的 Main.storyboard](hello-ios-multiscreen-quickstart-images/image1.png)

3. 將 [ **流覽控制器** ] 從 [ **工具箱** ] 拖曳至設計介面：

    ![將 [流覽控制器] 從 [工具箱] 拖曳至設計介面](hello-ios-multiscreen-quickstart-images/image2.png)

4. 將 **Segue Segue** (這是 **Phoneword** 場景左邊的灰色箭號) 從 **Phoneword** 場景到 **流覽控制器** ，以變更應用程式的起點：

    ![將 Segue Segue 拖曳到流覽控制器，以變更應用程式的起點](hello-ios-multiscreen-quickstart-images/image3.png)

5. 按一下黑色列來選取 [ **根視圖控制器** ]，然後按下 [ **刪除** ]，從設計介面中移除它。
  然後，將 **Phoneword** 場景移至 **流覽控制器** 旁邊：

    ![將 Phoneword 場景移至流覽控制器旁邊](hello-ios-multiscreen-quickstart-images/image4.png)

6. 將 **ViewController** 設定為流覽控制器的根視圖控制器。 按下 **Ctrl** 鍵，並在 [瀏覽控制器] 內部按一下。 應該會出現藍色線條。 然後，仍按住 **Ctrl** 鍵，從 **流覽控制器** 拖曳至 **Phoneword** 場景和發行。 這稱為「Ctrl 拖曳」：

    ![從流覽控制器拖曳至 Phoneword 場景和版本](hello-ios-multiscreen-quickstart-images/image5.png)

7. 從 popover，將關聯性設為 [根]：

    ![將關聯性設為 [根]](hello-ios-multiscreen-quickstart-images/image6.png)

    **ViewController** 現在是 **流覽控制器的根視圖控制器。**

8. 按兩下 **Phoneword** 畫面的 [標題] 列，然後將 **標題** 變更為 **Phoneword**：

    ![將標題變更為 Phoneword](hello-ios-multiscreen-quickstart-images/image7.png)

9. 從 [工具箱] 拖曳 [按鈕]，並放置於 [通話按鈕] 下方。 拖曳控點，讓新的 [按鈕] 與 [通話按鈕] 等寬：

    ![讓新的 [按鈕] 與 [通話按鈕] 等寬](hello-ios-multiscreen-quickstart-images/image8.png)

10. 在 **屬性總管** 中，將 **按鈕** 的 **名稱** 變更為 `CallHistoryButton`，並將 **標題** 變更為 **通話記錄**：

    ![將按鈕的名稱變更為 CallHistoryButton，並將標題變更為通話記錄](hello-ios-multiscreen-quickstart-images/image9.png)

11. 建立 [通話記錄] 畫面。 從 [ **工具箱**] 將 [ **資料表視圖控制器** ] 拖曳至設計介面：

    ![將 [資料表視圖控制器] 拖曳至設計介面](hello-ios-multiscreen-quickstart-images/image10.png)

12. 按一下場景底部的黑色列，以選取 [ **資料表視圖控制器** ]。 在 [ **屬性瀏覽器**] 中，將 **table view 控制器的** 類別變更為 `CallHistoryController` ，然後按下 **enter**：

    ![將資料表視圖控制器類別變更為 Callhistorycontroller.cs](hello-ios-multiscreen-quickstart-images/image11.png)

    iOS 設計工具將產生稱為 `CallHistoryController` 的自訂支援類別，用來管理此畫面的內容檢視階層。 **CallHistoryController.cs** 檔案將出現在 **方案總管** 中：

    ![方案總管中的 CallHistoryController.cs 檔案](hello-ios-multiscreen-quickstart-images/image12.png)

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

14. 在 **Phoneword** 場景和 **通話記錄** 場景之間建立 _segue_ (轉換) 。
  在 **Phoneword 場景** 中，選取 [**通話記錄] 按鈕**，然後從 **按鈕****拖曳** 至 **通話記錄** 場景：

    ![Ctrl-從按鈕拖曳至通話記錄場景](hello-ios-multiscreen-quickstart-images/image13.png)

    從 [動作 Segue] popover，選取 [顯示]：

    ![選取 [顯示] 作為 Segue 類型](hello-ios-multiscreen-quickstart-images/image14.png)

    iOS 設計工具將在兩個場景之間新增一個 Segue：

    ![兩個場景之間的 Segue](hello-ios-multiscreen-quickstart-images/image15.png)

15. 選取場景底部的黑色列，並在 [**屬性瀏覽器**] 中將 **視圖控制器 > 標題** 變更為 **通話記錄**，以將 **標題** 加入至 **表格視圖控制器**：

    ![將 view controller 標題變更為通話記錄](hello-ios-multiscreen-quickstart-images/image16.png)

16. 執行應用程式時，**通話記錄按鈕** 將會開啟 [通話記錄] 畫面，但資料表檢視會是空白，這是因為沒有可追蹤和顯示電話號碼的程式碼。

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
    - 已將變數 `translatedNumber` 從 `ViewDidLoad` 方法移至「類別層級變數」。
    - 已修改 **CallButton** 程式碼，透過呼叫 `PhoneNumbers.Add(translatedNumber)` 來將撥打的號碼新增至電話號碼清單
    - 已新增 `PrepareForSegue` 方法

    儲存並建置應用程式，以確定沒有任何錯誤。

    儲存並建置應用程式，以確定沒有任何錯誤。

18. 按 [啟動] 按鈕，在 **iOS 模擬器** 內啟動應用程式：

    ![範例應用程式的第一個畫面](hello-ios-multiscreen-quickstart-images/19.png)

恭喜您完成第一個多重畫面的 Xamarin.iOS 應用程式！

::: zone-end

應用程式現在可以使用分鏡腳本 Segue 和程式碼來處理瀏覽。 現在是時候在 [Hello, iOS 深度剖析](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md)中仔細分析我們剛了解到的工具與技能。

## <a name="related-links"></a>相關連結

- [Hello, iOS (範例)](/samples/xamarin/ios-samples/hello-ios) \(英文\)
- [iOS 人性化介面指導方針](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [iOS 佈建入口網站](https://developer.apple.com/ios/manage/overview/index.action)