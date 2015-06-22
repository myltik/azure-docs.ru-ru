<properties 
	pageTitle="Как использовать приложение Chrome Postman со службой &quot;Поиск Azure&quot;" 
	description="Как использовать приложение Chrome Postman со службой &quot;Поиск Azure&quot;" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="04/27/2015" 
	ms.author="heidist"/>

# Как использовать приложение Chrome Postman со службой "Поиск Azure" #

[Postman](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm "Chrome Postman") — это приложение для браузера Google Chrome, которое позволяет разработчикам эффективно работать со службами на основе интерфейса API REST, такими как Поиск Azure. Приложение Postman отправляет вызовы API для быстрого создания и запроса индексов поиска без необходимости написания кода. С помощью этого средства вы сможете быстро изучить интерфейс API и опробовать новые компоненты.

![][1]
 
## Требования ##

Требуется служба "Поиск Azure". Как и с любым пользовательским приложением, использующим службу "Поиск Azure", для создания индекса вам потребуется URL-адрес службы, а также ключ API (`api-key`). Инструкции о том, как получить значения для службы поиска, приведены в [статье, посвященной созданию службы на портале](search-create-service-portal.md) .

## Установка приложения Postman ##
Чтобы загрузить приложение Postman, посетите [интернет-магазин Google Chrome](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm). По ссылке на этой странице можно загрузить и установить клиент REST для приложения Postman. После установки это приложение можно запустить на панели запуска Chrome.

![][2]
 
## Настройка приложения Postman для отправки запросов в службу "Поиск Azure" ##
Чтобы настроить приложение Postman, выполните следующие действия.

1. Введите URL-адрес службы "Поиск Azure" в поле с надписью Enter request URL here (Введите URL-адрес запроса).  
2. Добавьте следующий текст к URL-адресу: `?api-version=2015-02-28`. Также можно задать другую версию API. Подробные сведения приведены в статье [Управление версиями службы поиска Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx).
3. Убедитесь, что выбран метод `GET`.
4. Нажмите кнопку **Headers** (Заголовки).
5. Введите значения для следующих параметров:
	- `api-key`: [ключ администратора];
	- `Content-Type`: `application/json; charset=utf-8`.
6. Нажмите кнопку **Send** (Отправить), чтобы отправить вызов REST в службу "Поиск Azure" и визуализировать ответ JSON.

![][3]

## Создание индекса службы "Поиск Azure" с помощью приложения Postman ##

После этого шага необходимо отправить вызов REST, чтобы создать индекс службы "Поиск Azure". В отличие от предыдущего вызова, для создания индекса требуется метод HTTP PUT, а также документ JSON с определением схемы индекса. В данном примере мы собираемся создать индекс для хранения списка походных маршрутов. Для этого:

1. Измените URL-адрес на `https://[SEARCH SERVICE].search.windows.net/indexes/trails?api-version=2015-02-28`, указав имя службы поиска.
2. Измените тип запроса с `GET` на `PUT`.
3. В основном содержимом RAW введите следующий объект JSON.

	    {
	    "name": "trails", 
	    "fields": [
	    {"name": "id", "type": "Edm.String", "key": true, "searchable": false}, 
	    {"name": "name", "type": "Edm.String"}, 
	    {"name": "county", "type": "Edm.String"}, 
	    {"name": "elevation", "type": "Edm.Int32"}, 
	    {"name": "location", "type": "Edm.GeographyPoint"} ]
	    }

4. Нажмите кнопку **Send** (Отправить).

![][4]
 
## Публикация документов в индексе службы "Поиск Azure" с помощью приложения Postman ##
После создания индекса в него можно загрузить документы. Для этого мы опубликуем группу документов в пакете, используя сведения о пяти маршрутах из набора данных, предоставленных Геологической службой США (USGS).

1. Измените URL-адрес на `https://[SEARCH SERVICE].windows.net/indexes/trails/docs/index?api-version=2015-02-28`, указав имя службы поиска. Обратите внимание, что URL-адрес включает путь к только что созданному индексу.
2. Измените тип HTTP на `POST`.
3. В основном содержимом RAW введите следующий объект JSON.

	    {
	      "value": [
		    {"@search.action": "upload", "id": "233358", "name": "Pacific Crest National Scenic Trail", "county": "San Diego", "elevation":1294, "location": { "type": "Point", "coordinates": [-120.802102,49.00021] }},
		    {"@search.action": "upload", "id": "801970", "name": "Lewis and Clark National Historic Trail", "county": "Richland", "elevation":584, "location": { "type": "Point", "coordinates": [-104.8546903,48.1264084] }},
		    {"@search.action": "upload", "id": "1144102", "name": "Intake Trail", "county": "Umatilla", "elevation":1076, "location": { "type": "Point", "coordinates": [-118.0468873,45.9981939] }},
		    {"@search.action": "upload", "id": "1509897", "name": "Burke Gilman Trail", "county": "King", "elevation":10, "location": { "type": "Point", "coordinates": [-122.2754036,47.7059315] }},
		    {"@search.action": "upload", "id": "1517508", "name": "Cavanaugh-Oso Truck Trail", "county": "Skagit", "elevation":339, "location": { "type": "Point", "coordinates": [-121.9470829,48.2981608] }}
	      ]
	    }
    
4. Нажмите кнопку **Send** (Отправить).

![][5]

## Отправка запроса в индекс с помощью приложения Postman ##
Последний шаг — отправить запрос в индекс и создать полнотекстовый поисковой запрос слова *trail* (маршрут).

1. Используя имя службы поиска, включите в URL-адрес следующие данные: `https://[SEARCH SERVICE].search.windows.net/indexes/trails/docs?api-version=2015-02-28&search=trail`. Обратите внимание, что URL-адрес включает параметр запроса `search` и условие поиска *trail* (маршрут).
2. Измените тип запроса HTTP на `GET`.
3. Нажмите кнопку **Send** (Отправить).
 
В ответе отобразятся результаты поиска в формате JSON, возвращаемые службой "Поиск Azure".

![][6]

## Дальнейшие действия ##
Вы уже знаете, как использовать службу "Поиск Azure" с приложением Postman. Оно также поддерживает следующие возможности, которые помогут вам выполнить дальнейшие действия.

1. Приложение Postman поддерживает параметр `Collections` (Коллекции), который позволяет с легкостью сохранять часто создаваемые запросы. Вы можете обмениваться коллекциями с другими пользователями, чтобы те смогли использовать их в собственных копиях приложения Postman.
2. В документации по службе "Поиск Azure" обязательно запишите тип запроса HTTP (`GET`, `PUT` и так далее), связанный с каждым вызовом, и соответствующим образом измените его в приложении Postman.

Документацию по API REST можно найти на сайте [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Дополнительные примеры также приводятся на [странице с видеодемонстрациями и учебниками](https://msdn.microsoft.com/library/azure/dn818681.aspx).

<!-- Image References -->
[1]: ./media/search-chrome-postman/full_postman_client.png
[2]: ./media/search-chrome-postman/postman.png
[3]: ./media/search-chrome-postman/configure.png
[4]: ./media/search-chrome-postman/create_index.png
[5]: ./media/search-chrome-postman/upload_documents.png
[6]: ./media/search-chrome-postman/query.png

<!--HONumber=54--> 