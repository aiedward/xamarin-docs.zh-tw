---
title： " Xamarin.Essentials ：剪貼簿" 描述： "本檔描述中的剪貼簿類別 Xamarin.Essentials ，可讓您將文字複製並貼到應用程式之間的系統剪貼簿。
assetid： C52AE99A-0FB3-425D-9106-3DA5777FEFA0 author： jamesmontemagno ms-chap： jamont ms. date： 01/06/2020 ms. custom： video no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials：剪貼簿

**剪貼簿**類別可讓您在應用程式之間複製文字，並貼到系統剪貼簿。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-clipboard"></a>使用 [剪貼簿]

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

檢查 [剪貼簿]**** 是否已具有要貼上的文字：

```csharp
var hasText = Clipboard.HasText;
```

將文字設定至 [剪貼簿]****：

```csharp
await Clipboard.SetTextAsync("Hello World");
```

從 [剪貼簿]**** 讀取文字：

```csharp
var text = await Clipboard.GetTextAsync();
```

每當剪貼簿的任何內容變更時，就會觸發事件：

```csharp
public class ClipboardTest
{
    public ClipboardTest()
    {
        // Register for clipboard changes, be sure to unsubscribe when needed
        Clipboard.ClipboardContentChanged += OnClipboardContentChanged;
    }

    void OnClipboardContentChanged(object sender, EventArgs    e)
    {
        Console.WriteLine($"Last clipboard change at {DateTime.UtcNow:T}";);
    }
}
```

> [!TIP]
> 對剪貼簿的存取必須在主要使用者介面執行緒上完成。 請參閱 [MainThread](~/essentials/main-thread.md) API 以了解如何叫用主要使用者介面執行緒上的方法。

## <a name="api"></a>API

- [剪貼簿原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [剪貼簿 API 文件](xref:Xamarin.Essentials.Clipboard)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Clipboard-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
