---
title: "Шлюз управления данными для фабрики данных | Документация Майкрософт"
description: "Настройка шлюза данных для перемещения данных между локальными узлами и облаком. Используйте шлюз управления данными в фабрике данных Azure для перемещения данных."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: monicar
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: abnarain
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 9e40eba285aeb1cce6b77311d1b69a6b96967a0b
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---
# <a name="data-management-gateway"></a>Шлюз управления данными
Шлюз управления данными — это агент клиента, который необходимо установить в локальной среде, чтобы обеспечить копирование данных между облачными и локальными хранилищами данных. Перечень локальных хранилищ данных, поддерживаемых фабрикой данных, приведен в разделе [Поддерживаемые хранилища данных и форматы](data-factory-data-movement-activities.md#supported-data-stores-and-formats).

Эта статья дополняет пошаговое руководство, приведенное в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md). В руководстве создается конвейер, который использует шлюз для перемещения данных из локальной базы данных SQL Server в большой двоичный объект Azure. В этой статье содержатся подробные сведения об использовании шлюза управления данными. 

Шлюз управления данными можно развернуть, сопоставив с ним несколько локальных компьютеров. Чтобы увеличить масштаб, увеличьте число заданий перемещения данных, которые могут выполняться одновременно на узле. Эта функция также доступна для логического шлюза с одним узлом. Дополнительные сведения см. в статье [Шлюз управления данными: высокий уровень доступности и масштабируемость (предварительная версия)](data-factory-data-management-gateway-high-availability-scalability.md).

> [!NOTE]
> В настоящее время шлюз поддерживает действия копирования и действие хранимой процедуры только в фабрике данных. Невозможно использовать шлюз из настраиваемого действия для доступа к локальным источникам данных.      

## <a name="overview"></a>Обзор
### <a name="capabilities-of-data-management-gateway"></a>Возможности шлюза управления данными
Шлюз управления данными предоставляет следующие возможности.

* Моделирование источников локальных и облачных данных в пределах одной фабрики данных, а также перемещение данных.
* Использование единой панели для мониторинга и управления с возможностью контроля состояния шлюза на странице фабрики данных.
* Безопасное управление доступом к локальным источникам данных.
  * Изменять настройки корпоративного брандмауэра не требуется. Шлюз только пропускает исходящие HTTP-соединения во внешнюю сеть.
  * Учетные данные для ваших локальных хранилищ данных можно шифровать с помощью личного сертификата.
* Эффективное перемещение данных: данные передаются параллельно, благодаря логике автоматического повторения перемещение не зависит от временных сетевых проблем.

### <a name="command-flow-and-data-flow"></a>Поток команд и поток данных
Если для копирования локальных данных в облако либо для передачи данных из облака обратно в локальное хранилище используется действие копирования, то в таких операциях участвует шлюз данных.

Ниже показана обобщенная схема потока данных и этапы копирования через шлюз. ![Поток данных при использовании шлюза](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Разработчик создает шлюз для фабрики данных Azure, используя [портал Azure](https://portal.azure.com) или [командлет PowerShell](https://msdn.microsoft.com/library/dn820234.aspx).
2. Разработчик создает связанную службу для локального хранилища данных, указывая шлюз. В ходе настройки связанной службы разработчик указывает типы аутентификации и учетные данные в приложении настройки учетных данных.  Приложение настройки учетных данных проверяет подключение к хранилищу данных и шлюзу и сохраняет учетные данные.
3. Перед сохранением учетных данных в облаке шлюз шифрует их при помощи связанного с ним сертификата (указывается разработчиком).
4. Служба фабрики данных взаимодействует со шлюзом, планируя задания и управляя ими через специальный канал. Этот канал управления использует общую очередь служебной шины Azure. Когда нужно начать задание копирования, фабрика данных добавляет в очередь запрос с указанием учетных данных. После опроса очереди шлюз начинает задание.
5. Шлюз расшифровывает учетные данные с помощью того же сертификата, после чего подключается к локальному хранилищу данных, используя соответствующий тип аутентификации и учетные данные.
6. Шлюз копирует данные из локального хранилища в облачное или из облачного обратно в локальное в зависимости от настроек действия копирования в конвейере данных. На этом этапе шлюз напрямую взаимодействует с облачными службами хранилища, такими как хранилище BLOB-объектов Azure, через защищенный канал (HTTPS).

### <a name="considerations-for-using-gateway"></a>Рекомендации по использованию шлюза
* Для нескольких локальных источников данных можно использовать один шлюз управления данными. Однако **каждый экземпляр шлюза связан только с одной фабрикой данных Azure** и не может использоваться совместно с другой.
* На компьютере может быть установлен **только один экземпляр шлюза управления данными**. Предположим, у вас есть две фабрики данных, которым необходимо получить доступ к локальным источникам данных. В этом случае вам необходимо установить шлюзы на двух локальных компьютерах. Другими словами, каждый шлюз связан с отдельной фабрикой данных.
* **Шлюз не обязательно устанавливать на том же компьютере, что и источник данных**. Но, если он расположен вблизи источника данных, это сокращает количество времени, требуемого для подключения шлюза к этому источнику. Рекомендуется установить шлюз на компьютере, отличном от того, на котором размещен локальный источник данных. Когда шлюз и источник данных находятся на разных компьютерах, шлюз не конкурирует за использование ресурсов с источником данных.
* У вас может быть **несколько шлюзов на разных компьютерах, подключенных к одному и тому же локальному источнику данных**. Например, имеется два шлюза, обслуживающие две фабрики данных, однако один и тот же локальный источник данных зарегистрирован в обеих фабриках данных.
* Если вы уже установили шлюз на компьютер, который обслуживает сценарий **Power BI**, установите **отдельный шлюз для фабрики данных Azure** на другой компьютер.
* Шлюз необходимо использовать, даже если используется **ExpressRoute**.
* Источник данных следует считать локальным (то есть защищенным брандмауэром), даже если используется **ExpressRoute**. А для связи между службой и источником данных используется шлюз.
* Необходимо **использовать шлюз**, даже если хранилище данных находится в облаке на **виртуальной машине IaaS Azure**.

## <a name="installation"></a>Установка
### <a name="prerequisites"></a>Предварительные требования
* Поддерживаемые **операционные системы**: Windows 7, Windows 8, Windows 8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. В настоящее время установка шлюза управления данными на контроллере домена не поддерживается.
* Требуется .NET Framework 4.5.1 или более поздней версии. При установке шлюза на компьютере под управлением Windows 7 установите .NET Framework 4.5 или более поздней версии. Дополнительные сведения см. в разделе [Требования к системе для .NET Framework](https://msdn.microsoft.com/library/8z6watww.aspx).
* Рекомендуемая **конфигурация** для компьютера шлюза: четырехъядерный процессор с тактовой частотой не менее 2 ГГц, не менее 8 ГБ ОЗУ и 80 ГБ дискового пространства.
* Когда хост-компьютер переходит в спящий режим, шлюз не может отвечать на запросы данных. Поэтому перед установкой шлюза на компьютере следует настроить соответствующую **схему управления питанием**. Если компьютер настроен на использование режима гибернации, во время установки шлюза отобразится соответствующее сообщение.
* Для успешной установки шлюза управления данными и его настройки вы должны обладать правами администратора на компьютере. В локальную группу Windows **Пользователи шлюза управления данными** можно добавить дополнительных пользователей. Участники этой группы могут использовать **диспетчер конфигурации шлюза управления данными** для настройки шлюза.

Так как циклы копирования выполняются с определенной частотой, ресурсы компьютера (ЦП, память) используются нерегулярно: есть пиковые нагрузки и есть время простоя. Использование ресурсов также зависит от объема перемещаемых данных. Когда выполняется несколько заданий копирования, в пиковые периоды уровень использования ресурсов системы повышается.

### <a name="installation-options"></a>Параметры установки
Шлюз управления данными можно установить одним из следующих способов.

* Скачать установочный пакет MSI из [Центра загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=39717).  Пакет MSI также может использоваться для обновления имеющегося шлюза управления данными до последней версии с сохранением всех параметров.
* Щелкнуть ссылку **Скачивание и установка шлюза данных** в разделе "Установка вручную" или ссылку **Установка непосредственно на этот компьютер** в разделе "Экспресс-установка". В разделе [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md) приведены пошаговые инструкции по экспресс-установке. В случае ручной установки вы перейдете к центру загрузки.  Инструкции по скачиванию и установке шлюза из центра загрузки приведены в следующем разделе.

### <a name="installation-best-practices"></a>Рекомендации по установке
1. Настройте схему управления питанием на хост-компьютере шлюза, отключив спящий режим. Когда хост-компьютер переходит в спящий режим, шлюз не может отвечать на запросы данных.
2. Создайте резервную копию сертификата, связанного со шлюзом.

### <a name="install-the-gateway-from-download-center"></a>Установка шлюза из центра загрузки
1. Перейдите на [страницу скачивания шлюза управления данными Майкрософт](https://www.microsoft.com/download/details.aspx?id=39717).
2. Щелкните **Скачать**, выберите нужную версию (**32-разрядную** или **64-разрядную**) и нажмите кнопку **Далее**.
3. Сразу запустите **MSI** -файл или сохраните его на жесткий диск и затем запустите.
4. На странице **приветствия** выберите **язык**, после чего нажмите кнопку **Далее**.
5. **Примите** условия лицензионного соглашения и нажмите кнопку **Далее**.
6. Выберите **папку** для установки шлюза, затем нажмите кнопку **Далее**.
7. На странице **Готово к установке** щелкните **Установить**.
8. Нажмите кнопку **Готово** для завершения установки.
9. Получите ключ на портале Azure. Пошаговые инструкции представлены в следующем разделе.
10. Запустите на компьютере **диспетчер конфигурации шлюза управления данными**, затем на странице **Регистрация шлюза** выполните следующие действия.
    1. Вставьте ключ в текст.
    2. При необходимости щелкните **Показать ключ шлюза**, чтобы просмотреть текст ключа.
    3. Щелкните **Зарегистрировать**.

### <a name="register-gateway-using-key"></a>Регистрация шлюза с помощью ключа
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Если логический шлюз на портале еще не создан
Чтобы создать шлюз на портале и найти ключ на странице **Настройка**, выполните действия из пошагового руководства в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md).    

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Если логический шлюз на портале уже создан
1. На портале Azure перейдите к странице **Фабрика данных** и щелкните элемент **Связанные службы**.

    ![Страница "Фабрика данных"](media/data-factory-data-management-gateway/data-factory-blade.png)
2. На странице **Связанные службы** выберите логический **шлюз**, созданный на портале.

    ![Логический шлюз](media/data-factory-data-management-gateway/data-factory-select-gateway.png)  
3. На странице **Шлюз данных** щелкните **Скачивание и установка шлюза данных**.

    ![Ссылка для скачивания на портале](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)   
4. На странице **Настройка** щелкните **Повторное создание ключа**. Нажмите кнопку "Да" в окне предупреждающего сообщения, внимательно его прочитав.

    ![Повторное создание ключа](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Нажмите кнопку "Копировать" рядом с ключом. Ключ cкопируется в буфер обмена.

    ![Копирование ключа](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Значки и уведомления в области уведомлений
На следующем рисунке показаны некоторые значки панели задач.

![значки на панели задач](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Если навести курсор на значок или сообщение уведомления на панели задач, появится всплывающее окно со сведениями о состоянии шлюза или операции обновления.

### <a name="ports-and-firewall"></a>Порты и брандмауэр
Описанные здесь рекомендации относятся к двум брандмауэрам: к **корпоративному брандмауэру**, работающему на центральном маршрутизаторе организации, и к **брандмауэру Windows**, настроенному в качестве управляющей программы на локальном компьютере, на котором установлен шлюз.  

![брандмауэры](./media/data-factory-data-management-gateway/firewalls2.png)

На уровне корпоративного брандмауэра необходимо настроить следующие домены и исходящие порты:

| Имена доменов | порты; | Описание |
| --- | --- | --- |
| *.servicebus.windows.net |443, 80 |Используется для связи с серверной частью службы перемещения данных. |
| *.core.windows.net |443 |Используется для промежуточного копирования с помощью большого двоичного объекта Azure (если оно настроено).|
| *.frontend.clouddatahub.net |443 |Используется для связи с серверной частью службы перемещения данных. |


Эти исходящие порты, как правило, включены на уровне брандмауэра Windows. В противном случае домены и порты можно соответствующим образом настроить на компьютере шлюза.

> [!NOTE]
> 1. В зависимости от того, какие источники и приемники используются, может потребоваться добавить в список разрешений корпоративного брандмауэра или брандмауэра Windows дополнительные домены и исходящие порты.
> 2. Для некоторых баз данных в облаке (например, [База данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings), [Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access) и т. д.) может потребоваться добавить в список разрешений брандмауэра IP-адрес компьютера шлюза.
>
>


#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Копирование данных из источника данных в приемник данных
Убедитесь, что правила брандмауэра в корпоративном брандмауэре, брандмауэре Windows на компьютере шлюза и в самом хранилище данных активированы надлежащим образом. Активация этих правил позволяет шлюзу успешно подключаться как к источнику, так и к приемнику. Активируйте правила для каждого хранилища данных, задействованного в операции копирования.

Например, при копировании **данных из локального хранилища в приемник базы данных SQL Azure или приемник хранилища данных SQL Azure**выполните следующее.

* Разрешите исходящий трафик **TCP** через порт **1433** для брандмауэра Windows и корпоративного брандмауэра.
* в параметрах брандмауэра Azure SQL Server добавить IP-адрес компьютера шлюза в список разрешенных IP-адресов.

> [!NOTE]
> Если брандмауэр блокирует исходящий порт 1433, то шлюз не сможет напрямую получить доступ к SQL Azure. В этом случае можно использовать [промежуточное копирование](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) в базу данных SQL Azure или хранилище данных SQL Azure. Тогда для перемещения данных понадобится только протокол HTTPS (порт 443).
>
>


### <a name="proxy-server-considerations"></a>Рекомендации для прокси-сервера
Если для доступа в Интернет в среде вашей корпоративной сети используется прокси-сервер, настройте шлюз управления данными для применения соответствующих параметров прокси-сервера. Прокси-сервер можно настроить на этапе начальной регистрации.

![Настройка прокси-сервера при регистрации](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Шлюз использует прокси-сервер для подключения к облачной службе. Щелкните ссылку **Изменить** во время начальной настройки. Отобразится диалоговое окно **Proxy setting** (Настройка прокси-сервера).

![Настройка прокси-сервера с помощью диспетчера конфигурации](media/data-factory-data-management-gateway/SetProxySettings.png)

Доступны три варианта конфигурации.

* **Без прокси**: шлюз явно не использует никакой прокси-сервер для подключения к облачным службам.
* **Использовать системный прокси**: шлюз использует параметры прокси-сервера, настроенные в файлах diahost.exe.config и diawp.exe.config.  Если в файлах diahost.exe.config и diawp.exe.config параметры прокси-сервера не настроены, шлюз подключается к облачной службе напрямую, не используя прокси-сервер.
* **Использовать настраиваемый прокси**: шлюз использует параметры HTTP-прокси вместо конфигурации, настроенной в файлах diahost.exe.config и diawp.exe.config.  Необходимо указать адрес и порт.  Имя пользователя и пароль являются необязательными и указываются в зависимости от настроек аутентификации прокси-сервера.  Все параметры шифруются с помощью сертификата учетных данных шлюза и сохраняются локально на хост-компьютере шлюза.

Служба узла шлюза управления данными автоматически перезапускается после сохранения обновленных параметров прокси-сервера.

После успешной регистрации шлюза, если требуется просмотреть или обновить параметры прокси-сервера, используйте диспетчер конфигурации шлюза управления данными.

1. Запустите **диспетчер конфигурации шлюза управления данными**.
2. Переключитесь на вкладку **Параметры**.
3. Щелкните ссылку **Изменить** в разделе **HTTP-прокси**, чтобы открыть диалоговое окно **Указание HTTP-прокси**.  
4. После нажатия кнопки **Далее** появится диалоговое окно предупреждения, запрашивающее разрешение на сохранение настроек прокси-сервера и перезапуск службы узла шлюза.

При помощи диспетчера конфигурации можно просмотреть и обновить HTTP-прокси.

![Настройка прокси-сервера с помощью диспетчера конфигурации](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Если настроить на прокси-сервере аутентификацию NTLM, то служба узла шлюза будет запускаться под учетной записью домена. Если через какое-то время вы измените пароль для учетной записи домена, то не забудьте обновить параметры конфигурации службы и перезапустить ее. Учитывая это требование, для доступа к прокси-серверу рекомендуется использовать выделенную учетную запись домена, для которой не требуется часто менять пароль.
>
>

### <a name="configure-proxy-server-settings"></a>Настройка параметров прокси-сервера
Если для HTTP-прокси выбран вариант конфигурации **Использовать системный прокси**, шлюз использует параметры прокси-сервера, заданные в файлах diahost.exe.config и diawp.exe.config.  Если в файлах diahost.exe.config и diawp.exe.config параметры прокси-сервера не заданы, шлюз подключается к облачной службе напрямую, не используя прокси-сервер. Ниже приводится процедура обновления файла конфигурации diahost.exe.config.  

1. В проводнике создайте резервную копию исходного файла C:\Program Files\Microsoft Data Management Gateway\2.0\Shared\diahost.exe.config.
2. Запустите файл Notepad.exe с правами администратора и откройте текстовый файл с путем C:\Program Files\Microsoft Data Management Gateway\2.0\Shared\diahost.exe.config. Найдите тег по умолчанию для system.net, как показано в коде ниже.

         <system.net>
             <defaultProxy useDefaultCredentials="true" />
         </system.net>    

   Затем можно добавить данные прокси-сервера, как показано в следующем примере:

         <system.net>
               <defaultProxy enabled="true">
                     <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
               </defaultProxy>
         </system.net>

   Внутри тега прокси-сервера можно указать дополнительные свойства, чтобы задать требуемые параметры, например scriptLocation. Сведения о синтаксисе см. в статье [Элемент <proxy> (параметры сети)](https://msdn.microsoft.com/library/sa91de1e.aspx).

         <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
3. Сохраните файл конфигурации в исходном расположении, а затем перезапустите службу узла шлюза управления данными, которая получает изменения. Чтобы перезапустить службу, воспользуйтесь приложением "Службы" на панели управления или откройте **диспетчер конфигурации шлюза управления данными**, нажмите кнопку **Остановить службу**, а затем щелкните **Запустить службу**. Если служба не запускается, вероятно, в измененном файле конфигурации приложения добавлен неправильный синтаксис тегов XML.

> [!IMPORTANT]
> Не забудьте обновить **оба** файла (diahost.exe.config и diawp.exe.config).  


Кроме того, необходимо также убедиться, что Microsoft Azure входит в белый список в вашей компании. Список допустимых IP-адресов Microsoft Azure можно скачать из [Центра загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Возможные признаки проблем, связанных с брандмауэром и прокси-сервером
Вероятная причина приведенных ниже ошибок — неправильная конфигурация брандмауэра или прокси-сервера, которая блокирует подключение шлюза к фабрике данных для аутентификации. Чтобы настроить брандмауэр и прокси-сервер должным образом, см. предыдущий раздел.

1. При попытке зарегистрировать шлюз появляется следующая ошибка: "Не удалось зарегистрировать ключ шлюза. Перед повторной попыткой регистрации ключа убедитесь, что шлюз управления данными подключен и на хост-компьютере запущена служба шлюза управления данными".
2. При открытии диспетчера конфигурации отображается состояние "Отключено" или "Подключение". В средстве просмотра событий Windows в разделе "Журналы приложения и служб" > "Шлюз управления данными" отображается сообщение об ошибке следующего вида: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Открытие порта 8050 для шифрования учетных данных
Приложение **настройки учетных данных** использует входящий порт **8050** для ретрансляции учетных данных, которые вы указываете на портале Azure для локальной связанной службы, в шлюз учетных данных. По умолчанию при установке шлюза программа установки открывается на компьютере шлюза.

В случае использования стороннего брандмауэра вы сможете открыть порт 8050 вручную. Если во время установки шлюза возникли проблемы с брандмауэром, попробуйте установить шлюз без настройки брандмауэра, используя следующую команду:

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Если вы не хотите открывать порт 8050 на компьютере шлюза, то настроить учетные данные для хранилища данных с помощью приложения **настройки учетных данных** будет невозможно. Используйте другие механизмы настройки. Например, можно использовать командлет PowerShell [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx). Дополнительные сведения о настройке учетных данных хранилища данных см. в разделе [Настройка учетных данных и безопасность](#set-credentials-and-securityy).

## <a name="update"></a>Блокировка изменений
По умолчанию шлюз управления данными автоматически обновляется, если доступна новая версия. Он не будет обновлен, пока не будут выполнены все запланированные задания. До завершения операции обновления в шлюзе не будут обрабатываться какие-либо дополнительные задачи. Если обновление завершится сбоем, для шлюза будет выполнен откат к предыдущей версии.

Время запланированного обновления см. в следующих местах:

* страница свойств шлюза на портале Azure;
* домашняя страница диспетчера конфигурации шлюза управления данными;
* сообщение в области уведомлений.

На вкладке "Главная" диспетчера конфигурации шлюза управления данными отображается расписание обновления, а также время последнего обновления или установки шлюза.

![запланировать обновления](media/data-factory-data-management-gateway/UpdateSection.png)

Вы можете установить обновление сразу или дождаться, пока шлюз не будет автоматически обновлен в запланированное время. На следующем изображении показано сообщение уведомления на странице диспетчера конфигурации шлюза вместе с кнопкой "Обновить", которую можно нажать, чтобы установить обновление немедленно.

![Обновление диспетчера конфигураций DMG](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Сообщение уведомления в области уведомлений будет выглядеть следующим образом.

![Сообщение на панели задач](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

На панели задач отображается состояние операции обновления (выполняемой вручную или автоматически). В следующий раз при запуске диспетчера конфигурации шлюза на панели уведомлений отобразится сообщение об обновлении шлюза и ссылка на [раздел о новых возможностях](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Включение или отключение функции автоматического обновления
Чтобы включить или отключить функцию автоматического обновления, сделайте следующее.

(Для шлюза с одним узлом)
1. Запустите Windows PowerShell на компьютере шлюза.
2. Перейдите в папку C:\Program Files\Microsoft Data Management Gateway\2.0\PowerShellScript.
3. Выполните следующую команду, чтобы отключить функцию автоматического обновления.   

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -off
    ```
4. Чтобы снова включить ее:

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -on  
    ```
([Для высокодоступного и масштабируемого шлюза с несколькими узлами (предварительная версия)](data-factory-data-management-gateway-high-availability-scalability.md))
1. Запустите Windows PowerShell на компьютере шлюза.
2. Перейдите в папку C:\Program Files\Microsoft Data Management Gateway\2.0\PowerShellScript.
3. Выполните следующую команду, чтобы отключить функцию автоматического обновления.   

    Для шлюза с функцией высокой доступности (предварительная версия) требуется дополнительный параметр AuthKey.
    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -off -AuthKey <your auth key>
    ```
4. Чтобы снова включить ее:

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -on -AuthKey <your auth key> 


## Configuration Manager
Once you install the gateway, you can launch Data Management Gateway Configuration Manager in one of the following ways:

1. In the **Search** window, type **Data Management Gateway** to access this utility.
2. Run the executable **ConfigManager.exe** in the folder: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

### Home page
The Home page allows you to do the following actions:

* View status of the gateway (connected to the cloud service etc.).
* **Register** using a key from the portal.
* **Stop** and start the **Data Management Gateway Host service** on the gateway machine.
* **Schedule updates** at a specific time of the days.
* View the date when the gateway was **last updated**.

### Settings page
The Settings page allows you to do the following actions:

* View, change, and export **certificate** used by the gateway. This certificate is used to encrypt data source credentials.
* Change **HTTPS port** for the endpoint. The gateway opens a port for setting the data source credentials.
* **Status** of the endpoint
* View **SSL certificate** is used for SSL communication between portal and the gateway to set credentials for data sources.  

### Diagnostics page
The Diagnostics page allows you to do the following actions:

* Enable verbose **logging**, view logs in event viewer, and send logs to Microsoft if there was a failure.
* **Test connection** to a data source.  

### Help page
The Help page displays the following information:  

* Brief description of the gateway
* Version number
* Links to online help, privacy statement, and license agreement.  

## Monitor gateway in the portal
In the Azure portal, you can view near-real time snapshot of resource utilization (CPU, memory, network(in/out), etc.) on a gateway machine.  

1. In Azure portal, navigate to the home page for your data factory, and click **Linked services** tile. 

    ![Data factory home page](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png) 
2. Select the **gateway** in the **Linked services** page.

    ![Linked services page](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. In the **Gateway** page, you can see the memory and CPU usage of the gateway.

    ![CPU and memory usage of gateway](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png) 
4. Enable **Advanced settings** to see more details such as network usage.
    
    ![Advanced monitoring of gateway](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

The following table provides descriptions of columns in the **Gateway Nodes** list:  

Monitoring Property | Description
:------------------ | :---------- 
Name | Name of the logical gateway and nodes associated with the gateway. Node is an on-premises Windows machine that has the gateway installed on it. For information on having more than one node (up to four nodes) in a single logical gateway, see [Data Management Gateway - high availability and scalability](data-factory-data-management-gateway-high-availability-scalability.md).    
Status | Status of the logical gateway and the gateway nodes. Example: Online/Offline/Limited/etc. For information about these statuses, See [Gateway status](#gateway-status) section. 
Version | Shows the version of the logical gateway and each gateway node. The version of the logical gateway is determined based on version of majority of nodes in the group. If there are nodes with different versions in the logical gateway setup, only the nodes with the same version number as the logical gateway function properly. Others are in the limited mode and need to be manually updated (only in case auto-update fails). 
Available memory | Available memory on a gateway node. This value is a near real-time snapshot. 
CPU utilization | CPU utilization of a gateway node. This value is a near real-time snapshot. 
Networking (In/Out) | Network utilization of a gateway node. This value is a near real-time snapshot. 
Concurrent Jobs (Running/ Limit) | Number of jobs or tasks running on each node. This value is a near real-time snapshot. Limit signifies the maximum concurrent jobs for each node. This value is defined based on the machine size. You can increase the limit to scale up concurrent job execution in advanced scenarios, where CPU/memory/network is under-utilized, but activities are timing out. This capability is also available with a single-node gateway (even when the scalability and availability feature is not enabled).  
Role | There are two types of roles in a multi-node gateway – Dispatcher and worker. All nodes are workers, which means they can all be used to execute jobs. There is only one dispatcher node, which is used to pull tasks/jobs from cloud services and dispatch them to different worker nodes (including itself).

In this page, you see some settings that make more sense when there are two or more nodes (scale out scenario) in the gateway. See [Data Management Gateway - high availability and scalability](data-factory-data-management-gateway-high-availability-scalability.md) for details about setting up a multi-node gateway.

### Gateway status
The following table provides possible statuses of a **gateway node**: 

Status  | Comments/Scenarios
:------- | :------------------
Online | Node connected to Data Factory service.
Offline | Node is offline.
Upgrading | The node is being auto-updated.
Limited | Due to Connectivity issue. May be due to HTTP port 8050 issue, service bus connectivity issue, or credential sync issue. 
Inactive | Node is in a configuration different from the configuration of other majority nodes.<br/><br/> A node can be inactive when it cannot connect to other nodes. 


The following table provides possible statuses of a **logical gateway**. The gateway status depends on statuses of the gateway nodes. 

Status | Comments
:----- | :-------
Needs Registration | No node is yet registered to this logical gateway
Online | Gateway Nodes are online
Offline | No node in online status.
Limited | Not all nodes in this gateway are in healthy state. This status is a warning that some node might be down! <br/><br/>Could be due to credential sync issue on dispatcher/worker node. 

## Scale up gateway
You can configure the number of **concurrent data movement jobs** that can run on a node to scale up the capability of moving data between on-premises and cloud data stores. 

When the available memory and CPU are not utilized well, but the idle capacity is 0, you should scale up by increasing the number of concurrent jobs that can run on a node. You may also want to scale up when activities are timing out because the gateway is overloaded. In the advanced settings of a gateway node, you can increase the maximum capacity for a node. 
  

## Troubleshooting gateway issues
See [Troubleshooting gateway issues](data-factory-troubleshoot-gateway-issues.md) article for information/tips for troubleshooting issues with using the data management gateway.  

## Move gateway from one machine to another
This section provides steps for moving gateway client from one machine to another machine.

1. In the portal, navigate to the **Data Factory home page**, and click the **Linked Services** tile.

    ![Data Gateways Link](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Select your gateway in the **DATA GATEWAYS** section of the **Linked Services** page.

    ![Linked Services page with gateway selected](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. In the **Data gateway** page, click **Download and install data gateway**.

    ![Download gateway link](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. In the **Configure** page, click **Download and install data gateway**, and follow instructions to install the data gateway on the machine.

    ![Configure page](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Keep the **Microsoft Data Management Gateway Configuration Manager** open.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)    
6. In the **Configure** page in the portal, click **Recreate key** on the command bar, and click **Yes** for the warning message. Click **copy button** next to key text that copies the key to the clipboard. The gateway on the old machine stops functioning as soon you recreate the key.  

    ![Recreate key](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Paste the **key** into text box in the **Register Gateway** page of the **Data Management Gateway Configuration Manager** on your machine. (optional) Click **Show gateway key** check box to see the key text.

    ![Copy key and Register](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Click **Register** to register the gateway with the cloud service.
9. On the **Settings** tab, click **Change** to select the same certificate that was used with the old gateway, enter the **password**, and click **Finish**.

   ![Specify Certificate](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   You can export a certificate from the old gateway by doing the following steps: launch Data Management Gateway Configuration Manager on the old machine, switch to the **Certificate** tab, click **Export** button and follow the instructions.
10. After successful registration of the gateway, you should see the **Registration** set to **Registered** and **Status** set to **Started** on the Home page of the Gateway Configuration Manager.

## Encrypting credentials
To encrypt credentials in the Data Factory Editor, do the following steps:

1. Launch web browser on the **gateway machine**, navigate to [Azure portal](http://portal.azure.com). Search for your data factory if needed, open data factory in the **DATA FACTORY** page and then click **Author & Deploy** to launch Data Factory Editor.   
2. Click an existing **linked service** in the tree view to see its JSON definition or create a linked service that requires a data management gateway (for example: SQL Server or Oracle).
3. In the JSON editor, for the **gatewayName** property, enter the name of the gateway.
4. Enter server name for the **Data Source** property in the **connectionString**.
5. Enter database name for the **Initial Catalog** property in the **connectionString**.    
6. Click **Encrypt** button on the command bar that launches the click-once **Credential Manager** application. You should see the **Setting Credentials** dialog box.

    ![Setting credentials dialog](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. In the **Setting Credentials** dialog box, do the following steps:
   1. Select **authentication** that you want the Data Factory service to use to connect to the database.
   2. Enter name of the user who has access to the database for the **USERNAME** setting.
   3. Enter password for the user for the **PASSWORD** setting.  
   4. Click **OK** to encrypt credentials and close the dialog box.
8. You should see a **encryptedCredential** property in the **connectionString** now.

    ```JSON
    {
        "name": "SqlServerLinkedService",
        "properties": {
            "type": "OnPremisesSqlServer",
            "description": "",
            "typeProperties": {
                "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
                "gatewayName": "adftutorialgateway"
            }
        }
    }
    ```
Если для доступа к порталу используется компьютер, отличный от компьютера шлюза, необходимо убедиться в том, что диспетчер учетных данных может подключиться к компьютеру шлюза. Если приложению не удается подключиться к компьютеру шлюза, вы не сможете задать учетные данные для источника данных и проверить подключение к нему.  

Когда вы используете приложение **настройки учетных данных**, портал шифрует учетные данные с помощью сертификата, который указан на вкладке **Сертификат** в **диспетчере конфигурации шлюза** на компьютере шлюза.

Если вам нужен способ шифрования учетных данных на основе API, используйте командлет PowerShell [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx). Командлет шифрует учетные данные с помощью сертификата, который настроен в шлюзе. Зашифрованные учетные данные добавляются в элемент **EncryptedCredential** в **connectionString** в JSON. JSON используется в командлете [New AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) или редакторе фабрики данных.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Для настройки учетных данных при помощи редактора фабрики данных существует еще один способ. Если при помощи редактора создать связанную службу SQL Server и ввести учетные данные в виде обычного текста, то эти учетные данные будут шифроваться с помощью сертификата, который принадлежит службе фабрики данных. Сертификат, который настроен для шлюза, НЕ используется. Хотя этот способ работает быстрее в некоторых случаях, он менее безопасен. Поэтому мы рекомендуем использовать его только для целей разработки и тестирования.

## <a name="powershell-cmdlets"></a>Командлеты PowerShell
В этом разделе описывается, как создать и зарегистрировать шлюз с использованием командлетов Azure PowerShell.

1. Запустите модуль **Azure PowerShell** в режиме администратора.
2. Войдите в учетную запись Azure, выполнив следующую команду и введя свои учетные данные Azure.

    ```PowerShell
    Login-AzureRmAccount
    ```
3. Используйте командлет **New-AzureRmDataFactoryGateway** для создания логического шлюза следующим образом:

    ```PowerShell
    $MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Пример команды и выходных данных**:

    ```
    PS C:\> $MyDMG = New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

    Name              : MyGateway
    Description       : gateway for walkthrough
    Version           :
    Status            : NeedRegistration
    VersionStatus     : None
    CreateTime        : 9/28/2014 10:58:22
    RegisterTime      :
    LastConnectTime   :
    ExpiryTime        :
    ProvisioningState : Succeeded
    Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI
    ```

1. В Azure PowerShell перейдите в папку **C:\Program Files\Microsoft Data Management Gateway\2.0\PowerShellScript\**. Выполните сценарий **RegisterGateway.ps1**, связанный с локальной переменной **$Key**, как показано в следующей команде. Этот сценарий регистрирует агент клиента, установленный на вашем компьютере с логическим шлюзом, созданным ранее.

    ```PowerShell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    С помощью параметра IsRegisterOnRemoteMachine можно зарегистрировать шлюз на удаленном компьютере. Пример:

    ```PowerShell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Вы можете использовать командлет **Get-AzureRmDataFactoryGateway**, чтобы получить список шлюзов своей фабрики данных. Если в поле **Состояние** указано значение **Подключено**, то шлюз готов к использованию.

    ```PowerShell        
    Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
Вы можете удалить шлюз, используя командлет **Remove-AzureRmDataFactoryGateway**, или обновить описание шлюза, используя командлет **Set-AzureRmDataFactoryGateway**. Дополнительную информацию о синтаксисе и другую информацию об этих командлетах см. в "Справочных материалах по командлетам фабрики данных".  

### <a name="list-gateways-using-powershell"></a>Вывод списка шлюзов с помощью PowerShell

```PowerShell
Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Удаление шлюза с помощью PowerShell

```PowerShell
Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```


## <a name="next-steps"></a>Дальнейшие действия
* В разделе [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md). В руководстве создается конвейер, который использует шлюз для перемещения данных из локальной базы данных SQL Server в большой двоичный объект Azure.  

