---
title: Microsoft 的 OpenJDK 發行版本預覽版
description: 設定 Microsoft OpenJDK 發行版本的逐步指南。
ms.prod: xamarin
ms.assetid: B5F8503D-F4D1-44CB-8B29-187D1E20C979
ms.technology: xamarin-android
author: vyedin
ms.author: vyedin
ms.date: 07/22/2018
ms.openlocfilehash: 6c1346918ca6881e551f6c5b89ab16ad13d3f804
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242502"
---
# <a name="microsofts-openjdk-distribution-preview"></a>Microsoft 的 OpenJDK 發行版本預覽版

_此指南描述切換到 Microsoft OpenJDK 發行版本預覽版的步驟。_

![預覽版功能](~/media/shared/preview.png)

## <a name="overview"></a>概觀

從 Visual Studio 15.9 與 Visual Studio for Mac 7.7 開始，Visual Studio Tools for Xamarin 就會從 Oracle 的 JDK 移轉至僅適用於 Android 開發的輕量型 OpenJDK 版本：

![新工作流程提供 VS 15.8 Preview 5 中之 OpenJDK 的 Web 預覽](openjdk-images/openjdk.png)

此移轉的優點包括：

- 您將一律擁有適用於 Android 開發的 OpenJDK 版本。

- 下載 JDK 9 或 10 將不會影響開發體驗。

- 大幅降低下載大小與所耗用資源量。

- 再也不需要處理第三方伺服器問題與安裝程式問題。

為讓您更快地移轉到改良的體驗，我們提供 Microsoft OpenJDK 發行版本組建，供您在 Windows 與 Mac 上測試。 安裝程式如下所述，而且您可以隨時還原為 Oracle JDK。

## <a name="download"></a>下載

若要開始，請下載適用於您系統的正確組建：

- **Mac** &ndash; https://dl.xamarin.com/OpenJDK/mac/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x86** &ndash; https://dl.xamarin.com/OpenJDK/win32/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x64** &ndash; https://dl.xamarin.com/OpenJDK/win64/microsoft-dist-openjdk-1.8.0.9.zip

## <a name="configure"></a>設定

解壓縮到正確位置：

- **Mac** &ndash; **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**
- **Windows** &ndash; **C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**

> [!IMPORTANT]
> 此範例使用組建 1.8.0.9：不過您下載的版本可能比較新。

將 IDE 指向新的 JDK：

- **Mac** &ndash; 按一下 [工具] > [SDK 管理員] > [位置]，並將 [Java SDK (JDK) 的位置] 變更為完整的 OpenJDK 安裝路徑。 在下列範例中，此路徑設定為 **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**。

![在 Mac 上針對 Microsoft OpenJDK 發行版本設定 JDK 路徑](openjdk-images/vsm.png)

- **Windows** &ndash; 按一下 [工具] > [選項] > [Xamarin] > [Android 設定]，並將 [Java 開發套件位置] 變更為完整的 OpenJDK 安裝路徑。 在下列範例中，此路徑設定為 **C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**：

![在 Windows 上針對 Microsoft OpenJDK 發行版本設定 JDK 路徑](openjdk-images/vs.png)

## <a name="revert"></a>還原

若要還原到 Oracle JDK，請將 Java SDK 位置變更為先前使用的 Oracle JDK 路徑並重建方案。 在 Mac 上，您可以按一下 [重設為預設值] 以還原到 Oracle JDK 路徑。

如果有關於 Microsoft OpenJDK 發行版本的任何問題，請使用您 IDE 中的意見反應工具回報問題，以便正確地追蹤及修正。

## <a name="known-issues--planned-fix-dates"></a>已知問題與規劃的修正日期

`JAVA_HOME` 環境變數可能未正確地匯出至 SDK 與 [裝置管理員]。 您可以將此環境變數設定為您電腦上的 OpenJDK 位置以做為因應措施。 這已在 15.9 預覽版中修正。

## <a name="summary"></a>總結

在此文章中，您將學到如何設定 IDE 以使用 Microsoft OpenJDK 發行版本的預覽版本，這預計將在 2018 年稍後成為穩定版本。
