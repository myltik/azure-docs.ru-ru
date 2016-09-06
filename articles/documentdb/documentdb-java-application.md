.<properties
	pageTitle="Учебник по разработке приложений Java с использованием DocumentDB | Microsoft Azure"
	description="В этом руководстве показано, как использовать службу Azure DocumentDB для хранения данных и обеспечения доступа к ним из приложения Java, размещенного на веб-сайтах Azure."
	keywords="Разработка приложений, учебник по базе данных, приложение java, учебник по веб-приложениям java, documentdb, azure, Microsoft azure"
	services="documentdb"
	documentationCenter="java"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="mimig"/>

<tags
	ms.service="documentdb"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-services"
	ms.date="08/24/2016"
	ms.author="anhoh"/>

# Создание веб-приложения Node.js с использованием DocumentDB

> [AZURE.SELECTOR]
- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md)
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md)

В этом учебнике по разработке веб-приложения Java показано, как использовать службу [Microsoft Azure DocumentDB](https://portal.azure.com/#gallery/Microsoft.DocumentDB) для хранения данных и обеспечения к ним доступа из приложения Java, размещенного на веб-сайтах Azure. Из этой статьи вы узнаете:

- Как создать основное приложение JSP в Eclipse
- Как работать со службой Azure DocumentDB с помощью пакета [DocumentDB Java SDK](https://github.com/Azure/azure-documentdb-java).

В данном руководстве рассказывается о том, как создать веб-приложение для управления задачами, с помощью которого можно создавать и извлекать задачи, а также помечать их как завершенные, как показано на рисунке ниже. Каждая задача из списка ToDo будет храниться в виде документов JSON в службе Azure DocumentDB.

![Приложение My ToDo List на Java](./media/documentdb-java-application/image1.png)

> [AZURE.TIP] В данном учебнике по разработке приложения предполагается, что у вас имеется некоторый опыт использования Java. Если вы никогда не работали с Java или [необходимыми инструментами](#Prerequisites), рекомендуется загрузить полный учебный проект [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) с портала GitHub и создать его, следуя [инструкциям, приведенным в конце этой статьи](#GetProject). После создания ознакомьтесь со статьей, чтобы разобраться в коде этого проекта.

##<a id="Prerequisites"></a>Необходимые условия для изучения этого учебника по разработке веб-приложения Java
Для работы с этим учебником необходимы:

- Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).
- [Комплект разработчика Java (JDK 7 +)](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
- [Интегрированная среда разработки Eclipse для разработчиков Java EE.](http://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunasr1)
- [Открытый веб-сайт Azure со средой выполнения Java (например, Tomcat или Jetty).](../app-service-web/web-sites-java-get-started.md)

При первой установке этих средств воспользуйтесь пошаговым руководством по установке, представленным на сайте coreservlets.com в разделе «Быстрый запуск» статьи [Учебник по установке TomCat7 и его использованию с Eclipse](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html).

##<a id="CreateDB"></a>Шаг 1. Создание учетной записи базы данных DocumentDB

Начнем с создания учетной записи DocumentDB. Если у вас уже есть учетная запись, переходите сразу к [шагу 2 (создание приложения Java JSP)](#CreateJSP).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

##<a id="CreateJSP"></a>Шаг 2. Создание приложения Java JSP

Для создания приложения JSP:

1. Во-первых, необходимо создать проект Java. В меню Eclipse выберите **File** (Файл), щелкните **New** (Создать), затем выберите **Dynamic Web Project** (Динамический веб-проект). Если элемента **Dynamic Web Project** (Динамический веб-проект) нет в списке доступных проектов, откройте меню **File** (Файл), щелкните пункт **New** (Создать), затем выберите **Project** (Проект)…, разверните список **Web** (Интернет), выберите **Dynamic Web Project** (Динамический веб-проект) и нажмите кнопку **Next** (Далее).

	![Разработка приложений JSP Java](./media/documentdb-java-application/image10.png)

2. Введите имя проекта в поле **Project name** (Имя проекта) и в раскрывающемся меню **Target Runtime** (Целевая среда выполнения) и, если необходимо, выберите значение (например Apache Tomcat v7.0), а затем нажмите кнопку **Finish** (Готово). Выбор целевой среды выполнения позволит вам запустить локальный проект через Eclipse.
3. В обозревателе проектов Eclipse разверните проект. Щелкните правой кнопкой мыши **WebContent**, выберите **Создать**, затем щелкните **JSP-файл**.
4. В диалоговом окне **New JSP File** (Новый JSP-файл) укажите для файла имя **index.jsp**. Сохраните родительскую папку с именем **WebContent** (Веб-содержимое), как показано на следующей иллюстрации, и нажмите кнопку **Next** (Далее).

	![Создание файла JSP — учебник по разработке веб-приложений Java](./media/documentdb-java-application/image11.png)

5. В диалоговом окне **Select JSP Template** (Выберите шаблон JSP) в целях обучения выберите **New JSP File (html)** (Новый JSP-файл (html)) и нажмите кнопку **Finish** (Готово).

6. При открытии файла index.jsp в Eclipse добавьте текст для отображения **Hello World!** в существующий элемент <body>. Обновленное содержимое элемента <body> должно отображаться следующим образом:

	    <body>
	        <% out.println("Hello World!"); %>
	    </body>

8. Сохраните файл index.jsp.
9. Если вы задали целевую среду выполнения в рамках шага 2, можно щелкнуть **Project** (Проект), а затем **Run** (Запуск) для локального запуска приложения JSP:

	![Привет, мир! — учебник по разработке приложений Java](./media/documentdb-java-application/image12.png)

##<a id="InstallSDK"></a>Шаг 3. Установка пакета DocumentDB Java SDK ##

Самый простой способ извлечь данные из пакета DocumentDB Java SDK и его зависимости — использовать [Apache Maven](http://maven.apache.org/).

Для этого необходимо преобразовать проект в проект Maven, выполнив следующие действия.

1. Щелкните правой кнопкой мыши проект в обозревателе проектов, нажмите **Configure** (Настройка), щелкните **Convert to Maven Project** (Преобразовать в проект Maven).
2. В окне **Create new POM** (Создать новый POM) примите параметры по умолчанию и нажмите кнопку **Finish** (Готово).
3. В **обозревателе проектов** откройте файл pom.xml.
4. На вкладке **Dependencies** (Зависимости) в колонке **Dependencies** (Зависимости) щелкните **Add** (Добавить).
4. В окне **Select Dependency** (Выбор зависимости) выполните следующие действия.
 - В поле **GroupId** (Идентификатор группы) введите com.microsoft.azure.
 - В поле **Artifact Id** (Идентификатор артефакта) введите azure-documentdb.
 - В поле **Version** (Версия) введите 1.5.1.

	![Установка пакета DocumentDB Java Application SDK](./media/documentdb-java-application/image13.png)

	Либо добавьте зависимость XML для GroupId и ArtifactId непосредственно в pom.xml в текстовом редакторе:

	    <dependency>
		    <groupId>com.microsoft.azure</groupId>
		    <artifactId>azure-documentdb</artifactId>
		    <version>1.5.1</version>
	    </dependency>

5. После нажатия кнопки **Ok** Maven установит пакет DocumentDB Java SDK.
6. Сохраните файл pom.xml.

##<a id="UseService"></a>Шаг 4. Использование службы DocumentDB в приложении Java

1. Во-первых, необходимо определить объект TodoItem:

	    @Data
	    @Builder
	    public class TodoItem {
		    private String category;
		    private boolean complete;
		    private String id;
		    private String name;
	    }

	В этом проекте мы используем [Проект Lombok](http://projectlombok.org/) для создания конструктора, получателя, задания и построителя. Альтернационным способом можно написать следующий код вручную, либо сгенерировать его в IDE.

2. Чтобы вызвать службу DocumentDB, необходимо создать новый экземпляр ** DocumentClient**. В общем случае рекомендуется повторно использовать **DocumentClient** вместо создания нового клиента для каждого последующего запроса. Мы можем повторно использовать клиент, поместив его в **DocumentClientFactory**: Здесь же необходимо вставить универсальный код ресурса (URI) и ПЕРВИЧНЫЙ КЛЮЧ, сохраненные в буфер обмена в [шаге 1](#CreateDB). Замените [YOUR\_ENDPOINT\_HERE] на URI и замените [YOUR\_KEY\_HERE] на первичный ключ.

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

3. Теперь давайте создадим объект доступа к данным (DAO) для абстрагирования сохранения элементов ToDo в DocumentDB.

	Для сохранения элементов ToDo в коллекции клиент должен знать, какие базы данных и коллекции необходимо связать (с помощью самостоятельных ссылок). Как правило, лучше всего кэшировать базы данных и коллекции, если это возможно для того, чтобы избежать дополнительных обращений к базе данных.

	Следующий код иллюстрирует способ получения базы данных и коллекции, если он существует, или создать новый, если он не существует:

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

4. Следующим шагом является написание кода для сохранения элементов TodoItems в коллекции. В этом примере мы будем использовать [Gson](https://code.google.com/p/google-gson/) для сериализации и десериализации элементов TodoItem POJO в документы JSON. [Jackson](http://jackson.codehaus.org/) или собственные сигнализаторы являются отличной альтернативой для социализации элементов POJO.

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



5. Подобно используемых баз данных DocumentDB и коллекций, документы также связываются с помощью самостоятельных ссылок. Следующая вспомогательная функция позволит получать документы с определенным атрибутом (например, id) без использования самостоятельных ссылок:

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

6. Указанный в шаге 5 вспомогательный метод можно использовать для извлечения документа TodoItem JSON по идентификатору и затем десериализовать его в POJO:

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

8. Существует множество способов обновления документа с помощью DocumentClient. В приложении списка дел необходимо иметь возможность переключения между режимами в случае завершения TodoItem. Это достигается путем обновления атрибута "complete" (Завершено) в документе:

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


##<a id="Wire"></a>Шаг 5. Подключение другой части проекта по разработке приложений Java

Теперь после завершения построения битов необходимо создать пользовательский интерфейс и привязать его к нашей DAO.

1. Во-первых, необходимо начать с построения контроллера для вызова DAO:

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

	В более сложных приложениях контроллер может включать сложные бизнес-логики в верхней части DAO.

2. Далее мы создадим сервлет, чтобы перенаправлять запросы HTTP к контроллеру:

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

3. Нам потребуется веб-интерфейс, который будет отображаться для пользователя. Повторно напишем файл index.jsp, созданный ранее:

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

		      // Toggle table row color
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

5. Отлично! Теперь осталось протестировать приложение. Запустите приложение локально и добавьте несколько элементов Todo, заполнив имя элемента и категории и нажав кнопку **Add Task** (Добавить задачу).

6. Как только элемент появится, вы можете проверить его завершение, отметив флажком и щелкнув команду **Update Tasks** (Обновить задачи).

##<a id="Deploy"></a>Шаг 6. Развертывание приложения Java на веб-сайтах Azure

Веб-сайты Azure упрощают развертывание приложений Java с помощью простого экспорта приложения в виде WAR-файла, либо с помощью загрузки через систему управления версиями (например, GIT) или через клиент FTP.

1. Чтобы экспортировать приложение в виде WAR-файла, правой кнопкой мыши щелкните проект в **обозревателе проектов**, выберите команду **Export** (Экспорт) и щелкните **WAR File** (WAR-файл).
2. В окне **WAR Export** (Экспорт WAR-файла) выполните следующие действия:
 - В поле веб-проекта введите azure-documentdb-java-sample.
 - В поле Destination (Назначение) выберите место назначения для сохранения WAR-файла.
 - Нажмите кнопку **Готово**

3. Теперь, имея WAR-файл, можно просто передать его на веб-сайт Azure в каталог **webapps** (Веб-приложения). Инструкции по передаче файла см. в разделе [Добавление приложения на веб-сайт Java в Azure](../app-service-web/web-sites-java-add-app.md).

	После загрузки WAR-файла в каталог веб-приложения среда выполнения обнаруживает, что вы добавили его и автоматически загрузит ее.
4. Для просмотра вашего готового продукта перейдите на страницу http://YOUR\_SITE\_NAME.azurewebsites.net/azure-documentdb-java-sample/ и начните добавлять задачи.

##<a id="GetProject"></a>Получение проекта из GitHub

Все примеры в этом учебнике включены в проект [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) на портале GitHub. Чтобы импортировать проект todo в Eclipse, убедитесь, что у вас есть программное обеспечение и ресурсы, перечисленные в разделе [Необходимые условия](#Prerequisites), а затем выполните следующие действия.

1. Установите [проект Lombok](http://projectlombok.org/). Lombok используется для формирования конструкторов, получателей и заданий в проекте. Дважды щелкните загруженный файл lombok.jar для установки или установите его из командной строки.
2. Если открыта рабочая область Eclipse, закройте ее и запустите снова для загрузки проекта Lombok.
3. В Eclipse откройте меню **File** (Файл) и щелкните **Import** (Импорт).
4. В окне **Import** (Импорт) щелкните **Git**, затем выберите **Projects from Git** (Проекты из Git) и нажмите кнопку **Next** (Далее).
5. На экране **Select Repository Source** (Выбрать источник репозитория) щелкните **Clone URI** (Клон URI).
6. На экране **Source Git Repository** (Исходный репозиторий Git) в поле **URI** введите https://github.com/Azure-Samples/documentdb-java-todo-app.git и нажмите кнопку **Next** (Далее).
7. На экране **Branch Selection** (Выбор ветви) выберите значение **master** и нажмите кнопку **Next** (Далее).
8. На экране **Local Destination** (Локальная папка назначения) нажмите кнопку **Browse** (Обзор), выберите папку, в которую можно копировать репозитории, и нажмите кнопку **Next** (Далее).
9. На экране **Select a wizard to use for importing projects** (Выбор мастера для импорта проектов) убедитесь, что установлен флажок **Import existing projects** (Импортировать существующие проекты) и нажмите кнопку **Next** (Далее).
10. На экране **Import Projects** (Импорт проектов) снимите флажок возле проекта **DocumentDB** и нажмите кнопку **Finish** (Далее). В проекте DocumentDB содержится пакет DocumentDB Java SDK, который будет добавлен в качестве зависимости.
11. В **обозревателе проектов** перейдите в расположение azure-documentdb-java-sample\\src\\com.microsoft.azure.documentdb.sample.dao\\DocumentClientFactory.java и замените значения в полях HOST (Узел) и MASTER\_KEY (Основной ключ) значениями URI и ПЕРВИЧНОГО КЛЮЧА для вашей учетной записи DocumentDB, а затем сохраните файл. Дополнительные сведения см. в инструкции [Шаг 1. Создание учетной записи базы данных DocumentDB](#CreateDB).
12. В **обозревателе проектов** правой кнопкой мыши щелкните элемент **azure-documentdb-java-sample**, затем выберите **Build Path** (Построить путь) и щелкните **Configure Build Path** (Настроить построение пути).
13. На экране **Java Build Path** (Путь построения Java) в правой области откройте вкладку **Libraries** (Библиотеки) и щелкните **Add External JAR** (Добавить внешние JAR-файлы). Перейдите в расположение файла lombok.jar, щелкните **Open** (Открыть), а затем нажмите кнопку **ОК** (ОК).
14. С помощью инструкции из шага 12 снова откройте окно **Properties** (Свойства), а затем в левой области щелкните **Targeted Runtimes** (Целевые среды выполнения).
15. На экране **Targeted Runtimes** (Целевые среды выполнения) щелкните **New** (Создать), выберите **Apache Tomcat v7.0** и нажмите кнопку **ОК**.
16. С помощью инструкции из шага 12 снова откройте окно **Properties** (Свойства), а затем в левой области щелкните **Project Facets** (Аспекты проекта).
17. На экране **Project Facets** (Аспекты проекта) выберите **Dynamic Web Module** (Динамический веб-модуль) и **Java**, а затем нажмите кнопку **ОК**.
18. На вкладке **Servers** (Серверы) в нижней части экрана правой кнопкой мыши щелкните **Tomcat v7.0 Server at localhost** (Tomcat v7.0 Server на localhost), а затем щелкните **Add and Remove** (Добавление и удаление).
19. В окне **Add and Remove** (Добавление и удаление) переместите элемент **azure-documentdb-java-sample** в поле **Configured** (Настроено), а затем нажмите кнопку **Finish** (Готово).
20. На вкладке **Server** (Сервер) правой кнопкой мыши щелкните параметр **Tomcat v7.0 Server at localhost** (Tomcat v7.0 Server на localhost), а затем нажмите кнопку **Restart** (Перезагрузить).
21. В браузере перейдите по ссылке http://localhost:8080/azure-documentdb-java-sample/ и начните добавлять элементы в список задач. Обратите внимание, что в случае изменения заданных по умолчанию значений портов необходимо изменить значение 8080 выбранным вами значением.
22. Инструкции по развертыванию проекта на веб-сайтах Azure см. в пункте [Шаг 6. Развертывание приложений на веб-сайтах Azure](#Deploy).

[1]: media/documentdb-java-application/keys.png

<!---HONumber=AcomDC_0831_2016-->