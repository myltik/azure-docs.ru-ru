
<properties
   pageTitle="Защита кластера Service Fabric: проверка подлинности клиента с помощью Azure Active Directory | Microsoft Azure"
   description="В этой статье описано создание кластера Service Fabric с использованием Azure Active Directory (AAD) для проверки подлинности клиента."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/11/2016"
   ms.author="seanmck"/>

# ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ: создание кластера Service Fabric с использованием Azure Active Directory для проверки подлинности клиента

С помощью Azure Active Directory \(AAD\) можно защитить доступ к конечным точкам управления кластера Service Fabric. Эта статья рассказывает о том, как создать необходимые артефакты AAD, как заполнить их во время создания кластера и как подключиться к этим кластерам впоследствии.

>[AZURE.IMPORTANT] Интеграция AAD с кластерами Service Fabric сейчас находится в предварительной версии. Все описанное в этой статье доступно в среде выполнения Service Fabric версии 5.0, но сейчас использовать ее для кластеров в рабочей среде не рекомендуется.

## Моделирование кластера Service Fabric в AAD

AAD позволяет организациям \(известным как клиенты\) управлять доступом пользователей к приложениям, которые делятся на приложения с веб-интерфейсом входа и приложения с собственным интерфейсом входа клиента. В этом документе предполагается, что клиент уже создан. Если это не так, обратитесь к статье [Как получить клиент Azure Active Directory](../active-directory/active-directory-howto-tenant.md).

Кластеры Service Fabric предлагают множество точек входа для управления функциями кластеров, включая веб-интерфейс [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) и [Visual Studio](service-fabric-manage-application-in-visual-studio.md). В ходе выполнения действий, описанных в этой статье, вы создадите два приложения AAD для управления доступом к кластеру: веб-приложение и собственное приложение.

Чтобы упростить некоторые шаги по настройке AAD с кластером Service Fabric, мы создали набор сценариев Windows PowerShell.

>[AZURE.NOTE] Эти действия необходимо выполнить *перед* созданием кластера, поэтому в тех случаях, когда сценариям необходимы имена кластеров и конечные точки, нужно использовать плановые значения, а не те кластеры и конечные точки, которые вы уже создали.

1. Для выполнения дальнейших действий [скачайте скрипты][sf-aad-ps-script-download] и извлеките их.

2. Запустите `SetupApplications.ps1`, указав TenantId \(идентификатор клиента\), ClusterName \(имя кластера\) и WebApplicationReplyUrl \(URL-адрес ответа веб-приложения\) в качестве параметров. Например:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Для определения **TenantId** взгляните на URL-адрес клиента на классическом портале Azure. Идентификатор GUID, включенный в этот URL-адрес, и есть идентификатор клиента TenantId. Например:

    https://<i></i>manage.windowsazure.com/microsoft.onmicrosoft.com\#Workspaces/ActiveDirectoryExtension/Directory/\*\*690ec069-8200-4068-9d01-5aaf188e557a\*\*/users

    Имя кластера **ClusterName** будет использоваться в качестве префикса для приложений AAD, создаваемых сценарием. Это имя может не совпадать с фактическим именем кластера, так как оно лишь позволяет связать артефакты AAD с кластерами Service Fabric, с которыми они используются.

    **WebApplicationReplyUrl** — это конечная точка по умолчанию, в которую AAD возвратит пользователей после входа. В этом параметре следует указать конечную точку Service Fabric Explorer для кластера, по умолчанию это:

    https://&lt;cluster_domain&gt;:19080/Explorer

    Вам будет предложено войти в учетную запись с правами администратора для клиента AAD. После этого сценарий перейдет к созданию веб-приложения и собственного приложения для представления кластера Service Fabric. Если посмотреть на список приложений клиента на [классическом портале Azure][azure-classic-portal], вы увидите два новых приложения:

    - *ClusterName*\_Cluster
    - *ClusterName*\_Client

    Скрипт выведет код JSON, необходимый для шаблона Azure Resource Manager \(ARM\) при создании кластера в следующем разделе, поэтому не закрывайте окно PowerShell.

    ![Выходные данные сценария SetupApplication включают код JSON, необходимый для шаблона ARM][setupapp-script-output]

## Создание кластера

После создания приложений AAD можно создать кластер Service Fabric. Сейчас портал Azure не поддерживает проверку подлинности AAD для кластеров Service Fabric, поэтому ее нужно настроить с помощью шаблона ARM в Visual Studio или PowerShell.

Обратите внимание, что AAD применяется только для проверки подлинности клиента в кластере. Чтобы создать безопасный кластер, необходимо также указать сертификат, который будет использоваться для защиты связи между узлами кластера и проверки подлинности сервера для конечных точек управления кластера. Вы можете воспользоваться [шаблоном ARM для защиты кластера в коллекции быстрого запуска Azure][secure-cluster-arm-template] или выполнить инструкции, содержащиеся в файле readme для [проекта группы ресурсов Service Fabric в Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

Укажите параметры узла из выходных данных шаблона ARM для сценария `SetupApplication` в параметрах fabricSettings, managementEndpoint и т. д. Если вы закрыли окно, выходные данные также приведены ниже:

```json
  "azureActiveDirectory": {
    "tenantId": "<your_tenant_id>",
    "clusterApplication": "<your_cluster_application_client_id>",
    "clientApplication": "<your_native_application_client_id>"
  }
```

Параметр clusterApplication ссылается на веб-приложение, созданное в предыдущем разделе. Его идентификатор можно найти в выходных данных сценария SetupApplication \(он указан как `WebAppId`\). Параметр clientApplication ссылается на собственное приложение, и его идентификатор клиента доступен в выходных данных сценария SetupApplication как NativeClientAppId.

## Назначение пользователей ролям

После создания приложений, представляющих кластер, необходимо назначить пользователей ролям, поддерживаемым Service Fabric: read-only и admin. Это можно сделать на [классическом портале Azure][azure-classic-portal].

1. Перейдите к своему клиенту и выберите "Приложения".
2. Выберите веб-приложение с именем, похожим на `myTestCluster_Cluster`.
3. Откройте вкладку "Пользователи".
4. Выберите пользователя для назначения и нажмите кнопку **Назначить** в нижней части экрана.

    ![Кнопка "Назначить пользователей ролям"][assign-users-to-roles-button]

5. Выберите роль для назначения пользователю.

    ![Назначение пользователей ролям][assign-users-to-roles-dialog]

>[AZURE.NOTE] Дополнительные сведения о ролях в Service Fabric см. в разделе [Контроль доступа на основе ролей для клиентов Service Fabric](service-fabric-cluster-security-roles.md).

## Подключение к кластеру

При переходе к Service Fabric Explorer в кластере с поддержкой AAD автоматически откроется страница для безопасного входа в систему.

Для подключения из собственного клиента, например Visual Studio или Windows PowerShell, будет необходимо указать AAD в качестве механизма входа в систему и затем предоставить отпечаток сертификата сервера, который служит для проверки удостоверения конечной точки. Ниже приведены подробные сведения для этих двух точек входа.

### Подключение из Visual Studio

В Visual Studio можно изменить профиль публикации, добавив необходимые атрибуты, как показано ниже:

```xml
<ClusterConnectionParameters     
    ConnectionEndpoint="<your_cluster_endpoint>:19000"  
    AzureActiveDirectory="true"
    ServerCertThumbprint="<your_cert_thumbprint>"
    />
```

При публикации в кластер Visual Studio откроет окно входа в систему, в котором можно пройти проверку подлинности для кластера.

![Окно входа в систему AAD при публикации в Visual Studio][vs-publish-aad-login]

### Подключение с помощью Windows PowerShell

В PowerShell можно указать необходимые параметры командлета Connect-ServiceFabricCluster, как показано ниже:

```PowerShell
Connect-ServiceFabricCluster -AzureActiveDirectory -ConnectionEndpoint <cluster_endpoint>:19000 -ServerCertThumbprint <server_cert_thumbprint>
```

Как и в Visual Studio, в PowerShell будет открыто окно безопасного входа в систему для проверки подлинности.

>[AZURE.NOTE] По умолчанию шлюз TCP Service Fabric, используемый Visual Studio и PowerShell, прослушивает порт 19000. Если вы настроили другой порт, следует использовать его при указании конечной точки подключения.

## Известные проблемы

### Ошибка проверки подлинности для собственного клиента из-за несовпадения адреса ответа

При проверке подлинности собственного клиента, например Visual Studio или PowerShell, может появиться следующее сообщение об ошибке:

*Адрес ответа http://localhost/ не совпадает с адресом ответа, который настроен для приложения \<GUID\_клиентского\_приложения\_в\_кластере\>*

Чтобы это обойти, добавьте **http://<i></i>localhost** в качестве URI перенаправления в определение клиентского приложения в кластере для AAD, в дополнение к адресу 'urn:ietf:wg:oauth:2.0:oob', который уже указан.

## Дальнейшие действия

- Ознакомьтесь с дополнительными сведениями о [защите кластера Service Fabric](service-fabric-cluster-security.md).
- Узнайте, как [опубликовать приложение на удаленный кластер с помощью Visual Studio](service-fabric-publish-app-remote-cluster.md).

<!-- Links -->
[sf-aad-ps-script-download]: http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[secure-cluster-arm-template]: https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad
[aad-graph-api-docs]: https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com

<!-- Images -->
[assign-users-to-roles-button]: ./media/service-fabric-cluster-security-client-auth-with-aad/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-security-client-auth-with-aad/assign-users-to-roles.png
[setupapp-script-output]: ./media/service-fabric-cluster-security-client-auth-with-aad/setupapp-script-arm-json-output.png
[vs-publish-aad-login]: ./media/service-fabric-cluster-security-client-auth-with-aad/vs-login-prompt.png

<!---HONumber=AcomDC_0413_2016-->