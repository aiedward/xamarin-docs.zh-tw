---
title: 文字輸入
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 43f1f0318bceb3da8a3d6216a49ec2af48ac155c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="text-input"></a>文字輸入

利用接受使用者文字輸入來完成`UITextField`單行輸入與 UITextView 為多行編輯的文字。 您可以任何這些控制項拖曳到螢幕，並按兩下，設定初始文字。

以下螢幕擷取畫面顯示位於工具箱填補，Visual Studio 中，適用於 Mac 的這些控制項的圖示：

 [![](text-input-images/image11a.png "UITextField")](text-input-images/image11a.png#lightbox)

 [![](text-input-images/image13a.png "UITextView")](text-input-images/image13a.png#lightbox)

一旦您擁有具名插座，並儲存分鏡腳本檔案，將會更新 Visual Studio for Mac`.designer.cs`部分類別，以及您可以加入 C# 程式碼參考至類別檔案中的控制項。 每個控制項都有自己的獨特屬性和 C# 程式碼可以存取的事件。

 <a name="UITextField" />


## <a name="uitextfield"></a>UITextField

`UITextField`控制項通常用來接受單行文字輸入，例如使用者名稱或密碼。 部分可供自訂控制項的選項如下所示：

 [![](text-input-images/image15a.png "UITextField 屬性")](text-input-images/image15a.png#lightbox)

這些控制項說明如下：

-  **預留位置**– 這是選擇性的。 如果設定，它會顯示在文字欄位是空的通常是為了向使用者解釋應該何種輸入。
-  **清除按鈕**– 這會控制當標準清除 按鈕 （灰色圓形與 (X)） 會顯示在文字欄位中，為方法，讓使用者快速純文字。 它可以是永久隱藏、 永久可見的或顯示，根據正在編輯的欄位。
-  **最小字型大小**和**調整至適當比例**– 可讓字型大小自動調整，以容納較長的文字，並避免截斷，但受限於不小於指定的大小。
-  **大小寫**-是否要自動大寫單字、 句子或所有的輸入。
-  **更正**– 是否已啟用拼字檢查和建議。
-  **鍵盤**– 控制項輸入顯示鍵盤樣式，因此哪些金鑰鍵盤上可用。 這包括數字鍵台、 電話填補、 電子郵件、 URL 以及其他選項。
-  **外觀**– 控制鍵盤的外觀樣式和的任一暗色調或淺佈景主題。
-  **傳回索引鍵**– 變更索引標籤上傳回的索引鍵，以更精確反映所要採取的動作。 支援的值包括 Go、 聯結、 下一步，路由，請完成和搜尋。
-  **安全**– 識別是否為已遮罩輸入 （例如密碼輸入）。


如果呼叫 UITextField`textfield1`已加入至螢幕設計工具中，您可以設定或變更其在 C# 中的屬性，如下所示：

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

Xamarin.iOS 提供列舉型別，以讓您輕鬆選取您要的設定，例如`UIKeyboardType`和`UIReturnKeyType`上述程式碼片段中。

### <a name="display-text-programmatically"></a>以程式設計方式顯示文字

如果您不想要與設計工具設計螢幕，或如果您想要以動態方式在執行階段加入一些文字，您可以建立並顯示以程式設計方式在 UITextField`ViewDidLoad`方法檢視控制站，像這樣：

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView" />


## <a name="uitextview"></a>UITextView

`UITextView`控制項可用來顯示唯讀狀態的文字，或接受多行文字輸入。 它有許多相同的選項為`UITextField`（例如大小寫，更正，等）。

 [![](text-input-images/image16a.png "UITextView 屬性")](text-input-images/image16a.png#lightbox)

特定的屬性包括：

-  **行為**– 文字是否可編輯或唯讀狀態。
-  **偵測**– 偵測並轉換為可點按的項目資料不經例如電話號碼可以觸發呼叫，位址成為連結到對應，在 Safari 中開啟的 Url 或日期和時間的事件，在變得行事曆。


如果 UITextView 已加入至螢幕與設計工具，您可以設定或變更其內容如下：

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```



## <a name="related-links"></a>相關連結

- [控制項 （範例）](https://developer.xamarin.com/samples/Controls/)
