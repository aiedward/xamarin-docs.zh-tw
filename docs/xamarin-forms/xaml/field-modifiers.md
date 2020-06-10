---
title： "description：" 中的 XAML 欄位修飾詞 Xamarin.Forms x:FieldModifier namespace 屬性會為已命名的 XAML 專案指定產生之欄位的存取層級。
assetid： 12357CE0-3C11-4B62-947F-72DB6DFC23A2 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：08/02/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xaml-field-modifiers-in-xamarinforms"></a>中的 XAML 欄位修飾詞Xamarin.Forms

`x:FieldModifier`Namespace 屬性會為已命名的 XAML 元素指定產生之欄位的存取層級。 屬性的有效值為：

- `private`–指定 XAML 元素的產生欄位只能在其宣告所在的類別主體中存取。
- `public`–指定針對 XAML 元素產生的欄位沒有存取限制。
- `protected`–指定 XAML 專案的產生欄位可以在其類別和衍生類別實例之間存取。
- `internal`–指定針對 XAML 元素產生的欄位只能在相同元件的類型內進行存取。
- `notpublic`–指定針對 XAML 元素產生的欄位只能在相同元件的類型內進行存取。

根據預設，如果未設定屬性的值，則為元素產生的欄位將會是 `private` 。

> [!NOTE]
> 屬性的值可以使用任何大小寫，因為它將會轉換成小寫 Xamarin.Forms 。

若要處理屬性，必須符合下列條件 `x:FieldModifier` ：

- 最上層的 XAML 元素必須是有效的 `x:Class` 。
- 目前的 XAML 元素具有 `x:Name` 指定的。

下列 XAML 顯示設定屬性的範例：

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="internal" />
<Label x:Name="publicLabel" x:FieldModifier="public" />
```

> [!IMPORTANT]
> `x:FieldModifier`屬性不能用來指定 XAML 類別的存取層級。
