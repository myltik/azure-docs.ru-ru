---
title: Предустановка задачи для индексатора мультимедийных данных Azure
description: В этом разделе содержится обзор предустановки задачи для индексатора мультимедийных данных Azure.
services: media-services
documentationcenter: ''
author: Asolanki
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/03/2017
ms.author: adsolank;juliako;
ms.openlocfilehash: ae6c4da189cd6637b4e1fa9274473b62f6664e51
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782863"
---
# <a name="task-preset-for-azure-media-indexer"></a>Предустановка задачи для индексатора мультимедийных данных Azure

Индексатор мультимедийных данных Azure — это обработчик мультимедиа, который используется для выполнения следующих задач: обеспечение возможности поиска по мультимедийным файлам и содержимому, создание дорожек скрытых субтитров и ключевых слов, а также индексирование файлов ресурсов, являющихся частью вашего ресурса.

В этом разделе описывается предустановка задачи, которую необходимо передать в задание индексирования. Полный пример см. в статье [Индексирование файлов мультимедиа с помощью индексатора мультимедийных данных Azure](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>XML-файл конфигурации индексатора мультимедийных данных Azure

В следующей таблице описаны элементы и атрибуты XML-файла конфигурации.

|ИМЯ|Обязательный параметр|ОПИСАНИЕ|
|---|---|---|
|Входные данные|Да|Файлы ресурсов-контейнеров, которые необходимо проиндексировать.<br/>Azure Media Indexer поддерживает следующие форматы файлов мультимедиа: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Можно указать одно или несколько имен файлов в атрибуте **name** или **list** элемента **input**, как показано ниже. Если индексируемый файл ресурса не указан, выбирается основной файл. Если основной файл ресурса-контейнера не задан, индексируется первый файл входного ресурса-контейнера.<br/><br/>Чтобы явно указать имя файла ресурса-контейнера, используйте запись следующего формата:<br/>```<input name="TestFile.wmv" />```<br/><br/>Вы также можете настроить индексирование сразу нескольких файлов ресурсов-контейнеров (до 10). Для этого:<br/>- Создайте текстовый файл (файл манифеста) с расширением LST.<br/>- Внесите в этот файл манифеста список всех имен файлов входного ресурса-контейнера.<br/>- Добавьте (передайте) файл манифеста в ресурс.<br/>- Укажите имя файла манифеста в атрибуте list элемента input.<br/>```<input list="input.lst">```<br/><br/>**Примечание.** Если в файле манифеста указано более 10 файлов, задание индексирования завершится ошибкой с кодом 2006.|
|metadata|false|Метаданные для указанных файлов ресурсов.<br/>```<metadata key="..." value="..." />```<br/><br/>Вы можете указать значения (value) для предопределенных ключей (key). <br/><br/>Сейчас поддерживаются следующие ключи.<br/><br/>**title** и **description** — служат для обновления модели языка с целью повышения точности распознавания речи.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**username** и **password** — применяются для проверки подлинности при загрузке файлов из Интернета по протоколу HTTP или HTTPS.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Значения username (имя пользователя) и password (пароль) применяются ко всем URL-адресам мультимедийного содержимого во входном манифесте.|
|features<br/><br/>Добавлено в версии 1.2. В настоящее время поддерживается только одна функция: распознавание речи (ASR).|false|Функция распознавания речи имеет следующие ключи настройки.<br/><br/>Language:<br/>- естественный язык, распознаваемый в файле мультимедиа;<br/>- English, Spanish (английский, испанский).<br/><br/>CaptionFormats:<br/>- список предпочтительных форматов вывода (если они есть), разделенных точкой с запятой;<br/>- ttml;sami;webvtt.<br/><br/><br/>GenerateAIB:<br/>- Логический флаг. Если он установлен, создается AIB-файл (для использования с SQL Server и клиентским индексатором IFilter). Подробнее этот вопрос раскрыт в записи блога Using AIB Files with Azure Media Indexer and SQL Server(Использование AIB-файлов с индексатором мультимедийных данных Azure и SQL Server).<br/>- True; False.<br/><br/>GenerateKeywords:<br/>- Логический флаг. Если он установлен, создается XML-файл ключевых слов.<br/>- True; False.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Пример XML-файла конфигурации индексатора мультимедийных данных Azure

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="CaptionFormats" value="ttml;sami;webvtt"/>  
        <add key="GenerateAIB" value ="true" />  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>Дополнительная информация

См. статью [Индексирование файлов мультимедиа с помощью индексатора мультимедийных данных Azure](media-services-index-content.md).

