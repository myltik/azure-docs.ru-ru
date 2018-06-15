---
title: Использование Emulator Express для запуска и отладки облачной службы Azure на локальном компьютере | Документация Майкрософт
description: Использование Emulator Express для запуска и отладки облачной службы на локальном компьютере
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 73108f98-a552-4817-b7a1-551367b71906
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/06/2017
ms.author: mikejo
ms.openlocfilehash: 638aa005321963ce8bdcbb7df4317fc4bdec74ae
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
ms.locfileid: "30292206"
---
# <a name="using-emulator-express-to-run-and-debug-an-azure-cloud-service-on-a-local-machine"></a>Использование Emulator Express для запуска и отладки облачной службы Azure на локальном компьютере
Emulator Express позволяет протестировать и отладить облачную службу, не запуская Visual Studio от имени администратора. В зависимости от требований облачной службы параметры проекта можно задать таким образом, чтобы использовался либо Emulator Express, либо полный эмулятор. Дополнительные сведения о полном эмуляторе см. в статье [Запуск приложения Azure в эмуляторе вычислений](storage/common/storage-use-emulator.md).

## <a name="using-emulator-express-in-visual-studio"></a>Использование Emulator Express в Visual Studio
При создании проекта Azure в пакете Azure SDK 2.3 или более поздней версии Emulator Express используется автоматически. Для существующих проектов, созданных с использованием более ранней версии пакета Azure SDK, выбрать Emulator Express позволяет выполнение описанных ниже действий.

1. Создайте или откройте проект облачной службы Azure в среде Visual Studio.

1. В **обозревателе решений** щелкните проект правой кнопкой мыши и в контекстном меню выберите пункт **Свойства**.

1. На странице свойств проекта выберите вкладку **Веб**.

    ![Свойства проекта облачной службы Azure](./media/vs-azure-tools-emulator-express-debug-run/web-properties.png)

1. В разделе **Локальный сервер Development Server** выберите **Использовать IIS Express**.

1. В разделе **Эмулятор** выберите **Использовать Emulator Express**.
   
1. Чтобы запустить Emulator Express, выполните следующую команду в командной строке: 

    ```
    csrun.exe /useemulatorexpress
    ```

## <a name="emulator-express-limitations"></a>Ограничения Emulator Express
Ниже перечислены известные ограничения Emulator Express. 

- Emulator Express несовместим с веб-сервером IIS.
- Облачная служба может содержать несколько ролей, однако каждая роль ограничивается одним экземпляром.
- Невозможен доступ к портам с номерами меньше 1000. Это значит, что если ваш поставщик проверки подлинности обычно использует порт с номером меньше 1000, то вам придется изменить его на порт с номером больше 1000.
- Все ограничения, связанные с эмулятором вычислений Azure, применяются также к Emulator Express. Например, число экземпляров роли в развернутой службе не может быть больше 50. Дополнительные сведения об эмуляторе вычислений Azure см. в статье [Запуск приложения Azure в эмуляторе вычислений](http://go.microsoft.com/fwlink/p/?LinkId=623050).

## <a name="next-steps"></a>Дополнительная информация
[Отладка облачных служб Azure](https://msdn.microsoft.com/library/azure/ee405479.aspx)
