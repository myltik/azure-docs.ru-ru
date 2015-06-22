## Настройка строки подключения к хранилищу

Библиотека клиента хранилища Azure для .NET поддерживает использование строки подключения для настройки конечных точек и учетных данных для доступа к службам хранилища. Рекомендуется хранить строку подключения хранилища в файле конфигурации, а не встраивать ее в приложение. Вы можете сохранить строку подключения двумя способами.

- Если приложение запущено в облачной службе Azure, сохраните строку подключения с помощью системы конфигурации службы Azure (файлы`*.csdef` и `*.cscfg`).
- Если приложение запущено в виртуальных машинах Azure или вы создаете приложения .NET, которые будут запускаться за пределами Azure, сохраните строку подключения, используя систему конфигурации .NET (например, файл `web.config` или `app.config`).

Далее в этом учебнике будет показано, как получить строку подключения из кода.

### Настройка строки подключения из облачной службы Azure

Облачные службы Azure обладают уникальным механизмом настройки службы, который позволяет динамически изменять параметры настройки на портале управления Azure, не развертывания приложение повторно.

Настройка строки подключения в конфигурации службы Azure.

1.  В обозревателе решений Visual Studio в папке **Роли**
    проекта развертывания Azure щелкните правой кнопкой мыши
    свою веб-роль или рабочую роль и выберите пункт **Свойства**.  
    ![Select the properties on a Cloud Service role in Visual Studio][connection-string1]

2.  Откройте вкладку **Параметры** и нажмите кнопку **Добавить параметр**.  
    ![Add a Cloud Service setting in visual Studio][connection-string2]

    В таблице параметров появится новая запись **Setting1**.

3.  В раскрывающемся списке **Тип** выберите для новой записи **Setting1** значение
    **Строка подключения**.  
    ![Set connection string type][connection-string3]

4.  Нажмите кнопку **...** в правой части записи **Setting1**.
    Откроется диалоговое окно **Строка подключения учетной записи хранения**.

5.  Укажите эмулятор хранения (хранилище Windows
    Azure, эмулированное на вашем локальном компьютере) или учетную запись
    хранения в облаке. Код в этом руководстве подходит для обоих
    вариантов. 

	> [AZURE.NOTE] Вы можете указать эмулятор хранилища, чтобы избежать затрат, связанных со службой хранилища Microsoft Azure. Однако если вы выберете учетную запись хранения Azure в облаке, затраты на выполнение заданий в учебнике будут незначительны.

	Выбрав учетную запись хранения в облаке, введите первичный ключ доступа для этой учетной записи. Сведения о том, как скопировать первичный ключ доступа через портал управления Azure, см. в разделе 	
	[Практическое руководство. Просмотр, копирование и повторное создание ключей доступа к хранилищу](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).
	
    ![Select target environment][connection-string4]

6.  Измените **имя** параметра **Setting1** на что-то более понятное,
    например **StorageConnectionString**. Эта строка будет
    использована далее в этом руководстве.  
    ![Change connection string name][connection-string5]
	
### Настройка строки подключения с помощью конфигурации .NET

При написании приложения, которое не является облачной службой Azure (см. предыдущий раздел), рекомендуется использовать систему конфигурации .NET (например, `web.config` или `app.config`). Это относится к веб-сайтам Azure или виртуальным машинам Azure, а также приложениям, разработанным для работы вне Azure. Строка подключения хранится в элементе <appSettings>: замените `account-name` именем учетной записи хранения, а `account-key` - ключом доступа учетной записи:

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
  		</appSettings>
	</configuration>

Например, параметр конфигурации в файле конфигурации может быть аналогичным следующему:

	<configuration>
    	<appSettings>
      		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln9fT7bvY+rxu2iWAEyzPNITGkhM88J8HUoyofpK7C8fHcZc2kIZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />
    	</appSettings>
	</configuration>

Теперь все готово для выполнения задач, описанных в данном руководстве.

[connection-string1]: ./media/storage-configure-connection-string-include/connection-string1.png
[connection-string2]: ./media/storage-configure-connection-string-include/connection-string2.png
[connection-string3]: ./media/storage-configure-connection-string-include/connection-string3.png
[connection-string4]: ./media/storage-configure-connection-string-include/connection-string4.png
[connection-string5]: ./media/storage-configure-connection-string-include/connection-string5.png

[Настройка строк подключения]: http://msdn.microsoft.com/library/azure/ee758697.aspx

<!--HONumber=49-->