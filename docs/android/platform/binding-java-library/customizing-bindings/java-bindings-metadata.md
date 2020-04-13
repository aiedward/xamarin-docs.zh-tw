---
title: Java 繫結中繼資料
description: Xamarin.Android 中的 C# 代碼透過綁定調用 Java 函式庫,這是一種抽象 Java 本機介面 (JNI) 中指定的低級詳細資訊的機制。 Xamarin.Android 提供了一個生成這些綁定的工具。 此工具允許開發人員控制如何使用元數據創建綁定,元數據允許修改命名空間和重命名成員等過程。 本文檔討論元數據的工作原理,總結了元數據支持的屬性,並解釋了如何通過修改此元數據來解決綁定問題。
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 25a5d79084f7caa78eec4011c047bd19a63ef748
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487785"
---
# <a name="java-bindings-metadata"></a>Java 繫結中繼資料

_Xamarin.Android 中的 C# 代碼透過綁定調用 Java 函式庫,這是一種抽象 Java 本機介面 (JNI) 中指定的低級詳細資訊的機制。Xamarin.Android 提供了一個生成這些綁定的工具。此工具允許開發人員控制如何使用元數據創建綁定,元數據允許修改命名空間和重命名成員等過程。本文檔討論元數據的工作原理,總結了元數據支持的屬性,並解釋了如何通過修改此元數據來解決綁定問題。_

## <a name="overview"></a>概觀

Xamarin.Android **Java 綁定庫**嘗試使用有時稱為_綁定生成器_的工具來自動執行綁定現有 Android 庫所需的大量工作。 綁定 Java 庫時,Xamarin.Android 將檢查 JAva 類,並生成要綁定的所有包、類型和成員的清單。 此 API 清單儲存在 XML 檔中,可在**\{專案目錄_obj_Release_api.xml**中找不到 **,用於「發現」** 生成,在**\{專案目錄__obj_Debug_api.xml**中找到,用於**DEBUG**生成。

![api.xml 檔案在 obj/除錯資料夾中的位置](java-bindings-metadata-images/java-bindings-metadata-01.png)

綁定生成器將使用**api.xml**檔作為生成必要 C# 包裝類的指南。 這個XML文件的內容是谷歌Android_開源專案_格式的變體。
以下代碼段是**api.xml**內容的範例:

```xml
<api>
    <package name="android">
        <class abstract="false" deprecated="not deprecated" extends="java.lang.Object"
            extends-generic-aware="java.lang.Object" 
            final="true" 
            name="Manifest" 
            static="false" 
            visibility="public">
            <constructor deprecated="not deprecated" final="false"
                name="Manifest" static="false" type="android.Manifest"
                visibility="public">
            </constructor>
        </class>
...
</api>
```

這個樣本,**這個範例中,api.xml**`android`的套件中`Manifest`, 因為`java.lang.Object`此類別為延伸 。

在許多情況下,需要人工説明,使 JAva API 感覺更像".NET",或者糾正阻止綁定程式集編譯的問題。 例如,可能需要將 JAVA 包名稱更改為 .NET 命名空間、重命名類或更改方法的返回類型。

這些更改不是通過直接修改**api.xml**來實現的。
相反,更改記錄在 JAVA 綁定庫範本提供的特殊 XML 檔中。 編譯 Xamarin.Android 綁定程式集時,綁定產生器在建立綁定程式集時將受到這些映射檔的影響

可以在項目的 **「轉換」** 資料夾中找到這些 XML 映射檔:

- **MetaData.xml**&ndash;允許對最終 API 進行更改,例如更改生成的綁定的命名空間。 

- **EnumFields.xml**&ndash;包含`int`Java 常量`enums`和 C# 之間的映射。 

- **EnumMethod.xml**&ndash;允許更改方法參數,並將類型`int`從 Java 常`enums`量傳回到 C# 。 

**MetaData.xml**檔案是這些檔案匯入最多的檔案,因為它允許對繫結進行通用變更,例如:

- 重新命名命名空間、類、方法或欄位,以便它們遵循 .NET 約定。 

- 刪除不需要的命名空間、類、方法或欄位。 

- 將類移到不同的命名空間。 

- 添加其他支援類以使綁定的設計遵循 .NET 框架模式。 

讓我們繼續更詳細地討論**元數據.xml。**

## <a name="metadataxml-transform-file"></a>中繼資料.xml 轉換檔

正如我們已經知道的,檔**Metadata.xml**被綁定生成器用於影響綁定程式集的創建。
元資料格式使用[XPath](https://www.w3.org/TR/xpath/)語法,幾乎與*GAPI 中數據*指南中描述的[GAPI 中數據](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)相同。 此實現幾乎是 XPath 1.0 的完整實現,因此支援 1.0 標準中的項。 此檔是一種功能強大的機制,用於更改、添加、隱藏或移動 API 檔中的任何元素或屬性。 元資料規範中的所有規則元素都包含路徑屬性,用於標識要應用規則節點的節點。 規則依以下順序應用:

- **添加節點**&ndash;將子節點追加到路徑屬性指定的節點。
- **attr**&ndash;設定路徑屬性指定的元素的屬性的值。
- **刪除節點**&ndash;刪除與指定 XPath 匹配的節點。

下面是**中繼資料.xml**檔的範例:

```xml
<metadata>
    <!-- Normalize the namespace for .NET -->
    <attr path="/api/package[@name='com.evernote.android.job']" 
        name="managedName">Evernote.AndroidJob</attr>

    <!-- Don't  need these packages for the Xamarin binding/public API --> 
    <remove-node path="/api/package[@name='com.evernote.android.job.v14']" />
    <remove-node path="/api/package[@name='com.evernote.android.job.v21']" />

    <!-- Change a parameter name from the generic p0 to a more meaningful one. -->
    <attr path="/api/package[@name='com.evernote.android.job']/class[@name='JobManager']/method[@name='forceApi']/parameter[@name='p0']" 
        name="name">api</attr>
</metadata>
```

下面列出 Java API 的一些最常用的 XPath 元素:

- `interface`&ndash;用於查找 Java 介面。 例如 `/interface[@name='AuthListener']`。

- `class`&ndash;尋找類別 。 例如 `/class[@name='MapView']`。

- `method`&ndash;用於在 Java 類或介面上查找方法。 例如 `/class[@name='MapView']/method[@name='setTitleSource']`。

- `parameter`&ndash;標識方法的參數。 例如，`/parameter[@name='p0']`

### <a name="adding-types"></a>新增類型

這個`add-node`元素將告訴 Xamarin.Android 繫結項目,將新的包裝類別加入到**api.xml**。 例如,以下代碼段將指示綁定產生器建立具有建構函數和單個字段的類:

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```

### <a name="removing-types"></a>移除類型

可以指示 Xamarin.Android 綁定生成器忽略 JAVA 類型,而不是綁定它。 這是以`remove-node`XML 元素加入**到中繼資料.xml**檔案來實作的:

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>重新命名成員

無法透過直接編輯**api.xml**檔來重新命名成員,因為 Xamarin.Android 需要原始 Java 本機介面 (JNI) 名稱。 因此,`//class/@name`不能更改屬性;如果是,綁定將不起作用。

請考慮要重命名類型的情況。 `android.Manifest`
為此,我們可能會嘗試直接編輯**api.xml**並重命名類,如下所示:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

這將導致綁定產生器為包裝器類建立以下 C# 代碼:

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

請注意,包裝類已重新命名為`NewName`,而原始 JAVA`Manifest`類型仍為 。 Xamarin.Android 綁定類不再可能`android.Manifest`存取包裝類綁定到不存在的 JAVA 類型。

要正確更改包裝類型(或方法)的託管名稱,必須設置`managedName`如下示例所示的屬性:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>重新命名`EventArg`包裝類

當 Xamarin.Android 綁定產生`onXXX`器識別_偵聽器類型的_setter`EventArgs`方法時,將生成 C# 事件和子類以支援基於 JAVA 的偵聽器模式的 .NET 調味 API。 例如,請考慮以下 JAVA 類和方法:

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin.Android 將從 setter 方法中`on`刪除`2DSignNextManuever`前綴`EventArgs`, 而是用作 子類名稱的基礎。 子類別將命名為類似於:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

這不是合法的 C# 類別名稱。 要更正此問題,綁定作者必須使用`argsType`屬性`EventArgs`並為 子類提供有效的 C# 名稱:

```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

## <a name="supported-attributes"></a>受支援的屬性

以下各節介紹用於轉換 JAVA API 的一些屬性。

### <a name="argstype"></a>阿格斯類型

此屬性放置在 setter 方法上,`EventArg`以命名將生成的子類以支援 JAva 偵聽器。 下面將在本指南後面的「[重新命名事件阿格包裝器類](#Renaming_EventArg_Wrapper_Classes)」一節中詳細介紹這一點。

### <a name="eventname"></a>eventName

指定事件的名稱。 如果為空,則禁止生成事件。
這在標題「[重新命名事件阿格包裝器類」中](#Renaming_EventArg_Wrapper_Classes)進行了更詳細的描述。

### <a name="managedname"></a>託管名稱

這用於更改包、類、方法或參數的名稱。 例如,將 Java`MyClass`類別的名稱`NewClassName`變更為 :

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

下一個範例展示將方法`java.lang.object.toString`重新命名為`Java.Lang.Object.NewManagedName`的 XPath 表示式:

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>託管類型

`managedType`用於更改方法的返回類型。 在某些情況下,綁定生成器將錯誤地推斷 JAva 方法的返回類型,這將導致編譯時間錯誤。 在這種情況下,一個可能的解決方案是更改方法的返回類型。

例如,綁定生成器認為 JAVA`de.neom.neoreadersdk.resolution.compareTo()`方法`int`應`Object`返回 和 作為 參數,這將導致錯誤消息錯誤**CS0535:'DE。Neom.Neoreadersdk.解析度"不實現介面成員"Java.Lang.I可比較.比較比較(JAva.Lang.object)"。** 以下程式碼段展示如何產生的 C# 方法的第一個參數類型`DE.Neom.Neoreadersdk.Resolution``Java.Lang.Object`從 : 變更為 : 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]" name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>託管返回

更改方法的返回類型。 這不會更改返回屬性(因為對返回屬性的更改可能會導致對 JNI 簽名的不相容更改)。 在下面的範例中,`append`方法的傳回`SpannableStringBuilder`類型從`IAppendable`變更為 (回想 C# 不支援共變數傳回類型):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>混淆

混淆 Java 庫的工具可能會干擾 Xamarin.Android 綁定生成器及其生成 C# 包裝類的能力。 模糊類的特徵包括: 

- 類別名稱包括**$**,**$.類別**
- 類別名稱完全受小寫字元(即**a.類別**)的損壞

此程式碼段是如何產生「未模糊」C# 類型的範例:

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

此屬性可用於更改託管屬性的名稱。

使用`propertyName`的專門案例涉及 JAVA 類僅具有欄位 getter 方法的情況。 在此情況下,綁定生成器需要創建僅寫入屬性,這在 .NET 中是不希望的。 以下代碼段演示如何透過設定`propertyName`為空字串來'刪除'.NET 屬性:

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

請注意,設置器和 getter 方法仍將由綁定生成器創建。

### <a name="sender"></a>傳送者

指定方法的哪個參數應為方法映射到`sender`事件時的參數。 該值可以是`true`或`false`。 例如：

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>可見性

此屬性用於更改類、方法或屬性的可見性。 例如,可能需要升級`protected`JAVA 方法,以便其對應的 C#`public`包裝器是 :

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>Enltt.xml 和枚舉方法.xml

在某些情況下,Android 庫使用整數常量來表示傳遞給庫的屬性或方法狀態。 在許多情況下,將這些整數常量綁定到 C# 中的枚舉非常有用。 為了便於此映射,請使用綁定專案中的**EnumFields.xml**和**Enum方法.xml**檔。 

### <a name="defining-an-enum-using-enumfieldsxml"></a>使用枚舉場.xml 定義枚舉

**EnumFields.xml**檔包含`int`Java 常量`enums`和 C# 之間的映射。 讓我們舉以下範例,為一組`int`常量創建 C# 枚舉: 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

在這裡,我們採取了JAVA類`SKRealReachSettings`,並定義了一個C#枚`SKMeasurementUnit`舉 ,在`Skobbler.Ngx.Map.RealReach`命名空間 調用。 這些`field`條目定義 Java 常量`UNIT_SECOND`(範例)的名稱,即枚舉項的名稱`Second`(範例)和由兩個實體表示的整數值(示`0`例)。 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>使用 Enter 定義 Getter/Setter 方法.xml

**EnumMethod.xml**檔案允許變更方法參數,並將類型從`int`Java 常`enums`量傳回到 C# 。 換句話說,它將 C# 枚舉的讀取和寫入(在**EnumFields.xml**檔中定義)映`int``get`射`set`到 JAVA 常 量和方法。

給定上面`SKRealReachSettings`定義的枚舉,以下**enum 方法.xml**檔將定義此枚舉的 getter/setter:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

第一`method`行將`getMeasurementUnit`JAVA 方法的返回值`SKMeasurementUnit`映射到 枚舉。 第二`method`條線將的第`setMeasurementUnit`一個參數映射到同一枚舉例。

在所有這些變更都到位後,您可以使用 Xamarin.Android 中的以下代碼來`MeasurementUnit`設定 : 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```

## <a name="summary"></a>總結

本文討論了Xamarin.Android如何使用元數據從*谷歌**AOSP格式*轉換API定義。 在介紹了使用*Metadata.xml*可能發生的更改後,它檢查了重命名成員時遇到的限制,並介紹了支援的 XML 屬性列表,描述了何時應使用每個屬性。

## <a name="related-links"></a>相關連結

- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [繫結 Java 程式庫](~/android/platform/binding-java-library/index.md)
- [GAPI 中繼資料](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
