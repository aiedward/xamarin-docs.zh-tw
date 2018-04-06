---
title: Hello, iOS 多重畫面
description: 在這份含有兩部分的指南中，將擴充我們在 Hello, iOS 指南中所建立的 Phoneword 應用程式來處理第二個畫面。 過程中，我們將介紹「模型-檢視-控制器」設計模式、實作第一個 iOS 瀏覽，並且更深入了解 iOS 應用程式結構和功能。
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: d72e6230-c9ee-4bee-90ec-877d256821aa
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/02/2016
ms.openlocfilehash: 3b59b942b3c256418d60436a1c4ba8b4aea14aa4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="helloios-multiscreen-quickstart"></a>Hello, iOS 多重畫面快速入門

此逐步解說的這個部分會在 Phoneword 應用程式中新增第二個畫面，其中將顯示使用應用程式所撥打之電話號碼的歷程記錄。 最終的應用程式將具有第二個畫面來顯示通話記錄，如下列螢幕擷取畫面所示：

 [![](hello-ios-multiscreen-quickstart-images/00.png "最終的應用程式將具有第二個畫面來顯示通話記錄，如此螢幕擷取畫面所示")](hello-ios-multiscreen-quickstart-images/00.png#lightbox)

[隨附的深度剖析](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md)將檢閱已建置的應用程式並討論架構、巡覽、以及過程中遇到的其他 iOS 新概念。

 <a name="Requirements" />

## <a name="requirements"></a>需求

本指南會從 Hello, iOS 文件未完成的地方繼續，而且必須完成 [Hello, iOS 快速入門](~/ios/get-started/hello-ios/index.md)。 您可以從 [Hello, iOS 範例](https://developer.xamarin.com/samples/monotouch/Hello_iOS/)下載完整版的 Phoneword 應用程式。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="walkthrough"></a>逐步解說

本逐步解說將會在 **Phoneword** 應用程式中新增 [通話記錄] 畫面。


1. 在 Visual Studio for Mac 中開啟 **Phoneword** 應用程式。 如有必要，可從[這裡](https://developer.xamarin.com/samples/monotouch/Hello_iOS/)下載 [Hello, iOS 逐步解說](~/ios/get-started/hello-ios/index.md)指南的完整 Phoneword 應用程式。


2. 從 **Solution Pad** 開啟 **Main.storyboard** 檔案：

  ![](hello-ios-multiscreen-quickstart-images/02new.png "iOS 設計工具中的 Main.storyboard")


3. 將 [巡覽控制器] 從 [工具箱] 拖曳至設計介面 (您可能需要縮小，才能在設計介面上容納這些所有項目！)：

  ![](hello-ios-multiscreen-quickstart-images/03new.png "將 [巡覽控制器] 從 [工具箱] 拖曳至設計介面")


4. 將**無來源的 Segue** (單一檢視控制器左邊的灰色箭頭) 拖曳到 [瀏覽控制項]，以變更應用程式的起點：

  ![](hello-ios-multiscreen-quickstart-images/04new.png "將無來源的 Segue 拖曳至 [巡覽控制器]，以變更應用程式的起點")


5. 按底部列來選取現有的 [根檢視控制器]，然後按 **Delete** 鍵，從設計介面移除它。
接著，移至 [巡覽控制器] 旁的 **Phoneword** 場景：

  ![](hello-ios-multiscreen-quickstart-images/05new.png "移至 [巡覽控制器] 旁的 Phoneword 場景")


6. 設定 **ViewController** 作為巡覽控制器的**根檢視控制器**。 按住 **Ctrl** 鍵，並在 [巡覽控制器] 內部按一下。 應該會出現藍色線條。 接著，持續按住 **Ctrl** 鍵，從 [巡覽控制器] 拖曳至 **Phoneword** 場景，然後放開。 這稱為「Ctrl 拖曳」：

 ![](hello-ios-multiscreen-quickstart-images/06.png "從 [巡覽控制器] 拖曳至 Phoneword 場景，然後放開")


7. 從 popover，將關聯性設為 [根]：

  ![](hello-ios-multiscreen-quickstart-images/07new.png "將關聯性設為 [根]")

  **ViewController** 現在是**瀏覽控制器的根檢視控制器：**

  ![](hello-ios-multiscreen-quickstart-images/08.png "ViewController 現在是巡覽控制器的根檢視控制器")


8. 按兩下 **Phoneword** 畫面的 [標題] 列，然後將**標題**變更為 **Phoneword**：

  ![](hello-ios-multiscreen-quickstart-images/09.png "將標題變更為 'Phoneword'")


9. 從 [工具箱] 拖曳 [按鈕]，並放置於 [通話按鈕] 下方。 拖曳控點，讓新的 [按鈕] 與 [通話按鈕] 等寬：

  ![](hello-ios-multiscreen-quickstart-images/10new.png "讓新的 [按鈕] 與 [通話按鈕] 等寬")


10. 在 **Properties Pad** 中，將按鈕的 [名稱] 變更為 **CallHistoryButton**，並將 [標題] 變更為**通話記錄**：

  ![](hello-ios-multiscreen-quickstart-images/11new.png "將按鈕的名稱變更為 CallHistoryButton，並將標題變更為通話記錄")


11. 建立 [通話記錄] 畫面。 從 [工具箱]，將 [資料表檢視控制器] 拖曳至設計介面：

 ![](hello-ios-multiscreen-quickstart-images/12new.png "將 [資料表檢視控制器] 拖曳至設計介面")


12. 接下來，按一下場景底部的黑色列來選取 [資料表檢視控制器]。 在 **Properties Pad** 中，將 [資料表檢視控制器] 的類別變更為 `CallHistoryController`，然後按 **Enter** 鍵：

  ![](hello-ios-multiscreen-quickstart-images/13new.png "將資料表檢視控制器類別變更為 CallHistoryController")

  iOS 設計工具將產生名為 `CallHistoryController` 的自訂支援類別，來管理此畫面的內容檢視階層。
  **CallHistoryController.cs** 檔案將出現在 **Solution Pad** 中：

  ![](hello-ios-multiscreen-quickstart-images/14new.png "Solution Pad 中的 CallHistoryController.cs 檔案")


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


14. 在 **Phoneword** 場景和**通話記錄**場景之間建立一個 _Segue_ (轉換)。
  在 **Phoneword 場景**中，選取 [通話記錄按鈕]，然後從**按鈕** Ctrl 拖曳至**通話記錄**場景：

  ![](hello-ios-multiscreen-quickstart-images/15.png "從按鈕 Ctrl 拖曳至通話記錄場景")

  從 [動作 Segue] popover，選取 [顯示]

  iOS 設計工具將在兩個場景之間新增一個 Segue：

  ![](hello-ios-multiscreen-quickstart-images/17new.png "介於兩個場景之間的 Segue")


15. 選取場景底部的黑色列，然後在 **Properties Pad** 中，將**檢視控制器標題**變更為**通話記錄**，以便將**標題**新增至**資料表檢視控制器**：

  ![](hello-ios-multiscreen-quickstart-images/18new.png "在 Properties Pad 中，將檢視控制器標題變更為通話記錄")

16. 當應用程式執行時，[通話記錄按鈕] 將開啟 [通話記錄] 畫面，但資料表檢視將是空白，因為沒有可追蹤和顯示電話號碼的程式碼。

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

          // set the View Controller that’s powering the screen we’re
          // transitioning to

          var callHistoryContoller = segue.DestinationViewController as CallHistoryController;

          //set the Table View Controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryContoller != null)
          {
            callHistoryContoller.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```


這裡有幾件事會發生
  * 已將變數 `translatedNumber` 從 `ViewDidLoad` 方法移至「類別層級變數」。
  * 已修改 **CallButton** 程式碼，透過呼叫 `PhoneNumbers.Add(translatedNumber)` 來將撥打的號碼新增至電話號碼清單
  * 已新增 `PrepareForSegue` 方法

  儲存並建置應用程式，以確定沒有任何錯誤。

20. 按 [啟動] 按鈕，在 **iOS 模擬器**內啟動應用程式：

  ![](hello-ios-multiscreen-quickstart-images/19.png "按 [啟動] 按鈕，在 iOS 模擬器內啟動應用程式")


恭喜您完成第一個多重畫面的 Xamarin.iOS 應用程式！

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="walkthrough"></a>逐步解說

本逐步解說將會在 **Phoneword** 應用程式中新增 [通話記錄] 畫面。


1. 在 Visual Studio 中開啟 **Phoneword** 應用程式。 如有必要，可從 [Hello, iOS 逐步解說](~/ios/get-started/hello-ios/index.md)指南下載[完整的 Phoneword 應用程式](https://developer.xamarin.com/samples/monotouch/Hello_iOS/)。 請記住，需要連線到 [Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)，才能使用 iOS 設計工具和 iOS 模擬器。


2. 從編輯使用者介面開始。 從 [方案總管] 中開啟 **Main.storyboard** 檔案，確定已將 [檢視方式] 設為 [iPhone 6]：

  ![](hello-ios-multiscreen-quickstart-images/image1.png "iOS 設計工具中的 Main.storyboard")


3. 將 [巡覽控制器] 從 [工具箱] 拖曳至設計介面：

  ![](hello-ios-multiscreen-quickstart-images/image2.png "將 [巡覽控制器] 從 [工具箱] 拖曳至設計介面")


4. 將**無來源的 Segue** (亦即 **Phoneword** 場景左邊的灰色箭頭) 從 **Phoneword** 場景拖曳至 [巡覽控制器]，以變更應用程式的起點：

  ![](hello-ios-multiscreen-quickstart-images/image3.png "將無來源的 Segue 拖曳至 [巡覽控制器]，以變更應用程式的起點")


5. 按一下黑色列來選取 [根檢視控制器]，然後按 **Delete** 鍵，從設計介面移除它。
  接著，移至 [巡覽控制器] 旁的 **Phoneword** 場景：

  ![](hello-ios-multiscreen-quickstart-images/image4.png "移至 [巡覽控制器] 旁的 Phoneword 場景")


6. 設定 **ViewController** 作為巡覽控制器的 `Root View Controller`。 按住 **Ctrl** 鍵，並在 [巡覽控制器] 內部按一下。 應該會出現藍色線條。 接著，持續按住 **Ctrl** 鍵，從 [巡覽控制器] 拖曳至 **Phoneword** 場景，然後放開。 這稱為「Ctrl 拖曳」：

  ![](hello-ios-multiscreen-quickstart-images/image5.png "從 [巡覽控制器] 拖曳至 Phoneword 場景，然後放開")


7. 從 popover，將關聯性設為 [根]：

  ![](hello-ios-multiscreen-quickstart-images/image6.png "將關聯性設為 [根]")

  **ViewController** 現在是**巡覽控制器的根檢視控制器**。


8. 按兩下 **Phoneword** 畫面的 [標題] 列，然後將**標題**變更為 **Phoneword**：

  ![](hello-ios-multiscreen-quickstart-images/image7.png "將標題變更為 Phoneword")


9. 從 [工具箱] 拖曳 [按鈕]，並放置於 [通話按鈕] 下方。 拖曳控點，讓新的 [按鈕] 與 [通話按鈕] 等寬：

  ![](hello-ios-multiscreen-quickstart-images/image8.png "讓新的 [按鈕] 與 [通話按鈕] 等寬")


10. 在**屬性總管**中，將**按鈕**的**名稱**變更為 `CallHistoryButton`，並將**標題**變更為**通話記錄**：

  ![](hello-ios-multiscreen-quickstart-images/image9.png "將按鈕的名稱變更為 'CallHistoryButton'，並將標題變更為「通話記錄」")


11. 建立 [通話記錄] 畫面。 從 [工具箱]，將 [資料表檢視控制器] 拖曳至設計介面：

  ![](hello-ios-multiscreen-quickstart-images/image10.png "將 [資料表檢視控制器] 拖曳至設計介面")


12. 按一下場景底部的黑色列來選取**資料表檢視控制器**。 在**屬性總管**中，將**資料表檢視控制器**的類別變更為 `CallHistoryController`，然後按 **Enter** 鍵：

  ![](hello-ios-multiscreen-quickstart-images/image11.png "將資料表檢視控制器類別變更為 CallHistoryController")

  iOS 設計工具將產生名為 `CallHistoryController` 的自訂支援類別，來管理此畫面的內容檢視階層。
  **CallHistoryController.cs** 檔案將出現在**方案總管**中：

  ![](hello-ios-multiscreen-quickstart-images/image12.png "方案總管中的 CallHistoryController.cs 檔案")


13. 按兩下 **CallHistoryController.cs** 檔案加以開啟，並使用下列程式碼來取代內容：

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

  儲存應用程式並建置該檔案，以確保沒有任何錯誤。 現在可以略過任何建置警告。


14. 在 **Phoneword** 場景和**通話記錄**場景之間建立一個 _Segue_ (轉換)。
  在 **Phoneword 場景**中，選取 [通話記錄按鈕]，然後從**按鈕** **Ctrl 拖曳**至**通話記錄**場景：

  ![](hello-ios-multiscreen-quickstart-images/image13.png "從按鈕 Ctrl 拖曳至通話記錄場景")

  從 [動作 Segue] popover，選取 [顯示]：

  ![](hello-ios-multiscreen-quickstart-images/image14.png "選取 [顯示] 作為 Segue 類型")

  iOS 設計工具將在兩個場景之間新增一個 Segue：

  ![](hello-ios-multiscreen-quickstart-images/image15.png "介於兩個場景之間的 Segue")


15. 選取場景底部的黑色列，然後在**屬性總管**中，將 [檢視控制器] > [標題] 變更為**通話記錄**，以便將**標題**新增至**資料表檢視控制器**：

  ![](hello-ios-multiscreen-quickstart-images/image16.png "將檢視控制器標題變更為通話記錄")


16. 當應用程式執行時，[通話記錄按鈕] 將開啟 [通話記錄] 畫面，但資料表檢視將是空白，因為沒有可追蹤和顯示電話號碼的程式碼。

  此應用程式會將電話號碼儲存為字串清單。

  在 **ViewController** 頂端新增 `System.Collections.Generic` 的 `using` 指示詞：

        using System.Collections.Generic;

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

          // set the View Controller that’s powering the screen we’re
          // transitioning to

          var callHistoryContoller = segue.DestinationViewController as CallHistoryController;

          //set the Table View Controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryContoller != null)
          {
            callHistoryContoller.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```

這裡有幾件事會發生
  * 已將變數 `translatedNumber` 從 `ViewDidLoad` 方法移至「類別層級變數」。
  * 已修改 **CallButton** 程式碼，透過呼叫 `PhoneNumbers.Add(translatedNumber)` 來將撥打的號碼新增至電話號碼清單
  * 已新增 `PrepareForSegue` 方法

  儲存並建置應用程式，以確定沒有任何錯誤。

  儲存並建置應用程式，以確定沒有任何錯誤。


20. 按 [啟動] 按鈕，在 **iOS 模擬器**內啟動應用程式：

  ![](hello-ios-multiscreen-quickstart-images/19.png "範例應用程式的第一個畫面")


恭喜您完成第一個多重畫面的 Xamarin.iOS 應用程式！


-----

應用程式現在可以使用分鏡腳本 Segue 和程式碼來處理巡覽。 現在是時候在 [Hello, iOS 深度剖析](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md)中仔細分析我們剛了解到的工具與技能。


## <a name="related-links"></a>相關連結

- [Hello, iOS (範例)](https://developer.xamarin.com/samples/monotouch/Hello_iOS/) \(英文\)
- [iOS 人性化介面指導方針](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html) \(英文\)
- [iOS 佈建入口網站](https://developer.apple.com/ios/manage/overview/index.action) \(英文\)
