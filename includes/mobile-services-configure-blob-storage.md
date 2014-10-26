Регистрируется новый сценария вставки, создающий подпись общего доступа (SAS) после вставки нового элемента мероприятия.

1.  Если вы еще не создали учетную запись хранения, см. раздел [Создание учетной записи хранения][Создание учетной записи хранения]

2.  В портале управления нажмите **Хранилище**, выберите учетную запись хранения, нажмите **Управление ключами**.

    ![][]

3.  Запишите **имя учетной записи хранения** и **ключ доступа к хранилищу**.

    ![][1]

4.  В своей мобильной службе нажмите вкладку **Настройка**, прокрутите вниз до **Параметры приложения** и введите пару **Имя** и **Значение** для каждого из перечисленных, полученные из учетной записи хранения, а затем нажмите **Сохранить**.

    -   `STORAGE_ACCOUNT_NAME`
    -   `STORAGE_ACCOUNT_ACCESS_KEY`

    ![][2]

    Ключ доступа к учетной записи хранения хранится в зашифрованном виде в параметрах приложения. Этот ключ можно получить из любого серверного сценария во время выполнения. Дополнительные сведения см. в разделе [Параметры приложения][Параметры приложения].

5.  Нажмите вкладку **Данные** и нажмите таблицу **TodoItem**.

    ![][3]

6.  В **todoitem** нажмите вкладку **Сценарий** и выберите **Вставить**, замените функцию вставки следующим кодом, а затем нажмите кнопку **Сохранить**:

        var azure = require('azure');
        var qs = require('querystring');
        var appSettings = require('mobileservice-config').appSettings;

        function insert(item, user, request) {
            // Get storage account settings from app settings. 
            var accountName = appSettings.STORAGE_ACCOUNT_NAME;
            var accountKey = appSettings.STORAGE_ACCOUNT_ACCESS_KEY;
            var host = accountName + '.blob.core.windows.net';

            if ((typeof item.containerName !== "undefined") && (
            item.containerName !== null)) {
                // Set the BLOB store container name on the item, which must be lowercase.
                item.containerName = item.containerName.toLowerCase();

                // If it does not already exist, create the container 
                // with public read access for blobs.        
                var blobService = azure.createBlobService(accountName, accountKey, host);
                blobService.createContainerIfNotExists(item.containerName, {
                    publicAccessLevel: 'blob'
                }, function(error) {
                    if (!error) {

                        // Provide write access to the container for the next 5 mins.        
                        var sharedAccessPolicy = {
                            AccessPolicy: {
                                Permissions: azure.Constants.BlobConstants.SharedAccessPermissions.WRITE,
                                Expiry: new Date(new Date().getTime() + 5 * 60 * 1000)
                            }
                        };

                        // Generate the upload URL with SAS for the new image.
                        var sasQueryUrl = 
                        blobService.generateSharedAccessSignature(item.containerName, 
                        item.resourceName, sharedAccessPolicy);

                        // Set the query string.
                        item.sasQueryString = qs.stringify(sasQueryUrl.queryString);

                        // Set the full path on the new new item, 
                        // which is used for data binding on the client. 
                        item.imageUri = sasQueryUrl.baseUrl + sasQueryUrl.path;

                    } else {
                        console.error(error);
                    }
                    request.execute();
                });
            } else {
                request.execute();
            }
        }

    ![][4]

    Этот заменяет функцию, которая вызывается, когда происходит вставка нового сценария в таблицу TodoItem. Этот новый сценарий создает новый SAS для вставки, действительный в течение 5 минут, и присваивает значение созданного SAS свойству `sasQueryString` возвращенного элемента. Свойство `imageUri` также задается для пути ресурса нового BLOB-объекта, чтобы отображать изображение в пользовательском интерфейсе клиента во время привязки.

    > [WACOM.NOTE] Этот код создает SAS для отдельных BLOB-объектов. Если требуется передать несколько BLOB-объектов в контейнер при помощи того же SAS, вместо этого можно вызвать [метод generateSharedAccessSignature][метод generateSharedAccessSignature] с именем пустого BLOB-ресурса, например:
    >
    >     blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);
    >
    > </p>

Далее вы обновите приложение быстрого запуска, чтобы добавить функции передачи изображений с помощью SAS, созданных при вставке.



  [Создание учетной записи хранения]: /ru-ru/manage/services/storage/how-to-create-a-storage-account
  []: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-account.png
  [1]: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png
  [2]: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png
  [Параметры приложения]: http://msdn.microsoft.com/ru-ru/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
  [3]: ./media/mobile-services-configure-blob-storage/mobile-portal-data-tables.png
  [4]: ./media/mobile-services-configure-blob-storage/mobile-insert-script-blob.png
  [метод generateSharedAccessSignature]: http://go.microsoft.com/fwlink/?LinkId=390455
