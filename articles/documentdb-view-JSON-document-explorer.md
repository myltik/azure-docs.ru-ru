<properties 
	pageTitle="Просмотр, изменение, создание и отправка документов JSON с помощью обозревателя документов DocumentDB | Azure" 
	description="Сведения об обозревателе документов DocumentDB, средстве портала предварительной версии Azure для просмотра, изменения, создания и отправки документов JSON с помощью DocumentDB." 
	services="documentdb" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/13/2015" 
	ms.author="stbaro"/>

# Просмотр, изменение, создание и отправка документов JSON с помощью обозревателя документов DocumentDB #

В этой статье приведены общие сведения об обозревателе документов [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), средстве портала предварительной версии Azure для просмотра, изменения, создания и отправки документов JSON с помощью DocumentDB. 

После завершения этого учебника вы сможете ответить на следующие вопросы:  

-	Как легко создать, просмотреть, изменить и удалить отдельные документы DocumentDB через веб-браузер?
-	Как легко просмотреть системные свойства документа DocumentDB через веб-браузер?
-	Как легко выполнить массовый прием документов в DocumentDB через веб-браузер?

##<a id="Launch"></a>Запуск обозревателя документов и навигация в нем##

Обозреватель документов можно запустить из любой учетной записи DocumentDB, базы данных и панелей коллекции.  

1. В нижней части каждой панели находятся колонки **Средства разработчика**, которые содержат часть **Обозреватель документов**.

	![Screenshot of the Document Explorer part](./media/documentdb-view-JSON-document-explorer/documentexplorerpart.png) 

2. Чтобы запустить обозреватель документов, достаточно просто щелкнуть плитку.

	<p>Поля раскрывающихся списков **База данных** и **Коллекция** заполняются заранее в зависимости от контекста, в котором запускается обозреватель документов.  Например, при запуске из панели базы данных предварительно заполняется текущая база данных.  Если запуск выполняется из панели коллекции, предварительно заполняется текущая коллекция.

	![Screenshot of Document Explorer](./media/documentdb-view-JSON-document-explorer/documentexplorerinitial.png)

3. Обозреватель документов загружает первые 100 документы в выбранной коллекции.  Можно загрузить дополнительные документы (в пакетах по 100), выбрав параметр **Дополнительная загрузка** в нижней части панели обозревателя документов.  

4. Поля раскрывающихся списков **База данных** и **Коллекция** можно использовать для простого изменения коллекции, документы которой просматриваются в настоящее время, без необходимости закрывать и повторно запускать обозреватель документов.  

5. Обозреватель документов также поддерживает фильтрацию загруженного в настоящий момент времени набора документов по их свойству идентификатора.  Достаточно просто ввести в поле фильтра.

	![Screenshot of Document Explorer with filter highlighted](./media/documentdb-view-JSON-document-explorer/documentexplorerfilter.png) 

	При этом результаты в списке обозревателя документов будут отфильтрованы на основе предоставленных критериев.

	![Screenshot of Document Explorer with filtered results](./media/documentdb-view-JSON-document-explorer/documentexplorerfilterresults.png)


	> [AZURE.IMPORTANT] Функциональные возможности фильтрации обозревателя документов обеспечивают только фильтрацию загруженного в ***настоящее время*** набора документов и не выполняют запросы к выбранной в данный момент коллекции.

6. Чтобы обновить список документов, которые загружены в обозревателе документов, щелкните команду **Обновить** в верхней части панели.

	![Screenshot of Document Explorer refresh command](./media/documentdb-view-JSON-document-explorer/documentexplorerrefresh.png)


##<a id="Create"></a>Просмотр, создание и изменение документов с помощью обозревателя документов##

Обозреватель документов позволяет легко создавать, изменять и удалять документы.  

- Чтобы создать документ, просто щелкните команду **Создать документ**, после чего появляется минимальный фрагмент JSON.

	![Screenshot of Document Explorer create document experience](./media/documentdb-view-JSON-document-explorer/createdocument.png) 

- Просто введите или вставьте содержимое JSON документа, который необходимо создать, и щелкните команду **Сохранить**, чтобы зафиксировать документ.

	![Screenshot of Document Explorer save command](./media/documentdb-view-JSON-document-explorer/savedocument1.png)

	> [AZURE.NOTE] Если не указано свойство id (идентификатор), обозреватель документов автоматически добавляет свойство id и создает GUID как значение идентификатора. 

- Чтобы изменить существующий документ, просто выберите его в обозревателе документов, измените документ по своему усмотрению и щелкните команды **Сохранить**.

	![Screenshot of Document Explorer edit document functionality](./media/documentdb-view-JSON-document-explorer/editdocument.png)

- Если в момент изменения документа понадобится отменить текущие изменения, достаточно просто щелкнуть команду Отменить, подтвердить действие отмены и в результате повторно загружается предыдущее состояние документа.

	![Screenshot of Document Explorer discard command](./media/documentdb-view-JSON-document-explorer/discardedit.png)

- Для удаления документа выделите его, щелкните команду **Удалить** и подтвердите операцию удаления. После подтверждения документ немедленно удаляется из списка обозревателя документов:

	![Screenshot of Document Explorer delete command](./media/documentdb-view-JSON-document-explorer/deletedocument.png)

- Следует иметь в виду, что обозреватель документов выполняет проверку нового или измененного документа на содержание допустимых данных JSON.  При наведении указателя мыши на неверный раздел можно даже получить сведения об ошибке проверки.

	![Screenshot of Document Explorer invalid JSON highlighting](./media/documentdb-view-JSON-document-explorer/invalidjson1.png)

- Кроме этого, обозреватель документов предотвращает сохранение документа с недопустимым содержимым JSON.

	![Screenshot of Document Explorer invalid JSON save error](./media/documentdb-view-JSON-document-explorer/invalidjson2.png)

- И наконец, обозреватель документов позволяет легко просмотреть системные свойства текущего загруженного документа, щелкнув команду **Свойства**.

	![Screenshot of Document Explorer document properties view](./media/documentdb-view-JSON-document-explorer/documentproperties.png)

	> [AZURE.NOTE] Свойство метки времени (_ts) внутренне представлено как время эпохи, но обозреватель документов отображает значение в читаемом формате GMT.

##<a id="BulkAdd"></a>Массовое добавление документов с помощью обозревателя документов##

Обозреватель документов поддерживает массовый приме одного или нескольких существующих документов JSON.  

1. Чтобы начать процесс загрузки, щелкните команду **Добавить документ**.
	
	![Screenshot of Document Explorer bulk ingestion functionality](./media/documentdb-view-JSON-document-explorer/adddocument1.png)

2. Открывается новая панель.  Щелкните кнопку обзора, чтобы открыть окно обозревателя файлов и выберите один или несколько документов JSON для передачи.

	![Screenshot of Document Explorer bulk ingestion process](./media/documentdb-view-JSON-document-explorer/adddocument2.png)

	> [AZURE.NOTE] Обозреватель документов  в настоящее время поддерживает передачу до 100 документов JSON в одной операции передачи.

3. После завершения выбора нажмите кнопку **Передать**.  Документы автоматически добавляются в сетку обозревателя документов, а результаты передачи отображаются в ходе выполнения операции. Ошибки импорта выводятся для отдельных файлов.

	![Screenshot of Document Explorer bulk ingestion results](./media/documentdb-view-JSON-document-explorer/adddocument3.png)

4. После завершения операции можно выбрать до 100 других документов для передачи.

##<a name="NextSteps"></a>Дальнейшие действия

Для получения дополнительных сведений о DocumentDB щелкните [здесь](http://azure.com/docdb).

<!--HONumber=49-->