---
title: 使用原生程式庫
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: b7d69e99327aa3d3e3e1f5e5dbc61697d1fb9b71
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "75489163"
---
# <a name="using-native-libraries"></a>使用原生程式庫

Xamarin.Android 支援通過標準 PInvoke 機制使用本機庫。 您還可以將不屬於作業系統的其他本機庫捆綁到 .apk 中。

要使用 Xamarin.Android 應用程式部署本機庫,請將庫二進位檔案新增到專案中,並將其**生成操作**設定為**AndroidNativeLibrary**。

要使用 Xamarin.Android 函式庫專案部署本機庫,請將函式庫二進位檔案加入到專案中,並將其**產生操作**設定為**嵌入式本機庫**。

請注意,由於 Android 支援多個應用程式二進位介面 (ABI),Xamarin.Android 必須知道本機庫的構建為哪個 ABI。
方法有二：

1. 路徑"嗅探"
1. 使用項目檔案中`AndroidNativeLibrary/Abi`的元素

路徑探查會使用原生程式庫的父目錄名稱來指定程式庫的目標 ABI。 因此,如果添加到`lib/armeabi/libfoo.so`專案中,則 ABI 將被「嗅探`armeabi`」為 。

或者,您可以編輯項目檔以顯式指定要使用的 ABI:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

有關使用本機庫的詳細資訊,請參閱[與本機庫的 Interop。](https://www.mono-project.com/docs/advanced/pinvoke/)

## <a name="debugging-native-code-with-visual-studio"></a>使用視覺化工作室除錯本機代碼

如果您使用的是*Visual Studio 2019*或*Visual Studio 2017,* 則無需修改上述專案檔。
您可以通過添加專案引用到C++**動態分享庫 (Android)** 專案來建構和調試 Xamarin.Android 解決方案中的C++。

要調試專案中的本機C++代碼,請按照以下步驟操作:

1. 按兩下項目**屬性**並選擇 **「Android 選項」** 頁面。
2. 移至**除錯選項**。
3. 在**除錯器**下拉選單中,選擇**C++(** 而不是預設的 **.NET (Xamarin)。**

Visual Studio C++開發人員可以看到[SanAngeles_NativeDebug](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk)示例,嘗試使用 Xamarin 從 Visual Studio 2019 或 Visual Studio 2017 調試C++;並參考我們的[博客文章](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/)瞭解更多資訊。

## <a name="related-links"></a>相關連結

- [SanAngeles_NativeDebug(範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk)
- [開發 Xamarin Android 原生應用程式](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)
