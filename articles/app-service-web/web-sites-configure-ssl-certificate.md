---
title: "Защита личного домена приложения с помощью протокола HTTPS | Документация Майкрософт"
description: "Узнайте, как защитить имя личного домена приложения в службе приложений Azure, настроив привязку SSL-сертификата. Вы узнаете также, как получить SSL-сертификат с помощью нескольких инструментов."
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 613d7932-73aa-4318-867c-1ce1416224dc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: e0bfa7620feeb1bad33dd2fe4b32cb237d3ce158
ms.openlocfilehash: 0d909f6272cc9dcfd13bd9c18affa8d1e249efe4
ms.lasthandoff: 04/21/2017


---
# <a name="secure-your-apps-custom-domain-with-https"></a>Защита личного домена приложения с помощью протокола HTTPS
> [!div class="op_single_selector"]
> * [Приобретение SSL-сертификата в Azure](web-sites-purchase-ssl-web-site.md)
> * [Использование SSL-сертификата из другого источника](web-sites-configure-ssl-certificate.md)
> 
> 

В этой статье показано, как в [службе приложений Azure](../app-service/app-service-value-prop-what-is.md) включить протокол HTTPS для веб-приложения, серверной части мобильного приложения или приложения API, использующего личный домен. В ней рассматривается только аутентификация на сервере. Если требуется взаимная аутентификация (включая проверку подлинности клиента), ознакомьтесь с разделом [Настройка взаимной проверки подлинности TLS для веб-приложения](app-service-web-configure-tls-mutual-auth.md).

Чтобы с помощью протокола HTTPS защитить приложение, использующее имя личного домена, следует добавить сертификат для этого доменного имени. По умолчанию Azure защищает домен **\*.azurewebsites.net** одним SSL-сертификатом, чтобы ваши клиенты могли обращаться к вашему приложению, размещенному по адресу **https://*&lt;имя_приложения>*.azurewebsites.net**. Но если вы хотите использовать личный домен, например **contoso.com**, **www.contoso.com** или **\*.contoso.com**, то сертификат по умолчанию не сможет обеспечить его защиту. Более того, как и все [групповые сертификаты](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/), сертификат по умолчанию не обеспечивает такую безопасность, как личный домен с сертификатом для этого домена.   

> [!NOTE]
> В любой момент вы можете обратиться за помощью к экспертам по Azure на [форумах Azure](https://azure.microsoft.com/support/forums/). Чтобы получить более индивидуальную помощь, перейдите на сайт [службы поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.
> 
> 

<a name="bkmk_domainname"></a>

## <a name="what-you-need"></a>Необходимые элементы
Чтобы защитить имя личного домена с помощью протокола HTTPS, следует привязать пользовательский SSL-сертификат к этому домену в Azure. Перед привязкой настраиваемого сертификата необходимо выполнить следующее.

* **Настройте личный домен**. Служба приложений позволяет добавить сертификат только для доменного имени, которое уже настроено в приложении. Соответствующие указания приведены в разделе [Сопоставление имени личного домена с приложением Azure](web-sites-custom-domain-name.md). 
* **Перейдите на уровень "Базовый" или выше**. Планы службы приложений для более низких ценовых категорий не поддерживают пользовательские SSL-сертификаты. Соответствующие инструкции приведены в разделе [Увеличение масштаба приложения в Azure](web-sites-scale.md). 
* **Получите SSL-сертификат.** Если у вас еще нет сертификата, необходимо получить его из доверенного [центра сертификации](http://en.wikipedia.org/wiki/Certificate_authority) (ЦС). Сертификат должен отвечать следующим требованиям:
  
  * он подписан доверенным центром сертификации (а не серверами частных центров сертификации);
  * он содержит закрытый ключ;
  * он создан для обмена ключами и экспортирован в PFX-файл;
  * в нем используется как минимум 2048-разрядное шифрование;
  * его имя субъекта соответствует личному домену, который необходимо защитить. Чтобы защитить нескольких доменов одним сертификатом, необходимо использовать имя с подстановочными знаками (например, **\*.contoso.com**) или указать значения subjectAltName.
  * Этот сертификат объединяется со всеми **[промежуточными сертификатами](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)**, которые использует ваш центр сертификации. В противном случае возможны невоспроизводимые проблемы взаимодействия в работе некоторых клиентов.
    
    > [!NOTE]
    > Самый простой способ получить SSL-сертификат, который соответствует всем требованиям, — [приобрести его непосредственно на портале Azure](web-sites-purchase-ssl-web-site.md). В этой статье показано, как сделать это вручную, а затем привязать сертификат к личному домену в службе приложений.
    > 
    > **Сертификаты с шифрованием на основе эллиптических кривых (ECC)** можно использовать со службой приложений, но это выходит за рамки данной статьи. Чтобы создать сертификаты ECC, обратитесь к своему центру сертификации за конкретными указаниями.
    > 
    > 

<a name="bkmk_getcert"></a>

## <a name="step-1-get-an-ssl-certificate"></a>Шаг 1. Получите SSL-сертификат.
Так как ЦС предоставляет различные типы SSL-сертификатов по разным рекомендуемым ценам, следует начать с решения о том, какой тип SSL-сертификат купить. Для защиты одного доменного имени (**www.contoso.com**) достаточно базового сертификата. Чтобы защитить несколько доменных имен (**contoso.com**,*и* **www.contoso.com** 
*и* **mail.contoso.com**), нужно использовать [групповой сертификат](http://en.wikipedia.org/wiki/Wildcard_certificate) или сертификат с [альтернативным именем субъекта](http://en.wikipedia.org/wiki/SubjectAltName) (`subjectAltName`).

Решив, какой следует приобрести SSL-сертификат, следует отправить в ЦС запрос подписи сертификата (CSR). Получив запрошенный сертификат из ЦС, следует создать PFX-файл из этого сертификата. Это можно выполнить с помощью инструмента на ваш выбор. Ниже приведены инструкции для распространенных инструментов.

* [Инструкции для CertReq.exe](#bkmk_certreq) — служебной программы Windows для создания запросов сертификатов. 
  Она входила в состав Windows, начиная с выпуска Windows XP и Windows Server 2000.
* [Инструкции для диспетчера IIS](#bkmk_iismgr) — предпочтительного инструмента, если вы уже работали с ним.
* [Инструкции для OpenSSL](#bkmk_openssl) — [кроссплатформенного инструмента с открытым кодом](https://www.openssl.org). Используйте его для ознакомления с SSL-сертификатами любой платформы.
* [Инструкции для subjectAltName с использованием OpenSSL](#bkmk_subjectaltname) позволят вам получить сертификаты `subjectAltName`.

Если вы хотите протестировать конфигурацию в службе приложений до приобретения сертификата, то можно создать [самозаверяющий сертификат](https://en.wikipedia.org/wiki/Self-signed_certificate). В этом учебнике предоставлено два способа его создания:

* [Создание самозаверяющего сертификата с помощью Certreq.exe.](#bkmk_sscertreq)
* [Создание самозаверяющего сертификата с помощью OpenSSL.](#bkmk_ssopenssl)

<a name="bkmk_certreq"></a>

### <a name="get-a-certificate-using-certreqexe"></a>Получение сертификата с помощью Certreq.exe
1. Создайте файл (например, **myrequest.txt**), скопируйте в него приведенный ниже текст и сохраните в рабочем каталоге. 
   Замените заполнитель `<your-domain>` именем личного домена приложения.
   
        [NewRequest]
        Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
        Exportable = TRUE
        KeyLength = 2048              ; Required minimum is 2048
        KeySpec = 1
        KeyUsage = 0xA0
        MachineKeySet = FALSE
        ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
        ProviderType = 12
        HashAlgorithm = SHA256
   
        [EnhancedKeyUsageExtension]
        OID=1.3.6.1.5.5.7.3.1         ; Server Authentication
   
    Для получения дополнительных сведений о параметрах в CSR и других доступных параметрах ознакомьтесь со [справочной документацией по Certreq](https://technet.microsoft.com/library/dn296456.aspx).
2. В командной строке с помощью команды `CD` перейдите в рабочий каталог и выполните следующую команду, чтобы создать CSR.
   
        certreq -new myrequest.txt myrequest.csr
   
    **myrequest.csr** .
3. Отправьте файл **myrequest.csr** в центр сертификации для получения SSL-сертификата. Передайте этот файл либо скопируйте его содержимое из текстового редактора в веб-форму.
   
    Список центров сертификации, являющихся доверенными для корпорации Майкрософт, см. в разделе [Microsoft Trusted Root Certificate Program: Participants][cas] (Участники программы доверенных корневых сертификатов Майкрософт).
4. Получив CER-файл сертификата из ЦС, сохраните его в рабочем каталоге. Затем выполните следующую команду, чтобы завершить ожидающий CSR.
   
        certreq -accept -user <certificate-name>.cer
   
    Эта команда сохраняет готовый сертификат в хранилище сертификатов Windows.
5. Если ваш ЦС использует промежуточные сертификаты, то установите их, прежде чем продолжить. Обычно их можно скачать из ЦС в виде отдельных файлов в различных форматах для разных типов веб-серверов. Выберите версию для служб Microsoft IIS.
   
    Скачав сертификаты, щелкните каждый из них правой кнопкой мыши в проводнике и выберите  **Установить сертификат**. Используйте значения по умолчанию в **мастере импорта сертификатов** и нажимайте кнопку **Далее**, пока импорт не будет завершен.
6. Чтобы экспортировать SSL-сертификат из хранилища сертификатов, нажмите клавишу `Win`+`R` и запустите **certmgr.msc** для запуска диспетчера сертификатов. 
   Выберите **Личные** > **Сертификаты**. В столбце **Кому выдан** должна отображаться запись с именем вашего личного домена, а в столбце **Кем выдан** — ЦС, использованный для создания сертификата.
   
    ![вставить изображение диспетчера сертификатов][certmgr]
7. Щелкните сертификат правой кнопкой мыши и выберите **Все задачи** > **Экспорт**. В **мастере экспорта сертификатов** нажмите кнопку **Далее**, а затем выберите **Да, экспортировать закрытый ключ**. Нажмите кнопку **Далее** еще раз.
   
    ![Экспорт закрытого ключа][certwiz1]
8. Выберите **Обмен личной информацией — PKCS #12**, **Include all certificates in the certificate path if possible** (Включить все сертификаты по пути сертификатов, если возможно) и **Экспортировать все расширенные свойства**. Затем щелкните **Далее**.
   
   ![включить все сертификаты и расширенные свойства][certwiz2]
9. Выберите **Пароль**, а затем введите пароль и подтвердите его. Нажмите кнопку **Далее**.
   
   ![укажите пароль][certwiz3]
10. Укажите путь к файлу и имя файла экспортированного сертификата с расширением **PFX**. Нажмите кнопку **Далее** , чтобы завершить процедуру.
    
    ![укажите путь к файлу][certwiz4]

Теперь вы готовы передать экспортированный PFX-файл в службу приложений. Ознакомьтесь с разделом [Шаг 2. Передача и привязка пользовательского SSL-сертификата](#bkmk_configuressl).

<a name="bkmk_iismgr"></a>

### <a name="get-a-certificate-using-the-iis-manager"></a>Получение сертификата с помощью диспетчера IIS
1. С помощью диспетчера IIS создайте CSR и отправьте его в ЦС. Дополнительные сведения о создании CSR можно найти в разделе [Request an Internet Server Certificate (IIS 7)][iiscsr] (Запрос сертификата сервера Интернета (IIS 7)).
2. Отправьте CSR в ЦС, чтобы получить SSL-сертификат. Список центров сертификации, являющихся доверенными для корпорации Майкрософт, см. в разделе [Microsoft Trusted Root Certificate Program: Participants][cas] (Участники программы доверенных корневых сертификатов Майкрософт).
3. Завершите CSR с помощью сертификата, возвращенного ЦС. Дополнительные сведения о завершении работы с CSR можно найти в разделе [Install an Internet Server Certificate (IIS 7)][installcertiis] (Установка сертификата сервера Интернета (IIS 7)).
4. Если ваш ЦС использует промежуточные сертификаты, то установите их, прежде чем продолжить. Обычно их можно скачать из ЦС в виде отдельных файлов в различных форматах для разных типов веб-серверов. Выберите версию для служб Microsoft IIS.
   
    Скачав сертификаты, щелкните каждый из них правой кнопкой мыши в проводнике и выберите **Установить сертификат**. 
    Используйте значения по умолчанию в **мастере импорта сертификатов** и нажимайте кнопку **Далее**, пока импорт не будет завершен.
5. Экспортируйте SSL-сертификат из диспетчера IIS. Чтобы узнать больше об экспорте сертификата, ознакомьтесь с разделом [Export a Server Certificate (IIS 7)][exportcertiis] (Экспорт сертификата сервера (IIS 7)). 
   
   > [!IMPORTANT]
   > В **мастере экспорта сертификатов** обязательно выберите **Да, экспортировать закрытый ключ**.  
   > 
   > ![Экспорт закрытого ключа][certwiz1]  
   > 
   > Выберите также **Обмен личной информацией — PKCS #12**, **Include all certificates in the certificate path if possible** (Включить все сертификаты по пути сертификатов, если возможно) и **Экспортировать все расширенные свойства**.
   > 
   > ![включить все сертификаты и расширенные свойства][certwiz2]
   > 
   > 

Теперь вы готовы передать экспортированный PFX-файл в службу приложений. Ознакомьтесь с разделом [Шаг 2. Передача и привязка пользовательского SSL-сертификата](#bkmk_configuressl).

<a name="bkmk_openssl"></a>

### <a name="get-a-certificate-using-openssl"></a>Получение сертификата с помощью OpenSSL
1. В терминале командной строки с помощью команды `CD` перейдите в рабочий каталог, затем создайте закрытый ключ и CSR, выполнив следующую команду.
   
        openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048
2. При появлении запроса введите соответствующую информацию. Например:
   
         Country Name (2 letter code)
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:
   
        Please enter the following 'extra' attributes to be sent with your certificate request
   
           A challenge password []:
   
    По завершении в рабочем каталоге должно быть два файла: **myserver.key** и **server.csr**. 
    Файл **Server.csr** содержит CSR, а файл **myserver.key** понадобится позже.
3. Отправьте CSR в ЦС, чтобы получить SSL-сертификат. Список центров сертификации, являющихся доверенными для корпорации Майкрософт, см. в разделе [Microsoft Trusted Root Certificate Program: Participants][cas] (Участники программы доверенных корневых сертификатов Майкрософт).
4. После того, как ЦС отправит вам запрошенный сертификат, сохраните его в файл **myserver.crt** в рабочем каталоге. Если ваш ЦС предоставляет его в текстовом формате, то просто скопируйте содержимое полученного сертификата в файл **myserver.crt** в текстовом редакторе и сохраните его. Файл должен выглядеть следующим образом.
   
        -----BEGIN CERTIFICATE-----
        MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
        UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
        c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
        NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
        ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
        ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
        enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
        3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
        xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
        ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
        Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
        AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
        Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
        F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
        7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
        lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
        A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
        -----END CERTIFICATE-----
5. В терминале командной строке выполните следующую команду, чтобы экспортировать **myserver.pfx** из **myserver.key** и **myserver.crt**.
   
        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
   
    При появлении запроса укажите пароль для защиты PFX-файла.
   
   > [!NOTE]
   > Если ваш ЦС использует промежуточные сертификаты, то необходимо указать их с помощью параметра `-certfile`. Обычно их можно скачать из ЦС в виде отдельных файлов в различных форматах для разных типов веб-серверов. Выберите версию с расширением `.pem` .
   > 
   > Ваша команда `openssl -export` должна выглядеть как в следующем примере, который создает PFX-файл, включающий в себя промежуточные сертификаты из файла **intermediate-cets.pem** .
   > 
   > `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`
   > 
   > 

Теперь вы готовы передать экспортированный PFX-файл в службу приложений. Ознакомьтесь с разделом [Шаг 2. Передача и привязка пользовательского SSL-сертификата](#bkmk_configuressl).

<a name="bkmk_subjectaltname"></a>

### <a name="get-a-subjectaltname-certificate-using-openssl"></a>Получение сертификата SubjectAltName с помощью OpenSSL
1. Создайте файл **sancert.cnf**, скопируйте в него приведенный ниже текст и сохраните в рабочем каталоге.
   
        # -------------- BEGIN custom sancert.cnf -----
        HOME = .
        oid_section = new_oids
        [ new_oids ]
        [ req ]
        default_days = 730
        distinguished_name = req_distinguished_name
        encrypt_key = no
        string_mask = nombstr
        req_extensions = v3_req # Extensions to add to certificate request
        [ req_distinguished_name ]
        countryName = Country Name (2 letter code)
        countryName_default =
        stateOrProvinceName = State or Province Name (full name)
        stateOrProvinceName_default =
        localityName = Locality Name (eg, city)
        localityName_default =
        organizationalUnitName  = Organizational Unit Name (eg, section)
        organizationalUnitName_default  =
        commonName              = Your common name (eg, domain name)
        commonName_default      = www.mydomain.com
        commonName_max = 64
        [ v3_req ]
        subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
        # -------------- END custom sancert.cnf -----
   
    В строке, которая начинается с `subjectAltName`, замените значение списком доменных имен, которые требуется защитить (в дополнение к  `commonName`). Например:
   
        subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com
   
    Значения других полей, включая `commonName`, изменять не нужно. Вам будет предложено указать их на следующих шагах.
2. В терминале командной строки с помощью команды `CD` перейдите в рабочий каталог и выполните следующую команду.
   
        openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf
3. При появлении запроса введите соответствующую информацию. Например:
   
         Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com
   
    По завершении в рабочем каталоге должно быть два файла: **myserver.key** и **server.csr**. 
    Файл **Server.csr** содержит CSR, а файл **myserver.key** понадобится позже.
4. Отправьте CSR в ЦС, чтобы получить SSL-сертификат. Список центров сертификации, являющихся доверенными для корпорации Майкрософт, см. в разделе [Microsoft Trusted Root Certificate Program: Participants][cas] (Участники программы доверенных корневых сертификатов Майкрософт).
5. После того, как ЦС отправит вам запрошенный сертификат, сохраните его в файл **myserver.crt**. Если ваш ЦС предоставляет его в текстовом формате, то просто скопируйте содержимое полученного сертификата в файл **myserver.crt** в текстовом редакторе и сохраните его. Файл должен выглядеть примерно так:
   
        -----BEGIN CERTIFICATE-----
        MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
        UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
        c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
        NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
        ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
        ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
        enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
        3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
        xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
        ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
        Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
        AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
        Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
        F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
        7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
        lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
        A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
        -----END CERTIFICATE-----
6. В терминале командной строке выполните следующую команду, чтобы экспортировать **myserver.pfx** из **myserver.key** и **myserver.crt**.
   
        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
   
    При появлении запроса укажите пароль для защиты PFX-файла.
   
   > [!NOTE]
   > Если ваш ЦС использует промежуточные сертификаты, то необходимо указать их с помощью параметра `-certfile`. Обычно их можно скачать из ЦС в виде отдельных файлов в различных форматах для разных типов веб-серверов. Выберите версию с расширением `.pem` .
   > 
   > Ваша команда `openssl -export` должна выглядеть как в следующем примере, который создает PFX-файл, включающий в себя промежуточные сертификаты из файла **intermediate-cets.pem** .
   > 
   > `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`
   > 
   > 

Теперь вы готовы передать экспортированный PFX-файл в службу приложений. Ознакомьтесь с разделом [Шаг 2. Передача и привязка пользовательского SSL-сертификата](#bkmk_configuressl).

<a name="bkmk_sscertreq"></a>

### <a name="generate-a-self-signed-certificate-using-certreqexe"></a>Создание самозаверяющего сертификата с помощью Certreq.exe
> [!IMPORTANT]
> Самозаверяющие сертификаты используются только для тестирования. Большинство браузеров вернет ошибку при посещении веб-сайта, защищенного с помощью самозаверяющего сертификата. Некоторые браузеры могут даже отказаться переходить на такой сайт. 
> 
> 

1. Создайте текстовый файл (например, **mycert.txt**), скопируйте в него приведенный ниже текст и сохраните в рабочем каталоге. 
   Замените заполнитель `<your-domain>` именем личного домена приложения.
   
        [NewRequest]
        Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
        Exportable = TRUE
        KeyLength = 2048              ; KeyLength can be 2048, 4096, 8192, or 16384 (required minimum is 2048)
        KeySpec = 1
        KeyUsage = 0xA0
        MachineKeySet = True
        ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
        ProviderType = 12
        HashAlgorithm = SHA256
        RequestType = Cert            ; Self-signed certificate
        ValidityPeriod = Years
        ValidityPeriodUnits = 1
   
        [EnhancedKeyUsageExtension]
        OID=1.3.6.1.5.5.7.3.1         ; Server Authentication
   
    Важным параметром является `RequestType = Cert`, который указывает самозаверяющий сертификат. 
    Для получения дополнительных сведений о параметрах в CSR и других доступных параметрах ознакомьтесь со [справочной документацией по Certreq](https://technet.microsoft.com/library/dn296456.aspx).
2. В командной строке с помощью команды `CD` перейдите в рабочий каталог и выполните следующую команду.
   
        certreq -new mycert.txt mycert.crt
   
    Теперь ваш новый самозаверяющий сертификат установлен в хранилище сертификатов.
3. Чтобы экспортировать сертификат из хранилища сертификатов, нажмите клавишу `Win`+`R` и запустите **certmgr.msc** для запуска диспетчера сертификатов. 
   Выберите **Личные** > **Сертификаты**. В столбце **Кому выдан** должна отображаться запись с именем вашего личного домена, а в столбце **Кем выдан** — ЦС, использованный для создания сертификата.
   
    ![вставить изображение диспетчера сертификатов][certmgr]
4. Щелкните сертификат правой кнопкой мыши и выберите **Все задачи** > **Экспорт**. В **мастере экспорта сертификатов** нажмите кнопку **Далее**, а затем выберите **Да, экспортировать закрытый ключ**. Нажмите кнопку **Далее** еще раз.
   
    ![Экспорт закрытого ключа][certwiz1]
5. Выберите **Обмен личной информацией — PKCS #12**, **Include all certificates in the certificate path if possible** (Включить все сертификаты по пути сертификатов, если возможно) и **Экспортировать все расширенные свойства**. Затем щелкните **Далее**.
   
   ![включить все сертификаты и расширенные свойства][certwiz2]
6. Выберите **Пароль**, а затем введите пароль и подтвердите его. Нажмите кнопку **Далее**.
   
   ![укажите пароль][certwiz3]
7. Укажите путь к файлу и имя файла экспортированного сертификата с расширением **PFX**. Нажмите кнопку **Далее** , чтобы завершить процедуру.
   
   ![укажите путь к файлу][certwiz4]

Теперь вы готовы передать экспортированный PFX-файл в службу приложений. Ознакомьтесь с разделом [Шаг 2. Передача и привязка пользовательского SSL-сертификата](#bkmk_configuressl).

<a name="bkmk_ssopenssl"></a>

### <a name="generate-a-self-signed-certificate-using-openssl"></a>Создание самозаверяющего сертификата с помощью OpenSSL
> [!IMPORTANT]
> Самозаверяющие сертификаты используются только для тестирования. Большинство браузеров вернет ошибку при посещении веб-сайта, защищенного с помощью самозаверяющего сертификата. Некоторые браузеры могут даже отказаться переходить на такой сайт. 
> 
> 

1. Создайте текстовый файл **serverauth.cnf**, скопируйте в него следующее содержимое и сохраните его в рабочем каталоге.
   
        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca
   
        [ req_distinguished_name ]
        countryName            = Country Name (2 letter code)
        countryName_min            = 2
        countryName_max            = 2
        stateOrProvinceName        = State or Province Name (full name)
        localityName            = Locality Name (eg, city)
        0.organizationName        = Organization Name (eg, company)
        organizationalUnitName        = Organizational Unit Name (eg, section)
        commonName            = Common Name (eg, your app's domain name)
        commonName_max            = 64
        emailAddress            = Email Address
        emailAddress_max        = 40
   
        [ req_attributes ]
        challengePassword        = A challenge password
        challengePassword_min        = 4
        challengePassword_max        = 20
   
        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth
2. В терминале командной строки с помощью команды `CD` перейдите в рабочий каталог и выполните следующую команду.
   
        openssl req -sha256 -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf
   
    Эта команда создает два файла, **myserver.crt** (самозаверяющий сертификат) и **myserver.key** (закрытый ключ), на основе параметров в файле **serverauth.cnf**.
3. Экспортируйте сертификат в PFX-файл, выполнив следующую команду.
   
        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
   
    При появлении запроса укажите пароль для защиты PFX-файла.

Теперь вы готовы передать экспортированный PFX-файл в службу приложений. Ознакомьтесь с разделом [Шаг 2. Передача и привязка пользовательского SSL-сертификата](#bkmk_configuressl).

<a name="bkmk_configuressl"></a>

## <a name="step-2-upload-and-bind-the-custom-ssl-certificate"></a>Шаг 2. Передача и привязка пользовательского SSL-сертификата
Прежде чем продолжить, просмотрите раздел [Необходимые элементы](#bkmk_domainname) и убедитесь, что:

* у вас есть личный домен, который сопоставлен с вашим приложением Azure;
* ваше приложение работает на уровне **Базовый** или выше;
* у вас есть SSL-сертификат для личного домена, полученный из ЦС.

1. В браузере откройте **[портал Azure](https://portal.azure.com/)**.
2. Щелкните **Служба приложений** в левой части страницы.
3. Щелкните имя приложения, которому вы хотите назначить этот сертификат. 
4. В разделе **Параметры** щелкните **SSL-сертификаты**.
5. Нажмите кнопку **Загрузить сертификат**
6. Выберите PFX-файл, экспортированный на [шаге 1](#bkmk_getcert) , и укажите пароль, который был создан ранее. 
   Нажмите кнопку **Загрузить** , чтобы передать сертификат. Теперь переданный сертификат должен появиться в колонке **SSL-сертификат** .
7. В разделе **SSL-привязки** щелкните **Add bindings** (Добавить привязки).
8. В колонке **Добавление привязки SSL** в раскрывающихся списках выберите имя домена для защиты с применением SSL, а также используемый сертификат. Также можно выбрать SSL на основе **[указания имени сервера (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** или IP-адреса.
   
    ![вставить изображение для привязок SSL](./media/web-sites-configure-ssl-certificate/sslbindings.png)
   
    > [!NOTE] 
    > **SSL на основе IP** связывает сертификат с доменным именем, сопоставляя выделенный общедоступный IP-адрес сервера с доменным именем. Для этого необходимо, чтобы для каждого имени домена (contoso.com, fabricam.com и т. д.), связанного с вашей службой, был доступен выделенный IP-адрес. Это традиционный метод сопоставления сертификатов SSL с веб-сервером.  
    >
    > **SSL на основе SNI** является расширением SSL и **[протокола TLS](http://en.wikipedia.org/wiki/Transport_Layer_Security)**, которое позволяет нескольким доменам совместно использовать IP-адрес с отдельными сертификатами безопасности для каждого домена. Большинство современных браузеров (включая Internet Explorer, Chrome, Firefox и Opera) поддерживает SNI, однако более старые браузеры могут не поддерживать SNI. Дополнительные сведения об SNI см. в статье Википедии **[Server Name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication)** (Указание имени сервера).
    > 

9. Чтобы сохранить изменения и активировать SSL, щелкните **Добавить привязку** .

## <a name="step-3-change-your-domain-name-mapping-ip-based-ssl-only"></a>Шаг 3. Изменение сопоставления доменного имени (только для SSL на основе IP-адреса)
Если вы используете только привязки **SNI SSL**, то пропустите этот раздел. Несколько привязок **SNI SSL** могут совместно работать с существующим общим IP-адресом, назначенным вашему приложению. Тем не менее при создании привязки **SSL на основе IP-адреса** служба приложений создает выделенный IP-адрес для этой привязки. Он необходим привязке **SSL на основе IP-адреса**. Можно создать только один выделенный IP-адрес, поэтому можно добавить только одну привязку **SSL на основе IP-адреса**.

Из-за этого выделенного IP-адреса потребуется дополнительно настроить приложение, если:

* Вы [использовали запись A для сопоставления личного домена](web-sites-custom-domain-name.md#a) с приложением Azure и только что добавили привязку **SSL на основе IP-адреса**. В этом случае необходимо переназначить существующую запись А, чтобы она указывала на выделенный IP-адрес. Для этого выполните приведенные ниже действия.
  
  1. После настройки привязки SSL-подключения на основе IP вашему веб-приложению будет предоставлен выделенный IP-адрес. Этот IP-адрес можно найти на странице **Личный домен** в разделе параметров приложения прямо над разделом **Имена узлов**. Он будет указан как **Внешний IP-адрес**
     
      ![Виртуальный IP-адрес](./media/web-sites-configure-ssl-certificate/virtual-ip-address.png)
  2. [Переназначьте запись A для имени личного домена на этот новый IP-адрес](web-sites-custom-domain-name.md#a).
* У вас уже есть одна или несколько привязок **SNI SSL** в приложении, и вы только что добавили привязку **SSL на основе IP-адреса**. 
  После завершения привязки доменное имя *&lt;имя_приложения>*.azurewebsites.net будет указывать на новый IP-адрес. 
  Следовательно, любое существующее [сопоставление записи CNAME для личного домена](web-sites-custom-domain-name.md#cname) с доменным именем *&lt;имя_приложения>*.azurewebsites.net, включая защищенное с помощью привязки **SNI SSL**, также позволяет получать трафик на новый адрес, который был создан только для привязок **SSL на основе IP-адреса**. В этом сценарии необходимо отправить трафик **SNI SSL** обратно на исходный общий IP-адрес, выполнив следующие действия.
  
  1. Определите все [сопоставления записи CNAME личных доменов](web-sites-custom-domain-name.md#cname) с привязкой **SNI SSL** для своего приложения.
  2. Переназначьте каждую запись CNAME, указав **sni.**&lt;имя_приложения>.azurewebsites.net вместо &lt;имя_приложения>.azurewebsites.net.

## <a name="step-4-test-https-for-your-custom-domain"></a>Шаг 4. Тестирование протокола HTTPS для личного домена
Нам осталось только убедиться, что протокол HTTPS для личного домена функционирует. В разных браузерах перейдите по адресу `https://<your.custom.domain>` , чтобы посмотреть, как он обслуживает ваше приложение.

* Если приложение выдает ошибки проверки сертификата, вероятно, вы используете самозаверяющий сертификат.
* Если это не так, возможно, вы пропустили промежуточные сертификаты при экспорте PFX-файла сертификата. Вернитесь к разделу [Необходимые элементы](#bkmk_domainname) , чтобы убедиться, что запрос подписи сертификата (CSR) отвечает всем требованиям службы приложений.

<a name="bkmk_enforce"></a>

## <a name="enforce-https-on-your-app"></a>Принудительное использование HTTPS для приложения
Если все равно хотите разрешить доступ к приложению по протоколу HTTP, пропустите этот шаг. Служба приложений *не* принуждает использовать протокол HTTPS, чтобы посетители могли обращаться к приложению с помощью протокола HTTP. Если вы хотите принудительно использовать HTTPS для приложения, то можете определить для него правило перезаписи в файле `web.config` . У каждого приложения службы приложений имеется такой файл, независимо от используемой языковой платформы.

> [!NOTE]
> Для каждого языка используется определенное перенаправление запросов. ASP.NET MVC может использовать фильтр [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) вместо правила перезаписи в файле `web.config` (ознакомьтесь с разделом [Создание приложения ASP.NET MVC с проверкой подлинности и базой данных SQL и развертывание службы приложений Azure](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)).
> 
> 

Выполните следующие действия.

1. Перейдите к консоли отладки Kudu для своего приложения. Вот ее адрес: `https://<appname>.scm.azurewebsites.net/DebugConsole`.
2. В консоли отладки с помощью команды cd перейдите в каталог `D:\home\site\wwwroot`.
3. Откройте `web.config` , нажав кнопку с карандашом.
   
    ![](./media/web-sites-configure-ssl-certificate/openwebconfig.png)
   
    При развертывании приложения с помощью Visual Studio или Git служба приложений автоматически создает соответствующий файл `web.config` приложения .NET, PHP, Node.js или Python в его корневой папке. 
    Если `web.config` не существует, выполните команду `touch web.config` в командной строке веб-службы, чтобы создать его. Или вы можете создать его в локальном проекте и повторно развернуть свой код.
4. Если вам нужно было создать файл `web.config`, скопируйте в него следующий код и сохраните его. Если вы открыли существующий файл web.config, то вам нужно только скопировать весь тег `<rule>` в элемент `configuration/system.webServer/rewrite/rules` в файле `web.config`.
   
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
   
    Это правило возвращает ответ "HTTP 301" (постоянное перенаправление) для протокола HTTPS всякий раз, когда пользователь запрашивает страницу с помощью протокола HTTP. Оно выполняет перенаправление с адреса http://contoso.com на адрес https://contoso.com.
   
   > [!IMPORTANT]
   > При наличии уже других тегов `<rule>` в файле `web.config` поместите скопированный тег `<rule>` перед другими тегами `<rule>`.
   > 
   > 
5. Сохраните файл в консоли отладки Kudu. Сразу же после этого все запросы начнут перенаправляться на адрес с использованием протокола HTTPS.

Дополнительные сведения о модуле переопределения URL-адресов IIS см. в документации по [модулю переопределения URL-адресов](http://www.iis.net/downloads/microsoft/url-rewrite).

## <a name="more-resources"></a>Дополнительные ресурсы
* [Центр управления безопасностью Microsoft Azure](/support/trust-center/security/)
* [Параметры конфигурации, разблокированные на веб-сайтах Azure](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
* [Включение ведения журналов диагностики](web-sites-enable-diagnostic-log.md)
* [Настройка веб-приложений в службе приложений Azure](web-sites-configure.md)
* [Портал управления Azure](https://manage.windowsazure.com)

> [!NOTE]
> Если требуется приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](https://azure.microsoft.com/try/app-service/), который поможет быстро создать кратковременное приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.
> 
> 

[customdomain]: web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://social.technet.microsoft.com/wiki/contents/articles/31634.microsoft-trusted-root-certificate-program-participants-v-2016-april.aspx
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/web-sites-configure-ssl-certificate/staticip.png
[website]: ./media/web-sites-configure-ssl-certificate/sslwebsite.png
[scale]: ./media/web-sites-configure-ssl-certificate/sslscale.png
[standard]: ./media/web-sites-configure-ssl-certificate/sslreserved.png
[pricing]: /pricing/details/
[configure]: ./media/web-sites-configure-ssl-certificate/sslconfig.png
[uploadcert]: ./media/web-sites-configure-ssl-certificate/ssluploadcert.png
[uploadcertdlg]: ./media/web-sites-configure-ssl-certificate/ssluploaddlg.png
[sslbindings]: ./media/web-sites-configure-ssl-certificate/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/web-sites-configure-ssl-certificate/waws-certmgr.png
[certwiz1]: ./media/web-sites-configure-ssl-certificate/waws-certwiz1.png
[certwiz2]: ./media/web-sites-configure-ssl-certificate/waws-certwiz2.png
[certwiz3]: ./media/web-sites-configure-ssl-certificate/waws-certwiz3.png
[certwiz4]: ./media/web-sites-configure-ssl-certificate/waws-certwiz4.png



