<properties urlDisplayName="Create and Manage Hybrid Connections - BizTalk Services" pageTitle="Создание гибридных подключений и управление ими | Azure" metaKeywords="BizTalk Services, BizTalk, web sites, website, hybrid connections, Azure" description="Learn how to create a hybrid connection, manage the connection, and install the Hybrid Connection Manager." metaCanonical="" services="integration-services" documentationCenter="" title="Create and Manage Hybrid Connections" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />


#Создание гибридных подключений и управление ими

В этом разделе описаны действия по созданию гибридных подключений Azure и управлению ими. 

Действия для подключения к локальному ресурсу:

1. [Создание гибридного подключения](#CreateHybridConnection) путем указания имени компьютера или IP-адреса локального ресурса в вашей частной сети.

2.	[Связывание своего веб-сайта Azure или мобильной службы Azure](#LinkWebSite) с гибридным подключением.

3. [Установка диспетчера гибридных подключений](#InstallHCM) на локальном ресурсе и соединение с конкретным гибридным подключением. Портал Azure предлагает простой интерфейс для установки и подключения.

4. [Управление гибридными подключениями](#ManageHybridConnection) и их ключами.


##<a name="CreateHybridConnection"></a>Создание гибридного подключения

Гибридное подключение можно создать на портале управления Azure с помощью веб-сайтов **или** служб BizTalk. 

**Сведения о создании гибридных подключений с помощью веб-сайтов** можно узнать в статье [Подключение веб-сайта Azure к локальным ресурсам](http://go.microsoft.com/fwlink/p/?LinkId=397538).

**Вот как создать гибридные подключения в службах BizTalk**:

1. Войдите на [портал управления Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. В левой панели навигации выберите **Службы BizTalk**, а затем выберите нужную службу BizTalk. 
<br/>Если служба BizTalk отсутствует, можно [создать ее](http://go.microsoft.com/fwlink/p/?LinkID=329870).
3. Выберите вкладку "Гибридные подключения":
<br/>
![Hybrid Connections Tab][HybridConnectionTab]

4. Выберите пункт **Создать гибридное подключение** или нажмите кнопку **ДОБАВИТЬ** на панели задач. Заполните следующие поля:

	<table border="1">
    <tr>
       <td><strong>Имя</strong></td>
        <td>Имя гибридного подключения должно быть уникальным и не должно совпадать с именем службы BizTalk. Можно ввести любое имя, но рекомендуется использовать специальные имена, соответствующие назначению подключения. Примеры приведены ниже.<br/><br/>
		Payroll<em>SQLServer</em><br/>
		SupplyList<em>SharepointServer</em><br/>
		Customers<em>OracleServer</em>
        </td>
    </tr>
    <tr>
        <td><strong>Имя компьютера</strong></td>
        <td>Введите полное имя компьютера, только имя компьютера или IPv4-адрес локального ресурса. Примеры приведены ниже.
        <br/><br/>
<em>mySQLServer</em>
<br/>
<em>mySQLServer</em>.<em>Domain</em>.corp.<em>yourCompany</em>.com
<br/>
<em>myHTTPSharePointServer</em>
<br/>
<em>myHTTPSharePointServer</em>.<em>yourCompany</em>.com
<br/>
10.100.10.10
       </td>
    </tr>
	<tr>
        <td><strong>Порт</strong></td>
        <td>Укажите номер порта локального ресурса. Например, при использовании веб-сайта укажите порт 80 или 443. Если используется SQL Server, укажите порт 1433.</td>
	</tr>
	</table>


5. Установите флажок. 

####Дополнительно

- Можно создать несколько гибридных подключений. Сведения о допустимом количестве подключений см. в статье [Службы BizTalk: диаграмма выпусков](http://go.microsoft.com/fwlink/p/?LinkID=302279). 
- Каждое гибридное подключение создается с парой строк подключения: ключи приложений, которые ОТПРАВЛЯЮТ, и локальные ключи, которые ПРОСЛУШИВАЮТ. В каждой паре есть первичный и вторичный ключи. 


##<a name="LinkWebSite"></a>Связывание веб-сайта Azure или мобильной службы Azure

Чтобы связать веб-сайт Azure с существующим гибридным подключением, в колонке "Гибридные подключения" выберите элемент **Использовать существующее гибридное подключение**. Ознакомьтесь со статьей [Подключение веб-сайта Azure к локальным ресурсам](http://go.microsoft.com/fwlink/p/?LinkId=397538).

Чтобы связать мобильную службу Azure с существующим гибридным подключением, при изменении или создании мобильной службы выберите пункт **Добавить гибридное подключение**. Ознакомьтесь со статьей [Мобильные службы Azure и гибридные подключения](http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started).


##<a name="InstallHCM"></a>Локальная установка диспетчера гибридных подключений

После создания гибридного подключения необходимо установить диспетчер гибридных подключений на локальном ресурсе. Его можно загрузить с веб-сайта Azure или из службы BizTalk. Действия, выполняемые в службе BizTalk: 

1. Войдите на [портал управления Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. В левой панели навигации выберите **Службы BizTalk**, а затем выберите нужную службу BizTalk. 
3. Выберите вкладку **Гибридные подключения**:
<br/>
![Hybrid Connections Tab][HybridConnectionTab]
4. На панели задач нажмите кнопку **Локальная настройка**:
<br/>
![On-Premises Setup][HCOnPremSetup]
5. Нажмите **Установить и настроить**, чтобы запустить или скачать диспетчер гибридных подключений в локальной системе. 
6. Установите флажок для запуска процесса установки. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString â€œ*Локальная строка подключения, которую вы скопировали*â€ 
--> 

#### Дополнительно
- Гибридные подключения поддерживают локальные ресурсы, установленные в следующих операционных системах:

	- Windows Server 2008 R2
	- Windows Server 2012
	- Windows Server 2012 R2


- После установки диспетчера гибридных подключений произойдут следующие события: 

	- Автоматическая настройка гибридного подключения на платформе Azure для использования основной строки подключения к приложению. 
	- Автоматическая настройка локального ресурса для использования основной локальной строки подключения.

- Для прохождения авторизации диспетчер гибридных подключений должен использовать допустимую локальную строку подключения. Для прохождения авторизации веб-сайт Azure или мобильная служба должны использовать допустимую строку подключения к приложению.


##<a name="ManageHybridConnection"></a>Управление гибридными подключениями
Для управления гибридными подключениями можно выбрать следующие варианты:

- Использование страницы службы BizTalk на портале Azure. 
- Использование [интерфейсов REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx).

####Копирование или повторное создание строк гибридных подключений

1. Войдите на [портал управления Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. В левой панели навигации выберите **Службы BizTalk**, а затем выберите нужную службу BizTalk. 
3. Выберите вкладку **Гибридные подключения**:
<br/>
![Hybrid Connections Tab][HybridConnectionTab]
4. Выберите гибридное подключение. На панели задач нажмите кнопку **Управление подключениями**:
<br/>
![Manage Options][HCManageConnection]
<br/>
В разделе **Управление подключениями** содержится перечень строк подключения к приложению и локальных строк подключения. Вы можете скопировать сроки подключений или повторно создать ключ доступа, используемый в строке подключения. 
<br/>
<br/>
**При повторном создании** общий ключ доступа, используемый в строке подключения, будет изменен. Сделайте следующее:
- На портале управления Azure в приложении Azure выберите пункт **Синхронизировать ключи**.
- Повторно запустите **программу локальной установки**. Локальный ресурс автоматически настроится для использования обновленной основной строки подключения.


####Применение групповой политики для контроля локальных ресурсов, используемых гибридным подключением

1. Загрузите административные шаблоны диспетчера гибридных подключений.
2. Извлеките файлы.
3. На компьютере, где осуществляется изменение групповой политики, выполните следующие действия: 
- Скопируйте файлы ADMX в папку *%WINROOT%\PolicyDefinitions*.
- Скопируйте файлы ADML в папку *%WINROOT%\PolicyDefinitions\ru-ru*.

После копирования файлов можно изменить политику с помощью редактора групповой политики.




## Далее

- [Подключение веб-сайта Azure к локальному ресурсу](http://go.microsoft.com/fwlink/p/?LinkId=397538)
- [Пошаговое создание гибридного подключения: подключение к локальному SQL Server с веб-сайта Azure](http://go.microsoft.com/fwlink/?LinkID=397979)
- [Мобильные службы Azure и гибридные подключения](http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started)
- [Обзор гибридных подключений](http://azure.microsoft.com/ru-ru/documentation/articles/integration-hybrid-connection-overview)


## См. также

- [API-интерфейс REST для управления службами BizTalk в Windows Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
- [Службы BizTalk: Диаграмма выпусков](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Создание службы BizTalk с помощью портала управления Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Службы BizTalk: вкладки "Панель мониторинга", "Монитор" и "Масштаб"](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>


[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png
