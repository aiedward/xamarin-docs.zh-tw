---
title: 搭配使用自然語言架構和 Xamarin
description: 本檔說明自然語言架構。 在 iOS 12 中引進，自然語言架構是慣用的 iOS API，可用於語言辨識、語音辨識的各部分，以及命名實體辨識。
ms.prod: xamarin
ms.assetid: 126C8764-F873-4EB9-98A3-D82AB5689111
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/20/2018
ms.openlocfilehash: 3d559f197ffc1696c4fd9f7beff7b107103142d3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434194"
---
# <a name="using-the-natural-language-framework-with-xamarinios"></a>搭配使用自然語言架構和 Xamarin

在 iOS 12 中引進，自然語言架構會啟用裝置自然語言處理。 它支援語言辨識、token 化和標記。 Token 化會將文字分割成其元件的單字、句子或段落;標記會識別語音、人員、地點和組織的各部分。

自然語言架構也可以使用自訂核心 ML 模型來分類及標記特製化內容中的文字。

[NSLinguisticTagger](xref:Foundation.NSLinguisticTagger)類別仍可使用。 不過，自然語言架構是用於自然語言處理的慣用機制。

## <a name="sample-app-xamarinnl"></a>範例應用程式： XamarinNL

若要瞭解如何搭配使用自然語言架構和 Xamarin，請參閱 [XamarinNL 範例應用程式](/samples/xamarin/ios-samples/ios12-xamarinnl)。
這個範例應用程式示範如何使用自然語言架構來：

- [辨識語言](#recognizing-languages)。
- 將[文字 token 化為單字和句子](#tokenizing-text-into-words-sentences-and-paragraphs)。
- [標記命名實體和語音的部分](#tagging-named-entities-and-parts-of-speech)。

## <a name="recognizing-languages"></a>辨識語言

範例應用程式的辨識 **器** 索引標籤會示範如何使用 [`NLLanguageRecognizer`](xref:NaturalLanguage.NLLanguageRecognizer)
判斷文字區塊的語言。

> [!NOTE]
> 語言辨識是特定類型的文字分類。 自然語言架構也支援透過開發人員提供的核心 ML 模型進行自訂文字分類。 如需詳細資訊，請參閱 WWDC 2018 中的 [自然語言架構會話簡介](https://developer.apple.com/videos/play/wwdc2018/713/) 。

### <a name="dominant-language"></a>主要語言

請按一下 [ **語言** ] 按鈕，以識別使用者輸入中的主要語言。

的 `HandleDetermineLanguageButtonTap` 方法會 `LanguageRecognizerViewController` 使用 [`GetDominantLanguage`](xref:NaturalLanguage.NLLanguageRecognizer.GetDominantLanguage*)
的方法 `NLLanguageRecognizer` ，可提取 [`NLLanguage`](xref:NaturalLanguage.NLLanguage)
針對在文字中找到的主要語言：

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

請按 [ **語言** 機率] 按鈕，提取使用者輸入的語言假設清單。

`HandleLanguageProbabilitiesButtonTap`類別的方法會具現 `LanguageRecognizerViewController` 化 `NLLanguageRecognizer` ，並要求它[`Process`](xref:NaturalLanguage.NLLanguageRecognizer.Process*)
使用者的文字。 然後，它會呼叫語言辨識器的 [`GetNativeLanguageHypotheses`](xref:NaturalLanguage.NLLanguageRecognizer.GetNativeLanguageHypotheses*)
方法，它會提取語言的字典以及相關聯的機率。 `LanguageRecognizerTableViewController`然後，類別會呈現這些語言和機率。

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

可能的 `NLLanguage` 值包括：

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

支援語言的完整清單可在 [`NLLanguage`](xref:NaturalLanguage.NLLanguage)
列舉 API 檔。

## <a name="tokenizing-text-into-words-sentences-and-paragraphs"></a>將文字 token 化為單字、句子及段落

範例應用程式的 [ **Tokenizer** ] 索引標籤會示範如何使用，將文字區塊分隔成其元件單字或句子 [`NLTokenizer`](xref:NaturalLanguage.NLTokenizer) 。

您可以使用 [ **單字** 或 **句子** ] 按鈕來提取標記清單。 每個標記都會與原始文字中的單字或句子相關聯。

`ShowTokens` 藉由呼叫，將使用者的輸入分割成權杖 [`GetTokens`](xref:NaturalLanguage.NLTokenizer.GetTokens*)
的方法 `NLTokenizer` 。 這個方法會傳回陣列 [`NSValue`](xref:Foundation.NSValue)
物件，每個物件都會包裝與 `NSRange` 原始文字中的標記相對應的值。

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

`LanguageTokenizerTableViewController` 在每個資料表資料格中轉譯單一標記。 它會 `NSRange` 從標記中解壓縮 `NSValue` ，並在原始文字中尋找對應的字串，並在資料表視圖資料格上設定標籤：

```csharp
public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
    var cell = TableView.DequeueReusableCell(TokenCell);
    NSRange range = Tokens[indexPath.Row].RangeValue;
    cell.TextLabel.Text = Text.Substring((int)range.Location, (int)range.Length);
    return cell;
}
```

## <a name="tagging-named-entities-and-parts-of-speech"></a>標記命名實體和語音部分

XamarinNL 範例應用程式 **的 [標記** ] 索引標籤會示範如何使用 [`NLTagger`](xref:NaturalLanguage.NLTagger)
類別，可讓類別與輸入字串的標記產生關聯。
自然語言架構包含內建的支援，可辨識人員、地點、組織和語音的部分。

> [!NOTE]
> 自然語言架構也透過開發人員提供的核心 ML 模型，支援自訂標記配置。 如需詳細資訊，請參閱 WWDC 2018 中的 [自然語言架構會話簡介](https://developer.apple.com/videos/play/wwdc2018/713/) 。

請將 **已命名的實體** 或語音按鈕的 **各部分** ，以提取：

- 物件的陣列 `NSValue` ，每個物件都會 `NSRange` 在原始文字中包裝權杖的。
- 值的陣列 [`NLTag`](xref:NaturalLanguage.NLTag) – `NSValue` 相同陣列索引的標記類別。

在中， `LanguageTaggerViewController` `HandlePartsOfSpeechButtonTap` 以及 `HandleNamedEntitiesButtonTap` 每次呼叫 `ShowTags` 時， [`NLTagScheme`](xref:NaturalLanguage.NLTagScheme) 都會傳遞– `NLTagScheme.LexicalClass` (用於語音) 的部分或 `NLTagScheme.NameType`) 的命名實體 (。

`ShowTags` 建立 `NLTagger` ，並將其具現化，並以型別的陣列 `NLTagScheme` 來查詢 (在此案例中，只有傳入的 `NLTagScheme` 值) 。 然後，它會使用 [`GetTags`](xref:NaturalLanguage.NLTagger.GetTags*)
上的方法 `NLTagger` 來判斷與使用者輸入中的文字相關的標記。

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

然後，這些標記會顯示在資料表中 `LanguageTaggerTableViewController` 。

可能的 `NLTag` 值包括：

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

支援標記的完整清單可在 [`NLTag`](xref:NaturalLanguage.NLTag)
列舉 API 檔。

## <a name="related-links"></a>相關連結

- [範例應用程式-XamarinNL](/samples/xamarin/ios-samples/ios12-xamarinnl)
- [自然語言架構簡介](https://developer.apple.com/videos/play/wwdc2018/713/)
- [Apple) 的自然語言 (](https://developer.apple.com/documentation/naturallanguage?language=objc)