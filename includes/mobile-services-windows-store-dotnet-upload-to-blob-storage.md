##<a name="add-select-images"></a>Обновление клиентского приложения быстрого запуска для захвата и передачи изображений

1. В Visual Studio 2012 откройте файл Package.appxmanifest и на вкладке **Возможности** включите функции **Веб-камера** и **Микрофон**.

   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-app-manifest-camera.png)
 
   	Это позволит приложению использовать камеру, подключенную к компьютеру. Пользователям необходимо будет разрешить доступ к камере при первом запуске приложения.

1. Откройте файл MainPage.xaml и замените элемент **StackPanel** непосредственно после первого элемента **Task** следующим кодом:

        <StackPanel Orientation="Horizontal" Margin="72,0,0,0">
            <TextBox Name="TextInput" Margin="5" MaxHeight="40" MinWidth="300"></TextBox>
            <Button Name="btnTakePhoto" Style="{StaticResource PhotoAppBarButtonStyle}"
                    Click="OnTakePhotoClick" />
            <Button Name="ButtonSave" Style="{StaticResource UploadAppBarButtonStyle}" 
                    Click="ButtonSave_Click"/>
        </StackPanel>

2. Замените элемент **StackPanel** в **DataTemplate** следующим кодом:

        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                        Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                        Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}"
                    MaxHeight="250"/>
        </StackPanel> 

   	Это приводит к добавлению изображения в **ItemTemplate** и определению источника привязки в качестве URI переданного изображения в службе хранилища BLOB-объектов.

3. Откройте файл проекта MainPage.xaml.cs и добавьте следующие операторы **using**:
	
		using Windows.Media.Capture;
		using Windows.Storage;
		using Windows.UI.Popups;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
    
4. В классе TodoItem добавьте следующие свойства:

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
		
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
		
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
		
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

   	>[AZURE.NOTE]Для добавления новых свойств к объекту TodoItem необходимо включить динамическую схему в мобильной службе. После включения динамической схемы в таблицу TodoItem автоматически добавляются новые столбцы, которые соответствуют этим новым свойствам.

5. Добавьте в класс MainPage приведенный ниже код:

        // Use a StorageFile to hold the captured image for upload.
        StorageFile media = null;
		
		private async void OnTakePhotoClick(object sender, RoutedEventArgs e)
		{
			// Capture a new photo or video from the device.
			CameraCaptureUI cameraCapture = new CameraCaptureUI();
			media = await cameraCapture
				.CaptureFileAsync(CameraCaptureUIMode.PhotoOrVideo);
        }

  	Этот код отображает пользовательский интерфейс для захвата изображения и сохранения его в файле хранения.

6. Замените существующий метод  `InsertTodoItem` следующим кодом:
 
        private async void InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;
			
            if (media != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";
                todoItem.ResourceName = media.Name;
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

                // Get the new image as a stream.
                using (var fileStream = await media.OpenStreamForReadAsync())
                {                   					
                    // Upload the new image as a BLOB from the stream.
                    CloudBlockBlob blobFromSASCredential =
                        container.GetBlockBlobReference(todoItem.ResourceName);
                    await blobFromSASCredential.UploadFromStreamAsync(fileStream.AsInputStream());
                }
				
				// When you request an SAS at the container-level instead of the blob-level,
				// you are able to upload multiple streams using the same container credentials.
            }
			
            // Add the new item to the collection.
            items.Add(todoItem);
        }

	Этот код отправляет в мобильную службу запрос на вставку нового объекта TodoItem, включая имя файла изображения. Ответ содержит учетные данные SAS, которые затем используются для вставки изображения в хранилище BLOB-объектов, а также URI изображения для привязки данных.

Последним шагом является проверка приложение и подтверждение успешного выполнения загрузки.
		
##<a name="test"></a>Тестирование передачи изображений в приложении

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

2. Введите текст в поле раздела **Вставить TodoItem**, затем щелкните **Фото**.

   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar.png)

  	Появится пользовательский интерфейс камеры. 

3. Щелкните изображение, чтобы сделать снимок, затем нажмите кнопку **ОК**
  
   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-camera.png)

4. Щелкните **Загрузить**, чтобы вставить новые элементы и передать изображение.

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar2.png)

5. Новый элемент отображается в представлении списка вместе с переданным изображением.

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-ie.png)

   	>[AZURE.NOTE]Изображение автоматически скачивается из службы хранилища больших двоичных объектов, когда свойство <code>imageUri</code> нового элемента привязано к элементу управления <strong>Изображение</strong>.


<!--HONumber=42-->
