<properties
   pageTitle="Устранение сбоев в расширениях виртуальной машины Azure | Microsoft Azure"
   description="Узнайте об устранении сбоев в расширениях виртуальной машины Azure."
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Устранение сбоев в расширениях виртуальных машин Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.


## Общие сведения о шаблонах диспетчере ресурсов Azure

Шаблон диспетчера ресурсов Azure позволяет декларативно задать IaaS-инфраструктуру Azure на языке JSON, установив зависимости между ресурсами.


Откройте статью [Создание шаблонов расширений](virtual-machines-extensions-authoring-templates.md), чтобы получить дополнительную информацию о создании шаблонов для использования расширений.

В этой статье вы узнаете, как устранять некоторые распространенные сбои в расширениях виртуальных машин.

## Просмотр состояния расширений
Шаблоны диспетчера ресурсов Azure могут выполняться из Azure Powershell или Azure CLI. После выполнения шаблона состояние расширения можно узнать в обозревателе ресурсов Azure или с помощью средств командной строки. Ниже приведены некоторые примеры:

Azure CLI:

      azure vm get-instance-view

Azure Powershell:

      Get-AzureVM -ResourceGroupName $RGName -Name $vmName -Status

Пример выходных данных:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## Устранение сбоев в расширениях

### Повторное выполнение расширения на виртуальной машине

При выполнении скриптов на виртуальной машине с помощью расширения Custom Script может возникать ошибка, указывающая на то, что виртуальная машина создана успешно, но скрипт не выполнен. В этом случае рекомендуется удалить соответствующее расширение и выполнить шаблон еще раз. Примечание. В будущем эта функция будет усовершенствована, что позволит устранить необходимость в удалении расширения.

#### Удаление расширения из Azure CLI

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

В данном коде publisher-name соответствует типу расширения из выходных данных azure vm get-instance-view, а имя представляет собой имя ресурса расширения из шаблона.

#### Удаление расширения из Azure Powershell

    Remove-AzureVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

После удаления расширения шаблон можно выполнить повторно, чтобы запустить скрипты на виртуальной машине.

<!---HONumber=Oct15_HO3-->