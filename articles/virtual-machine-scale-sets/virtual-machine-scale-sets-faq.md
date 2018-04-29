---
title: Часто задаваемые вопросы о масштабируемых наборах виртуальных машин Azure | Документация Майкрософт
description: Ответы на часто задаваемые вопросы о масштабируемых наборах виртуальных машин.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: negat
ms.custom: na
ms.openlocfilehash: c161b8fb70f20ef7d82834e6c61daff759726b93
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Часто задаваемые вопросы о масштабируемых наборах виртуальных машин Azure

Ответы на часто задаваемые вопросы о масштабируемых наборах виртуальных машин в Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Наиболее часто задаваемые вопросы о масштабируемых наборах
**Вопрос.** Сколько виртуальных машин может входить в масштабируемый набор?

**Ответ.** В масштабируемый набор может входить от 0 до 1000 виртуальных машин на базе образов платформы или от 0 до 300 виртуальных машин на базе пользовательских образов. 

**Вопрос.** Поддерживаются ли диски данных в масштабируемых наборах?

**Ответ.** Да. Масштабируемый набор может определять конфигурацию подключенных дисков с данными, применяемую ко всем виртуальным машинам в наборе. Дополнительные сведения см. в статье [Масштабируемые наборы ВМ Azure и подключенные диски данных](virtual-machine-scale-sets-attached-disks.md). Ниже перечислены другие варианты хранения данных.

* Файлы Azure (общие диски SMB).
* Диск операционной системы.
* временный диск (локальный, не поддерживаемый хранилищем Azure);
* Служба данных Azure (например, таблицы Azure, большие двоичные объекты Azure).
* внешняя служба данных (например, удаленная база данных).

**Вопрос.** Какие регионы Azure поддерживают масштабируемые наборы?

**Ответ.** Все регионы Azure поддерживают масштабируемые наборы.

**Вопрос.** Как создать масштабируемый набор с помощью пользовательского образа?

**Ответ.** Создайте управляемый диск на основе VHD-файла пользовательского образа и добавьте ссылку на него в шаблоне масштабируемого набора. [Ниже приведен пример](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os).

**Вопрос.** Если уменьшить емкость масштабируемого набора с 20 до 15, какие виртуальные машины будут удалены?

**Ответ.** Для максимальной доступности виртуальные машины удаляются из масштабируемого набора в доменах обновления и доменах сбоя равномерно. Сначала удаляются виртуальные машины с самым высоким уровнем идентификатора.

**Вопрос.** Что будет, если затем увеличить емкость с 15 до 18?

**Ответ.** Если увеличить емкость до 18, то будут созданы 3 новые виртуальные машины. При этом идентификатор экземпляра виртуальной машины будет каждый раз увеличиваться, начиная с предыдущего наибольшего значения (например, 20, 21, 22). Виртуальные машины распределяются между доменами сбоя и обновления.

**Вопрос.** Можно ли указать последовательность выполнения при использовании нескольких расширений в масштабируемом наборе?

**Ответ.** Напрямую нет, но при использовании расширения customScript ваш скрипт может подождать, пока завершится выполнение другого расширения. Дополнительные рекомендации по виртуализации расширения см. в записи блога [Extension Sequencing in Azure virtual machine scale sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/) (Последовательность расширений в масштабируемых наборах виртуальных машин Azure).

**Вопрос.** Работают ли масштабируемые наборы с группами доступности Azure?

**Ответ.** Региональный (не зональный) масштабируемый набор использует *группы размещения*, каждую из которых можно настроить в качестве неявной группы доступности с пятью доменами сбоя и пятью доменами обновления. Масштабируемые наборы, содержащие более 100 виртуальных машин, охватывают несколько групп размещения. Дополнительные сведения о группах размещения см. в статье [Работа с крупными масштабируемыми наборами виртуальных машин](virtual-machine-scale-sets-placement-groups.md). Группа доступности виртуальных машин может находиться в той же виртуальной сети, что и набор масштабирования виртуальных машин. Типичная конфигурация — это такое размещение виртуальных машин узла управления, для которого часто требуется уникальная конфигурация в группе доступности и узлы данных в наборе масштабирования.

**Вопрос.** Работают ли масштабируемые наборы с зонами доступности Azure?

**Ответ.** Да! Дополнительные сведения см. в [документации по зонам масштабируемых наборов](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Autoscale

### <a name="what-are-best-practices-for-azure-autoscale"></a>Существуют ли рекомендации по автомасштабированию Azure?

Рекомендации по автомасштабированию виртуальных компонентов см. в [этой статье](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Где можно найти имена метрик на основе узла, используемые в процессе автомасштабирования?

Имена метрик на основе узла, используемые в процессе автомасштабирования, перечислены в статье [Метрики, поддерживаемые Azure Monitor](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Существуют ли какие-либо образцы автомасштабирования на основе раздела или длины очереди служебной шины Azure?

Да. Образцы автомасштабирования на основе раздела или длины очереди служебной шины Azure см. в статье [Общие метрики автомасштабирования Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

Для очереди служебной шины используйте следующий код JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Для очереди хранилища используйте следующий код JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Замените примеры значений соответствующими универсальными кодами ресурсов (URI).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Следует ли выполнять автомасштабирование с использованием метрик на основе узла или с применением расширения диагностики?

Вы можете создать на виртуальной машине конфигурацию автомасштабирования с использованием метрик уровня узла или метрик на основе гостевой ОС.

Список поддерживаемых метрик см. в статье [Общие метрики автомасштабирования Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics). 

Полное определение конфигурации масштабируемых наборов см. в статье [Расширенная настройка автомасштабирования с помощью шаблонов Resource Manager для масштабируемых наборов виртуальных машин](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets). 

В этой конфигурации используется метрика ЦП уровня узла и метрика количества сообщений.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Как можно задать правила оповещений в масштабируемом наборе виртуальных машин?

Оповещения на основе метрик в масштабируемых наборах виртуальных машин можно создать с помощью PowerShell или Azure CLI. Дополнительные сведения см. в статье [Примеры для быстрого запуска Azure Monitor с помощью PowerShell](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) и разделе [Работа с оповещениями](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

Идентификатор целевого ресурса масштабируемого набора виртуальных машин выглядит следующим образом: 

/subscriptions/идентификатор_подписки/resourceGroups/группа_ресурсов/providers/Microsoft.Compute/virtualMachineScaleSets/имя_набора.

В качестве метрики, для которой будут устанавливаться оповещения, можно выбрать любой счетчик производительности виртуальной машины. Дополнительные сведения см. в разделе [Метрики гостевой ОС для виртуальных машин под управлением Windows, развернутых с помощью Resource Manager](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) и [Метрики гостевой ОС для виртуальных машин под управлением Linux](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) статьи [Общие метрики автомасштабирования Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Как можно задать параметры автомасштабирования в масштабируемом наборе виртуальных машин с помощью PowerShell?

Дополнительные сведения о настройке параметров автомасштабирования в масштабируемом наборе виртуальных машин с помощью PowerShell см. в [этой записи блога](https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/).




## <a name="certificates"></a>Сертификаты

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm-how-do-i-provision-a-virtual-machine-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration-the-common-certificate-rotation-operation-would-be-almost-the-same-as-a-configuration-update-operation-do-you-have-an-example-of-how-to-do-this"></a>Как осуществляется безопасная отправка сертификата на виртуальную машину? Как подготовить масштабируемый набор виртуальных машин к запуску веб-сайта, где SSL для веб-сайта безопасно отправляется из конфигурации сертификата? (Обычная операция смены сертификатов практически такая же, как и операция обновления конфигурации.) Существует ли пример того, как это можно сделать? 

Чтобы безопасно отправить сертификат на виртуальную машину, вы можете установить сертификат клиента из хранилища ключей непосредственно в хранилище сертификатов Windows.

Используйте следующий код JSON:

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

Этот код поддерживает операционные системы Linux и Windows.

Дополнительные сведения см. в разделе о [создании и обновлении масштабируемого набора виртуальных машин](https://msdn.microsoft.com/library/mt589035.aspx).


### <a name="example-of-self-signed-certificate"></a>Пример самозаверяющего сертификата

1.  Создайте самозаверяющий сертификат в хранилище ключей.

    Используйте следующие команды PowerShell:

    ```powershell
    Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

    Connect-AzureRmAccount

    Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
    ```

    Таким образом вы получите входные данные для шаблона Azure Resource Manager.

    Пример создания самозаверяющего сертификата в хранилище ключей см. в статье [Сценарии защиты кластера Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/).

2.  Измените шаблон Resource Manager.

    Добавьте это свойство в раздел **virtualMachineProfile** как часть ресурса масштабируемого набора виртуальных машин:

    ```json 
    "osProfile": {
        "computerNamePrefix": "[variables('namingInfix')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('KeyVault', 'Microsoft.KeyVault/vaults', 'MikhegnVault')]"
                },
                "vaultCertificates": [
                    {
                        "certificateUrl": "https://mikhegnvault.vault.azure.net:443/secrets/VMSSCert/20709ca8faee4abb84bc6f4611b088a4",
                        "certificateStore": "My"
                    }
                ]
            }
        ]
    }
    ```
  

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Можно ли определить пару ключей SSH, используемых в процессе проверки подлинности SSH с помощью масштабируемого набора виртуальных машин Linux, развернутого из шаблона Resource Manager?  

Да. В этом случае REST API для **osProfile** и стандартной виртуальной машины аналогичен. 

Включите раздел **osProfile** в свой шаблон:

```json 
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```
 
Этот блок JSON используется в [шаблоне быстрого запуска 101-vm-sshkey на сайте GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).
 
Раздел osProfile также используется в [шаблоне быстрого запуска grelayhost.json на сайте GitHub](https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json).

Дополнительные сведения см. в разделе о [создании и обновлении масштабируемого набора виртуальных машин](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration).
  

### <a name="how-do-i-remove-deprecated-certificates"></a>Как удалить устаревшие сертификаты? 

Чтобы удалить устаревший сертификат, его необходимо удалить из списка сертификатов в хранилище. Не удаляйте сертификаты, которые вы хотите оставить на компьютере. В этом случае сертификат не удаляется со всех виртуальных машин, но он и не добавляется на новые виртуальные машины, созданные в масштабируемом наборе. 

Чтобы удалить сертификат из имеющихся виртуальных машин, напишите расширение пользовательского скрипта, которое позволяет удалить сертификат из хранилища сертификатов вручную.
 
### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning-i-want-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-use-them-in-my-resource-manager-template"></a>Как во время подготовки внедрить имеющийся открытый ключ SSH на уровень SSH масштабируемого набора виртуальных машин? Я хочу сохранить значения открытых ключей SSH в Azure Key Vault, чтобы затем использовать их в своем шаблоне Resource Manager.

Если вы устанавливаете на виртуальные машины только открытый ключ SSH, его не нужно отправлять в Key Vault. Открытые ключи не являются секретом.
 
При создании виртуальной машины Linux открытые ключи SSH можно указать в обычном текстовом формате.

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
```
 
Имя элемента конфигурации Linux | Обязательно | type | ОПИСАНИЕ
--- | --- | --- | --- |  ---
ssh | Нет  | Коллекция | Указывает конфигурацию ключа SSH для операционной системы Linux.
path | Yes | Строка | Указывает путь к файлу Linux, где должны храниться ключи SSH или сертификат.
keyData | Yes | Строка | Указывает открытый ключ SSH в кодировке Base64.

Пример см. в [шаблоне быстрого запуска 101-vm-sshkey на сайте GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

 
### <a name="when-i-run-update-azurermvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>При выполнении команды `Update-AzureRmVmss` после добавления нескольких сертификатов из одного хранилища ключей отображается следующая ошибка:
 
>Update-AzureRmVmss: список секретов содержит повторяющиеся экземпляры /subscriptions/<идентификатор_моей_подписки>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, которые запрещены.
 
Такая ситуация может произойти при попытке повторно добавить то же хранилище вместо использования нового сертификата хранилища для имеющегося исходного хранилища. Команда `Add-AzureRmVmssSecret` не работает должным образом при добавлении дополнительных секретов.
 
Чтобы добавить дополнительные секреты из одного хранилища ключей, обновите список $vmss.properties.osProfile.secrets[0].vaultCertificates.
 
Ожидаемую структуру входных данных см. в разделе о [создании и обновлении масштабируемого набора виртуальных машин](https://msdn.microsoft.com/library/azure/mt589035.aspx).
 
Сначала найдите секрет в объекте масштабируемого набора виртуальных машин, который находится в хранилище ключей. Затем добавьте ссылку на сертификат (URL-адрес и имя хранилища секретов) в список, связанный с хранилищем.

> [!NOTE] 
> Сейчас вы не можете удалить сертификаты из виртуальной машины через API-интерфейс масштабируемого набора виртуальных машин.
>

На новые виртуальные машины старый сертификат добавляться не будет, но на развернутых виртуальных машинах, где он уже установлен, этот сертификат останется.
 
### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Можно ли, не указывая пароль, отправить сертификат в масштабируемый набор виртуальных машин, если он находится в хранилище секретов?

Пароли не нужно жестко определять в скриптах. Их можно динамически извлечь на основе разрешений, используемых для выполнения скрипта развертывания. При выполнении скрипта, который перемещает сертификат из хранилища секретов в хранилище ключей, команда `get certificate`, выполняемая в хранилище секретов, также возвращает пароль для доступа к PFX-файлу.
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Как работает свойство Secrets в разделе virtualMachineProfile.osProfile масштабируемого набора виртуальных машин? Зачем нужно указывать исходное хранилище при определении абсолютного URI сертификата с использованием свойства certificateUrl? 

В свойстве Secrets профиля ОС должна находиться ссылка на сертификат службы удаленного управления Windows (WinRM). 

Указав исходное хранилище, вы сможете применить политики списка управления доступом (ACL), определенные в модели облачной службы Azure пользователя. Если его не указать, пользователи без необходимых разрешений смогут развертывать или использовать секреты в хранилище ключей с помощью поставщика вычислительных ресурсов (CRP). Политики ACL применяются даже к еще не созданным ресурсам.

Если указать неправильный идентификатор исходного хранилища, но допустимый URL-адрес хранилища ключей, при опросе операции отобразится ошибка.
 
### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>При добавлении секретов в имеющийся масштабируемый набор они применяются ко всем виртуальным машинам или только к новым? 

Сертификаты добавляются на все виртуальные машины, в том числе на имеющиеся. Если для свойства upgradePolicy масштабируемого набора виртуальных машин задано значение **manual**, сертификат добавляется в процессе обновления виртуальной машины вручную.
 
### <a name="where-do-i-put-certificates-for-linux-vms"></a>Куда следует поместить сертификаты виртуальных машин Linux?

Дополнительные сведения о развертывании сертификатов виртуальных машин Linux из хранилища ключей, управляемого клиентом, см. в [этой записи блога](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).
  
### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Как добавить новый сертификат хранилища в новый объект сертификата?

Чтобы добавить сертификат хранилища в имеющийся секрет, используйте приведенный ниже пример сценария PowerShell. Используйте только один объект секрета.
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Что происходит с сертификатами после пересоздания образа виртуальной машины?

После пересоздания образа виртуальной машины сертификаты удаляются. В рамках этого процесса полностью удаляется диск ОС. 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Что происходит после удаления сертификата из хранилища ключей?

Если удалить секрет из хранилища ключей и отменить выделение всех виртуальных машин с помощью команды `stop deallocate`, а затем снова запустить этот процесс, произойдет сбой. Этот сбой связан с тем, что CRP не может получить секреты из хранилища ключей. В этом случае вы можете удалить сертификаты из модели масштабируемого набора виртуальных машин. 

Компонент CRP не сохраняет секреты клиента. При выполнении команды `stop deallocate` для всех виртуальных машин в масштабируемом наборе кэш удаляется. В этом случае секреты извлекаются из хранилища ключей.

Эта проблема не влияет на масштабирование, так как в Azure Service Fabric хранится кэшированная копия секрета (в модели с одним клиентом Fabric).
 
### <a name="why-do-i-have-to-specify-the-exact-location-for-the-certificate-url-httpsname-of-the-vaultvaultazurenet443secretsexact-location-as-indicated-in-service-fabric-cluster-security-scenarioshttpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>Почему нужно указать точное расположение URL-адреса сертификата (https://<name of the vault>.vault.azure.net:443/secrets/<exact location>), как указано в статье [Сценарии защиты кластера Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)?
 
В соответствии с документацией по Azure Key Vault, если версия секрета не указана, REST API получения секрета должен вернуть последнюю версию.
 
Метод | URL-адрес
--- | ---
ПОЛУЧЕНИЕ | https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}

Замените параметры {*имя_секрета*} и {*версия_секрета*} именем и версией секрета, которую необходимо получить. Версию секрета можно не указывать. В этом случае извлекается текущая версия.
  
### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Почему при использовании Key Vault необходимо указать версию сертификата?

Указание версии сертификата в Key Vault позволяет пользователям понять, какой сертификат развернут на их виртуальных машинах.

Если вы создаете виртуальную машину, а затем обновляете секрет в хранилище ключей, новый сертификат не загружается на виртуальные машины, но ссылка на него отображается. При создании виртуальных машин на них устанавливается новый секрет. Чтобы избежать этого, необходимо добавить ссылку на версию секрета.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Моя группа работает с несколькими сертификатами, добавленными как файлы сертификата открытого ключа (CER-файлы). Какой метод вы рекомендуете использовать для развертывания этих сертификатов в масштабируемом наборе виртуальных машин?

Чтобы развернуть файлы сертификата открытого ключа (CER-файлы) в масштабируемый набор виртуальных машин, вы можете создать PFX-файл, содержащий только CER-файлы. Для этого используйте параметр `X509ContentType = Pfx`. Например, отправьте CER-файл как объект x509Certificate2 в C# и PowerShell, а затем вызовите метод X509Certificate.Export. 

Дополнительные сведения о методе X509Certificate.Export см. в [этой статье](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-most-other-resource-providers-have-this-option"></a>Я не вижу параметр для передачи сертификатов в виде строк в формате Base64. Большинство других поставщиков ресурсов предоставляют этот параметр.

Чтобы выполнить эмуляцию передачи сертификата в виде строки в формате Base64, можно извлечь последнюю версию URL-адреса в шаблоне Resource Manager. Включите в шаблон Resource Manager следующее свойство JSON:

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Нужно ли помещать сертификаты в объекты JSON в хранилищах ключей?

В масштабируемых наборах и на виртуальных машинах сертификаты нужно помещать в объекты JSON. 

Мы также поддерживаем тип содержимого application/x-pkcs12. Инструкции по использованию application/x-pkcs12 см. в записи блога об [использовании PFX-сертификатов в Azure Key Vault](http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/).
 
Сейчас мы не поддерживаем CER-файлы. Чтобы использовать CER-файлы, экспортируйте их в PFX-контейнеры.



## <a name="compliance-and-security"></a>Обеспечение соответствия и безопасности

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Соответствуют ли масштабируемые наборы виртуальных машин требованиям стандарта PCI?

Масштабируемые наборы виртуальных машин представляют собой тонкий слой API поверх поставщика вычислительных ресурсов. Оба компонента входят в состав вычислительной платформы в дереве службы Azure.

С точки зрения соответствия масштабируемые наборы виртуальных машин являются основной частью вычислительной платформы Azure. Они используют те же группы, средства, процессы, методы развертывания, элементы управления безопасностью, JIT-компиляцию, возможности мониторинга, оповещения и т. д., что и поставщик вычислительных ресурсов. Масштабируемые наборы виртуальных машин соответствуют требованиям сферы платежных карт, так как поставщик вычислительных ресурсов является частью аттестации по требованиям Стандарта безопасности данных в сфере платежных карт (PCI DSS).

Дополнительные сведения см. в [центре управления безопасностью Майкрософт](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-azure-managed-service-identityhttpsdocsmicrosoftcomazureactive-directorymsi-overview-work-with-virtual-machine-scale-sets"></a>Работает ли [управляемое удостоверение службы Azure](https://docs.microsoft.com/azure/active-directory/msi-overview) с масштабируемыми наборами виртуальных машин?

Да. Некоторые примеры шаблонов MSI доступны в шаблонах быстрого запуска Azure. Для Linux — [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux). Для Windows — [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows).


## <a name="extensions"></a>расширения.

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Как удалить расширение масштабируемого набора виртуальных машин?

Чтобы удалить расширение масштабируемого набора виртуальных машин, используйте следующий пример сценария PowerShell:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
Значение параметра extensionName находится в строке `$vmss`.
   
### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-log-analytics"></a>Существует ли пример шаблона масштабируемого набора виртуальных машин, который интегрируется с Log Analytics?

Шаблон масштабируемого набора виртуальных машин, который интегрируется с Log Analytics, приведен во втором примере статьи о [развертывании кластера Azure Service Fabric и включении мониторинга с помощью Log Analytics](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).
   
### <a name="extensions-seem-to-run-in-parallel-on-virtual-machine-scale-sets-this-causes-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this"></a>Похоже, что расширения выполняются в масштабируемых наборах виртуальных машин одновременно. В результате этого выполнение операции настраиваемого расширения скриптов завершается сбоем. Как это исправить?

Дополнительные сведения о виртуализации расширений в масштабируемых наборах виртуальных машин см. в [этой статье](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/).
 
 
### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Как сбросить пароль виртуальных машин в масштабируемом наборе?

Сбросить пароль виртуальных машин в масштабируемом наборе можно двумя способами.

- Измените модель масштабируемого набора виртуальных машин. Доступно для API вычислений версии 2017-12-01 и более поздней.

    Обновите учетные данные администратора непосредственно в модели масштабируемого набора (например, с помощью обозревателя ресурсов Azure, PowerShell или CLI). После обновления масштабируемого набора у всех новых виртуальных машин будут новые учетные данные. Для имеющихся виртуальных машин новые данные доступны только в том случае, если они будут пересозданы с использованием нового образа. 

- Сбросьте пароль с помощью расширений доступа к виртуальной машине.

    Используйте следующий пример сценария PowerShell:
    
    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}
     
    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```


### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Как добавить расширение для всех виртуальных машин в масштабируемом наборе?

Если политика обновления настроена на **автоматическое** обновление, при повторном развертывании шаблона новые свойства расширения применяются на всех виртуальных машинах.

Если политика обновления настроена на обновление **вручную**, сначала обновите расширение, а затем вручную обновите все экземпляры на виртуальных машинах.

  
### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected-that-is-will-the-vms-not-match-the-virtual-machine-scale-set-model-or-are-they-ignored-when-an-existing-machine-is-service-healed-or-reimaged-are-the-scripts-that-are-currently-configured-on-the-virtual-machine-scale-set-executed-or-are-the-scripts-that-were-configured-when-the-vm-was-first-created-used"></a>Если обновить расширения, связанные с имеющимся масштабируемым набором, повлияет ли это на имеющиеся виртуальные машины? (То есть *не* перестанут ли виртуальные машины соответствовать требованиям модели масштабируемого набора виртуальных машин?) Или они игнорируются? После пересоздания образа или восстановления имеющейся машины будут ли выполняться скрипты, настроенные в настоящее время в масштабируемом наборе, или будут использоваться скрипты, настроенные при первом создании машины виртуальной машины?

Если обновляется определение расширения в модели масштабируемого набора виртуальных машин, а для свойства upgradePolicy задано значение **automatic**, обновляются и виртуальные машины. Если для свойства upgradePolicy задано значение **manual**, расширения помечаются как несоответствующие требованиям модели. 

Если виртуальная машина восстановлена, она рассматривается как прошедшая перезагрузку, и расширения повторно не запускаются. Пересоздание образа напоминает замену диска ОС на исходный образ. В этом случае запускаются все специализации, например расширения, из последней модели.
 
### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-azure-ad-domain"></a>Как присоединить масштабируемый набор виртуальных машин к домену Azure Active Directory (Azure AD)?

Чтобы присоединить масштабируемый набор виртуальных машин к домену Azure AD, можно определить расширение. 

Чтобы определить расширение, используйте свойство JsonADDomainExtension.

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```
 
### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-example-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>Расширение масштабируемого набора виртуальных машин пытается установить что-то, требующее перезагрузки. Например, "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted Install-WindowsFeature –Name FS-Resource-Manager –IncludeManagementTools".

Если расширение масштабируемого набора виртуальных машин пытается установить что-то, требующее перезагрузки, вы можете использовать расширение настройки требуемого состояния службы автоматизации Azure (Automation DSC). При использовании операционной системы Windows Server 2012 R2 Azure запрашивает установку и перезагрузку Windows Management Framework (WMF) 5.0, а затем выполняет настройку. 
 
### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Как включить антивредоносную программу в масштабируемом наборе виртуальных машин?

Чтобы включить антивредоносную программу в масштабируемом наборе виртуальных машин, используйте следующий пример сценария PowerShell:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### <a name="i-need-to-execute-a-custom-script-thats-hosted-in-a-private-storage-account-the-script-runs-successfully-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signature-sas-it-fails-this-message-is-displayed-missing-mandatory-parameters-for-valid-shared-access-signature-linksas-works-fine-from-my-local-browser"></a>Мне нужно выполнить настраиваемый скрипт, размещенный в учетной записи частного хранилища. При использовании общедоступного хранилища скрипт выполняется успешно, но когда я использую подписанный URL-адрес, он завершается сбоем и отображается следующее сообщение об ошибке: "Отсутствуют обязательные параметры для допустимой подписи коллективного доступа". В локальном браузере проблемы с подписанным URL-адресом и ссылкой не возникали.

Чтобы выполнить настраиваемый скрипт, размещенный в учетной записи частного хранилища, настройте защищенные параметры с использованием имени и ключа учетной записи хранения. Дополнительные сведения см. в статье [Расширение Custom Script в ОС Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).


## <a name="networking"></a>Сеть
 
### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Можно ли назначить группу безопасности сети масштабируемому набору, чтобы она применялась ко всем сетевым картам виртуальных машин в наборе?

Да. Группу безопасности сети можно применить непосредственно к масштабируемому набору, указав ее в разделе networkInterfaceConfigurations сетевого профиля. Пример:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            }
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                 }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Как переключить виртуальный IP-адрес для масштабируемого набора виртуальных машин в той же подписке и в том же регионе?

Если у вас есть два масштабируемых набора виртуальных машин с внешними интерфейсами Azure Load Balancer и они находятся в одной подписке и регионе, можно освободить общедоступные IP-адреса в одном из них и назначить их другому. Пример приведен в статье [VIP Swap: Blue-green deployment in Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) (Переключение виртуальных IP-адресов: развертывание по схеме "blue-green" в Azure Resource Manager). Это подразумевает задержку, так как ресурсы освобождаются и выделяются на уровне сети. Другой, более быстрый вариант — воспользоваться шлюзом приложений Azure с двумя серверными пулами и правилом маршрутизации. Также можно разместить приложение в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), которая обеспечивает быстрое переключение между промежуточными и рабочими слотами.
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Как указать диапазон частных IP-адресов для выделения статических частных IP-адресов?

IP-адреса выбираются из указанной подсети. 

При выделении IP-адресов масштабируемого набора виртуальных машин всегда используется динамический метод, но это не означает, что эти IP-адреса можно изменить. В этом случае под динамическим методом подразумевается, что вам не нужно указывать IP-адрес в запросе PUT. Укажите статический набор на основе подсети. 
    
### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Как развернуть масштабируемый набор виртуальных машин в имеющейся виртуальной сети Azure? 

Чтобы развернуть масштабируемый набор виртуальных машин в имеющейся виртуальной сети Azure, используйте [этот шаблон](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet). 

### <a name="how-do-i-add-the-ip-address-of-the-first-vm-in-a-virtual-machine-scale-set-to-the-output-of-a-template"></a>Как добавить IP-адрес первой виртуальной машины в масштабируемом наборе к выходным данным шаблона?

Дополнительные сведения о добавлении IP-адреса первой виртуальной машины в масштабируемом наборе к выходным данным шаблона см. в ответе на вопрос о [получении частных IP-адресов VMSS](http://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Можно ли использовать масштабируемые наборы с ускоренной сетью?

Да. Чтобы использовать ускоренную сеть, в настройках networkInterfaceConfigurations масштабируемого набора задайте для параметра enableAcceleratedNetworking значение true. Например:
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
        "name": "niconfig1",
        "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
                ]
            }
            }
        ]
        }
    }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Как настроить DNS-серверы, используемые масштабируемым набором?

Чтобы создать масштабируемый набор виртуальных машин с пользовательской конфигурацией DNS, добавьте пакет JSON dnsSettings в раздел networkInterfaceConfigurations конфигурации масштабируемого набора. Пример:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Как настроить масштабируемый набор, чтобы назначать общедоступный IP-адрес каждой виртуальной машине?

Чтобы создать масштабируемый набор виртуальных машин, назначающий общедоступный IP-адрес каждой виртуальной машине, убедитесь, что версия API ресурса Microsoft.Compute/virtualMachineScaleSets — 2017-03-30, и добавьте пакет JSON _publicipaddressconfiguration_ в раздел ipConfigurations конфигурации масштабируемого набора. Пример:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Можно ли настроить масштабируемый набор для работы с несколькими шлюзами приложений?

Да. Можно добавить идентификаторы ресурсов для нескольких серверных пулов адресов шлюза приложений в список _applicationGatewayBackendAddressPools_ в разделе _ipConfigurations_ сетевого профиля масштабируемого набора.

## <a name="scale"></a>Масштаб

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>В каких случаях следует создавать масштабируемый набор с одной виртуальной машиной или без них?

Одна из причин заключается в использовании эластичных свойств масштабируемого набора виртуальных машин. Например, вы можете развернуть масштабируемый набор без виртуальных машин, чтобы определить инфраструктуру, не платя за выполнение виртуальной машины. Затем при развертывании виртуальных машин вы можете увеличить емкость масштабируемого набора в соответствии с количеством рабочих экземпляров.

Вторая причина заключается в том, что в масштабируемом наборе выполняются операции, не учитывающие доступность в таком же смысле, что и группы доступности с отдельными виртуальными машинами. Масштабируемые наборы виртуальных машин позволяют работать с взаимозаменяемыми недифференцированными единицами вычислений. Это единообразие является ключевым преимуществом при выборе между масштабируемыми наборами виртуальных машин и группами доступности. Многие рабочие нагрузки без отслеживания состояния не учитывают отдельные единицы. Если рабочая нагрузка снижается, вы можете уменьшить масштаб до одной единицы вычисления, а при увеличении нагрузки — снова увеличить количество этих единиц.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Как изменить количество виртуальных машин в масштабируемом наборе?

Чтобы изменить число виртуальных машин в масштабируемом наборе на портале Azure, в разделе свойств масштабируемого набора виртуальных машин щелкните колонку "Масштабирование" и воспользуйтесь ползунком. Другие способы изменения количества экземпляров см. в статье [Change the instance count of an Azure VM Scale Set](https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/) (Изменение количества экземпляров в масштабируемом наборе виртуальных машин).

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Как настроить пользовательские оповещения, отображающиеся при достижении определенных пороговых значений?

Обработкой оповещений при достижении определенных пороговых значений можно управлять несколькими способами. Например, вы можете определить настраиваемые объекты webhook. Ниже приведен пример объекта webhook из шаблона Resource Manager.

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ],
```

В этом примере при достижении порогового значения оповещение отправляется на сайт Pagerduty.com.



## <a name="patching-and-operations"></a>Установка исправлений и эксплуатация

### <a name="how-do-i-create-a-scale-set-in-an-existing-resource-group"></a>Как создать масштабируемый набор в существующей группе ресурсов?

Создание масштабируемых наборов в существующей группе ресурсов на портале Azure пока невозможно, но можно указать существующую группу ресурсов при развертывании масштабируемого набора с помощью шаблона Azure Resource Manager. Можно также указать существующую группу ресурсов при создании масштабируемого набора с помощью Azure PowerShell или интерфейса командной строки.

### <a name="can-we-move-a-scale-set-to-another-resource-group"></a>Можно ли переместить масштабируемый набор другую группу ресурсов?

Да, ресурсы масштабируемого набора можно переместить в новую подписку или группу ресурсов.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Как обновить образ масштабируемого набора виртуальных машин и как управлять установкой исправлений?

Дополнительные сведения об обновлении масштабируемого набора виртуальных машин и управлении установкой исправлений см. в [этой статье.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set)

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Можно ли использовать операцию пересоздания образа, чтобы сбросить параметры виртуальной машины, не изменяя образ? (То есть я хочу сбросить параметры виртуальной машины к значениям по умолчанию, а не создавать новый образ.)

Да. Операцию пересоздания образа можно использовать, чтобы сбросить параметры виртуальной машины, не изменяя образ. Тем не менее, если масштабируемый набор виртуальных машин ссылается на образ платформы последней версии (значение `version = latest`), образ ОС виртуальной машины можно обновить на более позднюю версию при вызове операции `reimage`.

Дополнительные сведения см. в статье об [управлении всеми виртуальными машинами в масштабируемом наборе](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set).

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-log-analytics"></a>Существует возможность интегрировать масштабируемые наборы с Azure Log Analytics?

Да, это можно сделать, установив расширение Log Analytics на виртуальных машинах масштабируемого набора. Ниже приведен пример для Azure CLI.
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
Необходимые значения workspaceId и workspaceKey можно найти на портале OMS. На странице "Обзор" щелкните элемент "Параметры". Откройте расположенную сверху вкладку "Подключенные источники".

Примечание. Если параметру _upgradePolicy_ масштабируемого набора присвоено значение Manual, необходимо применить расширение для всех виртуальных машин в наборе, вызвав их обновление. В интерфейсе командной строки для этого потребовалось бы ввести команду _az vmss update-instances_.

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Как включить диагностику загрузки?

Чтобы включить диагностику загрузки, сначала создайте учетную запись хранения. Затем вставьте приведенный ниже блок JSON в раздел **virtualMachineProfile** масштабируемого набора виртуальных машин и обновите этот набор.

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

После создания виртуальной машины в свойстве InstanceView отобразятся сведения для снимка экрана и т. д. Ниже приведен пример:
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```


## <a name="virtual-machine-properties"></a>Свойства виртуальной машины

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Как получить сведения о свойствах каждой виртуальной машины, не выполняя несколько вызовов? Например, как можно получить сведения о домене сбоя для каждой из 100 виртуальных машин в масштабируемом наборе?

Чтобы получить сведения о свойствах каждой виртуальной машины, не выполняя несколько вызовов, вы можете вызвать `ListVMInstanceViews`, выполнив запрос REST API `GET` к следующему универсальному коду ресурса:

/subscriptions/<идентификатор_подписки>/resourceGroups/<имя_группы_ресурсов>/providers/Microsoft.Compute/virtualMachineScaleSets/<имя_масштабируемого_набора>/virtualMachines?$expand=instanceView&$select=instanceView.

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Можно ли передать разные аргументы расширения на разные виртуальные машины в масштабируемом наборе?

Нет. Вы не можете передать разные аргументы расширения на разные виртуальные машины в масштабируемом наборе. Но расширения могут действовать на основе уникальных свойств виртуальной машины, на которых они выполняются, таких как имя машины. Кроме того, расширения могут запрашивать метаданные экземпляра на сайте http://169.254.169.254, чтобы получить дополнительные сведения о виртуальной машине.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Почему между именами виртуальных машин в масштабируемом наборе и их идентификаторами существуют пропуски? Например, 0, 1, 3...

Пропуски между именами виртуальных машин в масштабируемом наборе и их идентификаторами возникают, потому что для свойства **overprovision** масштабируемого набора задано стандартное значение **true**. Если значение избыточной подготовки — **true**, создается большее количество виртуальных машин, чем запрашивалось. Лишние виртуальные машины удаляются. Это позволит повысить надежность развертывания, но повлияет на связанные правила преобразования сетевых адресов и именования. 

Для этого свойства можно задать значение **false**. На надежность развертывания небольших масштабируемых наборов это не окажет значительного влияния.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Какова разница между удалением виртуальной машины в масштабируемом наборе и отменой ее подготовки? Как понять, что выбрать в том или ином сценарии?

Основное различие между удалением виртуальной машины в масштабируемом наборе и отменой ее подготовки заключается в том, что при отмене подготовки (`deallocate`) не удаляются виртуальные жесткие диски. При выполнении команды `stop deallocate` взимается плата за хранение. Причины выбора того или иного варианта заключаются в следующем:

- Вы хотите прекратить платить за вычислительные ресурсы, но сохранить состояние диска виртуальных машин.
- Вы хотите запустить набор виртуальных машин быстрее, чем при развертывании масштабируемого набора.
  - Вы создали собственную подсистему автомасштабирования и хотите быстрее выполнить сквозное масштабирование (касательно этого сценария).
- У вас есть масштабируемый набор, неравномерно распределенный между доменами сбоя и обновления. Причиной этого может быть выборочное удаление или удаление после избыточной подготовки виртуальных машин. Чтобы равномерно распределить виртуальные машины между доменами сбоя и обновления, выполните команду `stop deallocate`, а затем — `start`.

