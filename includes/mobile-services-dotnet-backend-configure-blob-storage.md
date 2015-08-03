
##Установка клиента хранилища в проекте мобильной службы

Чтобы иметь возможность создавать учетные данные SAS для отправки изображений в хранилище BLOB-объектов, необходимо добавить пакет NuGet, который устанавливает библиотеку клиента хранилища в проекте мобильной службы.

1. В **обозревателе решений** Visual Studio щелкните правой кнопкой проект мобильной службы, а затем выберите пункт **Управление пакетами NuGet**.

2. В левой области щелкните категорию **В сети**, выберите **Только стабильные**, выполните поиск **WindowsAzure.Storage**, нажмите кнопку **Установить** для пакета **Хранилище Azure**, затем примите условия лицензионных соглашений.

  	![](./media/mobile-services-configure-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

  	Это добавит библиотеку клиента для служб хранилища Azure в проект мобильной службы.

##Обновление определения TodoItem в модели данных

Класс TodoItem определяет объект данных, и вам нужно добавить некоторые свойства в этот класс, как и в клиент.

1. В Visual Studio 2013 откройте проект мобильной службы, разверните папку DataObjects, а затем откройте файл проекта TodoItem.cs.
	
2. Добавьте следующие новые свойства в класс **TodoItem**:

        public string containerName { get; set; }
		public string resourceName { get; set; }
		public string sasQueryString { get; set; }
		public string imageUri { get; set; } 

	Эти свойства используются для создания SAS и хранения сведений об образе. Обратите внимание, что регистр в этих свойствах совпадает с серверной версией JavaScript.

	>[AZURE.NOTE]Когда используется инициализатор базы данных по умолчанию, Entity Framework будет удалять и заново создавать базу данных всякий, когда будет обнаруживать изменение модели данных в определении модели Code First. Чтобы сделать это изменение модели данных и сохранить существующие данные в базе данных, необходимо использовать Code First Migrations. Нельзя использовать инициализатор по умолчанию для базы данных SQL в Azure. Дополнительные сведения см. в разделе [Как использовать Code First Migrations для обновления модели данных](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

##Обновление контроллера TodoItem для создания подписанного URL-адреса 

Существующий параметр **TodoItemController** обновляется таким образом, что метод **PostTodoItem** создает SAS, когда вставляется новый элемент TodoItem. Вы также

0. Если вы еще не создали учетную запись хранения, см. раздел [Создание учетной записи хранения]

1. В портале управления нажмите **Хранилище**, выберите учетную запись хранения, нажмите **Управление ключами**.

2. Запишите **имя учетной записи хранения** и **ключ доступа к хранилищу**.
 
3. В своей мобильной службе нажмите вкладку **Настройка**, прокрутите вниз до **Параметры приложения** и введите пару **Имя** и **Значение** для каждого из перечисленных, полученные из учетной записи хранения, а затем нажмите **Сохранить**.

	+ `STORAGE_ACCOUNT_NAME`
	+ `STORAGE_ACCOUNT_ACCESS_KEY`

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

	Ключ доступа к учетной записи хранения хранится в зашифрованном виде в параметрах приложения. Этот ключ можно получить из любого серверного сценария во время выполнения. Дополнительные сведения см. в разделе [Параметры приложения].

4. В обозревателе решений в Visual Studio откройте файл Web.config для проекта мобильной службы и добавьте следующие параметры нового приложения, заменив заполнители именем учетной записи хранения и ключом доступа, заданными на портале:

		<add key="STORAGE_ACCOUNT_NAME" value="**your_account_name**" />
		<add key="STORAGE_ACCOUNT_ACCESS_KEY" value="**your_access_token_secret**" />

	Мобильной службой эти сохраненные параметры используются при запуске локального компьютера, который позволяет проверить код перед его публикацией. При работе в Azure мобильная служба вместо этого использует значения параметров приложения, заданные на портале, и игнорирует эти параметры проекта.

7.  В папке "Контроллеры" откройте файл TodoItemController.cs и добавьте следующие директивы **using**:

		using System;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
  
8.  Замените существующий метод **PostTodoItem** на следующий код:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            string storageAccountName;
            string storageAccountKey;

            // Try to get the Azure storage account token from app settings.  
            if (!(Services.Settings.TryGetValue("STORAGE_ACCOUNT_NAME", out storageAccountName) |
            Services.Settings.TryGetValue("STORAGE_ACCOUNT_ACCESS_KEY", out storageAccountKey)))
            {
                Services.Log.Error("Could not retrieve storage account settings.");
            }

            // Set the URI for the Blob Storage service.
            Uri blobEndpoint = new Uri(string.Format("https://{0}.blob.core.windows.net", storageAccountName));

            // Create the BLOB service client.
            CloudBlobClient blobClient = new CloudBlobClient(blobEndpoint, 
                new StorageCredentials(storageAccountName, storageAccountKey));

            if (item.containerName != null)
            {
                // Set the BLOB store container name on the item, which must be lowercase.
                item.containerName = item.containerName.ToLower();

                // Create a container, if it doesn't already exist.
                CloudBlobContainer container = blobClient.GetContainerReference(item.containerName);
                await container.CreateIfNotExistsAsync();

                // Create a shared access permission policy. 
                BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

                // Enable anonymous read access to BLOBs.
                containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
                container.SetPermissions(containerPermissions);

                // Define a policy that gives write access to the container for 5 minutes.                                   
                SharedAccessBlobPolicy sasPolicy = new SharedAccessBlobPolicy()
                {
                    SharedAccessStartTime = DateTime.UtcNow,
                    SharedAccessExpiryTime = DateTime.UtcNow.AddMinutes(5),
                    Permissions = SharedAccessBlobPermissions.Write
                };

                // Get the SAS as a string.
                item.sasQueryString = container.GetSharedAccessSignature(sasPolicy); 

                // Set the URL used to store the image.
                item.imageUri = string.Format("{0}{1}/{2}", blobEndpoint.ToString(), 
                    item.containerName, item.resourceName);
            }

            // Complete the insert operation.
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

   	Этот метод POST создает новый SAS для вставленного элемента, действительный в течение 5 минут, и присваивает значение созданного SAS свойству `sasQueryString` возвращенного элемента. Свойство `imageUri` также задается для пути ресурса нового BLOB-объекта, чтобы отображать изображение в пользовательском интерфейсе клиента во время привязки.

	>[AZURE.NOTE]Этот код создает SAS для отдельных больших двоичных объектов. Если требуется передать несколько BLOB-объектов в контейнер при помощи того же SAS, вместо этого можно вызвать <a href="http://go.microsoft.com/fwlink/?LinkId=390455" target="_blank">метод generateSharedAccessSignature</a> с именем пустого BLOB-ресурса, например: <pre><code>blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);</code></pre>

Далее вы обновите приложение быстрого запуска, чтобы добавить функции передачи изображений с помощью SAS, созданных при вставке.
 
<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Создание учетной записи хранения]: ../articles/storage/storage-create-storage-account.md
[Параметры приложения]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=July15_HO4-->