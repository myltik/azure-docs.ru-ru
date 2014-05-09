<properties linkid="develop-python-web-app-with-blob-storage" urlDisplayName="Веб-приложение с хранилищем BLOB-объектов" pageTitle="Веб-приложение Python с хранилищем таблиц | Microsoft Azure" metaKeywords="хранилище таблиц Python Azure, приложения Python Azure, учебник Python Azure, пример Python Azure" description="Учебник, в котором показано, как создать веб-приложение Python с помощью клиентских библиотек Azure. Django используется в качестве веб-платформы." metaCanonical="" services="storage" documentationCenter="Python" title="Веб-приложение Python, использующее хранилище таблиц" authors="" solutions="" videoId="" scriptId="" manager="" editor="mollybos" />





# Веб-приложение Python, использующее хранилище таблиц

В этом учебнике вы узнаете, как создать приложение, которое использует хранилище таблиц с помощью клиентских библиотек Azure для Python. Если это ваше первое приложение Python Azure, вы можете сначала рассмотреть [веб-приложение Hello World на Django][].

В этом руководстве вы создадите веб-приложение списка задач, которое можно развернуть в Azure. Список задач позволяет пользователю извлекать задачи, добавлять новые задачи и помечать задачи как завершенные.  Мы будем использовать Django в качестве веб-платформы.

Элементы задач хранятся в хранилище Azure. Хранилище Azure обеспечивает хранение неструктурированных данных с функциями отказоустойчивости и высокой доступности. Хранилище Azure включает в себя несколько структур данных, где можно хранить данные и осуществлять доступ к ним, кроме того, вы можете использовать службы хранилища из API-интерфейсов, включенных в состав пакета Azure SDK для Python, или через API-интерфейсы REST. Дополнительные сведения см. в разделе [Хранение данных и доступ к ним в Azure].

Вы узнаете:

-   Как работать со службой хранилища таблиц Azure

Снимок экрана готового приложения будет аналогичен приведенному ниже (элементы добавленных задач будут отличаться):

![](./media/storage-python-use-table-storage-web-app/web-app-with-storage-Finaloutput-mac.png)

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <a id="setup"> </a>Настройка среды разработки

**Примечание.** Если нужно установить клиентские библиотеки или Python, см. [руководство по установке Python](http://windowsazure.com/ru-ru/documentation/articles/python-how-to-install).



*Примечание для Windows*. Если вы использовали установщик Windows WebPI, Django и клиентские библиотеки уже установлены.

## Создание учетной записи хранения в Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## Создание проекта Django

Ниже приведены шаги по созданию приложения.

-   Создание проекта Django по умолчанию с именем "TableserviceSample" 
- 	В командной строке перейдите в каталог, где хотите сохранить код, а затем выполните следующую команду: 

		django-admin.py startproject TableserviceSample

-   Добавление нового файла Python **views.py** в проект
-   Добавьте следующий код в **views.py** для импорта требуемой поддержки Django:
           
        from django.http import HttpResponse
        from django.template.loader import render_to_string
        from django.template import Context

-   Создайте новую папку с именем **templates** в папке **TableserviceSample/TableserviceSample**.
-   Измените параметры приложения, чтобы можно было расположить шаблоны. Откройте **settings.py** и добавьте следующую запись в раздел INSTALLED_APPS:

        'TableserviceSample',

-   Добавьте новый файл шаблона Django **mytasks.html** в папку **templates** и добавьте в него следующий код:
 
<pre>
	&lt;html&gt;
	&lt;head&gt;&lt;title&gt;&lt;/title&gt;&lt;/head&gt;
	&lt;body&gt;
	&lt;h2&gt;My Tasks&lt;/h2&gt; &lt;br&gt;
	&lt;table border="1"&gt; 
	&lt;tr&gt;
	&lt;td&gt;Name&lt;/td&gt;&lt;td&gt;Category&lt;/td&gt;&lt;td&gt;Date&lt;/td&gt;&lt;td&gt;Complete&lt;/td&gt;&lt;td&gt;Action&lt;/td&gt;&lt;/tr&gt;
	{% for entity in entities %}
	&lt;form action=&quot;update_task&quot; method=&quot;GET&quot;&gt;
	&lt;tr&gt;&lt;td&gt;{{entity.name}} &lt;input type=&quot;hidden&quot; name='name' value=&quot;{{entity.name}}&quot;&gt;&lt;/td&gt;
	&lt;td&gt;{{entity.category}} &lt;input type=&quot;hidden&quot; name='category' value=&quot;{{entity.category}}&quot;&gt;&lt;/td&gt;
	&lt;td&gt;{{entity.date}} &lt;input type=&quot;hidden&quot; name='date' value=&quot;{{entity.date}}&quot;&gt;&lt;/td&gt;
	&lt;td&gt;{{entity.complete}} &lt;input type=&quot;hidden&quot; name='complete' value=&quot;{{entity.complete}}&quot;&gt;&lt;/td&gt;

	&lt;td&gt;&lt;input type=&quot;submit&quot; value=&quot;Complete&quot;&gt;&lt;/td&gt;
	&lt;/tr&gt;
	&lt;/form&gt;
	{% endfor %}
	&lt;/table&gt;
	&lt;br&gt;
	&lt;hr&gt;
	&lt;table border=&quot;1&quot;&gt;
	&lt;form action=&quot;add_task&quot; method=&quot;GET&quot;&gt;
	&lt;tr&gt;&lt;td&gt;Name:&lt;/td&gt;&lt;td&gt;&lt;input type=&quot;text&quot; name=&quot;name&quot;&gt;&lt;/input&gt;&lt;/td&gt;&lt;/tr&gt;
	&lt;tr&gt;&lt;td&gt;Category:&lt;/td&gt;&lt;td&gt;&lt;input type=&quot;text&quot; name=&quot;category&quot;&gt;&lt;/input&gt;&lt;/td&gt;&lt;/tr&gt;
	&lt;tr&gt;&lt;td&gt;Item Date:&lt;/td&gt;&lt;td&gt;&lt;input type=&quot;text&quot; name=&quot;date&quot;&gt;&lt;/input&gt;&lt;/td&gt;&lt;/tr&gt;
	&lt;tr&gt;&lt;td&gt;&lt;input type=&quot;submit&quot; value=&quot;add task&quot;&gt;&lt;/input&gt;&lt;/td&gt;&lt;/tr&gt;
	&lt;/form&gt;
	&lt;/table&gt;
	&lt;/body&gt;
	&lt;/html&gt;    

</pre> 

    
## Импорт модуля хранения windowsazure
Добавьте следующий код в начало **views.py** сразу после операций импорта Django

	from azure.storage import TableService

## Получение имени учетной записи хранения и ключа учетной записи
Добавьте следующий код в **views.py** сразу после импорта windowsazure и замените "yourkey" и "youraccount" на реальные значения ключа и имени учетной записи. Ключ и имя учетной записи можно получить через портал управления Azure. 

	account_name = 'youraccount'
	account_key = 'yourkey'

## Создание TableService
Добавьте следующий код после **account_name**:

	table_service = TableService(account_name=account_name, account_key=account_key)
	table_service.create_table('mytasks')

## Список задач
Добавьте функцию list_tasks в **views.py**:

	def list_tasks(request): 
		entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
		html = render_to_string('mytasks.html', Context({'entities':entities}))
		return HttpResponse(html)

##  Добавление задачи
Добавьте функцию add_task в **views.py**:

	def add_task(request):
		name = request.GET['name']
		category = request.GET['category']
		date = request.GET['date']
		table_service.insert_entity('mytasks', {'PartitionKey':name+category, 'RowKey':date, 'name':name, 'category':category, 'date':date, 'complete':'No'}) 
		entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')
		html = render_to_string('mytasks.html', Context({'entities':entities}))
		return HttpResponse(html)

## Обновление состояния задачи
Добавьте функцию update_task в **views.py**:

	def update_task(request):
		name = request.GET['name']
		category = request.GET['category']
		date = request.GET['date']
		partition_key = name + category
		row_key = date
		table_service.update_entity('mytasks', partition_key, row_key, {'PartitionKey':partition_key, 'RowKey':row_key, 'name': name, 'category':category, 'date':date, 'complete':'Yes'})
		entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
		html = render_to_string('mytasks.html', Context({'entities':entities}))
		return HttpResponse(html)


## Сопоставление URL-адресов
Теперь необходимо сопоставить URL-адреса в приложении Django. Откройте **urls.py** и добавьте следующие сопоставления в urlpatterns:

	url(r'^$', 'TableserviceSample.views.list_tasks'),
	url(r'^list_tasks$', 'TableserviceSample.views.list_tasks'),
	url(r'^add_task$', 'TableserviceSample.views.add_task'),
	url(r'^update_task$', 'TableserviceSample.views.update_task'),

## Запуск приложения


-  Перейти в каталог **TableserviceSample**, если это еще не сделано, и выполните команду:

	python manage.py runserver

-   Введите в браузере `http://127.0.0.1:8000/`. Замените 8000 на реальный номер порта.

Теперь можно щелкнуть элемент **Добавить задачу** для создания задачи и нажать кнопку **Завершить**, чтобы обновить задачу и установить состояние "Да".



## Запуск приложения в эмуляторе вычислений, публикация и остановка/удаление приложения

Теперь, когда вы успешно запустили свое приложение на встроенном сервере Django, можно проверить его работу, развернув его в эмуляторе Azure (только для Windows) и затем опубликовав в Azure.  Общие указания о том, как это сделать, приведены в статье [Веб-приложение Hello World на Django].


<h2><a id="NextSteps"></a>Следующие шаги</h2>

Вы изучили основные сведения о службе хранилища таблиц Azure. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

- См. справочник MSDN: [Хранение данных и доступ к ним в Azure] []
- Посетите блог команды разработчиков хранилища Azure <http://blogs.msdn.com/b/windowsazurestorage/>


[Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx

[Руководство по установке]: ../python-how-to-install 

[Веб-приложение Hello World на Django]: http://windowsazure.com/ru-ru/documentation/articles/virtual-machines-python-django-web-app-windows-server

