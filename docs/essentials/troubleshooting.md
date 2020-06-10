---
title： " Xamarin.Essentials ：疑難排解" 描述： "本檔說明如何針對使用程式庫進行開發時所遇到的問題進行疑難排解 Xamarin.Essentials 。
assetid： 2E474FAF-F841-4E3C-B815-F7ABD8EE3361 author： jamesmontemagno ms-chap： jamont ms. date： 01/06/2020 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials：疑難排解

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>錯誤：偵測到 Xamarin.Android.Support.Compat 的版本衝突

使用下列其中一項更新 NuGet 套件（或新增套件）時，可能會發生下列錯誤：Xamarin.Forms
使用的專案 Xamarin.Essentials ：

```error
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue.
 MyApp -> Xamarin.Essentials 1.3.1 -> Xamarin.Android.Support.CustomTabs 28.0.0.3 -> Xamarin.Android.Support.Compat (= 28.0.0.3)
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

問題在於這兩個 NuGet 的相依性不符。 您可以手動新增支援兩者的特定相依性版本 (在本例中為 **Xamarin.Android.Support.Compat**)，來解決此問題。

若要這樣做，請手動新增衝突來源的 NuGet，並使用 [版本]**** 清單來選取特定版本。 28.0.0.3 的目前版本支援 Util NuGet 將會解決此錯誤，而無法 & 相容。

如需如何解決問題的詳細資訊和影片，請參閱[此部落格文章](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/)。

如果遇到任何問題或發現錯誤，請在[ Xamarin.Essentials GitHub 存放庫](https://github.com/xamarin/Essentials)中回報。
