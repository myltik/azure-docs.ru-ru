<properties
   pageTitle="Использование шаблонов для создания пользовательских сценариев на виртуальных машинах | Microsoft Azure"
   description="Автоматизируйте процесс настройки виртуальных машин Azure с Windows и Linux с помощью расширения Custom Script на основе шаблонов диспетчера ресурсов"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="07/01/2015"
   ms.author="kundanap"/>

# Использование расширения Custom Script с шаблонами диспетчера ресурсов Azure

В этой статье описывается процесс создания шаблонов диспетчера ресурсов Azure с помощью расширения Custom Script для начальной загрузки заданий на виртуальную машину Linux или Windows.

Общие сведения о расширении Custom Script см. в [этой статье](virtual-machines-extensions-customscript.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-extensions-customscript.md).

Расширение Custom Script широко применяется для конфигурации рабочей нагрузки на виртуальных машинах Windows и Linux с момента своего появления. С введением шаблонов диспетчера ресурсов Azure пользователи получили возможность создавать такие шаблоны, которые не только инициализируют виртуальную машину, но и управляют ее рабочей нагрузкой.

## Общие сведения о шаблонах диспетчера ресурсов Azure

Шаблон диспетчера ресурсов Azure позволяет декларативно задать IaaS-инфраструктуру Azure на языке JSON, установив зависимости между ресурсами. Более подробно шаблоны диспетчера ресурсов Azure рассматриваются в следующих статьях.

<a href="https://azure.microsoft.com/ru-RU/documentation/articles/resource-group-overview/" target="_blank">Общие сведения о группе ресурсов</a>. <br/> <a href="https://azure.microsoft.com/ru-RU/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli/" target="_blank">Развертывание шаблонов с помощью интерфейса командной строки Azure</a>. <br/> <a href="https://azure.microsoft.com/ru-RU/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/" target="_blank">Развертывание шаблонов с помощью Azure Powershell</a>.

### Предварительные требования для запуска расширения Custom Script

1. Установите последние командлеты Azure PowerShell или интерфейса командной строки Azure, которые можно скачать <a href="http://azure.microsoft.com/downloads" target="_blank">здесь</a>.
2. Если сценарии будут запускаться на существующей виртуальной машине, убедитесь, что на ней включен агент виртуальной машины. Если агент не включен, установите его, следуя указаниям в этой <a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">статье</a>.
3. Передайте сценарии, которые требуется запускать на виртуальной машине, в службу хранилища Azure. Сценарии могут поступать как из одного, так и из нескольких контейнеров хранилища.
4. Кроме того, скрипты можно загрузить в учетную запись Github.
5. Сценарий должен быть создан таким образом, чтобы запущенный с помощью расширения первый сценарий запускал в свою очередь другие сценарии.

## Общие сведения об использовании расширения Custom Script с шаблонами

Для развертывания с помощью шаблонов используется та же версия расширения Custom Script, которая доступна для API управления службами Azure. Это расширение поддерживает такие же параметры и сценарии, например добавление файлов в учетную запись хранения Azure или в расположение Github. Основное отличие при использовании с шаблонами — это необходимость указывать версию расширения в ее непосредственном виде, а не в формате основной\_номер\_версии.*.

 ## Фрагмент шаблона для расширения Custom Script на виртуальной машине Linux

В разделе Resource шаблона определите следующий ресурс расширения:

      {
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "MyCustomScriptExtension",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
    "properties":
    {
      "publisher": "Microsoft.OSTCExtensions",
      "type": "CustomScriptForLinux",
      "typeHandlerVersion": "1.2",
      "settings": {
      "fileUris": [ "https: //raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-ubuntu/mongo-install-ubuntu.sh                        ],
      "commandToExecute": "shmongo-install-ubuntu.sh"
      }
    }
    }

## Фрагмент шаблона для расширения Custom Script на виртуальной машине Windows

В разделе Resource шаблона определите следующий ресурс:

       {
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "MyCustomScriptExtension",
       "apiVersion": "2015-05-01-preview",
       "location": "[parameters('location')]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.Compute",
           "type": "CustomScriptExtension",
           "typeHandlerVersion": "1.4",
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }

В приведенных выше примерах замените URL-адрес и имя файла на собственные значения.

Развернуть созданный шаблон можно с помощью интерфейса командной строки Azure или Azure Powershell.

Полные примеры настройки приложения на виртуальной машине с помощью расширения Custom Script вы найдете в следующих примерах.

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">Расширение Custom Script на виртуальной машине Linux</a>. </br> <a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">Расширение Custom Script на виртуальной машине Windows</a>.

<!---HONumber=Oct15_HO3-->