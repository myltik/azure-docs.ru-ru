---
title: Настройка SSL для облачной службы | Документация Майкрософт
description: Узнайте, как определить конечную точку HTTPS для веб-роли и как передать SSL-сертификат, чтобы обеспечить безопасность приложения. В этих примерах используется портал Azure.
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: 371ba204-48b6-41af-ab9f-ed1d64efe704
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: adegeo
ms.openlocfilehash: 0e053ad7f1033317948b6ef0856984b21e56e425
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2017
ms.locfileid: "24859782"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Настройка SSL для приложения в Azure

SSL-шифрование — это наиболее распространенный метод обеспечения безопасности данных, отправленных через Интернет. В этой теме, посвященной общим задачам, обсуждается, как определить конечную точку HTTPS для веб-роли и как передать SSL-сертификат, чтобы обеспечить безопасность приложения.

> [!NOTE]
> Процедуры в этом задании применяются для облачных служб Azure. Информацию о процедурах для служб приложений см. в [этом разделе](../app-service/app-service-web-tutorial-custom-ssl.md).
>

В этой задаче используется рабочее развертывание. Сведения об использовании промежуточного развертывания приведены в конце данного раздела.

Если вы еще не создали облачную службу, сначала прочтите [эту статью](cloud-services-how-to-create-deploy-portal.md) .

## <a name="step-1-get-an-ssl-certificate"></a>Шаг 1. Получение SSL-сертификата
Чтобы настроить протокол SSL для приложения, сначала необходимо получить SSL-сертификат, подписанный центром сертификации, то есть доверенным сторонним поставщиком, который выдает сертификаты для этой цели. Если у вас еще нет сертификата, нужно получить его в компании, продающей SSL-сертификаты.

Сертификат должен отвечать следующим требованиям для SSL-сертификатов в Azure:

* Сертификат должен содержать закрытый ключ.
* Сертификат должен быть создан для обмена ключами, которые можно экспортировать в файл обмена личной информацией (PFX-файл).
* Имя субъекта сертификата должно совпадать с именем домена, которое используется для обращения к облачной службе. Не удается получить SSL-сертификат от центра сертификации (ЦС) в домене cloudapp.net. Необходимо получить имя пользовательского домена для использования при получении доступа к службе. При запросе сертификата из ЦС имя субъекта сертификата должно соответствовать имени личного домена, который используется для доступа к вашему приложению. Например, если вы используете домен **contoso.com**, необходимо запросить сертификат из центра сертификации для ***.contoso.com** или **www.contoso.com**.
* Сертификат должен использовать как минимум 2048-разрядное шифрование.

В целях тестирования вы можете [создать](cloud-services-certs-create.md) и использовать самозаверяющий сертификат. Самозаверяющий сертификат не прошел аутентификацию через ЦС, и можно использовать домен cloudapp.net в качестве URL-адреса веб-сайта. Например, в приведенной ниже задаче используется самозаверяющий сертификат, в котором задано общее имя **sslexample.cloudapp.net**.

Далее необходимо включить сведения о сертификате в файлы определения службы и конфигурации службы.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Шаг 2. Изменение файлов определения службы и конфигурации
Ваше приложение должно быть настроено для использования сертификата и также необходимо добавить конечную точку HTTPS. Для этого следует обновить файлы определения службы и конфигурации службы.

1. В среде разработки откройте файл определения службы (CSDEF), добавьте раздел **Certificates** внутри раздела **WebRole** и добавьте следующие сведения о сертификате (и промежуточных сертификатах).

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```

   В разделе **Certificates** определяется имя сертификата, его расположение и имя хранилища, в котором он находится.

   Разрешениям (атрибуту`permisionLevel`) можно присвоить одно из приведенных ниже значений.

   | Значение разрешения | ОПИСАНИЕ |
   | --- | --- |
   | limitedOrElevated |**(По умолчанию).** Все процессы роли могут получить доступ к закрытому ключу. |
   | elevated |Доступ к закрытому ключу могут получить только процессы повышенного уровня. |

2. В файле определения службы добавьте элемент **InputEndpoint** в раздел **Endpoints**, чтобы включить протокол HTTPS.

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443"
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. В файле определения службы добавьте элемент **Binding** в раздел **Sites**. Этот элемент добавляет привязку HTTPS для сопоставления конечной точки с вашим сайтом:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```

   Все необходимые изменения внесены в файл определения службы, но еще нужно добавить сведения о сертификате в сам файл конфигурации службы.
4. В файле конфигурации службы (CSCFG-файл) ServiceConfiguration.Cloud.cscfg в разделе **Certificates** добавьте значения из своего сертификата. Следующий пример кода содержит в разделе **Certificates** все значения, кроме значения thumbprint.

   ```xml
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff"
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc"
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

(В этом примере для алгоритма отпечатка используется обозначение **sha1**. Укажите соответствующее значение для алгоритма отпечатка своего сертификата.)

Теперь, когда файлы определения службы и конфигурации службы обновлены, создайте пакет развертывания для передачи в Azure. Если используется **cspack**, не используйте флаг **/generateConfigurationFile**, так как это приведет к перезаписи сведений о сертификате, которые вы только что добавили.

## <a name="step-3-upload-a-certificate"></a>Шаг 3. Отправка сертификата
Подключитесь к порталу Azure и выполните следующие действия.

1. В разделе портала **Все ресурсы** выберите облачную службу.

    ![Публикация облачной службы](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Щелкните **Сертификаты**.

    ![Щелкните значок «сертификаты»](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Щелкните **Отправить** в верхней части области сертификатов.

    ![Щелкните элемент "Отправить"](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Укажите **файл** и **пароль**, а затем нажмите кнопку **Отправить** в нижней части области ввода данных.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Шаг 4. Подключение к экземпляру роли с использованием HTTPS
Теперь, когда развертывание готово и работает в Azure, вы можете подключиться к нему, используя HTTPS.

1. Щелкните **URL-адрес сайта**, чтобы открыть веб-браузер.

   ![Щелкните URL-адрес сайта](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. В веб-браузере измените ссылку, указав **https** вместо **http**, и перейдите на страницу.

   > [!NOTE]
   > Если используется самозаверяющий сертификат, то при переходе к конечной точке HTTPS, связанной с самозаверяющим сертификатом, в браузере может появиться сообщение об ошибке сертификата. Эту проблему можно устранить, воспользовавшись сертификатом, подписанным доверенным центром сертификации. Пока это не будет сделано, вы можете игнорировать данную ошибку. (Другой способ — добавить самозаверяющий сертификат в используемое хранилище сертификатов доверенного центра сертификации.)
   >
   >

   ![Предварительный просмотр сайта](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Если вы хотите использовать SSL для промежуточного развертывания вместо производственного, вам нужно сначала определить URL-адрес, используемый для промежуточного развертывания. После развертывания облачной службы URL-адрес промежуточной среды определяется по **идентификатору развертывания`https://deployment-id.cloudapp.net/` GUID и имеет следующий формат:**  
   >
   > Создайте сертификат с общим именем, значение которого равно URL-адресу на основе GUID (например, **328187776e774ceda8fc57609d404462.cloudapp.net**). С помощью портала добавьте этот сертификат в промежуточную облачную службу. Затем добавьте сведения об этом сертификате в CSDEF- и CSCFG-файлы, перепакуйте приложение и обновите промежуточное развертывание для использования нового пакета.
   >

## <a name="next-steps"></a>Дополнительная информация
* [Общая настройка облачной службы](cloud-services-how-to-configure-portal.md).
* Узнайте, как [развернуть облачную службу](cloud-services-how-to-create-deploy-portal.md).
* Настройте [пользовательское доменное имя](cloud-services-custom-domain-name-portal.md).
* [Управляйте облачной службой](cloud-services-how-to-manage-portal.md).
