<properties
   pageTitle="Пакет SDK Java для диспетчера ресурсов Azure| Microsoft Azure"
   description="Обзор примеров проверки подлинности использования пакета SDK Java для диспетчера ресурсов"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="navale;tomfitz;"/>
   
# Пакет SDK Java для диспетчера ресурсов Azure
   
Пакеты SDK предварительной версии для диспетчера ресурсов Azure (ARM) доступны для нескольких языков и платформ. Каждая из этих языковых реализаций доступна в экосистеме диспетчеров пакетов и на сайте GitHub.

Код в каждом из этих пакетов SDK генерируется из [спецификаций RESTful API Azure](https://github.com/azure/azure-rest-api-specs). Эти спецификации с открытым исходным кодом основаны на спецификации Swagger v2. Код пакета SDK создается в проекте с открытым исходным кодом, который называется [AutoRest](https://github.com/azure/autorest). AutoRest преобразует эти спецификации RESTful API в клиентские библиотеки на нескольких языках. Если требуется изменить какие-либо аспекты созданного кода в пакетах SDK, можно воспользоваться открытым, бесплатным, поддерживающим распространенный формат спецификации API набором средств для создания пакетов SDK.

Пакет SDK Java для диспетчера ресурсов Azure размещен в GitHub в [репозитории пакетов SDK Azure для Java](https://github.com/azure/azure-sdk-for-java). Обратите внимание, что на момент написания этой статьи доступна предварительная версия пакета SDK. Доступны перечисленные далее пакеты:

* Управление вычислениями: (azure-mgmt-compute)
* Управление DNS: (azure-mgmt-dns)
* Управление сетью: (azure-mgmt-network)
* Управление ресурсами: (azure-mgmt-resources)
* Управление SQL: (azure-mgmt-sql)
* Управление хранилищем: (azure-mgmt-storage)
* Управление диспетчером трафика: (azure-mgmt-traffic-manager)
* Служебные программы и вспомогательные методы: (azure-mgmt-utility)
* Управление веб-сайтами и веб-приложениями: (azure-mgmt-websites)

Актуальный список пакетов SDK Azure см. [на вики-странице компонентов для Java](https://github.com/Azure/azure-sdk-for-java/wiki/Azure-SDK-for-Java-Features).

## Предварительные требования
1. Java версии 1.6 и более поздней
2. [maven](https://maven.apache.org/), если вы хотите вести разработку в пакете SDK.

## Получение пакета SDK
Начать работу с пакетом SDK Azure для Java рекомендуется с использованием распределенных JAR-файлов [Maven](https://maven.apache.org/). Эти зависимости можно добавить во многие средства управления зависимостями Java (Maven, Gradle, Ivy...). Перейдите по этой [ссылке](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.azure%22), чтобы получить список библиотек, доступных в maven.

Кроме того, пакет SDK можно получить непосредственно из источника с помощью Git. Чтобы получить исходный код пакета SDK через Git, введите:

    git clone https://github.com/Azure/azure-sdk-for-java.git
    cd ./azure-sdk-for-java/

(В примерах в этом обзоре в качестве источника пакетов SDK будет использоваться Maven.)

Пакет SDK содержит примеры для некоторых сценариев, например для проверки подлинности, подготовки виртуальной машины и т. д. Все примеры можно найти в репозитории GitHub [azure-mgmt-samples](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples).

## Вспомогательные классы

В состав пакета SDK входят вспомогательные классы для ряда основных пакетов. Следующие вспомогательные классы реализованы в пакете auzre-mgmt-utility:

* AuthHelper — вспомогательный класс проверки подлинности;
* ComputeHelper — вспомогательный класс вычислений;
* NetworkHelper — вспомогательный класс сетей;
* ResourceHelper — вспомогательный класс развертываний;
* StorageHelper — вспомогательный класс хранилищ.

**Сведения о зависимостях Maven**

    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-utility</artifactId>
      <version>0.9.1</version>
    </dependency>

Будет использоваться пакет служебной программы версии 0.9.1.

## Аутентификация

Проверку подлинности для ARM обрабатывает Azure Active Directory (AD). Чтобы подключиться к любому API, сначала необходимо пройти проверку подлинности в Azure AD для получения маркера проверки подлинности, который можно передать в каждый запрос. Чтобы получить этот маркер, нужно создать приложение Azure AD и субъект-службу, которая будет использоваться для входа. Пошаговые инструкции см. в статье [Создание приложения Azure AD и субъекта-службы](./resource-group-create-service-principal-portal.md).

После создания субъекта-службы у вас должны быть указанные ниже значения:
* Идентификатор клиента (GUID)
* Секрет клиента (строка)
* Идентификатор клиента (GUID) или имя домена (строка). При наличии этих значений можно получить маркер доступа Active Directory, действующий в течение часа.

Пакет SDK для Java содержит вспомогательный класс AuthHelper, который создает маркер доступа после указания идентификатора клиента (client id), секрета и идентификатора клиента (tenant id). В следующем примере в классе [ServicePrincipalExample](https://github.com/Azure/azure-sdk-for-java/blob/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/authentication/ServicePrincipalExample.java) используется метод AuthHelper *getAccessTokenFromServicePrincipalCredentials* для получения маркера доступа.

```java
public static Configuration createConfiguration() throws Exception {
   String baseUri = System.getenv("arm.url");

   return ManagementConfiguration.configure(
         null,
         baseUri != null ? new URI(baseUri) : null,
         System.getenv(ManagementConfiguration.SUBSCRIPTION_ID),
         AuthHelper.getAccessTokenFromServicePrincipalCredentials(
                  System.getenv(ManagementConfiguration.URI), System.getenv("arm.aad.url"),
                  System.getenv("arm.tenant"), System.getenv("arm.clientid"),
                  System.getenv("arm.clientkey"))
                  .getAccessToken());
}
```

## Создание виртуальной машины 
В пакете служебной программы содержится вспомогательный класс [ComputeHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ComputeHelper.java) для создания виртуальной машины. Несколько примеров по работе с виртуальными машинами можно найти в пакете azure-mgmt-samples в разделе [compute](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/compute).

Ниже приведен простой поток для создания виртуальной машины. В этом примере в процессе создания виртуальной машины вспомогательный класс создаст хранилище и сеть.

```java
public static void main(String[] args) throws Exception {
        Configuration config = createConfiguration();
        ResourceManagementClient resourceManagementClient = ResourceManagementService.create(config);
        StorageManagementClient storageManagementClient = StorageManagementService.create(config);
        ComputeManagementClient computeManagementClient = ComputeManagementService.create(config);
        NetworkResourceProviderClient networkResourceProviderClient = NetworkResourceProviderService.create(config);

        String resourceGroupName = "javasampleresourcegroup";
        String region = "EastAsia";

        ResourceContext context = new ResourceContext(
                region, resourceGroupName, System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);

        System.out.println("Start create vm...");

        try {
            VirtualMachine vm = ComputeHelper.createVM(
                    resourceManagementClient, computeManagementClient, networkResourceProviderClient, 
                    storageManagementClient,
                    context, vnName, vmAdminUser, vmAdminPassword)
              .getVirtualMachine();

            System.out.println(vm.getName() + " is created");
        } catch (Exception ex) {
            System.out.println(ex.toString());
        }
}
```

## Развертывание шаблона
Класс [ResouceHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ResourceHelper.java) был создан для упрощения процесса развертывания шаблона ARM с помощью пакета SDK для Java.

```java
// create a new resource group
ResourceManagementClient resourceManagementClient = createResourceManagementClient();
ResourceContext resourceContext = new ResourceContext(
        resourceGroupLocation, resourceGroupName,
        System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);
ComputeHelper.createOrUpdateResourceGroup(resourceManagementClient, resourceContext);

// create the template deployment
DeploymentExtended deployment = ResourceHelper.createTemplateDeploymentFromURI(
        resourceManagementClient,
        resourceGroupName,
        DeploymentMode.Incremental,
        deploymentName,
        TEMPLATE_URI,
        "1.0.0.0",
        parameters);
```

Дополнительные примеры можно найти в примерах пакетов в разделе [templatedeployments](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/templatedeployments).

## Дополнительные материалы и помощь
Документация по пакету SDK Azure для Java: [документы по Java](http://azure.github.io/azure-sdk-for-java/). При возникновении проблем с пакетом SDK сообщите об ошибке на странице [Issues](https://github.com/Azure/azure-sdk-for-java/issues) или просмотрите [StackOverflow для пакета SDK Azure Java](http://stackoverflow.com/questions/tagged/azure-java-sdk).

<!---HONumber=AcomDC_0316_2016-->