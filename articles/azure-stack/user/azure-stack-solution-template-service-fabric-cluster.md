---
title: Развертывание защищенного кластера Service Fabric в Azure Stack | Документация Майкрософт
description: Сведения о развертывании защищенного кластера Service Fabric в Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: mattbriggs
ms.reviewer: shnatara
ms.openlocfilehash: a88d8dd2af94ac796a3b2e3c667fd40a308f02a1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33877162"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Развертывание кластера Service Fabric в Azure Stack

Используйте элемент **Кластер Service Fabric** из Azure Marketplace для развертывания защищенного кластера Service Fabric в Azure Stack. 

Дополнительные сведения о работе с Service Fabric см. в статьях [Общие сведения о Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) и [Сценарии защиты кластера Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) в документации Azure.

## <a name="prerequisites"></a>предварительным требованиям

Ниже перечислены необходимые условия для развертывания кластера Service Fabric.
1. **Сертификат кластера**  
   Этот сертификат сервера X.509 вы добавляете в Key Vault при развертывании Service Fabric. 
   - **Имя этого сертификата** должно соответствовать полному доменному имени (FQDN) созданного вами кластера Service Fabric. 
   - Сертификат должен быть PFX-файлом, так как и открытый, и закрытый ключи являются обязательными. 
   Дополнительные сведения см. в разделе с [требованиями](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) для создания сертификата на стороне сервера.

    > [!NOTE]  
    > Вы можете использовать самозаверяющий сертификат вместо сертификата сервера X.509 для целей тестирования. Самозаверяющие сертификаты не обязательно должны совпадать с FQDN кластера.

2.  **Сертификат клиента администрирования** — это сертификат, который клиент будет использовать для проверки подлинности в кластере Service Fabric. Этот сертификат может быть самозаверяющим. Дополнительные сведения см. в разделе с [требованиями](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) для создания клиентского сертификата.

3.  **Следующие компоненты должны быть доступны в Azure Stack Marketplace:**
     - **Windows Server 2016** — этот шаблон использует образ Windows Server 2016 для создания кластера.  
     - **Расширение для пользовательских скриптов** — расширение виртуальной машины от корпорации Майкрософт.  
     - **Настройка требуемого состояния PowerShell** — расширение виртуальной машины от корпорации Майкрософт.


## <a name="add-a-secret-to-key-vault"></a>Добавление секрета в Key Vault
Чтобы развернуть кластер Service Fabric, необходимо указать правильный *идентификатор секрета* Key Vault или URL-адрес для кластера Service Fabric. Шаблон Azure Resource Manager принимает Key Vault в качестве входного, а затем извлекает сертификат кластера во время установки кластера Service Fabric. 

> [!IMPORTANT]  
> Вы должны использовать PowerShell для добавления секрета в Key Vault для использования в Service Fabric. Не используйте портал.  

Используйте следующий скрипт, чтобы создать Key Vault и добавить *сертификат кластера* в него. (См. раздел [Предварительные требования](#prerequisites).) Прежде чем запустить скрипт, просмотрите его пример и обновите указанные параметры в соответствии со своей средой. Этот скрипт также будет выводить значения, которые необходимо указать в шаблоне Azure Resource Manager. 

> [!TIP]  
> Для успешного выполнения скрипта должно быть доступно общедоступное предложение, которое содержит службы для вычисления, сети, хранилища и Key Vault. 

  ```PowerShell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


Дополнительные сведения см. в статье [Управление Key Vault в Azure Stack с использованием PowerShell](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-kv-manage-powershell).

## <a name="deploy-the-marketplace-item"></a>Развертывание элемента Marketplace

1. На пользовательском портале последовательно выберите **Создать** > **Вычисление** > **Кластер Service Fabric**. 

   ![Выбор кластера Service Fabric](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

2. Для каждой страницы, например *Основные сведения*, заполните форму развертывания. Используйте значения по умолчанию, если вы не знаете нужные значения. Нажмите кнопку **ОК** для перехода к следующей странице:

   ![Основы](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

3. На странице *Параметры сети* укажите определенные порты, чтобы открыть их для ваших приложений:

   ![Параметры сети](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

4. На странице *Безопасность* добавьте значения, полученные в результате [создания Azure KeyVault](#add-a-secret-to-key-vault) и передачи секрета.

   В *соответствующем поле* введите отпечаток *сертификата клиента администрирования*. (См. раздел [Предварительные требования](#prerequisites).)
   
   - В поле "Исходное Key Vault" укажите всю строку *идентификатора Key Vault*, содержащуюся в результатах скрипта. 
   - В поле URL-адреса сертификата кластера укажите полный URL-адрес из *идентификатора секрета*, содержащегося в результатах скрипта. 
   - В соответствующем поле укажите *отпечаток сертификата кластера* из результатов скрипта.
   - В соответствующем поле укажите *отпечаток сертификата клиента администрирования*, созданный вами при выполнении предварительных требований. 

   ![Выходные данные скрипта](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Безопасность](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

5. Завершите работу мастера, а затем нажмите кнопку **Создать** для развертывания кластера Service Fabric.



## <a name="access-the-service-fabric-cluster"></a>Доступ к кластеру Service Fabric
Вы можете получить доступ к кластеру Service Fabric, используя Service Fabric Explorer или Service Fabric PowerShell.


### <a name="use-service-fabric-explorer"></a>Использование Service Fabric Explorer
1.  Проверьте, что веб-браузер имеет доступ к сертификату клиента администрирования и может пройти проверку подлинности в кластере Service Fabric.  

    a. Откройте Internet Explorer и последовательно выберите **Свойства обозревателя** > **Содержимое** > **Сертификаты**.
  
    Б. На странице сертификатов выберите **Импорт** для запуска *мастера импорта сертификатов*, а затем нажмите кнопку **Далее**. На странице *File to Import* (Файл для импорта) щелкните **Обзор** и выберите **сертификат клиента администрирования**, который вы указали в шаблоне Azure Resource Manager.
        
       > [!NOTE]  
       > Этот сертификат не является сертификатом кластера, который был добавлен ранее в Key Vault.  

    c. Убедитесь, что в окне проводника в раскрывающемся списке расширений выбрано значение "Файл обмена личной информацией".  

       ![Файл обмена личной информацией](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. На странице *Хранилища сертификатов* выберите **Личное**, а затем завершите работу мастера.  
       ![Хранилище сертификатов](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
2. Чтобы найти FQDN кластера Service Fabric, выполните следующие действия.  

    a. Перейдите к группе ресурсов, связанной с кластером Service Fabric, и найдите ресурс *общедоступного IP-адреса*. Выберите объект, связанный с общедоступным IP-адресом, чтобы открыть колонку *Общедоступный IP-адрес*.  

      ![Общедоступный IP-адрес](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    Б. В колонке "Общедоступный IP-адрес" FQDN отображается как *DNS-имя*.  

      ![DNS-имя](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

3. Чтобы найти URL-адрес для Service Fabric Explorer и конечную точку подключения клиента, просмотрите результаты развертывания шаблона.

4. В браузере перейдите по адресу https://*FQDN*:19080. Замените *FQDN* на полное доменное имя кластера Service Fabric из шага 2.   
   Если вы использовали самозаверяющий сертификат, то получите предупреждение, что соединение не является безопасным. Чтобы перейти на веб-сайт, выберите **More Information** (Подробнее), а затем **Go on to the webpage** (Перейти на веб-страницу). 

5. Для проверки подлинности на сайте нужно выбрать сертификат для использования. Выберите **More choices** (Дополнительные варианты), выберите соответствующий сертификат и нажмите кнопку **ОК** для подключения к Service Fabric Explorer. 

   ![Проверка подлинности](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>Использование Service Fabric PowerShell

1. Установите *пакет SDK для Microsoft Azure Service Fabric*  из раздела [Установка пакета SDK и инструментов](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) документации Azure Service Fabric.  

2. После завершения установки настройте системные переменные среды, чтобы убедиться, что командлеты Service Fabric доступны в PowerShell.  
    
    a. Последовательно выберите **Панель управления** > **Система и безопасность** > **Система**, а затем выберите **Дополнительные параметры системы**.  
    
      ![Панель управления](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    Б. На вкладке **Дополнительно** в *свойствах системы* выберите **Переменные среды**.  

    c. Для *системных переменных* измените **путь** и убедитесь, что **C:\\Program Files\\Microsoft Service Fabric\\bin\\Fabric\\Fabric.Code** находится в верхней части списка переменных среды.  

      ![Список переменных среды](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

3. После изменения порядка переменных среды перезапустите PowerShell и выполните следующий скрипт PowerShell для получения доступа к кластеру Service Fabric:

   ````PowerShell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ````
   
   > [!NOTE]  
   > В скрипте отсутствует *https://* перед именем кластера. Требуется порт 19000.
 
