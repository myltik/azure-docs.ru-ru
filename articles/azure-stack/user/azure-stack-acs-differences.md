---
title: Хранилище Azure Stack. Отличия и рекомендации
description: Сведения о различиях между хранилищем Azure Stack и службой хранилища Azure, а также рекомендации по развертыванию Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.reviwer: xiaofmao
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: jeffgilb
ms.openlocfilehash: 165a899dbad0893b3a2bddcfc68c9b5d737e9d3d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Хранилище Azure Stack. Отличия и рекомендации

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Хранилище Azure Stack — это набор облачных служб хранилища в Microsoft Azure Stack. Хранилище Azure Stack предоставляет такие согласованные с Azure компоненты и функции: большой двоичный объект, таблицу, очередь и функцию управления учетными записями.

В этой статье перечислены известные различия между хранилищем Azure Stack и службой хранилища Azure. Здесь также содержатся дополнительные рекомендации, которые необходимо учитывать при развертывании Azure Stack. См. дополнительные сведения о [ключевых различиях](azure-stack-considerations.md) между Azure Stack и Azure.

## <a name="cheat-sheet-storage-differences"></a>Памятка. Различия хранилищ

| Функция | Azure (глобальная) | Azure Stack |
| --- | --- | --- |
|Хранилище файлов|Поддерживаются облачные общие папки с файлами SMB|Еще не поддерживается
|Шифрование службы хранилища Azure для неактивных данных (предварительная версия)|256-битное шифрование AES|128-битное шифрование AES BitLocker
|Тип учетной записи хранения|Учетные записи хранения общего назначения и учетные записи хранения BLOB-объектов Azure|Только общего назначения.
|Варианты репликации|Локально избыточное хранилище, геоизбыточное хранилище, геоизбыточное хранилище с доступом для чтения и хранилище, избыточное в пределах зоны.|Локально избыточное хранилище.
|Хранилище уровня "Премиум"|Полностью поддерживается|Может быть подготовлено, но не имеет ограничений производительности или гарантий.
|Управляемые диски|Поддерживается уровень "Премиум" и "Стандартный"|Пока не поддерживается.
|Имя большого двоичного объекта|1024 символов (2048 байт)|880 символов (1760 байт)
|Максимальный размер блочного BLOB-объекта|4,75 ТБ (100 МБ х 50 000 блоков)|4,75 ТБ (100 МБ x 50 000 блоков) для обновления 1802 или более новой версии. 50 000 x 4 МБ (примерно 195 ГБ) для предыдущих версий.
|Копирование моментального снимка страничного BLOB-объекта|Поддерживается архивация неуправляемых дисков виртуальных машин Azure, подключенных к запущенной виртуальной машине|Пока не поддерживается.
|Копирование добавочных моментальных снимков страничного BLOB-объекта|Поддерживаются страничные BLOB-объекты Azure уровня "Премиум" и "Стандартный"|Пока не поддерживается.
|Уровни хранилища BLOB-объектов|"Горячий", "холодный" и архивный уровни хранилища.|Пока не поддерживается.
Обратимое удаление для хранилища BLOB-объектов|Предварительный просмотр|Пока не поддерживается.
|Максимальный размер страничного BLOB-объекта|8 ТБ|1 TБ
|Размер страницы страничного BLOB-объекта|512 байт|4 КБ
|Ключ секции таблицы и размер ключа строки|1024 символов (2048 байт)|400 символов (800 байт)
|Моментальный снимок большого двоичного объекта|Максимальное число моментальных снимков одного большого двоичного объекта не ограничено.|Максимальное число моментальных снимков одного большого двоичного объекта — 1000.|

### <a name="metrics"></a>Метрики
Кроме того, есть некоторые различия в метриках хранилища:
* Данные транзакций в метриках хранилища не различают внутреннюю или внешнюю пропускную способность сети.
* Данные транзакции в метриках хранилища не включают доступ виртуальных машин к подключенным дискам.

## <a name="api-version"></a>Версия API
Хранилище Azure Stack поддерживает следующие версии:

API-интерфейсы служб хранилища Azure.

Обновление 1802 или более новые версии:
 - [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17);
 - [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31);
 - [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11);
 - [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08);
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05).

Предыдущие версии:
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05).


API управления службами хранилища Azure:

 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN);
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN);
 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN).

## <a name="sdk-versions"></a>Версии пакета SDK

Хранилище Azure Stack поддерживает следующие клиентские библиотеки:

| Клиентская библиотека | Поддерживаемая версия Azure Stack | Ссылка                                                                                                                                                                                                                                                                                                                                     | Спецификация конечной точки       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | От 6.2.0 до 8.7.0          | Пакет NuGet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>Выпуск GitHub:<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | Файл app.config              |
| Java           | От 4.1.0 до 6.1.0           | Пакет Maven<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>Выпуск GitHub:<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | Настройка строки подключения      |
| Node.js        | От 1.1.0 до 2.7.0           | Ссылка на NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Например: выполните команду "npm install azure-storage@2.7.0")<br> <br>Выпуск GitHub:<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | Объявление экземпляра службы |
| C++            | От 2.4.0 до 3.1.0           | Пакет NuGet:<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>Выпуск GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | Настройка строки подключения      |
| PHP            | От 0.15.0 до 1.0.0          | Выпуск GitHub:<br>https://github.com/Azure/azure-storage-php/releases<br> <br>Установка через компоновщик (подробности см. ниже)                                                                                                                                                                                                                  | Настройка строки подключения      |
| Python         | От 0.30.0 до 1.0.0          | Выпуск GitHub:<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | Объявление экземпляра службы |
| Ruby           | От 0.12.1 до 1.0.1          | Пакет RubyGems:<br>Общая версия:<br>https://rubygems.org/gems/azure-storage-common/<br>Большой двоичный объект: https://rubygems.org/gems/azure-storage-blob/<br>Очередь: https://rubygems.org/gems/azure-storage-queue/<br>Таблица: https://rubygems.org/gems/azure-storage-table/<br> <br>Выпуск GitHub:<br>https://github.com/Azure/azure-storage-ruby/releases | Настройка строки подключения      |

## <a name="next-steps"></a>Дополнительная информация

* [Приступая к работе со средствами разработки хранилища Azure Stack](azure-stack-storage-dev.md)
* [Общие сведения о хранилище Azure Stack](azure-stack-storage-overview.md)

