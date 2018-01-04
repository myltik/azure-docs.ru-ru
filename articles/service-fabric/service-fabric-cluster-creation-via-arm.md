---
title: "Создание кластера Azure Service Fabric из шаблона | Документация Майкрософт"
description: "В этой статье описывается настройка защищенного кластера Service Fabric в Azure с помощью Azure Resource Manager, Azure Key Vault и Azure Active Directory (Azure AD) для аутентификации клиента."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: chackdan
ms.openlocfilehash: 251f7fc99f1c8d79f31118df11b7522930903c25
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Создание кластера Service Fabric в Azure с помощью Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [портал Azure](service-fabric-cluster-creation-via-portal.md)
>
>

Это пошаговое руководство поможет выполнить настройку защищенного кластера Azure Service Fabric в Azure с помощью Azure Resource Manager. Мы понимаем, что эта статья получилась довольно объемной. Тем не менее, если вы уже хорошо знакомы с ее содержимым, убедитесь, что тщательно выполняете каждое действие.

В этом руководстве описываются следующие процедуры.

* Основные понятия, которые должны учитывать off перед развертыванием кластера service fabric.
* С помощью модулей диспетчера ресурсов службы фабрики для создания кластера в Azure.
* Настройка Azure Active Directory (Azure AD) для проверки подлинности пользователей при выполнении операций управления в кластере.
* Разработка пользовательского шаблона Azure Resource Manager для кластера и его развертывания.

## <a name="key-concepts-to-be-aware-of"></a>Основные понятия, которые следует учитывать
В Azure, которая требует Service fabric использовать x509 сертификат для защиты кластера и ее конечных точек. Сертификаты используются в Service Fabric, чтобы обеспечить функции аутентификации и шифрования для защиты различных аспектов кластера и его приложений. Для клиентского доступа и выполнения операций управления в кластере, включая развертывание, обновление и удаление приложений, служб и данных, содержащихся в них можно использовать сертификаты и учетные данные Azure Active Directory. Использование Azure Active Directory настоятельно рекомендуется, так как это единственный способ запретить совместное использование сертификатов на клиентах.  Дополнительные сведения о том, как сертификаты используются в Service Fabric, см. в статье [Сценарии защиты кластера Service Fabric][service-fabric-cluster-security].

Service Fabric использует сертификаты X.509 для защиты кластера и обеспечения функций безопасности приложений. Вы используете [хранилище ключей] [ key-vault-get-started] для управления сертификатами для кластеров Service Fabric в Azure. 


### <a name="cluster-and-server-certificate-required"></a>Сертификат кластера и сервера (обязательно)
Эти сертификаты (одна первичная и при необходимости вторичная) необходимы для защиты кластера и предотвратить несанкционированный доступ к нему. Он обеспечивает безопасность кластера двумя способами.

* **Аутентификация в кластере** позволяет аутентифицировать обмен данными между узлами для федерации кластера. Только узлы, которые могут подтвердить свою подлинность с помощью этого сертификата, могут присоединиться к кластеру.
* **Проверка подлинности сервера:** выполняет проверку подлинности конечные точки управления кластера клиент управления, чтобы клиент управления известно осуществляет обмен данными в реальном кластера, а не «злоумышленник в середине». Этот сертификат также предоставляет SSL для API управления HTTPS и Service Fabric Explorer по протоколу HTTPS.

Для этого сертификат должен отвечать следующим требованиям:

* Сертификат должен содержать закрытый ключ. Эти сертификаты обычно имеют расширения PFX-файл или PEM  
* Сертификат должен быть создан для обмена ключами, которые можно экспортировать в файл обмена личной информацией (PFX-файл).
* **Имя субъекта сертификата должен соответствовать домену, которая используется для доступа к кластеру Service Fabric**. Это сопоставление не требуется указывать SSL для конечной точки управления HTTPS и обозреватель Service Fabric кластера. Не удается получить SSL-сертификат из центра сертификации (ЦС) для *. cloudapp.azure.com домена. Необходимо получить имя личного домена для кластера. При запросе сертификата из ЦС имя субъекта сертификата должно совпадать с именем личного домена, используемого для кластера.

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>Настройка Azure Active Directory для проверки подлинности клиента (необязательно, но рекомендуется)

Azure AD позволяет организациям (известным как клиенты) управлять доступом пользователей к приложениям. Приложения можно разделить на две группы: те, в которых есть пользовательский веб-интерфейс входа в систему, и те, в которых вход выполняется с помощью собственного клиентского приложения. В этой статье предполагается, что клиент уже создан. Если это не так, обратитесь к статье [Как получить клиент Azure Active Directory][active-directory-howto-tenant].

Кластеры Service Fabric предлагают несколько точек входа для управления функциями кластеров, включая веб-интерфейс [Service Fabric Explorer][service-fabric-visualizing-your-cluster] и [Visual Studio][service-fabric-manage-application-in-visual-studio]. В итоге вы получаете два приложения Azure AD для управления доступом к кластеру: одно веб-приложение и одно собственное приложение.

Дополнительные сведения о настройке позднее в этом документе.

### <a name="application-certificates-optional"></a>Сертификаты приложения (необязательно)
В кластере можно установить любое количество дополнительных сертификатов для обеспечения безопасности приложений. Перед созданием кластера рассмотрите сценарии безопасности приложений, которые требуют установки сертификатов на узлах, в том числе:

* шифрование и расшифровка значений конфигурации приложений;
* шифрование данных между узлами во время репликации.

Концепция создания безопасного кластер в Linux или Windows ничем не отличается. 

### <a name="client-authentication-certificates-optional"></a>Сертификаты проверки подлинности клиента (необязательно)
Администратор или пользователь работе клиента можно указать любое количество дополнительных сертификатов. По умолчанию сертификат кластера имеет привилегии администратора клиента. Эти дополнительные клиентские сертификаты не должны устанавливаться в кластере, необходимо только указать как разрешенные в конфигурации кластера, тем не менее, они должны быть установлены на клиентских компьютерах для подключения к кластеру и выполнения любого управления операции.


## <a name="prerequisites"></a>Технические условия 
Концепция создания безопасного кластер в Linux или Windows ничем не отличается. В этом руководстве рассматривается использование azure powershell или azure CLI для создания новых кластеров. Либо находятся необходимые компоненты 

-  [Azure PowerShell 4.1 и более поздних версий] [ azure-powershell] или [Azure CLI 2.0 и более поздних версий][azure-CLI].
-  Подробные сведения можно найти на fabic модулей службы- [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) и [модуль az SF CLI](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>Служба структуры RM модуль используется для развертывания кластера

В этом документе используем powershell структуры RM службы и модуль CLI для развертывания кластера, powershell или команду CLI модуля позволяет для нескольких сценариев. Сообщите нам, проходят через каждый из них. Подбор сценарий, что вы считаете, что наиболее подходящие для потребностей. 

- Создание нового кластера — с помощью системы создается самозаверяющий сертификат
    - Использовать шаблон по умолчанию для кластера
    - Использование шаблона, который уже имеется.
- Создание нового кластера — с помощью сертификата, которым вы владеете
    - Использовать шаблон по умолчанию для кластера
    - Использование шаблона, который уже имеется.

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>Создание нового кластера - с помощью системы создается самозаверяющий сертификат

Используйте следующую команду для создания кластера, если у вас есть, чтобы система создать самозаверяющий сертификат и использовать его для защиты кластера. Эта команда устанавливает сертификат первичного кластера, который используется для обеспечения безопасности кластера и Настройка административного доступа для выполнения операций управления, с помощью этого сертификата.

### <a name="login-in-to-azure"></a>вход в Azure.

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```
#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module-to-set-up-the-cluster"></a>Используйте шаблон nodetype узла 1 по умолчанию равен 5, который поставляется в модуле настройки кластера

Используйте следующую команду для создания кластера быстро, указывая минимальные параметры

Шаблон, который используется на [образцы шаблона службы azure fabric: шаблон windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) и [Ubuntu шаблона](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Команды ниже подходит для создания кластеров Windows и Linux, необходимо просто соответствующим образом указать операционной системы. Powershell или команды CLI также выдает сертификат сертификатов в указанный в theCertificateOutputFolder. Другие параметры, как Конфигурация виртуальной Машины также принимает команды.

```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

```

```CLI

declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!1"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"



az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Использовать пользовательский шаблон, который уже имеется. 

Если необходимо создать пользовательский шаблон, в соответствии с потребностями, настоятельно рекомендуется начать с одного из шаблонов, доступных на [образцы шаблона службы azure fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Выполните инструкции и пояснения к [настроить шаблон кластера] [ customize-your-cluster-template] разделе ниже.

Если вы уже пользовательский шаблон, затем убедитесь, убедитесь, что значения и проверьте, что все три сертификата, касающиеся следующие имена параметров в шаблоне и файл параметров имеют значение null следующим образом.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```Powershell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Вот эквивалентную команду CLI сделать то же самое. Измените значения в инструкции declare с соответствующими значениями. CLI поддерживает все параметры, которые поддерживает вышеуказанная команда powershell.

```CLI

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>Создание нового кластера - с помощью сертификата вы приобрели в центре сертификации или у вас уже есть.

Используйте следующую команду для создания кластера, если у вас есть сертификат, который вы хотите использовать для защиты кластера с.

Если это сертификата, подписанного ЦС, то приведет к использованию в других целях, а также затем рекомендуется предоставить группе различных ресурсов, специально для хранилища ключей. Рекомендуется поместить хранилище ключей в собственной группе ресурсов. Так вы сможете удалять группы ресурсов для вычислений и хранения, включая группу ресурсов, в которой размещен кластер Service Fabric, без потери ключей и секретов. **Имя группы ресурсов, содержащий ваше хранилище ключа _должен находиться в том же регионе_ в кластере, на котором он используется.**


#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module"></a>Используйте шаблон nodetype узла 1 по умолчанию равен 5, который поставляется в модуле
Шаблон, который используется на [azure образцы: шаблон windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) и [Ubuntu шаблона](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

```Powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!1" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

```

```CLI

declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser

```

#### <a name="use-the-custom-template-that-you-have"></a>Использовать пользовательский шаблон, который у вас есть 
Если необходимо создать пользовательский шаблон, в соответствии с потребностями, настоятельно рекомендуется начать с одного из шаблонов, доступных на [образцы шаблона службы azure fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Выполните инструкции и пояснения к [настроить шаблон кластера] [ customize-your-cluster-template] разделе ниже.

Если вы уже пользовательский шаблон, затем убедитесь, убедитесь, что значения и проверьте, что все три сертификата, касающиеся следующие имена параметров в шаблоне и файл параметров имеют значение null следующим образом.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword #certPassword

```

Вот эквивалентную команду CLI сделать то же самое. Измените значения в инструкции declare с соответствующими значениями.

```CLI

declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

#### <a name="use-a-pointer-to-the-secret-you-already-have-uploaded-into-the-keyvault"></a>Используйте указатель секрета, который вы уже отправили в keyvault

Чтобы использовать существующее хранилище ключей, _необходимо включить его для развернутой службы_, благодаря чему поставщик вычислительных ресурсов сможет получить из него сертификаты и установить его на узлах кластера:

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment


$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroup -SecretIdentifier $secretID -TemplateFile $templateFile -ParameterFile $templateParmfile 

```
Вот эквивалентную команду CLI сделать то же самое. Измените значения в инструкции declare с соответствующими значениями.

```cli

declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifieraz $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 

```

<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Настройка Azure Active Directory для проверки подлинности клиента

Azure AD позволяет организациям (известным как клиенты) управлять доступом пользователей к приложениям. Приложения можно разделить на две группы: те, в которых есть пользовательский веб-интерфейс входа в систему, и те, в которых вход выполняется с помощью собственного клиентского приложения. В этой статье предполагается, что клиент уже создан. Если это не так, обратитесь к статье [Как получить клиент Azure Active Directory][active-directory-howto-tenant].

Кластеры Service Fabric предлагают несколько точек входа для управления функциями кластеров, включая веб-интерфейс [Service Fabric Explorer][service-fabric-visualizing-your-cluster] и [Visual Studio][service-fabric-manage-application-in-visual-studio]. В итоге вы получаете два приложения Azure AD для управления доступом к кластеру: одно веб-приложение и одно собственное приложение.

Чтобы упростить некоторые шаги по настройке Azure AD с кластером Service Fabric, мы создали набор сценариев Windows PowerShell.

> [!NOTE]
> Перед созданием кластера необходимо выполнить следующие действия. Поскольку в сценариях предварительно заданы имена кластеров и конечные точки, эти имена нужно выбрать заблаговременно, при этом они должны отличаться от созданных ранее имен.

1. [Скачайте сценарии][sf-aad-ps-script-download] на компьютер.
2. Щелкните правой кнопкой мыши ZIP-файл, выберите **Свойства**, установите флажок **Разблокировать** и нажмите кнопку **Применить**.
3. Извлеките ZIP-файл.
4. Запустите `SetupApplications.ps1` и укажите TenantId (идентификатор клиента), ClusterName (имя кластера) и WebApplicationReplyUrl (URL-адрес ответа веб-приложения) в качестве параметров. Например: 

```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'

```

Чтобы узнать TenantId, можно выполнить команду PowerShell `Get-AzureSubscription`. После выполнения этой команды для каждой подписки отображается TenantId.

Значение ClusterName используется в качестве префикса для приложений Azure AD, создаваемых сценарием. Точное совпадение с именем реального кластера не требуется. Оно предназначено только для упрощения сопоставления артефактов Azure AD с кластером Service Fabric, с которым они используются.

WebApplicationReplyUrl является конечной точкой по умолчанию, которую Azure AD возвращает пользователям после завершения ими входа. Эту конечную точку следует назначить конечной точкой Service Fabric Explorer для кластера, по умолчанию это:

https://&lt;cluster_domain&gt;:19080/Explorer

Вам будет предложено войти в учетную запись с правами администратора для клиента Azure AD. После входа сценарий начнет создавать веб-приложение и собственное приложение для представления кластера Service Fabric. При рассмотрении приложений клиента в [портал Azure][azure-portal], вы увидите два новых элемента:

   * *имя_кластера*\_Кластер
   * *имя_кластера*\_Клиент

Этот скрипт выводит код JSON для шаблона Azure Resource Manager, который вы будете использовать в следующем разделе для создания кластера. Рекомендуем не закрывать пока окно PowerShell.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a id="customize-arm-template" ></a>

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Создание шаблона Resource Manager для кластера Service Fabric
Этот раздел предназначен для пользователей, желающих получить пользовательский создания шаблона диспетчера ресурсов кластера Service Fabric. При наличии шаблона, по-прежнему можно вернуться и использовать powershell или CLI модули для ее развертывания. 

Пример диспетчера ресурсов шаблоны доступны в [Azure примерами на GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Их можно использовать в качестве отправной точки для создания шаблона кластера.

### <a name="create-the-resource-manager-template"></a>Создание шаблона Resource Manager
В этом руководстве используется пример шаблона [защищенного кластера с 5 узлами][service-fabric-secure-cluster-5-node-1-nodetype] и его параметры. Скачайте файлы `azuredeploy.json` и `azuredeploy.parameters.json` на компьютер и откройте их в текстовом редакторе.

### <a name="add-certificates"></a>Добавление сертификатов
Сертификаты добавляются в шаблон Resource Manager кластера с помощью ссылок на хранилище ключей, которое содержит ключи сертификата. Добавление этих параметров хранилища ключей и значений в файл параметров шаблона диспетчера ресурсов (azuredeploy.parameters.json). 

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Добавление всех сертификатов в osProfile набора масштабирования виртуальных машин
Каждый сертификат, который устанавливается в кластере, должен быть настроен в разделе osProfile ресурса VMSS (Microsoft.Compute/virtualMachineScaleSets). Это указывает, что поставщик ресурсов должен установить сертификат на виртуальных машинах. Эта установка содержит сертификат кластера, а также все сертификаты безопасности приложений, которые планируется использовать для приложений.

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>Настройка сертификата кластера Service Fabric
Сертификат аутентификации кластера должен быть настроен в ресурсе кластера Service Fabric (Microsoft.ServiceFabric/clusters) и расширении Service Fabric для масштабируемых наборов виртуальных машин в ресурсе масштабируемого набора виртуальных машин. Это позволяет поставщику ресурсов Service Fabric настроить его, чтобы использовать для аутентификации кластера и сервера на конечных точках управления.

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Добавление ресурса набора масштабирования виртуальных машин сведения о сертификате.
```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Добавьте сведения о сертификате в ресурс кластера Service Fabric:
```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Добавление конфигурации Azure AD с помощью Azure AD для клиентского доступа

Конфигурация Azure AD s Добавление шаблона диспетчера ресурсов кластера, ссылаясь на хранилище ключей, содержащий ключи сертификата. Добавьте эти параметры Azure AD и значения в файл параметров шаблона диспетчера ресурсов (azuredeploy.parameters.json).

```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### <a name="populate-the-parameter-file-with-the-values"></a>Заполните файл со значениями параметров.
Наконец используйте выходные значения из хранилища ключей и команд powershell Azure AD для заполнения файла параметров:

Если вы планируете использовать модули powershell RM структуры службы Azure, затем не необходимо заполнить сведения о сертификате кластера, если вам требуется систему для создания подписи самозаверяющего сертификата для безопасности кластера, достаточно разместить их как значения null. 

> [!NOTE]
> Для модулей RM взять и заполнить эти значения пустой параметр имена параметров должны совпадать имена ниже
>

```json
        "clusterCertificateThumbprint": {
            "value": ""
        },
        "clusterCertificateUrlValue": {
            "value": ""
        },
        "sourceVaultvalue": {
            "value": ""
        },
```

Если вы используете сертификаты приложения или используете существующий кластер, отправленными keyvault, необходимо получить эту информацию и заполнить его 

Модули RM имеют возможность geneate конфигурации Azure AD для вас. Поэтому если вы планируете использовать Azure AD для клиентского доступа, необходимо заполнить его.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

### <a name="test-your-template"></a>Тестировать шаблон  
Используйте следующую команду PowerShell для тестирования с помощью файла параметров шаблона диспетчера ресурсов:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Если возникают проблемы, а получения зашифрованная сообщений, затем с помощью «-Отладка» в качестве альтернативы.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

На приведенной ниже схеме показано, как конфигурация хранилища ключей и Azure AD помещается в шаблон Resource Manager.

![Сопоставление зависимостей Resource Manager][cluster-security-arm-dependency-map]

## <a name="create-the-cluster-using-azure-resource-template"></a>Создание кластера с помощью шаблона ресурсов Azure 

Теперь можно развернуть кластер с помощью действия, описанные ранее в этом документе, или если значения в файле параметров заполняется, затем теперь вы готовы для создания кластера с помощью [развертывания шаблона ресурсов Azure] [ resource-group-template-deploy] напрямую.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Если возникают проблемы, а получения зашифрованная сообщений, затем с помощью «-Отладка» в качестве альтернативы.


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Назначение пользователей ролям
Создав приложения, представляющие кластер, сопоставьте пользователей с ролями, поддерживаемыми Service Fabric и разрешающими доступ на чтение и администрирование. Можно назначать роли с помощью [портал Azure][azure-portal].

1. На портале Azure выберите клиента в правом верхнем углу.

    ![Нажмите кнопку "клиент"][select-tenant-button]
2. Выберите **Azure Active Directory** на левой вкладке, а затем выберите «Enterprise приложений».
3. Выберите «Все приложения», найдите и выберите веб-приложения, который имеет имя, например `myTestCluster_Cluster`.
4. Нажмите кнопку **пользователей и групп** вкладки.

    ![Вкладка «пользователи и группы»][users-and-groups-tab]
5. Нажмите кнопку **добавить пользователя** кнопку на новую страницу, выберите пользователя и роль, и нажмите кнопку **выберите** , расположенную в нижней части страницы.

    ![Назначить пользователей в роли страницы][assign-users-to-roles-page]
6. Нажмите кнопку **назначить** , расположенную в нижней части страницы.

    ![Добавление назначения подтверждения][assign-users-to-roles-confirm]

> [!NOTE]
> Дополнительные сведения о ролях в Service Fabric см. в статье [Контроль доступа на основе ролей для клиентов Service Fabric](service-fabric-cluster-security-roles.md).
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>По устранению неполадок при установке Azure Active Directory
Настройка Azure AD и его использования, может оказаться сложной задачей, поэтому воспользуйтесь следующими советами по возможности для отладки проблемы.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer предлагает выбрать сертификат
#### <a name="problem"></a>Проблема
После успешного входа в Azure AD в Service Fabric Explorer браузер отображает домашнюю страницу, но на экране отображается предложение выбрать сертификат.

![Диалоговое окно сертификата SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Причина
Пользователю не назначена роль в приложении кластера Azure AD. Таким образом, аутентификация Azure AD в кластере Service Fabric завершается ошибкой. Service Fabric Explorer воспользуется проверкой подлинности на основе сертификата.

#### <a name="solution"></a>Решение
Следуйте инструкциям по настройке Azure AD и назначению ролей пользователей. Кроме того, рекомендуется включить параметр "Для доступа к приложению требуется назначение пользователей", как делает сценарий `SetupApplications.ps1`.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Подключение с помощью PowerShell завершается ошибкой: "Указанные учетные данные недействительны".
#### <a name="problem"></a>Проблема
После успешного входа в Azure AD при использовании PowerShell для подключения к кластеру с помощью режима безопасности AzureActiveDirectory возникла ошибка, подключение завершается с ошибкой: "Указанные учетные данные недействительны".

#### <a name="solution"></a>Решение
Решение этой проблемы такое же, как и в предыдущем случае.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer при входе возвращает ошибку "AADSTS50011"
#### <a name="problem"></a>Проблема
При попытке входа на страницу Azure AD в Service Fabric Explorer выдается ошибка: "AADSTS50011: адрес ответа &lt;URL-адрес&gt; не совпадает с адресами ответа, настроенными для приложения: &lt;guid&gt;".

![Несовпадение адреса ответа в Service Fabric Explorer][sfx-reply-address-not-match]

#### <a name="reason"></a>Причина
Приложение кластера (веб-приложение), представляющее Service Fabric Explorer, пытается выполнить аутентификацию в Azure AD и в составе запроса предоставляет URL-адрес ответа для перенаправления. URL-адрес не указан в списке **URL-АДРЕС ОТВЕТА** приложения Azure AD.

#### <a name="solution"></a>Решение
Выберите «Регистрация приложения» в AAD страницу, выберите приложение кластера и выберите **URL-адреса ответа** кнопки. На странице «URL-адреса ответа» добавить в список URL-адрес Service Fabric Explorer или заменить один из элементов в списке. После завершения сохраните изменения.

![URL-адрес ответа веб-приложения][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Подключение к кластеру с помощью аутентификации Azure AD с использованием PowerShell
Чтобы подключить кластер Service Fabric, воспользуйтесь следующим примером команды PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Дополнительные сведения о командлете Connect-ServiceFabricCluster см. в [этой статье](https://msdn.microsoft.com/library/mt125938.aspx).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Можно ли повторно использовать один и тот же клиент Azure AD в нескольких кластерах?
Да. Не забудьте добавить URL-адрес Service Fabric Explorer в (веб-)приложение кластера. В противном случае — Service Fabric Explorer не работает.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Почему мне все еще нужен сертификат сервера при включенном Azure AD?
FabricClient и FabricGateway выполняют взаимную аутентификацию. Во время аутентификации Azure AD интеграция Azure AD предоставляет удостоверение клиента серверу и для аутентификации сервера используется сертификат сервера. Дополнительные сведения о сертификатах Service Fabric см. в разделе [Сертификаты X.509 и Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Дальнейшие действия
На этом этапе у вас имеется защищенный кластер с Azure Active Directory для аутентификации управления. Далее [подключитесь к этому кластеру](service-fabric-connect-to-secure-cluster.md) и узнайте, как [управлять секретами приложений](service-fabric-application-secret-management.md).


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric
[customize-your-cluster-template]: service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[select-tenant-button]: ./media/service-fabric-cluster-creation-via-arm/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-via-arm/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

