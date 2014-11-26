<properties linkid="manage-services-how-to-use-appdynamics" urlDisplayName="Monitor with AppDynamics" pageTitle="How to use AppDynamics with Azure" metaKeywords="" description="Learn how to use AppDynamics for Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How To Use AppDynamics for Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi" />

# Как использовать AppDynamics для Azure

В этом разделе описывается, как приступить к работе с AppDynamics для Azure.

## Оглавление

-   [Что такое AppDynamics?](#what)
-   [Предварительные требования](#prereq)
-   [Регистрация для учетной записи AppDynamics](#register)
-   [Загрузка агента .NET из AppDynamics](#download)
-   [Добавление агента .NET в роли Azure и изменение запуска](#addagent)
-   [Публикация приложения на базе AppDynamics в Azure](#publish)
-   [Отслеживание приложения](#monitor)

## <span id="what"></span></a>Что такое AppDynamics?

AppDynamics — решение для отслеживания производительности приложения, которое помогает в следующем:

-   Определение проблем, таких как медленные и приостановленные пользовательские запросы и ошибки, в рабочей среде.

-   Диагностика и выявление причины таких проблем.

В AppDynamics существует два компонента:

-   Агент сервера приложений. Агент сервера приложений .NET ведет сбор данных с серверов. В каждом экземпляре роли, которые требуется отслеживать, выполняется отдельный агент. Агент можно загрузить на портале AppDynamics.

-   Контроллер AppDynamics. Агент отправляет сведения в размещенную службу контроллера AppDynamics в Azure. С помощью консоли на основе веб-браузера выполняется вход в контроллер для отслеживания, анализа и устранения неполадок в приложении.

    ![Диаграмма AppDynamics](./media/cloud-services-how-to-appdynamics/addiagram.png)

## <span id="prereq"></span></a>Предварительные требования

-   Visual Studio 2010 или более поздняя версия
-   Решение Visual Studio для отслеживания
-   Пакет SDK Azure
-   Учетная запись Azure

## <span id="register"></span></a>Регистрация для учетной записи AppDynamics

Чтобы зарегистрироваться для AppDynamics для учетной записи Azure:

1.  Щелкните **Пробная версия** или **Регистрация** для AppDynamics в магазине Azure по адресу [https://datamarket.azure.com/browse/Applications](https://datamarket.azure.com/browse/Applications).

    Если выбран вариант **Регистрация**, вы получите бесплатную версию AppDynamics Pro для Windows Azure с полным набором функций, которая по прошествии 30 дней будет преобразована в бесплатную версию AppDynamics Lite для Azure с ограниченными возможностями. Для этого варианта нет необходимости указывать кредитную карту. Обновление до AppDynamics Pro для Azure доступно в любое время.

    При выборе варианта **Пробная версия** вы получите бесплатную версию AppDynamics Pro для Azure с полным набором функциональных возможностей. Для этого варианта требуется указать кредитную карту. По истечении 30 дней, если подписка не отменена, с кредитной карты будет списана плата за продолжение использования AppDynamics Pro для Azure.

    Требуется одна лицензия агента для каждого экземпляра роли, которые необходимо отслеживать. Например, для сайта, где выполняется 2 экземпляра веб-роли и 2 экземпляра рабочей роли, требуется 4 лицензии агента.

2.  На странице регистрации укажите информацию о пользователе, пароль, адрес электронной почты, название организации и имя приложения, за которым ведется наблюдение (в том виде, в котором оно опубликовано в Azure).

3.  Щелкните **Зарегистрироваться сейчас**.

    Пользователь получит учетные данные AppDynamics и URL-адрес контроллера AppDynamics (узел и порт), назначенные вашей учетной записи пользователя, в сообщении электронной почты, отправленном по адресу, который был указан на странице регистрации. Сохраните эти сведения.

    Если уже имеются учетные данные AppDynamics из другого продукта, можно выполнить регистрацию с их использованием.

    Пользователю также предоставляется домашняя страница учетной записи AppDynamics.

    Откроется домашняя страница вашей учетной записи AppDynamics.

    Домашняя страница учетной записи AppDynamics содержит следующее:

    -   URL-адрес контроллера, с которого выполняется вход в учетную запись в размещенной службе контроллера AppDynamics.

    -   Учетные данные AppDynamics: имя учетной записи и ключ доступа.

    -   Ссылка на сайт загрузки AppDynamics, по которой можно загрузить агент .NET AppDynamics.

    -   Число дней, оставшееся до истечения срока действия пробной версии Pro.

    -   Ссылки на видео и документацию по началу работы с AppDynamics.

    Вы можете в любое время перейти на домашнюю страницу своей учетной записи AppDynamics. Для этого достаточно ввести ее URL-адрес в веб-браузере, а затем выполнить вход с использованием учетных данных AppDynamics.

## <span id="download"></span></a>Загрузка агента .NET из AppDynamics

1.  Перейдите на сайт загрузки AppDynamics. URL-адрес содержится в приветственном сообщении на главной странице учетной записи AppDynamics.

2.  Войдите в систему с использованием имени учетной записи и ключа доступа AppDynamics.

3.  Загрузите файл с именем AppDynamicsdotNetAgentSetup64.msi. Не запускайте этот файл.

## <span id="addagent"></span></a>Добавление агента .NET в роли Azure и изменение запуска

Этот шаг предоставляет ролям в решении Visual Studio возможность вести отслеживание в AppDynamics. Традиционная процедура установки с помощью мастера в стиле Windows для использования AppDynamics для Azure отсутствует.

1.  Создайте новый проект Azure в Visual Studio или откройте существующий проект Azure.

2.  Если создан новый проект, добавьте в решение проекты веб-роли и/или рабочей роли.

3.  Для каждого проекта веб-роли и рабочей роли, которые требуется отслеживать, добавьте загруженный msi-файл агента .NET.

    Обратите внимание, что хотя все *проекты роли* имеют отдельный прикрепленный msi-файл агента .NET, каждый *экземпляр роли* в проекте нуждается в отдельной лицензии на агент .NET.

4.  В каждый проект веб-роли и рабочей роли, которые требуется отслеживать, добавьте текстовый файл с именем startup.cmd и вставьте в него следующие строки:

        if defined COR_PROFILER GOTO END 
        SETLOCAL EnableExtensions 
        REM Run the agent installer 
        AppDynamicsdotNetAgentSetup64.msi AD_Agent_Environment=Azure AD_Agent_ControllerHost=%1 AD_Agent_ControllerPort=%2 AD_Agent_AccountName=%3 AD_Agent_AccessKey=%4 AD_Agent_ControllerApplication=%5 /quiet /log d:\adInstall.log  
        SHUTDOWN /r /c "Rebooting the instance after the installation of AppDynamics Monitoring Agent" /t 0 
        GOTO END   
        :END

5.  Для каждой рабочей и веб-роли, которые требуется отслеживать, установите для MSI-файла агента AppDynamics свойство **Копировать в выходной каталог**, а для файла startup.cmd свойство **Копировать всегда**.

    ![Копировать всегда](./media/cloud-services-how-to-appdynamics/adcopyalways.png)

6.  В файле ServiceDefinition.csdef для проекта Azure добавьте элемент задачи запуска, который вызывает startup.cmd с параметрами для каждого элемента WorkerRole и WebRole.

    Добавьте следующие строки.

        <Startup>
        <Task commandLine="startup.cmd [your_controller_host] [your_controller_port] [your_account_name] [your_access_key] [your_application_name]" executionContext="elevated" taskType="simple"/>
        </Startup>

    где:

    -   *your controller host* и *your controller port* указывают хост и порт контроллера, присвоенные вашей учетной записи, а *your account name* и *your access key* указывают учетные данные, присвоенные автоматически в AppDynamics. Эти сведения доступны в почтовом сообщении, отправленном при регистрации в AppDynamics, а также на домашней странице AppDynamic. См. раздел [Регистрация учетной записи AppDynamics](#register).

    -   *your application name* указывает имя, выбранное для приложения. Это имя будет определять приложение в интерфейсе контроллера AppDynamics.

    Файл ServiceDefinition.csdef будет иметь примерно следующий вид:

    ![Определение службы](./media/cloud-services-how-to-appdynamics/adscreen.png)

## <a name="publish"></a>Публикация приложения на базе AppDynamics в Azure

Для каждого проекта роли на базе AppDynamics:

1.  В Visual Studio выберите проект соответствующей роли.

2.  Выберите команду "Опубликовать в Azure".

## <a name="monitor"></a>Отслеживание приложения

1.  Войдите в контроллер AppDynamics с использованием URL-адреса, указанного в приветственном сообщении, а также на домашней странице учетной записи AppDynamics.

2.  Отправьте ряд запросов в приложение, чтобы создать трафик для отслеживания, и подождите несколько минут.

3.  В контроллере AppDynamics выберите приложение.

4.  Отслеживайте работу приложения.

## <a name="learn"></a>Подробнее

Ссылки на документацию и видео доступны на домашней странице учетной записи AppDynamics.

Последние обновления этого документа можно найти в вики-версии по адресу[http://docs.appdynamics.com/display/ADAZ/How+To+Use+AppDynamics+for+Windows+Azure](http://docs.appdynamics.com/display/ADAZ/How+To+Use+AppDynamics+for+Windows+Azure).


