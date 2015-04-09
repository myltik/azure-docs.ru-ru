<properties 
	pageTitle="Создание веб-приложения Node.js с использованием DocumentDB" 
	description="В этом учебном курсе показано, как использовать службу Azure DocumentDB для хранения данных и обеспечения доступа к ним из приложения Java, размещенного на веб-сайтах Azure." 
	services="documentdb" 
	documentationCenter="java" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.devlang="java" 
	ms.topic="hero-article" 
	ms.tgt_pltfrm="NA" 
	ms.workload="data-services" 
	ms.date="03/23/2015" 
	ms.author="andrl"/>

# Создание веб-приложения Node.js с использованием DocumentDB #

В этом учебном курсе показано, как использовать службу [Microsoft Azure DocumentDB (предварительная версия)](https://portal.azure.com/#gallery/Microsoft.DocumentDB) для хранения данных и обеспечения доступа к ним из приложения Java, размещенного на веб-сайтах Azure. В этом разделе вы узнаете...

- Как собрать базовое приложение JSP в Eclipse.
- Как работать со службой Azure DocumentDB, используя [пакет SDK DocumentDB для Java](https://github.com/Azure/azure-documentdb-java).

В этом учебном курсе показано, как создать веб-приложение для управления задачами, которое позволяет создавать, получать и помечать задачи выполненными, как показано на рисунке ниже. Каждая задача в списке хранится в виде документов JSON в Azure DocumentDB.

![My ToDo List application](./media/documentdb-java-application/image1.png)

> [AZURE.TIP] В данном учебнике предполагается, что читатель имеет опыт использования Java. Если вы не знакомы с Java или [необходимыми средствами](#Prerequisites), то рекомендуется скачать готовый проект [todo](https://github.com/Azure/azure-documentdb-java/tree/master/tutorial/todo) с [GitHub](https://github.com/Azure/azure-documentdb-java) и собрать его с использованием [инструкций, приведенных в конце этого раздела](#GetProject). После сборки можно просмотреть этот раздел, чтобы получить подробные сведения о коде в контексте проекта.  

## <a id="Prerequisites"></a>Предварительные требования ##
Перед началом работы с этим учебником необходимо иметь следующее:

- Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](../../pricing/free-trial/).
- [Пакет средств разработки для Java (JDK) 7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
- [Среда разработки Eclipse IDE для разработчиков Java EE.](http://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunasr1)
- [Служба веб-сайтов Azure со средой выполнения Java (например, Tomcat или Jetty).](web-sites-java-get-started.md)

При первоначальной установке этих средств можно воспользоваться сайтом coreservlets.com, на котором представлена пошаговая процедура процесса установки в разделе "Быстрый запуск" учебника [Учебный курс. Установка TomCat7 и его использование с Eclipse](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html). 

## <a id="CreateDB"></a>Шаг 1. Создание учетной записи базы данных DocumentDB ##
Чтобы подготовить учетную запись базы данных DocumentDB в Azure:

1. Если отсутствует учетная запись базы данных, создайте ее, следуя инструкциям в разделе [Создание учетной записи базы данных](documentdb-create-account.md). Если учетная запись уже есть, перейдите к шагу 2.
2. Используя раздел **Ключи**, показанный на приведенном ниже рисунке, скопируйте **URI** и **первичный ключ** в буфер обмена и держите их под рукой. Эти значения будут использоваться в веб-приложении, которое будет создано в дальнейшем.

![Screen shot of the Azure Preview portal, showing a DocumentDB account, with the ACTIVE hub highlighted, the Keys button highlighted on the DocumentDB account blade, and the URI, PRIMARY KEY and SECONDARY KEY values highlighted on the Keys blade][1]


##<a id="CreateJSP"></a>Шаг 2. Создание приложения JSP ##

Для создания приложения JSP: 

1. Во-первых, начнем с создания проекта Java. Запустите Eclipse, нажмите **Файл**, **Создать**, а затем **Динамический веб-проект**. Если в списке доступных проектов **Динамический веб-проект** отсутствует, то сделайте следующее: нажмите **Файл**, **Создать**, **Проект**... разверните **Веб**, нажмите **Динамический веб-проект** и нажмите **Далее**. 

	![](./media/documentdb-java-application/image10.png)

2. В текстовом поле **Имя проекта** введите имя проекта, в раскрывающемся меню **Целевой объект среды выполнения** дополнительно можно выбрать значение (например, Apache Tomcat v7.0), а затем нажать **Готово**. Выбор целевого объекта среды выполнения позволяет запускать проект в Eclipse локально.
3. В Eclipse в представлении обозревателя проекта раскройте свой проект. Щелкните правой кнопкой мыши **WebContent**, выберите **Создать**, затем щелкните **JSP-файл**.
4. В диалоговом окне **Новый JSP-файл** введите имя файла **index.jsp**. Оставьте родительскую папку **WebContent**, как показано на следующем рисунке, а затем нажмите **Далее**.

	![](./media/documentdb-java-application/image11.png)

5. В диалоговом окне **Выбор шаблона JSP** для данного учебника выберите **Новый JSP-файл (html)**, а затем нажмите **Готово**.

6. После того как в Eclipse откроется файл index.jsp, добавьте текст для вывода **Hello World!** в существующий элемент <body>. Обновленное содержимое <body> должно выглядеть как следующий код.
    
	    <body>
	        <% out.println("Hello World!"); %>
	    </body>

8. Сохраните файл index.jsp. 
9. Если на шаге 2 был задан целевой объект среды выполнения, можно нажать **Проект** и затем **Запустить**, чтобы локально запустить приложение JSP.

	![](./media/documentdb-java-application/image12.png)

##<a id="InstallSDK"></a>Шаг 3. Установка пакета SDK DocumentDB для Java ##

Наиболее простой способ - получение пакета SDK DocumentDB для Java и его зависимостей через [Apache Maven](http://maven.apache.org/).

Чтобы сделать это, необходимо преобразовать проект в проект maven, выполнив следующие действия. 

1. Щелкните правой кнопкой мыши проект в обозревателе проектов, нажмите **Настройка**, щелкните **Преобразовать в проект Maven**.
2. В окне **Создать новый POM** примите значения по умолчанию и нажмите **Готово**.
3. Откройте в **обозревателе решений** файл pom.xml. 
4. На вкладке **Зависимости** в панели **Зависимости** нажмите **Добавить**.
4. В окне **Выбор зависимости** выполните следующие действия.
 - В поле **Идентификатор группы** введите "com.microsoft.azure".
 - В поле **Идентификатор артефакта** введите "azure-documentdb".
 - В поле **Версия** введите "0.9.0".

	![](./media/documentdb-java-application/image13.png)

	Либо добавьте зависимость XML для GroupId и ArtifactId непосредственно в pom.xml в текстовом редакторе:

	    <dependency>
		    <groupId>com.microsoft.azure</groupId>
		    <artifactId>azure-documentdb</artifactId>
		    <version>0.9.0</version>
	    </dependency>

5. Нажмите **ОК** и Maven установит пакет SDK DocumentDB для Java.
6. Сохраните файл pom.xml.

##<a id="UseService"></a>Шаг 4. Использование службы DocumentDB в приложении Java ##

1. Во-первых, необходимо определить объект TodoItem:

	    @Data
	    @Builder
	    public class TodoItem {
		    private String category;
		    private boolean complete;
		    private String id;
		    private String name;
	    }

	В этом проекте используется [проект Lombok](http://projectlombok.org/) для создания конструктора, методов получения, методов задания и построителя. Альтернационным способом можно написать следующий код вручную, либо сгенерировать его в интегрированной среде разработки.

2. Чтобы вызвать службу DocumentDB, необходимо создать экземпляр нового **DocumentClient**. В общем случае рекомендуется повторно использовать **DocumentClient** вместо создания нового клиента для каждого  последующего запроса. Клиент можно использовать повторно, обернув его в **DocumentClientFactory**. Там же необходимо вставить значения URI и первичного ключа, которые были сохранены в буфере обмена на [шаге 1](#CreateDB). Замените [YOUR\_ENDPOINT\_HERE] на URI и замените [YOUR\_KEY\_HERE] на первичный ключ.

	    private static final String HOST = "[YOUR_ENDPOINT_HERE]";
	    private static final String MASTER_KEY = "[YOUR_KEY_HERE]";
	
	    private static DocumentClient documentClient;
	
	    public static DocumentClient getDocumentClient() {
	        if (documentClient == null) {
	            documentClient = new DocumentClient(HOST, MASTER_KEY,
	                    ConnectionPolicy.GetDefault(), ConsistencyLevel.Session);
	        }
	
	        return documentClient;
	    }

3. Теперь давайте создадим объект доступа к данным (DAO) для абстрагирования сохранения элементов ToDo в DocumentDB.

	Для того чтобы сохранить элементы ToDo в коллекции, клиенту необходимо знать, в какую базу данных и коллекцию нужно сохранять (они ссылаются на сами элементы). Как правило, лучше всего кэшировать базы данных и коллекции, если это возможно для того, чтобы избежать дополнительных обращений к базе данных.

	В следующем коде показано, как получить базу данных и коллекцию, если они существуют, либо создать новые, если они не существуют.

		public class DocDbDao implements TodoDao {
		    // The name of our database.
		    private static final String DATABASE_ID = "TodoDB";
		
		    // The name of our collection.
		    private static final String COLLECTION_ID = "TodoCollection";
		
		    // The DocumentDB Client
		    private static DocumentClient documentClient = DocumentClientFactory
		            .getDocumentClient();
		
		    // Cache for the database object, so we don't have to query for it to
		    // retrieve self links.
		    private static Database databaseCache;
		
		    // Cache for the collection object, so we don't have to query for it to
		    // retrieve self links.
		    private static DocumentCollection collectionCache;
		
		    private Database getTodoDatabase() {
		        if (databaseCache == null) {
		            // Get the database if it exists
		            List<Database> databaseList = documentClient
		                    .queryDatabases(
		                            "SELECT * FROM root r WHERE r.id='" + DATABASE_ID
		                                    + "'", null).getQueryIterable().toList();
		
		            if (databaseList.size() > 0) {
		                // Cache the database object so we won't have to query for it
		                // later to retrieve the selfLink.
		                databaseCache = databaseList.get(0);
		            } else {
		                // Create the database if it doesn't exist.
		                try {
		                    Database databaseDefinition = new Database();
		                    databaseDefinition.setId(DATABASE_ID);
		
		                    databaseCache = documentClient.createDatabase(
		                            databaseDefinition, null).getResource();
		                } catch (DocumentClientException e) {
		                    // TODO: Something has gone terribly wrong - the app wasn't
		                    // able to query or create the collection.
		                    // Verify your connection, endpoint, and key.
		                    e.printStackTrace();
		                }
		            }
		        }
		
		        return databaseCache;
		    }
		
		    private DocumentCollection getTodoCollection() {
		        if (collectionCache == null) {
		            // Get the collection if it exists.
		            List<DocumentCollection> collectionList = documentClient
		                    .queryCollections(
		                            getTodoDatabase().getSelfLink(),
		                            "SELECT * FROM root r WHERE r.id='" + COLLECTION_ID
		                                    + "'", null).getQueryIterable().toList();
		
		            if (collectionList.size() > 0) {
		                // Cache the collection object so we won't have to query for it
		                // later to retrieve the selfLink.
		                collectionCache = collectionList.get(0);
		            } else {
		                // Create the collection if it doesn't exist.
		                try {
		                    DocumentCollection collectionDefinition = new DocumentCollection();
		                    collectionDefinition.setId(COLLECTION_ID);
		
		                    collectionCache = documentClient.createCollection(
		                            getTodoDatabase().getSelfLink(),
		                            collectionDefinition, null).getResource();
		                } catch (DocumentClientException e) {
		                    // TODO: Something has gone terribly wrong - the app wasn't
		                    // able to query or create the collection.
		                    // Verify your connection, endpoint, and key.
		                    e.printStackTrace();
		                }
		            }
		        }
		
		        return collectionCache;
		    }
		}

4. Следующим шагом является написание кода для сохранения элементов TodoItems в коллекции. В этом примере будет использован [Gson](https://code.google.com/p/google-gson/) для сериализации и десериализации традиционных объектов Java TodoItem (POJO) в документы JSON. [Jackson](http://jackson.codehaus.org/) или собственный сериализатор также прекрасно подходят для сериализации POJO.

	    // We'll use Gson for POJO <=> JSON serialization for this example.
	    private static Gson gson = new Gson();

	    @Override
	    public TodoItem createTodoItem(TodoItem todoItem) {
	        // Serialize the TodoItem as a JSON Document.
	        Document todoItemDocument = new Document(gson.toJson(todoItem));
	
	        // Annotate the document as a TodoItem for retrieval (so that we can
	        // store multiple entity types in the collection).
	        todoItemDocument.set("entityType", "todoItem");
	
	        try {
	            // Persist the document using the DocumentClient.
	            todoItemDocument = documentClient.createDocument(
	                    getTodoCollection().getSelfLink(), todoItemDocument, null,
	                    false).getResource();
	        } catch (DocumentClientException e) {
	            e.printStackTrace();
	            return null;
	        }
	
	        return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
	    }



5. Подобно используемых баз данных DocumentDB и коллекций, документы также связываются с помощью самостоятельных ссылок. Следующая вспомогательная функция позволяет получить документы с помощью другого атрибута (например, "идентификатора"), а не собственной ссылки.

	    private Document getDocumentById(String id) {
	        // Retrieve the document using the DocumentClient.
	        List<Document> documentList = documentClient
	                .queryDocuments(getTodoCollection().getSelfLink(),
	                        "SELECT * FROM root r WHERE r.id='" + id + "'", null)
	                .getQueryIterable().toList();
	
	        if (documentList.size() > 0) {
	            return documentList.get(0);
	        } else {
	            return null;
	        }
	    }

6. Для получения документа JSON TodoItem по идентификатору и его последующей десериализации в POJO можно воспользоваться вспомогательным методом из шага 5. 

	    @Override
	    public TodoItem readTodoItem(String id) {
	        // Retrieve the document by id using our helper method.
	        Document todoItemDocument = getDocumentById(id);
	
	        if (todoItemDocument != null) {
	            // De-serialize the document in to a TodoItem.
	            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
	        } else {
	            return null;
	        }
	    }

7. Можно также использовать DocumentClient для получения коллекции или перечня элементов TodoItems, используя DocumentDB SQL:
	
	    @Override
	    public List<TodoItem> readTodoItems() {
	        List<TodoItem> todoItems = new ArrayList<TodoItem>();
	
	        // Retrieve the TodoItem documents
	        List<Document> documentList = documentClient
	                .queryDocuments(getTodoCollection().getSelfLink(),
	                        "SELECT * FROM root r WHERE r.entityType = 'todoItem'",
	                        null).getQueryIterable().toList();
	
	        // De-serialize the documents in to TodoItems.
	        for (Document todoItemDocument : documentList) {
	            todoItems.add(gson.fromJson(todoItemDocument.toString(),
	                    TodoItem.class));
	        }
	
	        return todoItems;
	    }

8. Обновить документ с помощью DocumentClient можно несколькими способами. В приложении списка дел необходимо иметь возможность переключения между режимами в случае завершения TodoItem. Это достигается путем обновления атрибута complete (Завершено) в документе:
	
	    @Override
	    public TodoItem updateTodoItem(String id, boolean isComplete) {
	        // Retrieve the document from the database
	        Document todoItemDocument = getDocumentById(id);
	
	        // You can update the document as a JSON document directly.
	        // For more complex operations - you could de-serialize the document in
	        // to a POJO, update the POJO, and then re-serialize the POJO back in to
	        // a document.
	        todoItemDocument.set("complete", isComplete);
	
	        try {
	            // Persist/replace the updated document.
	            todoItemDocument = documentClient.replaceDocument(todoItemDocument,
	                    null).getResource();
	        } catch (DocumentClientException e) {
	            e.printStackTrace();
	            return null;
	        }
	
	        return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
	    }

9. Наконец мы хотим иметь возможность удалить элементы TodoItem из нашего списка. Чтобы сделать это, можно использовать вспомогательный метод, который был записан ранее, для получения ссылок и дать клиенту команду для удаления.
	
	    @Override
	    public boolean deleteTodoItem(String id) {
	        // DocumentDB refers to documents by self link rather than id.
	
	        // Query for the document to retrieve the self link.
	        Document todoItemDocument = getDocumentById(id);
	
	        try {
	            // Delete the document by self link.
	            documentClient.deleteDocument(todoItemDocument.getSelfLink(), null);
	        } catch (DocumentClientException e) {
	            e.printStackTrace();
	            return false;
	        }
	
	        return true;
	    }


##<a id="Wire"></a> Шаг 5. Связывание остальных частей приложения в единое целое ##

Теперь после завершения построения битов необходимо создать пользовательский интерфейс и привязать его к нашей DAO.

1. Во-первых, начнем с построения контроллера для вызова DAO.

		public class TodoItemController {
		    public static TodoItemController getInstance() {
		        if (todoItemController == null) {
		            todoItemController = new TodoItemController(TodoDaoFactory.getDao());
		        }
		        return todoItemController;
		    }
		
		    private static TodoItemController todoItemController;
		
		    private final TodoDao todoDao;
		
		    TodoItemController(TodoDao todoDao) {
		        this.todoDao = todoDao;
		    }
		
		    public TodoItem createTodoItem(@NonNull String name,
		            @NonNull String category, boolean isComplete) {
		        TodoItem todoItem = TodoItem.builder().name(name).category(category)
		                .complete(isComplete).build();
		        return todoDao.createTodoItem(todoItem);
		    }
		
		    public boolean deleteTodoItem(@NonNull String id) {
		        return todoDao.deleteTodoItem(id);
		    }
		
		    public TodoItem getTodoItemById(@NonNull String id) {
		        return todoDao.readTodoItem(id);
		    }
		
		    public List<TodoItem> getTodoItems() {
		        return todoDao.readTodoItems();
		    }
		
		    public TodoItem updateTodoItem(@NonNull String id, boolean isComplete) {
		        return todoDao.updateTodoItem(id, isComplete);
		    }
		}

	В более сложном приложении контроллер может содержать сложную бизнес-логику поверх DAO.

2. Затем создадим сервлет, который будет перенаправлять HTTP-запросы на контроллер.

		public class TodoServlet extends HttpServlet {
			// API Keys
			public static final String API_METHOD = "method";
		
			// API Methods
			public static final String CREATE_TODO_ITEM = "createTodoItem";
			public static final String GET_TODO_ITEMS = "getTodoItems";
			public static final String UPDATE_TODO_ITEM = "updateTodoItem";
		
			// API Parameters
			public static final String TODO_ITEM_ID = "todoItemId";
			public static final String TODO_ITEM_NAME = "todoItemName";
			public static final String TODO_ITEM_CATEGORY = "todoItemCategory";
			public static final String TODO_ITEM_COMPLETE = "todoItemComplete";
		
			public static final String MESSAGE_ERROR_INVALID_METHOD = "{'error': 'Invalid method'}";
		
			private static final long serialVersionUID = 1L;
			private static final Gson gson = new Gson();
		
			@Override
			protected void doGet(HttpServletRequest request,
					HttpServletResponse response) throws ServletException, IOException {
		
				String apiResponse = MESSAGE_ERROR_INVALID_METHOD;
		
				TodoItemController todoItemController = TodoItemController
						.getInstance();
		
				String id = request.getParameter(TODO_ITEM_ID);
				String name = request.getParameter(TODO_ITEM_NAME);
				String category = request.getParameter(TODO_ITEM_CATEGORY);
				boolean isComplete = StringUtils.equalsIgnoreCase("true",
						request.getParameter(TODO_ITEM_COMPLETE)) ? true : false;
		
				switch (request.getParameter(API_METHOD)) {
				case CREATE_TODO_ITEM:
					apiResponse = gson.toJson(todoItemController.createTodoItem(name,
							category, isComplete));
					break;
				case GET_TODO_ITEMS:
					apiResponse = gson.toJson(todoItemController.getTodoItems());
					break;
				case UPDATE_TODO_ITEM:
					apiResponse = gson.toJson(todoItemController.updateTodoItem(id,
							isComplete));
					break;
				default:
					break;
				}
		
				response.getWriter().println(apiResponse);
			}
	
			@Override
			protected void doPost(HttpServletRequest request,
					HttpServletResponse response) throws ServletException, IOException {
				doGet(request, response);
			}
		}

3. Для отображения информации пользователю понадобится веб-интерфейс пользователя. Повторно напишем файл index.jsp, созданный ранее:

		<html>
		<head>
		  <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
          <meta http-equiv="X-UA-Compatible" content="IE=edge;" />
		  <title>Azure DocumentDB Java Sample</title>
		
		  <!-- Bootstrap -->
		  <link href="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">
		
		  <style>
		    /* Add padding to body for fixed nav bar */
		    body {
		      padding-top: 50px;
		    }
		  </style>
		</head>
		<body>
		  <!-- Nav Bar -->
		  <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
		    <div class="container">
		      <div class="navbar-header">
		        <a class="navbar-brand" href="#">My Tasks</a>
		      </div>
		    </div>
		  </div>
		
		  <!-- Body -->
		  <div class="container">
		    <h1>My ToDo List</h1>
		
		    <hr/>
		
		    <!-- The ToDo List -->
		    <div class = "todoList">
		      <table class="table table-bordered table-striped" id="todoItems">
		        <thead>
		          <tr>
		            <th>Name</th>
		            <th>Category</th>
		            <th>Complete</th>
		          </tr>
		        </thead>
		        <tbody>
		        </tbody>
		      </table>
		
		      <!-- Update Button -->
		      <div class="todoUpdatePanel">
		        <form class="form-horizontal" role="form">
		          <button type="button" class="btn btn-primary">Update Tasks</button>
		        </form>
		      </div>
		
		    </div>
		
		    <hr/>
		
		    <!-- Item Input Form -->
		    <div class="todoForm">
		      <form class="form-horizontal" role="form">
		        <div class="form-group">
		          <label for="inputItemName" class="col-sm-2">Task Name</label>
		          <div class="col-sm-10">
		            <input type="text" class="form-control" id="inputItemName" placeholder="Enter name">
		          </div>
		        </div>
		
		        <div class="form-group">
		          <label for="inputItemCategory" class="col-sm-2">Task Category</label>
		          <div class="col-sm-10">
		            <input type="text" class="form-control" id="inputItemCategory" placeholder="Enter category">
		          </div>
		        </div>
		
		        <button type="button" class="btn btn-primary">Add Task</button>
		      </form>
		    </div>
		
		  </div>
		
		  <!-- Placed at the end of the document so the pages load faster -->
		  <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.js"></script>
		  <script src="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/bootstrap.min.js"></script>
		  <script src="assets/todo.js"></script>
		</body>
		</html>

4. И наконец, запишем несколько сценариев Javascript на стороне клиента, чтобы связать вместе веб-интерфейс и сервлет:
	
		var todoApp = {
		  /*
		   * API methods to call Java backend.
		   */
		  apiEndpoint: "api",
		
		  createTodoItem: function(name, category, isComplete) {
		    $.post(todoApp.apiEndpoint, {
		        "method": "createTodoItem",
		        "todoItemName": name,
		        "todoItemCategory": category,
		        "todoItemComplete": isComplete
		      },
		      function(data) {
		        var todoItem = data;
		        todoApp.addTodoItemToTable(todoItem.id, todoItem.name, todoItem.category, todoItem.complete);
		      },
		      "json");
		  },
		
		  getTodoItems: function() {
		    $.post(todoApp.apiEndpoint, {
		        "method": "getTodoItems"
		      },
		      function(data) {
		        var todoItemArr = data;
		        $.each(todoItemArr, function(index, value) {
		          todoApp.addTodoItemToTable(value.id, value.name, value.category, value.complete);
		        });
		      },
		      "json");
		  },
		
		  updateTodoItem: function(id, isComplete) {
		    $.post(todoApp.apiEndpoint, {
		        "method": "updateTodoItem",
		        "todoItemId": id,
		        "todoItemComplete": isComplete
		      },
		      function(data) {},
		      "json");
		  },
		
		  /*
		   * UI Methods
		   */
		  addTodoItemToTable: function(id, name, category, isComplete) {
		    var rowColor = isComplete ? "active" : "warning";
		
		    todoApp.ui_table().append($("<tr>")
		      .append($("<td>").text(name))
		      .append($("<td>").text(category))
		      .append($("<td>")
		        .append($("<input>")
		          .attr("type", "checkbox")
		          .attr("id", id)
		          .attr("checked", isComplete)
		          .attr("class", "isComplete")
		        ))
		      .addClass(rowColor)
		    );
		  },
		
		  /*
		   * UI Bindings
		   */
		  bindCreateButton: function() {
		    todoApp.ui_createButton().click(function() {
		      todoApp.createTodoItem(todoApp.ui_createNameInput().val(), todoApp.ui_createCategoryInput().val(), false);
		      todoApp.ui_createNameInput().val("");
		      todoApp.ui_createCategoryInput().val("");
		    });
		  },
		
		  bindUpdateButton: function() {
		    todoApp.ui_updateButton().click(function() {
		      // Disable button temporarily.
		      var myButton = $(this);
		      var originalText = myButton.text();
		      $(this).text("Updating...");
		      $(this).prop("disabled", true);
		
		      // Call api to update todo items.
		      $.each(todoApp.ui_updateId(), function(index, value) {
		        todoApp.updateTodoItem(value.name, value.value);
		        $(value).remove();
		      });
		
		      // Re-enable button.
		      setTimeout(function() {
		        myButton.prop("disabled", false);
		        myButton.text(originalText);
		      }, 500);
		    });
		  },
		
		  bindUpdateCheckboxes: function() {
		    todoApp.ui_table().on("click", ".isComplete", function(event) {
		      var checkboxElement = $(event.currentTarget);
		      var rowElement = $(event.currentTarget).parents('tr');
		      var id = checkboxElement.attr('id');
		      var isComplete = checkboxElement.is(':checked');
		
		      // Togle table row color
		      if (isComplete) {
		        rowElement.addClass("active");
		        rowElement.removeClass("warning");
		      } else {
		        rowElement.removeClass("active");
		        rowElement.addClass("warning");
		      }
		
		      // Update hidden inputs for update panel.
		      todoApp.ui_updateForm().children("input[name='" + id + "']").remove();
		
		      todoApp.ui_updateForm().append($("<input>")
		        .attr("type", "hidden")
		        .attr("class", "updateComplete")
		        .attr("name", id)
		        .attr("value", isComplete));
		
		    });
		  },
		
		  /*
		   * UI Elements
		   */
		  ui_createNameInput: function() {
		    return $(".todoForm #inputItemName");
		  },
		
		  ui_createCategoryInput: function() {
		    return $(".todoForm #inputItemCategory");
		  },
		
		  ui_createButton: function() {
		    return $(".todoForm button");
		  },
		
		  ui_table: function() {
		    return $(".todoList table tbody");
		  },
		
		  ui_updateButton: function() {
		    return $(".todoUpdatePanel button");
		  },
		
		  ui_updateForm: function() {
		    return $(".todoUpdatePanel form");
		  },
		
		  ui_updateId: function() {
		    return $(".todoUpdatePanel .updateComplete");
		  },
		
		  /*
		   * Install the TodoApp
		   */
		  install: function() {
		    todoApp.bindCreateButton();
		    todoApp.bindUpdateButton();
		    todoApp.bindUpdateCheckboxes();
		
		    todoApp.getTodoItems();
		  }
		};
		
		$(document).ready(function() {
		  todoApp.install();
		});

5. Отлично! Теперь осталось протестировать приложение. Локально запустите приложение и добавьте несколько элементов списка задач, заполнив имя элемента и категорию, а затем нажав **Добавить задачу**.

6. После появления элемента можно обновить его при выполнении, нажав флажок и затем **Обновить задачи**.

##<a id="Deploy"></a>Шаг 6. Разверните приложение на веб-сайтах Azure ##

Веб-сайты Azure упрощают развертывание приложений Java с помощью простого экспорта приложения в виде WAR-файла, либо с помощью загрузки через систему управления версиями (например, GIT) или через клиент FTP.

1. Для экспорта приложения в виде WAR-файла нажмите правой кнопкой мыши на проекте в **обозревателе проектов**, нажмите **Экспорт** и **WAR-файл**. 
2. В окне **Экспорт в WAR** выполните следующие действия.
 - В поле "Веб-проект" введите "azure-documentdb-java-sample".
 - В поле "Назначение" выберите папку для сохранения WAR-файла.
 - Нажмите кнопку **Готово**

3. Теперь, имея WAR-файл, можно просто передать его на веб-сайт Azure в каталог **webapps**. Инструкции по отправке файла см. в разделе [Добавление приложения на веб-сайт Java в Azure](web-sites-java-add-app.md).

	После загрузки WAR-файла в каталог веб-приложения среда выполнения обнаруживает, что вы добавили его и автоматически загрузит ее.
4. Чтобы просмотреть готовый проект, перейдите к http://YOUR\_SITE\_NAME.azurewebsites.net/azure-documentdb-java-sample/ и начните добавлять свои задачи!

##<a id="GetProject"></a>Получение проекта из GitHub##

Все примеры из этого учебника включены в проект [todo](https://github.com/Azure/azure-documentdb-java/tree/master/tutorial/todo) на GitHub, который является частью репозитория [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java). Чтобы импортировать проект todo в Eclipse, убедитесь, что у вас имеются программное обеспечение и ресурсы, перечисленные в разделе [Предварительные требования](#Prerequisites), а затем выполните следующие действия.

1. Установите [проект Lombok](http://projectlombok.org/). Lombok используется для создания конструкторов, методов получения, методов задания в проекте. После загрузки файла lombok.jar дважды щелкните на нем для установки либо установите его из командной строки. 
2. Если среда Eclipse открыта, закройте ее или перезапустите для загрузки Lombok.
3. В Eclipse в меню **Файл** нажмите **Импорт**.
4. В окне **Импорт** нажмите **Git**, **Проекты из Git**, а затем нажмите **Далее**. 
5. На экране **Выберите исходный репозиторий** нажмите **Клонировать URI**.
6. На экране **Исходный репозиторий Git** в поле **URI** введите https://github.com/Azure/azure-documentdb-java.git, а затем нажмите кнопку **Далее**.
7. На экране **Выбор ветки** проверьте, что выбрана ветка **master**, а затем нажмите **Далее**.
8. На экране **Локальное назначение** нажмите **Просмотр**, чтобы выбрать папку, куда будет скопирован репозиторий, а затем нажмите **Далее**.
9. На экране **Выбор мастера, используемого для импорта проектов** проверьте, что выбран пункт **Импортировать существующие проекты**, а затем нажмите **Далее**.
10. На экране **Импорт проектов** снимите выбор с проекта **DocumentDB**, а затем нажмите **Готово**. Проект DocumentDB содержит пакет SDK DocumentDB для Java, который мы добавим вместо зависимости.
11. В **обозревателе проекта** перейдите к azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java и замените значения HOST и MASTER_KEY значениями URI и первичного ключа для учетной записи DocumentDB, а затем сохраните файл. Дополнительные сведения см. в [шаге 1. Создание учетной записи базы данных DocumentDB](#CreateDB).
12. В **обозревателе проекта** щелкните правой кнопкой мыши **azure-documentdb-java-sample**, нажмите **Путь для сборки**, а затем нажмите **Настроить путь для сборки**.
13. На экране **Путь сборки Java** в панели справа выберите вкладку **Библиотеки**, а затем нажмите **Добавить внешние файлы JAR**. Перейдите к папке с файлом lombok.jar и нажмите **Открыть**, а затем нажмите **OK**.
14. Используйте шаг 12, чтобы снова открыть окно **Свойства**, а затем в панели слева выберите **Целевые объекты среды выполнения**.
15. На экране **Целевые объекты среды выполнения** нажмите **Создать**, выберите **Apache Tomcat v7.0**, а затем нажмите **OK**.
16. Используйте шаг 12, чтобы снова открыть окно **Свойства**, а затем в панели слева выберите **Аспекты проекта**.
17. На экране **Аспекты проекта** выберите **Динамический веб-модуль** и **Java**, а затем нажмите **OK**.
18. На вкладке **Серверы** в нижней части экрана щелкните правой кнопкой мыши **Tomcat v7.0 Server на localhost**, а затем нажмите **Добавить или удалить**.
19. В окне **Добавить или удалить** переместите **azure-documentdb-java-sample** в поле **Настроено**, а затем нажмите **Готово**. 
20. На вкладке **Серверы** щелкните правой кнопкой мыши **Tomcat v7.0 Server на localhost**, а затем нажмите **Перезапуск**.
21. В браузере перейдите по адресу http://localhost:8080/azure-documentdb-java-sample/ и начните добавлять задачи в список задач. Обратите внимание, что если было изменено значение порта по умолчанию, то нужно заменить 8080 выбранным значением.
22. Для развертывания проекта на веб-сайте Azure см. [шаг 6. Развертывание приложения на веб-сайтах Azure](#Deploy). 

[1]: ../includes/media/documentdb-keys/keys.png

<!--HONumber=49-->