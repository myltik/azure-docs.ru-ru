<properties pageTitle="Calling SQL stored procedures with a JavaScript back end" metaKeywords="stored procedures, SQL, mobile devices, Azure" description="explains how to use SQL stored procedures in a mobile services JavaScript backed." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Calling SQL stored procedures with a JavaScript back end" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Вызов процедур, сохраненных в SQL, с помощью внутреннего сервера JavaScript

Причины, по которым вам может потребоваться вызвать процедуры, сохраненные в SQL Server:

-   мобильная служба использует существующие сохраненные процедуры, к которым нужно получить доступ;
-   архитектура приложения предписывает использование сохраненных процедур для доступа к базе данных;
-   персональные предпочтения.

В этом разделе даются следующие подробные инструкции.

-   [Вызов простой сохраненной процедуры][Вызов простой сохраненной процедуры]
-   [Вызов сохраненной процедуры с параметрами][Вызов сохраненной процедуры с параметрами]
-   [Дополнительные сведения][Дополнительные сведения]

В этой статье описаны сохраненные процедуры в мобильной службе с внутренним сервером JavaScript.

Однако вместо этого можно создать мобильные службы с внутренним сервером .Net, который имеет совершенно другую архитектуру на основе Entity Framework. Дополнительные сведения см. в разделе [Принципы работы внутреннего сервера .Net мобильных служб Azure][Принципы работы внутреннего сервера .Net мобильных служб Azure].

## Вызов простой сохраненной процедуры

**"Моя база данных использует сохраненные процедуры. Как можно вызвать их из мобильной службы?"**

Это можно сделать с помощью [объекта mssql][объекта mssql], который позволяет выполнять инструкции *Transact-SQL* ("T-SQL"), вызывающие процедуру.

### Запись и тестирование кода Transact SQL

Предположим, что сохраненная процедура называется *GetAll*, а имя мобильной службы — **todolist**.

1.  Щелкните значок **Базы данных** на боковой панели портала Azure, а затем выберите свою базу данных. Имя базы данных является именем мобильной службы с суффиксом "<b>_db</b>".

2.  На странице быстрого запуска "База данных" щелкните ссылку **Управление** в нижней части страницы.

3.  Войдите в базу данных.

4.  Нажмите кнопку **Создать запрос** на верхней панели и добавьте следующий код:

            EXEC todolist.GetAll

5.  Щелкните кнопку **Запустить** и проверьте результаты.

Обратите внимание, что имя процедуры включает префикс с именем схемы базы данных, которое по умолчанию является именем вашей мобильной службы.

### Куда следует направить вызов объекта mssql?

Если требуется прямо вызвать сохраненную процедуру, самый гибкий способ — записать и вызвать [пользовательский API][пользовательский API].

1.  На панели мониторинга мобильной службы щелкните **API**, а затем — **СОЗДАТЬ**, назовите скрипт ***getall*** и добавьте этот код в вызов сохраненной процедуры:

        exports.get = function(request, response) {
            var mssql = request.service.mssql;
            var sql = "EXEC todolist.GetAll";
            mssql.query(sql, {
                success: function(results) {                          
                        response.send(200, results); 
                }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

2.  Протестируйте его в браузере, поместив этот URL-адрес в адресную строку:

        https://todolist.azure-mobile.net/api/getall

Вместо этого можно поместить аналогичный код в стандартный скрипт сервера таблицы *read*, *insert*, *update* или *delete*, тогда код обойдет поведение по умолчанию и вызовет сохраненную процедуру. Аналогичный код можно поместить в скрипт *Scheduler*. Однако самый гибкий способ — это создание пользовательского API.

### Вызов кода из клиента

Измените код клиента, чтобы вызвать метод **invokeApi** в объекте **MobileServiceClient**. Точный синтаксис кода зависит от клиентского устройства и описывается в следующих разделах:

-   [C# в Магазине Windows][C# в Магазине Windows]
-   [JavaScript в Магазине Windows][JavaScript в Магазине Windows]
-   [Windows Phone][Windows Phone]
-   [iOS][iOS]
-   [Android][Android]
-   [HTML][HTML]

## <a name="parameters"></a>Вызов сохраненной процедуры с параметрами

Предположим, что сохраненная процедура называется *ItemsByStatus*, она имеет один параметр с именем *Status*, и ее нужно вызвать из пользовательского API.

1.  Выполните шаги в предыдущем разделе, чтобы записать и протестировать код T-SQL на портале баз данных Azure, но используйте следующий код T-SQL:

        EXEc todolist.ItemsByStatus @Status = 1

2.  Щелкните значок **Запустить** и проверьте результаты.

3.  Как и в предыдущем шаге, создайте пользовательский API с именем **completeall** со следующим кодом, который вызывает сохраненную процедуру. Обратите внимание, что в коде ниже фактическое значение параметра <b>@Status</b>, использовавшееся при тестировании, заменяется на параметр <b>"?"</b>, который заполняется в среде выполнения с помощью указанного параметра.

        exports.get = function(request, response) { 
            var mssql = request.service.mssql;
            var param1 = parseInt(request.query.status);
            var sql = "EXEC todolist.ItemsByStatus @Status = ?";
            mssql.query(sql, [param1], {
                success: function(results) { 
                         response.send(200, results[0]); 
                    }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

4.  Протестируйте API с помощью этого URL-адреса в браузере:

        https://todolist.azure-mobile.net/api/completeall?status=1

5.  Вызовите API из клиента, как описано в предыдущем разделе.

### Более сложные подписи параметра

Сохраненная процедура в предыдущем разделе содержала только один параметр. Ниже представлен синтаксис для использования более длинной подписи:

        function read(query, user, request) {
            var sql = 'EXEC MySProc @Param1 = ?, @Param2 = ?, @Param3 = ?';
            var param1 = request.parameters.first;
            var param2 = request.parameters.second;
            var param3 = request.parameters.third;
            mssql.query(sql, [param1, param2, param3], {
                success: function(results) {
                    request.respond(200, results);
                }
            });
        }

Обратите внимание, что в операторе **EXEC** параметры вызываются по имени, а не по позиции. Это рекомендуемый синтаксис, он изолирует код от мелких изменений в сохраненной процедуре, например, изменения порядка параметров или добавления дополнительных параметров.

## <a name="more"></a>Дополнительные сведения

В этом разделе представлены общие сведения.

Подробные ссылки на использованный код см. в следующих разделах:

-   -   

Вы можете столкнуться со следующими сценариями:

-   [Выполнение более 1 операции чтения с помощью скриптов][Выполнение более 1 операции чтения с помощью скриптов]. В этом разделе описывается, как прочитать таблицу (с условиями) или вызвать сохраненную процедуру другим способом. Блогер Карлос Фигейра (Carlos Figueira) часто публикует записи о мобильных службах и базах данных Azure.

-   [Доступ к сохраненным процедурам из другой схемы][Доступ к сохраненным процедурам из другой схемы]. В этом разделе описываются решения проблем, которые могут возникнуть при доступе к сохраненным процедурам, которые хранятся в других схемах той же базы данных мобильных служб.

Для создания сохраненных процедур и управления ими можно также использовать портал Azure.




  [Вызов простой сохраненной процедуры]: #simple
  [Вызов сохраненной процедуры с параметрами]: #parameters
  [Дополнительные сведения]: #more
  [Принципы работы внутреннего сервера .Net мобильных служб Azure]: http://curah.microsoft.com/64518/how-the-azure-mobile-services-net-backend-works
  [объекта mssql]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj554212.aspx
  [пользовательский API]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn280974.aspx
  [C# в Магазине Windows]: http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
  [JavaScript в Магазине Windows]: http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-windows-store-javascript-call-custom-api/
  [Windows Phone]: http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-windows-phone-call-custom-api/
  [iOS]: http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-ios-call-custom-api/
  [Android]: http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-android-call-custom-api/
  [HTML]: http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-html-call-custom-api/
  [Выполнение более 1 операции чтения с помощью скриптов]: http://social.msdn.microsoft.com/Forums/windowsazure/ru-ru/fccf4ae7-f43c-4c2d-8518-32e2df84a824/how-do-i-do-more-than-1-read-operation-by-scripting?forum=azuremobile
  [Доступ к сохраненным процедурам из другой схемы]: http://blogs.msdn.com/b/jpsanders/archive/2013/05/02/windows-azure-mobile-services-accessing-a-stored-procedure-from-a-different-schema.aspx
