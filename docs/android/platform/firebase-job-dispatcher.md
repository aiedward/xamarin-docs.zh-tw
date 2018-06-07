---
title: Firebase 作業發送器
description: 本指南會討論如何使用 Firebase 作業發送器程式庫，將來自 Google 的背景工作的排程。
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 06/05/2018
ms.openlocfilehash: 0d512342f1c978c84341d20c298a9fa750800d84
ms.sourcegitcommit: 5db075bdd0b62d5d1d1567c267303a6a1888c8f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2018
ms.locfileid: "34806799"
---
# <a name="firebase-job-dispatcher"></a>Firebase 作業發送器

_本指南會討論如何使用 Firebase 作業發送器程式庫，將來自 Google 的背景工作的排程。_


## <a name="overview"></a>概觀

Android 應用程式能繼續回應使用者以最佳方式之一是確保複雜或長時間執行的工作在背景中執行。 不過，很重要，背景工作不會產生負面影響的使用者經驗與裝置。 

比方說，背景工作可能網站每隔三或四個分鐘輪詢查詢特定的資料集的變更。 不過，它會對電池壽命而言的影響，這似乎良性的。 應用程式重複會喚醒裝置，請將 CPU 提高為更高的電源狀態、 無線電開啟電源，確定網路要求，然後處理結果。 它變差，因為裝置將不會立即關閉電源，並返回低電源閒置狀態。 無法順利排程的背景工作不小心可能會保留裝置處於的不必要和多餘的電源需求。 這看似無害的活動 （輪詢網站） 將導致裝置在相對較短的時間內無法使用。

Android 提供下列應用程式開發介面，以協助在背景中執行的工作，但本身沒有足夠的智慧型工作排程。 

* **[意圖服務](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash;意圖服務非常適合用來執行工作，不過它們會不提供任何方法可以排程工作。
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash;只有這些 Api 可讓工作排程，但不能實際執行工作。 此外，AlarmManager 僅允許以時間為基礎的條件約束，這表示在特定時間或經過一段時間後會引發警示。 
* **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)**  &ndash; JobSchedule 是絕佳的 API，可搭配排程作業的作業系統。 不過，它是只適用於這些目標應用程式開發介面層級 21 的 Android 應用程式或更高版本。 
* **[廣播接收者](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; Android 應用程式可以設定廣播的接收者，以回應系統事件或對應方式執行工作。 不過，廣播的接收者不提供任何控制何時應執行的工作。 Android 作業系統中的變更也會限制當廣播的接收者也能運作，或它們可以回應的工作種類。 

有兩個主要的功能來有效率地執行背景工作 (有時稱為_背景工作_或_作業_):

1. **有智慧地排程工作**&ndash;很重要的應用程式會進行的工作在背景中的，它會以為良好的公民。 在理想情況下，應用程式不應要求執行作業。 相反地，應用程式應該指定當作業可以執行，並再排程符合條件時執行該工作必須符合的條件。 這可讓 Android，以聰明的方式執行的工作。 例如，網路要求可能會批次處理以發揮最大的負擔與網路有關的所有項目執行。
2. **封裝工作**&ndash;執行背景工作的程式碼應該封裝中不連續的元件，可以獨立使用者介面執行將會是相當容易重新排定工作無法完成基於某些原因。

Firebase 作業發送器是向 Google 提供 fluent 應用程式開發的 API，來簡化排程的背景工作的程式庫。 它會取代為 Google 雲端管理員。 Firebase 作業發送器包含下列 Api:

* A`Firebase.JobDispatcher.JobService`是抽象類別必須使用會在背景工作執行的邏輯擴充。
* A`Firebase.JobDispatcher.JobTrigger`宣告時應該啟動作業。 這通常表示為視窗的時間，例如，等待至少 30 秒之前啟動工作，但在 5 分鐘內執行作業。
* A`Firebase.JobDispatcher.RetryStrategy`包含應該要如何處理工作無法正確執行時的相關資訊。 嘗試再次執行工作之前要等待的重試策略指定多久。 
* A`Firebase.JobDispatcher.Constraint`是選擇性的值，描述之前，必須符合可執行工作，例如裝置 unmetered 的網路上的條件或充電。
* `Firebase.JobDispatcher.Job`是一種 API，可統一--工作單位，可由排程在使用前一個 Api `JobDispatcher`。 `Job.Builder`類別用來具現化`Job`。
* A`Firebasee.JobDispatcher.JobDispatcher`排程與作業系統的工作，以及提供方法來取消工作，如有必要，使用先前的三個 Api。

若要排程工作與 Firebase 作業發送器，Xamarin.Android 應用程式必須封裝擴充的型別中的程式碼`JobService`類別。 `JobService` 有三個生命週期的方法，可以呼叫作業的存留期間：

* **`bool OnStartJob(IJobParameters parameters)`** &ndash; 這個方法是，工作會發生，且應該永遠。 在主執行緒上執行。 這個方法會傳回`true`剩餘，如果沒有工作或`false`如果完成的工作。 
* **`bool OnStopJob(IJobParameters parameters)`** &ndash; 這會停止工作，因為某些原因時呼叫。 它應該傳回`true`如果應該重新排程稍後的工作。
* **`JobFinished(IJobParameters parameters, bool needsReschedule)`** &ndash; 這個方法時，會呼叫`JobService`任何非同步工作完成。 

若要將作業排程，應用程式會具現化`JobDispatcher`物件。 然後，`Job.Builder`用來建立`Job`物件，提供給`JobDispatcher`以再試一次並排程執行作業。

本指南將討論如何 Xamarin.Android 應用程式中加入 Firebase 作業發送器，並用來排程背景工作。

## <a name="requirements"></a>需求

Firebase 作業發送器需要 Android API 層級 9 或更高版本。 Firebase 作業發送器程式庫依賴 Google Play 服務; 所提供的部分元件裝置必須已安裝的 Google Play 服務。

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>使用中 Xamarin.Android Firebase 作業發送器程式庫

若要開始使用 Firebase 作業發送器，先新增[Xamarin.Firebase.JobDispatcher NuGet 封裝](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)Xamarin.Android 專案。 搜尋 NuGet 封裝管理員**Xamarin.Firebase.JobDispatcher**封裝 （也就是仍在發行前版本）。

加入 Firebase 作業發送器程式庫之後, 建立`JobService`類別，然後將它與執行個體執行排定`FirebaseJobDispatcher`。


### <a name="creating-a-jobservice"></a>建立 JobService

Firebase 作業發送器程式庫所執行的所有工作都必須要擴充的型別都來`Firebase.JobDispatcher.JobService`抽象類別。 建立`JobService`非常類似於建立`Service`Android 架構： 

1. 擴充`JobService`類別
2. 裝飾以子類別`ServiceAttribute`。 雖然並非絕對必要，建議您明確設定`Name`參數，以協助偵錯`JobService`。 
3. 新增`IntentFilter`宣告`JobService`中**AndroidManifest.xml**。 這也有助於找出並叫用的 Firebase 作業發送器庫`JobService`。

下列程式碼是最簡單的範例`JobService`的應用程式，使用 TPL 以非同步方式執行一些工作：

```csharp
[Service(Name = "com.xamarin.fjdtestapp.DemoJob")]
[IntentFilter(new[] {FirebaseJobServiceIntent.Action})]
public class DemoJob : JobService
{
    static readonly string TAG = "X:DemoService";

    public override bool OnStartJob(IJobParameters jobParameters)
    {
        Task.Run(() =>
        {
            // Work is happening asynchronously (code omitted)
                       
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(IJobParameters jobParameters)
    {
        Log.Debug(TAG, "DemoJob::OnStartJob");
        // nothing to do.
        return false;
    }
}
```

### <a name="creating-a-firebasejobdispatcher"></a>建立 FirebaseJobDispatcher

可以排程任何工作之前，就必須建立`Firebase.JobDispatcher.FirebaseJobDispatcher`物件。 `FirebaseJobDispatcher`負責排程`JobService`。 下列程式碼片段是一種方式建立的執行個體`FirebaseJobDispatcher`: 
 
 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

在先前的程式碼片段中，`GooglePlayDriver`是類別，可協助`FirebaseJobDispatcher`互動排程應用程式開發介面，在 Google Play 服務的某些裝置上。 參數`context`是任何 Android `Context`，例如的活動。 目前`GooglePlayDriver`是唯一`IDriver`Firebase 作業發送器文件庫中的實作。 

Firebase 作業發送器的 Xamarin.Android 繫結提供的擴充方法來建立`FirebaseJobDispatcher`從`Context`: 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

一次`FirebaseJobDispatcher`已具現化，便可建立`Job`和執行程式碼`JobService`類別。 `Job`由`Job.Builder`物件，並將在下一節中討論。

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>建立與 Job.Builder Firebase.JobDispatcher.Job

`Firebase.JobDispatcher.Job`類別會負責將中繼資料的封裝執行時所需`JobService`。 A`Job`包含資訊之前，必須符合作業可以執行，如果任何條件約束如`Job`為週期性，或將導致該工作會執行任何觸發程序。  以最低限度`Job`必須_標記_(識別作業的唯一字串`FirebaseJobDispatcher`) 及類型`JobService`應執行。 Firebase 作業發送器會具現化`JobService`時要執行此作業的時間。  A`Job`使用的執行個體建立`Firebase.JobDispatcher.Job.JobBuilder`類別。 

下列程式碼片段是最簡單的方式來建立範例`Job`使用 Xamarin.Android 繫結：

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

`Job.Builder`會執行某些基本驗證檢查的輸入值上的作業。 如果它不可能的則擲回例外狀況`Job.Builder`建立`Job`。  `Job.Builder`將建立`Job`使用下列預設值：

* A`Job`的_存留期_（多久它將會排定執行） 在裝置重新開機後，才是&ndash;裝置重新開機之後`Job`都會遺失。
* A`Job`不週期性&ndash;它將只執行一次。
* A`Job`會儘速執行排程。
* 預設重試策略`Job`是使用_指數型輪詢_(上一節中會更詳細探討討論[設定 RetryStrategy](#Setting_a_RetryStrategy))

### <a name="scheduling-a-job"></a>排程工作

在建立之後`Job`，它必須排程`FirebaseJobDispatcher`之前執行。 有兩種方法來排程`Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

所傳回的值`FirebaseJobDispatcher.Schedule`會是以下的整數值的其中一個：

* `FirebaseJobDispatcher.ScheduleResultSuccess` &ndash; `Job`已成功排程。
* `FirebaseJobDispatcher.ScheduleResultUnknownError` &ndash; 發生未知的問題發生了阻礙`Job`進行排程。
* `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable` &ndash; 無效的`IDriver`使用或`IDriver`由於某種原因而無法使用。 
* `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger` &ndash; `Trigger`不受支援。
* `FirebaseJobDispatcher.ScheduleResultBadService` &ndash; 服務未正確設定，或無法使用。
 
### <a name="configuring-a-job"></a>設定作業的作業

您可自訂的工作。 如何可自訂工作的範例包括：

* [將參數傳遞至作業](#Passing_Parameters_to_a_Job) &ndash; A`Job`可能需要額外的值來執行其工作，例如下載檔案。
* [設定條件約束](#Setting_Constraints)&ndash;可能需要符合特定條件時，才執行作業。 例如，只執行`Job`裝置時正在充電。 
* [指定何時`Job`應該執行](#Setting_Job_Triggers) &ndash; Firebase 作業發送器可以讓應用程式指定何時應執行工作的時間。  
* [宣告失敗作業的重試策略](#Setting_a_RetryStrategy) &ndash; A_重試策略_相關指引提供給`FirebaseJobDispatcher`上應該執行的動作`Jobs`，無法完成。 

每個主題都將在下列各節中更討論。

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-jarameters-to-a-job"></a>Jarameters 傳遞至作業

參數傳遞至作業建立`Bundle`連同傳遞`Job.Builder.SetExtras`方法：

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

`Bundle`存取從`IJobParameters.Extras`屬性`OnStartJob`方法：

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>設定條件約束

條件約束可協助降低成本或電池清空在裝置上的。 `Firebase.JobDispatcher.Constraint`類別做為整數值會定義這些條件約束：

* `Constraint.OnUnmeteredNetwork` &ndash; 僅當裝置連線到 unmetered 網路時，才能執行作業。 這是可用來防止使用者從產生資料費用。
* `Constraint.OnAnyNetwork` &ndash; 裝置連線到任何網路上執行作業。 如果指定連同`Constraint.OnUnmeteredNetwork`，這個值將會優先採用。
* `Constraint.DeviceCharging` &ndash; 裝置正在充電時，才執行作業。

設定條件約束`Job.Builder.SetConstraint`方法： 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

`JobTrigger`相關指引提供給作業系統的相關作業應該開始的時間。 A`JobTrigger`具有_執行視窗_定義時的排程的時間`Job`應該執行。 執行視窗有_啟動視窗_值和_結束視窗_值。 開始視窗裝置應執行工作之前等待的秒數，而結束視窗值的執行之前要等待的秒數上限`Job`。 

A`JobTrigger`可以建立`Firebase.Jobdispatcher.Trigger.ExecutionWindow`方法。  例如`Trigger.ExecutionWindow(15,60)`應該介於 15 到 60 秒，從它的排程時間執行作業的方式。 `Job.Builder.SetTrigger`方法是用來 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

預設值`JobTrigger`的作業由值`Trigger.Now`，以指定的工作會執行排程之後，儘速...

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>設定 RetryStrategy

`Firebase.JobDispatcher.RetryStrategy`用來指定一部裝置的延遲量應該使用然後再嘗試重新執行失敗的作業。 A`RetryStrategy`具有_原則_，而後者可定義何種時間基底演算法會用來重新排定 失敗的工作，並指定已排程作業的視窗執行視窗。 這_方法是重新排定視窗_由兩個值所定義。 第一個值是重新排定工作之前要等待的秒數 (_初始輪詢_值)，而且第二個數字的作業必須在執行之前的秒數上限 (_最大輪詢_值)。 
 
這些 int 值所識別的重試原則的兩種類型：

* `RetryStrategy.RetryPolicyExponential` &ndash; _指數型輪詢_原則將會以指數方式增加初始輪詢值之後每一項失敗。 工作失敗，第一次程式庫會等候指定然後再重新排定工作的 _initial 間隔&ndash;範例 30 秒。 作業失敗時，第二次程式庫會等候至少 60 秒後再嘗試執行此作業。 第三個嘗試失敗之後，程式庫會等待 120 秒，以此類推。 預設值`RetryStrategy`Firebase 作業發送器程式庫由`RetryStrategy.DefaultExponential`物件。 它有 30 秒的初始輪詢和 3600 秒的最大輪詢。
* `RetryStrategy.RetryPolicyLinear` &ndash; 此策略是_線性輪詢_作業應該重新排程為在執行設定的間隔 （直到它成功）。 線性輪詢最適合針對必須儘快完成的工作或將快速自行解決問題。 Firebase 作業發送器程式庫定義`RetryStrategy.DefaultLinear`具有至少 30 秒的方法是重新排定視窗、 最高達 3600 秒。

可以定義自訂`RetryStrategy`與`FirebaseJobDispatcher.NewRetryStrategy`方法。 它會採用三個參數：

1. `int policy` &ndash; _原則_是其中一個先前`RetryStrategy`值`RetryStrategy.RetryPolicyLinear`，或`RetryStrategy.RetryPolicyExponential`。
2. `int intialBackoffSeconds` &ndash; _初始輪詢_延遲，以秒為單位，必須先嘗試再次執行該工作。 這個預設值為 30 秒。 
3. `int maximumBackoffSeconds` &ndash; _最大輪詢_值宣告的嘗試再次執行工作之前要延遲的秒數上限。 預設值為 3600 秒。 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>取消作業

若要取消已排程的所有作業或只是單一工作，使用`FirebaseJobDispatcher.CancelAll()`方法或`FirebaseJobDispatcher.Cancel(string)`方法：

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

這兩種方法會傳回整數值：

* `FirebaseJobDispatcher.CancelResultSuccess` &ndash; 已成功取消作業。
* `FirebaseJobDispatcher.CancelResultUnknownError` &ndash; 發生錯誤，無法從已取消作業。
* `FirebaseJobDispatcher.CancelResult.NoDriverAvailable` &ndash; `FirebaseJobDispatcher`無法取消工作，因為沒有不具有效`IDriver`可用。

## <a name="summary"></a>摘要

本指南將討論如何使用 Firebase 作業發送器有智慧地在背景中執行的工作。 它討論如何封裝要執行為工作`JobService`以及如何使用`FirebaseJobDispatcher`排程的工作，指定與條件`JobTrigger`和失敗應該如何處理與`RetryStrategy`。


## <a name="related-links"></a>相關連結

- [需由 NuGet Xamarin.Firebase.JobDispatcher](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [GitHub 上 firebase-工作-發送器](https://github.com/firebase/firebase-jobdispatcher-android)
- [Xamarin.Firebase.JobDispatcher 繫結](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [智慧型工作排程](https://developer.android.com/topic/performance/scheduling.html)
- [Android 使用電池及記憶體最佳化的 Google I/O 2016 （影片）](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
