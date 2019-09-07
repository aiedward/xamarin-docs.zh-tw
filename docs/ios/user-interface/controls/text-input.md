---
title: 在 Xamarin 中輸入文字
description: 本檔說明 Xamarin iOS 應用程式中的文字輸入。 它討論如何以程式設計方式和 iOS 設計工具來使用 UITextField 和 UITextVIew。
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 8f47ebdd8c1ba220229c6e652af99e8fa3ae2960
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768822"
---
# <a name="text-input-in-xamarinios"></a>在 Xamarin 中輸入文字

接受使用者文字輸入是透過`UITextField`適用于多行可編輯文字的單行輸入和 UITextView 來完成。 您可以將其中一個控制項拖曳到螢幕上，然後按兩下以設定初始文字。

下列螢幕擷取畫面顯示這些控制項的圖示，位於 Visual Studio for Mac 的 [工具箱] 面板中：

 [![](text-input-images/image11a.png "UITextField")](text-input-images/image11a.png#lightbox)

 [![](text-input-images/image13a.png "UITextView")](text-input-images/image13a.png#lightbox)

一旦您命名了「輸出口」並儲存分鏡腳本檔案之後，Visual Studio for Mac `.designer.cs`將會更新部分類別， C#而且您可以將參考該控制項的程式碼加入至您的類別檔案。 每個控制項都有自己的唯一屬性和事件，可以在您C#的程式碼中存取。

 <a name="UITextField" />

## <a name="uitextfield"></a>UITextField

`UITextField`控制項最常用來接受單行文字輸入，例如使用者名稱或密碼。 有一些可供自訂控制項的選項如下所示：

 [![](text-input-images/image15a.png "UITextField 屬性")](text-input-images/image15a.png#lightbox)

這些控制項的說明如下：

- **預留位置**–這是選擇性的。 如果設定，則會在文字欄位空白時顯示，通常會向使用者說明預期的輸入。
- [**清除] 按鈕**–這會控制標準 [清除] 按鈕（具有（X）的灰色圓形）在文字欄位中出現的時間，以讓使用者快速清除文字。 視是否正在編輯欄位而定，它可以永久隱藏、永久可見或顯示。
- **最小字型大小**和**調整**大小-允許自動調整字型大小，以容納較長的文字並防止截斷，但限制為小於指定的大小。
- **大小寫**–是否自動將單字、句子或所有輸入全部大寫。
- **更正**–是否已啟用拼寫檢查和建議。
- **鍵盤**–控制針對輸入顯示的鍵盤樣式，因此鍵盤上可用的按鍵。 這包括數位板、手機 Pad、電子郵件、URL 和其他選項。
- **外觀**–控制鍵盤的外觀樣式，而且會有深色或淺色的主題。
- 傳回**金鑰**–變更傳回金鑰上的標籤，以更清楚地反映將採取的動作。 支援的值包括 Go、Join、Next、Route、Done 和 Search。
- **Secure** –識別輸入是否已遮罩（例如輸入密碼）。

如果使用設計工具`textfield1`將名為的 UITextField 加入至螢幕，您可以在中C#設定或變更其屬性，如下所示：

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

在適當的情況下，Xamarin 會提供列舉，讓您可以輕鬆地選取您想要的`UIKeyboardType`設定`UIReturnKeyType` ，例如上述程式碼片段中的和。

### <a name="display-text-programmatically"></a>以程式設計方式顯示文字

如果您不想要使用設計工具設計您的畫面，或是想要在執行時間動態新增一些文字，您可以在視圖控制器的`ViewDidLoad`方法中，以程式設計方式建立並顯示 UITextField，如下所示：

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView" />

## <a name="uitextview"></a>UITextView

`UITextView`控制項可以用來顯示唯讀文字，或接受多行文字輸入。 它有許多與相同的選項`UITextField` （例如大小寫、更正等等）。

 [![](text-input-images/image16a.png "UITextView 屬性")](text-input-images/image16a.png#lightbox)

特定屬性包括：

- **行為**–文字是否可編輯或為唯讀。
- **偵測**–偵測並將輸入的資料轉換成可按的元素，例如可以觸發電話的電話號碼、會變成地圖連結的位址、在 Safari 中開啟的 url，或是在行事曆中變成事件的日期和時間。

如果已使用設計工具將 UITextView 加入至螢幕，您可以設定或變更其屬性，如下所示：

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```

## <a name="related-links"></a>相關連結

- [控制項（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
