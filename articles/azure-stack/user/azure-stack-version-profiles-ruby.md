---
title: Использование профилей версий API с помощью Ruby в Azure Stack | Документация Майкрософт
description: Сведения об использовании профилей версий API с помощью Ruby в Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: dd8130ac12f9c7c2095f9329dc4ce8a34187cf62
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "34011220"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Использование профилей версий API с помощью Ruby в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

## <a name="ruby-and-api-version-profiles"></a>Использование профилей версии API с помощью Ruby

Пакет SDK Ruby для Resource Manager Azure Stack предоставляет средства для создания и администрирования инфраструктуры. Поставщики ресурсов в пакете SDK включают вычисления, виртуальные сети и хранилища на языке Ruby. Профили API в пакете SDK Ruby позволяют выполнять разработку гибридных облаков приложений, помогая переключаться между глобальными ресурсами Azure и ресурсами в Azure Stack.

Профиль API состоит из поставщиков ресурсов и версий службы. Профиль API можно использовать для объединения разных типов ресурсов.

 - Чтобы воспользоваться преимуществами последних версий всех служб, используйте **последний** профиль накопительного пакета Azure SDK.
 - Чтобы использовать службы, совместимые с Azure Stack, используйте профиль **V2017_03_09** накопительного пакета Azure SDK.
 - Чтобы использовать последнюю версию API службы, используйте **последний** профиль определенного пакета. Например, если вы хотите использовать только последнюю версию API службы вычислений, используйте **последний** профиль пакета **вычислений**.
 - Чтобы использовать определенную версию API для службы, используйте нужные версии API, определенные в пакете.

> [!Note]   
> Можно объединить все параметры в одном приложении.

## <a name="install-the-azure-ruby-sdk"></a>Установка пакета Azure SDK

 - Выполните официальные инструкции по установке [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
 - Выполните официальные инструкции по установке [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
    - При установке выберите **Добавить Ruby в переменную PATH**.
    - В ходе установки Ruby при появлении запроса установите комплект средств для разработки.
    - Затем установите средство увязки с помощью следующей команды:  
      `Gem install bundler`
 - При необходимости создайте подписку и сохраните ее идентификатор для дальнейшего использования. См. инструкции по [созданию подписки](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
 - Создайте субъект-службу и сохраните его идентификатор и секрет. См. инструкции по [созданию субъекта-службы для Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals). 
 - Убедитесь, что субъект-служба имеет роль участника или владельца в вашей подписке. См. инструкции по [назначению роли субъекта-службы](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

## <a name="install-the-rubygem-packages"></a>Установка пакетов rubygem

Можно установить пакеты azure rubygem напрямую.

````Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
````

Имейте в виду, пакет SDK Ruby для Azure Resource Manager доступен в режиме предварительной версии, следовательно, в будущих выпусках ожидаются критические важные изменения интерфейса. Большое число в дополнительном номере версии может указывать на критически важные изменения.

## <a name="usage-of-the-azuresdk-gem"></a>Использование пакета azure_sdk

Пакет azure_sdk — это коллекция пакетов в составе пакета SDK для Ruby. Этот пакет включает **последний** профиль, который поддерживает последнюю версию всех служб. Он представляет профиль версии **V2017_03_09**, созданный для Azure Stack.

Чтобы установить накопительный пакет azure_sdk, выполните следующую команду:  

````Ruby  
  gem install 'azure_sdk
````

## <a name="prerequisite"></a>Предварительные требования

Чтобы использовать пакет SDK Azure для Ruby и Azure Stack, укажите следующие значения и задайте значения для переменных среды. См. инструкции по указанию переменных среды для вашей операционной системы после таблицы. 

| Значение | Переменные среды | ОПИСАНИЕ | 
| --- | --- | --- | --- |
| Tenant ID | AZURE_TENANT_ID | Значение [идентификатора клиента](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview) Azure Stack. |
| Идентификатор клиента | AZURE_CLIENT_ID | Идентификатор приложения субъекта-службы, сохраненный во время создания субъекта-службы (см. выше).  |
| Идентификатор подписки | AZURE_SUBSCRIPTION_ID | [Идентификатор подписки](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) для доступа к предложениям в Azure Stack. |
| Секрет клиента | AZURE_CLIENT_SECRET | Секрет приложения субъекта-службы, сохраненный во время создания субъекта-службы (см. выше). |
| Конечная точка Resource Manager | ARM_ENDPOINT | См. дополнительные сведения о [конечной точке Resource Manager для Azure Stack](#The-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Конечная точка Resource Manager для Azure Stack

Microsoft Azure Resource Manager — это платформа управления, которая позволяет администраторам развертывать, управлять и администрировать ресурсы Azure. Azure Resource Manager может обрабатывать эти задачи в рамках одной операции как группы, а не по отдельности.

Вы можете получить метаданные из конечной точки Resource Manager. Конечная точка возвращает JSON-файл со сведениями, необходимыми для запуска кода.

  > [!Note]  
  > В Пакете средств разработки Azure Stack (ASDK) **ResourceManagerUrl**: `https://management.local.azurestack.external/`.  
  > В интегрированных системах **ResourceManagerUrl**: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`.  
  > Чтобы получить необходимые метаданные, используйте: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.
  
  Пример JSON-файла:

  ```json
  { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
    "graphEndpoint": "https://graph.windows.net/",  
    "portal Endpoint": "https://portal.local.azurestack.external/", 
    "authentication": {
      "loginEndpoint": "https://login.windows.net/", 
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
    }
  }
  ```

### <a name="set-environmental-variables"></a>Настройка переменных среды

**Microsoft Windows**  
Используйте следующие команды, чтобы настроить переменные среды в командной строке Windows:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**macOS, Linux и системы на основе Unix**  
В системах на основе Unix можно использовать такую команду:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Существующие профили API

Накопительный пакет azure_sdk включает два профиля:

1. **V2017_03_09**  
  Профиль, созданный для Azure Stack. Используйте этот профиль, чтобы обеспечить наибольшую совместимость служб и Azure Stack.
2. **Актуальная**  
  Профиль включает последние версии всех служб. Используйте последние версии всех служб.

См. дополнительные сведения о [профилях API и Azure Stack](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Использование профиля API пакета SDK Azure для Ruby

Чтобы создать профиль клиента, используйте следующие строки. Этот параметр требуется только для Azure Stack и других частных облаков. В глобальной среде Azure эти параметры используются по умолчанию.

````Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
    ENV['AZURE_TENANT_ID'],
    ENV['AZURE_CLIENT_ID'],
    ENV['AZURE_CLIENT_SECRET'],
    active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack
client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
````

Профиль клиента можно использовать для доступа к отдельным поставщикам ресурсов, включая вычисления, хранилища и сети.

````Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2017_03_09::Compute::Mgmt::Models::PurchasePlan.new
````

## <a name="define-azurestack-environment-setting-functions"></a>Определение функций параметров среды Azure Stack

Для аутентификации субъекта-службы в среде Azure Stack определите конечные точки с помощью **get_active_directory_settings()**. Этот метод использует переменную среды **ARM_Endpoint**, заданную при настройке переменных среды.

````Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
        error_model = JSON.load(response_content)
        fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
end
````

## <a name="samples-using-api-profiles"></a>Примеры с профилями API

Вы можете использовать следующие примеры, доступные в репозитории GitHub, при создании решений с помощью профилей API Azure Stack и Ruby:

 - [Управление ресурсами и группами ресурсов Azure на Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)
 - [Управление виртуальными машинами с помощью Ruby](https://github.com/Azure-Samples/compute-ruby-manage-vm/tree/master/Hybrid)
 - [Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона на Ruby](https://github.com/Azure-Samples/resource-manager-ruby-template-deployment/tree/master/Hybrid)

### <a name="sample-resource-manager-and-groups"></a>Пример с Resource Manager и группами

Для запуска примера необходимо установить Ruby. При использовании Visual Studio Code также скачайте пакет SDK для Ruby в качестве расширения. 

> [!Note]  
> Репозиторий с примерами см. здесь: [Управление ресурсами и группами ресурсов Azure на Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid).

1. Клонируйте репозиторий.

    ````Bash
    git clone https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups.git
    ````

2. Установите зависимости с помощью пакета.

    ````Bash
    cd resource-manager-ruby-resources-and-groups\Hybrid\
    bundle install
    ````

3. Создайте субъект-службу Azure с помощью PowerShell и получите необходимые значения. 

  См. дополнительные сведения о [создании субъекта-службы с сертификатом с помощью Azure PowerShell](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

  Требуемые значения:
  - Tenant ID
  - Идентификатор клиента
  - Секрет клиента
  - Идентификатор подписки
  - Конечная точка Resource Manager

  Настройте следующие переменные среды, используя данные, полученные от созданного субъекта-службы.

  - export AZURE_TENANT_ID={your tenant id}
  - export AZURE_CLIENT_ID={your client id}
  - export AZURE_CLIENT_SECRET={your client secret}
  - export AZURE_SUBSCRIPTION_ID={your subscription id}
  - export ARM_ENDPOINT={URL-адрес диспетчера ресурсов AzureStack}

  > [!Note]  
  > В Windows используйте набор вместо экспорта.

4. Убедитесь, что в качестве переменной расположения задано расположение Azure Stack. Например, LOCAL="local"

5. Добавьте следующую строку кода, если вы используете Azure Stack или другие частные облака для выбора правильных конечных точек Active Directory.

  ````Ruby  
  active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
  ````

6. В переменную options добавьте параметры Active Directory и базовый URL-адрес для работы с Azure Stack. 

  ````Ruby  
  options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
  }
  ````

7. Создайте профиль клиента, который предназначен для профиля Azure Stack:

  ````Ruby  
    client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
  ````

8. Для аутентификации субъекта-службы в среде Azure Stack определите конечные точки с помощью **get_active_directory_settings()**. Этот метод использует переменную среды **ARM_Endpoint**, заданную при настройке переменных среды.

  ````Ruby  
  def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
      error_model = JSON.load(response_content)
      fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
  end
  ````

9. Запустите пример.

  ````Ruby
    bundle exec ruby example.rb
  ````

## 

## <a name="next-steps"></a>Дополнительная информация

* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Установка PowerShell для Azure Stack)
* [Configure the Azure Stack user's PowerShell environment](azure-stack-powershell-configure-user.md) (Настройка пользовательской среды PowerShell в Azure Stack)  
