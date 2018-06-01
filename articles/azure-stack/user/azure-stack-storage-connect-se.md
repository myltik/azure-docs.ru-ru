---
title: Подключение обозревателя службы хранилища к подписке Azure Stack или к учетной записи хранения | Документация Майкрософт
description: Узнайте, как подключить обозреватель службы хранилища к подписке Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/14/2018
ms.author: mattbriggs
ms.reviewer: xiaofmao
ms.openlocfilehash: a3a69f3cf91c0d202d54d0da5dc5fe0531ef9db1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196383"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Подключение обозревателя службы хранилища к подписке Azure Stack или к учетной записи хранения

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Из этой статьи вы узнаете, как подключаться к подпискам Azure Stack и учетным записям хранения с помощью Обозревателя службы хранилища Azure. Обозреватель службы хранилища Azure — это автономное приложение, позволяющее легко работать с данными из хранилища Azure Stack на платформе Windows, macOS и Linux.

>[!NOTE]
>Доступно несколько средств для перемещения данных в хранилище Azure Stack и из него. Дополнительные сведения см. в статье [Средства для хранилища Azure Stack](azure-stack-storage-transfer.md).

Если Обозреватель службы хранилища еще не установлен, [скачайте](http://www.storageexplorer.com/) и установите его.

Когда вы подключитесь к подписке Azure Stack или к учетной записи хранения, для дальнейшей работы с данными Azure Stack можно использовать статьи об [обозревателе службы хранилища Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md). 

## <a name="prepare-for-connecting-to-azure-stack"></a>Подготовка к подключению к Azure Stack

Чтобы получить доступ к подписке Azure Stack, обозревателю службы хранилища требуется прямой доступ к Azure Stack или VPN-подключение. Дополнительные сведения о настройке VPN-подключения в Azure Stack см. в разделе [Подключение c VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

Для Пакета средств разработки Azure Stack необходимо экспортировать корневой сертификат центра сертификации Azure Stack.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Экспорт и импорт сертификата Azure Stack

1. Откройте `mmc.exe` на хост-компьютере или локальном компьютере Azure Stack с помощью VPN-подключения к Azure Stack. 

2. В меню **Файл** выберите **Добавить или удалить оснастку**, затем добавьте **сертификаты** для управления **своей учетной записью пользователя**.

3. Найдите **AzureStackSelfSignedRootCert** в разделе **Console Root (Корень консоли)\Certificated (Local Computer) (Cертифицированный (локальный компьютер))\Trusted Root Certification Authorities (Доверенные корневые центры сертификации)\Сертификаты**.

    ![Загрузка корневого сертификата Azure Stack с помощью mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

4. Щелкните правой кнопкой мыши сертификат, выберите **All Tasks** (Все задачи) > **Export** (Экспорт), а затем следуйте инструкциям, чтобы экспортировать сертификат в виде **файла X.509 (CER) в кодировке Base-64**.

    Экспортированный сертификат будет использоваться на следующем шаге.

5. Запустите Обозреватель службы хранилища и закройте диалоговое окно **Подключение к службе хранилища Azure**, если оно отобразится.

6. В меню **Изменить** последовательно выберите **SSL-сертификаты**, а затем **Импорт сертификатов**. Используйте диалоговое окно выбора файла, чтобы найти и открыть сертификат, изученный на предыдущем шаге.

    После импорта сертификата появится запрос на перезапуск Обозревателя службы хранилища.

    ![Импорт сертификата в Обозреватель службы хранилищ](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. После перезапуска Обозревателя службы хранилища откройте меню **Изменить** и проверьте, выбран ли параметр **Target Azure Stack** (Целевой объект Azure Stack). Если он не выбран, установите флажок **Target Azure Stack** (Целевой объект Azure Stack) и перезапустите Обозреватель службы хранилища, чтобы изменения вступили в силу. Эта настройка необходима для совместимости со средой Azure Stack.

    ![Проверка установки флажка Target Azure Stack (Целевой объект Azure Stack)](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription"></a>Подключение к подписке Azure Stack

Чтобы подключить Обозреватель службы хранилища к подписке Azure Stack, сделайте следующее.

1. На панели обозревателя хранилища слева выберите **Управление учетными записями**. 
    Отобразятся все подписки Майкрософт, в которые вы вошли.

2. Чтобы подключиться к подписке Azure Stack, выберите **Добавить учетную запись**.

    ![Добавление учетной записи Azure Stack](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. В диалоговом окне Connect to Azure Storage (Подключение к службе хранилища Azure) в разделе **Среда: Azure** выберите **Azure** или **Azure для Китая** в зависимости от используемой учетной записи Azure Stack. Нажмите кнопку **входа**, чтобы войти в учетную запись Azure Stack, связанную по крайней мере с одной активной подпиской Azure Stack.

    ![Подключение к службе хранилища Azure](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Выполнив вход с помощью учетной записи Azure Stack, на панели слева вы увидите подписки Azure Stack, связанные с этой учетной записью. Выберите подписки Azure Stack, с которыми вы собираетесь работать, а затем щелкните **Применить**. (Чтобы выбрать или очистить все подписки Azure Stack, установите флажок **Все подписки**. Чтобы отменить выбор, снимите флажок.)

    ![Выбор подписок Azure Stack после заполнения диалогового окна Custom Cloud Environment (Пользовательская облачная среда)](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    На панели слева отобразятся все учетные записи хранения, связанные с выбранными подписками Azure Stack.

    ![Список учетных записей хранения, включая учетные записи подписки Azure Stack](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Подключение к учетной записи хранения Azure Stack

Для подключения к учетной записи хранения Azure Stack также можно использовать имя учетной записи хранения и пару ключей.

1. На панели обозревателя хранилища слева выберите "Управление учетными записями". Отобразятся учетные записи Майкрософт, в которые вы вошли.

    ![Добавить учетную запись](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Чтобы подключиться к подписке Azure Stack, выберите **Добавить учетную запись**.

    ![Добавить учетную запись](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. В диалоговом окне "Подключения к службе хранилища Azure" выберите **Use a storage account name and key** (Использовать имя и ключ учетной записи хранения).

4. Введите имя учетной записи в текстовое поле **Имя учетной записи**, вставьте ключ учетной записи в текстовое поле **Ключ учетной записи**, выберите **Other (enter below)** (Другие (введите ниже)) из раскрывающегося списка **Домен конечных точек хранилища** и вставьте входную конечную точку Azure Stack в текстовое поле внизу.

    Конечная точка Azure Stack содержит две части: имя региона и домен Azure Stack. В Пакете средств разработки Azure Stack конечной точкой по умолчанию является **local.azurestack.external**. Если вы не знаете конечную точку, обратитесь к администратору облака.

    ![Подключение имени и ключа учетной записи](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Нажмите кнопку **Подключиться**.
6. После успешного подключения учетная запись хранения появится в списке, а к ее имени будет добавлен текст (**External, Other**) (Внешняя, другая).

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Дополнительная информация

* [Начало работы с Обозревателем службы хранилища](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
* [Хранилище Azure Stack. Отличия и рекомендации](azure-stack-acs-differences.md)
* Дополнительные сведения о службе хранилища Azure см. в статье [Введение в хранилище Microsoft Azure](../../storage/common/storage-introduction.md).
