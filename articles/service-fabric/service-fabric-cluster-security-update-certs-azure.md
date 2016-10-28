<properties
   pageTitle="Добавление, смена и удаление сертификатов, используемых в кластере Service Fabric в Azure | Microsoft Azure"
   description="В данной статье описано, как передать дополнительный сертификат кластера и сменить старый основной сертификат."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="chackdan"/>

# Добавление и удаление сертификатов для кластера Service Fabric в Azure

Рекомендуется ознакомиться с тем, как Service Fabric использует сертификаты X.509, и прочитать раздел [Сценарии обеспечения безопасности кластера](service-fabric-cluster-security.md). Необходимо понять, что такое сертификат кластера и для чего он используется, прежде чем продолжить.

Service Fabric позволяет указать два сертификата кластера, основной и дополнительный, при настройке сертификата безопасности во время создания кластера. Чтобы узнать больше, ознакомьтесь с [созданием кластера Azure с помощью портала](service-fabric-cluster-creation-via-portal.md) или [созданием кластера Azure с помощью Azure Resource Manager](service-fabric-cluster-creation-via-Resource Manager.md). Если при развертывании с помощью Resource Manager указан только один сертификат кластера, то он используется в качестве основного сертификата. После создания кластера можно добавить новый сертификат в качестве дополнительного.

>[AZURE.NOTE] Для работы с безопасным кластером всегда требуется хотя бы один действительный (не отозванный и не просроченный) развернутый сертификат (основной или дополнительный). В противном случае кластер не будет функционировать. За 90 дней до истечения срока действия всех действительных сертификатов система создает трассировку "Предупреждение" и событие предупреждения о работоспособности на узле. В настоящее время Service Fabric не отправляет какие-либо электронные сообщения или другие уведомления о данной теме.


## Добавление дополнительного сертификата с помощью портала
Чтобы добавить еще один сертификат в качестве дополнительного, необходимо передать его в хранилище ключей Azure и затем развернуть на виртуальных машинах в кластере. Дополнительные сведения см. в статье [Deploy certificates to VMs from a customer-managed Key Vault](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) (Развертывание сертификатов на виртуальных машинах из хранилища ключей, управляемого клиентом).

1. Инструкции доступны в разделе [Шаг 2. Отправка сертификата X.509 в хранилище ключей](service-fabric-secure-azure-cluster-with-certs.md#step-2-upload-the-x509-certificate-to-the-key-vault).

2. Войдите на [портал Azure](https://portal.azure.com/) и перейдите к ресурсу кластера, к которому необходимо добавить этот сертификат.
3. В разделе **Параметры** щелкните **Безопасность**. Откроется колонка "Cluster Security" (Безопасность кластера).
4. Нажмите кнопку **+Certificate** (+ Сертификат) вверху колонки, чтобы перейти к колонке **Добавление сертификата**.
5. Из раскрывающегося списка выберите "Отпечаток дополнительного сертификата" и заполните отпечаток дополнительного сертификата, переданного вами в хранилище.

>[AZURE.NOTE]
В отличие от процесса создания кластера, мы не описываем подробно сведения о хранилище ключей, так как предполагается, что к моменту перехода к этой колонке вы уже развернули сертификат на виртуальных машинах, и он уже доступен в локальном хранилище сертификатов экземпляра VMSS.

Щелкните **Сертификат**. Начнется развертывание, и в колонке "Cluster Security" (Безопасность кластера) появится синяя полоса состояния.

![Снимок экрана: раздел отпечатков сертификатов на портале][SecurityConfigurations_02]

После успешного завершения развертывания вы сможете использовать основной или дополнительный сертификат, чтобы выполнять операции управления в кластере.

![Снимок экрана выполняемого развертывания сертификата][SecurityConfigurations_03]

Ниже приведен снимок экрана колонки безопасности после завершения развертывания.

![Снимок экрана отпечатков сертификата после развертывания][SecurityConfigurations_08]


Теперь можно использовать только что добавленный новый сертификат для подключения и выполнения операций в кластере.

>[AZURE.NOTE]
В данный момент нет возможности заменить основной сертификат дополнительным на портале. Мы работаем над этой функцией. Пока имеется действительный сертификат, кластер будет работать нормально.

## Добавление дополнительного сертификата и замена им основного с помощью PowerShell для Resource Manager

В этих инструкциях предполагается, что вы ознакомлены с принципами работы Resource Manager и развернули по крайней мере один кластер Service Fabric с помощью шаблона Resource Manager, и у вас под рукой имеется шаблон, который использовался для настройки этого кластера. Предполагается также, что вы уверенно используете JSON.

>[AZURE.NOTE]
Если вам нужен пример шаблона и параметров, которые можно использовать в качестве образца или отправной точки, скачайте его из этого репозитория [git-repo]. (https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

#### Изменение шаблона Resource Manager 

Если вы воспользовались примером из репозитория [git-repo](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) для образца, то вы найдете эти изменения в примере 5-VM-1-NodeTypes-Secure\_Step2.JSON. Использование 5-VM-1-NodeTypes-Secure\_Step1.JSON для развертывания безопасного кластера

1. Откройте шаблон Resource Manager, который использовался для развертывания кластера.
2. Добавьте новый параметр secCertificateThumbprint типа string. Если используется шаблон Resource Manager, скачанный с портала во время создания, или шаблон быстрого запуска, то просто найдите этот параметр, так как он уже определен.
3. Найдите определение ресурса Microsoft.ServiceFabric/clusters. В разделе свойств вы увидите тег Certificate в формате JSON, который должен выглядеть как приведенный ниже фрагмент JSON.
```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('certificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
        }
``` 

4. Добавьте новый тег thumbprintSecondary и присвойте ему значение [parameters('secCertificateThumbprint')].

Теперь определение ресурса должна выглядеть следующим образом (в зависимости от источника шаблона оно может отличаться от приведенного ниже фрагмента). Как видно ниже, вы указываете новый сертификат в качестве основного и делаете текущий основной сертификат дополнительным. В результате текущий сертификат заменяется новым сертификатом за один шаг развертывания.

```JSON

      "properties": {
        "certificate": {
            "thumbprint": "[parameters('certificateThumbprint')]",
            "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },

```

#### Изменение файла шаблона с учетом новых параметров, добавленных выше

Если вы воспользовались примером из репозитория [git-repo](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) для образца, то можете начать изменять пример 5-VM-1-NodeTypes-Secure\_Step2.JSON.


Измените файла параметров шаблона Resource Manager, добавьте новые параметры для secCertificate и поменяйте местами существующие сведения об основном и дополнительном сертификатах, затем замените сведения об основном сертификате сведениями о новом сертификате.

```JSON
    "secCertificateThumbprint": {
      "value": "OLD Primary Certificate Thumbprint"
    },
   "secSourceVaultValue": {
      "value": "OLD Primary Certificate Key Vault location"
    },
    "secCertificateUrlValue": {
      "value": "OLD Primary Certificate location in the key vault"
     },
    "certificateThumbprint": {
      "value": "New Certificate Thumbprint"
    },
    "sourceVaultValue": {
      "value": "New Certificate Key Vault location"
    },
    "certificateUrlValue": {
      "value": "New Certificate location in the key vault"
     },

```

### Развертывание шаблона в Azure

1. Теперь все готово к развертыванию шаблона в Azure. Откройте командную строку Azure PowerShell версии не ниже 1.
2. Войдите со своей учетной записью Azure и выберите конкретную подписку Azure. Это важный шаг для пользователей, имеющих доступ к нескольким подпискам Azure.


```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subcription ID> 

```

Проверьте шаблон перед его развертыванием. Используйте группу ресурсов, в которой развернут кластер.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Разверните шаблон в группу ресурсов. Используйте группу ресурсов, в которой развернут кластер. Выполните команду New-AzureRmResourceGroupDeployment. Не нужно указывать режим, так как по умолчанию используется **добавочный** режим.

>[AZURE.NOTE]
Если задан полный режим, то вы можете случайно удалить ресурсы, которые находятся не в шаблоне. Поэтому не используйте этот режим данном сценарии.
   

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Ниже приведен заполненный пример той же команды PowerShell.

```powershell
$ResouceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResouceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResouceGroup2

```

После завершения развертывания подключитесь к кластеру с помощью нового сертификата и выполните несколько запросов. Если получится это сделать. Затем можно удалить старый основной сертификат.

Если используется самозаверяющий сертификат, то не забудьте импортировать его в локальное хранилище сертификатов TrustedPeople.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Для краткой справки ознакомьтесь с командой для подключения к безопасному кластеру:
```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
Для краткой справки ознакомьтесь с командой для получения данных о работоспособности кластера:
```powershell
Get-ServiceFabricClusterHealth 
```
 
## Удаление старого сертификата с помощью портала
Ниже описан процесс удаления старого сертификата, чтобы кластер больше не использовал его:

1. Войдите на [портал Azure](https://portal.azure.com/) и перейдите к параметрам безопасности кластера.
2. Щелкните правой кнопкой мыши сертификат, который требуется удалить
3. Выберите "Удалить" и следуйте инструкциям на экране.

[SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png


## Дальнейшие действия
Дополнительные сведения об управлении кластерами доступны в следующих статьях.

- [Обновление кластера Service Fabric](service-fabric-cluster-upgrade.md)
- [Настройка доступа на основе ролей для клиентов](service-fabric-cluster-security-roles.md)


<!--Image references-->
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_02.png
[SecurityConfigurations_03]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_03.png
[SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png
[SecurityConfigurations_08]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_08.png

<!----HONumber=AcomDC_0817_2016-->


