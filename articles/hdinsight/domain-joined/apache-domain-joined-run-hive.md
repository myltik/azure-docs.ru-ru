---
title: Настройка политик Hive в присоединенном к домену кластере Azure HDInsight
description: Дополнительные сведения
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3fade1e5-c2e1-4ad5-b371-f95caea23f6d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: omidm
ms.openlocfilehash: bd99e5fda80663b37c60d972742b16c27b92cf55
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31592681"
---
# <a name="configure-hive-policies-in-domain-joined-hdinsight"></a>Настройка политик Hive в присоединенном к домену кластере HDInsight
Узнайте, как настроить политики Apache Ranger для Hive. В этой статье вы создадите две политики Ranger, чтобы ограничить доступ к таблице hivesampletable. Таблица hivesampletable поставляется с кластерами HDInsight. После настройки политик подключитесь к таблицам Hive в HDInsight с помощью Excel и драйвера ODBC.

## <a name="prerequisites"></a>предварительным требованиям
* Присоединенный к домену кластер HDInsight. См. статью [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md) (Настройка присоединенных к домену кластеров HDInsight).
* Рабочая станция с Office 2016, Office 2013 профессиональный плюс, Office 365 профессиональный плюс, Excel 2013 автономный или Office 2010 профессиональный плюс.

## <a name="connect-to-apache-ranger-admin-ui"></a>Подключение к пользовательскому интерфейсу администратора Apache Ranger
**Подключение к пользовательскому интерфейсу администратора Ranger**

1. В браузере подключитесь к пользовательскому интерфейсу администратора Ranger по URL-адресу https://&lt;имя_кластера >.azurehdinsight.net/Ranger/.

   > [!NOTE]
   > Ranger использует учетные данные, отличающиеся от учетных данных кластера Hadoop. Чтобы браузеры не использовали кэшированные учетные данные Hadoop, подключитесь к пользовательскому интерфейсу администратора Ranger в новом окне браузера в режиме InPrivate.
   >
   >
2. Войдите, используя имя пользователя и пароль домена администратора кластера.

    ![Домашняя страница Ranger для подключенного к домену кластера HDInsight](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    В настоящее время Ranger работает только с Yarn и Hive.

## <a name="create-domain-users"></a>Создание пользователей домена
Сведения о создании учетных записей hiveruser1 и hiveuser2 см. в разделе [Создание присоединенного к домену кластера HDInsight](apache-domain-joined-configure-using-azure-adds.md#create-a-domain-joined-hdinsight-cluster). В этом руководстве вы используете эти две учетные записи.

## <a name="create-ranger-policies"></a>Создание политик Ranger
В этом разделе вы создадите две политики Ranger для доступа к таблице hivesampletable. Вам нужно будет предоставить разрешение select для разных наборов столбцов. Оба пользователя созданы при работе с разделом [Создание присоединенного к домену кластера HDInsight](apache-domain-joined-configure-using-azure-adds.md#create-a-domain-joined-hdinsight-cluster). В следующем разделе вы протестируете две политики в Excel.

**Создание политик Ranger**

1. Откройте пользовательский интерфейс администратора Ranger. См. раздел [Подключение к пользовательскому интерфейсу администратора Apache Ranger](#connect-to-apache-ranager-admin-ui).
2. Щелкните **&lt;имя_кластера>_hive** в разделе **Hive**. Отобразятся две предварительно настроенные политики.
3. Щелкните **Добавить новую политику**, а затем введите следующие значения.

   * Имя политики: read-hivesampletable-all.
   * База данных Hive: по умолчанию.
   * Таблица: hivesampletable.
   * Столбец Hive: *.
   * Пользователь: hiveuser1.
   * Разрешения: select.

     ![Настойка политики Hive Ranger для присоединенного к домену кластера HDInsight](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

     > [!NOTE]
     > Если пользователь домена не указан в поле "Выберите пользователя", подождите несколько минут для синхронизации Ranger с AAD.
     >
     >
4. Щелкните **Добавить**, чтобы сохранить политику.
5. Повторите последние два шага, чтобы создать еще одну политику со следующими свойствами.

   * Имя политики: read-hivesampletable-devicemake.
   * База данных Hive: по умолчанию.
   * Таблица: hivesampletable.
   * Столбец Hive: clientid, devicemake.
   * Пользователь: hiveuser2.
   * Разрешения: select.

## <a name="create-hive-odbc-data-source"></a>Создание источника данных Hive ODBC
Инструкции см. в разделе [Создание источника данных Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Свойство  |ОПИСАНИЕ |
 | --- | --- |
 | Имя источника данных | Присвойте имя источнику данных |
 | Узел | Введите &lt;имя_кластера_HDInsight>.azurehdinsight.net. Например, myHDICluster.azurehdinsight.net |
 | Порт | Используйте **443**. (Этот порт был изменен с 563 на 443.) |
 | База данных | Используйте **значение по умолчанию**. |
 | Тип сервера Hive | Выберите **Hive Server 2**. |
 | Механизм | Выберите **Служба Azure HDInsight**. |
 | Путь HTTP | Оставьте пустым. |
 | Имя пользователя | Укажите hiveuser1@contoso158.onmicrosoft.com. Обновите имя домена, если оно отличается. |
 | Пароль | Введите пароль для hiveuser1. |

Щелкните **Проверка** перед сохранением источника данных.

## <a name="import-data-into-excel-from-hdinsight"></a>Импорт данных в Excel из службы HDInsight
В последнем разделе вы настроили две политики.  У hiveuser1 есть разрешение select на все столбцы, а у hiveuser2 — на два столбца. В этом разделе вы выполните олицетворение двух пользователей для импорта данных в Excel.

1. Откройте новую или существующую рабочую книгу в Excel.
2. На вкладке **Данные** щелкните **From Other Data Sources** (Из других источников данных), а затем выберите **Из мастера подключения к данным**, чтобы запустить **мастер подключения к данным**.

    ![Откройте мастер подключения к данным][img-hdi-simbahiveodbc.excel.dataconnection]
3. Выберите **ODBC DSN** в качестве источника данных и нажмите кнопку **Далее**.
4. В источниках данных ODBC выберите имя источника данных, созданного на предыдущем шаге, и нажмите кнопку **Далее**.
5. В мастере повторно введите пароль для кластера и нажмите кнопку **ОК**. Подождите открытие диалогового окна **Выбор базы данных и таблицы** . Это может занять несколько секунд.
6. Выберите **hivesampletable**, а затем нажмите кнопку **Далее**.
7. Нажмите кнопку **Готово**
8. В диалоговом окне **Импорт данных** можно изменить или указать запрос. Для этого щелкните **Свойства**. Это может занять несколько секунд.
9. Выберите вкладку **Определение**. Текст команды:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   В соответствии с заданными политиками Ranger у hiveuser1 есть разрешение select на все столбцы.  Поэтому этот запрос срабатывает с учетными данными hiveuser1, но не с учетными данными hiveuser2.

   ![Свойства подключения][img-hdi-simbahiveodbc-excel-connectionproperties]
10. Нажмите **OK** , чтобы закрыть диалоговое окно "Свойства подключения".
11. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Импорт данных**.  
12. Введите пароль для hiveuser1 еще раз и нажмите кнопку **ОК**. Пройдет несколько секунд, прежде чем данные будут импортированы в Excel. После этого отобразятся 11 столбцов с данными.

Проверка второй политики (read-hivesampletable-devicemake), созданной в предыдущем разделе

1. Добавьте новый лист в Excel.
2. Выполните последнюю процедуру для импорта данных.  Единственное изменение заключается в том, что нужно использовать учетные данные hiveuser2, а не hiveuser1. При этом произойдет сбой, так как у hiveuser2 есть разрешение только на просмотр двух столбцов. Появится следующая ошибка:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. Выполните ту же процедуру для импорта данных. На этот раз используйте учетные данные hiveuser2 и измените инструкцию FROM в предложении SELECT:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    на:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    После этого импортируются два столбца с данными.

## <a name="next-steps"></a>Дополнительная информация
* Сведения о настройке присоединенного к домену кластера HDInsight см. в [этой статье](apache-domain-joined-configure.md).
* Сведения об управлении присоединенным к домену кластером HDInsight см. в [этой статье](apache-domain-joined-manage.md).
* Сведения о выполнении запросов Hive с помощью SSH в присоединенных к домену кластерах HDInsight см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Сведения о подключении к Hive с помощью Hive JDBC см. в статье [Подключение к Hive в Azure HDInsight с помощью драйвера Hive JDBC](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
* Сведения о подключении Excel к Hadoop с помощью Hive ODBC см. в статье [Подключение Excel к Hadoop с помощью драйвера Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* Сведения о подключении Excel к Hadoop с помощью Power Query см. в [этой статье](../hadoop/apache-hadoop-connect-excel-power-query.md).
