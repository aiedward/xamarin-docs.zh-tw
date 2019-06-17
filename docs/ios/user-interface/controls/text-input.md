---
title: 在 Xamarin.iOS 中的文字輸入
description: 本文件說明在 Xamarin.iOS 應用程式中的文字輸入。 它討論使用 UITextField 和 UITextVIew，以程式設計方式和 「 iOS 設計工具中。
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: a4d23984d4fcfd0776fd6b3537d5a257f70e7837
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827121"
---
# <a name="text-input-in-xamarinios"></a>在 Xamarin.iOS 中的文字輸入

接受使用者文字輸入以完成`UITextField`單行輸入和多行編輯文字的 UITextView。 您可以拖曳任一個控制項到螢幕，並按兩下，設定初始文字。

以下螢幕擷取畫面顯示位於 for Mac 在 Visual Studio 中的 [工具箱] 面板的這些控制項的圖示：

 [![](text-input-images/image11a.png "UITextField")](text-input-images/image11a.png#lightbox)

 [![](text-input-images/image13a.png "UITextView")](text-input-images/image13a.png#lightbox)

一旦您已命名輸出，並儲存分鏡腳本檔案，Visual Studio for Mac 將會更新`.designer.cs`部分類別，您可以新增C#參考至類別檔中的控制項的程式碼。 每個控制項都有它自己的唯一的屬性和事件，可於您C#程式碼。

 <a name="UITextField" />


## <a name="uitextfield"></a>UITextField

`UITextField`控制項通常會用來接受單行文字輸入，例如使用者名稱或密碼。 部分可供自訂控制項的選項如下所示：

 [![](text-input-images/image15a.png "UITextField 屬性")](text-input-images/image15a.png#lightbox)

這些控制項的說明如下：

-  **預留位置**– 這是選擇性的。 如果設定，它會顯示在文字欄位是空的通常是為了向使用者說明哪些輸入預期。
-  **清除按鈕**– 這會控制當標準的清除 按鈕 （具有 (X) 灰色的圓形） 會顯示在文字欄位中，為使用者快速純文字的方式。 它可以是永久隱藏、 永久可見的或顯示，取決於正在編輯的欄位。
-  **最小字型大小**並**調整至適當比例**– 可讓自動調整，容納較長的文字，並避免截斷，但受限於字型大小為否小於指定的大小。
-  **大小寫**– 是否要自動大寫單字、 句子或所有的輸入。
-  **更正**– 是否已啟用拼字檢查和建議。
-  **鍵盤**– 控制項的鍵盤樣式顯示，針對輸入，並因此何種都是可用鍵盤上。 這包括數字鍵台、 Phone 填補、 電子郵件、 URL 及其他選項。
-  **外觀**– 控制項的鍵盤外觀樣式和的任一深或亮色調佈景主題。
-  **傳回索引鍵**– 變更傳回的索引鍵，以更精確反映所要採取的動作標籤。 支援的值包括 Go、 聯結、 下一步，路由，完成之後和搜尋。
-  **安全**– 識別是否已遮罩輸入 （例如密碼輸入）。


如果呼叫 UITextField`textfield1`已新增至螢幕設計工具中，您可以設定或變更其屬性，在C#，如下所示：

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

Xamarin.iOS，以輕鬆地選取您要的設定，例如，提供列舉`UIKeyboardType`和`UIReturnKeyType`在上面的程式碼片段。

### <a name="display-text-programmatically"></a>以程式設計的方式顯示文字

如果您不想要與設計工具設計螢幕，或如果您想要以動態方式在執行階段加入一些文字，您可以建立並顯示以程式設計方式在 UITextField`ViewDidLoad`方法的檢視控制器，就像這樣：

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView" />


## <a name="uitextview"></a>UITextView

`UITextView`控制項可以用來顯示唯讀文字，或接受多行文字輸入。 它有許多相同的選項為`UITextField`（例如大寫字、 更正等）。

 [![](text-input-images/image16a.png "UITextView 屬性")](text-input-images/image16a.png#lightbox)

特定的屬性包括：

-  **行為**– 文字是否為可編輯或唯讀狀態。
-  **偵測**– 偵測，並將轉換為可點按的項目資料不經例如電話號碼可以觸發呼叫，解決成為連結至對應，在 Safari 中開啟的 Url 或日期和時間，成為一名事件行事曆。


如果 UITextView 已加入至螢幕與設計工具，您可以設定或變更其屬性，像這樣：

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```



## <a name="related-links"></a>相關連結

- [控制項 （範例）](https://developer.xamarin.com/samples/monotouch/Controls/)
