---
title: "Xamarin.Android 錯誤矩陣"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6992C4FF-ECBB-3493-AEE6-3E063C1A8C54
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: c3aab44fcb0522b762aaa101de0aeba08016b641
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinandroid-errors-matrix"></a>Xamarin.Android 錯誤矩陣

## <a name="errors-reference"></a>錯誤參考

本文件提供從 Xamarin 各種錯誤碼一些資訊。

<table>
    <thead>
        <tr>
            <td>分類</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr><td>XA0xxx</td><td><code>mandroid</code> 錯誤</td></tr>
        <tr><td>XA1xxx</td><td>檔案複製 / 符號連結 （相關的專案） 錯誤</td></tr>
        <tr><td>XA2xxx</td><td>連結器錯誤</td></tr>
        <tr><td>XA3xxx</td><td>AOT 錯誤</td></tr>
        <tr><td>XA4xxx</td><td>程式碼產生錯誤</td></tr>
        <tr><td>XA5xxx</td><td>GCC 和工具鏈錯誤</td></tr>
        <tr><td>XA6xxx</td><td><code>mandroid</code> 內部工具錯誤</td></tr>
        <tr><td>XA7xxx</td><td>保留</td></tr>
        <tr><td>XA8xxx</td><td>保留</td></tr>
        <tr><td>XA9xxx</td><td>授權錯誤</td></tr>
    </tbody>
</table>

## <a name="error-codes"></a>錯誤碼

### <a name="xa0xxx-errors"></a>XA0xxx 錯誤

<table>
    <thead>
        <tr><td>錯誤碼</td><td>描述</td></tr>
    </thead>
    <tbody>
        <tr><td>XA0000</td><td>未預期的錯誤-請填寫 錯誤報告在<a href="http://bugzilla.xamarin.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA0001</td><td>'-devname 提供不執行任何特定裝置的動作</td></tr>
        <tr><td>XA0002</td><td>無法剖析的環境變數 '{0}'。</td></tr>
        <tr><td>XA0003</td><td>SDK 或產品組件 (.dll) 與應用程式名稱 '{0}.exe' 衝突。</td></tr>
        <tr><td>XA0004</td><td>新的 refcounting 邏輯需要 sgen 太啟用。</td></tr>
        <tr><td>XA0005</td><td>輸出目錄 '{0}' 不存在</td></tr>
        <tr><td>XA0006</td><td>在 '{0}' 沒有任何對內平台，使用--平台 = P 指定 SDK</td></tr>
        <tr><td>XA0007</td><td>根組件 '{0}' 不存在</td></tr>
        <tr><td>XA0008</td><td>您應該提供一個根組件只</td></tr>
        <tr><td>XA0009</td><td>載入組件時發生錯誤： {0}</td></tr>
        <tr><td>XA0010</td><td>無法剖析命令列引數： {0}</td></tr>
        <tr><td>XA0011</td><td>針對較新的執行階段 ({1}) 建置 {0} 高於 MonoTouch 支援</td></tr>
        <tr><td>XA0012</td><td>若要完成不完整的資料提供`{0}`。</td></tr>
        <tr><td>XA0013</td><td>設定檔作業支援需要 sgen 太啟用</td></tr>
        <tr><td>XA0014</td><td>iOS {0} 不支援目標 ARMv6 建置應用程式</td></tr>
        <tr><td>XA0020</td><td>無法判斷 mandroid 路徑。</td></tr>
        <tr><td>XA0100</td><td>在 Android 應用程式專案中 EmbeddedNativeLibrary '{0}' 無效。 請改用 AndroidNativeLibrary。</td></tr>
    </tbody>
</table>

### <a name="xa1xxx-errors"></a>XA1xxx 錯誤

<table>
    <thead>
        <tr><td>錯誤碼</td><td>描述</td></tr>
    </thead>
    <tbody>
        <tr><td>XA1001</td><td>找不到應用程式在指定的目錄</td></tr>
        <tr><td>XA1002</td><td>無法建立符號連結，已複製檔案</td></tr>
        <tr><td>XA1003</td><td>無法終止應用程式 '{0}'。 您可能必須手動終止應用程式。</td></tr>
        <tr><td>XA1004</td><td>無法取得已安裝的應用程式的清單。</td></tr>
        <tr><td>XA1005</td><td>無法終止 '{1}' 的裝置上的應用程式 '{0}': {2}。 您可能必須手動終止應用程式。</td></tr>
        <tr><td>XA1006</td><td>無法安裝在裝置 '{1}' 上的應用程式 '{0}': {2}。</td></tr>
        <tr><td>XA1007</td><td>無法啟動裝置 '{1}' 上的應用程式 '{0}': {2}。 您仍然可以在其上的點選以手動方式啟動應用程式。</td></tr>
        <tr><td>XA1008</td><td>無法啟動模擬器： {0}</td></tr>
        <tr><td>XA1009</td><td>無法複製的組件 '{0}' 到 '{1}': {2}</td></tr>
        <tr><td>XA1010</td><td>無法載入組件 '{0}': {1}</td></tr>
        <tr><td>XA1011</td><td>無法加入遺漏的資源檔: '{0}'</td></tr>
        <tr><td>XA1101</td><td>無法啟動應用程式</td></tr>
        <tr><td>XA1102</td><td>無法附加至應用程式 （若要刪除該）： {0}</td></tr>
        <tr><td>XA1103</td><td>無法中斷連接</td></tr>
        <tr><td>XA1104</td><td>無法傳送封包： {0}</td></tr>
        <tr><td>XA1105</td><td>未預期的回應類型</td></tr>
        <tr><td>XA1106</td><td>無法取得裝置上的應用程式清單： 要求已逾時。</td></tr>
        <tr><td>XA1107</td><td>無法啟動應用程式</td></tr>
        <tr><td>XA1201</td><td>無法載入模擬器： {0}</td></tr>
        <tr><td>XA1301</td><td>原生程式庫`{0}`({1}) 已被忽略，因為它不符合目前組建 architecture(s) ({2})</td></tr>
    </tbody>
</table>

### <a name="xa2xxx-errors"></a>XA2xxx 錯誤

<table>
    <thead>
        <tr><td>錯誤碼</td><td>描述</td></tr>
    </thead>
    <tbody>
        <tr><td>XA2001</td><td>無法連結的組件</td></tr>
        <tr><td>XA2002</td><td>無法解析參考： {0}</td></tr>
        <tr><td>XA2003</td><td>會忽略選項 '{0}'，因為連結已停用</td></tr>
        <tr><td>XA2004</td><td>找不到額外的連結器定義檔 '{0}'。</td></tr>
        <tr><td>XA2005</td><td>無法剖析來自 '{0}' 定義。</td></tr>
        <tr><td>XA2006</td><td>無法解析參考 '{2}' 中繼資料項目 '{0}' （定義於 '{1}'）。</td></tr>
    </tbody>
</table>

### <a name="xa3xxx-errors"></a>XA3xxx 錯誤

這些是 AOT 錯誤。

<table>
    <thead>
        <tr><td>錯誤碼</td><td>描述</td></tr>
    </thead>
    <tbody>
        <tr><td>XA3001</td><td>無法 AOT 組件 '{0}'</td></tr>
        <tr><td>XA3002</td><td>AOT 限制： 方法 '{0}' 必須是靜態，因為它以 [MonoPInvokeCallback] 裝飾。</td></tr>
        <tr><td>XA3003</td><td>發生衝突-偵錯和-llvm 選項。 軟偵錯已停用。</td></tr>
    </tbody>
</table>

### <a name="xa4xxx-errors"></a>XA4xxx 錯誤

這些是程式碼產生錯誤。

<table>
    <thead>
        <tr><td>錯誤碼</td><td>描述</td></tr>
    </thead>
    <tbody>
        <tr><td>XA4001</td><td>找不到 expansed 主要範本`{0}`。</td></tr>
        <tr><td>XA4101</td><td>在註冊機構無法建立類型的簽章`{0}`。</td></tr>
        <tr><td>XA4102</td><td>在註冊機構找到無效的型別`{0}`中方法的簽章`{2}`。 請改用 `{1}`。</td></tr>
        <tr><td>XA4103</td><td>在註冊機構找到無效的型別`{0}`中方法的簽章`{2}`： 型別實作 INativeObject，但是並沒有會採用兩個建構函式 (IntPtr，bool) 引數</td></tr>
        <tr><td>XA4104</td><td>在註冊機構無法封送處理類型的傳回值`{0}`中方法的簽章`{1}`。</td></tr>
        <tr><td>XA4105</td><td>在註冊機構無法封送處理類型的參數`{0}`中方法的簽章`{1}`。</td></tr>
        <tr><td>XA4106</td><td>在註冊機構無法封送處理結構的傳回值`{0}`中方法的簽章`{1}`。</td></tr>
        <tr><td>XA4107</td><td>在註冊機構無法封送處理類型的參數`{0}`中方法的簽章`{1}`。</td></tr>
        <tr><td>XA4108</td><td>在註冊機構無法取得 managed 類型的 ObjectiveC 類型`{0}`。</td></tr>
        <tr><td>XA4109</td><td>無法編譯產生的註冊機構的程式碼。 請檔案錯誤報告在<a href="http://bugzilla.xamarin.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA4110</td><td>在註冊機構無法封送處理類型的 out 參數`{0}`中方法的簽章`{1}`。</td></tr>
        <tr><td>XA4111</td><td>在註冊機構無法建立類型的簽章`{0}' in method `{1}'。</td></tr>
        <tr><td>XA4200</td><td>只產生 ACW 的 'claas' 類型。</td></tr>
        <tr><td>XA4201</td><td>無法判斷類型 {0} JNI 名稱。</td></tr>
        <tr><td>XA4203</td><td>指定的型別名稱必須是完整名稱。</td></tr>
        <tr><td>XA4204</td><td>無法解析介面類型 '{0}'。 您是否遺漏了組件參考?</td></tr>
        <tr><td>XA4205</td><td>[ExportField] 只能用於具有 0 個參數的方法。</td></tr>
        <tr><td>XA4206</td><td>[匯出] 無法用於泛型型別。</td></tr>
        <tr><td>XA4207</td><td>[ExportField] 無法用於泛型型別。</td></tr>
        <tr><td>XA4208</td><td>[Java.Interop.ExportFieldAttribute] 不能傳回 void 的方法。</td></tr>
        <tr><td>XA4209</td><td>無法建立類別 JavaTypeInfo： 由於 {1} {0}</td></tr>
        <tr><td>XA4210</td><td>您需要將參考加入 Mono.Android.Export.dll，當您使用 exportattribute 標記的型或 ExportFieldAttribute。</td></tr>
        <tr><td>XA4211</td><td>AndroidManifest.xml //uses-sdk/@android:targetSdkVersion '{0}' 必須小於 $(TargetFrameworkVersion) '{1}'。 使用 API-\ {1 \} ACW 編譯。</td></tr>
    </tbody>
</table>

### <a name="xa5xxx-errors"></a>XA5xxx 錯誤

<table>
    <thead>
        <tr><td>錯誤碼</td><td>描述</td></tr>
    </thead>
    <tbody>
        <tr><td>XA5101</td><td>遺漏 '{0}' 編譯器。 請安裝 Android NDK。</td></tr>
        <tr><td>XA5102</td><td>無法從組件轉換成原生程式碼。 請檔案錯誤報告在<a href="http://bugzilla.xamarin.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA5103</td><td>無法編譯檔案 '{0}'。 請檔案錯誤報告在<a href="http://bugzilla.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA5201</td><td>原生連結失敗。 請檢閱使用者旗標，提供給 gcc: {0}</td></tr>
        <tr><td>XA5202</td><td>原生連結失敗。 請檢閱組建記錄檔。</td></tr>
        <tr><td>XA5303</td><td>原生連結警告： {0}</td></tr>
        <tr><td>XA5300</td><td>Android SDK 未完整安裝，或找不到。</td></tr>
        <tr><td>XA5301</td><td>遺漏 '帶狀' 工具。 請安裝 Xcode '命令列工具' 元件</td></tr>
        <tr><td>XA5302</td><td>遺漏 'dsymutil' 工具。 請安裝 Xcode '命令列工具' 元件</td></tr>
        <tr><td>XA5203</td><td>無法產生偵錯符號 （dSYM 目錄）。 請檢閱組建記錄檔。</td></tr>
        <tr><td>XA5204</td><td>無法刪除最終的二進位檔。 請檢閱組建記錄檔。</td></tr>
        <tr><td>XA5205</td><td>遺漏 'aapt' 工具。 請安裝 Android SDK 建置工具套件。</td></tr>
        <tr><td>XA5206</td><td>{0}. Android 的資源目錄 {1} 不存在。</td></tr>
        <tr><td>XA5207</td><td>{0}. 使用 Java 程式庫檔案 {1} 不存在。</td></tr>
        <tr><td>XA5208</td><td>下載失敗。 請下載 {0}，並將它放到 {1} 目錄。</td></tr>
        <tr><td>XA5209</td><td>解壓縮失敗。 請下載 {0}，並將它解壓縮至 {1} 目錄。</td></tr>
        <tr><td>XA5210</td><td>{0}. 原生程式庫檔案 {1} 不存在。</td></tr>
    </tbody>
</table>

### <a name="xa6xxx-errors"></a>XA6xxx 錯誤

<table>
    <thead>
        <tr><td>錯誤碼</td><td>描述</td></tr>
    </thead>
    <tbody>
        <tr><td>XA6001</td><td>正在執行的 Cecil 版本不支援移除組件</td></tr>
        <tr><td>XA6002</td><td>可能不會刪除組件`{0}`。</td></tr>
        <tr><td>XA6003</td><td>UnauthorizedAccessException message]</td></tr>
    </tbody>
</table>

### <a name="xa9xxx-errors"></a>XA9xxx 錯誤

這個錯誤碼是授權和啟用的錯誤。

 <a name="xa9000" />


#### <a name="xa9000"></a>XA9000

 **原因：**授權已過期

 **在檢查：**建置

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>警告</td>
            <td>警告</td>
            <td>警告</td>
            <td>警告</td>
            <td>警告</td>
        </tr>
    </tbody>
</table>

 <a name="XA9001" />


#### <a name="xa9001"></a>XA9001

 **原因：**試用版已過期

 **在檢查：**建置

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>警告</td>
            <td>警告</td>
            <td>警告</td>
            <td>警告</td>
            <td>警告</td>
        </tr>
    </tbody>
</table>

 <a name="XA9002" />


#### <a name="xa9002"></a>XA9002

 **原因：** AndroidJavaSource

 **在檢查：**建置

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>確定</td>
            <td>確定</td>
            <td>確定</td>
            <td>確定</td>
        </tr>
    </tbody>
</table>

 **原因：** AndroidJavaLibrary

 **在檢查：**建置

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>確定</td>
            <td>確定</td>
            <td>確定</td>
            <td>確定</td>
        </tr>
    </tbody>
</table>

 **如果繫結組件具有內嵌 d，這會攔截在封裝階段，而不建置時間。**

 **原因：** AndroidNativeLibrary

 **在檢查：**建置

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>確定</td>
            <td>確定</td>
            <td>確定</td>
            <td>確定</td>
        </tr>
    </tbody>
</table>

 <a name="XA9003" />


#### <a name="xa9003"></a>XA9003

 **原因：** System.Runtime.Serialization

 **在檢查：**封裝

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>確定</td>
            <td>確定</td>
            <td>確定</td>
        </tr>
    </tbody>
</table>

 **原因：** System.ServiceModel.Web

 **在檢查：**封裝

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>確定</td>
            <td>確定</td>
            <td>確定</td>
        </tr>
    </tbody>
</table>

 **原因：** Mono.Data.Tds

 **在檢查：**建置

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>確定</td>
            <td>確定</td>
            <td>確定</td>
        </tr>
    </tbody>
</table>

 **這是由 System.Data.dll 中，允許的參考**

 **原因：** Mono.Android.Export

 **在檢查：**建置

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>確定</td>
            <td>確定</td>
            <td>確定</td>
            <td>確定</td>
        </tr>
    </tbody>
</table>

 <a name="XA9004" />


#### <a name="xa9004"></a>XA9004

 **原因：** -程式碼剖析

 **在檢查：**封裝

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>確定</td>
            <td>確定</td>
            <td>確定</td>
        </tr>
    </tbody>
</table>

 <a name="XA9005" />


#### <a name="xa9005"></a>XA9005

 **原因：**大小上限 (32 kb)

 **在檢查：**封裝

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>確定</td>
            <td>-</td>
            <td>-</td>
            <td>-</td>
        </tr>
    </tbody>
</table>

 <a name="XA9006" />


#### <a name="xa9006"></a>XA9006

 **原因：** System.Data.SqlClient 命名空間

 **在檢查：**封裝

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>確定</td>
            <td>確定</td>
            <td>確定</td>
        </tr>
    </tbody>
</table>

 <a name="XA9008" />


#### <a name="xa9008"></a>XA9008

 **原因：**從命令列建置

 **在檢查：**建置

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>確定</td>
            <td>確定</td>
            <td>確定</td>
        </tr>
    </tbody>
</table>

 <a name="XA9009" />


#### <a name="xa9009"></a>XA9009

 **原因：**遺失序號

 **在檢查：**無法測試又複雜

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
        </tr>
    </tbody>
</table>

 <a name="XA9010" />


#### <a name="xa9010"></a>XA9010

 **原因：**無效 ProductId

 **在檢查：**建置

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
        </tr>
    </tbody>
</table>

相當於 XA9018

 <a name="XA9011" />


#### <a name="xa9011"></a>XA9011

 **原因：**無法更新授權檔案 （以新的檔案格式）

 **在檢查：**啟用

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
        </tr>
    </tbody>
</table>

 <a name="XA9012" />


#### <a name="xa9012"></a>XA9012

 **原因：**沒有網際網路

 **在檢查：**啟用

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
        </tr>
    </tbody>
</table>

 <a name="XA9013" />


#### <a name="xa9013"></a>XA9013

 **原因：**未知的錯誤

 **在檢查：**啟用

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
        </tr>
    </tbody>
</table>

 <a name="XA9014" />


#### <a name="xa9014"></a>XA9014

 **原因：**無效的啟動程式碼

 **在檢查：**啟用

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
        </tr>
    </tbody>
</table>

 <a name="XA9017" />


#### <a name="xa9017"></a>XA9017

 **原因：**啟用伺服器不會傳回有效的授權

 **在檢查：**啟用

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
            <td>錯誤</td>
        </tr>
    </tbody>
</table>

<a name="XA9018" />

#### <a name="xa9018"></a>XA9018

**原因：**授權無效

**在檢查：**建置

<table>
    <thead>
        <tr>
            <th>入門</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>商務</th>
            <th>企業</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>-</td>
            <td>-</td>
            <td>錯誤</td>
            <td>-</td>
            <td>-</td>
        </tr>
    </tbody>
</table>
