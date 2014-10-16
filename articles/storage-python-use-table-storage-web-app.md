<properties linkid="develop-python-web-app-with-blob-storage" urlDisplayName="Web App with Blob Storage" pageTitle="Python web app with table storage | Microsoft Azure" metaKeywords="Azure table storage Python, Azure Python application, Azure Python tutorial, Azure Python example" description="A tutorial that teaches you how to create a Python web application using the Azure Client Libraries. Django is used as the web framework." metaCanonical="" services="storage" documentationCenter="Python" title="Python Web Application using Table Storage" authors="huvalo" solutions="" videoId="" scriptId="" manager="" editor="mollybos" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Веб-приложение Python, использующее хранилище таблиц

В этом учебнике вы узнаете, как создать приложение, которое использует хранилище таблиц с помощью клиентских библиотек Azure для Python. Если это ваше первое приложение Python Azure, вы можете сначала рассмотреть [веб-приложение Hello World на Django][].

В этом руководстве вы создадите веб-приложение списка задач, которое можно развернуть в Azure. Список задач позволяет пользователю извлекать задачи, добавлять новые задачи и помечать задачи как завершенные. Мы будем использовать Django в качестве веб-платформы.

Элементы задач хранятся в хранилище Azure. Хранилище Azure обеспечивает хранение неструктурированных данных с функциями отказоустойчивости и высокой доступности. Хранилище Azure включает в себя несколько структур данных, где можно хранить данные и осуществлять доступ к ним. Кроме того, вы можете использовать службы хранилища из API-интерфейсов, включенных в состав пакета Azure SDK для Python, или через
API-интерфейсы REST. Дополнительные сведения см. в разделе [Хранение данных и доступ к ним в Azure][].

Вы узнаете:

-   Как работать со службой хранилища таблиц Azure

Снимок экрана готового приложения будет аналогичен приведенному ниже (элементы добавленных задач будут отличаться):

![][]

[WACOM.INCLUDE [создание-учетная запись-примечание][]]

## <span id="setup"></span> </a>Настройка среды разработки

**Примечание.** Если нужно установить клиентские библиотеки или Python, см. [Руководство по установке Python][].

*Примечание для Windows*: Если вы использовали установщик Windows WebPI, Django и клиентские библиотеки уже установлены.

## Создание учетной записи хранения в Azure

[WACOM.INCLUDE [создание-хранилище-учетная запись][]]

## Создание проекта Django

Ниже приведены шаги по созданию приложения.

-   Создание проекта Django по умолчанию с именем "TableserviceSample"
-   В командной строке перейдите в каталог, где хотите сохранить код, а затем выполните следующую команду:

        django-admin.py startproject TableserviceSample

-   Добавьте новый файл Python **views.py** в проект
-   Добавьте следующий код **views.py** для импорта требуемой поддержки Django:

        from django.http import HttpResponse
        from django.template.loader import render_to_string
        from django.template import Context

-   Создайте новую папку с именем **templates** в папке **TableserviceSample/TableserviceSample**.
-   Измените параметры приложения, чтобы можно было расположить шаблоны. Откройте **settings.py** и добавьте следующую запись в раздел INSTALLED\_APPS:

        'TableserviceSample',

-   Добавьте новый файл шаблона Django **mytasks.html** в папку **templates** и добавьте к нему следующий код:

<!-- -->

        <html>
        <head><title></title></head>
        <body>
        <h2>My Tasks</h2> <br>
        <table border="1"> 
        <tr>
        <td>Name</td><td>Category</td><td>Date</td><td>Complete</td><td>Action</td></tr>
        {% for entity in entities %}
        <form action="update_task" method="GET">
        <tr><td>{{entity.name}} <input type="hidden" name='name' value="{{entity.name}}"></td>
        <td>{{entity.category}} <input type="hidden" name='category' value="{{entity.category}}"></td>
        <td>{{entity.date}} <input type="hidden" name='date' value="{{entity.date}}"></td>
        <td>{{entity.complete}} <input type="hidden" name='complete' value="{{entity.complete}}"></td>

        <td><input type="submit" value="Complete"></td>
        </tr>
        </form>
        {% endfor %}
        </table>
        <br>
        <hr>
        <table border="1">
        <form action="add_task" method="GET">
        <tr><td>Name:</td><td><input type="text" name="name"></input></td></tr>
        <tr><td>Category:</td><td><input type="text" name="category"></input></td></tr>
        <tr><td>Item Date:</td><td><input type="text" name="date"></input></td></tr>
        <tr><td><input type="submit" value="add task"></input></td></tr>
        </form>
        </table>
        </body>
        </html>    

## Импорт модуля хранения windowsazure

Добавьте следующий код в начало **views.py** сразу после импортов Django

    from azure.storage import TableService

## Получение имени учетной записи хранения и ключа учетной записи

Добавьте следующий код в **views.py** сразу после импорта windowsazure и замените "yourkey" и "youraccount" на реальные значения ключа и имени учетной записи. Ключ и имя учетной записи можно получить через портал управления Azure.

    account_name = 'youraccount'
    account_key = 'yourkey'

## Создание службы TableService

Добавьте следующий код после **account\_name**:

    table_service = TableService(account_name=account_name, account_key=account_key)
    table_service.create_table('mytasks')

## Перечисление задач

Добавьте функцию list\_tasks к **views.py**:

    def list_tasks(request): 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

## Добавление задачи

Добавьте функцию add\_task к **views.py**:

    def add_task(request):
        name = request.GET['name']
        category = request.GET['category']
        date = request.GET['date']
        table_service.insert_entity('mytasks', {'PartitionKey':name+category, 'RowKey':date, 'name':name, 'category':category, 'date':date, 'complete':'No'}) 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

## Обновление состояния задачи

Добавьте функцию update\_task к **views.py**:

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

## Выполнение приложения

-   Перейдите в каталог **TableserviceSample**, если это еще не сделано, и выполните команду:

    python manage.py runserver

-   Введите в браузере: `http://127.0.0.1:8000/`. Замените 8000 на реальный номер порта.

Теперь можно щелкнуть элемент **Добавить задачу** для создания задачи и нажать кнопку **Завершить**, чтобы обновить задачу и установить состояние "Да".

## Запуск приложения в эмуляторе вычислений, публикация и остановка/удаление приложения

Теперь, когда вы успешно запустили свое приложение на встроенном сервере Django, можно проверить его работу, развернув его в эмуляторе Azure (только для Windows) и затем опубликовав в Azure. Общие указания о том, как это сделать, приведены в статье [Веб-приложение Hello World на Django][веб-приложение Hello World на Django].

## <span id="NextSteps"></span></a> Дальнейшие действия

Вы изучили основные сведения о службе хранилища таблиц Azure. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. справочник MSDN: [Хранение данных и доступ к ним в Azure][]
-   Посетите блог команды разработчиков хранилища Azure <http://blogs.msdn.com/b/windowsazurestorage/>

  [веб-приложение Hello World на Django]: http://windowsazure.com/ru-ru/documentation/articles/virtual-machines-python-django-web-app-windows-server
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  []: ./media/storage-python-use-table-storage-web-app/web-app-with-storage-Finaloutput-mac.png
  [создание-учетная запись-примечание]: ../includes/create-account-note.md
  [Руководство по установке Python]: http://windowsazure.com/ru-ru/documentation/articles/python-how-to-install
  [создание-хранилище-учетная запись]: ../includes/create-storage-account.md
