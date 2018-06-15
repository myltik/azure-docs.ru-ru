---
title: Аутентификация пользователей в Data Lake Store с помощью Azure Active Directory и Java | Документация Майкрософт
description: Узнайте, как реализовать аутентификацию пользователей в Data Lake Store с помощью Azure Active Directory и Java
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 633bf87d1e02a1132cfc5cd151b1e58418de8152
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625024"
---
# <a name="end-user-authentication-with-data-lake-store-using-java"></a>Аутентификация пользователей в Data Lake Store с помощью Java
> [!div class="op_single_selector"]
> * [С использованием Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Использование пакета SDK для .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Использование Python](data-lake-store-end-user-authenticate-python.md)
> * [Использование REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

В этой статье описывается, как использовать пакет SDK для Java для аутентификации пользователей с помощью Azure Data Lake Store. См. дополнительные сведения [аутентификации между службами с помощью Data Lake Store и Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="prerequisites"></a>предварительным требованиям
* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Создание собственного приложения Active Directory** Вам нужно выполнить инструкции по [аутентификации пользователей в Data Lake Store с помощью Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). В этом руководстве это средство используется для создания зависимостей проекта. Хотя зависимости можно создать и без использования таких систем, как Maven или Gradle, они существенно упрощают управление ими.

* Интегрированная среда разработки, например [IntelliJ IDEA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) или аналогичная (необязательно).

## <a name="end-user-authentication"></a>Аутентификация пользователей
1. Создайте проект Maven с использованием [архетипа mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) с помощью командной строки или интегрированной среды разработки (IDE). Инструкции по созданию проекта Java с использованием IntelliJ см. [здесь](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Инструкции по созданию проекта с использованием Eclipse см. [здесь](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Добавьте приведенные ниже зависимости в файл Maven **pom.xml**. Добавьте следующий фрагмент перед тегом **\</project >**:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.2.3</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    Первая зависимость предназначена для использования пакета SDK для Data Lake Store (`azure-data-lake-store-sdk`) из репозитория Maven. Вторая зависимость нужна, чтобы указать, какие платформы ведения журналов (`slf4j-nop`) будут использоваться для этого приложения. Пакет SDK для Data Lake Store использует библиотеку [SLF4J](http://www.slf4j.org/), которая позволяет выбрать любую из ряда популярных платформ ведения журналов, таких как Log4j, платформа для Java, Logback и др., или отключить ведение журнала. В этом примере мы отключим ведение журнала, так как используем привязку **slf4j-nop**. Сведения о других вариантах ведения журнала в приложении см. [здесь](http://www.slf4j.org/manual.html#projectDep).

3. Добавьте следующие инструкции импорта в приложение.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;

4. Используйте следующий фрагмент кода в приложении Java, чтобы получить маркер для собственного приложения Active Directory, созданного ранее с помощью `DeviceCodeTokenProvider`. Замените часть **FILL-IN-HERE** фактическими значениями для собственного приложения Azure Active Directory.

        private static String nativeAppId = "FILL-IN-HERE";
            
        AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   

Пакет SDK для Data Lake Store предоставляет удобные методы управления маркерами безопасности, которые нужны для подключения к учетной записи Data Lake Store. Тем не менее пакет SDK не требует использования только этих методов. Можно использовать любые другие средства получения маркера, например [пакет SDK для Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) или пользовательский код.

## <a name="next-steps"></a>Дополнительная информация
В этой статье описывается, как использовать аутентификацию пользователей, чтобы реализовать аутентификацию с помощью Azure Data Lake Store и пакета SDK для Java. Дополнительные сведения об использовании пакета SDK для Java для работы с Azure Data Lake Store см. в следующих статьях.

* [Операции с данными в Data Lake Store с помощью пакета SDK для Java](data-lake-store-get-started-java-sdk.md)


