---
title: Развертывание QuickBooks в Azure RemoteApp | Microsoft Docs
description: Узнайте, как предоставить совместный доступ к QuickBooks с помощью Azure RemoteApp.
services: remoteapp
documentationcenter: ''
author: ericorman
manager: mbaldwin

ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: elizapo

---
# Развертывание QuickBooks в Azure RemoteApp
> [!IMPORTANT]
> Мы выводим удаленное приложение Azure RemoteApp из эксплуатации. Дополнительные сведения см. в [объявлении](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

В этой статье рассказывается, как предоставить совместный доступ к QuickBooks как к приложению в Azure RemoteApp.

Предоставить совместный доступ к QuickBooks 2015 Enterprise в Azure RemoteApp можно либо в гибридной, либо в облачной коллекции. Файл компании должен находиться на виртуальной машине, где располагается сервер базы данных QuickBooks, отдельный от серверов Azure RemoteApp. Нельзя хранить файл компании в образе Azure RemoteApp, иначе данные могут быть утеряны. Размещать файл QuickBooks на внешнем сервере базы данных QuickBooks, доступ к которому осуществляется через стандартную сеть Windows, позволяет только QuickBooks Enterprise.

> [!IMPORTANT]
> Сервер базы данных QuickBooks, на котором хранится файл компании, должен находиться на отдельной виртуальной машине в той же виртуальной сети, что и коллекция Azure RemoteApp.
> 
> 

## Процедура развертывания QuickBooks
1. Создайте виртуальную машину Azure, установите на нее QuickBooks и сервер базы данных QuickBooks и перенесите на эту виртуальную машину файл компании. Проверьте настройки правил брандмауэра.
2. Установите QuickBooks на [пользовательский образ](remoteapp-imageoptions.md) и создайте облачную или гибридную [коллекцию Azure RemoteApp](remoteapp-collections.md) в одной виртуальной сети с сервером базы данных QuickBooks, на котором находится файл компании.
3. [Опубликуйте](remoteapp-publish.md) приложение QuickBooks для пользователей.
4. Запустите клиент QuickBooks из коллекции Azure RemoteApp, через стандартную сеть Windows перейдите к виртуальной машине с сервером базы данных QuickBooks и откройте файл компании.

## Ссылки на документацию
* [Поддерживаемые конфигурации](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top) QuickBooks
* [Варианты развертывания](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/) QuickBooks

Ознакомьтесь также с моей презентацией по Ignite, [Основы управления и администрирования Microsoft Azure RemoteApp](https://channel9.msdn.com/Events/Ignite/2015/BRK3868). Перемотайте на 1:02:45, чтобы перейти сразу к той части, где говорится о QuickBooks.

## Архитектура развертывания
![Развертывание QuickBooks + Azure RemoteApp](./media/remoteapp-quickbooks/ra-quickbooks.png)

<!---HONumber=AcomDC_0817_2016-->