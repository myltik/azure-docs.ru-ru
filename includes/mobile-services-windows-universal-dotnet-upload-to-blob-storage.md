## Установка клиента хранения для приложений Магазина Windows
Чтобы иметь возможность использовать учетные данные SAS для передачи изображений в хранилище BLOB-объектов, необходимо добавить пакет NuGet, который устанавливает библиотеку клиента хранилища для приложений Магазина Windows.

1. В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши имя проекта, затем выберите **Управлять пакетами NuGet**.
2. В левой области выберите категорию **В сети**, найдите пакет `WindowsAzure.Storage`, щелкните **Установить** для пакета **Хранилище Azure**, а затем примите условия лицензионных соглашений.
   
      ![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-add-storage-nuget-package-dotnet.png)
   
      Это приведет к добавлению в проект библиотеки клиента для служб хранилища Azure.

Далее необходимо будет обновить приложение quickstart для захвата и сохранения изображений.

## Обновление клиентского приложения быстрого запуска для захвата и передачи изображений
1. В Visual Studio откройте файл Package.appxmanifest и на вкладке **Возможности** включите функции **Веб-камера** и **Микрофон**.
   
       ![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-app-manifest-camera.png)
   
       Это позволит приложению использовать камеру, подключенную к компьютеру. Пользователям необходимо будет разрешить доступ к камере при первом запуске приложения.
2. Повторите описанное выше действие для проекта приложения Windows Phone.
3. В проекте приложения Windows откройте файл MainPage.xaml и замените элемент **StackPanel** непосредственно после первого элемента **QuickStartTask** указанным ниже кодом.
   
        <StackPanel Orientation="Horizontal" Margin="72,0,0,0">
            <TextBox Name="TextInput" Margin="5" MaxHeight="40" MinWidth="300"></TextBox>
            <AppBarButton Label="Photo" Icon="Camera" Name="ButtonCapture" Click="ButtonCapture_Click" />
            <AppBarButton Label="Upload" Icon="Upload" Name="ButtonSave" Click="ButtonSave_Click"/>
        </StackPanel>
        <Grid Name="captureGrid" Margin="62,0,0,0" Visibility="Collapsed" >
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <CaptureElement x:Name="previewElement" Grid.Row="0" Tapped="previewElement_Tapped" />
            <Image Name="imagePreview" Grid.Row="0"  />
            <StackPanel Name="captureButtons" Orientation="Horizontal" Grid.Row="1">
                <TextBlock Name="TextCapture" />
                <AppBarButton Label="Retake" Icon="Redo" Name="ButtonRetake" Click="ButtonCapture_Click" />
                <AppBarButton Label="Cancel" Icon="Cancel" Name="ButtonCancel" Click="ButtonCancel_Click" />
            </StackPanel>
        </Grid>
4. Замените элемент **StackPanel** в **DataTemplate** на следующий код:
   
        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                        Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                        Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}"
                    MaxHeight="250"/>
        </StackPanel> 
   
       Это приводит к добавлению изображения в **ItemTemplate** и определению источника привязки в качестве URI переданного изображения в службе хранилища BLOB-объектов.
5. В проекте приложения Windows Phone откройте файл MainPage.xaml и замените элемент **ButtonSave** указанным ниже кодом.
   
        <StackPanel Grid.Row ="1" Grid.Column="1"  Orientation="Horizontal">
            <AppBarButton Label="Photo" Icon="Camera" Name="ButtonCapture" 
                          Click="ButtonCapture_Click" Height="78" Width="62" />
            <AppBarButton Label="Upload" Icon="Upload" Name="ButtonSave" 
                          Click="ButtonSave_Click"/>
        </StackPanel>
        <Grid Grid.Row="2" Name="captureGrid" Grid.RowSpan="3" Grid.ColumnSpan="2" 
              Canvas.ZIndex="99" Background="{ThemeResource ApplicationPageBackgroundThemeBrush}" 
              Visibility="Collapsed">
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <CaptureElement Grid.Row="0" x:Name="previewElement" Tapped="previewElement_Tapped" />                    
            <Image Grid.Row="0" Name="imagePreview" Visibility="Collapsed" />
            <StackPanel Grid.Row="1" Name="captureButtons" 
                        Orientation="Horizontal" Visibility="Collapsed">
                <TextBlock Name="TextCapture" VerticalAlignment="Bottom" />
                <AppBarButton Label="Retake" Icon="Redo" Name="ButtonRetake" 
                              Click="ButtonRetake_Click" />
                <AppBarButton Label="Cancel" Icon="Cancel" Name="ButtonCancel" 
                              Click="ButtonCancel_Click" />
            </StackPanel>
        </Grid>
6. Замените элемент **StackPanel** в **DataTemplate** на следующий код:
   
        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                      Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                      Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}" 
                   MaxHeight="150"/>
        </StackPanel>
7. В общей папке DataModel откройте файл проекта TodoItem.cs и добавьте в класс TodoItem указанные ниже свойства.
   
        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
   
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
   
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
   
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 
8. Откройте общий файл проекта MainPage.cs и добавьте указанные ниже операторы **using**.
   
        using Windows.Media.Capture;
        using Windows.Media.MediaProperties;
        using Windows.Storage;
        using Windows.UI.Xaml.Input;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Windows.UI.Xaml.Media.Imaging;
9. Добавьте в класс MainPage указанный ниже код.
   
        // Use a StorageFile to hold the captured image for upload.
        StorageFile media = null;
        MediaCapture cameraCapture;
        bool IsCaptureInProgress;
   
        private async Task CaptureImage()
        {
            // Capture a new photo or video from the device.
            cameraCapture = new MediaCapture();
            cameraCapture.Failed += cameraCapture_Failed;
   
            // Initialize the camera for capture.
            await cameraCapture.InitializeAsync();
   
            #if WINDOWS_PHONE_APP
            cameraCapture.SetPreviewRotation(VideoRotation.Clockwise90Degrees);
            cameraCapture.SetRecordRotation(VideoRotation.Clockwise90Degrees);
            #endif
   
            captureGrid.Visibility = Windows.UI.Xaml.Visibility.Visible;
            previewElement.Visibility = Windows.UI.Xaml.Visibility.Visible;
            previewElement.Source = cameraCapture;
            await cameraCapture.StartPreviewAsync();
        }
   
        private async void previewElement_Tapped(object sender, TappedRoutedEventArgs e)
        {
            // Block multiple taps.
            if (!IsCaptureInProgress)
            {                
                IsCaptureInProgress = true;
   
                // Create the temporary storage file.
                media = await ApplicationData.Current.LocalFolder
                    .CreateFileAsync("capture_file.jpg", CreationCollisionOption.ReplaceExisting);
   
                // Take the picture and store it locally as a JPEG.
                await cameraCapture.CapturePhotoToStorageFileAsync(
                    ImageEncodingProperties.CreateJpeg(), media);
   
                captureButtons.Visibility = Visibility.Visible;
   
                // Use the stored image as the preview source.
                BitmapImage tempBitmap = new BitmapImage(new Uri(media.Path));
                imagePreview.Source = tempBitmap;
                imagePreview.Visibility = Visibility.Visible;
                previewElement.Visibility = Visibility.Collapsed;
                IsCaptureInProgress = false;
            }
        }
   
        private async void cameraCapture_Failed(MediaCapture sender, MediaCaptureFailedEventArgs errorEventArgs)
        {
            // It's safest to return this back onto the UI thread to show the message dialog.
            MessageDialog dialog = new MessageDialog(errorEventArgs.Message);
            await this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, 
                async () => { await dialog.ShowAsync(); });            
        }
   
        private async void ButtonCapture_Click(object sender, RoutedEventArgs e)
        {
            // Prevent multiple initializations.
            ButtonCapture.IsEnabled = false;
   
            await CaptureImage();
        }
   
        private async void ButtonRetake_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            // Reset the capture and then start again.
            await ResetCaptureAsync();
            await CaptureImage();
        }
   
        private async void ButtonCancel_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            await ResetCaptureAsync();
        }
   
        private async Task ResetCaptureAsync()
        {
            captureGrid.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            imagePreview.Visibility = Visibility.Collapsed;
            captureButtons.Visibility = Visibility.Collapsed;
            previewElement.Source = null;
            ButtonCapture.IsEnabled = true;
   
            // Make sure we stop the preview and release resources.
            await cameraCapture.StopPreviewAsync();
            cameraCapture.Dispose();
            media = null;
        }
   
      Этот код отображает пользовательский интерфейс, который используется для захвата изображения и сохранения его в файле хранилища.
10. Замените существующий метод `InsertTodoItem` следующим кодом:
    
         private async Task InsertTodoItem(TodoItem todoItem)
         {
             string errorString = string.Empty;
    
             if (media != null)
             {
                 // Set blob properties of TodoItem.
                 todoItem.ContainerName = "todoitemimages";
    
                 // Use a unigue GUID to avoid collisions.
                 todoItem.ResourceName = Guid.NewGuid().ToString();
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
                 using (var inputStream = await media.OpenReadAsync())
                 {
                     // Upload the new image as a BLOB from the stream.
                     CloudBlockBlob blobFromSASCredential =
                         container.GetBlockBlobReference(todoItem.ResourceName);
                     await blobFromSASCredential.UploadFromStreamAsync(inputStream);
                 }
    
                 // When you request an SAS at the container-level instead of the blob-level,
                 // you are able to upload multiple streams using the same container credentials.
    
                 await ResetCaptureAsync();
             }
    
             // Add the new item to the collection.
             items.Add(todoItem);
         }
    
     Этот код отправляет запрос в мобильную службу на вставку нового объекта TodoItem. В отклике содержится SAS, который затем используется для отправки изображения из локального хранилища в хранилище больших двоичных объектов Azure. URL-адрес отправленного изображения используется в процессе привязки данных.

Последнее действие состоит в тестировании обеих версий приложения и проверке того, успешно ли выполняется отправка с обоих устройств.

## <a name="test"></a>Тестирование передачи изображений с помощью вашего приложения
1. Убедитесь, что в Visual Studio проект Windows задан в качестве проекта по умолчанию, а затем нажмите клавишу F5 для запуска приложения.
2. Введите текст в поле раздела **Вставить TodoItem**, затем щелкните **Фото**.
3. Щелкните окно предварительного просмотра, чтобы сделать снимок. Затем нажмите кнопку **Отправить**, чтобы вставить новый элемент и отправить изображение.
   
    ![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar2.png)
4. Новый элемент отображается в представлении списка вместе с переданным изображением.
   
    ![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-quickstart-blob-ie.png)
   
       >[AZURE.NOTE]Изображение будет автоматически скачано из хранилища больших двоичных объектов после привязки свойства *imageUri* нового элемента к элементу управления **Image**.
5. Остановите приложение и перезапустите версию приложения для проекта Windows Phone.
   
    Отобразится ранее отправленное изображение.
6. Как и до этого, введите какой-нибудь текст в текстовое поле, а затем нажмите кнопку **Фотография**.
   
       ![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-upload-blob-app-view-wp8.png)
7. Коснитесь окна предварительного просмотра, чтобы сделать снимок. Затем нажмите кнопку **Отправить**, чтобы вставить новый элемент и отправить изображение.
   
    ![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-upload-blob-app-view-final-wp8.png)

Вы завершили работу с учебником, посвященным отправке изображений.

<!---HONumber=Oct15_HO3-->