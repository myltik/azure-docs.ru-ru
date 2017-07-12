---
title: "Приступая к работе с Azure AD версии 2 для Android. Установка | Документация Майкрософт"
description: "Получение маркера доступа для приложения Android и вызов API Microsoft Graph или API, которые требуют маркеры доступа, из конечной точки Azure Active Directory версии 2."
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 9071ab19844898abf7feb7fc0d84120298bb5eaf
ms.contentlocale: ru-ru


---

<a id="set-up-your-project" class="xliff"></a>

## Настройка проекта

> Предпочитаете скачать этот пример проекта Android Studio? [Скачайте проект](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip) и перейдите к [настройке](#create-an-application-express "Configuration Step"), чтобы настроить пример кода перед выполнением.


<a id="create-a-new-project" class="xliff"></a>

### Создание нового проекта 
1.  Откройте Android Studio и перейдите к: `File` > `New` > `New Project`.
2.  Присвойте имя приложению и щелкните `Next`.
3.  Выберите *API версии 21 или более поздней (Android версии 5.0)* и щелкните `Next`.
4.  Выйдите из раздела `Empty Activity`, щелкните `Next`, а затем — `Finish`.


<a id="add-the-microsoft-authentication-library-msal-to-your-project" class="xliff"></a>

### Добавление библиотеки проверки подлинности Майкрософт (MSAL) в проект
1.  Откройте Android Studio и перейдите к: `Gradle Scripts` > `build.gradle (Module: app)`.
2.  Скопируйте следующий код и вставьте его в раздел `Dependencies`.

```ruby  
compile ('com.microsoft.identity.client:msal:0.1.+') {
    exclude group: 'com.android.support', module: 'appcompat-v7'
}
compile 'com.android.volley:volley:1.0.0'
```

<!--start-collapse-->
<a id="about-this-package" class="xliff"></a>

### Об этом пакете

Приведенный выше пакет устанавливает библиотеку проверки подлинности Майкрософт (MSAL). MSAL обрабатывает получение, кэширование и обновление маркеров пользователей, которые используются для доступа к API, защищенному конечной точкой Azure Active Directory версии 2.
<!--end-collapse-->

<a id="create-your-applications-ui" class="xliff"></a>

## Создание пользовательского интерфейса приложения

1.  Откройте `activity_main.xml` (в `res` > `layout`).
2.  Измените макет действия `android.support.constraint.ConstraintLayout` или другой на `LinearLayout`.
3.  Добавьте свойство `android:orientation="vertical"` в узел `LinearLayout`.
4.  Скопируйте и вставьте следующий код в узел `LinearLayout`, заменив текущее содержимое:

```xml
<TextView
    android:text="Welcome, "
    android:textColor="#3f3f3f"
    android:textSize="50px"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginLeft="10dp"
    android:layout_marginTop="15dp"
    android:id="@+id/welcome"
    android:visibility="invisible"/>

<Button
    android:id="@+id/callGraph"
    android:text="Call Microsoft Graph"
    android:textColor="#FFFFFF"
    android:background="#00a1f1"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginTop="200dp"
    android:textAllCaps="false" />

<TextView
    android:text="Getting Graph Data..."
    android:textColor="#3f3f3f"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginLeft="5dp"
    android:id="@+id/graphData"
    android:visibility="invisible"/>

<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="0dip"
    android:layout_weight="1"
    android:gravity="center|bottom"
    android:orientation="vertical" >

    <Button
        android:text="Sign Out"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="15dp"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:textAllCaps="false"
        android:id="@+id/clearCache"
        android:visibility="invisible" />
</LinearLayout>
```


