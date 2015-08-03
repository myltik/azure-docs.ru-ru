<properties
   pageTitle="Создание нескольких экземпляров ресурса"
   description="В этом разделе описывается, как использовать операцию копирования в шаблоне диспетчера ресурсов Azure для выполнения нескольких итераций при развертывании ресурсов."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2015"
   ms.author="tomfitz"/>

# Создание нескольких экземпляров ресурсов в диспетчере ресурсов Azure

В этом разделе показано, как выполнить итерацию в шаблоне диспетчера ресурсов Azure для создания нескольких экземпляров ресурса.

## Объект copy и функция copyIndex()

Чтобы создать несколько экземпляров ресурса, можно определить объект **copy**, указывающий количество итераций. Объект copy имеет следующий формат:

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Вы можете получить доступ к текущему значению итерации с помощью функции **copyIndex()**, как показано ниже с использованием функции concat.

    [concat('examplecopy-', copyIndex())]

## Использование значения индекса в имени

Вы можете использовать операцию копирования для создания нескольких экземпляров ресурса, каждому из которых присвоено уникальное имя на основе увеличивающегося индекса. Например, вам может потребоваться добавить уникальный номер в конец имени каждого развертываемого ресурса. Чтобы развернуть три веб-сайта:

- examplecopy-0
- examplecopy-1
- examplecopy-2

используйте следующий шаблон.

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

## Смещение значения индекса

В предыдущем примере можно заметить, что значение индекса изменяется от нуля до 2. Для смещения значения индекса можно передать значение в функцию **copyIndex()**, например **copyIndex(1)**. Число выполняемых итераций по-прежнему указывается в элементе копирования, но значение copyIndex сдвигается на указанное значение. Таким образом, если использовать шаблон, аналогичный приведенному в предыдущем примере, но указать **copyIndex(1)**, будут развернуты три следующих веб-сайта:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## Использование с массивом
   
Эта операция копирования особенно удобна при работе с массивами, поскольку можно выполнить итерацию по каждому элементу в массиве. Чтобы развернуть три веб-сайта:

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

используйте следующий шаблон.

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      },
      "count": { 
         "type": "int", 
         "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

## Дальнейшие действия
- [Создание шаблонов диспетчера ресурсов Azure](./resource-group-authoring-templates.md)
- [Функции шаблонов в диспетчере ресурсов Azure](./resource-group-template-functions.md)
- [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](azure-portal/resource-group-template-deploy.md)

<!---HONumber=July15_HO4-->