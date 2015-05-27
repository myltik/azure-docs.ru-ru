
##<a name="add-select-images"></a>Обновление клиентского приложения быстрого запуска для захвата и передачи изображений

В этом разделе будет обновляться проект из учебника [Приступая к работе с мобильными службами] для съемки фотографий и передачи их в хранилище BLOB-объектов Azure. Для захвата изображения в этом учебнике используется [CameraCaptureTask] из пространства имен `Microsoft.Phone.Tasks`. Этот класс запускает пользовательский интерфейс камеры на устройстве Windows Phone для записи фотографии и автоматически сохраняет изображение в галерее камеры на устройстве Windows Phone. Если вам не нужны изображения, сохраненные в альбоме камеры, используйте вместо этого класс [PhotoCamera] в пространстве имен `Microsoft.Devices`.

1. В обозревателе решений для Visual Studio в проекте разверните пункт **Свойства**. Затем откройте файл WMAppManifest.xml и на вкладке **Возможности** включите камеру, щелкнув **ID_CAP_ISV_CAMERA**. Закройте файл, чтобы сохранить изменения.

   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-WMAppmanifest-wp8.png)

   	Это позволит приложению использовать камеру, подключенную к компьютеру. Пользователям необходимо будет разрешить доступ к камере при первом запуске приложения.

2. Откройте файл MainPage.xaml и замените элемент **Grid** с именем **ContentPanel** следующим кодом:

        <!--ContentPanel - place additional content here-->
        <Grid x:Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="*" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="2*" />
                <ColumnDefinition Width="2*" />
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.ColumnSpan="2" Text="Enter some text below, click Capture Image to add a captured image. Then click Save to insert a new TodoItem item into your database" TextWrapping="Wrap" Margin="12"/>
            <TextBox Grid.Row="1" Grid.ColumnSpan="2" Name="TextInput" Text="" />
            <Button Name="ButtonCaptureImage" Grid.Row="2" Click="ButtonCaptureImage_Click">Capture Image</Button>
            <Button Grid.Row ="2" Grid.Column="1" Name="ButtonSave" Click="ButtonSave_Click">Save</Button>
            <TextBlock Grid.Row="3" Grid.ColumnSpan="2" Text="Click refresh below to load the unfinished TodoItems from your database. Use the checkbox to complete and update your TodoItems" TextWrapping="Wrap" Margin="12" />
            <Button Grid.Row="4" Grid.ColumnSpan="2" Name="ButtonRefresh" Click="ButtonRefresh_Click">Refresh</Button>
            <phone:LongListSelector Grid.Row="5" Grid.ColumnSpan="2" Name="ListItems">
                <phone:LongListSelector.ItemTemplate>
                    <DataTemplate>
                        <StackPanel Orientation="Vertical">
                            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
                            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}" MaxHeight="150"/>
                        </StackPanel>
                    </DataTemplate>
                </phone:LongListSelector.ItemTemplate>
            </phone:LongListSelector>
        </Grid>


   	Это приводит к добавлению новой кнопки для запуска [CameraCaptureTask] и добавляет изображение в **ItemTemplate**, а также определяет источник привязки в качестве URI переданного изображения в службе хранилища BLOB-объектов.

3. Откройте файл проекта MainPage.xaml.cs и добавьте следующие операторы **using**:
	
		using Microsoft.Phone.Tasks;
		using System.IO;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
    
4. В файле проекта MainPage.xaml.cs обновите класс TodoItem, добавив следующие свойства:

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
		
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
		
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
		
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

5. В файле проекта MainPage.xaml.cs обновите класс MainPage. Добавьте следующий код для объявления [CameraCaptureTask] и объект потока, который будет ссылаться на записанное изображение:

        // Using the CameraCaptureTask to allow the user to capture a todo item image //
        CameraCaptureTask cameraCaptureTask;
		
        // Using a stream reference to upload the image to blob storage.
        Stream imageStream = null;

6. В файле проекта MainPage.xaml.cs обновите класс MainPage. Добавьте следующий код, чтобы обновить конструктор для создания CameraCaptureTask и добавления обработчика события для события Completed:

        // Constructor
        public MainPage()
        {
            InitializeComponent();
			
            cameraCaptureTask = new CameraCaptureTask();
            cameraCaptureTask.Completed += cameraCaptureTask_Completed;
        }
		
        void cameraCaptureTask_Completed(object sender, PhotoResult e)
        {
            imageStream = e.ChosenPhoto;
        }

7. В файле проекта MainPage.xaml.cs обновите класс MainPage. Добавьте следующий код, который отображает пользовательский интерфейс камеры, чтобы пользователь мог сохранять изображение при нажатии кнопки **Запись изображения**:

        private void ButtonCaptureImage_Click(object sender, RoutedEventArgs e)
        {
            cameraCaptureTask.Show();
        }


8. В файле проекта MainPage.xaml.cs обновите класс MainPage. Замените существующий метод `InsertTodoItem` следующим кодом:
 
        private async void InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;            
			
            if (imageStream != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";
                todoItem.ResourceName = Guid.NewGuid().ToString() + ".jpg";
            }                       
			
            // Send the item to be inserted. When blob properties are set this
            // generates an SAS in the response.
            await todoTable.InsertAsync(todoItem);  
			
            // If we have a returned SAS, then upload the blob.
            if (!string.IsNullOrEmpty(todoItem.SasQueryString))
            {
                // Get the URI generated that contains the SAS 
                // and extract the storage credentials.
                StorageCredentials cred = new StorageCredentials(todoItem.SasQueryString);
                var imageUri = new Uri(todoItem.ImageUri);
				
                // Instantiate a Blob store container based on the info in the returned item.
                CloudBlobContainer container = new CloudBlobContainer(
                    new Uri(string.Format("https://{0}/{1}",
                        imageUri.Host, todoItem.ContainerName)), cred);                
				
                // Upload the new image as a BLOB from the stream.
                CloudBlockBlob blobFromSASCredential =
                    container.GetBlockBlobReference(todoItem.ResourceName);
                await blobFromSASCredential.UploadFromStreamAsync(imageStream);
				
				// When you request an SAS at the container-level instead of the blob-level,
				// you are able to upload multiple streams using the same container credentials.

                imageStream = null;
            }              
			
            // Add the new item to the collection.
            items.Add(todoItem);
            TextInput.Text = "";
        }


	Этот код отправляет в мобильную службу запрос на вставку нового объекта TodoItem, включая имя файла изображения. Ответ содержит учетные данные SAS, которые затем используются для вставки изображения в хранилище BLOB-объектов, а также URI изображения для привязки данных.

Последним шагом является проверка приложение и подтверждение успешного выполнения загрузки.
		
##<a name="test"></a>Тестирование передачи изображений с помощью вашего приложения

1. В Visual Studio нажмите клавишу F5, чтобы протестировать приложение в эмуляторе или на фактическом целевом устройстве.

2. Введите текст в текстовое поле, затем щелкните **Записать изображение**.

   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-wp8.png)

  	Появится пользовательский интерфейс камеры.

3. Щелкните изображение или нажмите кнопку снимка на телефоне, чтобы сделать снимок.
  
   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-wp8.png)

4. Щелкните **принять**, чтобы принять изображение и выйти из пользовательского интерфейса камеры.

    ![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-accept-wp8.png)

5. Щелкните **Сохранить**, чтобы вставить новый элемент и передать изображение.

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-save-wp8.png)

6. Новый элемент отображается в представлении списка вместе с переданным изображением.

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-final-wp8.png)

   >[AZURE.NOTE]Изображение автоматически скачивается из службы хранилища больших двоичных объектов, когда свойство <code>imageUri</code> нового элемента привязано к элементу управления <strong>Изображение</strong>.


[Приступая к работе с мобильными службами]: ../articles/mobile-services-windows-phone-get-started.md
[CameraCaptureTask]: http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.tasks.cameracapturetask(v=vs.105).aspx
[PhotoCamera]: http://msdn.microsoft.com/library/windowsphone/develop/microsoft.devices.photocamera(v=vs.105).aspx

<!--HONumber=54-->