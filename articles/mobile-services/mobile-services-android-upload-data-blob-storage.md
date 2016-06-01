<properties
	pageTitle="Использование мобильных служб для передачи изображений в хранилище BLOB-объектов (Android) | Мобильные службы"
	description="Узнайте, как использовать мобильные службы для передачи изображений в службу хранилища Azure и получения доступа к изображениям из приложения Android."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="04/11/2016"
	ms.author="ricksal"/>

# Передача изображений из устройства Android в службу хранилища Azure

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

&nbsp;&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

В этой статье описано, как с помощью приложения Android Azure Mobile Services можно передать изображения в службу хранилища Azure.

Для хранения данных мобильные службы используют базу данных SQL. Тем не менее эффективнее хранить данные больших двоичных объектов (BLOB-объектов) в службе хранилища Azure. Прочитав это руководство, вы научитесь делать снимки камерой Android с помощью приложения быстрого запуска мобильных служб и передавать изображения в службу хранилища Azure.


## Что необходимо для начала работы

Прежде чем приступить к этому руководству, необходимо прочитать статью о начале работы с мобильными службами: [Приступая к работе с мобильными службами].

Для работы с этим руководством требуется следующее:

+ [учетная запись хранения Azure](../storage/storage-create-storage-account.md);
+ устройство Android с камерой.

## Как работает приложение

Передача фотографий — это многоэтапный процесс.

- Сначала нужно создать снимок и вставить строку TodoItem в базу данных SQL, содержащую новые поля метаданных, которые используются службой хранилища Azure.
- Новый сценарий **вставки** SQL мобильных служб запрашивает у службы хранилища Azure подписанный URL-адрес (SAS).
- Этот скрипт возвращает клиенту SAS и URI для BLOB-объекта.
- Клиент передает фотографии, используя SAS и URI BLOB-объекта.

Итак, что такое SAS?

Хранить учетные данные, необходимые для передачи данных службе хранилища Azure, в клиентском приложении небезопасно. Вместо этого необходимо хранить эти учетные данные в мобильной службе, используя их для создания подписанного URL-адреса (SAS), который предоставляет разрешение передавать новое изображение. SAS — это тип учетных данных с 5-минутным сроком действия. Он безопасно возвращается в клиентское приложение мобильными службами. Затем приложение использует эти временные учетные данные для передачи изображения. Дополнительные сведения см. в статье [Подписанный URL-адрес. Часть 1: общие сведения о модели SAS](../storage/storage-dotnet-shared-access-signature-part-1.md).

## Пример кода
[Вот](https://github.com/Azure/mobile-services-samples/tree/master/UploadImages) завершенная часть приложения с исходным кодом клиента. Чтобы выполнить ее, необходимо завершить разделы этого учебника, посвященные серверной части мобильных служб.

## Обновление зарегистрированных сценариев вставки на классическом портале Azure

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]


## Обновление приложения быстрого запуска для создания и передачи изображений

### Справочник по клиентской библиотеке Android службы хранилища Azure

1. Чтобы добавить ссылку на библиотеку, в файле **app** > **build.gradle** добавьте следующую строку в раздел `dependencies`:

		compile 'com.microsoft.azure.android:azure-storage-android:0.6.0@aar'


2. Замените значение `minSdkVersion` на 15 (требуется API камеры).

3. Щелкните значок **Синхронизировать проект с файлами Gradle**.

### Обновление файла манифеста для камеры и хранилища

1. Укажите, что для приложения требуется камера, добавив следующую строку в файл **AndroidManifest.xml**:

	    <uses-feature android:name="android.hardware.camera"
	        android:required="true" />

2. Укажите, что приложению необходимо предоставить разрешение на запись во внешнее хранилище, добавив следующую строку в файл **AndroidManifest.xml**:

	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

	Обратите внимание, что внешним хранилищем Android не обязательно должна быть SD-карта. Дополнительные сведения см. в статье [Сохранение файлов](http://developer.android.com/training/basics/data-storage/files.html).

### Обновление файлов ресурсов для нового пользовательского интерфейса

1. Добавьте заголовки для новых кнопок, добавив следующую команду в файл **strings.xml** в каталоге *values*:

	    <string name="preview_button_text">Take Photo</string>
	    <string name="upload_button_text">Upload</string>

2. В файл **activity\_to\_do.xml**, находящийся в папке **res => layout**, добавьте следующий код перед кодом кнопки **Добавить**.

         <Button
             android:id="@+id/buttonPreview"
             android:layout_width="120dip"
             android:layout_height="wrap_content"
             android:onClick="takePicture"
             android:text="@string/preview_button_text" />

3. Замените элемент кнопки **Добавить** следующим кодом:

         <Button
             android:id="@+id/buttonUpload"
             android:layout_width="100dip"
             android:layout_height="wrap_content"
             android:onClick="uploadPhoto"
             android:text="@string/upload_button_text" />


### Добавление кода для фотосъемки

1. В файл **ToDoActivity.java** добавьте следующий код, чтобы создать объект **File** с уникальным именем.

		// Create a File object for storing the photo
	    private File createImageFile() throws IOException {
	        // Create an image file name
	        String timeStamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
	        String imageFileName = "JPEG_" + timeStamp + "_";
	        File storageDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);
	        File image = File.createTempFile(
	                imageFileName,  /* prefix */
	                ".jpg",         /* suffix */
	                storageDir      /* directory */
	        );
	        return image;
	    }

5. Добавьте следующий код, чтобы запустить приложение камеры Android. Теперь вы можете снимать. Если фотография вам нравится, нажмите кнопку **Сохранить**, после чего она будет сохранена в созданном вами файле.

		// Run an Intent to start up the Android camera
	    static final int REQUEST_TAKE_PHOTO = 1;
	    public Uri mPhotoFileUri = null;
	    public File mPhotoFile = null;

	    public void takePicture(View view) {
	        Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
	        // Ensure that there's a camera activity to handle the intent
	        if (takePictureIntent.resolveActivity(getPackageManager()) != null) {
	            // Create the File where the photo should go
	            try {
	                mPhotoFile = createImageFile();
	            } catch (IOException ex) {
	                // Error occurred while creating the File
	                //
	            }
	            // Continue only if the File was successfully created
	            if (mPhotoFile != null) {
	                mPhotoFileUri = Uri.fromFile(mPhotoFile);
	                takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT, mPhotoFileUri);
	                startActivityForResult(takePictureIntent, REQUEST_TAKE_PHOTO);
	            }
	        }
	    }

### Добавление кода для передачи файла фотографии в хранилище BLOB-объектов


1. Сначала мы добавим несколько новых полей метаданных в объект `ToDoItem`, добавив следующий код в файл **ToDoItem.java**.

		/**
	     *  imageUri - points to location in storage where photo will go
	     */
	    @com.google.gson.annotations.SerializedName("imageUri")
	    private String mImageUri;

	    /**
	     * Returns the item ImageUri
	     */
	    public String getImageUri() {
	        return mImageUri;
	    }

	    /**
	     * Sets the item ImageUri
	     *
	     * @param ImageUri
	     *            Uri to set
	     */
	    public final void setImageUri(String ImageUri) {
	        mImageUri = ImageUri;
	    }

	    /**
	     * ContainerName - like a directory, holds blobs
	     */
	    @com.google.gson.annotations.SerializedName("containerName")
	    private String mContainerName;

	    /**
	     * Returns the item ContainerName
	     */
	    public String getContainerName() {
	        return mContainerName;
	    }

	    /**
	     * Sets the item ContainerName
	     *
	     * @param ContainerName
	     *            Uri to set
	     */
	    public final void setContainerName(String ContainerName) {
	        mContainerName = ContainerName;
	    }

	    /**
	     *  ResourceName
	     */
	    @com.google.gson.annotations.SerializedName("resourceName")
	    private String mResourceName;

	    /**
	     * Returns the item ResourceName
	     */
	    public String getResourceName() {
	        return mResourceName;
	    }

	    /**
	     * Sets the item ResourceName
	     *
	     * @param ResourceName
	     *            Uri to set
	     */
	    public final void setResourceName(String ResourceName) {
	        mResourceName = ResourceName;
	    }

	    /**
	     *  SasQueryString - permission to write to storage
	     */
	    @com.google.gson.annotations.SerializedName("sasQueryString")
	    private String mSasQueryString;

	    /**
	     * Returns the item SasQueryString
	     */
	    public String getSasQueryString() {
	        return mSasQueryString;
	    }

	    /**
	     * Sets the item SasQueryString
	     *
	     * @param SasQueryString
	     *            Uri to set
	     */
	    public final void setSasQueryString(String SasQueryString) {
	        mSasQueryString = SasQueryString;
	    }

2. Замените конструктор с параметром 0 следующим кодом:

	    /**
	     * ToDoItem constructor
	     */
	    public ToDoItem() {
	        mContainerName = "";
	        mResourceName = "";
	        mImageUri = "";
	        mSasQueryString = "";
	    }

3. Замените конструктор с несколькими параметрами следующим кодом:

	    /**
	     * Initializes a new ToDoItem
	     *
	     * @param text
	     *            The item text
	     * @param id
	     *            The item id
	     */
	    public ToDoItem(String text,
	                    String id,
	                    String containerName,
	                    String resourceName,
	                    String imageUri,
	                    String sasQueryString) {
	        this.setText(text);
	        this.setId(id);
	        this.setContainerName(containerName);
	        this.setResourceName(resourceName);
	        this.setImageUri(imageUri);
	        this.setSasQueryString(sasQueryString);
	    }


4. В файле **ToDoActivity.java** замените метод **addItem** следующим кодом, который передает изображение.

	    /**
	     * Add a new item
	     *
	     * @param view
	     *            The view that originated the call
	     */
	    public void uploadPhoto(View view) {
	        if (mClient == null) {
	            return;
	        }

	        // Create a new item
	        final ToDoItem item = new ToDoItem();

	        item.setText(mTextNewToDo.getText().toString());
	        item.setComplete(false);
	        item.setContainerName("todoitemimages");

	        // Use a unigue GUID to avoid collisions.
	        UUID uuid = UUID.randomUUID();
	        String uuidInString = uuid.toString();
	        item.setResourceName(uuidInString);

	        // Send the item to be inserted. When blob properties are set this
	        // generates an SAS in the response.
	        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
	            @Override
	            protected Void doInBackground(Void... params) {
	                try {
		                    final ToDoItem entity = addItemInTable(item);

		                    // If we have a returned SAS, then upload the blob.
		                    if (entity.getSasQueryString() != null) {

	                       // Get the URI generated that contains the SAS
	                        // and extract the storage credentials.
	                        StorageCredentials cred =
								new StorageCredentialsSharedAccessSignature(entity.getSasQueryString());
	                        URI imageUri = new URI(entity.getImageUri());

	                        // Upload the new image as a BLOB from a stream.
	                        CloudBlockBlob blobFromSASCredential =
	                                new CloudBlockBlob(imageUri, cred);

	                        blobFromSASCredential.uploadFromFile(mPhotoFileUri.getPath());
  	                    }

	                    runOnUiThread(new Runnable() {
	                        @Override
	                        public void run() {
	                            if(!entity.isComplete()){
	                                mAdapter.add(entity);
	                            }
	                        }
	                    });
	                } catch (final Exception e) {
	                    createAndShowDialogFromTask(e, "Error");
	                }
	                return null;
	            }
	        };

	        runAsyncTask(task);

	        mTextNewToDo.setText("");
	    }


Этот код отправляет запрос в мобильную службу на вставку нового объекта TodoItem. В ответе содержится SAS, который затем используется для передачи изображения из локального хранилища в BLOB-объект службы хранилища Azure.


## Тестирование передачи изображений

1. В Android Studio щелкните **Run** (Запустить). В диалоговом окне выберите устройство для использования.

2. После отображения пользовательского интерфейса приложения введите текст в текстовое поле с надписью **Add a ToDo item** (Добавить элемент ToDo).

3. Щелкните **Take Photo** (Сфотографировать). После запуска приложения камеры сделайте снимок. Щелкните флажок, чтобы принять фотографию.

4. Щелкните **Upload** (Передать). Обратите внимание, что элемент ToDoItem будет добавлен в список, как обычно.

5. На портале Microsoft Azure перейдите к своей учетной записи хранения, выберите вкладку **Контейнеры** и щелкните имя контейнера в списке.

6. Отобразится список переданных файлов BLOB-объектов. Выберите один из них и нажмите кнопку **Загрузить**.

7. Переданное вами изображение появится в окне браузера.


## <a name="next-steps"> </a>Дальнейшие действия

Теперь, когда вы смогли безопасно передать изображения, интегрировав мобильную службу и службу BLOB-объектов, ознакомьтесь с некоторыми темами, посвященными серверным службам и интеграции:

+ [Отправка сообщений электронной почты из мобильных служб с помощью SendGrid]

  Узнайте, как добавить функциональные возможности электронной почты в мобильную службу, используя службу электронной почты SendGrid. В этом разделе показано, как добавить сценарии на стороне сервера для отправки сообщений электронной почты с помощью SendGrid.

+ [Планирование серверных заданий в мобильных службах]

  Узнайте, как использовать планировщик заданий мобильных служб для определения кода сценария на стороне сервера, который будет выполняться в соответствии с заданным расписанием.

+ [Справочник серверных скриптов мобильных служб]

  Справочные разделы по использованию серверных скриптов для выполнения задач на стороне сервера и интеграции с другими компонентами Azure, а также внешние ресурсы.

+ [Справочник принципов использования мобильных служб .NET]

  Дополнительные сведения об использовании мобильных служб с .NET


<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the insert script to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[Отправка сообщений электронной почты из мобильных служб с помощью SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Планирование серверных заданий в мобильных службах]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Справочник серверных скриптов мобильных служб]: mobile-services-how-to-use-server-scripts.md
[Приступая к работе с мобильными службами]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md

[Azure classic portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Справочник принципов использования мобильных служб .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=AcomDC_0518_2016-->