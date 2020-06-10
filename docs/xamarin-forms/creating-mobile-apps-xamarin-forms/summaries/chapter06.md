---
title： "第6章的摘要。 按鈕按一下 [描述：] 建立 Mobile Apps Xamarin.Forms ：第6章的摘要。 按鈕按一下 "ms. 生產： xamarin ms. 技術： assetid： D4F9C429-A6CF-40FA-AC68-3F149307A5F9 author： davidbritch ms-chap： dabritch ms. date： 07/18/2018 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="summary-of-chapter-6-button-clicks"></a>第6章的摘要。 按鈕點擊

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

[`Button`](xref:Xamarin.Forms.Button)是可讓使用者起始命令的視圖。 `Button`會以文字識別（以及選擇性的影像，如第[13 章的點陣圖](chapter13.md)所示）。 因此，會 `Button` 將許多相同的屬性定義為 `Label` ：

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button`也會定義三個控制其框線外觀的屬性，但這些屬性和其相互獨立性的支援是特定平臺：

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor)類型為`Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth)類型為`Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius)類型為`Double`

`Button`也會繼承和的所有 `VisualElement` 屬性 `View` ，包括 `BackgroundColor` 、 `HorizontalOptions` 和 `VerticalOptions` 。

## <a name="processing-the-click"></a>處理點擊

`Button`類別 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 會定義當使用者點擊時所引發的事件 `Button` 。 `Click`處理常式的類型為 `EventHandler` 。 第一個引數是 `Button` 產生事件的物件; 第二個引數是不 `EventArgs` 提供其他資訊的物件。

[**ButtonLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger)範例示範簡單 `Clicked` 的處理。

## <a name="sharing-button-clicks"></a>共用按鈕按一下

多個 `Button` views 可以共用相同的 `Clicked` 處理常式，但處理常式通常需要判斷哪個 `Button` 是負責特定事件的。 其中一個方法是將各種 `Button` 物件儲存為欄位，並檢查哪一個是在處理常式中引發事件。

[**TwoButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons)範例會示範這項技術。 此程式也 [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) `Button` 會示範 `false` 當按下不再 `Button` 有效時，如何將的屬性設定為。 已停用 `Button` 不會產生 `Clicked` 事件。

## <a name="anonymous-event-handlers"></a>匿名事件處理常式

`Clicked`如[**ButtonLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas)範例所示，您可以將處理常式定義為匿名 lambda 函式。 不過，如果沒有一些雜亂的反映程式碼，就無法共用匿名處理常式。

## <a name="distinguishing-views-with-ids"></a>區分識別碼的視圖

將 `Button` 屬性或屬性設定為，也可以區別多個物件 [`StyleId`](xref:Xamarin.Forms.Element.StyleId) [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) `string` 。 這個屬性是由所定義， `Element` 但不會在中使用 Xamarin.Forms 。 僅供應用程式使用。

[**SimplestKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad)範例會對數位鍵臺上的所有10個數字鍵使用相同的事件處理常式，並使用屬性來區分它們 `StyleId` ：

[![最簡單鍵盤的三向螢幕擷取畫面](images/ch06fg04-small.png "Calculator")](images/ch06fg04-large.png#lightbox "Calculator")

## <a name="saving-transient-data"></a>儲存暫時性資料

許多應用程式都需要在程式終止時儲存資料，並在程式重新開機時重載該資料。 [`Application`](xref:Xamarin.Forms.Application)類別會定義數個可協助您的程式儲存和還原暫時性資料的成員：

- [`Properties`](xref:Xamarin.Forms.Application.Properties)屬性是具有索引 `string` 鍵和專案的字典 `object` 。 字典的內容會在程式終止之前自動儲存在應用程式本機儲存體中，並在程式啟動時重載。
- `Application`類別會定義程式的標準類別所覆寫的三個受保護虛擬方法 `App` ： [`OnStart`](xref:Xamarin.Forms.Application.OnStart) 、 [`OnSleep`](xref:Xamarin.Forms.Application.OnSleep) 和 [`OnResume`](xref:Xamarin.Forms.Application.OnResume) 。 這些是指*應用程式生命週期*事件。
- 方法會儲存 [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) 字典的內容。

不需要呼叫 `SavePropertiesAsync` 。 字典的內容會在程式終止之前自動儲存，並在程式啟動前先行抓取。 在程式測試期間，如果程式損毀就儲存資料，這會很有用。

也適用于：

- [`Application.Current`](xref:Xamarin.Forms.Application.Current)，這是傳回目前物件的靜態屬性， `Application` 您可以用來取得 `Properties` 字典。

第一個步驟是在程式終止時，識別頁面上您想要保存的所有變數。 如果您知道這些變數的變更位置，只要將它們加入字典就可以了 `Properties` 。 在頁面的函式中，如果索引鍵存在，您可以從字典設定變數 `Properties` 。

較大的程式可能需要處理應用程式生命週期事件。 最重要的是 `OnSleep` 方法。 呼叫這個方法會指出程式已離開前景。 可能是使用者已按下裝置上的 [**首頁**] 按鈕，或顯示所有應用程式，或是正在關閉電話。 呼叫 `OnSleep` 是程式在終止之前所收到的唯一通知。 程式應該會有這個機會，以確保 `Properties` 字典是最新的。

的呼叫 `OnResume` 表示程式在上一次呼叫之後未終止， `OnSleep` 但現在正于前景中再次執行。 此程式可能會使用此機會來重新整理網際網路連線（例如）。

在 `OnStart` 程式啟動期間發生的呼叫。 您不需要等到這個方法呼叫來存取字典，因為在呼叫此函式 `Properties` 時，內容已經還原 `App` 。

[**PersistentKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad)範例與**SimplestKeypad**非常類似，不同之處在于程式會使用覆 `OnSleep` 寫來儲存目前的小數位數專案，以及頁面的函式來還原該資料。

> [!NOTE]
> 儲存程式設定的另一種方法是由 Xamarin.Essentials [喜好](~/essentials/preferences.md)設定類別所提供。

## <a name="related-links"></a>相關連結

- [第6章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [第6章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [第6章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Xamarin.FormsButton](~/xamarin-forms/user-interface/button.md)
