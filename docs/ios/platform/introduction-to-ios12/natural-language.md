---
title: 與 Xamarin.iOS 使用自然語言架構
description: 本文件說明自然語言架構。 介紹 iOS 12，自然語言架構是針對語言辨識、 詞性的識別和具名的實體辨識使用慣用的 iOS API。
ms.prod: xamarin
ms.assetid: 126C8764-F873-4EB9-98A3-D82AB5689111
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/20/2018
ms.openlocfilehash: 0b3fb7d467ae64e2cbfdb61644b1537bc5ae1161
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233064"
---
# <a name="using-the-natural-language-framework-with-xamarinios"></a>與 Xamarin.iOS 使用自然語言架構

介紹 iOS 12，自然語言架構可讓裝置在自然語言處理。 它支援的語言辨識、 token 化和標記。 Token 化會將文字分割成其元件單字、 句子或段落;標記識別詞性、 人員、 地點及組織。

自然語言架構也可以使用自訂的核心 ML 模型以分類和標記在特製化的內容中的文字。

[NSLinguisticTagger](xref:Foundation.NSLinguisticTagger)類別是仍然可用。 不過，自然語言架構是用於自然語言處理的慣用的機制。

## <a name="sample-app-xamarinnl"></a>範例應用程式：XamarinNL

若要了解如何使用 Xamarin.iOS 中的自然語言架構，看看[XamarinNL 範例應用程式](https://developer.xamarin.com/samples/monotouch/iOS12/XamarinNL)。
此範例應用程式會示範如何使用自然語言架構：

- [辨識語言](#recognizing-languages)。
- [Token 化文字，為單字和句子](#tokenizing-text-into-words-sentences-and-paragraphs)。
- [標記名稱為實體和詞性](#tagging-named-entities-and-parts-of-speech)。

## <a name="recognizing-languages"></a>辨識語言

**辨識器** 索引標籤的範例應用程式示範如何使用 [`NLLanguageRecognizer`](https://developer.xamarin.com/api/type/NaturalLanguage.NLLanguageRecognizer/)
若要判斷文字區塊的語言。

> [!NOTE]
> 語言辨識是特定類型的文字分類。 自然語言架構也支援透過開發人員提供的核心 ML 模型的自訂文字分類。 如需詳細資訊，看看[自然語言 Framework 簡介](https://developer.apple.com/videos/play/wwdc2018/713/)WWDC 2018，從工作階段。

### <a name="dominant-language"></a>主控項的語言

點選**語言**按鈕，以找出使用者輸入的主要語言。

`HandleDetermineLanguageButtonTap`方法的`LanguageRecognizerViewController`使用 [`GetDominantLanguage`](https://developer.xamarin.com/api/member/NaturalLanguage.NLLanguageRecognizer.GetDominantLanguage/)
方法`NLLanguageRecognizer`提取 [`NLLanguage`](https://developer.xamarin.com/api/type/NaturalLanguage.NLLanguage/)
在文字中找到的主要語言：

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

點選**語言機率**按鈕，以擷取使用者輸入的語言假設清單。

`HandleLanguageProbabilitiesButtonTap`方法`LanguageRecognizerViewController`類別會具現化`NLLanguageRecognizer`並要求 [`Process`](https://developer.xamarin.com/api/member/NaturalLanguage.NLLanguageRecognizer.Process/)
使用者的文字。 然後它會呼叫語言辨識器 [`GetNativeLanguageHypotheses`](https://developer.xamarin.com/api/member/NaturalLanguage.NLLanguageRecognizer.GetNativeLanguageHypotheses)
方法，擷取語言和相關聯的機率的字典。 `LanguageRecognizerTableViewController`類別接著會轉譯這些語言和機率。

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

潛在`NLLanguage`值包括：

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

會提供做為支援之語言的完整清單的一部分 [`NLLanguage`](https://developer.xamarin.com/api/type/NaturalLanguage.NLLanguage/)
列舉 API 文件。

## <a name="tokenizing-text-into-words-sentences-and-paragraphs"></a>Token 化成單字、 句子和段落的文字

**Tokenizer**索引標籤的範例應用程式示範如何將其元件文字分成文字區塊，或使用句子[ `NLTokenizer` ](https://developer.xamarin.com/api/type/NaturalLanguage.NLTokenizer/)。

點選**單字**或是**句子**擷取語彙基元清單 按鈕。 每個語彙基元是相關聯的文字或以原始文字的句子。

`ShowTokens` 使用者的輸入為語彙基元，藉由呼叫的分割 [`GetTokens`](https://developer.xamarin.com/api/member/NaturalLanguage.NLTokenizer.GetTokens/)
方法`NLTokenizer`。 這個方法傳回的陣列 [`NSValue`](xref:Foundation.NSValue)
物件，每個換行`NSRange`對應至原始的文字中的語彙基元的值。

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

`LanguageTokenizerTableViewController` 呈現單一的語彙基元，每個資料表資料格中。 它會擷取`NSRange`從權杖`NSValue`在原始的文字中，尋找對應的字串，在資料表檢視儲存格上設定的標籤：

```csharp
public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
    var cell = TableView.DequeueReusableCell(TokenCell);
    NSRange range = Tokens[indexPath.Row].RangeValue;
    cell.TextLabel.Text = Text.Substring((int)range.Location, (int)range.Length);
    return cell;
}
```

## <a name="tagging-named-entities-and-parts-of-speech"></a>具名的實體和詞性標記

**標記者**XamarinNL 範例應用程式 索引標籤將示範如何使用 [`NLTagger`](https://developer.xamarin.com/api/type/NaturalLanguage.NLTagger/)
輸入字串的語彙基元相關聯類別的類別。
自然語言架構包含內建支援人員、 地點、 組織和組件的語音辨識。

> [!NOTE]
> 自然語言架構也支援自訂標記的配置，透過開發人員提供的核心 ML 模型。 如需詳細資訊，看看[自然語言 Framework 簡介](https://developer.apple.com/videos/play/wwdc2018/713/)WWDC 2018，從工作階段。

點選**具名實體**或是**詞性**按鈕提取：

- 陣列`NSValue`物件，每個換行`NSRange`權杖中的原始文字。
- 陣列[ `NLTag` ](https://developer.xamarin.com/api/type/NaturalLanguage.NLTag/)值 – 類別`NSValue`相同的陣列索引處的語彙基元。

中`LanguageTaggerViewController`，`HandlePartsOfSpeechButtonTap`並`HandleNamedEntitiesButtonTap`每次呼叫`ShowTags`，沿著傳遞[ `NLTagScheme` ](https://developer.xamarin.com/api/type/NaturalLanguage.NLTagScheme/) – 任一`NLTagScheme.LexicalClass`（適用於詞性） 或`NLTagScheme.NameType`（名為實體）。

`ShowTags` 建立`NLTagger`，產生的陣列`NLTagScheme`類型的它將會查詢 (在此情況下，只傳入`NLTagScheme`值)。 然後它會使用 [`GetTags`](https://developer.xamarin.com/api/member/NaturalLanguage.NLTagger.GetTags/)
方法`NLTagger`來判斷 標記與使用者輸入中的文字。

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

這些標記會再顯示在資料表中`LanguageTaggerTableViewController`。

潛在`NLTag`值包括：

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

會提供做為支援標記的完整清單的一部分 [`NLTag`](https://developer.xamarin.com/api/type/NaturalLanguage.NLTag/)
列舉 API 文件。

## <a name="related-links"></a>相關連結

- [範例應用程式 – XamarinNL](https://developer.xamarin.com/samples/monotouch/iOS12/XamarinNL)
- [自然語言 Framework 簡介](https://developer.apple.com/videos/play/wwdc2018/713/)
- [自然語言 (Apple)](https://developer.apple.com/documentation/naturallanguage?language=objc)
