<properties
	pageTitle="Создание приложений .NET HDInsight с неинтерактивной проверкой подлинности | Microsoft Azure"
	description="Узнайте, как создавать приложения .NET HDInsight с неинтерактивной проверкой подлинности."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/23/2016"
	ms.author="jgao"/>

# Создание приложений .NET HDInsight с неинтерактивной проверкой подлинности

Приложение .NET Azure HDInsight можно выполнить с помощью его собственного удостоверения (неинтерактивный режим) или удостоверения пользователя, вошедшего в приложение (интерактивный режим). Образец интерактивного приложения см. в статье [Отправка заданий Hive, Pig или Sqoop с использованием пакета SDK для HDInsight .NET](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). В этой статье описано, как создать приложение .NET HDInsight с неинтерактивной проверкой подлинности для подключения к Azure HDInsight и отправки задания Hive.

Из приложения .NET вам потребуется следующее:

- идентификатор клиента для подписки Azure;
- идентификатор клиента для приложения Azure Directory;
- секретный ключ приложения Azure Directory.  

Основная процедура включает в себя следующие шаги:

2. Создание приложения Azure Active Directory.
2. Назначение ролей для приложения AD.
3. Разработка клиентского приложения.


##Предварительные требования

- Кластер HDInsight. Его можно создать, следуя инструкциям в [руководстве по началу работы](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). 




## Создание приложения Azure Active Directory 
При создании приложения Active Directory фактически создается приложение и субъект-служба. Приложения можно выполнять с помощью его удостоверения.

Сейчас для создания приложения Active Directory необходимо использовать классический портал Azure. Эта возможность будет добавлена на портал Azure в одном из следующих выпусков. Это также можно выполнить с помощью Azure PowerShell или Azure CLI. Дополнительные сведения об использовании PowerShell или CLI с субъектом-службой см. в статье [Проверка подлинности субъекта-службы в Azure Resource Manager](../resource-group-authenticate-service-principal.md).

**Порядок создания приложения Azure Active Directory**

1.	Войдите на [классический портал Azure](https://manage.windowsazure.com/).
2.	Выберите **Active Directory** на левой панели.

    ![Active Directory на классическом портале Azure](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\active-directory.png)
    
3.	Выберите каталог, который хотите использовать для создания нового приложения. Он должен существовать.
4.	Щелкните **Приложения** в верхней части списка существующих приложений.
5.	Нажмите расположенную внизу кнопку **Добавить**, чтобы добавить приложение.
6.	Введите **Имя**, выберите **Веб-приложение и/или веб-API** и нажмите кнопку **Далее**.

    ![новое приложение Azure Active Directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application.png)

7.	Укажите **URL-адрес входа** и **URI кода приложения**. В поле **URL-АДРЕС ВХОДА** введите URI веб-сайта, который описывает ваше приложение. Существование этого сайта не проверяется. В поле "URI кода приложения" введите URI, который идентифицирует ваше приложение. Затем нажмите кнопку **Завершить**. Создание приложения занимает несколько секунд. После выполнения операции на портале отображается страница "Сводка". Не закрывайте портал.

    ![свойства нового приложения Azure Active Directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application-properties.png)

**Получение идентификатора клиента приложения и секретного ключа**

1.	В верхнем меню на странице созданного приложения AD щелкните элемент **Настройка**.
2.	Скопируйте значение **Идентификатор клиента**. Оно понадобится в приложении .NET.
3.	В разделе **Ключи** щелкните раскрывающийся список **Выбрать длительность** и выберите **1 год** или **2 года**. Значение ключа отображается только после сохранения конфигурации.
4.	В нижней части страницы щелкните **Сохранить**. При отображении секретного ключа скопируйте его значение. Оно понадобится в приложении .NET.

##Назначение приложения AD роли

Необходимо назначить приложение [роли](../active-directory/role-based-access-built-in-roles.md), чтобы предоставить ему разрешения для выполнения действий. Вы можете задать область действия на уровне подписки, группы ресурсов или ресурса. Разрешения наследуются на более низких уровнях области действия (например, добавление приложения в роль читателя для группы ресурсов означает, что оно может считывать группу ресурсов и все содержащиеся в ней ресурсы). В этом учебнике описано, как задать область действия на уровне группы ресурсов. Так как классический портал Azure не поддерживает группы ресурсов, эту процедуру следует выполнять на портале Azure.

**Добавление роли владельца для приложения AD**

1.	Войдите на [портал Azure](https://portal.azure.com).
2.	Щелкните **Группа ресурсов** на левой панели.
3.	Выберите группу ресурсов с кластером HDInsight, где позднее будет выполняться ваш запрос Hive. Если имеется слишком много групп ресурсов, можно использовать фильтр.
4.	В колонке кластера щелкните **Доступ**.

    ![значок облака и молнии = быстрый запуск](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)
5.	В колонке **Пользователи** щелкните **Добавить**.
6.	Следуйте инструкциям, чтобы добавить роль **Владелец** в приложение AD, созданное в предыдущей процедуре. После успешного выполнения этой процедуры приложение должно появиться в колонке "Пользователи" с ролью "Владелец".


##Разработка клиентского приложения HDInsight

Создайте консольное приложение C# .NET, следуя инструкциям в статье [Отправка заданий Hadoop в HDInsight](hdinsight-submit-hadoop-jobs-programmatically/#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Затем замените метод GetTokenCloudCredentials на следующий код:

    public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
    {
        var authFactory = new AuthenticationFactory();

        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };

        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

        var accessToken =
            authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never)
                .AccessToken;

        return new TokenCloudCredentials(accessToken);
    }

Получите идентификатор клиента через PowerShell:

    Get-AzureRmSubscription

Или с помощью Azure CLI.

    azure account show --json

      
## См. также

- [Отправка заданий Hadoop в HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Создание приложения Active Directory и субъекта-службы с помощью портала](../resource-group-create-service-principal-portal.md)
- [Проверка подлинности субъекта-службы в Azure Resource Manager](../resource-group-authenticate-service-principal.md)
- [Управление доступом на основе ролей в Azure](../active-directory/role-based-access-control-configure.md)

<!---HONumber=AcomDC_0330_2016-->