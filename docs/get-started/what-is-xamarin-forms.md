---
標題：「什麼是 Xamarin.Forms ？」
描述：「本文介紹 Xamarin.Forms 和相關連結庫。」
assetid： C1E24DB9-3099-4F79-BB88-10AABF7D4614 author： profexorgeek ms. author： jusjohns ms. date： 05/28/2020 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="what-is-xamarinforms"></a>什麼是 Xamarin.Forms ？

[![Xamarin.FormsIOS 和 Android 中範例應用程式的螢幕擷取畫面](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Xamarin.Forms是一個開放原始碼 UI 架構。 Xamarin.Forms可讓開發人員從單一的共用程式碼基底建立 Xamarin. Android、Xamarin 和 Windows 應用程式。

Xamarin.Forms可讓開發人員在 XAML 中使用 c # 的程式碼後置來建立使用者介面。 這些介面會於每個平台上轉譯為高效能的原生控制項。

## <a name="who-xamarinforms-is-for"></a>誰 Xamarin.Forms 是

Xamarin.Forms適用于具有下列目標的開發人員：

- 跨平台共用 UI 版面配置和設計。
- 跨平台共用程式碼、測試及商務邏輯。
- 使用 Visual Studio，在 C# 中撰寫跨平台應用程式。

## <a name="how-xamarinforms-works"></a>Xamarin.Forms運作方式

![Xamarin.Forms架構圖](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Xamarin.Forms提供一致的 API，可跨平臺建立 UI 元素。 此 API 可於 XAML 或 C# 中實作，並支援如 Model-View-ViewModel (MVVM) 等模式的資料繫結。

在執行時間，會利用平臺轉譯器， Xamarin.Forms 將跨平臺 UI 元素轉換成 xamarin、xamarin 和 UWP 上的原生控制項。 這可讓開發人員取得原生外觀、風格和效能，並同時實現跨平台共用程式碼的優點。

Xamarin.Forms應用程式通常是由共用 .NET Standard 程式庫和個別平臺專案所組成。 共用程式庫包含 XAML 或 C# 檢視，以及所有例如服務、模型或其他程式碼等商務邏輯。 平台專案則包含應用程式所需的所有平台特定邏輯或套件。

Xamarin.Forms使用 Xamarin 平臺在平臺上以原生方式執行 .NET 應用程式。 如需 Xamarin 平臺的詳細資訊，請參閱[什麼是 xamarin？](~/get-started/what-is-xamarin.md)。

## <a name="additional-functionality"></a>其他功能

Xamarin.Forms有大型的程式庫生態系統，可為應用程式新增各種功能。 本節說明其中一些額外的功能。

### Xamarin.Essentials

Xamarin.Essentials是一種程式庫，可提供原生裝置功能的跨平臺 Api。 就像 Xamarin 本身一樣， Xamarin.Essentials 是簡化存取原生公用程式之過程的抽象概念。 提供的一些公用程式範例 Xamarin.Essentials 包括：

- 裝置資訊
- 檔案系統
- 加速計
- 電話撥號程式
- 文字轉換語音
- 螢幕鎖定

如需詳細資訊，請參閱 [Xamarin.Essentials](~/essentials/index.md) \(英文\)。

### <a name="shell"></a>Shell

Xamarin.FormsShell 藉由提供大部分應用程式所需的基本功能，來降低行動應用程式開發的複雜度。 Shell 提供的一些功能範例包括：

- 一般導覽體驗
- 以 URI 為基礎的導覽配置
- 整合式搜尋處理常式

如需詳細資訊，請參閱[ Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>平台特定功能

Xamarin.Forms提供通用的 API，可跨平臺呈現原生控制項，但特定平臺的功能可能不存在於其他平臺上。 例如，Android 平台具有原生功能，可在 `ListView` 中使用快速捲動，但 iOS 中則無此功能。 Xamarin.Forms平臺細節可讓您利用僅在特定平臺上提供的功能，而不需建立自訂轉譯器或效果。

Xamarin.Forms包含適用于各種平臺特定功能的預先建立解決方案。 如需詳細資訊，請參閱：

- [Xamarin.Forms平臺細節](~/xamarin-forms/platform/platform-specifics/index.md)
- [Android 平臺-細節](~/xamarin-forms/platform/android/index.md)
- [iOS 平臺-細節](~/xamarin-forms/platform/ios/index.md)
- [Windows 平台特定功能](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>質感視覺效果

Xamarin.Forms材質視覺效果是用來將材質設計規則套用至 Xamarin.Forms 應用程式。 Xamarin.Forms材質視覺效果利用視覺屬性，選擇性地將自訂轉譯器套用至 UI，使應用程式在 iOS 和 Android 上具有一致的外觀與風格。

如需詳細資訊，請參閱[ Xamarin.Forms 材質視覺效果](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>相關連結

- [開始使用Xamarin.Forms](~/xamarin-forms/index.yml)
- [Xamarin.Essentials](~/essentials/index.md)
- [Xamarin.Forms外層](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin.Forms材質視覺效果](~/xamarin-forms/user-interface/visual/material-visual.md)
