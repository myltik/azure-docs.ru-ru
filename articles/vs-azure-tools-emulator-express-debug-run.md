---
title: "Использование Emulator Express для запуска и отладки облачной службы на локальном компьютере | Документация Майкрософт"
description: "Использование Emulator Express для запуска и отладки облачной службы на локальном компьютере"
services: visual-studio-online
documentationcenter: n/a
author: TomArcher
manager: douge
editor: 
ms.assetid: 73108f98-a552-4817-b7a1-551367b71906
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b09fb0be256fc4cc832822f676b6d1f9de1813cb


---
# <a name="using-emulator-express-to-run-and-debug-a-cloud-service-on-a-local-machine"></a>Использование Emulator Express для запуска и отладки облачной службы на локальном компьютере
Emulator Express позволяет протестировать и отладить облачную службу, не запуская Visual Studio от имени администратора. В зависимости от требований облачной службы параметры проекта можно задать таким образом, чтобы использовался либо Emulator Express, либо полный эмулятор. Дополнительные сведения о полном эмуляторе см. в статье [Запуск приложения Azure в эмуляторе вычислений](storage/storage-use-emulator.md). Emulator Express впервые появился в пакете Azure SDK 2.1, а начиная с пакета Azure SDK 2.3 стал эмулятором по умолчанию.

## <a name="using-emulator-express-in-the-visual-studio-ide"></a>Использование Emulator Express в интегрированной среде разработки Visual Studio
При создании нового проекта в Azure SDK 2.3 или более поздней версии Emulator Express выбирается автоматически. Для существующих проектов, созданных с использованием более ранней версии SDK, выбрать Emulator Express позволяет выполнение описанных ниже действий.

### <a name="to-configure-a-project-to-use-emulator-express"></a>Настройка проекта на использование Emulator Express
1. В контекстном меню проекта Azure выберите **Свойства** и откройте вкладку **Веб**.
2. В разделе **Локальный сервер разработки** установите переключатель **Использовать IIS Express**. Emulator Express несовместим с веб-сервером IIS.
3. В разделе **Эмулятор** установите переключатель **Использовать Emulator Express**.
   
    ![Emulator Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## <a name="launching-emulator-express-at-a-command-prompt"></a>Запуск Emulator Express в командной строке
В командной строке можно запустить экспресс-версию эмулятора вычислений Azure — csrun.exe. Для этого используется параметр /useemulatorexpress.

## <a name="limitations"></a>Ограничения
Прежде чем использовать Emulator Express, обратите внимание на некоторые ограничения.

* Emulator Express несовместим с веб-сервером IIS.
* Облачная служба может содержать несколько ролей, однако каждая роль ограничивается одним экземпляром.
* Невозможен доступ к портам с номерами меньше 1000. Это значит, например, что если ваш поставщик проверки подлинности обычно использует порт с номером меньше 1000, вам придется изменить его на порт с номером больше 1000.
* Все ограничения, связанные с эмулятором вычислений Azure, применяются также к Emulator Express. Например, число экземпляров роли в развернутой службе не может быть больше 50. Ознакомьтесь со статьей [Запуск приложения Azure в эмуляторе вычислений](http://go.microsoft.com/fwlink/p/?LinkId=623050).

## <a name="next-steps"></a>Дальнейшие действия
[Отладка облачных служб](https://msdn.microsoft.com/library/azure/ee405479.aspx)




<!--HONumber=Nov16_HO3-->


