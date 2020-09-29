---
title: Android 語音
description: 本文涵蓋使用功能強大的 Android. 語音命名空間的基本概念。 自開始，Android 已能夠辨識語音並將其輸出為文字。 這是相當簡單的程式。 不過，針對文字轉換語音，此程式更牽涉到，因為語音引擎不僅必須列入考慮，而且也可以從文字轉換語音 (TTS) 系統提供和安裝的語言。
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/02/2018
ms.openlocfilehash: ce94179fe0ae7f3df1f2fa5ead20ec09fd6f8f5e
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457909"
---
# <a name="android-speech"></a>Android 語音

_本文涵蓋使用功能強大的 Android. 語音命名空間的基本概念。自開始，Android 已能夠辨識語音並將其輸出為文字。這是相當簡單的程式。不過，針對文字轉換語音，此程式更牽涉到，因為語音引擎不僅必須列入考慮，而且也可以從文字轉換語音 (TTS) 系統提供和安裝的語言。_

## <a name="speech-overview"></a>語音總覽

有一個系統，它會「瞭解」人類語音，並 enunciates 輸入的內容（語音轉換文字和文字轉換語音）是行動裝置開發中不斷成長的領域，因為與我們的裝置進行自然通訊的需求會上升。 有許多情況下，具有將文字轉換成語音的功能（反之亦然）是一個非常有用的工具，可併入您的 android 應用程式。

例如，當行動電話上的行動電話使用時，使用者希望能以手上自由的方式操作裝置。 不同 Android 外型規格（例如 Android 磨損）的眾多，以及可使用 Android 裝置 (例如平板電腦和便箋板) 的大量擴展，已在絕佳的 TTS 應用程式上建立更大的焦點。

Google 為開發人員提供了一組豐富的 Android. 語音命名空間 Api，以涵蓋使裝置「語音感知」 (的大部分實例，例如專為盲人) 設計的軟體。  命名空間包含可讓文字轉譯成語音的功能 `Android.Speech.Tts` 、控制用來執行翻譯的引擎，以及 `RecognizerIntent` 允許將語音轉換成文字的數個。

雖然要瞭解的是要瞭解的工具，但根據使用的硬體，會有一些限制。 裝置不太可能會在每個可用的語言中，成功地解讀所有所說的內容。

## <a name="requirements"></a>需求

除了具有麥克風和喇叭的裝置之外，本指南並沒有特殊需求。

Android 裝置解讀語音的核心是與對應的搭配使用 `Intent` `OnActivityResult` 。
不過，請務必辨識語音無法理解，而是轉譯為文字。 差異很重要。

### <a name="the-difference-between-understanding-and-interpreting"></a>瞭解與解讀之間的差異

有一個簡單的理解定義，就是您能夠以語氣和內容來判斷所說的真正意義。 只是要解讀單字，並將它們輸出到另一種形式。

請考慮下列在日常對話中使用的簡單範例：

<kbd>喂，你好嗎？</kbd>

如果沒有變化 (將重點放在) 的特定單字或部分，這是一個簡單的問題。 但是，如果行的速度變慢，接聽者將會偵測到提問者不太滿意，也可能需要歡呼，或要求者 unwell。 如果強調是放在「是」，則詢問的人員通常會對回應感興趣。

如果沒有相當強大的音訊處理來使用變化，以及某種程度的人工智慧 (AI) 來瞭解內容，軟體甚至無法開始瞭解這是什麼，只要簡單的電話就可以將語音轉換成文字。

## <a name="setting-up"></a>設定

在使用語音系統之前，一定要先檢查以確定裝置有麥克風。 在未安裝麥克風的 Kindle 或 Google note 板上，不會有一點嘗試執行您的應用程式。

下列程式碼範例示範如何查詢麥克風是否可用，以及是否要建立警示。 如果此時沒有可用的麥克風，您可能會結束活動或停用錄製語音的功能。

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

### <a name="creating-the-intent"></a>建立意圖

語音系統的目的是使用一種稱為的特定意圖 `RecognizerIntent` 。 此意圖會控制大量的參數，包括等待無回應的時間長度，直到記錄被視為超過、辨識和輸出的任何其他語言，以及要包含在強制回應對話方塊中的任何文字，以 `Intent` 做為指令。 在此程式碼片段中， `VOICE` 是 `readonly int` 用來辨識的 `OnActivityResult` 。

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

### <a name="conversion-of-the-speech"></a>語音轉換

從語音中轉譯的文字將會在中傳遞 `Intent` ，這會在活動完成並透過存取時傳回 `GetStringArrayListExtra(RecognizerIntent.ExtraResults)` 。 這會傳回 `IList<string>` ，其中的索引可以使用和顯示，視呼叫端意圖中要求的語言數目 (以及在) 中指定而定 `RecognizerIntent.ExtraMaxResults` 。 不過，就像任何清單一樣，請務必檢查以確定有資料可顯示。

當您接聽的傳回值時 `StartActivityForResult` ，必須 `OnActivityResult` 提供方法。

在下列範例中， `textBox` 是 `TextBox` 用來輸出已聽寫的內容。 它也可以用來將文字傳遞至某種形式的解譯器，並從該處將文字和分支與應用程式的另一個部分進行比較。

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
                switch (matches[0].Substring(0, 5).ToLower())
                {
                    case "north":
                        MovePlayer(0);
                        break;
                    case "south":
                        MovePlayer(1);
                        break;
                }
            }
            else
            {
                textBox.Text = "No speech was recognised";
            }
        }
        base.OnActivityResult(requestCode, resultVal, data);
    }
}
```

## <a name="text-to-speech"></a>文字轉換語音

文字轉換語音不是語音轉換文字的相反，而且依賴兩個主要元件;安裝在裝置上的文字轉換語音引擎，以及要安裝的語言。

大部分情況下，Android 裝置會隨附預設的 Google TTS 服務，以及至少一個語言。 這是在裝置第一次設定時建立，而且會根據裝置當時的位置 (例如，德國的電話設定會安裝德文語言，而北美洲的電話將會有美國英文) 。

### <a name="step-1---instantiating-texttospeech"></a>步驟 1-具現化 TextToSpeech

`TextToSpeech` 最多可能需要3個參數，第二個是選擇性的 (`AppContext` ， `IOnInitListener` `engine`) 。 接聽程式可用來系結至服務，並測試引擎是否有任意數量的可用 Android 文字轉換語音引擎。 裝置至少會有 Google 本身的引擎。

### <a name="step-2---finding-the-languages-available"></a>步驟 2-尋找可用的語言

`Java.Util.Locale`類別包含一個實用的方法，稱為 `GetAvailableLocales()` 。 然後，您可以針對已安裝的語言，測試語音引擎所支援的語言清單。

產生「已瞭解」語言的清單是很簡單的事。 當使用者第一次將裝置設定為) 時，一律會有預設語言 (使用者設定的語言，因此在此範例中 `List<string>` ，會將 "default" 做為第一個參數，並根據的結果填入清單的其餘部分 `textToSpeech.IsLanguageAvailable(locale)` 。

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

此程式碼會呼叫 [TextToSpeech](xref:Android.Speech.Tts.TextToSpeech.IsLanguageAvailable*) ，以測試特定地區設定的語言套件是否已存在於裝置上。
這個方法會傳回 [LanguageAvailableResult](xref:Android.Speech.Tts.LanguageAvailableResult)，指出傳遞的地區設定的語言是否可用。 如果 `LanguageAvailableResult` 表示語言為，則 `NotSupported` 即使是該語言的下載) ，也沒有可用的語音套件 (。 如果 `LanguageAvailableResult` 設定為，則可以 `MissingData` 下載新的語言套件，如下一節的步驟4所述。

### <a name="step-3---setting-the-speed-and-pitch"></a>步驟 3-設定速度和音調

Android 可讓使用者藉由改變 `SpeechRate` 和 `Pitch` (語音) 的速度和色調，來改變語音的音效。 這會從0到1，且兩者的「正常」語音都是1。

### <a name="step-4---testing-and-loading-new-languages"></a>步驟 4-測試和載入新的語言

您可以使用來執行下載新的語言 `Intent` 。 此意圖的結果會導致叫用 [>detectandframe](xref:Android.App.Activity.OnActivityResult*) 方法。 不同于語音轉換文字範例 (使用 [RecognizerIntent](xref:Android.Speech.RecognizerIntent) 做為 `PutExtra`) 的參數 `Intent` ，測試和載入 `Intent` 是以 `Action` 基礎：

- [TextToSpeech](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionCheckTtsData)會 &ndash; 從平臺引擎啟動活動 `TextToSpeech` ，以確認裝置上的語言資源有適當的安裝和可用性。

- [TextToSpeech](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionInstallTtsData) &ndash; 會啟動一個活動，提示使用者下載必要的語言。

下列程式碼範例將說明如何使用這些動作來測試語言資源，並下載新的語言：

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

`TextToSpeech.Engine.ActionCheckTtsData` 測試語言資源的可用性。 `OnActivityResult` 當此測試完成時，即會叫用。 如果需要下載語言資源，則會 `OnActivityResult` 引發 `TextToSpeech.Engine.ActionInstallTtsData` 動作來啟動活動，讓使用者下載必要的語言。 請注意，此 `OnActivityResult` 執行不會檢查程式 `Result` 代碼，因為在這個簡化的範例中，已確定必須下載語言套件。

此 `TextToSpeech.Engine.ActionInstallTtsData` 動作會將 **Google TTS 語音資料** 活動呈現給使用者，以選擇要下載的語言：

![Google TTS 語音資料活動](speech-images/01-google-tts-voice-data.png)

例如，使用者可能會挑選法文並按一下下載圖示來下載法文語音資料：

![下載法文語言的範例](speech-images/02-selecting-french.png)

此資料的安裝會在下載完成後自動執行。

### <a name="step-5---the-ioninitlistener"></a>步驟 5-IOnInitListener

若要讓活動能夠將文字轉換成語音，必須執行介面方法 `OnInit` (這是為類別具現化指定的第二個參數 `TextToSpeech`) 。 這會初始化接聽程式並測試結果。

接聽程式應該 `OperationResult.Success` 至少測試和 `OperationResult.Failure` 。
下列範例只顯示：

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

## <a name="summary"></a>摘要

在本指南中，我們已探討將文字轉換成語音和語音轉換成文字的基本概念，以及如何將它們包含在您自己的應用程式中的可能方法。 雖然它們並未涵蓋每個特定案例，但您現在應該對語音的解讀方式有基本的瞭解、如何安裝新的語言，以及如何增加應用程式的包容性。

## <a name="related-links"></a>相關連結

- [Xamarin.Forms DependencyService](/samples/xamarin/xamarin-forms-samples/dependencyservice//)
- [文字轉換語音 (範例) ](/samples/xamarin/monodroid-samples/platformfeatures-texttospeech)
- [語音轉換文字 (範例) ](/samples/xamarin/monodroid-samples/platformfeatures-speechtotext)
- [Android. 語音命名空間](xref:Android.Speech)
- [Android. 語音名稱命名空間](xref:Android.Speech.Tts)