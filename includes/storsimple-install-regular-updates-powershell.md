<properties
   pageTitle="Установка обычных обновлений через Windows PowerShell для StorSimple"
   description="Описание способов использования Windows PowerShell для StorSimple и функции обновления StorSimple для установки обычных обновлений."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" /> 
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/27/2015"
   ms.author="v-sharos" />

### Установка обычных обновлений с помощью Windows PowerShell для StorSimple

1. Откройте последовательную консоль устройства и выберите вариант 1 **Войти с полным доступом**. Введите пароль. Пароль по умолчанию — *Password1*. 

2. В командной строке выполните следующую команду:

    **Get-HcsUpdateAvailability**

    Вы получите уведомление о том, что обновления доступны, и о том, являются они критическими или некритическими.

3. В командной строке выполните следующую команду:

    **Start-HcsUpdate**

    Начнется процесс обновления.

> [AZURE.IMPORTANT]
>
> - Эта команда применяется только для обычных обновлений. Эта команда выполняется только на одном контроллере, но обновляться будут оба контроллера. 
> - В процессе обновления может выполняться отработка отказа контроллера, но это не повлияет на доступность или работу системы.

<!--HONumber=52-->
