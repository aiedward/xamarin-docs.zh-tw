---
title: 在 Xamarin.Forms 中處理的檔案
description: 使用 Xamarin.Forms 處理的檔案，可以使用程式碼，在.NET Standard 程式庫，或使用內嵌的資源來達成。
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: ddffb45b8cd8d47371e4ab57f30a467cea45b27d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117768"
---
# <a name="file-handling-in-xamarinforms"></a>在 Xamarin.Forms 中處理的檔案

_使用 Xamarin.Forms 處理的檔案，可以使用程式碼，在.NET Standard 程式庫，或使用內嵌的資源來達成。_

## <a name="overview"></a>總覽

Xamarin.Forms 程式碼會在多個平台上執行 - 每一個都有自己的檔案系統。 先前，也就是說，讀取和寫入檔案是最容易執行每個平台上使用原生檔案 Api。 或者，內嵌的資源會是更簡單的解決方案，可將資料檔案與應用程式。 不過，透過.NET Standard 2.0 就可以共用.NET Standard 程式庫中的檔案存取程式碼。

如需處理映像檔的資訊，請參閱[處理映像](~/xamarin-forms/user-interface/images.md)頁面。

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>儲存和載入檔案

`System.IO`類別可以用來存取每個平台上的檔案系統。 `File`類別可讓您建立、 刪除和讀取檔案，而`Directory`類別可讓您建立、 刪除或列舉目錄的內容。 您也可以使用`Stream`子類別，其可提供更高的控制檔案作業 （例如壓縮或位置搜尋檔案中）。

可以使用撰寫文字檔`File.WriteAllText`方法：

```csharp
File.WriteAllText(fileName, text);
```

可以使用來讀取文字檔`File.ReadAllText`方法：

```csharp
string text = File.ReadAllText(fileName);
```

颾魤 ㄛ`File.Exists`方法會判斷指定的檔案是否存在：

```csharp
bool doesExist = File.Exists(fileName);
```

每個平台上的檔案路徑可以從.NET Standard 程式庫使用的值判斷[ `Environment.SpecialFolder` ](xref:System.Environment.SpecialFolder)列舉型別做為第一個引數`Environment.GetFolderPath`方法。 然後使用檔案名稱結合`Path.Combine`方法：

```csharp
string fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "temp.txt");
```

這些作業，包括儲存及載入文字的頁面的範例應用程式示範：

[![儲存及載入文字](files-images/saveandload-sml.png "儲存和載入應用程式中的檔案")](files-images/saveandload.png#lightbox "儲存和載入應用程式中的檔案")

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>載入內嵌為資源的檔案

若要內嵌至檔案 **.NET Standard**組件，建立或加入檔案，並確保**建置動作： EmbeddedResource**。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![設定內嵌資源建置動作](files-images/vs-embeddedresource-sml.png "設定 EmbeddedResource BuildAction")](files-images/vs-embeddedresource.png#lightbox "設定 EmbeddedResource 建置動作")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![文字檔案內嵌在 PCL 中，設定內嵌的資源建置動作](files-images/xs-embeddedresource-sml.png "設定 EmbeddedResource BuildAction")](files-images/xs-embeddedresource.png#lightbox "設定 EmbeddedResource 建置動作")

-----

`GetManifestResourceStream` 用來存取內嵌的檔案使用其**資源識別碼**。 根據預設，資源識別碼是使用內嵌在專案的預設命名空間前置詞的檔案名稱在此情況下組件是**WorkingWithFiles**檔案名稱，而且**PCLTextResource.txt**，讓資源識別碼是`WorkingWithFiles.PCLTextResource.txt`。

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream)) {
    text = reader.ReadToEnd ();
}
```

`text`變數再用於顯示文字，或使用它在程式碼中。 下列螢幕擷取畫面[範例應用程式](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)會顯示在轉譯的文字`Label`控制項。

 [![文字檔案內嵌在 PCL](files-images/pcltext-sml.png "內嵌的文字檔案中的應用程式中顯示的 PCL")](files-images/pcltext.png#lightbox "內嵌的文字檔案中的應用程式中顯示的 PCL")

載入和還原序列化 XML 也一樣簡單。 下列程式碼會示範 XML 檔案進行載入，並還原序列化從一項資源，然後繫結至`ListView`顯示。 XML 檔案包含的陣列`Monkey`（已定義類別的範例程式碼） 的物件。

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLXmlResource.xml");
List<Monkey> monkeys;
using (var reader = new System.IO.StreamReader (stream)) {
    var serializer = new XmlSerializer(typeof(List<Monkey>));
    monkeys = (List<Monkey>)serializer.Deserialize(reader);
}
var listView = new ListView ();
listView.ItemsSource = monkeys;
```

 [![Xml 檔內嵌在 PCL 中，顯示在 ListView](files-images/pclxml-sml.png "內嵌的 XML 檔案，在 ListView 中顯示的 PCL")](files-images/pclxml.png#lightbox "內嵌的 XML 檔案，在 ListView 中顯示的 PCL")

<a name="Embedding_in_Shared_Projects" />

### <a name="embedding-in-shared-projects"></a>在共用專案中內嵌

不過，前置詞用於共用專案的內容會編譯到參考的專案，因為內嵌檔案資源識別碼可以變更共用專案也可以做為內嵌資源，包含檔案。 這表示每個內嵌檔案的資源識別碼可能會不同，每個平台。

有兩個解決方案使用共用專案的這個問題：

-  **同步處理專案**-編輯專案屬性，若要使用的每個平台**相同**組件名稱和預設命名空間。 然後，這個值可以是 「 硬式編碼 」，為內嵌的資源識別碼，在共用專案中的前置詞。
-  **#if 編譯器指示詞**-使用編譯器指示詞來設定正確的資源識別碼前置詞，並使用該值，以動態方式建構正確的資源識別碼。


程式碼來說明第二個選項如下所示。 編譯器指示詞用來選取 （這是正常的參考專案的預設命名空間相同） 的硬式編碼資源前置詞。 `resourcePrefix`變數接著會用來建立有效的資源識別碼，藉由串連為內嵌的資源檔案名稱。

```csharp
#if __IOS__
var resourcePrefix = "WorkingWithFiles.iOS.";
#endif
#if __ANDROID__
var resourcePrefix = "WorkingWithFiles.Droid.";
#endif

Debug.WriteLine("Using this resource prefix: " + resourcePrefix);
// note that the prefix includes the trailing period '.' that is required
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
Stream stream = assembly.GetManifestResourceStream
    (resourcePrefix + "SharedTextResource.txt");
```

<a name="Organizing_Resources" />

### <a name="organizing-resources"></a>組織資源

上述範例假設檔案內嵌在表單的大小寫的資源識別碼的.NET Standard 程式庫專案的根目錄**Namespace.Filename.Extension**，例如`WorkingWithFiles.PCLTextResource.txt`和`WorkingWithFiles.iOS.SharedTextResource.txt`。

可以將組織資料夾中的內嵌的資源。 當內嵌的資源會放置於某個資料夾時，資料夾名稱會成為組件的資源識別碼 （以句點分隔），讓資源識別碼的格式會變成**Namespace.Folder.Filename.Extension**。 放置到資料夾的範例應用程式中使用的檔案**MyFolder**會使對應的資源識別碼`WorkingWithFiles.MyFolder.PCLTextResource.txt`和`WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`。

<a name="Debugging_Embedded_Resources" />

### <a name="debugging-embedded-resources"></a>偵錯的內嵌的資源

它有時很難了解為什麼未載入特定的資源，因為下列偵錯程式碼可以暫時新增的應用程式，以協助確認已正確設定的資源。 它會輸出到指定的組件中內嵌的所有已知的資源**錯誤**板，以協助偵錯載入問題的資源。

```csharp
using System.Reflection;
// ...
// use for debugging, not in released app code!
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
foreach (var res in assembly.GetManifestResourceNames()) {
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

## <a name="summary"></a>總結

這篇文章已示範一些簡單的檔案作業，來儲存及載入裝置上的文字，並載入內嵌的資源。 使用.NET Standard 2.0 就可以共用.NET Standard 程式庫中的檔案存取程式碼。

## <a name="related-links"></a>相關連結

- [FilesSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)
- [Xamarin.Forms 範例](https://github.com/xamarin/xamarin-forms-samples)
- [使用 Xamarin.iOS 中的檔案系統](~/ios/app-fundamentals/file-system.md)

