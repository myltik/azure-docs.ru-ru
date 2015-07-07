## Обзор метаданных приложения API

Метаданные, которые позволяют развертывание проекта веб-API как приложения API, содержатся в файле *apiapp.json* папки *Metadata*.

![](./media/app-service-api-review-metadata/metadatainse.png)

Содержимое файла *apiapp.json* по умолчанию выглядит примерно так:

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "ContactsList",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "ContactsList",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    }
		}

Обратите внимание на конечную точку `apiDefinition` `/swagger/docs/v1`. По умолчанию в проектах приложения API используется пакет NuGet [Swashbuckle](https://www.nuget.org/packages/Swashbuckle), который обеспечивает функцию автоматического создания метаданных [Swagger](http://swagger.io/).

Для этого учебника можно принять значения по умолчанию. В разделе [Метаданные приложения API](#api-app-metadata) ниже объясняется, как настроить эти метаданные.

<!---HONumber=62-->