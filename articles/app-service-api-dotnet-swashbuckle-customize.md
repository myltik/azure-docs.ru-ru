
<properties 
	pageTitle="Настройка идентификаторов операций, созданных с помощью Swashbuckle" 
	description="Узнайте, как настроить идентификаторы операций Swagger, созданные с помощью Swashbuckle, для приложения API в службе приложений Azure." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/16/2015" 
	ms.author="bradyg"/>

# Настройка идентификаторов операций, созданных с помощью Swashbuckle 

## Обзор

Swashbuckle создает идентификаторы операций Swagger, объединяя имя контроллера и метода. Использование такого шаблона влечет возникновение проблемы при наличии нескольких перегрузок метода. Swashbuckle создает повторяющиеся идентификаторы операции, которые являются недопустимым файлом Swagger JSON.

Например, следующий код контроллера приводит к тому, что Swashbuckle создает три идентификатора операции Contact_Get.

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

Проблему можно решить вручную, присвоив методам уникальные имена как в примере:

* Получить
* GetById;
* GetPage.

Альтернативным вариантом является расширение Swashbuckle, чтобы он автоматически создавал уникальные идентификаторы операций. В этой статье показано, как это сделать.

## Расширение Swashbuckle 

В следующих шагах показано, как настроить Swashbuckle используя файл *SwaggerConfig.cs*, который включен в проект с помощью шаблона проектов предварительной версии приложения API Visual Studio. Кроме того, Swashbuckle можно настроить в проекте веб-API, настроенного для развертывания в качестве приложения API.

1. Создайте пользовательскую реализацию `IOperationFilter` 

	Интерфейс `IOperationFilter` предоставляет точку расширения для пользователей Swashbuckle, которым нужно настроить различные параметры обработки метаданных Swagger. В следующем коде показан один из способов изменения поведения при создании идентификатора операции. Код добавляет имена параметров к имени идентификатора операции.

		using Swashbuckle.Swagger;
		using System.Web.Http.Description;
		
		namespace ContactsList
		{
		    public class MultipleOperationsWithSameVerbFilter : IOperationFilter
		    {
		        public void Apply(
		            Operation operation,
		            SchemaRegistry schemaRegistry,
		            ApiDescription apiDescription)
		        {
		            if (operation.parameters != null)
		            {
		                operation.operationId += "By";
		                foreach (var parm in operation.parameters)
		                {
		                    operation.operationId += string.Format("{0}",parm.name);
		                }
		            }
		        }
		    }
		}

2. В файле *App_Start\SwaggerConfig.cs* вызовите метод `OperationFilter`, чтобы библиотека Swashbuckle начала использовать новую реализацию `IOperationFilter`.

		c.OperationFilter<MultipleOperationsWithSameVerbFilter>();

	![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)

	Файл *SwaggerConfig.cs*, размещенный в пакете NuGet Swashbuckle, содержит множество закомментированных примеров точек расширения. Здесь не указаны дополнительные комментарии.

	После внесения этого изменения будет использоваться ваша реализация `IOperationFilter`, в результате чего будут созданы уникальные идентификаторов операции.
 
	![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

## Дальнейшие действия

В этой статье показано, как настроить библиотеку Swashbuckle таким образом, чтобы она создавала уникальные идентификаторы операции. Дополнительную информацию см. в статье [«Swashbuckle» на GitHub](https://github.com/domaindrivendev/Swashbuckle).

<!---HONumber=58-->