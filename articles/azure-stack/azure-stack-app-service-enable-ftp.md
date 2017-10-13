---
title: "Включение FTP в службе приложений в Azure Stack | Документация Майкрософт"
description: "Действия по включению FTP в службе приложений в Azure Stack"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: erikje
ms.openlocfilehash: 9cadc57831ac7f7e5d32b10a4a87dab3fac02958
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="enable-ftp-in-app-service-on-azure-stack"></a>Включение FTP в службе приложений в Azure Stack

Если вы успешно развернули службу приложений в Azure Stack Azure и хотите включить публикацию FTP, чтобы клиенты могли передавать файлы приложения и содержимое, необходимо выполнить дополнительные действия.  В будущих выпусках эти действия будут выполняться автоматически.

> [!NOTE]
> Эти шаги должен выполнить администратор службы или предприятия, чтобы настроить службу приложения в поставщике ресурсов Azure Stack.

## <a name="enable-ftp"></a>Включение FTP

1.  Войдите на портал Azure Stack с правами администратора службы.
2.  Откройте **сетевые интерфейсы** и выберите **FTP-NIC** в разделе **Группа ресурсов** - **AppService-LOCAL**. ![Сетевые интерфейсы Azure Stack][1]
3.  Запишите **общедоступный IP-адрес** **сетевой карты FTP**. 
![Сведения о сетевом интерфейсе Azure Stack][2]
4.  Перейдите к **виртуальным машинам** и выберите **FTP0-VM**. ![Виртуальные машины Azure Stack][3]
5.  Откройте сеанс удаленного рабочего стола на виртуальной машине с помощью кнопки **Подключиться**. Войдите в сеанс, используя учетные данные администратора, которые вы указали во время развертывания службы приложений.  
![Сведения о виртуальных машинах Azure Stack][4]
6.  Откройте **диспетчер служб IIS** на виртуальной машине FTP (FTP0-VM).
7.  В разделе **Sites** (Узлы) выберите **Hosting FTP Site** (Размещение узла FTP).
8.  Щелкните **FTP Firewall Support** (Поддержка брандмауэра FTP). ![Диспетчер служб IIS на виртуальной машине FTP0-VM][5]
9.  Введите общедоступный IP-адрес сетевой карты FTP и выберите команду **Apply** (Применить) ![Поддержка брандмауэра FTP в диспетчере IIS][6]

## <a name="validate-the-enabling-of-ftp"></a>Проверка включения FTP

1.  Войдите на портал Azure Stack с правами администратора службы или клиента.
2.  Откройте **Службы приложений** и выберите веб-приложение, мобильное приложение или приложение API, которое вы создали. ![Службы приложений][7]
3.  В сведениях о приложении найдите **имя узла FTP** и **Имя пользователя FTP или развертывания**. ![Сведения о приложении службы приложений][8]
> [!NOTE]
> Если вы не видите запись в разделе **Имя пользователя FTP или развертывания**, необходимо сначала указать учетные данные развертывания в **колонке учетных данных развертывания**.

4.  Откройте проводник, введите имя узла FTP в адресную строку файла, например ftp://ftp.appservice.azurestack.local.
5.  Если появится запрос, введите **учетные данные развертывания**, которые вы записали на шаге 3. Если функция включена, вы увидите список каталогов содержимого приложения службы приложений. ![Список файлов FTP][9]
<!--Image references-->
[1]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interfaces.png
[2]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interface-details.png
[3]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines.png
[4]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines-FTP0-VM.png
[5]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager.png
[6]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager-FTP-Firewall-Support.png
[7]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-services.png
[8]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-service-app-detail.png
[9]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-ftp-file-listing.png
