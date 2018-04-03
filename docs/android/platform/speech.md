---
title: Android 的語音
description: 本文件涵蓋使用非常強大的 Android.Speech 命名空間的基本概念。 自其開始，Android 已能夠辨識語音並將其輸出為文字。 它是相對較簡單的程序。 針對文字轉換語音，不過，處理程序是較為複雜，因為不僅不語音引擎一定納入考量，但也語言可用及已安裝從文字轉換語音 (TTS) 系統。
ms.topic: article
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/02/2018
ms.openlocfilehash: acc64fee37e1a6046991355389a09a29e1889993
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="android-speech"></a>Android 的語音

_本文件涵蓋使用非常強大的 Android.Speech 命名空間的基本概念。自其開始，Android 已能夠辨識語音並將其輸出為文字。它是相對較簡單的程序。針對文字轉換語音，不過，處理程序是較為複雜，因為不僅不語音引擎一定納入考量，但也語言可用及已安裝從文字轉換語音 (TTS) 系統。_

## <a name="speech-overview"></a>語音概觀

系統的 「 了解 「 人力語音和 enunciates 輸入的內容 — 文字與文字轉換語音的語音 — 用於與我們裝置自然通訊需求上升時不斷成長的區域，在行動應用程式開發中。 有許多執行個體其中有一項功能，將文字轉換語音，或反之亦然，是非常有用的工具，以併入您的 android 應用程式。

比方說，有縮短時間差向行動電話使用開車時，使用者想指針可用這種運作他們的裝置。 不同 Android 表單係數的 — 例如 Android 穿 — 而且曾經擴展包含那些能夠使用 Android 裝置 （例如平板電腦和附註填補） 具有較大的焦點上建立絕佳 TTS 應用程式。

Google 提供一組豐富的應用程式開發介面 Android.Speech 命名空間中來涵蓋大部份的情況中讓 「 語音感知 」 （例如軟體設計的 blind） 裝置的開發人員。  命名空間包含的設備，以允許文字轉換成語音透過`Android.Speech.Tts`，引擎用來轉譯，以及一些執行控制權`RecognizerIntent`s 允許語音來轉換成文字。

雖然功能有了解的語音，有一些限制所使用的硬體為基礎。 不太裝置已成功將解譯說出它在每個可用的語言中的所有項目。

## <a name="requirements"></a>需求

沒有任何特殊需求，如本指南中，您的裝置具有麥克風和喇叭以外。

解譯語音 Android 裝置的核心是使用`Intent`與對應`OnActivityResult`。
很重要，不過，辨識語音不了解，但解譯為文字。 差異就很重要。

### <a name="the-difference-between-understanding-and-interpreting"></a>了解與解譯的差異

了解簡單定義是，您可以判斷音調和內容正在所說的真正的意義。 若要解譯只表示文字，並在另一種形式對它們進行輸出。

請考慮下列簡單的範例是用於日常對話： 

<kbd>您好，如何？</kbd>

沒有變化 （強調放在特定的單字或單字的部分），就可以很簡單的問題。 不過，如果緩慢的步調套用到線條時，接聽的人將會偵測或發問不滿意，而且可能需要揮手發問不開心。 如果重點放在 「 是 」，要求的人員是通常更有興趣的回應。

而沒有相當強大處理使用的音訊的變化和某種程度的人工地智慧 (AI) 用於了解的內容、 軟體甚至無法開始了解什麼說： 簡單電話做到的最好是將語音轉換成文字。

## <a name="setting-up"></a>設定

使用語音系統之前, 永遠是個明智的選擇檢查並確定裝置有麥克風。 會嘗試執行您的應用程式不會安裝麥克風 Kindle 或 Google 的附註板很少的點。

下列程式碼範例示範查詢麥克風是否可用，以及如果不是，若要建立警示。 如果沒有麥克風功能此時您會停止活動或是停用記錄語音的功能。

```csharp
string rec = Android.Content.PM.PackageManager.FeatureMicrophone;
if (rec != "android.hardware.microphone")
{
    var alert = new AlertDialog.Builder(recButton.Context);
    alert.SetTitle("You don't seem to have a microphone to record with");
    alert.SetPositiveButton("OK", (sender, e) =>
    {
        return;
    });
    alert.Show();
}
```

### <a name="creating-the-intent"></a>建立目的

語音系統的對應方式使用特定類型的意圖呼叫`RecognizerIntent`。 此意圖控制大量的參數，包括時間等待無回應的記錄會被視為透過任何其他語言辨識，並輸出，以及要包含在任何文字`Intent`的強制回應對話方塊，做為方法的指示。 在此程式碼片段，`VOICE`是`readonly int`用於辨識`OnActivityResult`。

```csharp
var voiceIntent = new Intent(RecognizerIntent.ActionRecognizeSpeech);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguageModel, RecognizerIntent.LanguageModelFreeForm);
voiceIntent.PutExtra(RecognizerIntent.ExtraPrompt, Application.Context.GetString(Resource.String.messageSpeakNow));
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputPossiblyCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputMinimumLengthMillis, 15000);
voiceIntent.PutExtra(RecognizerIntent.ExtraMaxResults, 1);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguage, Java.Util.Locale.Default);
StartActivityForResult(voiceIntent, VOICE);
```

### <a name="conversion-of-the-speech"></a>語音的轉換

從語音解譯的文字將會傳遞內`Intent`，傳回當活動已完成，而且透過存取`GetStringArrayListExtra(RecognizerIntent.ExtraResults)`。 這會傳回`IList<string>`，而其索引使用和顯示，而是根據在呼叫端意圖中要求的語言數目 (以及中所指定`RecognizerIntent.ExtraMaxResults`)。 如同任何清單，很值得檢查，以確定沒有可顯示資料。

當傳回值的接聽`StartActivityForResult`、`OnActivityResult`方法有提供。

在下列範例中，`textBox`是`TextBox`用來輸出項目具有指示。 它無法同樣用來將文字傳遞到某種形式的解譯器，並從該處，應用程式可以比較的文字和應用程式的另一個組件的分支。

```csharp
protected override void OnActivityResult(int requestCode, Result resultVal, Intent data)
{
    if (requestCode == VOICE)
    {
        if (resultVal == Result.Ok)
        {
            var matches = data.GetStringArrayListExtra(RecognizerIntent.ExtraResults);
             if (matches.Count != 0)
             {
                  string textInput = textBox.Text + matches[0];
                  textBox.Text = textInput;
                  switch(matches[0].Substring(0,5).ToLower())
                  {
                     case "north":
                      MovePlayer(0);
                     break;
                   case "south":
                     MovePlayer(1);
                     break;
             }
             else
                  textBox.Text = "No speech was recognised";
        }
   }
    base.OnActivityResult(requestCode, resultVal, data);
}
```

## <a name="text-to-speech"></a>文字轉換語音

文字轉換語音非常相反的語音文字並不依賴兩個主要元件。正在安裝於裝置及安裝的語言的文字轉換語音引擎。

大部分，Android 裝置隨附的預設安裝 Google TTS 服務和至少一種語言。 這所建立的裝置已先設定，並會根據當時的裝置的位置時 （比方說，電話設定境內的德國人會安裝德文語言，而其中一個地區會有美式英文）。

### <a name="step-1---instantiating-texttospeech"></a>步驟 1-具現化 TextToSpeech

`TextToSpeech` 可能需要最多 3 個參數前, 兩個是必要項目，第三個為選擇性 (`AppContext`， `IOnInitListener`， `engine`)。 接聽程式用來繫結的服務和失敗的測試與正在使用 Android 的文字轉換語音引擎的引擎。 最少，裝置必須 Google 自己引擎。

### <a name="step-2---finding-the-languages-available"></a>步驟 2-尋找可用的語言

`Java.Util.Locale`類別包含有用的方法呼叫`GetAvailableLocales()`。 針對已安裝的語言可以測試此清單中的語音引擎所支援的語言。

它是毫無用處產生的 「 了解 「 語言清單。 一律會以預設語言 （使用者設定當他們第一次設定他們的裝置語言），因此在此範例`List<string>`"Default"做為第一個參數，清單的其餘部分將依據的結果會填滿`textToSpeech.IsLanguageAvailable(locale)`。

```csharp
var langAvailable = new List<string>{ "Default" };
var localesAvailable = Java.Util.Locale.GetAvailableLocales().ToList();
foreach (var locale in localesAvailable)
{
    var res = textToSpeech.IsLanguageAvailable(locale);
    switch (res)
    {
        case LanguageAvailableResult.Available:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryVarAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
    }
}
langAvailable = langAvailable.OrderBy(t => t).Distinct().ToList();
```

此程式碼會呼叫[TextToSpeech.IsLanguageAvailable](https://developer.xamarin.com/api/member/Android.Speech.Tts.TextToSpeech.IsLanguageAvailable/p/Java.Util.Locale/)以測試是否為特定地區設定的語言套件已存在於裝置上。 這個方法會傳回[LanguageAvailableResult](https://developer.xamarin.com/api/type/Android.Speech.Tts.LanguageAvailableResult/)，表示是否可以使用傳入的地區設定的語言。 如果`LanguageAvailableResult`指出的語言是`NotSupported`，沒有可以 （即使下載） 的語音的封裝，然後針對該語言。 如果`LanguageAvailableResult`設`MissingData`，它就可能會在步驟 4 中，如下所述，下載新的語言套件。

### <a name="step-3---setting-the-speed-and-pitch"></a>步驟 3-設定速度及字距等性質

Android 可讓使用者可以改變修改語音聲音`SpeechRate`和`Pitch`（速度及音調的語音速率）。 這是從 0 到 1，使用 「 一般 」 是兩個 1 的語音。

### <a name="step-4---testing-and-loading-new-languages"></a>步驟 4-測試及載入新的語言

下載新的語言透過執行`Intent`。 此意圖結果導致[OnActivityResult](https://developer.xamarin.com/api/member/Android.App.Activity.OnActivityResult/)方法被叫用。 不同於語音轉文字範例 (使用哪一個[RecognizerIntent](https://developer.xamarin.com/api/type/Android.Speech.RecognizerIntent/)為`PutExtra`參數`Intent`)，測試及載入`Intent`是`Action`-基礎：

-   [TextToSpeech.Engine.ActionCheckTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionCheckTtsData/) &ndash;啟動活動從平台`TextToSpeech`引擎來確認正確的安裝和裝置上的語言資源的可用性。

-   [TextToSpeech.Engine.ActionInstallTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionInstallTtsData/) &ndash;開始活動，以提示使用者下載必需的語言。

下列程式碼範例將說明如何使用這些動作測試的語言資源，並下載新的語言：

```csharp
var checkTTSIntent = new Intent();
checkTTSIntent.SetAction(TextToSpeech.Engine.ActionCheckTtsData);
StartActivityForResult(checkTTSIntent, NeedLang);
//
protected override void OnActivityResult(int req, Result res, Intent data)
{
    if (req == NeedLang)
    {
        var installTTS = new Intent();
        installTTS.SetAction(TextToSpeech.Engine.ActionInstallTtsData);
        StartActivity(installTTS);
    }
}
```

`TextToSpeech.Engine.ActionCheckTtsData` 測試的語言資源的可用性。 `OnActivityResult` 這項測試完成時，會叫用。 如果要下載的語言資源需要`OnActivityResult`引發`TextToSpeech.Engine.ActionInstallTtsData`動作啟動活動，可讓使用者下載必需的語言。 請注意這個`OnActivityResult`實作不會檢查`Result`因為簡化在本例中，判斷已發出的語言封裝必須先下載程式碼。

`TextToSpeech.Engine.ActionInstallTtsData`動作原因**Google TTS 語音資料**要選擇要下載的語言呈現給使用者的活動：

![Google TTS 語音資料活動](speech-images/01-google-tts-voice-data.png)

例如，使用者可能會挑選法文，並按一下 [下載] 圖示，以下載法文語音資料：

![下載法文語言的範例](speech-images/02-selecting-french.png)

下載完成後，會自動發生這項資料的安裝。


### <a name="step-5---the-ioninitlistener"></a>步驟 5-IOnInitListener

活動無法轉換文字轉換語音，介面方法`OnInit`已實作 (這是指定的具現化的第二個參數`TextToSpeech`類別)。 這會初始化接聽程式，並測試結果。

接聽程式應該測試兩個`OperationResult.Success`和`OperationResult.Failure`最少。
下列範例顯示只：

```csharp
void TextToSpeech.IOnInitListener.OnInit(OperationResult status)
{
    // if we get an error, default to the default language
    if (status == OperationResult.Error)
        textToSpeech.SetLanguage(Java.Util.Locale.Default);
    // if the listener is ok, set the lang
    if (status == OperationResult.Success)
        textToSpeech.SetLanguage(lang);
}
```

## <a name="summary"></a>總結

本指南中我們已經探討將文字轉換語音和語音轉換成文字及如何將它們包含您自己的應用程式內的可能方法的基本概念。 雖然它們不會討論每個案例中，您現在應該有基本了解如何解譯語音、 如何安裝新的語言，以及如何增加您的應用程式 inclusivity。



## <a name="related-links"></a>相關連結

- [Xamarin.Forms DependencyService](https://developer.xamarin.com/samples/UsingDependencyService/)
- [文字轉換語音 （範例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TextToSpeech)
- [語音轉換文字 （範例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SpeechToText)
- [Android.Speech 命名空間](https://developer.xamarin.com/api/namespace/Android.Speech/)
- [Android.Speech.Tts 命名空間](https://developer.xamarin.com/api/namespace/Android.Speech.Tts/)
