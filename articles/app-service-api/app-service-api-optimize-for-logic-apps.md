
<properties
	pageTitle="Усовершенствование приложения API для приложений логики"
	description="В этой статье показано, как оснастить свое приложение API для безукоризненной работы с приложениями логики."
	services="app-service\api"
	documentationCenter=".net"
	authors="sameerch"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="sameerch;guayan;tarcher"/>

# Усовершенствование приложения API для приложений логики #

В этой статье вы узнаете, как задать определение API приложения API, чтобы оно правильно взаимодействовало с приложениями логики. Это улучшает взаимодействие с пользователем приложения API при работе с ним в конструкторе приложений логики.

## Предварительные требования

Если вы не знакомы с [приложениями API](app-service-api-apps-why-best-platform.md) в [службе приложений Azure](../app-service/app-service-value-prop-what-is.md), рекомендуем вам ознакомиться с циклом статей о [создании приложений API](app-service-dotnet-create-api-app.md).


## Добавление отображаемых имен ##
Конструктор приложений логики отображает имена операций, полей и параметров, поэтому время от времени их может быть неудобно читать, так как они создаются программно. Для повышения удобочитаемости конструктор приложений логики может, где это возможно, вместо имен операции, полей и параметров по умолчанию отображать более удобочитаемые текстовые значения, которые называются *отображаемыми именами*. Для этого конструктор приложений логики проверяет наличие определенных свойств в метаданных Swagger, предоставляемых приложением API. Для отображаемых имен используются следующие свойства:

* Операции (действие и триггеры): значение свойства **summary**, если оно имеется; в противном случае — значение свойства **operationId**. Обратите внимание, что спецификация Swagger 2.0 позволяет указать в свойстве **summary** до 120 знаков.

* Параметры (входные данные): значение свойства расширения **x-ms-summary**, если оно имеется; в противном случае — значение свойства **name**. Свойство расширения **x-ms-summary** должно задаваться в коде динамически. Этот процесс описан в подразделе "Использование настраиваемых атрибутов для аннотирования свойств расширения" данного раздела. Свойство **name** может быть задано с помощью комментариев ///. Этот процесс описан в подразделе "Использование комментариев XML при создании определения API" этого раздела.

* Поля схемы (выходные данные): значение свойства расширения **x-ms-summary**, если оно имеется; в противном случае — значение свойства **name**. Свойство расширения **x-ms-summary** должно задаваться в коде динамически. Этот процесс описан в подразделе "Использование настраиваемых атрибутов для аннотирования свойств расширения" данного раздела. Свойство **name** может быть задано с помощью комментариев ///. Этот процесс описан в подразделе "Использование комментариев XML при создании определения API" этого раздела.

**Примечание.** Рекомендуется придерживаться длины отображаемых имен до 30 знаков.

### Использование комментариев XML при создании определения API

При разработке приложений с помощью Visual Studio общепринято аннотировать контроллеры API с помощью [комментариев XML](https://msdn.microsoft.com/library/b2s063f7.aspx). При компиляции с параметром [/doc](https://msdn.microsoft.com/library/3260k4x7.aspx) компилятор создаст файл XML-документации. Набор инструментов Swashbuckle, входящий в состав пакета SDK для приложений API, может включить эти комментарии при формировании метаданных API, и вы можете настроить проект API для этого, выполнив следующее.

1. Откройте проект в Visual Studio.

2. В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Свойства**.

	![Свойства проекта](./media/app-service-api-optimize-for-logic-apps/project-properties.png)

3. Когда появятся страницы свойств проекта, выполните следующее.

	- Выберите **конфигурацию**, к которой применяются параметры. Как правило, следует выбрать все конфигурации, чтобы применить заданные параметры для сборок отладки и выпуска.
	
	- Выберите вкладку **Сборка** слева.
	
	- Убедитесь в том, что выбран параметр **Файл XML-документации**. Visual Studio предоставит имя файла по умолчанию, основанное на имени вашего проекта. Можно задать это значение, как того требует соглашение об именовании, или оставить его как есть.

	![Задание свойства документации XML](./media/app-service-api-optimize-for-logic-apps/xml-documentation-file-property.png)

4. Откройте файл *SwaggerConfig.cs* (находится в папке проекта **App_Start**).

5. Добавьте директивы **using** для пространств имен **System** и **System.Globalization** в начало файла *SwaggerConfig.cs*.

		using System;
		using System.Globalization;
 
6. В файле *SwaggerConfig.cs* найдите вызов **GetXmlCommentsPath** и отмените преобразование строки в комментарий, чтобы этот вызов выполнялся. Так как этот метод еще не реализован, Visual Studio подчеркнет вызов **GetXmlCommentsPath** и укажет, что он не определен в текущем контексте. Это нормально. Вы реализуете его на следующем шаге.

7. Добавьте следующую реализацию метода **GetXmlCommentsPath** в класс **SwaggerConfig** (определенный в файле *SwaggerConfig.cs*). Этот метод просто возвращает файл XML-документации, указанный ранее в параметрах проекта.

        public static string GetXmlCommentsPath()
        {
            return String.Format(CultureInfo.InvariantCulture, 
								 @"{0}\bin\ContactsList.xml", 
								 AppDomain.CurrentDomain.BaseDirectory);
        }

8. Наконец, укажите комментарии XML для методов контроллера. Для этого откройте один из файлов контроллера приложения API и введите /// в пустой строке над методом контроллера, который хотите описать. Visual Studio автоматически вставит преобразованный в комментарий раздел, в котором можно указать сводку по методу, а также информацию о параметрах и возвращаемых значениях.

Теперь при выполнении сборки и публикации приложения API вы увидите, что файл документации также находится в полезных данных и передается с остальной частью приложения API.

## Классификация дополнительных операций и свойств

Конструктор приложений логики имеет ограниченную площадь экрана для отображения операций, параметров и свойств. Кроме того, приложение API может определять обширный набор операций и свойств. Отображение такого большого количества данных в небольшой области может привести к тому, что пользователю будет сложно пользоваться конструктором.

Чтобы привести все в порядок, конструктор приложений логики позволяет группировать операции и свойства приложения API в настраиваемые категории. С помощью правильной классификации операций и свойств приложение API может улучшить взаимодействие с пользователем, предоставляя сначала самые основные и полезные операции и свойства.

Чтобы обеспечить эту возможность, конструктор приложений логики ищет наличие определенного настраиваемого свойства расширения поставщика в определении API Swagger приложения API. Это свойство называется **x-ms-visibility** и может принимать следующие значения:

* Пустое или none — эти операции и свойства готовы к просмотру пользователем.

* Advanced — так как эти операции и свойства являются дополнительными, они скрыты по умолчанию. Тем не менее пользователь может легко получить к ним доступ при необходимости.

* Internal — эти операции и свойства считаются системными или внутренними свойствами и не предназначены для непосредственного использования пользователем. По этой причине они скрыты в конструкторе и доступны только в представлении кода. Для таких свойств вы также можете указать свойство расширения **x-ms-scheduler-recommendation**, чтобы задавать их значения в конструкторе приложений логики. Например, см. статью о [добавлении триггеров в приложение API](app-service-api-dotnet-triggers.md).


## Использование настраиваемых атрибутов для аннотирования свойств расширения

Как упоминалось выше, настраиваемые свойства расширений поставщика используются для аннотирования метаданных API, чтобы предоставить более полную информацию для конструктора приложений логики. При использовании статических метаданных для описания приложения API можно изменить непосредственно файл */metadata/apiDefinition.swagger.json* проекта, чтобы вручную добавить необходимые свойства расширения.

В приложениях API, использующих динамические метаданные, для аннотирования кода вы можете использовать настраиваемые атрибуты. Затем можно определить фильтр операций в файле *SwaggerConfig.cs*, чтобы найти настраиваемые атрибуты и добавить необходимое расширение поставщика. Этот подход подробно описывается ниже для динамического формирования свойства расширения **x-ms-summary**.

1. Определите класс атрибута с именем **CustomSummaryAttribute**, который будет использоваться для аннотирования кода.

	    [AttributeUsage(AttributeTargets.All)]
	    public class CustomSummaryAttribute : Attribute
	    {
	        public string SummaryText { get; internal set; }

	        public CustomSummaryAttribute(string summaryText)
	        {
	            this.SummaryText = summaryText;
	        }
	    }

2. Определите фильтр операций **AddCustomSummaryFilter**, который будет искать этот настраиваемый атрибут в параметрах операций.


	    using Swashbuckle.Swagger;

		...

		public class AddCustomSummaryFilter : IOperationFilter
	    {
	        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
	        {
	            if (operation.parameters == null)
	            {
	                // no parameter
	                return;
	            }

	            foreach (var param in operation.parameters)
	            {
	                var summaryAttributes = apiDescription.ParameterDescriptions.First(x => x.Name.Equals(param.name))
	                                        .ParameterDescriptor.GetCustomAttributes<CustomSummaryAttribute>();

	                if (summaryAttributes != null && summaryAttributes.Count > 0)
	                {
	                    // add x-ms-summary extension
	                    if (param.vendorExtensions == null)
	                    {
	                        param.vendorExtensions = new Dictionary<string, object>();
	                    }
	                    param.vendorExtensions.Add("x-ms-summary", summaryAttributes[0].SummaryText);
	                }
	            }
	        }
	    }

3. Измените файл *SwaggerConfig.cs* и добавьте класс фильтра, определенный выше.


            GlobalConfiguration.Configuration
                .EnableSwagger(c =>
                    {
                        ...
                        c.OperationFilter<AddCustomSummaryFilter>();
                        ...
                    }

4. Используйте класс **CustomSummaryAttribute** для аннотирования кода, как показано в следующем фрагменте кода.

        /// <summary>
        /// Send Message
        /// </summary>
        /// <param name="queueName">The name of the Storage Queue</param>
        /// <param name="messageText">The message text to be sent</param>
        public void SendMessage(
            [CustomSummary("Queue Name")] string queueName,
            [CustomSummary("Message Text")] string messageText)
        {
             ...
        }

	При выполнении сборки приложения API, приведенного выше, будут сформированы следующие метаданные API:


			...
            "post": {
                ...
                "parameters": [
                    {
                        "name": "queueName",
                        "in": "query",
                        "description": "The name of the Storage Queue",
                        "required": true,
                        "x-ms-summary": "Queue Name",
                        "type": "string"
                    },
                    {
                        "name": "messageText",
                        "in": "query",
                        "description": "The message text to be sent",
                        "required": true,
                        "x-ms-summary": "Message Text",
                        "type": "string"
                    }
                ],
                ...

5. Аналогичным образом можно определить фильтр схемы **AddCustomSummarySchemaFilter**, чтобы автоматически аннотировать свойство расширения **x-ms-summary** для моделей схемы, как показано в следующем примере.

	    public class AddCustomSummarySchemaFilter: ISchemaFilter
	    {
	        public void Apply(Schema schema, SchemaRegistry schemaRegistry, Type type)
	        {
	            SetCustomSummary(schema, type.GetCustomAttribute<CustomSummaryAttribute>());

	            if (schema.properties != null)
	            {
	                foreach (var property in schema.properties)
	                {
	                    var summaryAttribute = type.GetProperty(property.Key).GetCustomAttribute<CustomSummaryAttribute>();
	                    SetCustomSummary(property.Value, summaryAttribute);
	                }
	            }
	        }

	        private static void SetCustomSummary(Schema schema, CustomSummaryAttribute summaryAttribute)
	        {
	            if (summaryAttribute != null)
	            {
	                if (schema.vendorExtensions == null)
	                {
	                    schema.vendorExtensions = new Dictionary<string, object>();
	                }
	                schema.vendorExtensions.Add("x-ms-summary", summaryAttribute.SummaryText);
	            }
	        }
	    }

## Сводка

В этой статье вы узнали, как улучшить взаимодействие с пользователем приложения API при его использовании в конструкторе приложений логики. В качестве лучшей методики рекомендуется предоставлять соответствующие понятные имена для всех операций (действий и триггеров), параметров и свойств. Также рекомендуется предоставлять не больше 5 основных операций. Для входных параметров рекомендуется ограничить число основных свойств до 4, а обычных свойств до 5. Остальные операции и свойства должны быть помечены как дополнительные.
 

<!---HONumber=July15_HO3-->