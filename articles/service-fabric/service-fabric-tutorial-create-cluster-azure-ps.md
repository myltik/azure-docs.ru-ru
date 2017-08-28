---
title: "Создание кластера Service Fabric в Azure | Документы Майкрософт"
description: "Узнайте, как создать кластер Service Fabric c Windows или Linux в Azure с помощью PowerShell."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/13/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 80c5a2a43302e1cc8ec3b4298eb393a1861252d3
ms.contentlocale: ru-ru
ms.lasthandoff: 08/16/2017

---

# <a name="create-a-secure-cluster-on-azure-using-powershell"></a>Создание безопасного кластера в Azure с помощью PowerShell
В этом учебнике показано, как создать кластер Service Fabric (с Windows или Linux) в Azure. После окончания этого учебника у вас будет кластер в облаке, в который можно разворачивать приложения.

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание защищенного кластера Service Fabric в Azure с помощью PowerShell
> * Защита кластера с помощью сертификата X.509
> * Подключение к кластеру с помощью PowerShell
> * Удаление кластера

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим руководством выполните следующие действия:
- Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Установите [пакет SDK для Service Fabric и модуль PowerShell](service-fabric-get-started.md)
- Установите модуль [Azure PowerShell версии 4.1 или более поздней](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

В следующей процедуре создается кластер предварительной версии Service Fabric с одним узлом (одной виртуальной машиной). Кластер защищен с помощью самозаверяющего сертификата, который создается вместе с кластером и помещается в хранилище ключей. Кластеры из одного узла не поддерживают масштабирование за пределы одной виртуальной машины, кроме того, кластеры предварительной версии нельзя обновить до последующих версий.

Чтобы рассчитать затраты, связанные с запуском кластера Service Fabric в Azure, используйте [калькулятор цен Azure](https://azure.microsoft.com/pricing/calculator/).
Дополнительные сведения о создании кластеров Service Fabric см. в статье [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="create-the-cluster-using-azure-powershell"></a>Создание кластера с помощью Azure PowerShell
1. Скачайте локальную копию шаблона Azure Resource Manager и файл параметров из репозитория GitHub [шаблон Azure Resource Manager для Service Fabric](https://aka.ms/securepreviewonelineclustertemplate).  *azuredeploy.json* — шаблон Azure Resource Manager, который определяет кластер Service Fabric. *azuredeploy.parameters.json* — файл параметров для настройки развертывания кластера.

2. Настройте следующие параметры в файле параметров *azuredeploy.parameters.json*:

   | Параметр       | Описание | Рекомендуемое значение |
   | --------------- | ----------- | --------------- |
   | clusterLocation | Регион Azure, в котором развертывается кластер. | *Например, westeurope, eastasia, eastus* |
   | clusterName     | Имя создаваемого кластера. | *Например, bobs-sfpreviewcluster* |
   | adminUserName   | Учетная запись локального администратора виртуальных машин кластера. | *Любое допустимое имя пользователя Windows Server* |
   | adminPassword   | Пароль локального администратора виртуальных машин кластера. | *Любой допустимый пароль Windows Server* |
   | clusterCodeVersion | Выполняемая версия Service Fabric. (255.255.X.255 — предварительные версии). | **255.255.5718.255** |
   | vmInstanceCount | Число виртуальных машин в кластере (может быть 1 или 3–99). | **1** | *Для предварительной версии кластера укажите только одну виртуальную машину* |

3. Откройте консоль Windows PowerShell, войдите в Azure и выберите подписку, в которую нужно развернуть кластер:

   ```powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```
4. Создайте и зашифруйте пароль для сертификата, который будет использовать платформа Service Fabric.

   ```powershell
   $pwd = "<your password>" | ConvertTo-SecureString -AsPlainText -Force
   ```
5. Создайте кластер с сертификатом, выполнив следующую команду:

   ```powershell
      New-AzureRmServiceFabricCluster
          -TemplateFile C:\Users\me\Desktop\azuredeploy.json `
          -ParameterFile C:\Users\me\Desktop\azuredeploy.parameters.json `
          -CertificateOutputFolder C:\Users\me\Desktop\ `
          -CertificatePassword $pwd `
          -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
          -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >Параметр `-CertificateSubjectName` должен соответствовать параметру clusterName, указанному в файле параметров, а домен необходимо привязать к выбранному региону Azure, например `clustername.eastus.cloudapp.azure.com`.

По завершении настройки отобразятся сведения о кластере, созданном в Azure. А сертификат кластера будет скопирован в каталог -CertificateOutputFolder в пути, указанном для этого параметра. Этот сертификат понадобится вам для доступа к Service Fabric Explorer и просмотра сведений о работоспособности кластера.

Запишите URL-адрес кластера, который выглядит примерно так: *https://mycluster.westeurope.cloudapp.azure.com:19080*.

## <a name="modify-the-certificate--access-service-fabric-explorer"></a>Изменение сертификата и параметров доступа к Service Fabric Explorer ##

1. Дважды щелкните сертификат, чтобы запустить мастер импорта сертификатов.

2. Используйте параметры по умолчанию, но не забудьте установить флажок **Пометить этот ключ как экспортируемый** в шаге **Защита закрытого ключа**. Visual Studio необходимо экспортировать сертификат при настройке реестра контейнеров Azure, чтобы выполнить проверку подлинности кластера Service Fabric далее в этом руководстве.

3. Теперь можно открыть обозреватель Service Fabric в браузере. Для этого в браузере перейдите к URL-адресу, указанному в значении параметра **ManagementEndpoint** для кластера, и выберите сертификат, сохраненный на компьютере.

>[!NOTE]
>При открытии обозревателя Service Fabric появится сообщение об ошибке сертификата, так как используется самозаверяющий сертификат. В браузере Edge необходимо щелкнуть *Сведения*, а затем ссылку *Перейти на веб-страницу*. В браузере Chrome нужно щелкнуть *Дополнительно*, а затем ссылку *Продолжить*.

>[!NOTE]
>В случае сбоя создания кластера можно повторно запустить команду, которая обновляет уже развернутые ресурсы. Если сертификат создан в ходе неудачного развертывания, создается новый сертификат. Сведения об устранении неполадок при создании кластера см. в статье [Создание кластера Service Fabric с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="connect-to-the-secure-cluster"></a>Подключение к безопасному кластеру
Подключитесь к кластеру с помощью модуля Service Fabric PowerShell, который установлен вместе с пакетом SDK для Service Fabric.  Сначала установите сертификат в личное хранилище (Мое хранилище) текущего пользователя компьютера.  Выполните следующую команду PowerShell:

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Теперь все готово для подключения к защищенному кластеру.

Модуль PowerShell **Service Fabric** предоставляет многие командлеты для управления кластерами, приложениями и службами Service Fabric.  Для подключения к безопасному кластеру используйте командлет [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster). Сведения об отпечатке сертификата и конечной точке подключения можно найти в выходных данных из предыдущего шага.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Убедитесь, что подключение установлено и кластер находится в работоспособном состоянии, выполнив командлет [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Помимо собственных ресурсов кластер содержит другие ресурсы Azure. Чтобы удалить кластер и все ресурсы, который он использует, проще всего удалить группу ресурсов.

Войдите в Azure и выберите идентификатор подписки, в которой вы хотите удалить кластер.  Идентификатор подписки можно узнать, войдя на [портал Azure](http://portal.azure.com). Удалите группу ресурсов и все ресурсы кластера с помощью командлета [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId "Subcription ID"

$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="next-steps"></a>Дальнейшие действия
Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание кластера Service Fabric в Azure
> * Защита кластера с помощью сертификата X.509
> * Подключение к кластеру с помощью PowerShell
> * Удаление кластера

Теперь перейдите к следующему руководству, из которого вы узнаете, как развернуть существующее приложение.
> [!div class="nextstepaction"]
> [Развертывание существующего приложения .NET с помощью Docker Compose](service-fabric-host-app-in-a-container.md)

