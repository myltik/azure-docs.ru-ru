---
title: Как развернуть модель как веб-службу в FPGA с помощью Машинного обучения Azure
description: Узнайте, как развернуть веб-службу с моделью, работающей на базе FPGA, с использованием Машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: f3237980a1ad1969b5cf8d42d547ddf96608dd97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33784473"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Как развернуть модель как веб-службу в FPGA с помощью Машинного обучения Azure

В этом документе вы узнаете, как настроить среду рабочей станции и развернуть модель в виде веб-службы в [программируемых пользователем вентильных матрицах (FPGA)](concept-accelerate-with-fpgas.md). Веб-служба использует Project Brainwave для запуска модели в FPGA.

При использовании FPGA обеспечивается сверхнизкая задержка даже в случае пакетов одного размера.

## <a name="create-an-azure-machine-learning-model-management-account"></a>Создание учетной записи Управления моделями Машинного обучения Azure

1. Перейдите на страницу создания учетной записи Управления моделями на [портале Azure](https://aka.ms/aml-create-mma).

2. На портале создайте учетную запись Управления моделями в регионе **восточная часть США 2**.

   ![Снимок экрана создания учетной записи Управления моделями](media/how-to-deploy-fpga-web-service/azure-portal-create-mma.PNG)

3. Дайте вашей учетной записи Управления моделями имя, выберите подписку и группу ресурсов.

   >[!IMPORTANT]
   >Для местоположения следует выбрать регион **восточная часть США 2**.  В настоящее время другие регионы не поддерживаются.

4. Выберите ценовую категорию (S1 достаточно, но S2 и S3 также доступны).  Ценовая категория DevTest не поддерживается.  

5. Щелкните **Выбрать**, чтобы подтвердить ценовую категорию.

6. Нажмите кнопку**Создать** в разделе "Управление моделями Машинного обучения" слева.

## <a name="get-model-management-account-information"></a>Получение сведений об учетной записи Управления моделями

Чтобы получить сведения об учетной записи Управления моделями (MMA), щелкните __Model Management Account__ (Учетная запись Управления моделями) на портале Azure.

Скопируйте значения следующих параметров:

+ Имя учетной записи Управления моделями (в верхнем левом углу).
+ Имя группы ресурсов
+ Идентификатор подписки
+ Расположение (используйте eastus2).

![Сведения об учетной записи управления моделями](media/how-to-deploy-fpga-web-service/azure-portal-mma-info.PNG)

## <a name="set-up-your-machine"></a>Настройка компьютера

Чтобы настроить рабочую станцию ​​для развертывания FPGA, выполните следующие действия:

1. Скачайте и установите последнюю версию [Git](https://git-scm.com/downloads).

2. Установите [Anaconda (Python 3.6)](https://conda.io/miniconda.html).

3. Чтобы скачать среду Anaconda, используйте следующую команду из командной строки Git:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```

4. Чтобы создать среду, откройте **командную строку Anaconda** (а не командную строку Azure Machine Learning Workbench) и выполните следующую команду:

    > [!IMPORTANT]
    > Файл `environment.yml` находится в репозитории git, который вы клонировали в предыдущем шаге. При необходимости измените путь, чтобы указать на файл на рабочей станции.

    ```
    conda env create -f environment.yml
    ```

5. Включите среду, выполнив следующую команду:

    ```
    conda activate amlrealtimeai
    ```

6. Чтобы запустить сервер Jupyter Notebook, используйте следующую команду:

    ```
    jupyter notebook
    ```

    После выполнения этой команды вы должны увидеть текст, аналогичный приведенному ниже.

    ```text
    Copy/paste this URL into your browser when you connect for the first time, to login with a token:
        http://localhost:8888/?token=bb2ce89cc8ae931f5df50f96e3a6badfc826ff4100e78075
    ```

    > [!TIP]
    > При каждом выполнении команды вы будете получать другой токен.

    Если в вашем браузере не открывается автоматически страница Jupyter Notebook, используйте HTTP-адрес, возвращенный предыдущей командой, чтобы открыть страницу.

    ![Изображение веб-страницы Jupyter Notebook](./media/how-to-deploy-fpga-web-service/jupyter-notebook.png)

## <a name="deploy-your-model"></a>Развертывание модели

Из Jupyter Notebook откройте записную книжку `00_QuickStart.ipynb` из каталога `notebooks/resnet50`. Следуйте указаниям в блокноте, чтобы:

* Определить службу.
* Развертывание модели
* Использовать развернутую модель.
* Удалить развернутые службы.

> [!IMPORTANT]
> Чтобы оптимизировать задержку и пропускную способность, ваша рабочая станция должна находиться в том же регионе Azure, что и конечная точка.  В настоящее время API создаются в регионе Azure "Восточная часть США".

## <a name="ssltls-and-authentication"></a>SSL/TLS и аутентификация

Машинное обучение Azure обеспечивает поддержку SSL и аутентификацию на основе ключей. Это позволит вам ограничивать доступ к вашей службе и защищать данные, предоставляемые клиентами.

> [!NOTE]
> Шаги в этом разделе применимы только к моделям Машинного обучения Azure с поддержкой аппаратного ускорения. Сведения о стандартных службах Машинного обучения Azure см. в статье [Включение SSL в кластере Azure Machine Learning Compute (MLC)](https://docs.microsoft.com/azure/machine-learning/preview/how-to-setup-ssl-on-mlc).

> [!IMPORTANT]
> Аутентификация разрешена только для служб, которые предоставили SSL-сертификат и ключ. 
>
> Если вы не включите SSL, любой пользователь в Интернете сможет совершать вызовы в службу.
>
> Если вы включите SSL, при доступе к службе потребуется ключ аутентификации.

SSL шифрует данные, отправляемые между клиентом и службой. Он также используется клиентом для проверки удостоверения сервера.

Вы можете развернуть службу с включенным протоколом SSL или обновить уже развернутую службу, чтобы включить его. Выполняемые действия идентичны.

1. Получите имя домена.

2. Получите SSL-сертификат.

3. Разверните или обновите службу с включенным протоколом SSL.

4. Обновите DNS, чтобы указать службу.

### <a name="acquire-a-domain-name"></a>Получите имя домена.

Если у вас еще нет имени домена, вы можете приобрести его у __регистратора доменных имен__. Процесс различается у разных регистраторов, как и стоимость. Регистратор также предоставляет вам инструменты для управления доменным именем. Эти инструменты используются для сопоставления полного доменного имени (например, www.contoso.com) с IP-адресом, по которому размещается ваша служба.

### <a name="acquire-an-ssl-certificate"></a>Получение SSL-сертификата

Существует множество способов получить SSL-сертификат. Наиболее распространенным является покупка в одном из __центров сертификации__. Независимо от того, где вы получаете сертификат, вам нужны следующие файлы:

* __Сертификат__. Сертификат должен содержать полную цепочку сертификатов и должен быть в кодировке PEM.
* __Ключ__. Ключ должен быть в кодировке PEM.

> [!TIP]
> Если центр сертификации не может предоставить сертификат и ключ в виде файлов в кодировке PEM, вы можете использовать такую служебную программу, как [OpenSSL](https://www.openssl.org/), чтобы изменить формат.

> [!IMPORTANT]
> Эти самозаверяющие сертификаты следует использовать только для разработки. Они не должны использоваться в рабочей среде.
>
> Если вы используете самозаверяющий сертификат, см. инструкции в разделе [Использование служб с помощью самозаверяющих сертификатов](#self-signed).

> [!WARNING]
> При запросе сертификата вы должны предоставить полное доменное имя (FQDN) адреса, который вы планируете использовать для этой службы. Например, www.contoso.com. Адрес, указанный в сертификате, и адрес, используемый клиентами, сравниваются при проверке удостоверения службы.
>
> Если адреса не совпадают, клиенты получат сообщение об ошибке. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>Развертывание или обновление службы с включенным протоколом SSL

Чтобы развернуть службу с включенным SSL, установите для параметра `ssl_enabled` значение `True`. Установите для параметра `ssl_certificate` значение файла __сертификата__, а для параметра `ssl_key` значение __ключа__. В следующем примере показано развертывание службы с включенным SSL:

```python
from amlrealtimeai import DeploymentClient

subscription_id = "<Your Azure Subscription ID>"
resource_group = "<Your Azure Resource Group Name>"
model_management_account = "<Your AzureML Model Management Account Name>"
location = "eastus2"

model_name = "resnet50-model"
service_name = "quickstart-service"

deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)

with open('cert.pem','r') as cert_file:
    with open('key.pem','r') as key_file:
        cert = cert_file.read()
        key = key_file.read()
        service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
```

Ответ операции `create_service` содержит IP-адрес службы. IP-адрес используется при сопоставлении имени DNS с IP-адресом службы.

Ответ также содержит __первичный__ и __вторичный ключ__, используемые для применения службы.

### <a name="update-your-dns-to-point-to-the-service"></a>Обновите DNS, чтобы указать службу.

Используйте инструменты, предоставленные вашим регистратором доменных имен, чтобы обновить запись DNS для вашего доменного имени. Запись должна указывать на IP-адрес службы.

> [!NOTE]
> В зависимости от регистратора и срока жизни, настроенного для имени домена, может потребоваться от нескольких минут до нескольких часов, прежде чем клиенты смогут разрешить имя домена.

### <a name="consuming-authenticated-services"></a>Использование аутентифицированных служб

В следующих примерах показано, как использовать аутентифицированную службу с помощью Python и C #:

> [!NOTE]
> Замените `authkey` первичным или вторичным ключом, возвращаемым при создании службы.

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

Другие клиенты gRPC могут аутентифицировать запросы, установив заголовок авторизации. Общий подход заключается в создании объекта `ChannelCredentials`, который объединяет `SslCredentials` с `CallCredentials`. Это добавляется в заголовок авторизации запроса. Дополнительные сведения о реализации поддержки конкретных заголовков см. на странице [https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html).

В следующих примерах показано, как установить заголовок для C# и Go:

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

```go
conn, err := grpc.Dial(serverAddr, 
    grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
    grpc.WithPerRPCCredentials(&authCreds{
    Key: "authKey"}))

type authCreds struct {
    Key string
}

func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "authorization": c.Key,
    }, nil
}

func (c *authCreds) RequireTransportSecurity() bool {
    return true
}
```

### <a id="self-signed"></a>Использование служб с помощью самозаверяющих сертификатов

Существует два способа, позволяющих клиенту выполнить аутентификацию на сервере, защищенном с использованием самозаверяющего сертификата:

* В системе клиента установите переменную среды `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH`, чтобы указать файл сертификата.

* При создании объекта `SslCredentials` передайте содержимое файла сертификата в конструктор.

Использование любого из методов приведет к тому, что gRPC будет использовать сертификат в качестве корневого сертификата.

> [!IMPORTANT]
> gRPC не будет принимать ненадежные сертификаты. Использование ненадежного сертификата завершится ошибкой с кодом состояния `Unavailable`. Подробные сведения об ошибке будут содержать сообщение `Connection Failed`.
