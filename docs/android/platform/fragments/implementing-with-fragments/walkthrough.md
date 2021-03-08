---
title: Xamarin. Android 片段逐步解說-第1部分
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: ED368FA9-A34E-DC39-D535-5C34C32B9761
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: e7aa47b0d5602d9cdc0eb6e026333cfe85ade933
ms.sourcegitcommit: 2d52346fa1407358e57c339a130a2330bad8e5b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2021
ms.locfileid: "102446472"
---
# <a name="fragments-walkthrough-ndash-phone"></a>片段逐步解說 &ndash; 電話

這是逐步解說的第一個部分，將建立以直向的 Android 裝置為目標的 Xamarin Android 應用程式。 本逐步解說會討論如何在 Xamarin 中建立片段，以及如何將它們新增至範例。

[![逐步解說應用程式螢幕擷取畫面](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

系統會為此應用程式建立下列類別：

1. `PlayQuoteFragment`&nbsp;此片段會顯示 William Shakespeare play 的報價。 它將由裝載 `PlayQuoteActivity` 。
1. `Shakespeare`&nbsp;此類別會將兩個硬式編碼的陣列保存為屬性。
1. `TitlesFragment`&nbsp;此片段會顯示 William Shakespeare 所撰寫的播放標題清單。 它將由裝載 `MainActivity` 。
1. `PlayQuoteActivity`&nbsp; `TitlesFragment` 將會啟動， `PlayQuoteActivity` 以回應選取播放的使用者 `TitlesFragment` 。

## <a name="1-create-the-android-project"></a>1. 建立 Android 專案

建立名為 **FragmentSample** 的新 Xamarin. Android 專案。
# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![建立新的 Xamarin.Android 專案](./walkthrough-images/01-newproject.w157-sml.png)](./walkthrough-images/01-newproject.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![建立新的 Xamarin. Android 專案](./walkthrough-images/01-newproject.m742-sml.png)](./walkthrough-images/01-newproject.m742.png#lightbox)

建議您在此逐步解說中選取 **新式開發** 。

建立專案之後，將檔案配置 **/.axml** 重新命名為 **layout/activity_main. .axml**。

-----

## <a name="2-add-the-data"></a>2. 新增資料

此應用程式的資料會儲存在兩個屬於類別名稱屬性的硬式編碼字串陣列中 `Shakespeare` ：

* `Shakespeare.Titles`&nbsp;此陣列將保留 William Shakespeare 的播放清單。 這是的資料來源 `TitlesFragment` 。
* `Shakespeare.Dialogue`&nbsp;此陣列將會保留其中一個播放的引號清單 `Shakespeare.Titles` 。 這是的資料來源 `PlayQuoteFragment` 。

將新的 c # 類別加入至 **FragmentSample** 專案，並將其命名為 **Shakespeare.cs**。 在此檔案中， `Shakespeare` 使用下列內容建立名為的新 c # 類別

```csharp
class Shakespeare
{
    public static string[] Titles = {
                                      "Henry IV (1)",
                                      "Henry V",
                                      "Henry VIII",
                                      "Richard II",
                                      "Richard III",
                                      "Merchant of Venice",
                                      "Othello",
                                      "King Lear"
                                    };

    public static string[] Dialogue = {
                                        "So shaken as we are, so wan with care, Find we a time for frighted peace to pant, And breathe short-winded accents of new broils To be commenced in strands afar remote. No more the thirsty entrance of this soil Shall daub her lips with her own children's blood; Nor more shall trenching war channel her fields, Nor bruise her flowerets with the armed hoofs Of hostile paces: those opposed eyes, Which, like the meteors of a troubled heaven, All of one nature, of one substance bred, Did lately meet in the intestine shock And furious close of civil butchery Shall now, in mutual well-beseeming ranks, March all one way and be no more opposed Against acquaintance, kindred and allies: The edge of war, like an ill-sheathed knife, No more shall cut his master. Therefore, friends, As far as to the sepulchre of Christ, Whose soldier now, under whose blessed cross We are impressed and engaged to fight, Forthwith a power of English shall we levy; Whose arms were moulded in their mothers' womb To chase these pagans in those holy fields Over whose acres walk'd those blessed feet Which fourteen hundred years ago were nail'd For our advantage on the bitter cross. But this our purpose now is twelve month old, And bootless 'tis to tell you we will go: Therefore we meet not now. Then let me hear Of you, my gentle cousin Westmoreland, What yesternight our council did decree In forwarding this dear expedience.",
                                        "Hear him but reason in divinity, And all-admiring with an inward wish You would desire the king were made a prelate: Hear him debate of commonwealth affairs, You would say it hath been all in all his study: List his discourse of war, and you shall hear A fearful battle render'd you in music: Turn him to any cause of policy, The Gordian knot of it he will unloose, Familiar as his garter: that, when he speaks, The air, a charter'd libertine, is still, And the mute wonder lurketh in men's ears, To steal his sweet and honey'd sentences; So that the art and practic part of life Must be the mistress to this theoric: Which is a wonder how his grace should glean it, Since his addiction was to courses vain, His companies unletter'd, rude and shallow, His hours fill'd up with riots, banquets, sports, And never noted in him any study, Any retirement, any sequestration From open haunts and popularity.",
                                        "I come no more to make you laugh: things now, That bear a weighty and a serious brow, Sad, high, and working, full of state and woe, Such noble scenes as draw the eye to flow, We now present. Those that can pity, here May, if they think it well, let fall a tear; The subject will deserve it. Such as give Their money out of hope they may believe, May here find truth too. Those that come to see Only a show or two, and so agree The play may pass, if they be still and willing, I'll undertake may see away their shilling Richly in two short hours. Only they That come to hear a merry bawdy play, A noise of targets, or to see a fellow In a long motley coat guarded with yellow, Will be deceived; for, gentle hearers, know, To rank our chosen truth with such a show As fool and fight is, beside forfeiting Our own brains, and the opinion that we bring, To make that only true we now intend, Will leave us never an understanding friend. Therefore, for goodness' sake, and as you are known The first and happiest hearers of the town, Be sad, as we would make ye: think ye see The very persons of our noble story As they were living; think you see them great, And follow'd with the general throng and sweat Of thousand friends; then in a moment, see How soon this mightiness meets misery: And, if you can be merry then, I'll say A man may weep upon his wedding-day.",
                                        "First, heaven be the record to my speech! In the devotion of a subject's love, Tendering the precious safety of my prince, And free from other misbegotten hate, Come I appellant to this princely presence. Now, Thomas Mowbray, do I turn to thee, And mark my greeting well; for what I speak My body shall make good upon this earth, Or my divine soul answer it in heaven. Thou art a traitor and a miscreant, Too good to be so and too bad to live, Since the more fair and crystal is the sky, The uglier seem the clouds that in it fly. Once more, the more to aggravate the note, With a foul traitor's name stuff I thy throat; And wish, so please my sovereign, ere I move, What my tongue speaks my right drawn sword may prove.",
                                        "Now is the winter of our discontent Made glorious summer by this sun of York; And all the clouds that lour'd upon our house In the deep bosom of the ocean buried. Now are our brows bound with victorious wreaths; Our bruised arms hung up for monuments; Our stern alarums changed to merry meetings, Our dreadful marches to delightful measures. Grim-visaged war hath smooth'd his wrinkled front; And now, instead of mounting barded steeds To fright the souls of fearful adversaries, He capers nimbly in a lady's chamber To the lascivious pleasing of a lute. But I, that am not shaped for sportive tricks, Nor made to court an amorous looking-glass; I, that am rudely stamp'd, and want love's majesty To strut before a wanton ambling nymph; I, that am curtail'd of this fair proportion, Cheated of feature by dissembling nature, Deformed, unfinish'd, sent before my time Into this breathing world, scarce half made up, And that so lamely and unfashionable That dogs bark at me as I halt by them; Why, I, in this weak piping time of peace, Have no delight to pass away the time, Unless to spy my shadow in the sun And descant on mine own deformity: And therefore, since I cannot prove a lover, To entertain these fair well-spoken days, I am determined to prove a villain And hate the idle pleasures of these days. Plots have I laid, inductions dangerous, By drunken prophecies, libels and dreams, To set my brother Clarence and the king In deadly hate the one against the other: And if King Edward be as true and just As I am subtle, false and treacherous, This day should Clarence closely be mew'd up, About a prophecy, which says that 'G' Of Edward's heirs the murderer shall be. Dive, thoughts, down to my soul: here Clarence comes.",
                                        "To bait fish withal: if it will feed nothing else, it will feed my revenge. He hath disgraced me, and hindered me half a million; laughed at my losses, mocked at my gains, scorned my nation, thwarted my bargains, cooled my friends, heated mine enemies; and what's his reason? I am a Jew. Hath not a Jew eyes? hath not a Jew hands, organs, dimensions, senses, affections, passions? fed with the same food, hurt with the same weapons, subject to the same diseases, healed by the same means, warmed and cooled by the same winter and summer, as a Christian is? If you prick us, do we not bleed? if you tickle us, do we not laugh? if you poison us, do we not die? and if you wrong us, shall we not revenge? If we are like you in the rest, we will resemble you in that. If a Jew wrong a Christian, what is his humility? Revenge. If a Christian wrong a Jew, what should his sufferance be by Christian example? Why, revenge. The villany you teach me, I will execute, and it shall go hard but I will better the instruction.",
                                        "Virtue! a fig! 'tis in ourselves that we are thus or thus. Our bodies are our gardens, to the which our wills are gardeners: so that if we will plant nettles, or sow lettuce, set hyssop and weed up thyme, supply it with one gender of herbs, or distract it with many, either to have it sterile with idleness, or manured with industry, why, the power and corrigible authority of this lies in our wills. If the balance of our lives had not one scale of reason to poise another of sensuality, the blood and baseness of our natures would conduct us to most preposterous conclusions: but we have reason to cool our raging motions, our carnal stings, our unbitted lusts, whereof I take this that you call love to be a sect or scion.",
                                        "Blow, winds, and crack your cheeks! rage! blow! You cataracts and hurricanoes, spout Till you have drench'd our steeples, drown'd the cocks! You sulphurous and thought-executing fires, Vaunt-couriers to oak-cleaving thunderbolts, Singe my white head! And thou, all-shaking thunder, Smite flat the thick rotundity o' the world! Crack nature's moulds, an germens spill at once, That make ingrateful man!"
                                    };
}
```

## <a name="3-create-the-playquotefragment"></a>3. 建立 PlayQuoteFragment

`PlayQuoteFragment`是 Android 片段，它會顯示使用者先前在應用程式中選取的 Shakespeare 播放報價，此片段不會使用 Android 版面配置檔案，而是會動態建立其使用者介面。 將名為 `Fragment` 的新類別新增 `PlayQuoteFragment` 至專案：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![新增 c # 類別](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/02-addclass.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![新增 c # 類別](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/02-addclass.m742.png#lightbox)

-----

然後，將片段的程式碼變更為類似下列程式碼片段：

```csharp
public class PlayQuoteFragment : Fragment
{
    public int PlayId => Arguments.GetInt("current_play_id", 0);

    public static PlayQuoteFragment NewInstance(int playId)
    {
        var bundle = new Bundle();
        bundle.PutInt("current_play_id", playId);
        return new PlayQuoteFragment {Arguments = bundle};
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        if (container == null)
        {
            return null;
        }

        var textView = new TextView(Activity);
        var padding = Convert.ToInt32(TypedValue.ApplyDimension(ComplexUnitType.Dip, 4, Activity.Resources.DisplayMetrics));
        textView.SetPadding(padding, padding, padding, padding);
        textView.TextSize = 24;
        textView.Text = Shakespeare.Dialogue[PlayId];

        var scroller = new ScrollView(Activity);
        scroller.AddView(textView);

        return scroller;
    }
}
```

這是 Android 應用程式中的常見模式，可提供將會具現化片段的 factory 方法。 這可確保會使用必要的參數來建立片段，以便正常運作。 在此逐步解說中，應用程式預期會在 `PlayQuoteFragment.NewInstance` 每次選取報價時使用方法來建立新的片段。 `NewInstance`方法會採用單一參數 &ndash; 來顯示要顯示的報價索引。

`OnCreateView`當您要在螢幕上呈現片段時，Android 會叫用方法。 它會傳回 `View` 屬於片段的 Android 物件。 此片段不會使用版面配置檔案來建立視圖。 相反地，它會藉由具現化 **TextView** 來保存報價，以程式設計方式建立視圖，並將該 widget 顯示在 **ScrollView** 中。

> [!NOTE]
> 片段子類別必須有不具參數的公用預設的函式。

## <a name="4-create-the-playquoteactivity"></a>4. 建立 PlayQuoteActivity

片段必須裝載于活動內，因此此應用程式需要將裝載的活動 `PlayQuoteFragment` 。 活動會在執行時間以動態方式將片段新增至其版面配置。 將新的活動加入至應用程式，並將其命名為 `PlayQuoteActivity` ：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![將 Android 活動新增至專案](./walkthrough-images/03-addactivity.w157-sml.png)](./walkthrough-images/03-addactivity.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![將 Android 活動新增至專案](./walkthrough-images/03-addactivity.m742-sml.png)](./walkthrough-images/03-addactivity.m742.png#lightbox)

-----

編輯中的程式碼 `PlayQuoteActivity` ：

```csharp
using AndroidX.Fragment.App;

[Activity(Label = "PlayQuoteActivity")]
public class PlayQuoteActivity : FragmentActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        var playId = Intent.Extras.GetInt("current_play_id", 0);

        var detailsFrag = PlayQuoteFragment.NewInstance(playId);
        SupportFragmentManager.BeginTransaction()
                        .Add(Android.Resource.Id.Content, detailsFrag)
                        .Commit();
    }
}
```

當 `PlayQuoteActivity` 建立時，它會在的內容中，將新 `PlayQuoteFragment` 的和載入該片段的根視圖中 `FragmentTransaction` 。 請注意，此活動不會為其使用者介面載入 Android 版面配置檔案。 相反 `PlayQuoteFragment` 地，會在應用程式的根視圖中加入新的。 資源識別碼 `Android.Resource.Id.Content` 是用來參考活動的根視圖，而不知道它的特定識別碼。

## <a name="5-create-titlesfragment"></a>5. 建立 TitlesFragment

`TitlesFragment`會將稱為的特製化片段子類別化 `ListFragment` ，以封裝 `ListView` 在片段中顯示的邏輯。 會 `ListFragment` 公開 `ListAdapter` 屬性 (用 `ListView` 來顯示其內容) 以及名為的事件處理常式，可 `OnListItemClick` 讓片段回應所顯示之資料列上的按一下動作 `ListView` 。

若要開始使用，請在專案中加入新的片段，並將其命名為 **TitlesFragment**：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![將 Android 片段新增至專案](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/04-addfragment.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![將 Android 片段新增至專案](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/04-addfragment.m742.png#lightbox)

-----

編輯片段內的程式碼：

```csharp
using AndroidX.Fragment.App;

public class TitlesFragment : ListFragment
{
    int selectedPlayId;

    public TitlesFragment()
    {
        // Being explicit about the requirement for a default constructor.
    }

    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        ListAdapter = new ArrayAdapter<String>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
        }
    }

    public override void OnSaveInstanceState(Bundle outState)
    {
        base.OnSaveInstanceState(outState);
        outState.PutInt("current_play_id", selectedPlayId);
    }

    public override void OnListItemClick(ListView l, View v, int position, long id)
    {
        ShowPlayQuote(position);
    }

    void ShowPlayQuote(int playId)
    {
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

建立活動時，Android 會叫用 `OnCreate` 片段的方法，這是建立清單介面卡的位置 `ListView` 。  `ShowQuoteFromPlay`方法會啟動的實例 `PlayQuoteActivity` ，以顯示所選播放的報價。

## <a name="display-titlesfragment-in-mainactivity"></a>在 >mainactivity 中顯示 TitlesFragment

最後一個步驟是 `TitlesFragment` 在中顯示 `MainActivity` 。 活動不會動態載入片段。 相反地，會使用專案在活動的版面配置檔案中宣告片段，以靜態方式載入片段 `fragment` 。 要載入的片段是藉由將 `android:name` 屬性設定為片段類別 (，包括) 類型的命名空間來識別。 例如，若要使用 `TitlesFragment` ，則會將 `android:name` 設定為 `FragmentSample.TitlesFragment` 。

編輯版面配置檔案 **activity_main .axml**，並以下列內容取代現有的 XML：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <fragment
        android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```

> [!NOTE]
> `class`屬性是的有效替代 `android:name` 。 慣用的表單沒有正式的指導方針，有許多程式碼基底的範例會 `class` 與一起使用 `android:name` 。

>mainactivity 不需要變更程式碼。 該類別中的程式碼應該與此程式碼片段非常類似：

```csharp
using AndroidX.Fragment.App;

[Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
public class MainActivity : FragmentActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        SetContentView(Resource.Layout.activity_main);
    }
}
```

## <a name="run-the-app"></a>執行應用程式

現在程式碼已完成，請在裝置上執行應用程式，以查看其實際運作情況。

[![在手機上執行之應用程式的螢幕擷取畫面。](./walkthrough-images/05-app-screenshots-sml.png)](./walkthrough-images/05-app-screenshots.png#lightbox)

[本逐步解說的第2部分](./walkthrough-landscape.md) 會 optimtize 此應用程式，以供在橫向模式中執行的裝置。
