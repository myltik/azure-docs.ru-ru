#Включение HTTPS для веб-сайта Azure

> [WACOM.NOTE]
> Приступите к работе быстрее - воспользуйтесь НОВЫМ [пошаговым руководством](http://support.microsoft.com/kb/2990804) Azure!  Оно поможет легко и быстро связать пользовательское доменное имя с облачными службами Azure или веб-сайтами Azure И защитить обмен данными (SSL) с ними.

Обмен данными между веб-сайтом и браузером можно защитить с помощью протокола HTTPS, использующего шифрование Secure Socket Layer (SSL). Это наиболее часто используемый способ защиты данных, передаваемых через Интернет. Он гарантирует безопасность взаимодействия посетителя с вашим веб-сайтом. В этой статье описывается, как настроить протокол HTTPS для веб-сайта Azure. 

<a href="bkmk_azurewebsites"></a><h2>HTTPS для домена \*.azurewebsites.net</h2>

Если вы не планируете использовать пользовательское доменное имя, применяя вместо него домен \*.azurewebsites.net, назначенный для вашего веб-сайта платформой Azure (например, contoso.azurewebsites.net), то ваш сайт уже защищен сертификатом, предоставляемым корпорацией Майкрософт. Для доступа к своему сайту вы можете использовать **https://mywebsite.azurewebsites.net**. Однако \*.azurewebsites.net - это домен с подстановочным знаком. Как и [все домены с подстановочными знаками](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/), он не так безопасен, как пользовательский домен с собственным сертификатом. 

Далее в статье содержатся подробные сведения о том, как включить протокол HTTPS для пользовательских доменов, например **contoso.com**, **www.contoso.com** или **\*.contoso.com**

<a href="bkmk_domainname"></a><h2>Включение SSL для пользовательского домена</h2>

Чтобы включить протокол HTTPS для пользовательского домена, например **contoso.com**, необходимо сначала зарегистрировать имя этого домена у регистратора доменных имен. Дополнительные сведения о настройке доменного имени веб-сайта Azure см. в разделе [Настройка пользовательского доменного имени для веб-сайта Azure](/ru-ru/develop/net/common-tasks/custom-dns-web-site/). После регистрации имени пользовательского домена и настройки пользовательского имени для веб-сайта необходимо запросить SSL-сертификат для домена. 

> [WACOM.NOTE] Чтобы включить протокол HTTPS для имен пользовательских доменов, нужно установить **стандартный** режим для плана размещения веб-сайта. Если в настоящий момент используется бесплатный режим или общий режим, это может повлечь за собой дополнительные затраты. Допольнительные сведения о стоимости общего и **стандартного** режимов можно найти в разделе [Сведения о ценах][pricing]. 

Для подготовленного пользовательского домена процесс активации HTTPS для веб-сайта включает такие этапы:

1. [получение SSL-сертификата](#bkmk_getcert)
1. [Настройка стандартного режима](#bkmk_standardmode)
1. [Настройка SSL](#bkmk_configuressl)
1. [Принудительное использование HTTPS для веб-сайта Azure](#bkmk_enforce)

<a href="bkmk_getcert"></a><h2>Получение SSL-сертификата</h2>

Перед запросом сертификата SSL необходимо сначала определить, какие имена доменов будут защищены с помощью этого сертификата. От этого зависит требуемый тип сертификата. Если необходимо защитить одно доменное имя, например **contoso.com** или **www.contoso.com**, вам будет достаточно базового сертификата. Если нужно защитить несколько имен доменов, например **contoso.com**, **www.contoso.com** и **mail.contoso.com**, то можно получить [групповой сертификат](http://en.wikipedia.org/wiki/Wildcard_certificate) или сертификат с [альтернативным именем субъекта](http://en.wikipedia.org/wiki/SubjectAltName) (subjectAltName).

SSL-сертификаты для веб-сайтов Azure должны быть подписаны [центром сертификации](http://en.wikipedia.org/wiki/Certificate_authority) (ЦС). Если у вас еще нет сертификата, нужно получить его в компании, выпускающей SSL-сертификаты. Список центров сертификации можно найти в разделе [Программа корневых сертификатов Windows и Windows Phone 8 SSL (участвующие ЦС)][cas] на портале Microsoft TechNet Wiki.

Сертификат должен отвечать следующим требованиям для SSL-сертификатов в Azure:

* Сертификат должен содержать закрытый ключ.
* Сертификат должен быть создан для обмена ключами, которые можно экспортировать в файл обмена личной информацией (PFX-файл).
* Имя субъекта сертификата должно совпадать с именем домена, которое используется для обращения к веб-сайту. Если сертификат должен обслуживать несколько доменов, необходимо использовать подстановочные значения или указать значения альтернативного имени субъекта (subjectAltName), как обсуждалось ранее.
* Сертификат должен использовать как минимум 2048-разрядное шифрование.
* Веб-сайты Azure не поддерживают сертификаты, выданные серверами частных центров сертификации.

Чтобы получить SSL-сертификат для использования с веб-сайтами Azure, нужно отправить в центр сертификации запрос подписи сертификата (CSR), а затем создать из полученного сертификата PFX-файл. Для этого можно воспользоваться любым инструментом по своему выбору. Ниже приведены самые распространенные способы получения сертификата:

- [Получение сертификата с помощью Certreq.exe](#bkmk_certreq)
- [Получение сертификата с помощью диспетчера IIS](#bkmk_iismgr)
- [Получение сертификата с помощью OpenSSL](#bkmk_openssl)
- [Получение сертификата SubjectAltName с помощью OpenSSL](#bkmk_subjectaltname)
- [Создание самозаверяющих сертификатов (только для тестирования)](#bkmk_selfsigned) 

> [WACOM.NOTE] Во время выполнения этих действий вам предложат ввести значение параметра **Common Name** (Общее имя), например www.contoso.com. Для групповых сертификатов это значение должно быть \*.domainname (например, \*.contoso.com). Если необходима поддержка как групповых имен, например \*.contoso.com, так и имени корневого домена, такого как contoso.com, можно использовать групповой сертификат с альтернативным именем субъекта (subjectAltName).

> [WACOM.NOTE] Сертификаты на основе шифрования эллиптических кривых (ECC) поддерживаются веб-сайтами Azure, однако они относительно новые и нужно работать с центром сертификации по конкретным действиям для создания CSR.

Вам также могут понадобиться **[промежуточные сертификаты](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** (также известные как сертификаты цепочки), если их использует ваш центр сертификации. Использование промежуточных сертификатов считается более безопасным, чем применение "сертификатов без цепочки", поэтому ЦС в большинстве случаев используют такие сертификаты. Промежуточные сертификаты зачастую нужно отдельно загружать с веб-сайта ЦС. Действия, приведенные в данном разделе, описывают процедуру объединения всех промежуточных сертификатов с сертификатом, отправленным на веб-сайт Azure. 

<a href="bkmk_certreq"></a>
###Получение сертификата с помощью Certreq.exe (только для Windows)

Certreq.exe - это служебная программа Windows для создания запросов сертификатов. Она входит в базовый установочный пакет Windows, начиная с Windows XP и Windows Server 2000, поэтому данная программа будет доступной на всех недавних версиях Windows. Далее описаны шаги для получения SSL-сертификата с помощью certreq.exe.

1. Откройте **Блокнот** и создайте новый документ с указанным далее содержимым. Замените **mysite.com** в строке темы на пользовательское доменное имя вашего веб-сайта. Например, Subject = "CN=www.contoso.com".

		[NewRequest]
		Subject = "CN=mysite.com"
		Exportable = TRUE
		KeyLength = 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12
		RequestType = CMC

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1

	Дополнительные сведения об указанных выше вариантах, а также о других доступных вариантах можно найти в [справочной документации Certreq](http://technet.microsoft.com/library/cc725793.aspx).

2. Сохраните текстовый файл под именем **myrequest.txt**.

3. На **начальном экране** или в **меню "Пуск"** запустите файл **cmd.exe**.

4. В командной строке для создания файла запроса сертификата используйте следующую команду:

		certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

	Укажите путь к файлу **myrequest.txt**, созданному при выполнении шага 1, и путь, используемый при создании файла **myrequest.csr**.

5. Отправьте файл **myrequest.csr** в центр сертификации, чтобы получить SSL-сертификат. Возможно, для этого вам потребуется загрузить файл или открыть его в Блокноте и скопировать содержимое непосредственно в веб-форму.

	Список центров сертификации можно найти в разделе [Программа корневых сертификатов Windows и Windows Phone 8 SSL (участвующие ЦС)][cas] на портале Microsoft TechNet Wiki.

6. После того как центр сертификации предоставит вам файл сертификата (.CER), сохраните этот файл на компьютер, используемый для создания запроса, и выполните следующую команду, чтобы принять запрос и завершить процесс создания сертификата.

		certreq -accept -user mycert.cer

	В этом случае файл **mycert.cer**, полученный от центра сертификации, будет использоваться для подписывания сертификата. Никакие файлы создаваться не будут; вместо этого сертификат будет сохранен в хранилище сертификатов Windows.

6. Если ваш центр сертификации использует промежуточные сертификаты, необходимо установить эти сертификаты перед экспортом сертификата в следующем шаге. Обычно сертификаты загружаются из центра сертификации в виде отдельных файлов и предоставляются в различных форматах для различных типов веб-серверов. Выберите версию, которая предназначена для служб Microsoft IIS.

	После загрузки сертификата щелкните на нем правой кнопкой мыши в обозревателе и выберите **Установить сертификат**. Используйте значения по умолчанию в **Мастере импорта сертификатов** и выбирайте **Далее** до завершения импорта.

7. Чтобы экспортировать сертификат из хранилища сертификатов, запустите **certmgr.msc** на **начальном экране** или в **меню "Пуск"**. При появлении **диспетчера сертификатов** разверните **личную** папку, а затем выберите **Сертификаты**. В поле **Кому выдан** найдите запись с именем пользовательского домена, для которого запрашивался сертификат. В поле **Кем выдан** указывается центр сертификации, выдавший этот сертификат.

	![insert image of cert manager here][certmgr]

9. Щелкните сертификат правой кнопкой мыши и выберите **Все задачи**, а затем выберите **Экспорт**. В **мастере экспорта сертификатов** нажмите **Далее**, а затем выберите **Да, экспортировать закрытый ключ**. Нажмите кнопку **Далее**.

	![Export the private key][certwiz1]

10. Выберите **Обмен личной информацией - PKCS #12**, **Включить все сертификаты в цепочке сертификатов** и **Экспортировать все расширенные свойства**. Нажмите кнопку **Далее**.

	![include all certs and extended properties][certwiz2]

11. Выберите **Пароль**, а затем введите пароль и подтвердите его. Нажмите кнопку **Далее**.

	![specify a password][certwiz3]

12. Укажите путь к файлу и имя файла, который будет содержать экспортированный сертификат. Имя файла должно иметь расширение **.pfx**. Чтобы завершить процесс, нажмите кнопку **Далее**.

	![provide a file path][certwiz4]

Теперь можно отправить экспортированный PFX-файл на веб-сайт Azure.

<a href="bkmk_openssl"></a>
###Получение сертификата с помощью OpenSSL

1. Создайте закрытый ключ и запрос подписи сертификата с помощью следующей команды для командной строки, bash или сеанса терминала:

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. При появлении запроса введите соответствующую информацию. Например: 

 		Country Name (2 letter code) 
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

		Укажите следующие дополнительные атрибуты, отправляемые вместе с запросом сертификата

Пароль вызова []: 

	По завершении этого процесса у вас должно быть два файла: **myserver.key** и **server.csr**. Файл **server.csr** содержит запрос подписи сертификата.

3. Отправьте запрос CSR в центр сертификации, чтобы получить SSL-сертификат. Список центров сертификации можно найти в разделе [Программа корневых сертификатов Windows и Windows Phone 8 SSL (участвующие ЦС)][cas] на портале Microsoft TechNet Wiki.

4. Получив сертификат от центра сертификации, сохраните его в файл с именем **myserver.crt**. Если ваш центр сертификации предоставил сертификат в текстовом формате, просто вставьте текст сертификата в файл **myserver.crt**. При просмотре в текстовом редакторе файл должен иметь сходное содержимое:

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

	Сохраните файл.

5. В командной строке, сеансе терминала или Bash для преобразования файлов **myserver.key** и **myserver.crt** в формат **myserver.pfx**, который требуется для веб-сайтов Azure, используйте такую команду:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	При появлении запроса введите пароль для защиты файла .pfx.

	<div class="dev-callout"> 
	<b>Примечание.</b>
	<p>Если ваш центр сертификации использует промежуточные сертификаты, необходимо установить эти сертификаты перед экспортом сертификата в следующем шаге. Обычно сертификаты загружаются из центра сертификации в виде отдельных файлов и предоставляются в различных форматах для различных типов веб-серверов. Выберите версию, которая предоставляется в формате PEM (расширение файла .pem).</p>
	<p>Эти команды демонстрируют процедуру создания PFX-файла, который включает в себя промежуточные сертификаты, содержащиеся в файле <b>intermediate-cets.pem</b>:</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

	После выполнения этой команды вы должны получить файл **myserver.pfx**, который можно использовать для веб-сайтов Azure.

###<a name="bkmk_iismgr"></a>Получение сертификата с помощью диспетчера IIS

Если вы умеете пользоваться диспетчером IIS, можно использовать его для создания сертификата, который может использоваться с веб-сайтами Azure.

1. С помощью диспетчера IIS создайте файл запроса подписи сертификата (CSR) и отправьте его в центр сертификации. Дополнительные сведения о создании CSR можно найти в разделе [Запрос сертификата службы IIS 7][iiscsr].

2. Отправьте запрос CSR в центр сертификации, чтобы получить SSL-сертификат. Список центров сертификации можно найти в разделе [Программа корневых сертификатов Windows и Windows Phone 8 SSL (участвующие ЦС)][cas] на портале Microsoft TechNet Wiki.

3. Для завершения работы с CSR укажите сертификат, предоставленный центром сертификации. Дополнительные сведения о завершении работы с CSR можно найти в разделе [Установка служб IIS 7][installcertiis].

4. Если ваш центр сертификации использует промежуточные сертификаты, необходимо установить эти сертификаты перед экспортом сертификата в следующем шаге. Обычно сертификаты загружаются из центра сертификации в виде отдельных файлов и предоставляются в различных форматах для различных типов веб-серверов. Выберите версию, которая предназначена для служб Microsoft IIS.

	После загрузки сертификата щелкните на нем правой кнопкой мыши в обозревателе и выберите **Установить сертификат**. Используйте значения по умолчанию в **Мастере импорта сертификатов** и выбирайте **Далее** до завершения импорта.

4. Экспортируйте сертификат из диспетчера IIS. Дополнительные сведения об экспорте сертификата можно найти в. разделе [Экспорт сертификата служб IIS 7][exportcertiis]. Экспортированный файл на следующих этапах будет отправляться в Azure для использования с веб-сайтом Azure.

	<div class="dev-callout"> 
	<b>Примечание.</b>
	<p>Во время экспорта убедитесь, что выбран параметр <strong>Да, экспортировать закрытый ключ</strong>. Это позволит включить закрытый ключ в экспортированный сертификат.</p>
	</div>

	<div class="dev-callout"> 
	<b>Примечание.</b>
	<p>Во время экспорта убедитесь, что выбран параметр <strong>Включить все сертификаты в путь сертификации</strong> и <strong>Экспортировать все расширенные свойства</strong>. Это позволит включить в экспортированный сертификат все промежуточные сертификаты.</p>
	</div>


###<a href="bkmk_subjectaltname"></a>Получение сертификата SubjectAltName с помощью OpenSSL

OpenSSL можно использовать для создания запроса сертификата, который использует расширение SubjectAltName для поддержки нескольких имен доменов в одном сертификате, однако для этого требуется файл конфигурации. Для создания файла конфигурации выполните следующие шаги, а затем используйте этот файл для запроса сертификата.

1. Создайте новый файл с именем __sancert.cnf__, имеющий следующее содержимое:
 
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

	Найдите строку, которая начинается с subjectAltName. Замените указанные имена доменов на те имена доменов, которые должны поддерживаться вместе с общим именем. Например: 

		subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

	Не нужно менять значение поля commonName_default при появлении запроса на ввод общего имени на одном из следующих этапов.

2. Сохраните файл __sancert.cnf__.

1. Создайте закрытый ключ и запрос подписи сертификата с помощью файла конфигурации sancert.cnf. С использованием Bash или сеанса терминала введите такую команду:

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. При появлении запроса введите соответствующую информацию. Например: 

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com
 

	По завершении этого процесса у вас должно быть два файла: **myserver.key** и **server.csr**. Файл **server.csr** содержит запрос подписи сертификата.

3. Отправьте запрос CSR в центр сертификации, чтобы получить SSL-сертификат. Список центров сертификации можно найти в разделе [Программа корневых сертификатов Windows и Windows Phone 8 SSL (участвующие ЦС)][cas] на портале Microsoft TechNet Wiki.

4. Получив сертификат от центра сертификации, сохраните его в файл с именем **myserver.crt**. Если ваш центр сертификации предоставил сертификат в текстовом формате, просто вставьте текст сертификата в файл **myserver.crt**. При просмотре в текстовом редакторе файл должен иметь сходное содержимое:

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

	Сохраните файл.

5. В командной строке, сеансе терминала или Bash для преобразования файлов **myserver.key** и **myserver.crt** в формат **myserver.pfx**, который требуется для веб-сайтов Azure, используйте такую команду:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	При появлении запроса введите пароль для защиты файла .pfx.

	<div class="dev-callout"> 
	<b>Примечание.</b>
	<p>Если ваш центр сертификации использует промежуточные сертификаты, необходимо установить эти сертификаты перед экспортом сертификата в следующем шаге. Обычно сертификаты загружаются из центра сертификации в виде отдельных файлов и предоставляются в различных форматах для различных типов веб-серверов. Выберите версию, которая предоставляется в формате PEM (расширение файла .pem).</p>
	<p>Эти команды демонстрируют процедуру создания PFX-файла, который включает в себя промежуточные сертификаты, содержащиеся в файле <b>intermediate-cets.pem</b>:</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

	После выполнения этой команды вы должны получить файл **myserver.pfx**, который можно использовать для веб-сайтов Azure.


###<a href="bkmk_selfsigned"></a>Создание самозаверяющего сертификата (только для тестирования)

В некоторых случаях может понадобиться получить сертификат для тестирования и отложить покупку сертификата у одного из доверенных ЦС до развертывания рабочей среды. В этом случае можно использовать самозаверяющие сертификаты. Самозаверяющий сертификат - это сертификат, который вы создаете и подписываете от имени центра сертификации. Хотя сертификат можно использовать для защиты веб-сайта, большинство браузеров будут возвращать ошибки при посещении сайта, поскольку сертификат не был подписан доверенным ЦС. Некоторые браузеры могут даже отказаться отображать сайт.

- [Создание самозаверяющего сертификата с помощью makecert](#bkmk_ssmakecert)
- [Создание самозаверяющего сертификата с помощью OpenSSL](#bkmk_ssopenssl)

<a href="bkmk_ssmakecert"></a>
#### Создание самозаверяющего сертификата с помощью makecert ####

Можно создать тестовый сертификат из системы Windows с установленной программой Visual Studio. Для этого выполните следующие действия:

1. В меню **Пуск** или на **начальном экране** найдите **командную строку разработчика**. Щелкните **командную строку разработчика** и выберите **Запуск от имени администратора**.

	Если появится диалоговое окно контроля учетных записей, выберите **Да** для продолжения.

2. В командной строке разработчика используйте приведенную ниже команду для создания нового самозаверяющего сертификата. Необходимо заменить **serverdnsname** на DNS вашего веб-сайта.

		makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

	Эта команда создаст сертификат, который действует с 01.01.2013 по 01.01.2014, и сохранит расположение в хранилище сертификатов CurrentUser.

3. В **меню "Пуск"** или на **начальном экране** найдите приложение **Windows PowerShell** и запустите его.

4. В командной строке Windows PowerShell экспортируйте созданный ранее сертификат с помощью следующих команд:

		$mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
		get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

	При этом пароль будет сохранен в виде защищенной строки в $mypwd, затем будет найден сертификат с использованием имени DNS, указанного в параметре **dnsname**, и будет выполнен экспорт в файл, заданный параметром **filepath**. Защищенная строка, содержащая пароль, используется для защиты экспортируемого файла.

<a href="bkmk_ssopenssl"></a>
####Создание самозаверяющего сертификата с помощью OpenSSL ####

1. Создайте новый документ с именем **serverauth.cnf** и таким содержимым:

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName			= Country Name (2 letter code)
        countryName_min			= 2
        countryName_max			= 2
        stateOrProvinceName		= State or Province Name (full name)
        localityName			= Locality Name (eg, city)
        0.organizationName		= Organization Name (eg, company)
        organizationalUnitName		= Organizational Unit Name (eg, section)
        commonName			= Common Name (eg, your website's domain name)
        commonName_max			= 64
        emailAddress			= Email Address
        emailAddress_max		= 40

        [ req_attributes ]
        challengePassword		= A challenge password
        challengePassword_min		= 4
        challengePassword_max		= 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

	Это позволит указать параметры конфигурации, необходимые для создания SSL-сертификата, который может использоваться веб-сайтами Azure.

2. Создайте новый самозаверяющий сертификат с помощью следующей команды для командной строки, bash или сеанса терминала:

		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	При этом создается новый сертификат с использованием параметров конфигурации, указанных в файле **serverauth.cnf**.

3. Воспользуйтесь следующей командой, чтобы экспортировать сертификат в PFX-файл, который можно отправить на веб-сайт Azure:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	При появлении запроса введите пароль для защиты файла .pfx.

	Созданный этой командой файл **myserver.pfx** может использоваться для защиты веб-сайта Azure при тестировании.

<a href="bkmk_standardmode"></a><h2>Настройка стандартного режима</h2>

Включение HTTPS для пользовательского домена возможно только при выборе **стандартного** режима для плана размещения веб-сайтов Azure. Вот как перейти в **стандартный** режим:

> [WACOM.NOTE] Прежде чем перевести веб-сайт с бесплатного режима на **стандартный**, необходимо снять существующие ограничения расходов, установленные для вашей подписки на веб-сайт; в противном случае сайт может оказаться недоступным при превышении ограничений до завершения расчетного периода. Дополнительные сведения о ценах на режим общего доступа и **стандартный** режим можно найти в разделе [Сведения о ценах][pricing].

1. Откройте в браузере [портал управления][portal].

2. На вкладке **Веб-сайты** щелкните имя своего веб-сайта.

	![selecting a web site][website]

3. Выберите вкладку **МАСШТАБ**.

	![The scale tab][scale]

4. В разделе **Общие** установите план размещения веб-сайта, щелкнув **СТАНДАРТНЫЙ**.

	![standard mode selected][standard]

5. Щелкните **Сохранить**. При появлении запроса нажмите кнопку **Да**.

	> [WACOM.NOTE] Если появляется ошибка "Не выполнена настройка масштабирования для веб-сайта &lt;имя веб-сайта&gt;", можно использовать кнопку "Сведения" для получения дополнительной информации. Может появиться сообщение об ошибке "Недостаточно доступных стандартных экземпляров серверов для удовлетворения запроса". При появлении этой ошибки обратитесь в [службу поддержки Azure](http://www.windowsazure.com/ru-ru/support/options/).


##<a href="bkmk_configuressl"></a>Настройка SSL

Прежде чем выполнять действия, описанные в этом разделе, необходимо связать пользовательское доменное имя с веб-сайтом Azure. Дополнительные сведения можно найти в разделе [Настройка пользовательского имени домена для веб-сайта Azure][customdomain].

1. Откройте в браузере [портал управления Azure][portal].

2. На вкладке **Веб-сайты** щелкните имя веб-сайта и выберите вкладку **НАСТРОЙКА**.

	![the configure tab][configure]

3. В разделе **сертификаты** нажмите кнопку **загрузить сертификат**.

	![upload a certificate][uploadcert]

4. С помощью диалогового окна **загрузки сертификата** выберите PFX-файл сертификата, созданный ранее с помощью диспетчера IIS или OpenSSL. Укажите пароль, используемый для защиты файла .pfx (при наличии). В конце нажмите кнопку**проверить** для загрузки сертификата.

	![upload certificate dialog][uploadcertdlg]

5. В разделе **привязки ssl** вкладки **НАСТРОЙКА** в раскрывающихся списках выберите имя домена для защиты с применением SSL, а также используемый сертификат. Можно также выбрать использование [подключения с указанием имени сервера][sni] (SNI) или SSL на основе IP.

	![ssl bindings][sslbindings]
	
	* SSL на основе IP связывает сертификат с именем домена, сопоставляя выделенный открытый IP-адрес сервера с именем домена. Для этого необходимо, чтобы для каждого имени домена (contoso.com, fabricam.com и т. д.), связанного с вашей службой, был доступен выделенный IP-адрес. Это традиционный метод сопоставления сертификатов SSL с веб-сервером.

	* SSL-подключения на основе SNI являются расширением SSL и [Transport Layer Security][tls] (TLS), которое позволяет нескольким доменам совместно использовать IP-адрес с отдельными сертификатами безопасности для каждого домена. Большинство современных браузеров (включая Internet Explorer, Chrome, Firefox и Opera) поддерживает SNI, однако более старые браузеры могут не поддерживать SNI. Дополнительные сведения о SNI можно найти в статье Википедии [Server Name Indication][sni] (англ.).

6. Чтобы сохранить изменения и активировать SSL, нажмите кнопку **Сохранить**.

> [WACOM.NOTE]  Если выбран вариант **SSL-подключения на основе IP** и пользовательский домен настроен с помощью записи А, необходимо выполнить следующие дополнительные шаги:
>
> 1. После настройки привязки SSL-подключения на основе IP-адреса вашему веб-сайту будет предоставлен выделенный IP-адрес. Этот IP-адрес можно найти на странице **Панель мониторинга** вашего веб-сайта в разделе **Сводка**. Он будет указан как **виртуальный IP-адрес**:
>    
>     ![Virtual IP address](./media/configure-ssl-web-site/staticip.png)
>    
>     Обратите внимание: этот IP-адрес будет отличаться от виртуального IP-адреса, который использовался ранее для настройки записи A для вашего домена. Если выбрано использование SSL-подключения на основе SNI или не настроено использование SSL, для этой записи адрес указан не будет.
>
> 2. С помощью средств, предоставляемых регистратором имени домена, измените запись A для имени пользовательского домена, указав IP-адрес из предыдущего шага.


На этом этапе веб-сайт должен открываться с использованием HTTPS://, а не HTTP://, что подтверждает правильность настройки сертификата.

##<a href="bkmk_enforce"></a>Принудительное использование HTTPS для веб-сайта Azure

Веб-сайты Azure *не* требуют обязательного использования протокола HTTPS. Посетители по-прежнему могут получить доступ к вашему сайту, используя протокол HTTP, что может поставить под угрозу безопасность веб-сайта. Если вы хотите принудительно обеспечить использование безопасного соединения для своего веб-сайта, можно воспользоваться модулем **переопределения URL-адресов**. Модуль переопределения URL-адресов включен в веб-сайты Azure и дает возможность определять правила, которые применяются к входящим запросам перед их обработкой и отправкой в приложение. **Его можно использовать для приложений, написанных на любом языке программирования, поддерживаемом веб-сайтами Azure.** 

> [WACOM.NOTE] Приложения .NET MVC вместо модуля переопределения URL-адресов должны использовать фильтр [RequireHttps](http://msdn.microsoft.com/ru-ru/library/system.web.mvc.requirehttpsattribute.aspx). Дополнительные сведения об использовании фильтра RequireHttps можно найти в статье [Развертывание безопасного приложения ASP.NET MVC 5 с членством, OAuth и базой данных SQL на веб-сайте Azure](/ru-ru/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/).
> 
> Сведения о программном перенаправлении запросов с помощью других языков программирования и платформ см. в документации для этих технологий.

Правила модуля переопределения URL-адресов задаются в файле **web.config**, который хранится в корневом каталоге приложения. В примере ниже показано базовое правило модуля переопределения URL-адресов, которое заставляет весь входящий трафик использовать HTTPS.

<a name="example"></a>**Пример файла Web.Config модуля переопределения URL-адресов**

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <rewrite>
	      <rules>
	        <rule name="Force HTTPS" enabled="true">
	          <match url="(.*)" ignoreCase="false" />
	          <conditions>
	            <add input="{HTTPS}" pattern="off" />
	          </conditions>
	          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
	        </rule>
	      </rules>
	    </rewrite>
	  </system.webServer>
	</configuration>

Это правило работает путем возврата кода состояния HTTP 301 (постоянное перенаправление), когда пользователь запрашивает страницу с помощью HTTP. Код состояния 301 перенаправляет запрос на тот же запрошенный посетителем URL-адрес, но заменяет в запросе HTTP на HTTPS. Например, запрос HTTP://contoso.com будет перенаправлен на адрес HTTPS://contoso.com.

> [WACOM.NOTE] Если ваше приложение написано на **Node.js**, **PHP**, **Python Django** или **Java**, в нем скорее всего отсутствует файл web.config. Однако приложения на **Node.js**, **Python Django** и **Java** на самом деле используют web.config при размещении на веб-сайтах Azure, потому что Azure автоматически создает этот файл при развертывании, и вы его не видите. Если включить этот файл в приложение, он переопределит файл, автоматически создаваемый Azure.

###.NET

Для приложений .NET измените файл web.config и добавьте раздел **&lt;rewrite>** из этого [примера](#example) в раздел **&lt;system.WebServer>**.

Если файл web.config уже содержит раздел **&lt;rewrite>**, добавьте **&lt;rule>** из этого [примера](#example) в качестве первой записи раздела **&lt;rules>**.

###PHP

Для приложений PHP просто сохраните [пример](#example) как файл web.config в корневом каталоге приложения, а затем заново разверните приложение на веб-сайте Azure.

###Node.js, Python Django и Java

Если в приложениях Node.js, Python Django и Java отсутствует файл web.config, то он создается автоматически, но существует только на сервере, поскольку создается во время развертывания. Этот автоматически созданный файл содержит параметры, указывающие Azure, как следует размещать приложение.

Чтобы получить этот автоматически созданный файл с веб-сайта и изменить его, выполните следующие действия.

1. Загрузите файл с помощью FTP (см. статью [Отправка и загрузка файлов с помощью FTP и сбор журналов диагностики](http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx)).

2. Добавьте его в корневой каталог приложения.

3. Добавьте в него правила переопределения, используя следующие сведения.

	* **Node.js и Python Django**

		В файле web.config, созданном для приложений Node.js и Python Django, уже будет раздел **&lt;rewrite>**, содержащий записи **&lt;rule>**, необходимые для соответствующего функционирования сайта. Чтобы заставить сайт использовать HTTPS, добавьте **&lt;rule>** из примера в качестве первой записи в разделе **&lt;rules>**. Это обеспечит применение HTTPS и не повлияет на остальные правила.

	* **Java**
	
		Файл web.config для приложений Java, использующих Apache Tomcat, не содержит раздел **&lt;rewrite>**, поэтому необходимо добавить раздел **&lt;rewrite>** из примера в раздел **&lt;system.webServer>**.

4. Выполните повторное развертывание проекта (включая обновленный файл web.config) в Azure.

После развертывания файла web.config с правилом переопределения, заставляющим применять HTTPS, это правило вступит в действие немедленно, и все запросы начнут перенаправляться на HTTPS.

Дополнительные сведения о модуле переопределения URL-адресов IIS можно найти в документации по [переопределению URL-адресов](http://www.iis.net/downloads/microsoft/url-rewrite). 

## Дополнительные ресурсы ##
- [Центр управления безопасностью Microsoft Azure](/ru-ru/support/trust-center/security/)
- [Configuration options unlocked in Azure Web Sites (англ.)](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Включение ведения журналов диагностики](/ru-ru/documentation/articles/web-sites-enable-diagnostic-log/)
- [Настройка веб-сайтов](/ru-ru/documentation/articles/web-sites-configure/)
- [Портал управления Azure](https://manage.windowsazure.com)

[customdomain]: /ru-ru/develop/net/common-tasks/custom-dns-web-site/
[iiscsr]: http://technet.microsoft.com/ru-ru/library/cc732906(WS.10).aspx
[cas]: http://go.microsoft.com/fwlink/?LinkID=269988
[installcertiis]: http://technet.microsoft.com/ru-ru/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/ru-ru/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/configure-ssl-web-site/staticip.png
[website]: ./media/configure-ssl-web-site/sslwebsite.png
[scale]: ./media/configure-ssl-web-site/sslscale.png
[standard]: ./media/configure-ssl-web-site/sslreserved.png
[pricing]: https://www.windowsazure.com/ru-ru/pricing/details/
[configure]: ./media/configure-ssl-web-site/sslconfig.png
[uploadcert]: ./media/configure-ssl-web-site/ssluploadcert.png
[uploadcertdlg]: ./media/configure-ssl-web-site/ssluploaddlg.png
[sslbindings]: ./media/configure-ssl-web-site/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/configure-ssl-web-site/waws-certmgr.png
[certwiz1]: ./media/configure-ssl-web-site/waws-certwiz1.png
[certwiz2]: ./media/configure-ssl-web-site/waws-certwiz2.png
[certwiz3]: ./media/configure-ssl-web-site/waws-certwiz3.png
[certwiz4]: ./media/configure-ssl-web-site/waws-certwiz4.png
