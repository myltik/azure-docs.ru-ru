---
title: "Настройка Emulator Express для отладки приложений облачных служб в Visual Studio | Документация Майкрософт"
description: "Сведения об установке распространяемого компонента C++ для включения Emulator Express в Visual Studio"
services: cloud-services
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 22b20f7a-23f4-4f7f-b536-3bf1e01adcd1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/02/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 165ab7363efaf90eaab41098f71e2f1b846c346e
ms.openlocfilehash: 05d672dcb1335c617bb8d8cae43947bcd5e9ab3d

---
# <a name="use-emulator-express-to-debug-cloud-services-application-in-vs-2017"></a>Использование Emulator Express для отладки приложений облачных служб в VS 2017
В этой статье описывается, как использовать Emulator Express для отладки приложений облачных служб в VS 2017.

## <a name="background-context"></a>Общие сведения
Emulator Express по умолчанию используется для отладки веб-роли и рабочей роли облачных служб в Visual Studio. Этот параметр задается на странице свойств проекта облачных служб.

![Открытие свойств проекта][0]

![Emulator Express выбран как параметр по умолчанию][1]

Для Emulator Express требуется [Распространяемый компонент Visual C++][Распространяемый компонент Visual C++] для Visual Studio. В настоящее время он не устанавливается в рамках рабочей нагрузки Azure. После того как вы нажмете клавишу F5 для отладки приложений облачных служб, Visual Studio предложит установить этот компонент и перейти к отладке.

![Запрос на установку распространяемого компонента C++][2]

Нажмите кнопку "Да", чтобы установить распространяемый компонент C++.

![Установка распространяемого компонента C++][3]

Нажмите клавишу F5 еще раз, чтобы запустить сеансы отладки.

![Запуск отладки][4]

![Отладка выполнена успешно][5]

> Примечание. Установка распространяемого компонента Visual C++ выполняется один раз. Если вы обновили старую версию пакета SDK для Azure и установили Emulator Express, вам не потребуется выполнять установку повторно.
> 
> 

## <a name="manual-workaround"></a>Установка компонента вручную
Вы также можете установить [Распространяемый компонент Visual C++][Распространяемый компонент Visual C++] вручную. Результат будет такой же, как и при установке этого компонента с помощью Visual Studio.

[vcredist_x86.exe][vcredist_x86.exe]

[vcredist_x64.exe][vcredist_x64.exe]

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об использовании эмулятора вычислений Azure для отладки приложений облачных служб в Visual Studio см. в статье [Использование Emulator Express для запуска и отладки облачной службы на локальном компьютере][Использование Emulator Express для запуска и отладки облачной службы на локальном компьютере].

[Распространяемый компонент Visual C++]:https://www.microsoft.com/en-us/download/details.aspx?id=30679
[vcredist_x86.exe]:https://download.microsoft.com/download/1/6/B/16B06F60-3B20-4FF2-B699-5E9B7962F9AE/VSU_4/vcredist_x86.exe
[vcredist_x64.exe]:https://download.microsoft.com/download/1/6/B/16B06F60-3B20-4FF2-B699-5E9B7962F9AE/VSU_4/vcredist_x64.exe
[Использование Emulator Express для запуска и отладки облачной службы на локальном компьютере]:https://azure.microsoft.com/en-us/documentation/articles/vs-azure-tools-emulator-express-debug-run/

[0]: ./media/cloud-services-emulator-express-fix/vs-05.png
[1]: ./media/cloud-services-emulator-express-fix/vs-06.png
[2]: ./media/cloud-services-emulator-express-fix/vs-01.png
[3]: ./media/cloud-services-emulator-express-fix/vs-02.png
[4]: ./media/cloud-services-emulator-express-fix/vs-03.png
[5]: ./media/cloud-services-emulator-express-fix/vs-04.png



<!--HONumber=Nov16_HO3-->


