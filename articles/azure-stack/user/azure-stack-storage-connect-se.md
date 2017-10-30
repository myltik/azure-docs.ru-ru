---
title: "Подключение обозревателя службы хранилища к подписке Azure Stack"
description: "Узнайте, как подключить обозреватель службы хранилища к подписке Azure Stack"
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: c7e6d70148d39fd74f6409a0a239833f8e9f7614
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/12/2017
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription"></a>Подключение обозревателя службы хранилища к подписке Azure Stack

*Область применения: интегрированные системы Azure Stack и комплект разработки Azure Stack*

Обозреватель службы хранилища Azure (предварительная версия) — это автономное приложение, позволяющее легко работать с данными из хранилища Azure Stack на платформе Windows, macOS и Linux. Доступно несколько средств для перемещения данных в хранилище Azure Stack и из него. Дополнительные сведения см. в статье [Средства для хранилища Azure Stack](azure-stack-storage-transfer.md).

В этой статье вы узнаете, как подключиться к учетным записям хранения Azure Stack с помощью обозревателя службы хранилища. 

Если обозреватель хранилищ еще не установлен на компьютере, [скачайте](http://www.storageexplorer.com/) и установите его.

Когда вы подключитесь к подписке Azure Stack, для дальнейшей работы с данными Azure Stack можно использовать статьи об [обозревателе службы хранилища Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md). 

## <a name="prepare-an-azure-stack-subscription"></a>Подготовка подписки Azure Stack

Чтобы получить доступ к подписке Azure Stack, обозревателю службы хранилища требуется доступ к рабочему столу хост-компьютера или VPN-подключение. Дополнительные сведения о настройке VPN-подключения в Azure Stack см. в разделе [Подключение c VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

Для комплекта разработки Azure Stack необходимо экспортировать корневой сертификат центра сертификации Azure Stack.

### <a name="to-export-and-then-import-the-azure-stack-certificate"></a>Экспорт и импорт сертификата Azure Stack

1. Откройте `mmc.exe` на хост-компьютере или локальном компьютере Azure Stack с помощью VPN-подключения к Azure Stack. 

2. В меню **Файл** выберите **Добавить или удалить оснастку**, затем добавьте **сертификаты** для управления **своей учетной записью пользователя**.



3. Найдите **AzureStackSelfSignedRootCert** в разделе **Console Root (Корень консоли)\Certificated (Local Computer) (Cертифицированный (локальный компьютер))\Trusted Root Certification Authorities (Доверенные корневые центры сертификации)\Сертификаты**.

    ![Загрузка корневого сертификата Azure Stack с помощью mmc.exe][25]

4. Щелкните правой кнопкой мыши сертификат, выберите **All Tasks** (Все задачи) > **Export** (Экспорт), а затем следуйте инструкциям, чтобы экспортировать сертификат в виде **файла X.509 (CER) в кодировке Base-64**.  

    Экспортированный сертификат будет использоваться на следующем шаге.
5. Запустите обозреватель службы хранилища (предварительная версия) и закройте диалоговое окно **Connect to Azure Storage** (Подключение к службе хранилища Azure), если оно отобразится.

6. В меню **Изменить** выберите **SSL-сертификаты**, а затем щелкните **Импорт сертификатов**. Используйте диалоговое окно выбора файла, чтобы найти и открыть сертификат, изученный на предыдущем шаге.

    После импорта вам будет предложено перезапустить обозреватель хранилищ.

    ![Импорт сертификата в обозреватель хранилищ (предварительная версия)][27]

Теперь можно подключать обозреватель службы хранилища к подписке Azure Stack.

### <a name="to-connect-an-azure-stack-subscription"></a>Подключение к подписке Azure Stack


1. После перезапуска обозревателя хранилищ (предварительная версия) откройте меню **Изменить** и убедитесь, что **Target Azure Stack** (Целевой объект Azure Stack) выбран. В противном случае выберите его и перезапустите обозреватель хранилищ, чтобы изменения вступили в силу. Эта настройка необходима для совместимости со средой Azure Stack.

    ![Проверка установки флажка Target Azure Stack (Целевой объект Azure Stack)][28]

7. На панели слева щелкните **Управление учетными записями**.  
    Отобразятся все учетные записи Майкрософт, в которые вы вошли.

8. Чтобы подключиться к учетной записи Azure Stack, выберите **Добавить учетную запись**.

    ![Добавление учетной записи Azure Stack][29]

9. В диалоговом окне **Connect to Azure Storage** (Подключение к службе хранилища Azure) в разделе **Azure environment** (Среда Azure) выберите **Use Azure Stack Environment** (Использовать среду Azure Stack), а затем щелкните **Далее**.

10. Заполните диалоговое окно **Sign in to Azure Stack Environment** (Вход в среду Azure Stack), чтобы войти с использованием учетной записи Azure Stack, связанной по крайней мере с одной активной подпиской Azure Stack.  

    Ниже приведены подробные сведения о каждом поле диалогового окна.

    * **Имя среды.** Это поле может настраивать пользователь.
    * **ARM resource endpoint** (Конечная точка ресурса ARM). Примеры конечной точки ресурсов ARM:

        * Для оператора облака:<br> https://adminmanagement.local.azurestack.external   
        * Для клиента:<br> https://management.local.azurestack.external
 
    * **Идентификатор клиента** — необязательно. Значение задается, только если необходимо указать каталог.

12. Выполнив вход с помощью учетной записи Azure Stack, на панели слева вы увидите подписки Azure Stack, связанные с этой учетной записью. Выберите подписки Azure Stack, с которыми вы собираетесь работать, а затем щелкните **Применить**. (Чтобы выбрать или очистить все подписки Azure Stack, установите флажок **Все подписки**. Чтобы отменить выбор, снимите флажок.)

    ![Выбор подписок Azure Stack после заполнения диалогового окна Custom Cloud Environment (Пользовательская облачная среда)][30]  
    На панели слева отобразятся все учетные записи хранения, связанные с выбранными подписками Azure Stack.

    ![Список учетных записей хранения, включая учетные записи подписки Azure Stack][31]

## <a name="next-steps"></a>Дальнейшие действия
* [Приступая к работе с обозревателем службы хранилища (предварительная версия)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Хранилище Azure Stack. Отличия и рекомендации](azure-stack-acs-differences.md)


* Дополнительные сведения о службе хранилища Azure см. в статье [Введение в хранилище Microsoft Azure](../../storage/common/storage-introduction.md).

[25]: ./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png
[26]: ./media/azure-stack-storage-connect-se/export-root-cert-azure-stack.png
[27]: ./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png
[28]: ./media/azure-stack-storage-connect-se/select-target-azure-stack.png
[29]: ./media/azure-stack-storage-connect-se/add-azure-stack-account.png
[30]: ./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png
[31]: ./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png
