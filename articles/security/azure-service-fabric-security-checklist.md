---
title: Контрольный список для обеспечения безопасности Azure Service Fabric | Документация Майкрософт
description: В этой статье представлен контрольный список для обеспечения безопасности Azure Service Fabric.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: ef404b106d600f5cb25a46319d75c8978148b466
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895880"
---
# <a name="azure-service-fabric-security-checklist"></a>Контрольный список для обеспечения безопасности Azure Service Fabric
Эта статья содержит удобный контрольный список, который поможет обеспечить защиту среды Azure Service Fabric.

## <a name="introduction"></a>Введение
Azure Service Fabric — это платформа распределенных систем, которая дает возможность не только легко упаковывать и развертывать масштабируемые и надежные микрослужбы, но и управлять ими. Service Fabric также позволяет разрешить значительные трудности, возникающие при разработке облачных приложений и управлении ими. Получая гарантированную масштабируемость, надежность и управляемость, разработчики и администраторы могут сосредоточиться на реализации критически важных и ресурсоемких рабочих нагрузок вместо того, чтобы тратить силы на решение сложных проблем с инфраструктурой.

## <a name="checklist"></a>Контрольный список
Благодаря ему вы не пропустите важные моменты, касающиеся настройки безопасного решение Azure Service Fabric и управления им.


|Категория контрольного списка| ОПИСАНИЕ |
| ------------ | -------- |
|[Управление доступом на основе ролей (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles) | <ul><li>Благодаря контролю доступа администратор кластера может ограничить доступ разных групп пользователей на выполнение определенных операций в кластере, повысив тем самым уровень безопасности кластера.</li><li>Администраторы имеют полный доступ к возможностям управления (включая возможности чтения и записи). </li><li> Пользователи по умолчанию имеют доступ только на чтение для управления (например, при работе с запросами) и возможность разрешения приложений и служб.</li></ul>|
|[Сертификаты X.509 и Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>[Сертификаты](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates), которые используются в кластерах, выполняющих рабочие нагрузки в рабочей среде, следует создать с помощью правильно настроенной службы сертификации Windows Server или получить из утвержденного [центра сертификации](https://en.wikipedia.org/wiki/Certificate_authority).</li><li>Никогда не используйте в рабочей среде какие-либо [временные или тестовые сертификаты](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development), созданные с помощью таких инструментов, как [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Можно использовать [самозаверяющий сертификат](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), но это следует делать только для тестовых кластеров, а не в рабочей среде.</li></ul>|
|[Безопасность кластера](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>Сценарии обеспечения безопасности кластера включают безопасность обмена данными между узлами, безопасность обмена данными между клиентами и узлами, а также [управление доступом на основе ролей (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).</li></ul>|
|[Аутентификация в кластере](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Позволяет аутентифицировать [обмен данными между узлами](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) для федерации кластера. </li></ul>|
|[Аутентификация сервера](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Позволяет аутентифицировать [конечные точки управления кластера](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal) в клиенте управления.</li></ul>|
|[Безопасность приложения](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)| <ul><li>шифрование и расшифровка значений конфигурации приложений;</li><li>   шифрование данных между узлами во время репликации.</li></ul>|
|[Сертификат кластера](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) | <ul><li>Этот сертификат нужен, чтобы защитить связь между узлами кластера.</li><li>    Укажите отпечаток основного сертификата в разделе Thumbprint, а отпечаток дополнительного сертификата — в переменных ThumbprintSecondary.</li></ul>|
|[ServerCertificate](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)| <ul><li>Этот сертификат предоставляется клиенту при попытке подключиться к этому кластеру. Для обновления можно использовать два разных сертификата сервера — основной и дополнительный.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Это набор сертификатов, которые требуется установить на клиентских компьютерах, прошедших аутентификацию. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Укажите общее имя первого сертификата клиента для параметра CertificateCommonName. CertificateIssuerThumbprint — это отпечаток издателя сертификата. </li></ul>|
|ReverseProxyCertificate| <ul><li>Это необязательный сертификат, который можно указать, если вы хотите защитить [обратный прокси-сервер](https://docs.microsoft.com/en-in/azure/service-fabric/service-fabric-reverseproxy). </li></ul>|
|Key Vault| <ul><li>Используется для управления сертификатами кластеров Service Fabric в Azure.  </li></ul>|


## <a name="next-steps"></a>Дополнительная информация
- [Обновление кластера Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade)
- [Управление приложениями Service Fabric в Visual Studio](https://docs.microsoft.com/azure/service-fabric/service-fabric-manage-application-in-visual-studio)
- [Общие сведения о наблюдении за работоспособностью системы в Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction)
