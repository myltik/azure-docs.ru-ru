---
title: "Как использовать хранилище BLOB-объектов из Xamarin | Документация Майкрософт"
description: "Клиентская библиотека службы хранилища Azure для Xamarin позволяет разработчикам создавать приложения Магазина Windows, iOS и Android, используя их собственные пользовательские интерфейсы. В этом учебнике материале показано, как в Xamarin создать приложение, которое использует хранилище BLOB-объектов Azure."
services: storage
documentationcenter: xamarin
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: 44cb845d-cf78-4942-95b8-952da4f9a2c2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
translationtype: Human Translation
ms.sourcegitcommit: 5e531c6c23c510936fe7e4e844db2ec3afb170dc
ms.openlocfilehash: 32d79cf050968738764c2239cbd79c73734ee855
ms.lasthandoff: 01/30/2017


---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Как использовать хранилище BLOB-объектов из Xamarin
[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## <a name="overview"></a>Обзор
Xamarin позволяет разработчикам использовать общий язык C# для создания приложений для магазина Windows, iOS и Android из их собственных пользовательских интерфейсов. В этом учебнике показано, как использовать хранилище BLOB-объектов Azure с приложением Xamarin. Если вы хотите узнать больше о службе хранилища Azure, прежде чем углубиться в код, ознакомьтесь с разделом [Знакомство со службой хранилища Microsoft Azure](storage-introduction.md).

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Создание нового приложения Xamarin
Чтобы приступить к работе, мы создадим приложение, нацеленное на Android, iOS и Windows. Это приложение просто создаст контейнер и передаст в него большой двоичный объект. Мы будем использовать Visual Studio в Windows, но вы можете использовать полученные знания и при создании приложения с помощью Xamarin Studio в Mac OS.

Выполните следующие действия, чтобы создать приложение.

1. Если вы еще не сделали этого, скачайте и установите [Xamarin для Visual Studio](https://www.xamarin.com/download).
2. Откройте Visual Studio и создайте пустое приложение (Native Portable): **Файл > Создать > Проект > Кроссплатформенный > Пустое приложение (Native Portable)**.
3. В области обозревателя решений щелкните правой кнопкой мыши свое решение и выберите пункт **Управление пакетами NuGet для решения**. Найдите пакет **WindowsAzure.Storage** и установите последнюю стабильную версию во все проекты в решении.
4. Выполните сборку проекта и запустите его.

Теперь у вас должно быть приложение, в котором можно нажать кнопку, увеличивающую значение счетчика.

## <a name="create-container-and-upload-blob"></a>Создание контейнера и передача большого двоичного объекта
Далее вы добавите код для `MyClass.cs` в свой проект `(Portable)`. Этот код создает контейнер и передает в него большой двоичный объект. `MyClass.cs` должен выглядеть следующим образом.

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System.Threading.Tasks;

namespace XamarinApp
{
    public class MyClass
    {
        public MyClass ()
        {
        }

        public static async Task performBlobOperation()
        {
            // Retrieve storage account from connection string.
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Retrieve reference to a previously created container.
            CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

            // Create the container if it doesn't already exist.
            await container.CreateIfNotExistsAsync();

            // Retrieve reference to a blob named "myblob".
            CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

            // Create the "myblob" blob with the text "Hello, world!"
            await blockBlob.UploadTextAsync("Hello, world!");
        }
    }
}
```

Обязательно замените your_account_name_here и your_account_key_here действительными именем и ключом учетной записи. 

Ваши проекты для iOS, Android и Windows Phone содержат ссылки на проект Portable. Это означает, что весь общий код можно написать в одном месте и использовать его для всех проектов. Теперь, чтобы воспользоваться этим преимуществом, можно добавить следующую строку кода в каждый проект: `MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

```csharp
using Android.App;
using Android.Widget;
using Android.OS;

namespace XamarinApp.Droid
{
    [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        int count = 1;

        protected override async void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get our button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            button.Click += delegate {
                button.Text = string.Format ("{0} clicks!", count++);
            };

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

```csharp
using System;
using UIKit;

namespace XamarinApp.iOS
{
    public partial class ViewController : UIViewController
    {
        int count = 1;

        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override async void ViewDidLoad ()
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

namespace XamarinApp.WinPhone
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        int count = 1;

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.
        /// This parameter is typically used to configure the page.</param>
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>Выполнение приложения
Теперь можно запустить это приложение в эмуляторе Android или Windows Phone. Можно также запустить это приложение в эмуляторе iOS, но для этого потребуется Mac. Инструкции о том, как это сделать, см. в документации по [подключению Visual Studio к Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/).

После запуска приложение создаст контейнер `mycontainer` в вашей учетной записи хранения. Он должен содержать большой двоичный объект `myblob`, который содержит текст `Hello, world!`. Это можно проверить с помощью [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

## <a name="next-steps"></a>Дальнейшие действия
В данном учебнике по началу работы было рассмотрено создание кроссплатформенного приложения в Xamarin, которое использует службу хранилища Azure. Учебник ориентирован на один сценарий использования хранилища BLOB-объектов. Однако его можно использовать не только для работы с хранилищем BLOB-объектов, но также с хранилищами таблиц, файлов и очередей. Чтобы узнать больше, ознакомьтесь со следующими статьями:

* [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](storage-dotnet-how-to-use-blobs.md)
* [Приступая к работе с хранилищем таблиц Azure с помощью .NET](storage-dotnet-how-to-use-tables.md)
* [Приступая к работе с хранилищем очередей Azure с помощью .NET](storage-dotnet-how-to-use-queues.md)
* [Приступая к работе с хранилищем файлов Azure в Windows](storage-dotnet-how-to-use-files.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]


