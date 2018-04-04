---
title: 安裝 Windows 專案
description: 將新的 Windows 專案加入至現有的 Xamarin.Forms 方案
ms.prod: xamarin
ms.assetid: A0774D2E-6994-4D91-84E8-DAB66FC92320
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: 0ad8dedc2e92005473f8836c3cdd590ce4cab5ab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="setup-windows-projects"></a>安裝 Windows 專案

_將新的 Windows 專案加入至現有的 Xamarin.Forms 方案_

舊 Xamarin.Forms 專案 (或所建立的 Mac OS 上&nbsp;X) 不會有這些 Windows 專案設定。

這表示您必須手動加入這些專案類型，來建立 Windows 8.1、 Windows Phone 8.1 和 Windows 10 (UWP) 應用程式。

## <a name="add-a-windows-81-app"></a>新增 Windows 8.1 的應用程式

* 如果您使用 PCL 範本[更新設定檔](#pcl)，然後
* [將 Windows 8.1 應用程式新增](~/xamarin-forms/platform/windows/installation/tablet.md)tablet/桌面表單係數。

## <a name="add-a-windows-phone-81-app"></a>加入 Windows Phone 8.1 應用程式

* 如果您使用 PCL 範本[更新設定檔](#pcl)，然後
* [加入 Windows Phone 8.1 應用程式](~/xamarin-forms/platform/windows/installation/phone.md)

## <a name="add-a-universal-windows-platform-uwp-app"></a>新增通用 Windows 平台 (UWP) 應用程式

* 建置[UWP](https://msdn.microsoft.com/library/windows/apps/dn894631.aspx)應用程式需要 Windows 10 上執行的 Visual Studio 2015。
* 如果您使用 PCL 範本[更新設定檔](#pcl)，然後
* [新增通用 Windows 平台應用程式](~/xamarin-forms/platform/windows/installation/universal.md)

<a name="pcl" />

### <a name="update-your-pcl-profile"></a>更新 PCL 設定檔

如果您現有的 Xamarin.Forms 應用程式使用可攜式類別程式庫 (PCL) 範本，您必須更新其設定檔。

1. **以滑鼠右鍵按一下 > 屬性**（您現有的設定可能不同）

  ![](images/targets.png "PCL 目標")

2. 按一下**變更...**按鈕

3. 請確定**Windows 8**和**Windows Phone 8.1**選取選項 (和**Windows Phone Silveright**是*取消選取*):

  ![](images/pcl.png "PCL 目標選項")

4. 按**確定**並儲存變更。

這等同於**設定檔 111**如果您要在 Visual Studio 中設定您的 PCL for Mac 使用下拉式清單。

  ![](images/pcl-xs.png "PCL 設定檔 111")

**注意：** PCL 如果方案中仍有 Windows Phone 8 Silverlight 專案，應該設定為設定檔 259。 Windows Phone 8 Silverlight 支援已被取代，所以建議您的這個頁面上所顯示的專案類型取代。
