---
title: 搭配使用自然語言架構與 Xamarin
description: 本檔描述自然語言架構。 在 iOS 12 中引進, 自然語言架構是慣用的 iOS API, 用於語言辨識、語音辨識的元件, 以及命名實體識別。
ms.prod: xamarin
ms.assetid: 126C8764-F873-4EB9-98A3-D82AB5689111
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/20/2018
ms.openlocfilehash: 235628b512a63ee2f7ec4de2176ab0b90ad65487
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652617"
---
# <a name="using-the-natural-language-framework-with-xamarinios"></a>搭配使用自然語言架構與 Xamarin

自然語言架構是在 iOS 12 中引進, 可啟用裝置自然語言處理。 它支援語言辨識、token 化和標記。 Token 化會將文字分割成其元件單字、句子或段落;標記可識別語音、人員、地點和組織的各部分。

自然語言架構也可以使用自訂核心 ML 模型來分類及標記特殊內容中的文字。

[NSLinguisticTagger](xref:Foundation.NSLinguisticTagger)類別仍然可以使用。 不過, 自然語言架構是用於自然語言處理的慣用機制。

## <a name="sample-app-xamarinnl"></a>範例應用程式:XamarinNL

若要瞭解如何搭配使用自然語言架構與 Xamarin, 請參閱[XamarinNL 範例應用程式](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-xamarinnl)。
這個範例應用程式會示範如何使用自然語言架構來執行下列動作:

- [辨識語言](#recognizing-languages)。
- 將[文字 token 化成單字和句子](#tokenizing-text-into-words-sentences-and-paragraphs)。
- [標記命名的實體和語音的部分](#tagging-named-entities-and-parts-of-speech)。

## <a name="recognizing-languages"></a>辨識語言

範例應用程式的 [辨識**器**] 索引標籤會示範如何使用[`NLLanguageRecognizer`](xref:NaturalLanguage.NLLanguageRecognizer)
判斷文字區塊的語言。

> [!NOTE]
> 語言辨識是特定類型的文字分類。 自然語言架構也支援透過開發人員提供的核心 ML 模型自訂文字分類。 如需詳細資訊, 請參閱從 WWDC 2018[引入自然語言架構](https://developer.apple.com/videos/play/wwdc2018/713/)會話。

### <a name="dominant-language"></a>主要語言

請按 [**語言**] 按鈕, 以識別使用者輸入中的主要語言。

的`HandleDetermineLanguageButtonTap` 方法`LanguageRecognizerViewController`會使用[`GetDominantLanguage`](xref:NaturalLanguage.NLLanguageRecognizer.GetDominantLanguage*)
的`NLLanguageRecognizer`方法, 用來提取[`NLLanguage`](xref:NaturalLanguage.NLLanguage)
在文字中找到的主要語言:

```csharp
partial void HandleDetermineLanguageButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        NLLanguage lang = NLLanguageRecognizer.GetDominantLanguage(UserInput.Text);
        DominantLanguageLabel.Text = lang.ToString();
    }
}
```

### <a name="language-probabilities"></a>語言機率

請按 [**語言**機率] 按鈕, 以提取使用者輸入的語言假設清單。

`LanguageRecognizerViewController`類別的`NLLanguageRecognizer`方法會具現化, 並要求它`HandleLanguageProbabilitiesButtonTap`[`Process`](xref:NaturalLanguage.NLLanguageRecognizer.Process*)
使用者的文字。 然後它會呼叫語言辨識器[`GetNativeLanguageHypotheses`](xref:NaturalLanguage.NLLanguageRecognizer.GetNativeLanguageHypotheses*)
方法, 它會提取語言字典和相關聯的機率。 然後`LanguageRecognizerTableViewController` , 類別會呈現這些語言和機率。

```csharp
partial void HandleLanguageProbabilitiesButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var recognizer = new NLLanguageRecognizer();
        recognizer.Process(UserInput.Text);
        NSDictionary<NSString, NSNumber> probabilities = recognizer.GetNativeLanguageHypotheses(10);
        PerformSegue(ShowLanguageProbabilitiesSegue, this);
    }
}
```

可能`NLLanguage`的值包括:

- `Amharic`
- `Arabic`
- `Armenian`
- `Bengali`
- `Bulgarian`
- `Burmese`
- `Catalan`
- `Cherokee`
- `Croatian`
- `Czech`
- `Danish`
- `Dutch`
- `English`
- `Finnish`
- `French`
- `Georgian`
- `German`
- `Greek`
- `Gujarati`
- `Hebrew`
- `Hindi`
- `Hungarian`
- `Icelandic`
- `Indonesian`
- `Italian`
- `Japanese`
- `Kannada`
- `Khmer`
- `Korean`
- `Lao`
- `Malay`
- `Malayalam`
- `Marathi`
- `Mongolian`
- `Norwegian`
- `Oriya`
- `Persian`
- `Polish`
- `Portuguese`
- `Punjabi`
- `Romanian`
- `Russian`
- `SimplifiedChinese`
- `Sinhalese`
- `Slovak`
- `Spanish`
- `Swedish`
- `Tamil`
- `Telugu`
- `Thai`
- `Tibetan`
- `TraditionalChinese`
- `Turkish`
- `Ukrainian`
- `Undetermined`
- `Urdu`
- `Vietnamese`

提供支援語言的完整清單, 屬於[`NLLanguage`](xref:NaturalLanguage.NLLanguage)
列舉 API 檔。

## <a name="tokenizing-text-into-words-sentences-and-paragraphs"></a>將文字 token 化成單字、句子和段落

          **Tokenizer**索引標籤的範例應用程式示範如何將其元件文字分成文字區塊，或使用句子[`NLTokenizer`](xref:NaturalLanguage.NLTokenizer)。

請按 [**單字**] 或 [**句子**] 按鈕來提取權杖清單。 每個標記都會與原始文字中的單字或句子相關聯。

`ShowTokens`藉由呼叫, 將使用者的輸入分割成標記[`GetTokens`](xref:NaturalLanguage.NLTokenizer.GetTokens*)
的`NLTokenizer`方法。 這個方法會傳回的陣列。[`NSValue`](xref:Foundation.NSValue)
物件, 每個都會`NSRange`在原始文字中包裝對應于標記的值。

```csharp
void ShowTokens(NLTokenUnit unit)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tokenizer = new NLTokenizer(unit);
        tokenizer.String = UserInput.Text;
        var range = new NSRange(0, UserInput.Text.Length);
        NSValue[] tokens = tokenizer.GetTokens(range);
        PerformSegue(ShowTokensSegue, this);
    }
}
```

`LanguageTokenizerTableViewController`在每個資料表單元格中轉譯單一標記。 它會`NSRange`從權杖`NSValue`中解壓縮, 並在原始文字中尋找對應的字串, 然後在 [資料表視圖] 資料格上設定標籤:

```csharp
public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
    var cell = TableView.DequeueReusableCell(TokenCell);
    NSRange range = Tokens[indexPath.Row].RangeValue;
    cell.TextLabel.Text = Text.Substring((int)range.Location, (int)range.Length);
    return cell;
}
```

## <a name="tagging-named-entities-and-parts-of-speech"></a>標記命名實體和部分語音

XamarinNL範例應用程式的 [標記] 索引標籤會示範如何使用[`NLTagger`](xref:NaturalLanguage.NLTagger)
類別, 用來將分類與輸入字串的標記產生關聯。
自然語言架構包含內建的支援, 可辨識人員、地點、組織和語音的各部分。

> [!NOTE]
> 自然語言架構也透過開發人員提供的核心 ML 模型, 支援自訂標記配置。 如需詳細資訊, 請參閱從 WWDC 2018[引入自然語言架構](https://developer.apple.com/videos/play/wwdc2018/713/)會話。

請按 [**指名的實體**] 或 [**語音的部分**] 按鈕以提取:

- 物件的`NSValue`陣列, 每個都會在`NSRange`原始文字中包裝標記的。
- 值的陣列[`NLTag`](xref:NaturalLanguage.NLTag) –位於相同陣列索引`NSValue`之標記的分類。

在`LanguageTaggerViewController`中`HandlePartsOfSpeechButtonTap` , 和`HandleNamedEntitiesButtonTap`每`ShowTags`個呼叫[`NLTagScheme`](xref:NaturalLanguage.NLTagScheme)都會`NLTagScheme.NameType`傳遞– (適用于語音的某些部分) 或 (針對已命名的實體)。 `NLTagScheme.LexicalClass`

`ShowTags`建立, 並使用所要查詢的`NLTagScheme`類型陣列 (在此情況下, `NLTagScheme`僅限傳入的值) 將它具現化。 `NLTagger` 然後, 它會使用[`GetTags`](xref:NaturalLanguage.NLTagger.GetTags*)
上的`NLTagger`方法, 可決定與使用者輸入中的文字相關的標記。

```csharp
void ShowTags(NLTagScheme tagScheme)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tagger = new NLTagger(new NLTagScheme[] { tagScheme });
        var range = new NSRange(0, UserInput.Text.Length);
        tagger.String = UserInput.Text;

        NLTag[] tags = tagger.GetTags(range, NLTokenUnit.Word, tagScheme, NLTaggerOptions.OmitWhitespace, out NSValue[] ranges);
        NSValue[] tokenRanges = ranges;
        detailViewTitle = tagScheme == NLTagScheme.NameType ? "Named Entities" : "Parts of Speech";

        PerformSegue(ShowEntitiesSegue, this);
    }
}
```

然後, 這些標記會顯示在資料表`LanguageTaggerTableViewController`中。

可能`NLTag`的值包括:

- `Adjective`
- `Adverb`
- `Classifier`
- `CloseParenthesis`
- `CloseQuote`
- `Conjunction`
- `Dash`
- `Determiner`
- `Idiom`
- `Interjection`
- `Noun`
- `Number`
- `OpenParenthesis`
- `OpenQuote`
- `OrganizationName`
- `Other`
- `OtherPunctuation`
- `OtherWhitespace`
- `OtherWord`
- `ParagraphBreak`
- `Particle`
- `PersonalName`
- `PlaceName`
- `Preposition`
- `Pronoun`
- `Punctuation`
- `SentenceTerminator`
- `Verb`
- `Whitespace`
- `Word`
- `WordJoiner`

提供支援標記的完整清單, 屬於[`NLTag`](xref:NaturalLanguage.NLTag)
列舉 API 檔。

## <a name="related-links"></a>相關連結

- [範例應用程式-XamarinNL](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-xamarinnl)
- [自然語言架構簡介](https://developer.apple.com/videos/play/wwdc2018/713/)
- [自然語言 (Apple)](https://developer.apple.com/documentation/naturallanguage?language=objc)
