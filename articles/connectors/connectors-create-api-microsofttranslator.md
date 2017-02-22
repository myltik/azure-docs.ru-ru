---
title: "Добавление Microsoft Translator в приложения логики | Документация Майкрософт"
description: "Обзор соединителя Microsoft Translator с параметрами интерфейса API REST"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: da782baf-8bf8-4973-8238-e469865f5328
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: add307420b9e16a60b0e57b0ba08188b2509978e


---
# <a name="get-started-with-the-microsoft-translator-connector"></a>Начало работы с соединителем Microsoft Translator
Подключитесь к Microsoft Translator, чтобы переводить тексты, определять языки и выполнять многие другие действия. С помощью Microsoft Translator вы можете: 

* формировать бизнес-процессы на основе данных, получаемых из Microsoft Translator; 
* использовать действия для перевода текста, определения языка и т. д. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, когда в Dropbox создается файл, с помощью Microsoft Translator можно перевести его текст на другой язык.

Сведения о добавлении операции в приложения логики см. в статье [Создание нового приложения логики, подключающего службы SaaS](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Триггеры и действия
Microsoft Translator позволяет выполнять следующие действия. Триггеры отсутствуют.

| триггеры; | Действия |
| --- | --- |
| None |<ul><li>Определение языка</li><li>Преобразование текста в речь</li><li>Перевод текста</li><li>Получение языков</li><li>Получение языков для синтеза речи</li></ul> |

Все соединители поддерживают данные в форматах JSON и XML.

## <a name="create-a-connection-to-microsoft-translator"></a>Создание подключения к Microsoft Translator
> [!INCLUDE [Steps to create a connection to Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]
> 
> 

## <a name="swagger-rest-api-reference"></a>Справочник по REST API Swagger
Относится к версии 1.0.

### <a name="detect-language"></a>Определение языка
Определяет исходный язык заданного текста.  
```GET: /Detect```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Description (Описание) |
| --- | --- | --- | --- | --- | --- |
| запрос |string |Да |запрос |Нет |Текст, язык которого будет определен |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

### <a name="text-to-speech"></a>Преобразование текста в речь
Преобразует заданный текст в речь в виде звукового потока в формате WAVE.  
```GET: /Speak```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Description (Описание) |
| --- | --- | --- | --- | --- | --- |
| запрос |string |Да |запрос |Нет |Преобразуемый текст |
| язык |string |Да |запрос |Нет |Код языка для создания речи (например "en-us") |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

### <a name="translate-text"></a>Перевод текста
Переводит текст на указанный язык с помощью Microsoft Translator.  
```GET: /Translate```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Description (Описание) |
| --- | --- | --- | --- | --- | --- |
| запрос |string |Да |запрос |Нет |Текст для перевода |
| languageTo |string |Да |запрос |Нет |Код целевого языка (например "f") |
| languageFrom |string |Нет |запрос |None |Исходный язык. Если не указан, Microsoft Translator попытается определить язык автоматически (например en). |
| категория |string |Нет |запрос |общие |Категория перевода (по умолчанию — "Общие") |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

### <a name="get-languages"></a>Получение языков
Извлекает все языки, поддерживаемые Microsoft Translator.  
```GET: /TranslatableLanguages```

Для этого вызова параметры отсутствуют. 

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

### <a name="get-speech-languages"></a>Получение языков для синтеза речи
Извлекает языки, доступные для синтеза речи.  
```GET: /SpeakLanguages``` 

Для этого вызова параметры отсутствуют.

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="object-definitions"></a>Определения объектов
#### <a name="language-language-model-for-microsoft-translator-translatable-languages"></a>Language: языковая модель для переводимых языков Microsoft Translator
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| Код |string |Нет |
| Имя |string |Нет |

## <a name="next-steps"></a>Дальнейшие действия
[Создание приложения логики](../logic-apps/logic-apps-create-a-logic-app.md).

Вы можете вернуться к [списку интерфейсов API](apis-list.md).

<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png



<!--HONumber=Jan17_HO3-->


