---
title: "Управление разрешениями пользователя на уровнях файлов и папок (Azure HDInsight) | Документация Майкрософт"
description: "Как управлять разрешениями на файлы и папки для кластеров HDInsight, присоединенных к домену."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 9ca91721e691eca239478c4ac8b85e2652babdfd
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2017
---
# <a name="manage-user-permissions-at-the-file-and-folder-levels"></a>Управление разрешениями пользователя на уровнях файлов и папок

[Кластеры HDInsight, присоединенные к домену](./domain-joined/apache-domain-joined-introduction.md), используют строгую аутентификацию пользователей через Azure Active Directory (Azure AD), а также политики *контроля доступа на основе ролей* (RBAC) для таких служб, как YARN и Hive. Если в качестве хранилища данных по умолчанию для кластера вы используете службу хранилища Azure или Windows Azure Storage Blob (WASB), вы можете дополнительно применять разрешения на уровне папок и файлов. С помощью Apache Ranger вы можете управлять доступом к файлам кластера для пользователей и групп, синхронизированных с Azure AD.
<!-- [synchronized Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md). -->

Экземпляр Apache Ranger кластеров HDInsight, присоединенных к домену, предоставляется с предварительно настроенной службой Ranger-WASB. Служба Ranger-WASB представляет собой механизм управления политиками, который похож на Ranger-HDFS, но имеет отличия в логике применения политик доступа Ranger. В службе Ranger-WASB используется запрет (DENY) по умолчанию, если для входящего запроса не существует подходящей политики Ranger. Служба Ranger не передает проверку разрешений в WASB.

## <a name="permission-and-policy-model"></a>Модель разрешений и политик

Запросы на доступ к ресурсам оцениваются по следующему алгоритму.

![Последовательность оценки политик Apache Ranger](./media/hdinsight-add-acls-at-file-folder-levels/ranger-policy-evaluation-flow.png)

Сначала оцениваются запрещающие (DENY) правила, а после них — разрешающие (ALLOW). Если проверка закончилась и не обнаружено подходящих политик, возвращаются сведения о запрете (DENY).

### <a name="user-variable"></a>Переменная USER

Переменную `{USER}` можно использовать, чтобы назначить пользователю политику для доступа к подкаталогу `/{username}`, например так:

```
resource: path=/app-logs/{USER}, user: {USER}, recursive=true, permissions: all, delegateAdmin=true
```

Представленная выше политика предоставляет пользователям доступ к их личным подкаталогам в каталоге `/app-logs/`. Вот как эта политика выглядит в пользовательском интерфейсе Ranger:

![Пример использования переменной USER](./media/hdinsight-add-acls-at-file-folder-levels/user-variable.png)

### <a name="policy-model-examples"></a>Примеры для модели политики

В следующей таблице перечислено несколько примеров работы для модели политики.

| Политика Ranger | Существующий в файловой системе путь | Пользователь, запрос | Результат |
| -- | -- | -- | -- |
| /data/finance/, bob, WRITE | /data | bob, создание файла /data/finance/mydatafile.txt | ALLOW. Механизм проверки предков создает промежуточную папку finance. |
| /data/finance/, bob, WRITE | /data | alice, создание файла /data/finance/mydatafile.txt | DENY. Нет подходящей политики. |
| /data/finance*, bob, WRITE | /data | bob, создание файла /data/finance/mydatafile.txt | ALLOW. Присутствует необязательная рекурсивная политика (`*`). Подробнее см. раздел [о подстановочных знаках](#wildcards). |
| /data/finance/mydatafile.txt, bob, WRITE | /data | bob, создание файла /data/finance/mydatafile.txt | DENY. Механизм проверки предков завершается ошибкой для папки /data, так как не установлена подходящая политика. |
| /data/finance/mydatafile.txt, bob, WRITE | /data/finance | bob, создание файла /data/finance/mydatafile.txt | DENY. Не существует политики для проверки предков для папки /data/finance. |

Разрешения проверяются на уровне папок или файлов, в зависимости от типа операции. Например, запрос на чтение или открытие требует разрешений на чтение на уровне файлов, а запрос на создание требует разрешений на уровне папки для предка.

### <a name="wildcards-"></a>Подстановочные знаки (*)

Если в пути для политики присутствует подстановочный знак (`*`), он применяется к указанному пути и всему поддереву папок. Рекурсия используется так же, как и с флагом `recurse-flag`. В Ranger-WASB подстановочный знак обозначает одновременно рекурсию и частичное сопоставление имен.

## <a name="manage-file-and-folder-level-permissions-with-apache-ranger"></a>Управление разрешениями на уровне папок и файлов в Apache Ranger

Выполните [эти инструкции](./domain-joined/apache-domain-joined-configure.md), чтобы подготовить новый кластер, присоединенный к домену, если у вас его еще нет.

Откройте Ranger-WASB по адресу `https://<YOUR CLUSTER NAME>.azurehdinsight.net/ranger/`. Введите имя пользователя и пароль администратора кластера, которые вы указали при создании кластера.

Войдя, вы увидите панель мониторинга Ranger.

![Панель мониторинга Ranger](./media/hdinsight-add-acls-at-file-folder-levels/ranger-dashboard.png)

Чтобы просмотреть текущие разрешения для файлов и папок кластера, связанного с учетной записью хранения Azure, щелкните ссылку ***имя_кластера*_wasb** в разделе управления WASB.

![Панель мониторинга Ranger](./media/hdinsight-add-acls-at-file-folder-levels/wasb-dashboard-link.png)

Вы увидите список действующих политик. В качестве отправной точки вам предлагается несколько стандартных политик. Проверьте сведения о каждой из них, чтобы увидеть пример использования.

Для каждой политики вы можете увидеть, включена ли она, настроен ли для нее журнал аудита, каким группам и (или) пользователям она присвоена. Для каждой политики есть две кнопки действий: "Изменить" и "Удалить".

![Список политик](./media/hdinsight-add-acls-at-file-folder-levels/policy-list.png)

### <a name="adding-a-new-policy"></a>Создание политики

1. В правом верхнем углу страницы политик WASB выберите **Добавить новую политику**.

    ![Добавление новой политики](./media/hdinsight-add-acls-at-file-folder-levels/add-new.png)

2. Введите описательное **Имя политики**. Укажите **учетную запись хранения** Azure для кластера (*имя_учетной_записи*.blob.core.windows.net) и **контейнер учетной записи хранения**, который вы назначили при создании кластера. Введите **Относительный путь** (относительно кластера) для папки или файла, к которым осуществляется доступ.

    ![Форма добавления политики](./media/hdinsight-add-acls-at-file-folder-levels/new-policy.png)

3. Под формой укажите **Условия разрешения** для нового ресурса. Выберите группы и (или) пользователей, для которых нужно применить политику, и установите нужные разрешения. В следующем примере мы разрешим доступ для чтения и записи всем пользователям в группе `sales`.

    ![Разрешения для группы sales](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales.png)

4. Щелкните **Сохранить**.

### <a name="example-policy-conditions"></a>Пример условий политики

[Алгоритм оценки политик](#permission-and-policy-model) в Apache Ranger позволяет указать любое сочетание разрешающих и запрещающих условий в соответствии с вашими задачами. Вот несколько таких случаев.

1. Разрешить доступ всем пользователям группы sales, но запретить группе interns:

    ![Разрешить группе sales, запретить группе interns](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns.png)

2. Разрешить доступ всем пользователям группы sales и запретить группе interns, за исключением члена этой группы с именем hiveuser3, которому разрешен доступ на чтение:

    ![Разрешить группе sales, запретить группе interns, кроме hiveuser3](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns-except-hiveuser3.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка политик Hive в присоединенном к домену кластере HDInsight (предварительная версия)](./domain-joined/apache-domain-joined-run-hive.md)
* [Управление присоединенными к домену кластерами HDInsight (предварительная версия)](./domain-joined/apache-domain-joined-manage.md)
* [Authorize users for Ambari Views](hdinsight-authorize-users-to-ambari.md) (Управление пользователями для Ambari Views)

<!-- * [Synchronize Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md) -->

