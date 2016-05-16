<properties
	pageTitle="Оптимизация среды с помощью решения оценки SQL в Log Analytics | Microsoft Azure"
	description="Решения оценки SQL можно использовать для оценки риска и работоспособности серверной среды с постоянной периодичностью."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Оптимизация среды с помощью решения оценки SQL в Log Analytics


Решения оценки SQL можно использовать для оценки риска и работоспособности серверной среды с постоянной периодичностью. Эта статья поможет вам установить решение таким образом, чтобы в случае проблем вы могли предпринять корректирующие меры.

Это решение предоставляет приоритетный список рекомендаций, относящихся к развернутой серверной инфраструктуре. Рекомендации сгруппированы в шесть приоритетных областей, помогающих быстро оценить риски и принять корректирующие меры.

В основу предлагаемых рекомендаций положены знания и опыт, приобретенные специалистами Майкрософт в результате многочисленных посещений клиентов. Каждая рекомендация является определенным руководством, поясняющим важность проблемы и приводящим действия по реализации предложенных изменений.

Можно выбрать приоритетные области, наиболее важные для организации, и отслеживать выполнение задач по формированию работоспособной и свободной от рисков среды.

После добавления решения и завершения оценки сводная информация по приоритетным областям отображается на панели мониторинга **Оценка SQL** для инфраструктуры среды. В следующих разделах описано, как использовать информацию на панели мониторинга **Оценка SQL**, где можно увидеть и выполнить действия, рекомендованные для вашей инфраструктуры сервера SQL.

![image of SQL Assessment tile](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![image of SQL Assessment dashboard](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## Установка и настройка решения
Оценка SQL поддерживает выпуски Standard, Developer и Enterprise SQL Server — все текущие поддерживаемые версии.

Для установки и настройки решений используйте указанные ниже данные.

- Для решения оценки SQL на каждом компьютере с агентом OMS должна быть установлена платформа .NET Framework 4.
- При использовании агента Operations Manager с решением оценки SQL необходимо использовать учетную запись запуска от имени Operations Manager. Дополнительные сведения см. статье [Учетные записи запуска от имени Operations Manager для OMS](#operations-manager-run-as-accounts-for-oms).

    >[AZURE.NOTE] Агент MMA не поддерживает учетные записи запуска от имени Operations Manager.

- Решение оценки SQL необходимо добавить в рабочую область OMS, как описано в статье [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Добавление решений Log Analytics из коллекции решений). Дополнительная настройка не требуется.

>[AZURE.NOTE] После добавления решения на серверы с агентами добавляется файл AdvisorAssessment.exe. Данные конфигурации считываются и отправляются на обработку в службу OMS в облаке. К полученным данным применяется логика и облачная служба записывает данные.

## Сведения о сборе данных по оценке SQL

В следующей таблице показаны методы сбора данных для агентов, необходимость Operations Manager (SCOM) и периодичность сбора данных агентом.

| платформа | Direct Agent | Агент SCOM | Хранилище Azure | Нужен ли SCOM? | Отправка данных агента SCOM через группу управления | частота сбора |
|---|---|---|---|---|---|---|
|Windows|![Да](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![Да](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![Нет](./media/log-analytics-sql-assessment/oms-bullet-red.png)|	![Нет](./media/log-analytics-sql-assessment/oms-bullet-red.png)|![Да](./media/log-analytics-sql-assessment/oms-bullet-green.png)|	7 дней|

## Учетные записи запуска от имени в Operations Manager для службы OMS

Log Analytics в OMS использует агент Operations Manager и группу управления для сбора и отправки данных в службу OMS. С целью предоставления ценных услуг служба OMS строится на пакетах управления для рабочих нагрузок. Каждой рабочей нагрузке требуются права, относящиеся к конкретным рабочим нагрузкам, для запуска пакетов управления в другом контексте безопасности, например в учетной записи домена. Необходимо предоставить учетные данные, настроив учетную запись запуска от имени для Operations Manager.

Для настройки учетной записи запуска от имени Operations Manager для оценки SQL используйте указанные ниже сведения.

### Настройка учетной записи запуска от имени для оценки SQL

 Если пакет управления SQL Server уже используется, следует использовать эту учетную запись запуска от имени.

#### Настройка учетной записи SQL запуска от имени в консоли управления

1. В Operations Manager откройте консоль управления и щелкните **Администрирование**.

2. В разделе **Настройка запуска от имени** щелкните **Профили** и откройте **Профиль запуска от имени для оценки SQL в OMS**.

3. На странице **Учетные записи запуска от имени** нажмите кнопку **Добавить**.

4. Выберите учетную запись Windows запуска от имени, которая содержит учетные данные, необходимые для SQL Server, или нажмите кнопку **Создать** для ее создания.
	>[AZURE.NOTE] Тип учетной записи «Запуск от имени» должен быть указан как Windows. Учетная запись запуска от имени также должна входить в группу локальных администраторов на всех серверах Windows Server, на которых размещены экземпляры SQL Server.

5. Щелкните **Сохранить**.

6. Измените, а затем выполните следующий пример T-SQL на каждом экземпляре SQL Server, чтобы предоставить минимальные разрешения, необходимые учетной записи запуска от имени для выполнения оценки SQL. Но это не требуется делать в том случае, если учетная запись запуска от имени уже является частью серверной роли sysadmin в экземплярах SQL Server.

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### To configure the SQL Run As account using Windows PowerShell

Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## Understanding how recommendations are prioritized

Every recommendation made is given a weighting value that identifies the relative importance of the recommendation. Only the ten most important recommendations are shown.

### How weights are calculated

Weightings are aggregate values based on three key factors:

- The *probability* that an issue identified will cause problems. A higher probability equates to a larger overall score for the recommendation.

- The *impact* of the issue on your organization if it does cause a problem. A higher impact equates to a larger overall score for the recommendation.

- The *effort* required to implement the recommendation. A higher effort equates to a smaller overall score for the recommendation.

The weighting for each recommendation is expressed as a percentage of the total score available for each focus area. For example, if a recommendation in the Security and Compliance focus area has a score of 5%, implementing that recommendation will increase your overall Security and Compliance score by 5%.

### Focus areas

**Security and Compliance** - This focus area shows recommendations for potential security threats and breaches, corporate policies, and technical, legal and regulatory compliance requirements.

**Availability and Business Continuity** - This focus area shows recommendations for service availability, resiliency of your infrastructure, and business protection.

**Performance and Scalability** - This focus area shows recommendations to help your organization's IT infrastructure grow, ensure that your IT environment meets current performance requirements, and is able to respond to changing infrastructure needs.

**Upgrade, Migration and Deployment** - This focus area shows recommendations to help you upgrade, migrate, and deploy SQL Server to your existing infrastructure.

**Operations and Monitoring** - This focus area shows recommendations to help streamline your IT operations, implement preventative maintenance, and maximize performance.

**Change and Configuration Management** - This focus area shows recommendations to help protect day-to-day operations, ensure that changes don't negatively affect your infrastructure, establish change control procedures, and to track and audit system configurations.

### Should you aim to score 100% in every focus area?

Not necessarily. The recommendations are based on the knowledge and experiences gained by Microsoft engineers across thousands of customer visits. However, no two server infrastructures are the same, and specific recommendations may be more or less relevant to you. For example, some security recommendations might be less relevant if your virtual machines are not exposed to the Internet. Some availability recommendations may be less relevant for services that provide low priority ad hoc data collection and reporting. Issues that are important to a mature business may be less important to a start-up. You may want to identify which focus areas are your priorities and then look at how your scores change over time.

Every recommendation includes guidance about why it is important. You should use this guidance to evaluate whether implementing the recommendation is appropriate for you, given the nature of your IT services and the business needs of your organization.

## Use assessment focus area recommendations

Before you can use an assessment solution in OMS, you must have the solution installed. To read more about installing solutions, see [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md). After it is installed, you can view the summary of recommendations by using the SQL Assessment tile on the Overview page in OMS.

View the summarized compliance assessments for your infrastructure and then drill-into recommendations.

### To view recommendations for a focus area and take corrective action

1. On the **Overview** page, click the **SQL Assessment** tile.
2. On the **SQL Assessment** page, review the summary information in one of the focus area blades and then click one to view recommendations for that focus area.
3. On any of the focus area pages, you can view the prioritized recommendations made for your environment. Click a recommendation under **Affected Objects** to view details about why the recommendation is made.  
    ![image of SQL Assessment recommendations](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. You can take corrective actions suggested in **Suggested Actions**. When the item has been addressed, later assessments will record that recommended actions were taken and your compliance score will increase. Corrected items appear as **Passed Objects**.

## Ignore recommendations

If you have recommendations that you want to ignore, you can create a text file that OMS will use to prevent recommendations from appearing in your assessment results.

### To identify recommendations that you will ignore

1.	Sign in to your workspace and open Log Search. Use the following query to list recommendations that have failed for computers in your environment.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Here's a screen shot showing the Log Search query:
    ![failed recommendations](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)

2.	Выберите рекомендации, которые нужно проигнорировать. Эти значения будут использоваться для параметра RecommendationId в следующей процедуре.


### Создание и использование текстового файла IgnoreRecommendations.txt

1.	Создайте файл с именем IgnoreRecommendations.txt.
2.	Вставьте или введите значение RecommendationId для каждой рекомендации, которую служба OMS должна игнорировать, в отдельной строке, а затем сохраните и закройте файл.
3.	Поместите файл в следующую папку на каждом компьютере, где OMS должен проигнорировать рекомендации.
    - На компьютерах с Microsoft Monitoring Agent (подключенных напрямую или через Operations Manager) — *SystemDrive*:\\Program Files\\Microsoft Monitoring Agent\\Agent
    - На сервере управления Operations Manager — *SystemDrive*: \\Program Files\\Microsoft System Center 2012 R2\\Operations Manager\\Server

### Контроль игнорирования рекомендаций

1.	После выполнения следующей плановой оценки (по умолчанию выполняется раз в семь дней) указанные рекомендации помечаются как игнорируемые и больше не отображаются на панели мониторинга оценки.
2.	Для получения списка всех игнорируемых рекомендаций можно использовать следующие запросы поиска по журналам.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
3.	Если вы решите позже просмотреть игнорируемые рекомендации, удалите все файлы IgnoreRecommendations.txt или RecommendationIDs можно удалить из них.

## Часто задаваемые вопросы по решениям оценки SQL

*Как часто выполняется оценка?*
- Оценка выполняется каждые 7 дней.

*Есть ли способ настроить частоту оценки?*
- На данный момент нет.

*Если после добавления решения оценки SQL будет обнаружен другой сервер, оценивается ли он?*
- Да, после обнаружения он оценивается каждые 7 дней.

*Если сервер выведен из эксплуатации, он будет удален из оценки?*
- Если сервер не отправляет данные в течение 3 недель, то он удаляется.

*Как называется процесс, который собирает данные?*
- AdvisorAssessment.exe

*Сколько времени требуется для сбора данных?*
- Время сбора данных на сервере занимает примерно 1 час. На серверах, которые имеют большое количество экземпляров или баз данных SQL, процесс сбора может занять больше времени.

*Какие типы данных собираются?*
- Собираются следующие типы данных:
    - WMI
    - Реестр
    - Счетчики производительности
    - Динамические административные представления (DMV) SQL

*Можно ли настроить время сбора данных?*
- На данный момент нет.

*Почему необходимо настроить учетную запись «Запуск от имени»?*
- Для SQL Server выполняется небольшое количество SQL-запросов. Для того чтобы их можно было выполнить, необходимо использовать учетную запись выполнения от имени с разрешениями VIEW SERVER STATE для SQL. Кроме того, чтобы выполнять запросы к WMI, потребуются учетные данные локального администратора.

*Почему отображаются только первые 10 рекомендаций?*
- Вместо отображения полного списка задач мы рекомендуем сконцентрироваться на приоритетных рекомендациях. После этого станут доступны дополнительные рекомендации. Если вы хотите просмотреть весь список, используйте функцию поиска по журналам OMS.

*Можно ли игнорировать рекомендации?*
- Да, см. приведенный выше раздел [Игнорирование рекомендаций](#ignore-recommendations).



## Дальнейшие действия

- Выполните [поиск по журналам](log-analytics-log-searches.md) для просмотра подробных данных и рекомендаций по оценке SQL.

<!---HONumber=AcomDC_0504_2016-->