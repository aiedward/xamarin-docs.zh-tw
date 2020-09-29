---
title: 在 Xamarin 中輸入文字
description: 本檔說明在 Xamarin iOS 應用程式中輸入的文字。 它會討論如何以程式設計方式和在 iOS 設計工具中使用 UITextField 和 UITextVIew。
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: e8f8c2196edd5450ca2949d59c2fc06ff62274b7
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435375"
---
# <a name="text-input-in-xamarinios"></a>在 Xamarin 中輸入文字

接受使用者文字輸入的作業是使用 `UITextField` 適用于多行可編輯文字的單一行輸入和 UITextView 來完成。 您可以將其中一個控制項拖曳到螢幕上，然後按兩下以設定初始文字。

下列螢幕擷取畫面顯示這些控制項的圖示，這些控制項位於 Visual Studio for Mac 的 [工具箱] 面板中：

 [![UITextField](text-input-images/image11a.png)](text-input-images/image11a.png#lightbox)

 [![UITextView](text-input-images/image13a.png)](text-input-images/image13a.png#lightbox)

當您命名輸出並儲存分鏡腳本檔案之後，Visual Studio for Mac 將會更新 `.designer.cs` 部分類別，您可以將參考控制項的 c # 程式碼加入至您的類別檔案。 每個控制項都有自己的唯一屬性和事件，可在您的 c # 程式碼中存取。

 <a name="UITextField"></a>

## <a name="uitextfield"></a>UITextField

`UITextField`控制項最常用來接受單行文字輸入，例如使用者名稱或密碼。 以下顯示一些可供自訂控制項的選項：

 [![UITextField 屬性](text-input-images/image15a.png)](text-input-images/image15a.png#lightbox)

以下將說明這些控制項：

- **預留位置** –這是選擇性的。 如果設定，則會在文字欄位空白時顯示，通常會向使用者說明預期的輸入。
- **清除按鈕** ：當標準清除按鈕 (灰色圓圈（ (X) # A3 出現在文字欄位中）時，此控制項可讓使用者快速清除文字。 您可以根據欄位是否正在編輯，永久隱藏、永久可見或顯示。
- [**字型大小**] 和 [**調整成適當**大小] –允許自動調整字型大小以容納較長的文字，並防止截斷，但限制為小於指定的大小。
- **大小寫** –是否自動大寫單字、句子或所有輸入。
- **更正** –是否啟用拼寫檢查和建議。
- **鍵盤** –控制針對輸入顯示的鍵盤樣式，以及鍵盤上可用的按鍵。 這包括數位板、手機板、電子郵件、URL 和其他選項。
- **外觀** –控制鍵盤的外觀樣式，並會有深色或淺色主題。
- 傳回**金鑰**–變更傳回金鑰上的標籤，以更清楚地反映將採取的動作。 支援的值包括 Go、Join、Next、Route、Done 和 Search。
- **安全** –識別輸入是否遮罩 (例如輸入密碼) 。

如果已使用設計工具將呼叫的 UITextField `textfield1` 新增至畫面，您可以在 c # 中設定或變更其屬性，如下所示：

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

Xamarin 會提供適當的列舉，讓您輕鬆地選取您想要的設定，例如 `UIKeyboardType` `UIReturnKeyType` 上述程式碼片段中的和。

### <a name="display-text-programmatically"></a>以程式設計方式顯示文字

如果您不想要使用設計工具來設計您的畫面，或是想要在執行時間動態加入一些文字，可以在視圖控制器的方法中，以程式設計方式建立並顯示 UITextField，如下所示 `ViewDidLoad` ：

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView"></a>

## <a name="uitextview"></a>UITextView

`UITextView`控制項可以用來顯示唯讀文字或接受多行文字輸入。 它有許多與 (相同的選項 `UITextField` ，例如大小寫、更正等) 。

 [![UITextView 屬性](text-input-images/image16a.png)](text-input-images/image16a.png#lightbox)

特定屬性包括：

- **行為** –文字是否為可編輯或唯讀。
- **偵測** -偵測並將輸入的資料轉換成可按的元素，例如可觸發通話的電話號碼、會成為地圖連結的位址、在 Safari 中開啟的 url，或是在行事曆中成為事件的日期和時間。

如果已使用設計工具將 UITextView 新增至畫面，您可以設定或變更其屬性，如下所示：

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```

## <a name="related-links"></a>相關連結

- [控制項 (範例) ](/samples/xamarin/ios-samples/controls)