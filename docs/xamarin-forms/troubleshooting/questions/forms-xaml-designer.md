---
title： "為什麼 xaml 檔案的 Visual Studio XAML 設計工具不適用 Xamarin.Forms ？"
ms. 主題：疑難排解 ms-chap： assetid： cab2eefb-c52f-4d81-866e-8f1feabbdd64 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. date：04/25/2017 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>為什麼 xaml 檔案的 Visual Studio XAML 設計工具不適用 Xamarin.Forms ？

Xamarin.Forms目前不支援 XAML 檔案的視覺化設計工具。 因此，嘗試以編碼方式在 Visual Studio 的*XAML Ui 設計*工具或*xaml ui 設計*工具中開啟表單 XAML 檔案時，會擲回下列錯誤訊息：

> 「無法使用選取的編輯器開啟檔案。 請選擇其他編輯器」。

這項限制會在[ Xamarin.Forms XAML 基本概念](~/xamarin-forms/xaml/xaml-basics/index.md)指南中說明：

> 「還沒有視覺化設計工具可以在應用程式中產生 XAML Xamarin.Forms ，所以所有的 xaml 都必須以手動方式撰寫。」

不過，您 Xamarin.Forms 可以選取 [ **View > 其他 Windows > Xamarin.Forms 預覽**器] 功能表選項來顯示 XAML 預覽程式。
