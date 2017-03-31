---
title: "Часто задаваемые вопросы о масштабируемых наборах виртуальных машин Azure | Документация Майкрософт"
description: "Ответы на часто задаваемые вопросы о масштабируемых наборах виртуальных машин."
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/17/2017
ms.author: negat
ms.custom: na
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 850459a79c723de0eb3249cfe9ea19bc988ca318
ms.lasthandoff: 03/18/2017


---

# <a name="azure-virtual-machine-scale-sets-faq"></a>Часто задаваемые вопросы о масштабируемых наборах виртуальных машин Azure

В этой статье приведены ответы на часто задаваемые вопросы о масштабируемых наборах.

## <a name="autoscale"></a>Autoscale

### <a name="what-are-best-practices-for-azure-autoscale"></a>Существуют ли рекомендации по автомасштабированию Azure?

Да. Дополнительные сведения см. в следующей статье: https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices.

### <a name="where-do-i-find-the-metric-names-for-autoscaling-using-host-based-metrics"></a>Где можно найти имена метрик на основе узла, используемые в процессе автомасштабирования?

Дополнительные сведения см. в следующей статье: https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/.

### <a name="are-there-any-examples-of-autoscaling-based-on-a-service-bus-topic-and-queue-length"></a>Существуют ли какие-либо образцы автомасштабирования на основе раздела или длины очереди служебной шины?

Да. См.:

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/.

Для очереди служебной шины:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Для очередей хранилища:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Замените эти примеры значений соответствующими универсальными кодами ресурсов (URI).


### <a name="should-we-autoscale-with-host-based-metrics-or-use-a-diagnostics-extension"></a>Следует ли выполнять автомасштабирование с использованием метрик на основе узла или с применением расширения диагностики?

Вы можете создать на виртуальной машине конфигурацию автомасштабирования с использованием метрик уровня узла или метрик на основе гостевой ОС.

Список поддерживаемых метрик см. в следующей статье: https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics. Полное определение конфигурации масштабируемых наборов (в этом случае мы использовали метрику ЦП уровня узла и метрику количества сообщений) см. в следующей статье:

https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets.

### <a name="how-can-i-set-alert-rules-on-a-scale-set"></a>Как можно задать правила оповещений в масштабируемом наборе?

Оповещения на основе метрик в масштабируемых наборах можно создать с помощью PowerShell или интерфейса командной строки. См.:

https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules

https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts

Идентификатор целевого ресурса масштабируемого набора выглядит следующим образом: /subscriptions/ИД_подписки/resourceGroups/группа_ресурсов/providers/Microsoft.Compute/virtualMachineScaleSets/имя_набора.

В качестве метрики, на основе которой будут создаваться оповещения, можно выбрать любой счетчик производительности виртуальной машины.

Дополнительные сведения см. в следующих статьях: https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-windows-vm-v2-as-a-guest-os

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-linux-vm-v2-as-a-guest-os

### <a name="how-can-i-set-up-autoscale-on-a-scale-set-using-powershell"></a>Как можно задать параметры автомасштабирования в масштабируемом наборе с помощью PowerShell?

Дополнительные сведения см. в следующей статье: https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/.




## <a name="certificates"></a>Сертификаты

### <a name="how-do-you-securely-ship-a-certificate-into-the-vm--is-there-an-example-of-provisioning-a-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration--the-common-certificate-rotation-operation-would-amount-to-a-configuration-update-operation"></a>Как осуществляется безопасная отправка сертификата на виртуальную машину?  Существует ли пример подготовки масштабируемого набора к запуску веб-сайта, где SSL для веб-сайта безопасно отправляется из конфигурации сертификата?  Обычная операция смены сертификатов приравнивается к операции обновления конфигурации.

Мы поддерживаем установку сертификатов клиента из хранилища ключей непосредственно в хранилище сертификатов Windows.

Дополнительные сведения (в отношении масштабируемых наборов) см. в следующей статье:

https://msdn.microsoft.com/library/mt589035.aspx.

```json
        "secrets": [ {
              "sourceVault": {
                      "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
          },
          "vaultCertificates": [ {
                      "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                  "certificateStore": "certificateStoreName"
          } ]
        } ]
```

Приведенный выше блок поддерживает операционные системы Linux и Windows.

### <a name="self-signed-certificate-example"></a>Пример самозаверяющего сертификата:

#### <a name="create-a-self-signed-cert-in-a-keyvault"></a>Создание самозаверяющего сертификата в Key Vault

Указания по созданию (один из способов) самозаверяющего сертификата в Key Vault см. в следующей статье Service Fabric: https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/.

Команды PowerShell:

```powershell
Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

Login-AzureRmAccount

Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
```

Приведенная выше команда вернет входные данные для шаблона Resource Manager.

#### <a name="change-resource-manager-template"></a>Изменение шаблона Resource Manager

Добавьте это свойство в раздел virtualMachineProfile как часть ресурса масштабируемого набора:

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
 

### <a name="is-there-a-way-to-specify-an-ssh-key-pair-that-i-want-to-use-for-ssh-authentication-with-a-linux-scale-set-from-a-resource-manager-template"></a>Существует ли способ определения пары ключей SSH, которые можно использовать в процессе проверки подлинности SSH с помощью масштабируемого набора Linux, развернутого из шаблона Resource Manager?  

В этом случае REST API для osProfile и виртуальной машины аналогичен:
 
https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration.
 
Включите раздел `osProfile` в свой шаблон, как показано в следующем примере:

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
 
Этот блок JSON используется в следующем шаблоне быстрого запуска:
 
https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json.
 
Просмотрите также раздел osProfile в следующем шаблоне:
 
https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json.

### <a name="how-do-i-remove-deprecated-certificates"></a>Как удалить устаревшие сертификаты? 

Устаревший сертификат необходимо удалить из списка сертификатов в хранилище. Сертификаты, которые вы хотите оставить на машине, удалять не следует. В этом случае сертификат не удаляется со всех виртуальных машин, но он и не добавляется на новые виртуальные машины, созданные в масштабируемом наборе. Чтобы удалить сертификат из имеющихся виртуальных машин, необходимо написать расширение пользовательского скрипта, которое позволяет удалить сертификат из хранилища сертификатов вручную.
 
### <a name="how-do-i-take-an-existing-ssh-public-key-and-inject-it-into-the-scale-set-ssh-layer-during-provisioning--i-would-like-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-utilize-them-in-my-resource-manager-template"></a>Как во время подготовки внедрить открытый ключ SSH на уровень SSH масштабируемого набора?  Мне нужно хранить значения открытых ключей SSH в Azure Key Vault, чтобы затем использовать их в своем шаблоне Resource Manager.

Если вы устанавливаете на виртуальные машины только открытый ключ SSH, его не нужно отправлять в хранилище ключей, так как открытые ключи не являются секретом.
 
При создании виртуальной машины Linux открытые ключи SSH можно указать в обычном текстовом формате.
Пример можно найти здесь:

https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json.
 
В частности:

```json
"linuxConfiguration": {  
          "ssh": {  
            "publicKeys": [ {  
              "path": "path",
              "keyData": "publickey"
            } ]
          }
```
 
Имя элемента конфигурации Linux | Обязательно | Тип | Описание
--- | --- | --- | --- |  ---
ssh | Нет | Коллекция | Указывает конфигурацию ключа SSH для операционной системы Linux.
path | Да | Строка | Указывает путь к файлу Linux, где должны храниться ключи SSH или сертификат.
keyData | Да | Строка | Указывает открытый ключ SSH в кодировке Base64.
 
### <a name="when-i-run-update-azurermvmss-after-more-than-one-certificate-from-the-same-keyvault-i-get-the-following-error"></a>При выполнении команды Update-AzureRmVmss, когда в том же хранилище Key Vault находится несколько сертификатов, отображается следующая ошибка:
 
Update-AzureRmVmss: список секретов содержит повторяющиеся экземпляры /subscriptions/<идентификатор_моей_подписки>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, которые запрещены. Почему не удается добавить два сертификата из одного хранилища Key Vault?
 
Такая ситуация может произойти при попытке добавить то же хранилище дважды вместо нового сертификата хранилища для имеющегося исходного хранилища. Команда Add-AzureRmVmssSecret не работает должным образом при добавлении дополнительных секретов.
 
Если вы хотите добавить дополнительные секреты из одного хранилища ключей, следует обновить список $vmss.properties.osProfile.secrets[0].vaultCertificates.
 
Ожидаемую структуру входных данных см. здесь: https://msdn.microsoft.com/library/azure/mt589035.aspx.
 
Сначала найдите секрет в объекте масштабируемого набора с тем же хранилищем ключей. Затем необходимо добавить ссылку на сертификат (URL-адрес и имя хранилища секретов) в список, связанный с хранилищем.

Примечание. Сейчас удаление сертификатов из виртуальной машины через API-интерфейсы масштабируемого набора не поддерживается.
 
На новые виртуальные машины старый сертификат добавляться не будет, но на виртуальных машинах, где он уже был развернут, он останется.
 
### <a name="is-there-a-way-to-get-certificates-pushed-to-the-scale-set-without-providing-the-password-when-the-certificate-is-in-secretstore-currently"></a>Можно ли, не указывая пароль, получить сертификат, отправленный в масштабируемый набор, если он находится в хранилище секретов?

Пароли не нужно жестко определять в скриптах. Их можно динамически извлечь на основе разрешения выполняемого скрипта развертывания. При выполнении скрипта, который перемещает сертификат из хранилища секретов в хранилище ключей, команда на получение сертификата из хранилища секретов также возвращает пароль для доступа к PFX-файлу.
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-of-a-scale-set-work-why-do-you-need-sourcevault-when-you-have-to-specify-the-absolute-uri-to-a-certificate-with-certificateurl"></a>Как работает свойство secrets в разделе virtualMachineProfile.osProfile масштабируемого набора? Зачем нужно указывать исходное хранилище при определении абсолютного URI сертификата с использованием URL-адреса сертификата? 

В свойстве secrets профиля ОС должна находиться ссылка на сертификат WinRM. 

Указав исходное хранилище, вы сможете применить политики ACL, определенные в CSM. Если его не указать, пользователи без необходимых разрешений смогут развертывать или использовать секреты в хранилище ключей с помощью CRP. Политики ACL применяются даже к еще не созданным ресурсам.

Если указать неправильный идентификатор исходного хранилища, но допустимый URL-адрес хранилища ключей, при опросе операции отобразится ошибка.
 
### <a name="if-i-add-secrets-to-an-existing-scale-set-does-it-inject-them-in-existing-instances-or-only-new-ones"></a>Секреты, добавленные в готовый масштабируемый набор, применяются ко всем экземплярам или только к новым? 

Сертификаты добавляются на все виртуальные машины, в том числе на имеющиеся. Если для свойства upgradePolicy масштабируемого набора задано значение Manual, сертификат добавляется в процессе обновления виртуальной машины вручную.
 
### <a name="where-do-certificates-go-for-linux-vms"></a>Где хранятся сертификаты на виртуальных машинах Linux?

Дополнительные сведения см. в следующей статье: https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/.
  
### <a name="how-do-you-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Как добавить новый сертификат хранилища в новый объект сертификата?

Если вы хотите добавить сертификат хранилища в имеющийся секрет, в котором он должен быть единственным объектом, это можно сделать с помощью следующего примера сценария PowerShell:
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Что происходит с сертификатами после пересоздания образа виртуальной машины?

После пересоздания образа виртуальной машины сертификаты исчезают, так как в рамках этого процесса полностью удаляется диск ОС. 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Что происходит после удаления сертификата из хранилища ключей?

Если удалить секрет в хранилище ключей и отменить выделение всех виртуальных машин, а затем снова запустить этот процесс, произойдет сбой. Этот сбой связан с тем, что CRP не может получить секреты из Key Vault. В этом случае вы можете удалить сертификаты из модели масштабируемого набора. 

Компонент CRP не сохраняет секреты клиента. Если отменить выделение всех виртуальных машин в масштабируемом наборе, кэш удаляется. В этом случае секреты извлекаются из хранилища ключей.

Эта проблема не влияет на масштабирование, так как в структуре хранится кэшированная копия секрета (по крайней мере в модели с одним клиентом структуры).
 
### <a name="why-do-we-have-to-specify-the-exact-location-for-the-certificate-url-as-referenced-here-per-httpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>Почему нужно указать точное расположение URL-адреса сертификата, как указано в следующей статье: https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/? 
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
 
В соответствии с документацией по Key Vault, если версия секрета не указана, REST API получения секрета должен вернуть последнюю версию.
 
Метод | URL-адрес
--- | ---
ПОЛУЧЕНИЕ | https://mykeyvault.vault.azure.net/secrets/{имя_секрета}/{версия_секрета}?api-version={версия_API}

Замените параметры {имя_секрета} и {версия_секрета} версией секрета, которую необходимо получить. Если извлекается текущая версия, версию секрета можно не указывать.
  
### <a name="why-does-certificate-version-have-to-be-specified-when-using-key-vault"></a>Почему при использовании хранилища ключей необходимо указать версию сертификата?

Это позволяет понять пользователям, какой сертификат развернут на их виртуальных машинах.

Если вы создаете виртуальную машину, а затем обновляете секрет в хранилище ключей, новый сертификат не загружается на виртуальные машины, но ссылка на него будет отображаться. При создании виртуальных машин на них устанавливается новый секрет. Чтобы избежать этой путаницы, необходимо добавить ссылку на явную версию секрета.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-is-for-deployment-of-these-certs-to-a-scale-set"></a>Моя группа работает с несколькими сертификатами, добавленными как файлы сертификата открытого ключа (CER-файлы). Какой метод вы рекомендуете использовать для развертывания этих сертификатов в масштабируемом наборе?

Вы можете создать PFX-файл, содержащий только CER-файлы, задав для параметра X509ContentType значение Pfx. Например, отправьте CER-файл как объект x509Certificate2 в C# и PowerShell и вызовите этот метод: https://msdn.microsoft.com/library/24ww6yzk(v=vs.110).aspx.

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-that-most-other-resource-providers-provide"></a>Я не вижу параметр для передачи сертификатов в виде строк в формате Base64, предоставляемый большинством других поставщиков ресурсов.

Чтобы выполнить эмуляцию описанного поведения, можно извлечь последнюю версию URL-адреса в шаблоне Resource Manager. Вы можете включить в шаблон Resource Manager следующее свойство JSON:

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-we-have-to-wrap-certs-in-json-objects-in-keyvaults"></a>Нужно ли помещать сертификаты в объекты JSON в хранилищах ключей?

Это требование к масштабируемым наборам и виртуальным машинам. Мы также поддерживаем тип содержимого application/x-pkcs12. Инструкции см. здесь: http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/.
 
Сейчас мы не поддерживаем CER-файлы. Их необходимо экспортировать в PFX-контейнеры.





## <a name="compliance"></a>Соответствие нормативным требованиям

### <a name="are-scale-sets-pci-compliant"></a>Соответствуют ли масштабируемые наборы требованиям стандарта PCI?

Масштабируемые наборы представляют собой тонкий слой API поверх поставщика вычислительных ресурсов, который входит в состав области вычислительной платформы в дереве службы Azure.

Таким образом, с точки зрения соответствия масштабируемые наборы являются основой частью вычислительной платформы Azure. Учитывая вышесказанное, они используют те же группы, средства, процессы, методы развертывания, элементы управления безопасностью, JIT, возможности мониторинга, оповещения и т. д., что и поставщик вычислительных ресурсов.  Масштабируемые наборы соответствуют требованиям стандарта PCI, так как поставщик вычислительных ресурсов является частью аттестации PCI DSS.

Дополнительные сведения см. в следующей статье: [https://www.microsoft.com/TrustCenter/Compliance/PCI](https://www.microsoft.com/TrustCenter/Compliance/PCI).






## <a name="extensions"></a>расширения.

### <a name="how-do-you-delete-a-scale-set-extension"></a>Как удалить расширение масштабируемого набора?

Ниже приведен пример сценария PowerShell.

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
Параметр extensionName находится в строке `$vmss`.
   
### <a name="is-there-a-scale-set-template-example-that-integrates-with-oms"></a>Существует ли пример шаблона масштабируемого набора, который интегрируется с OMS?

Просмотрите второй пример в следующей статье:

https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric.
   
### <a name="extensions-seem-to-run-in-parallel-on-scale-sets-causing-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this-behavior"></a>Похоже, что расширения выполняются в масштабируемых наборах одновременно, за счет чего выполнение операции настраиваемого расширения скриптов завершается сбоем. Как исправить такое поведение?

Сведения см. в следующей статье: https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/. 
 
 
### <a name="how-do-i-reset-the-password-for-scale-set-vms"></a>Как сбросить пароль виртуальных машин в масштабируемом наборе?

Используйте расширения доступа к виртуальной машине.

Ниже приведен пример сценария PowerShell.

```powershell
$vmssName = "myvmss"
$vmssResourceGroup = "myvmssrg"
$publicConfig = @{"UserName" = "newuser"}
$privateConfig = @{"Password" = "********"}
 
$extName = "VMAccessAgent"
$publisher = "Microsoft.Compute"
$vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
$vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName VirtualMachineScaleSet $vmss
```
 
 
### <a name="how-do-i-add-an-extension-to-all-vms-in-my-scale-set"></a>Как добавить расширение для всех виртуальных машин в масштабируемом наборе?

- Если политика обновления настроена на автоматическое обновление, при повторном развертывании шаблона новые свойства расширения применяются на всех виртуальных машинах.
- Если политика обновления настроена на обновление вручную, вы должны обновить расширение, а затем вручную обновить его на всех экземплярах.
  
### <a name="if-the-extensions-associated-with-an-existing-scale-set-are-updated-would-they-affect-already-existing-vms-that-is-would-the-vms-show-up-as-not-matching-the-scale-set-model-or-would-they-be-ignored-when-an-existing-machine-is-service-healed--reimaged--etc-would-the-scripts-that-are-currently-configured-on-the-scale-set-be-executed-or-would-the-ones-that-were-configured-when-the-machine-was-first-created-be-used"></a>Если обновить расширения, связанные с имеющимся масштабируемым набором, повлияет ли это на добавленные в него виртуальные машины? То есть будут ли виртуальные машины рассматриваться как несоответствующие требованиям модели масштабируемого набора? Или они будут игнорироваться? После пересоздания образа или восстановления имеющейся машины будут ли выполняться скрипты, настроенные в настоящее время в масштабируемом наборе, или будет использоваться скрипт, настроенный при первом создании машины?

- Если политика обновления настроена на автоматическое обновление, при обновлении определения расширения в модели масштабируемого набора обновляются и виртуальные машины, а если эта политика настроена на обновление вручную, они помечаются как несоответствующие требованиям модели. 

- Если виртуальная машина восстановлена, она рассматривается как прошедшая перезагрузку, и расширения повторно не запускаются. Пересоздание образа напоминает замену диска ОС на исходный образ. В этом случае запускаются все специализации, например расширения, из последней модели.
 
### <a name="how-do-i-get-a-scale-set-to-join-an-ad-domain"></a>Как присоединить масштабируемый набор к домену AD?

Вы можете определить расширение с использованием JsonADDomainExtension, как показано ниже.
```json
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "joindomain",
                                "properties": {
                                    "publisher": "Microsoft.Compute",
                                    "type": "JsonADDomainExtension",
                                    "typeHandlerVersion": "1.0",
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
 
### <a name="my-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-instance-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>Расширение масштабируемого набора пытается установить что-то, требующее перезагрузки, например "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted Install-WindowsFeature –Name FS-Resource-Manager –IncludeManagementTools".

Вы можете использовать расширение DSC. При использовании операционной системы версии 2012 R2 Azure запрашивает настройку, перезагрузку и последующую конфигурацию WMF 5.0. 
 
### <a name="how-can-i-enable-antimalware-on-my-scale-set"></a>Как включить антивредоносную программу в масштабируемом наборе?

Ниже приведен пример сценария PowerShell.

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# retrieve the most recent version number of the extension
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### <a name="i-need-to-execute-a-custom-script-hosted-on-a-private-storage-account-i-have-no-problems-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signaturesas-it-fails-with-the-error-missing-mandatory-parameters-for-valid-shared-access-signature-i-know-that-linksas-works-fine-from-my-local-browser"></a>Мне нужно выполнить настраиваемый скрипт, размещенный в учетной записи частного хранилища. При использовании общедоступного хранилища проблемы не возникают, но когда я использую подписанный URL-адрес, отображается следующее сообщение об ошибке: "Отсутствуют обязательные параметры для допустимой подписи коллективного доступа". В локальном браузере проблемы со ссылкой и подписанным URL-адресом не возникали.

В этом случае необходимо настроить защищенные параметры с использованием имени и ключа учетной записи хранения. Дополнительные сведения см. в следующей статье: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings.







## <a name="networking"></a>Сеть
 
### <a name="how-do-i-do-vip-swap-for-scale-sets-in-the-same-subscription-and-same-region"></a>Как переключить виртуальный IP-адрес для масштабируемого набора в той же подписке и в том же регионе?

Дополнительные сведения см. в следующей статье: https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/. 
 
  
### <a name="what-is-the-resourceguid-property-on-a-nic-for"></a>Для чего предназначено свойство resourceGuid в сетевом адаптере?

Это уникальный идентификатор. В будущем этот идентификатор будет использоваться для входа в систему из нижних слоев. 
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-for-static-private-ip-address-allocation"></a>Как указать диапазон частных IP-адресов для выделения статических частных IP-адресов?

IP-адреса выбираются из указанной подсети. 

При выделении IP-адресов масштабируемого набора всегда используется динамический метод. Но это не означает, что эти IP-адреса можно изменить. Это только означает, что вам не нужно указывать IP-адрес в запросе PUT. Другими словами, вы указываете статический набор на основе подсети. 
    
### <a name="how-do-i-deploy-a-scale-set-into-an-existing-vnet"></a>Как развернуть масштабируемый набор в имеющейся виртуальной сети? 

Дополнительные сведения см. в следующей статье: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet. 

### <a name="how-do-i-add-a-scale-sets-first-vms-ip-address-to-the-output-of-a-template"></a>Как добавить IP-адрес первой виртуальной машины в масштабируемом наборе к выходным данным шаблона?

Ознакомьтесь со сведениями в следующей статье: http://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips.



## <a name="scale"></a>Масштаб

### <a name="why-would-you-ever-create-a-scale-set-with-fewer-than-2-vms"></a>Зачем нужно создавать масштабируемый набор с одной виртуальной машиной?

Одна из причин заключается в использовании эластичных свойств масштабируемого набора. Например, вы можете развернуть масштабируемый набор без виртуальных машин, чтобы определить инфраструктуру, не платя за выполнение виртуальной машины. Затем при развертывании виртуальных машин вы можете увеличить емкость масштабируемого набора в соответствии с количеством рабочих экземпляров.

Вторая причина заключается в выполнении в масштабируемом наборе операций, не учитывающих доступность в таком же смысле, что и группы доступности с отдельными виртуальными машинами. Масштабируемые наборы предоставляют возможность работать с взаимозаменяемыми недифференцированными единицами вычислений. Это единообразие является ключевым преимуществом при выборе между масштабируемыми наборами и группами доступности. Многие рабочие нагрузки без отслеживания состояния не учитывают отдельные единицы. Они могут уменьшать масштаб до одной единицы вычисления, если рабочая нагрузка снижается, а при увеличении нагрузки — снова увеличивать количество этих единиц.

### <a name="how-do-you-change-the-number-of-vms-in-a-scale-set"></a>Как изменить количество виртуальных машин в масштабируемом наборе?

Дополнительные сведения см. в следующей статье: https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/.

### <a name="how-can-you-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Как можно настроить пользовательские оповещения, отображающиеся при достижении определенных пороговых значений?

Обработкой оповещений можно управлять несколькими способами. Например, вы можете определить настраиваемые объекты webhook, используя пример шаблона Resource Manager, показанный ниже.
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
                     "notifications": [{
                               "operation": "Scale",
                              "email": {
                                     "sendToSubscriptionAdministrator": true,
                                     "sendToSubscriptionCoAdministrators": true,
                                     "customEmails": [
                                        "youremail@address.com"
                                     ]},
                              "webhooks": [{
                                    "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                                    "properties": {
                                        "key1": "custommetric",
                                        "key2": "scalevmss"
                                    }
                                    }
                              ]}],
```

В этом примере при достижении порогового значения оповещение отправляется в службу PagerDuty.



## <a name="troubleshooting"></a>Устранение неполадок

### <a name="how-do-i-enable-boot-diagnostics"></a>Как включить диагностику загрузки?

Создайте учетную запись хранения и вставьте приведенный ниже блок JSON в раздел virtualMachineProfile масштабируемого набора, а затем обновите этот набор.
```json
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": true,
          "storageUri": "http://yourstorageaccount.blob.core.windows.net"
        }
      }
```

Затем после создания виртуальной машины в InstanceView отобразятся сведения для снимка экрана (и тому подобное). например
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```

 

## <a name="updates"></a>Обновления

### <a name="how-to-i-update-my-scale-set-to-a-new-image-and-manage-patching"></a>Как обновить образ и параметры управления установкой исправлений масштабируемого набора?

Дополнительные сведения см. в следующей статье: https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.

### <a name="can-you-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Можно ли использовать операцию пересоздания образа, чтобы сбросить параметры виртуальной машины, не изменяя образ? (То есть сбросить параметры виртуальной машины к значениям по умолчанию, а не создавать новый образ.)

Да. Дополнительные сведения см. в следующей статье: https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set.

Тем не менее, если масштабируемый набор ссылается на образ платформы последней версии, образ ОС виртуальной машины можно обновить на более позднюю версию при вызове операции пересоздания образа.







## <a name="vm-properties"></a>Свойства виртуальной машины

### <a name="how-do-i-get-property-information-for-each-vm-without-having-to-make-multiple-calls-for-example-getting-the-fault-domain-for-each-vm-in-my-100-scale-set"></a>Как получить сведения о свойствах каждой виртуальной машины, не выполняя несколько вызовов? Например, как можно получить сведения о домене сбоя для каждой из 100 виртуальных машин в масштабируемом наборе?

Вы можете вызвать ListVMInstanceViews, выполнив запрос REST API `GET` к следующему универсальному коду ресурса.

`/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView`

### <a name="are-there-ways-to-pass-different-extension-arguments-to-different-vms-in-a-scale-set"></a>Можно ли передать разные аргументы расширения на разные виртуальные машины в масштабируемом наборе?

Нет. Но расширения могут действовать на основе уникальных свойств виртуальной машины, на которых они выполняются, таких как имя машины. Кроме того, расширения могут запрашивать метаданные экземпляра на сайте http://169.254.169.254, чтобы получить дополнительные сведения.

### <a name="why-are-there-gaps-between-my-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Почему между именами виртуальных машин в масштабируемом наборе и их идентификаторами существуют пропуски? Например, 0, 1, 3...

Эти пропуски возникают, потому что для свойства избыточной подготовки масштабируемого набора задано стандартное значение true. Из-за этого создается большее количество виртуальных машин, чем запрашивалось, и впоследствии лишние виртуальные машины удаляются. Это позволяет повысить надежность развертывания, но повлияет на связанные правила преобразования сетевых адресов и именования. Если задать для этого свойства значение false, это незначительно повлияет на надежность развертывания небольших масштабируемых наборов.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-scale-set-vs-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Какова разница между удалением виртуальной машины в масштабируемом наборе и отменой ее подготовки? Как понять, что выбрать в том или ином сценарии?

Основное различие заключается в том, что при отмене подготовки не удаляются виртуальные жесткие диски, и в этом случае взимается плата за хранение. Причины выбора того или иного варианта заключаются в следующем:

- Вы хотите прекратить платить за вычислительные ресурсы, но сохранить состояние диска виртуальных машин.
- Вы хотите запустить набор виртуальных машин быстрее, чем при развертывании масштабируемого набора.
  - Вы создали собственную подсистему автомасштабирования и хотите быстрее выполнить сквозное масштабирование (касательно этого сценария).
  - У вас есть масштабируемый набор, неравномерно распределенный между доменами сбоя и обновления (из-за выборочного удаления и удаления после избыточной подготовки виртуальных машин). Если отменить подготовку, а затем запустить этот процесс в масштабируемом наборе, виртуальные машины равномерно распределятся между доменами сбоя и обновления.








 
   

