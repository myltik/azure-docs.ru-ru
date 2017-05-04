---
title: "Привязывание существующего настраиваемого SSL-сертификата к веб-приложениям Azure | Документация Майкрософт"
description: "Узнайте, как привязать настраиваемый SSL-сертификат к веб-приложению, серверной части мобильного приложения или приложению API в службе приложений Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 5bbdd1db655c080b4372f6728bb47207757209e4
ms.lasthandoff: 04/27/2017


---
# <a name="bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>Привязывание существующего настраиваемого SSL-сертификата к веб-приложениям Azure

В этом руководстве показано, как привязать SSL-сертификат, приобретенный в доверенном центре сертификации, к [веб-приложениям Azure](app-service-web-overview.md). Завершив работу с этим руководством, вы сможете обращаться к своему веб-приложению через конечную точку HTTPS личного домена DNS.

![Веб-приложение с настраиваемым SSL-сертификатом](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!TIP]
> Если необходимо, вы можете получить SSL-сертификат непосредственно на портале Azure и привязать его к веб-приложению. Следуйте указаниям в [руководстве по сертификатам службы приложений](web-sites-purchase-ssl-web-site.md). 
>
> 

## <a name="before-you-begin"></a>Перед началом работы
Перед выполнением инструкций в этом руководстве убедитесь, что выполнено следующее:

- [Создано приложение службы приложений](/azure/app-service/).
- [С вашим веб-приложением сопоставлено настраиваемое DNS-имя](web-sites-custom-domain-name.md).
- Получен SSL-сертификат из доверенного центра сертификации.

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>Требования к SSL-сертификату

Чтобы сертификат можно было использовать в службе приложений, он:

* должен быть подписан доверенным центром сертификации;
* должен быть экспортирован в PFX-файл, защищенный паролем;
* должен содержать закрытый ключ длиной не менее 2048 битов;
* должен содержать все промежуточные сертификаты из цепочки сертификатов.

> [!NOTE]
> **Сертификаты с шифрованием на основе эллиптических кривых (ECC)** можно использовать со службой приложений, но это выходит за рамки данной статьи. Чтобы создать сертификаты ECC, обратитесь к своему центру сертификации за конкретными указаниями.
> 
>

## <a name="prepare-your-web-app"></a>Подготовка веб-приложения
Для привязки SSL-сертификата к веб-приложению ваш [план службы приложений](https://azure.microsoft.com/pricing/details/app-service/) должен находиться в ценовой категории **Базовый**, **Стандартный** или **Премиум**. На этом шаге следует убедиться, что веб-приложение находится в поддерживаемой ценовой категории.

### <a name="log-in-to-azure"></a>Вход в Azure

Перейдите на портал Azure. Для этого войдите на портал [https://portal.azure.com](https://portal.azure.com) с помощью вашей учетной записи Azure.

### <a name="navigate-to-your-web-app"></a>Переход к веб-приложению
В меню слева выберите **Службы приложений**, а затем щелкните имя своего веб-приложения.

![Выбор веб-приложения](./media/app-service-web-tutorial-custom-ssl/select-app.png)

Вы перешли в колонку управления веб-приложением (_колонка_: горизонтальная страница портала).  

### <a name="check-the-pricing-tier"></a>Проверка ценовой категории

В левой области навигации колонки веб-приложения перейдите к разделу **Параметры** и выберите **Увеличить масштаб (план службы приложений)**.

![Меню увеличения масштаба](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

Убедитесь, что веб-приложение не находится в ценовой категории **Бесплатный** или **Общий**. Текущая ценовая категория веб-приложения выделена синей рамкой. 

![Проверка ценовой категории](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

Настраиваемые SSL-сертификаты не поддерживаются в ценовых категориях **Бесплатный** и **Общий**. Если вам нужно перевести приложение в другую ценовую-категорию, перейдите к следующему разделу. В противном случае закройте колонку **Выбор ценовой категории** и перейдите к разделу [Привязка SSL-сертификата](#upload).

### <a name="scale-up-your-app-service-plan"></a>Изменение уровня плана службы приложений

Выберите одну из категорий: **Базовый**, **Стандартный** или **Премиум**. 

Нажмите кнопку **Выбрать**.

![Выбор ценовой категории](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

Если вы увидите уведомление ниже, значит уровень плана службы приложений изменен.

![Уведомление об увеличении масштаба](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Привязка SSL-сертификата

Все готово к передаче SSL-сертификата для веб-приложения. 

### <a name="export-certificate-to-pfx"></a>Экспорт сертификата в PFX-файл

Необходимо экспортировать настраиваемый SSL-сертификат с закрытым ключом, с помощью которого был создан запрос сертификата.

Если запрос сертификата был создан с помощью OpenSSL, то вы создали закрытый ключ. Чтобы экспортировать сертификат в PFX-файл, выполните следующую команду.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
```

Если для создания запроса сертификата вы использовали IIS или `Certreq.exe`, то сначала установите сертификат на локальный компьютер, а затем экспортируйте его в PFX-файл, выполнив действия, описанные в разделе [Экспорт сертификата с закрытым ключом](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-your-ssl-certificate"></a>Передача SSL-сертификата

Чтобы передать SSL-сертификат, щелкните **SSL-сертификаты** в левой области навигации веб-приложения.

Щелкните **Отправить сертификат**.

В разделе **PFX-файл сертификата** выберите свой PFX-файл. В поле **Пароль сертификата** введите пароль, созданный при экспорте PFX-файла.

Щелкните **Отправить**.

![Передача сертификата](./media/app-service-web-tutorial-custom-ssl/upload-certificate.png)

По завершении передачи сертификата службой приложений он появится на странице **SSL-сертификаты**.

![Сертификат добавлен](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>Привязка SSL-сертификата

Теперь переданный сертификат должен появиться на странице **SSL-сертификат** .

В разделе **SSL-привязки** щелкните **Добавить привязку**.

В колонке **Добавление привязки SSL** в раскрывающихся списках выберите доменное имя для защиты и используемый сертификат. 

В разделе **Тип SSL** можно выбрать SSL на основе **[указания имени сервера (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** или IP-адреса.
   
- **SSL на основе SNI** позволяет добавить несколько привязок SSL на основе SNI. Этот параметр позволяет использовать несколько SSL-сертификатов для защиты нескольких доменов с одним IP-адресом. Большинство современных браузеров (включая Internet Explorer, Chrome, Firefox и Opera) поддерживает SNI (более подробную информацию о поддержки браузеров можно найти в статье [Server Name Indication](http://wikipedia.org/wiki/Server_Name_Indication) (Указание имени сервера)).
- **SSL на основе IP-адреса** позволяет добавить только одну привязка SSL на основе IP-адреса. Этот параметр позволяет использовать только один SSL-сертификат для защиты выделенного общедоступного IP-адреса. Чтобы защитить несколько доменов, необходимо использовать один и тот же SSL-сертификат. Это традиционный вариант привязки SSL. 

Щелкните **Добавить привязку**.

![Привязка SSL-сертификата](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

По завершении передачи сертификата службой приложений он появится в разделах **SSL-привязки**.

![Сертификат, привязанный к веб-приложению](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>Переназначение записи A для SSL на основе IP-адреса

Если вы не используете SSL на основе IP-адреса в веб-приложении, перейдите к разделу [Тестирование протокола HTTPS для личного домена](#test). 

По умолчанию веб-приложение использует общий общедоступный IP-адрес. После привязки сертификата с помощью SSL на основе IP-адреса служба приложений создает выделенный IP-адрес для вашего веб-приложения.

Если с веб-приложением сопоставлена запись A, обновите реестр домена, указав этот новый выделенный IP-адрес.

Он появится на странице **Личный домен** вашего веб-приложения. [Скопируйте этот IP-адрес](app-service-web-tutorial-custom-domain.md#info), затем [переназначьте его записи A](app-service-web-tutorial-custom-domain.md#create-the-a-record).

<a name="test"></a>

## <a name="test-https"></a>Тестирование HTTPS
Нам осталось только убедиться, что протокол HTTPS для личного домена функционирует. В разных браузерах перейдите по адресу `https://<your.custom.domain>`, чтобы узнать, как он обслуживает ваше веб-приложение.

![Переход к приложению Azure на портале](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Если веб-приложение выдает ошибки проверки сертификата, вероятно, вы используете самозаверяющий сертификат.
>
> Если это не так, возможно, вы пропустили промежуточные сертификаты при экспорте сертификата в PFX-файл. 
>
>

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Принудительное использование HTTPS
Если вы по-прежнему хотите разрешить доступ к веб-приложению по протоколу HTTP, пропустите этот шаг. 

Служба приложений *не* принуждает использовать протокол HTTPS, чтобы кто угодно мог обратиться к веб-приложению с помощью протокола HTTP. Чтобы принудительно использовать HTTPS для веб-приложения, вы можете определить для него правило перезаписи в файле `web.config`. Служба приложений использует этот файл независимо от используемой языковой платформы веб-приложения.

> [!NOTE]
> Для каждого языка используется определенное перенаправление запросов. ASP.NET MVC может использовать фильтр [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) вместо правила перезаписи в файле `web.config` (ознакомьтесь с разделом [Создание приложения ASP.NET MVC с проверкой подлинности и базой данных SQL и развертывание службы приложений Azure](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)).
> 
> 

Если вы разработчик для .NET, то этот файл должен быть вам в общем и целом знаком. Он находится в корневом каталоге решения.

Кроме того, если вы программируете на PHP, Node.js, Python или Java, существует вероятность того, что этот файл создан от вашего имени в службе приложений.

Подключитесь к конечной точке FTP веб-приложения, следуя указаниям в разделе [Развертывание приложения в службе приложений Azure с помощью FTP или FTPS](app-service-deploy-ftp.md). 

Этот файл должен находиться в `/home/site/wwwroot`. Если это не так, создайте в этой папке файл `web.config` с приведенным ниже кодом XML.

```xml   
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <!-- BEGIN rule TAG FOR HTTPS REDIRECT -->
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
        <!-- END rule TAG FOR HTTPS REDIRECT -->
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```

Если файл `web.config` существует, то вам нужно только скопировать весь тег `<rule>` в элемент `configuration/system.webServer/rewrite/rules` в файле `web.config`. При наличии уже других тегов `<rule>` в файле `web.config` поместите скопированный тег `<rule>` перед другими тегами `<rule>`.

Это правило возвращает ответ "HTTP 301" (постоянное перенаправление) для протокола HTTPS всякий раз, когда пользователь отправляет HTTP-запрос к вашему веб-приложению. Например, оно перенаправляет `http://contoso.com` в `https://contoso.com`.

Дополнительные сведения о модуле переопределения URL-адресов IIS см. в документации по [модулю переопределения URL-адресов](http://www.iis.net/downloads/microsoft/url-rewrite).

## <a name="automate-with-scripts"></a>Автоматизация с помощью сценариев

Управлением привязками SSL для веб-приложения можно автоматизировать с помощью сценариев, воспользовавшись [Azure CLI](/cli/azure/install-azure-cli) или [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Инфраструктура CLI Azure

Следующая команда передает экспортированный PFX-файл и возвращает отпечаток. 

```bash
thumprint=$(az appservice web config ssl upload --certificate-file <path_to_PFX_file> \
--certificate-password <PFX_password> --name <app_name> --resource-group <resource_group_name> \
--query thumbprint --output tsv)
```

Следующая команда добавляет привязку SSL на основе SNI с помощью отпечатка из предыдущей команды.

```bash
az appservice web config ssl bind --certificate-thumbprint $thumbprint --ssl-type SNI \
--name <app_name> --resource-group <resource_group_name>
```

### <a name="azure-powershell"></a>Azure PowerShell

Следующая команда передает экспортированный PFX-файл и добавляет привязку SSL на основе SNI.

```PowerShell
New-AzureRmWebAppSSLBinding -WebAppName <app_name> -ResourceGroupName <resource_group_name> -Name <dns_name> `
-CertificateFilePath <path_to_PFX_file> -CertificatePassword <PFX_password> -SslState SniEnabled
```
## <a name="more-resources"></a>Дополнительные ресурсы
* [Центр управления безопасностью Microsoft Azure](/support/trust-center/security/)
* [Параметры конфигурации, разблокированные на веб-сайтах Azure](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
* [Включение ведения журналов диагностики](web-sites-enable-diagnostic-log.md)
* [Настройка веб-приложений в службе приложений Azure](web-sites-configure.md)

