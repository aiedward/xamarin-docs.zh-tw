---
title: 安卓語音
description: 本文介紹了使用非常強大的Android.語音命名空間的基礎知識。 自成立以來,Android 已經能夠識別語音並將其輸出為文本。 這是一個相對簡單的過程。 但是,對於文本到語音,該過程涉及更多,因為不僅必須考慮語音引擎,還必須考慮到文本到語音 (TTS) 系統中可用的語言。
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/02/2018
ms.openlocfilehash: e8c7d1a4fb3537644ed3b7737158a5e50abcdae5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019760"
---
# <a name="android-speech"></a>安卓語音

_本文介紹了使用非常強大的Android.語音命名空間的基礎知識。自成立以來,Android 已經能夠識別語音並將其輸出為文本。這是一個相對簡單的過程。但是,對於文本到語音,該過程涉及更多,因為不僅必須考慮語音引擎,還必須考慮到文本到語音 (TTS) 系統中可用的語言。_

## <a name="speech-overview"></a>語音概述

隨著對設備自然通信的需求不斷增長,擁有一個"理解"人類語音並闡明正在鍵入的內容的系統(語音到文本和文本到語音)是移動開發中一個不斷增長的領域。 在很多情況下,具有將文本轉換為語音的功能,反之亦然,是一個非常有用的工具,可以合併到您的 Android 應用程式中。

例如,隨著在駕駛時限制使用手機,使用者希望採用免提操作設備的方式。 大量不同的 Android 外形規格(如 Android 磨損)以及能夠使用 Android 設備(如平板電腦和記事本)的不斷增加,都引發了對出色的 TTS 應用程式的更大關注。

Google 在 Android. 語音命名空間為開發人員提供了一組豐富的 API,以涵蓋大多數使設備「語音感知」(例如為盲人設計的軟體)的實例。  命名空間包括允許文本通過`Android.Speech.Tts`、控制用於執行翻譯的引擎以及允許將語音轉換為文本`RecognizerIntent`的多個功能。

雖然有語言可以理解的設施,但根據所使用的硬體存在限制。 設備不太可能成功解釋以每種可用語言與之交談的所有內容。

## <a name="requirements"></a>需求

本指南沒有特殊要求,除了您的設備有麥克風和揚聲器。

Android設備解釋語音的核心是使用`Intent`相應的`OnActivityResult`。
然而,重要的是要認識到,演講不被理解,但被解釋為文本。 區別很重要。

### <a name="the-difference-between-understanding-and-interpreting"></a>理解和解釋之間的區別

理解的一個簡單定義是,你能夠通過語氣和上下文確定所說的真正含義。 解釋只是意味著採取的話,並輸出它們以另一種形式。

請考慮在日常對話中使用的以下簡單範例:

<kbd>喂,你好嗎?</kbd>

沒有拐點(強調特定單詞或單詞的一部分),這是一個簡單的問題。 然而,如果對線路應用緩慢的步伐,傾聽的人會發現,阿斯克不太高興,也許需要振作起來,或者阿斯克人不自在。 如果強調"是",則詢問者通常對回答更感興趣。

如果沒有相當強大的音訊處理來利用拐點,以及一定程度的人工智慧(AI)來理解上下文,軟體甚至不能開始理解所說的內容——一個簡單的手機能做的最好的事情就是將語音轉換為文本。

## <a name="setting-up"></a>設定

在使用語音系統之前,最好檢查以確保設備有麥克風。 試圖在Kindle或谷歌記事本上運行你的應用程式,而不安裝麥克風就沒有什麼意義了。

下面的代碼示例演示了查詢麥克風是否可用,如果沒有,則查詢以創建警報。 如果此時沒有可用的麥克風,您將退出活動或禁用錄製語音的功能。

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

語音系統的意圖使用稱為的特定類型的意圖`RecognizerIntent`。 此意圖控制大量參數,包括等待多長時間,直到記錄被考慮結束,任何其他語言識別和輸出,以及任何文本包括在`Intent`''的模式對話框作為指令手段。 在此程式段中`VOICE`,`readonly int`用於`OnActivityResult`識別 。

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

從語音解釋的文字將在 中傳遞`Intent`, 活動完成後返回該文本,`GetStringArrayListExtra(RecognizerIntent.ExtraResults)`並通過 進行訪問。 這將返回一個`IList<string>`,其中索引可以使用和顯示,具體取決於調用方意圖中請求的語言數(並在`RecognizerIntent.ExtraMaxResults`中 指定)。 與任何清單一樣,值得檢查以確保顯示數據。

偵聽 的返回`StartActivityForResult`值 時,`OnActivityResult`必須提供 方法。

在下面的範例中,`textBox``TextBox`是用於提供已指示的內容。 它同樣可以用來將文本傳遞給某種形式的解釋器,從那裡,應用程式可以將文本和分支與應用程式的另一部分進行比較。

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

## <a name="text-to-speech"></a>文字轉語音

文本到語音並不完全相反的語音到文本,並依賴於兩個關鍵組成部分;設備上正在安裝的文本到語音轉換引擎和正在安裝的語言。

在很大程度上,Android 設備附帶預設的 Google TTS 服務安裝和至少一種語言。 這是在第一次設置設備時確定的,並且將基於設備當時的位置(例如,在德國安裝的手機將安裝德語,而在美國安裝一個手機將具有美國英語)。

### <a name="step-1---instantiating-texttospeech"></a>第一步 - 實體化文字到語音

`TextToSpeech`最多只能採用 3 個參數,前兩個參數是必需的,第三`AppContext`個`IOnInitListener`參數`engine`是 可選的 (,,。 偵聽器用於綁定到服務並測試引擎為語音引擎的任意數量的可用 Android 文字故障。 至少,該設備將有自己的引擎。

### <a name="step-2---finding-the-languages-available"></a>第二步 - 尋找可用的語言

類`Java.Util.Locale`包含一個`GetAvailableLocales()`稱為 的有用方法。 然後,可以根據已安裝的語言測試語音引擎支援的此語言清單。

生成"被理解"語言清單是件小事。 始終有預設語言(使用者首次設置設備時設置的語言),因此在此示例中,`List<string>`將「預設」作為第一個參數,將根據 的結果填充清單的其餘`textToSpeech.IsLanguageAvailable(locale)`部分。

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

此代碼調用[TextTo 語音.Is 語言可用](xref:Android.Speech.Tts.TextToSpeech.IsLanguageAvailable*)來測試設備上是否存在給定區域設置的語言包。
此方法返回[語言可用Result](xref:Android.Speech.Tts.LanguageAvailableResult),它指示傳遞區域設置的語言是否可用。 如果`LanguageAvailableResult`指示該語言為`NotSupported`,則該語言沒有可用的語音包(甚至可供下載)。 如果`LanguageAvailableResult`設置`MissingData`為 ,則可以下載新的語言包,如步驟 4 中所述。

### <a name="step-3---setting-the-speed-and-pitch"></a>步驟 3 - 設定速度與間距

Android 允許用戶通過`SpeechRate`更改`Pitch`和 (語音的速度和音調)來更改語音的聲音。 這從 0 到 1,兩者"正常"語音為 1。

### <a name="step-4---testing-and-loading-new-languages"></a>步驟 4 - 測試和載入新語言

下載新語言使用`Intent`執行 。 此意圖的結果會導致調用[OnActivityResult](xref:Android.App.Activity.OnActivityResult*)方法。 與語音到文字範例(使用[辨識器意圖](xref:Android.Speech.RecognizerIntent)`PutExtra`為`Intent`參數 )不同,測試和`Intent`載入`Action`式基於:

- [TextTo語音轉換.Engine.ActionCheckTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionCheckTtsData)&ndash;從`TextToSpeech`平台 引擎啟動活動,以驗證裝置上語言資源的正確安裝和可用性。

- [TextTo語音轉換.引擎.ActionInstallTsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionInstallTtsData)&ndash;啟動一項活動,提示使用者下載必要的語言。

以下代碼範例說明了如何使用這些操作來測試語言資源和下載新語言:

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

`TextToSpeech.Engine.ActionCheckTtsData`測試語言資源的可用性。 `OnActivityResult`此測試完成後調用。 如果需要下載語言資源,`OnActivityResult`請觸`TextToSpeech.Engine.ActionInstallTtsData`發 操作以啟動允許使用者下載必要語言的活動。 請注意,此`OnActivityResult`實現不檢查`Result`代碼,因為在此簡化示例中,已確定需要下載語言包。

該`TextToSpeech.Engine.ActionInstallTtsData`操作程式**讓 Google TTS 語音資料**活動呈現給使用者,以便選擇要下載的語言:

![谷歌TTS語音資料活動](speech-images/01-google-tts-voice-data.png)

例如,使用者可以選擇法語並按一下下載圖示以下載法語語音數據:

![下載法語的範例](speech-images/02-selecting-french.png)

下載完成後,將自動安裝此數據。

### <a name="step-5---the-ioninitlistener"></a>第 5 步 - IoninitListener

要使活動能夠將文本轉換為語音,必須實現介面方法`OnInit`(這是`TextToSpeech`為 類實例化指定的第二個參數)。 這將初始化偵聽器並測試結果。

偵聽器應同時`OperationResult.Success`測試`OperationResult.Failure`和 至少。
下面的範例僅顯示:

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

在本指南中,我們研究了將文本轉換為語音和語音到文本的基礎知識,以及如何將它們包含在您自己的應用程式中的可能方法。 雖然它們並不涵蓋每個特定情況,但您現在應該對如何解釋語音、如何安裝新語言以及如何提高應用的包容性有基本的瞭解。

## <a name="related-links"></a>相關連結

- [Xamarin.Forms DependencyService](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice//)
- [文字到語音(範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-texttospeech)
- [語音到文字(範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-speechtotext)
- [安卓.語音命名空間](xref:Android.Speech)
- [安卓.語音.Ts 命名空間](xref:Android.Speech.Tts)
