## Общие сведения о шаблонах диспетчера ресурсов Azure

Шаблон Azure Resource Manager позволяет декларативно задать инфраструктуру IaaS Azure на языке JSON, установив зависимости между ресурсами. Более подробно шаблоны Azure Resource Manager рассматриваются в следующих статьях.

[Общие сведения о группе ресурсов](../articles/resource-group-overview.md)

## Фрагмент примера шаблона для расширений виртуальной машины
При развертывании расширений виртуальной машины в шаблоне Azure Resource Manager в нем необходимо декларативно задать конфигурацию расширения. Ниже приведен формат для указания конфигурации расширения.

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

Как видно из указанного выше, расширение шаблона содержит две основные части:

1. Имя, издатель и версия расширения.
2. Конфигурация расширения.

## Определение значений publisher, type и typeHandlerVersion для любого расширения

Расширения виртуальной машины Azure публикуются корпорацией Майкрософт и надежными сторонними издателями. Каждое расширение однозначно определяется по значениям publisher, type и typeHandlerVersion. Эти сведения можно определить следующим образом:

<!---HONumber=AcomDC_0330_2016-->