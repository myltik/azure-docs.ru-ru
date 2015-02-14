## <a name="setup-connection-string"> </a>Настройка строки подключения к хранилищу

Библиотека клиента хранилища Azure для .NET поддерживает использование строки подключения для настройки конечных точек и учетных данных для доступа к службам хранилища. Рекомендуется хранить строку подключения хранилища в файле конфигурации, а не встраивать ее в приложение. Вы можете сохранить строку подключения двумя способами.

- Если приложение запущено в облачной службе Azure, сохраните строку подключения с помощью системы конфигурации службы Azure (файлы`*.csdef` и `*.cscfg`).
- Если приложение запущено в виртуальных машинах Azure или вы создаете приложения .NET, которые будут запускаться за пределами Azure, сохраните строку подключения, используя систему конфигурации .NET (например, файл `web.config` или `app.config`).

Далее в этом учебнике будет показано, как получить строку подключения из кода.

### Настройка строки подключения из облачной службы Azure

Облачные службы Azure обладают уникальным механизмом настройки службы, который позволяет динамически изменять параметры настройки на портале управления Azure, не развертывания приложение повторно.

Настройка строки подключения в конфигурации службы Azure.

1.  В обозревателе решений Visual Studio щелкните правой кнопкой мыши веб-роль или рабочую роль в папке **Роли** вашего проекта развертывания Azure и выберите **Свойства**.  
    ![Select the properties on a Cloud Service role in Visual Studio][connection-string1]

2.  Откройте вкладку **Параметры** и нажмите кнопку **Добавить параметр**.  
    ![Add a Cloud Service setting in visual Studio][connection-string2]
 В таблице параметров появится новая запись **Setting1**.

3.  В раскрывающемся списке **Тип** новой записи **Setting1** выберите **Строка подключения**.  
    ![Set connection string type][connection-string3]

4.  Нажмите кнопку **...** в правой части записи **Setting1**. Откроется диалоговое окно **Строка подключения учетной записи хранения**.

5.  Выберите для использования целевой эмулятор (хранилище Microsoft Azure, смоделированное на локальном компьютере) или учетную запись хранения в облаке. Код, представленный в этом руководстве, работает в любом случае. Введите **Первичный ключ доступа**, скопированный на предыдущем шаге этого учебника, чтобы указать учетную запись хранения, созданную ранее в Azure.

	> [AZURE.NOTE] Вы можете указать эмулятор хранилища, чтобы избежать затрат, связанных со службой хранилища Microsoft Azure. Однако если вы выберете учетную запись хранения Azure в облаке, затраты на выполнение заданий в учебнике будут незначительны.
	
    ![Select target environment][connection-string4]

6.  Измените **Имя** записи с **Setting1** на более понятное имя, например **StorageConnectionString**. Эта строка подключения будет использоваться далее в этом руководстве.  
    ![Change connection string name][connection-string5]
	
### Настройка строки подключения с помощью конфигурации .NET

При написании приложения, которое не является облачной службой Azure (см. предыдущий раздел), рекомендуется использовать систему конфигурации .NET (например, `web.config` или `app.config`). Это относится к веб-сайтам Azure или виртуальным машинам Azure, а также приложениям, разработанным для работы вне Azure. Строка подключения хранится с помощью элемента `<appSettings>` следующим образом: Замените `account-name` именем учетной записи хранения, а `account-key` - ключом доступа учетной записи:

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

Дополнительные сведения о строках подключения хранилища см. в разделе [Настройка строк подключения][].
	
Теперь все готово для выполнения задач, описанных в данном руководстве.

[connection-string1]: ./media/storage-configure-connection-string/connection-string1.png
[connection-string2]: ./media/storage-configure-connection-string/connection-string2.png
[connection-string3]: ./media/storage-configure-connection-string/connection-string3.png
[connection-string4]: ./media/storage-configure-connection-string/connection-string4.png
[connection-string5]: ./media/storage-configure-connection-string/connection-string5.png

[Настройка строк подключения]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee758697.aspx<!--HONumber=42-->
