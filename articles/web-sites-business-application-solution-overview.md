<properties urlDisplayName="Create a Line-of-Business Application on Azure Websites" pageTitle="Создание бизнес-приложения на веб-сайтах Azure" metaKeywords="Web Sites" description="Это руководство содержит технический обзор использования веб-сайтов Azure для создания бизнес-приложений для интрасети. Это включает стратегии аутентификации, ретрансляцию Service Bus и мониторинг." umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="wpickett" title="Create a Line-of-Business Application on Azure Websites" authors="jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/01/2014" ms.author="jroth" />



# Создание бизнес-приложения на веб-сайтах Azure

Это руководство содержит технический обзор использования веб-сайтов Azure для создания бизнес-приложений. Для целей настоящего документа предполагается, что эти приложения являются приложениями интрасети, которые должны быть защищены для внутреннего использования. Существуют две типичных характеристики бизнес-приложений. Эти приложения требуют проверки подлинности, обычно с использованием каталога организации. И для них обычно требуется определенный уровень доступа и интеграции с локальными службами и данными. Это руководство предназначено для создания бизнес-приложений на [веб-сайтах Azure][websitesoverview]. Однако существуют ситуации, когда лучше подходят [облачные службы Azure][csoverview] или [виртуальные машины Azure][vmoverview]. Важно разобраться в различиях между этими вариантами, описанными в статье [Веб-сайты Azure, облачные службы и виртуальные машины: когда они используются][chooseservice]. 

В этом руководстве рассматриваются следующие вопросы:

- [Преимущества](#benefits)
- [Выбор стратегии аутентификации](#authentication)
- [Создание веб-сайта Azure, поддерживающего аутентификацию](#createintranetsite)
- [Использование Service Bus для интеграции с локальными ресурсами](#servicebusrelay)
- [Мониторинг приложения](#monitor)

<div class="dev-callout">
<strong>Примечание.</strong>
<p>В этом руководстве представлены некоторые из наиболее распространенных вопросов и задач, связанных с разработкой общедоступных сайтов .COM. Но существуют и другие возможности веб-сайтов Azure, которые можно использовать в конкретной реализации. Чтобы узнать об этих возможностях, см. другие руководства в статьях <a href="http://www.windowsazure.com/ru-ru/manage/services/web-sites/global-web-presence-solution-overview/">"Глобальное веб-присутствие"</a> и <a href="http://www.windowsazure.com/ru-ru/manage/services/web-sites/digital-marketing-campaign-solution-overview">"Цифровые маркетинговые кампании"</a>.</p>
</div>

##<a name="benefits"></a>Преимущества
Поскольку бизнес-приложения обычно предназначены для корпоративных пользователей, следует проанализировать причины использования облачных и локальных корпоративных ресурсов и инфраструктур. Во-первых, существует ряд типичных преимуществ переноса в облако, таких как возможность масштабирования в сторону увеличения и уменьшения в соответствии с динамической нагрузкой. Например, рассмотрим приложение, обрабатывающее данные ежегодных аттестаций. Большую часть года приложения этого типа обрабатывают очень небольшой трафик. Но в период аттестаций трафик для большой компании резко возрастает. Azure предоставляет варианты масштабирования, позволяющие компании выполнить горизонтальное масштабирование, чтобы справиться с нагрузкой в период большого трафика, связанного с аттестациями, и экономить деньги в течение всего остального года благодаря обратному масштабированию. Еще одним преимуществом облака является возможность уделить больше внимания разработке приложений и меньше - приобретению инфраструктуры и управлению инфраструктурой.

Кроме этих стандартных преимуществ, размещение бизнес-приложения в облаке предоставляет сотрудникам и партнерам больше возможностей использовать приложение из любой точки мира. В этом случае пользователям не потребуется подключаться к корпоративной сети для использования приложения, а ИТ-подразделение будет избавлено от необходимости реализовывать сложные решения с обратным прокси. Существует несколько вариантов проверки подлинности, обеспечивающих защиту доступа к корпоративным приложениям. Эти варианты рассматриваются в разделах данного руководства.

##<a name="authentication"></a>Выбор стратегии проверки подлинности
Для сценария бизнес-приложения выбранная стратегия проверки подлинности является одним из наиболее важных решений. Существует несколько вариантов:

- Использование [службы Azure Active Directory][adoverview]. Эту службу можно использовать в качестве отдельного каталога либо синхронизировать ее с локальной службой Active Directory. В этом случае приложения для проверки подлинности пользователей взаимодействуют со службой Azure Active Directory. Общую информацию об этом подходе см. в разделе [Использование Azure Active Directory][adusing].
- Использование для установки Active Directory виртуальных машин и виртуальной сети Azure. Это позволяет распространить в облако локальную установку Active Directory. Можно также использовать службы федерации Active Directory (ADFS) для федеративного направления запросов удостоверений обратно в локальную службу AD. Затем проверка подлинности для приложения Azure выполняется с помощью ADFS в локальной службе Active Directory. Дополнительную информацию об этом подходе см. в разделах [Выполнение Windows Server Active Directory на виртуальных машинах][adwithvm] и [Руководства по развертыванию Windows Server Active Directory на виртуальных машинах Azure][addeployguidelines]. 
- Использование промежуточной службы, такой как [служба Azure Access Control Service][acs2], для применения нескольких служб идентификации для аутентификации пользователей. Это позволяет абстрагировать проверку подлинности либо с помощью Active Directory, либо с помощью другого поставщика удостоверений. Дополнительную информацию см. в разделе [Использование Azure Active Directory Access Control][acsusing].

В этом сценарии бизнес-приложения первый вариант, использующий службу Azure Active Directory, предоставляет самый быстрый способ реализовать стратегию проверки подлинности для приложения. Далее в этом руководстве основное внимание уделяется именно Azure Active Directory. Тем не менее, в зависимости от бизнес-требований одно из двух других решений может подойти лучше. Например, если синхронизировать данные удостоверения с облаком нельзя, лучшим вариантом может оказаться решение ADFS. Либо, если необходимо поддерживать других поставщиков удостоверений, например Facebook, то лучше подойдет решение ACS.

Перед началом установки новой службы Azure Active Directory следует отметить, что существующие службы, такие как Office 365 и Windows Intune, уже используют Azure Active Directory. В этом случае существующая подписка будет связана с подпиской Azure. Дополнительную информацию см. в разделе [Что такое клиент Azure AD][adtenant].

Если в настоящее время нет службы, уже использующей Azure Active Directory, можно создать новый каталог на портале управления. Чтобы создать новый каталог, воспользуйтесь разделом **Active Directory** на портале управления.

![BusinessApplicationsAzureAD][BusinessApplicationsAzureAD]

После создания каталога можно создать пользователей, интегрированные приложениям и домены, а также управлять ими.

![BusinessApplicationsADUsers][BusinessApplicationsADUsers]

Полное пошаговое руководство для этих действий см. в разделе [Добавление единого входа в веб-приложение с помощью Azure AD][adsso]. При использовании этого нового каталога в качестве автономного ресурса следующим шагом является разработка приложений, интегрированных с каталогом. Но при наличии удостоверения локальной службы Active Directory обычно нужно синхронизировать ее с новым каталогом Active Directory Azure. Дополнительную информацию о выполнении соответствующих действий см. в разделе [Общие сведения об интеграции каталогов][dirintegration].

После создания и заполнения каталога необходимо создать веб-приложения, требующие проверки подлинности, и интегрировать их с каталогом. Эти действия описаны в следующих двух разделах.

##<a name="createintranetsite"></a>Создание веб-сайта Azure, поддерживающего проверку подлинности
В сценарии глобального веб-присутствия были рассмотрены различные варианты создания и развертывания нового веб-сайта. Тем, кто недавно работает с веб-сайтами Azure, рекомендуется [ознакомиться с этой информацией][scenarioglobalweb]. Приложение ASP.NET в Visual Studio является типичным вариантом веб-приложения интрасети, использующим проверку подлинности Windows. Одной из причин такого выбора является тесная интеграция и поддержка этого сценария, обеспечиваемая в ASP.NET и Visual Studio.

Например, при создании проекта ASP.NET MVC 4 в Visual Studio в диалоговом окне создания проекта можно выбрать вариант создания **Приложение интрасети**.

![BusinessApplicationsVSIntranetApp][BusinessApplicationsVSIntranetApp]

Этот параметр вносит изменения в настройки проекта для поддержки проверки подлинности Windows. В частности, в файле web.config для атрибута **mode** элемента **authentication** установлено значение **Windows**. При создании другого проекта ASP.NET, такого как проект веб-форм, или при работе с существующим проектом это изменение необходимо внести вручную.

Для проекта MVC необходимо также изменить два значения в окне свойств проекта. Установите для параметра **Проверка подлинности Windows** значение **Включено**, а для параметра **Анонимная проверка подлинности** - значение **Отключено**.

![BusinessApplicationsVSProperties][BusinessApplicationsVSProperties]

Чтобы проверять подлинность с помощью Azure Active Directory, необходимо зарегистрировать это приложение в каталоге, а затем изменить конфигурацию приложения для подключения. В Visual Studio это можно сделать двумя способами:

- [Инструмент идентификации и доступа для Visual Studio](#identityandaccessforvs)
- [Инструменты Microsoft ASP.NET для Azure Active Directory](#aspnettoolsforwaad)

###<a name="identityandaccessforvs"></a>Средство идентификации и доступа для Visual Studio
Один из способов - использование [инструмента идентификации и доступа][identityandaccess], который можно скачать и установить. Это средство интегрируется с Visual Studio в контекстном меню проекта. Следующие указания и снимки экрана получены с помощью Visual Studio 2012. Щелкните правой кнопкой мыши проект и выберите **Идентификация и доступ**. Необходимо настроить три момента. На вкладке **Поставщики** необходимо ввести **путь к документу метаданных STS** и **URI идентификатора приложения**(информацию о получении этих значений см. в разделе [Регистрация приложений в Azure Active Directory](#registerwaadapp)).

![BusinessApplicationsVSIdentityAndAccess][BusinessApplicationsVSIdentityAndAccess]

Последнее необходимое изменение конфигурации нужно выполнить в диалоговом окне **Удостоверение и доступ** на вкладке **Конфигурация**. Здесь необходимо установить флажок **Включить файлы cookie веб-фермы**. Подробное руководство для этих действий см. в разделе [Добавление единого входа в веб-приложение с помощью Azure AD][adsso].

####<a name="registerwaadapp"></a>Регистрация приложений в Azure Active Directory
Чтобы заполнить вкладку **Поставщики**, следует зарегистрировать приложение с помощью Azure Active Directory. На портале управления Azure в разделе **Active Directory** выберите каталог, а затем перейдите на вкладку **Приложения**. Она позволяет добавить веб-сайт Azure по URL-адресу. Обратите внимание, что при выполнении этих действий для локальной отладки в Visual Studio в качестве URL-адреса сначала указывается адрес localhost. Позже, когда выполняется развертывание, можно изменить этот адрес на фактический URL-адрес веб-сайта.

![BusinessApplicationsADIntegratedApps][BusinessApplicationsADIntegratedApps]

После добавления портал предоставляет как путь к документу метаданных STS (**URL-адрес документа метаданных федерации**), так и **URI идентификатора приложения**. Эти значения используются на вкладке **Поставщики** диалогового окна **Идентификация и доступ** в Visual Studio. 

![BusinessApplicationsADAppAdded][BusinessApplicationsADAppAdded]

###<a name="aspnettoolsforwaad"></a>Средства Microsoft ASP.NET для Azure Active Directory
Альтернативным способом выполнения предыдущих действий является использование [инструментов Microsoft ASP.NET для Azure Active Directory][aspnettools]. Для этого в Visual Studio в меню **Проект** выберите пункт **Включить аутентификацию Azure**. Появляется простое диалоговое окно с запросом адреса вашего домена Azure Active Directory (а не URL-адреса приложения).

![BusinessApplicationsVSEnableAuth][BusinessApplicationsVSEnableAuth]

Если вы являетесь администратором этого домена Active Directory, установите флажок **Подготовить это приложение в Azure AD**. Приложение будет зарегистрировано в Active Directory. Если вы не являетесь администратором, снимите этот флажок и сообщите появившиеся сведения администратору. Этот администратор может создать интегрированное приложение с помощью портала управления, используя приведенную выше последовательность действий для средства идентификации и доступа. Указания по использованию инструментов ASP.NET для Azure Active Directory см. в статье [Аутентификация Azure][azureauthtutorial].

При управлении бизнес-приложением для его развертывания можно использовать любую из поддерживаемых систем управления версиями. Но из-за высокого уровня интеграции Visual Studio в этом сценарии, вероятно, оптимальным вариантом системы управления версиями будет Team Foundation Service (TFS). В этом случае следует отметить, что веб-сайты Azure поддерживают интеграцию с TFS. На портале управления перейдите на вкладку **Панель мониторинга** своего веб-сайта. Затем выберите **Настроить развертывания в системе управления версиями**. Следуйте указаниям по использованию TFS. 

![BusinessApplicationsDeploy][BusinessApplicationsDeploy]

##<a name="servicebusrelay"></a>Использование Service Bus для интеграции с локальными ресурсами
Множество бизнес-приложений должны интегрироваться с данными и службами на площадке заказчика. Существует несколько причин, не позволяющих переместить определенные типы данных в облако. Эти причины могут быть обусловлены практическими соображениями или регламентирующими нормативами. Принимая на стадии планирования решение о том, какие данные размещать в Azure, а какие хранить локально, необходимо просмотреть ресурсы [Центра управления безопасностью Azure][trustcenter]. Гибридные веб-приложения выполняются в Azure и обращаются к ресурсам, которые должны размещаться локально.

При использовании виртуальных машин или облачных служб можно применять виртуальную сеть для подключения приложений Azure к корпоративной сети. Но веб-сайты не поддерживают виртуальные сети, поэтому оптимальным способом для интеграции этого типа с веб-сайтами является применение [службы ретрансляции Azure Service Bus][sbrelay]. Служба ретрансляции Service Bus позволяет приложениям из облака безопасно подключаться к службам WCF, работающим в корпоративной сети. Service Bus обеспечивает это взаимодействие без открытия портов брандмауэра. 

На следующей схеме и облачное приложение, и локальная служба WCF взаимодействуют с Service Bus, используя ранее созданное пространство имен. У локальной службы WCF есть доступ к внутренним данным и службам, которые нельзя переместить в облако. Служба WCF регистрирует конечную точку в пространстве имен. Веб-сайт, работающий в Azure, также подключается к этой конечной точке в Service Bus. Для этого им понадобится только возможность выполнения исходящих открытых HTTP-запросов.

![BusinessApplicationsServiceBusRelay][BusinessApplicationsServiceBusRelay]

Затем служба Service Bus соединяет облачное приложение с локальной службой WCF. Это обеспечивает базовую архитектуру для создания гибридных приложений, использующих службы и ресурсы как в Azure, так и локальные. Дополнительную информацию см. в статье [Как использовать службу ретрансляции Service Bus][sbrelayhowto] и учебнике по [ретрансляции сообщений Service Bus][sbrelaytutorial]. Пример, демонстрирующий этот способ, см. в статье [Enterprise Pizza - Connecting Web Sites to On-premise Using Service Bus][enterprisepizza] (Корпоративная пицца - подключение веб-сайтов к локальным ресурсам с помощью Service Bus).

##<a name="monitor"></a>Мониторинг приложения
Бизнес-приложения получают преимущества стандартных функциональных возможностей веб-сайтов, таких как масштабирование и отслеживание. Для бизнес-приложения, нагрузка на которое зависит от конкретных дней или часов, может быть полезной функция автомасштабирования (предварительная версия), обеспечивающая масштабирование сайта в соответствии с нагрузкой для эффективного использования ресурсов. В случае мониторинга возможен мониторинг конечных точек и мониторинг квот. Все эти сценарии более подробно рассматриваются в сценариях, связанных с [глобальным веб-присутствием][scenarioglobalweb] и [цифровыми маркетинговыми кампаниями][scenariodigitalmarketing].

Требования к мониторингу могут различаться для разных бизнес-приложений, обладающих разной значимостью для бизнеса. Для более важных приложений рассмотрите возможность инвестирования в решение для мониторинга, созданное независимым производителем, такое как [New Relic][newrelic].

Бизнес-приложениями обычно управляют ИТ-специалисты. В случае ошибок или непредвиденного поведения ИТ-специалисты могут включить подробное ведение журнала, а затем проанализировать полученные данные для определения проблемы. На портале управления выберите вкладку **Настройка** и просмотрите параметры в разделах **диагностика приложений** и **диагностика сайта**. 

![BusinessApplicationsDiagnostics][BusinessApplicationsDiagnostics]

Используйте различные журналы приложений и сайта для устранения неполадок веб-сайта. Обратите внимание, что для некоторых параметров задано значение **Файловая система**, при выборе которого файлы журнала помещаются в файловую систему сайта. Для доступа к ним можно использовать FTP, Azure PowerShell и средства командной строки Azure. Для других параметров задано значение **Хранилище**. В этом случае данные отправляются в указанную учетную запись хранилища Azure. Для параметра **Журналы веб-сервера** также можно указать политику хранения при использовании хранилища или дисковую квоту при использовании файловой системы. Это позволяет избежать неограниченного увеличения объема хранимых данных журнала.

![BusinessApplicationsDiagRetention][BusinessApplicationsDiagRetention]

Дополнительную информацию о параметрах ведения журнала см. в разделе [Практическое руководство. Настройка диагностики и скачивание журналов для веб-сайта][configurediagnostics].

Помимо просмотра исходных журналов с помощью FTP или служебных программ хранилища, таких как обозреватель хранилищ Azure, данные журнала также можно просматривать в Visual Studio. Дополнительную информацию об использовании этих журналов в сценариях устранения неполадок см. в статье [Устранение неполадок, связанных с веб-сайтами Azure, в Visual Studio][troubleshootwebsites].

##<a name="summary"></a>Сводка
Azure позволяет размещать в облаке защищенные приложения интрасети. Azure Active Directory предоставляет возможность проверки подлинности пользователей, чтобы только члены организации могли получать доступ к приложениям. Служба ретрансляции Service Bus предоставляет веб-приложениям механизм взаимодействия с локальными службами и данными. Этот гибридный подход к приложениям упрощает публикацию бизнес-приложения в облаке без переноса всех зависимых данных и служб. Для развернутых бизнес-приложений можно использовать преимущества стандартных возможностей масштабирования и мониторинга, предоставляемых веб-сайтами Azure. Дополнительные сведения см. в следующих технических статьях:

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Область</th>
   <th align="left" valign="top">Ресурсы</th>
</tr>
<tr>
   <td valign="middle"><strong>План</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/ru-ru/manage/services/web-sites/choose-web-app-service">Веб-сайты Azure, облачные службы и виртуальные машины: когда они используются?</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Создание и развертывание</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/ru-ru/develop/net/tutorials/get-started/">Развертывание веб-приложения ASP.NET на веб-сайтах Azure</a><br/>- <a href="http://www.windowsazure.com/ru-ru/develop/net/tutorials/web-site-with-sql-database/">Развертывание безопасного приложения ASP.NET MVC в Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Проверка подлинности</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/ru-ru/manage/windows/fundamentals/identity/">Общая информация о вариантах удостоверений Azure</a><br/>- <a href="http://www.windowsazure.com/ru-ru/documentation/services/active-directory/">Служба Azure Active Directory</a><br/>- <a href="http://technet.microsoft.com/ru-ru/library/jj573650.aspx">Что такое клиент Azure AD?</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn151790.aspx">Добавление единого входа в веб-приложение с помощью Azure AD</a><br/>- <a href="http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication">Azure Authentication Tutorial (Учебник по аутентификации в Azure)</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Ретрансляция Service Bus</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/ru-ru/develop/net/how-to-guides/service-bus-relay/">Как использовать службу ретрансляции Service Bus</a><br/>- <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/ee706736.aspx">Учебник по ретрансляции сообщений Service Bus</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Монитор</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/ru-ru/manage/services/web-sites/how-to-monitor-websites/">Мониторинг веб-сайтов</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">Практическое руководство: Получение уведомлений об оповещениях и управление правилами оповещения в Azure</a><br/>- <a href="http://www.windowsazure.com/ru-ru/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics">Практическое руководство: Настройка диагностики и скачивание журналов для веб-сайта</a><br/>- <a href="http://www.windowsazure.com/ru-ru/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/">Устранение неполадок, связанных с веб-сайтами Azure, в Visual Studio</a></td>
</tr>
</table>

  [websitesoverview]:/ru-ru/documentation/services/web-sites/
  [csoverview]:/ru-ru/documentation/services/cloud-services/
  [vmoverview]:/ru-ru/documentation/services/virtual-machines/
  [chooseservice]:/ru-ru/manage/services/web-sites/choose-web-app-service
  [scenarioglobalweb]:/ru-ru/manage/services/web-sites/global-web-presence-solution-overview/
  [scenariodigitalmarketing]:/ru-ru/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [adoverview]:/ru-ru/documentation/services/active-directory/
  [adusing]:/ru-ru/manage/windows/fundamentals/identity/#ad
  [adwithvm]:/ru-ru/manage/windows/fundamentals/identity/#adinvm
  [addeployguidelines]:http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156090.aspx
  [acs2]:http://msdn.microsoft.com/library/windowsazure/hh147631.aspx
  [acsusing]:/ru-ru/manage/windows/fundamentals/identity/#ac
  [adtenant]:http://technet.microsoft.com/ru-ru/library/jj573650.aspx
  [adsso]:http://msdn.microsoft.com/library/windowsazure/dn151790.aspx
  [dirintegration]:http://technet.microsoft.com/ru-ru/library/jj573653.aspx
  [identityandaccess]:http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e
  [aspnettools]:http://go.microsoft.com/fwlink/?LinkID=282306
  [azureauthtutorial]:http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication
  [trustcenter]:/ru-ru/support/trust-center/
  [sbrelay]:http://msdn.microsoft.com/ru-ru/library/windowsazure/jj860549.aspx
  [sbrelayhowto]:/ru-ru/develop/net/how-to-guides/service-bus-relay/
  [sbrelaytutorial]:http://msdn.microsoft.com/ru-ru/library/windowsazure/ee706736.aspx
  [enterprisepizza]:http://code.msdn.microsoft.com/windowsazure/Enterprise-Pizza-e2d8f2fa
  [newrelic]:http://newrelic.com/azure
  [configurediagnostics]:/ru-ru/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics
  [troubleshootwebsites]:/ru-ru/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [BusinessApplicationsAzureAD]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AzureAD.png
  [BusinessApplicationsADUsers]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_Users.png
  [BusinessApplicationsVSIntranetApp]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IntranetApp.png
  [BusinessApplicationsVSProperties]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_Properties.png
  [BusinessApplicationsVSIdentityAndAccess]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IdentityAndAccess.png
  [BusinessApplicationsADIntegratedApps]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_IntegratedApps.png
  [BusinessApplicationsADAppAdded]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_AppAdded.png
  [BusinessApplicationsVSEnableAuth]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_EnableAuth.png
  [BusinessApplicationsDeploy]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Deploy.png
  [BusinessApplicationsServiceBusRelay]: ./media/web-sites-business-application-solution-overview/BusinessApplications_ServiceBusRelay.png
  [BusinessApplicationsDiagnostics]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diagnostics.png
  [BusinessApplicationsDiagRetention]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diag_Retention.png
  
  
  
  
  
  
  
  
  
  
  
  
  
  




  




<!--HONumber=35.1-->
