---
title: "Использование управляемых удостоверений служб виртуальной машины Azure для входа и получения маркеров"
description: "Пошаговая инструкция по использованию субъекта-службы MSI виртуальной машины Azure для входа и получения маркера доступа."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: bryanla
ms.openlocfilehash: 905e7b0d8a0c45c98a86882a8c8f387be0950f9f
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in-and-token-acquisition"></a>Использование управляемых удостоверений служб виртуальной машины Azure (MSI) для входа и получения маркеров 
[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)] После включения MSI на виртуальной машине Azure его можно использовать для входа в систему и запроса маркера доступа. В этой статье показаны различные способы использования [субъекта-службы](develop/active-directory-dev-glossary.md#service-principal-object) MSI для входа в систему и получения [маркера доступа только для приложений](develop/active-directory-dev-glossary.md#access-token) для доступа к другим ресурсам, включая:

- автоматический вход в систему из PowerShell или Azure CLI;
- получение маркера для различных клиентов, в том числе .NET, PowerShell, Bash/CURL, REST;
- вход с помощью пакета SDK для Azure, включая .NET, Java, Node.js, Python, Ruby.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Если вы планируете использовать примеры PowerShell в этой статье, убедитесь, что установлен [Azure PowerShell версии 4.3.1](https://www.powershellgallery.com/packages/AzureRM) или более поздней. Если вы планируете использовать примеры Azure CLI, есть три варианта:
- использовать [Azure Cloud Shell](../cloud-shell/overview.md) с портала Azure;
- использовать внедренный компонент Azure Cloud Shell с помощью кнопки "Попробуйте!", расположенной в правом верхнем углу блока кода Azure CLI;
- [установить последнюю версию интерфейса командной строки 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 или более позднюю версию), если вы предпочитаете использовать CLI в локальной командной строке. 


> [!IMPORTANT]
> - Во всех примерах кода или скриптов в этой статье предполагается, что клиент выполняется на виртуальной машине с поддержкой MSI. Дополнительные сведения о включении MSI на виртуальной машине см. в статье [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью портала Azure](msi-qs-configure-portal-windows-vm.md) или в одном из вариантов статей (с использованием PowerShell, CLI, шаблона или пакета SDK для Azure). 
> - Во избежание ошибок авторизации (403/AuthorizationFailed) в примерах кода или сценария удостоверению виртуальной машины необходимо предоставить доступ для чтения в области виртуальной машины, чтобы разрешить выполнение операций Azure Resource Manager на виртуальной машине. Дополнительные сведения см. в статье [Назначение доступа на основе управляемого удостоверения службы (MSI) для ресурса с помощью портала Azure](msi-howto-assign-access-portal.md).
> - Прежде чем переходить к одному из следующих разделов, удаленно подключитесь к виртуальной машине с поддержкой MSI с помощью возможности "Подключение" виртуальной машины на портале Azure.

## <a name="how-to-sign-in-from-powershell-or-cli-using-msi"></a>Как войти из PowerShell или CLI с помощью MSI

Ранее запуск сценария с собственным идентификатором означал:
- его регистрацию в качестве конфиденциального клиента приложения или приложения веб-клиента в Azure AD;
- вход с использованием идентификатора клиента приложения или секретных учетных данных приложения.

Благодаря MSI скриптам клиента больше не требуется регистрация в Azure AD или предоставление учетных данных. В приведенных ниже примерах демонстрируется использование субъекта-службы MSI виртуальной машины для входа в систему.

### <a name="azure-powershell"></a>Azure PowerShell

Следующий сценарий демонстрирует, как:

- получать маркер доступа MSI для виртуальной машины Azure;
- использовать маркер доступа для входа в Azure AD с соответствующим субъектом-службой MSI;
- использовать субъект-службу MSI для вызова Azure Resource Manager и получения идентификатора субъекта-службы.

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token

# Use the access token to sign in under the MSI service principal
Login-AzureRmAccount -AccessToken $access_token -AccountId “CLIENT”

# The MSI service principal is now signed in for this session.
# Next, a call to Azure Resource Manager is made to get the service principal ID for the VM's MSI. 
$spID = (Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>).identity.principalid
echo "The MSI service principal ID is $spID"
```
   
### <a name="azure-cli"></a>Инфраструктура CLI Azure

Следующий сценарий демонстрирует, как:

- войти в Azure AD с помощью субъекта-службы MSI виртуальной машины;
- использовать субъект-службу MSI для вызова Azure Resource Manager и получения идентификатора субъекта-службы.

```azurecli-interactive
az login --msi
spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
echo The MSI service principal ID is $spID
```

## <a name="how-to-acquire-an-access-token-from-msi"></a>Как получить маркер доступа из MSI

С помощью MSI клиентскому приложению или скрипту больше не требуется регистрация в Azure AD или предоставление идентификатора и секрета клиента для получения маркера доступа. Клиент может просто запросить локальную конечную точку MSI для маркера доступа без предоставления учетных данных приложений. Маркер доступа только для приложений выдается субъекту-службе MSI, и его можно использовать в качестве маркера носителя при [выполнении вызовов между службами, требующих учетных данных клиента](active-directory-protocols-oauth-service-to-service.md).

В приведенных ниже примерах показано использование MSI виртуальной машины для получения маркера.

### <a name="net"></a>.NET

> [!IMPORTANT]
> Библиотека проверки подлинности Azure AD (ADAL) не интегрирована с MSI. Чтобы получить маркер доступа с помощью MSI, сделайте запрос к локальной конечной точке MSI в виртуальной машине. Дополнительные сведения см. в разделе [Часто задаваемые вопросы (FAQ)](msi-known-issues.md#frequently-asked-questions-faqs).

```csharp
// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

### <a name="azure-powershell-token"></a>Azure PowerShell

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"
```

### <a name="bashcurl"></a>Bash/Curl

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

### <a name="rest"></a>REST

Пример запроса:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Элемент | Описание |
| ------- | ----------- |
| `GET` | HTTP-команда, указывающая, что необходимо извлечь данные из конечной точки. В этом случае используется маркер доступа OAuth. | 
| `http://localhost:50342/oauth2/token` | Конечная точка MSI, в которой порт 50342 является портом по умолчанию, который можно настроить. |
| `resource` | Параметр строки запроса, указывающий URI идентификатора приложения целевого ресурса. Он также отображается в утверждении (аудитории) `aud` выданного маркера. В этом примере запрашивается маркер безопасности для доступа к Azure Resource Manager, который имеет универсальный код ресурса (URI) идентификатора приложения https://management.azure.com/. |
| `Metadata` | Поле заголовка HTTP-запроса, требуемое MSI с целью устранения рисков от атак с подделкой запроса со стороны сервера (SSRF). Должно быть присвоено значение true (все в нижнем регистре).

Пример ответа:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Элемент | Описание |
| ------- | ----------- |
| `access_token` | Запрашиваемый маркер доступа. При вызове REST API маркер внедряется в поле запроса заголовка `Authorization` в качестве маркера носителя, позволяя API выполнить проверку подлинности вызывающего объекта. | 
| `refresh_token` | Не используется MSI. |
| `expires_in` | Количество секунд, в течение которых маркер доступа является действительным, прежде чем его срок действия истечет (с момента выдачи). Время выдачи можно найти в утверждении `iat` маркера. |
| `expires_on` | Период времени после истечения срока действия маркера доступа. Дата представляется как количество секунд с 1970-01-01T0:0:0Z в формате UTC (соответствует утверждению `exp` маркера). |
| `not_before` | Период времени, когда маркер доступа вступает в силу и может быть принят. Дата представляется как количество секунд с 1970-01-01T0:0:0Z в формате UTC (соответствует утверждению `nbf` маркера). |
| `resource` | Ресурс, для которого был запрошен маркер доступа, соответствует параметру строки запроса `resource`. |
| `token_type` | Тип маркера, который является маркером доступа носителя, что значит, что ресурс может предоставлять доступ носителю этого маркера. |

## <a name="how-to-use-msi-with-azure-sdk-libraries"></a>Как использовать MSI-файл с библиотеками пакетов Azure SDK

Благодаря [пакетам SDK Azure](https://azure.microsoft.com/downloads) в Azure реализована поддержка разных платформ программирования. Некоторые из них обновлены для поддержки входа в систему с помощью MSI. Для них предоставляются соответствующие примеры для демонстрации использования. Этот список обновляется по мере добавления поддержки дополнительных платформ:

| SDK | Образец |
| --- | ------ | 
| .NET | [Развертывание шаблона ARM с виртуальной машины Windows с помощью управляемого удостоверения службы](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core | [Вызов служб Azure с виртуальной машины Linux с помощью управляемого удостоверения службы](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js| [Manage resources using Managed Service Identity using node.js](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) (Управление ресурсами, используя управляемое удостоверение службы с помощью Node.js) |
| Python | [Use MSI to authenticate simply from inside a VM](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) (Использование MSI для простой проверки подлинности внутри виртуальной машины) |
| Ruby   | [Управление ресурсами из виртуальных машин с поддержкой MSI](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) | 

## <a name="additional-information"></a>Дополнительная информация

### <a name="token-expiration"></a>Срок действия маркера

Локальная подсистема MSI кэширует маркеры. Таким образом их можно вызывать столько, сколько нужно, а сетевой вызов Azure AD выполняется, только если:
- произошел промах кэша из-за отсутствия маркера в кэше;
- истек срок действия маркера.

Если маркер кэшируется в коде, необходимо подготовиться к обработке сценариев, в которых ресурс указывает, что срок действия маркера истек.

### <a name="resource-ids-for-azure-services"></a>Идентификаторы ресурсов для служб Azure

Список служб, поддерживающих MSI, и соответствующие идентификаторы ресурсов см. в разделе [Службы Azure, поддерживающие аутентификацию Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication).

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="sign-in-or-token-acquisition-fails"></a>Сбой входа или получения маркера

Убедитесь, что MSI включен правильно. Вернитесь к виртуальной машине Azure на [портале Azure](https://portal.azure.com) и:

- просмотреть страницу "Конфигурация" и убедиться, что указан параметр "MSI enabled" = "Yes";
- просмотреть страницу "Расширения" и убедиться, что расширение MSI успешно развернуто.

Если какое-либо из условий не выполнено, может потребоваться повторно развернуть MSI для ресурса или устранить неполадки развертывания.

### <a name="http-request-error-responses"></a>Сообщения об ошибках в результате HTTP-запроса

- *bad_request_102. Необходимый заголовок метаданных не указан.*

  Поле заголовка запроса `Metadata` отсутствует или имеет неправильный формат. Должно быть указано значение `true` в нижнем регистре. В качестве примера см. "Пример запроса" в [предыдущем разделе для REST](#rest).

- *Неизвестно. Не удалось извлечь маркер из Active Directory. Дополнительные сведения см. в журналах в \<путь к файлу\>*.

  Убедитесь, что универсальный код ресурса (URI) запроса HTTP GET отформатирован правильно, в частности универсальный код ресурса (URI), указанный в строке запроса. В качестве примера см. "Пример запроса" в [предыдущем разделе для REST](#rest) или раздел [Службы Azure, поддерживающие аутентификацию Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) для получения списка служб и соответствующих идентификаторов ресурсов.

- *unknown_source: \<URI\>* неизвестного источника

  Убедитесь, что универсальный код ресурса (URI) запроса HTTP GET имеет правильный формат. Часть `scheme:host/resource-path` должна быть указана как `http://localhost:50342/oauth2/token`. В качестве примера см. "Пример запроса" в [предыдущем разделе для REST](#rest).

## <a name="related-content"></a>Связанная информация

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](msi-overview.md).
- О том, как включить MSI для виртуальной машины Azure, можно узнать в разделе [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью PowerShell](msi-qs-configure-powershell-windows-vm.md).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.

