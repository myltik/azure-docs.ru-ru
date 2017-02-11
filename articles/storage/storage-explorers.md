---
title: "Клиентские инструменты службы хранилища Azure | Документация Майкрософт"
description: "Список инструментов, позволяющих просматривать данные в службе хранилища Azure и взаимодействовать с ними."
services: storage
documentationcenter: 
author: micurd
manager: jahogg
editor: tysonn
ms.assetid: e4748642-98c4-437e-b0ed-4f9641c2e894
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2016
ms.author: micurd
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 6d2c365a271428a55e484939a2cce5ce5f7348d8


---
# <a name="azure-storage-client-tools"></a>Клиентские инструменты службы хранилища Azure
Пользователям службы хранилища Azure часто требуется просматривать свои данные или взаимодействовать с ними с помощью клиентского инструмента службы хранилища Azure. В приведенных ниже таблицах перечислен ряд инструментов, которые позволяют это сделать. Соответствующее поле содержит "X", если инструмент позволяет перечислить абстракцию данных и (или) предоставить к ней доступ. В таблице также показано, являются ли инструменты бесплатными. "Пробная версия" означает, что доступна бесплатная пробная версия, но полная версия продукта платная. "Да/нет" указывает, что есть как платная, так и бесплатная версия.

Мы предоставляем только набор клиентских инструментов службы хранилища Azure, доступных на данный момент. Развитие этих инструментов и расширение их функций может продолжаться. Если необходимо внести какие-либо исправления или были выпущены обновления, оставьте комментарий для нас. Кроме того, если вы знаете об инструментах, которые должны быть в этой статье, мы с радостью добавим их.

**Клиентские инструменты службы хранилища Microsoft Azure**

<table>
  <tr>
    <th rowspan="2">Клиентский инструмент службы хранилища Azure</th>
    <th rowspan="2">Блочный BLOB-объект</th>
    <th rowspan="2">Страничный BLOB-объект</th>
    <th rowspan="2">Добавление больших двоичных объектов</th>
    <th rowspan="2">Таблицы</th>
    <th rowspan="2">Очереди</th>
    <th rowspan="2">Файлы</th>
    <th rowspan="2">Бесплатно</th>
    <th colspan="4">Платформа</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td> Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Портал Microsoft Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Да</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://storageexplorer.com/">Обозреватель службы хранилища Microsoft Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Да</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Обозреватель сервера Microsoft Visual Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Да</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>

**Сторонние клиентские инструменты службы хранилища Azure**

Мы не проверяли функциональные возможности или качество, заявленные для следующих инструментов сторонних производителей, и их наличие в списке не означает поддержку корпорацией Майкрософт.

<table>
  <tr>
    <th rowspan="2">Клиентский инструмент службы хранилища Azure</th>
    <th rowspan="2">Блочный BLOB-объект</th>
    <th rowspan="2">Страничный BLOB-объект</th>
    <th rowspan="2">Добавление больших двоичных объектов</th>
    <th rowspan="2">Таблицы</th>
    <th rowspan="2">Очереди</th>
    <th rowspan="2">Файлы</th>
    <th rowspan="2">Бесплатно</th>
    <th colspan="4">Платформа</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td> Linux</td>
  </tr>
  <tr>
    <td><a href="http://www.cloudportam.com/">Cloud Portam</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Пробная версия</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata: Azure Management Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Пробная версия</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.cerebrata.com/products/azure-explorer/introduction">Cerabrata: Azure Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>Да</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://azurestorageexplorer.codeplex.com/">Обозреватель хранилищ Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Да</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx">CloudBerry Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>Да/нет</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.gapotchenko.com/cloudcombine">Cloud Combine</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Пробная версия</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://clumsyleaf.com">ClumsyLeaf: AzureXplorer, CloudXplorer, TableXplorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Да</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.gladinet.com/Azure-Storage/index.htm">Gladinet Cloud</a></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>Пробная версия</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://storageexplorer.codeplex.com/">Azure Web Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Да</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://zudio.co/">Zudio</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Пробная версия</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>



<!--HONumber=Nov16_HO3-->


