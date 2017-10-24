---
title: "Использование расширений PostgreSQL в базе данных Azure для PostgreSQL | Документация Майкрософт"
description: "Описывается, как расширить функциональные возможности базы данных с помощью расширений в базе данных Azure для PostgreSQL."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 3aa9f90d5ca5ccd33273566795da3df2052ff7c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>Расширения PostgreSQL в базе данных Azure для PostgreSQL
PostgreSQL предоставляет возможность расширить функциональность базы данных с помощью расширений. Расширения позволяют объединить несколько связанных объектов SQL в одном пакете, который затем можно загрузить или удалить из базы данных с помощью одной команды. После загрузки в базу данных расширения могут работать как встроенные функции. Дополнительные сведения о расширениях PostgreSQL см. на странице [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/9.6/static/extend-extensions.html) (Упаковка связанных объектов в расширение).

## <a name="how-to-use-postgresql-extensions"></a>Как использовать расширения PostgreSQL
Чтобы использовать расширения PostgreSQL, их необходимо сначала установить в базу данных. Чтобы установить определенное расширение, выполните команду [CREATE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html), используя инструмент psql. При этом упакованные объекты будут загружены в базу данных.

База данных Azure для PostgreSQL поддерживает подмножество основных расширений, которые перечислены здесь. Поддерживаются только указанные выше расширения. С помощью службы базы данных Azure для PostgreSQL невозможно создать собственное расширение.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Расширения, поддерживаемые базой данных Azure для PostgreSQL
В таблицах ниже приведен список стандартных расширений PostgreSQL, которые в настоящее время поддерживаются базой данных Azure для PostgreSQL. Эти сведения также можно получить, выполнив запрос pg\_available\_extensions.

### <a name="data-types-extensions"></a>Расширения типов данных

> [!div class="mx-tableFixed"]
| **Расширение** | **Описание** |
|---|---|
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Предоставляет тип строки символов без учета регистра. |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Предоставляет тип данных для хранения наборов пар "ключ — значение". |

### <a name="functions-extensions"></a>Расширения функций

> [!div class="mx-tableFixed"]
| **Расширение** | **Описание** |
|---|---|
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Предоставляет несколько функций для определения сходства и расстояния между строками. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Предоставляет функции и операторы для управления массивами целых чисел, не содержащими значений null. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Предоставляет функции шифрования. |
| [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Управляет секционированными таблицами по времени или идентификатору. |
| [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Предоставляет функции и операторы для определения сходства буквенно-цифрового текста на основе сопоставления триграмм. |
| [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Создает глобально уникальные идентификаторы (UUID). |

### <a name="full-text-search-extensions"></a>Расширения для полнотекстового поиска

> [!div class="mx-tableFixed"]
| **Расширение** | **Описание** |
|---|---|
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Словарь для текстового поиска, который удаляет из лексем знаки ударения (диакритические знаки). |

### <a name="index-types-extensions"></a>Расширения типов индекса

> [!div class="mx-tableFixed"]
| **Расширение** | **Описание** |
|---|---|
| [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Предоставляет примеры классов оператора GIN, которые реализуют поведение сбалансированного дерева для определенных типов данных. |
| [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Предоставляет классы оператора индекса GiST, которые реализуют сбалансированное дерево. |

### <a name="language-extensions"></a>Расширения языка

> [!div class="mx-tableFixed"]
| **Расширение** | **Описание** |
|---|---|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Загружаемый процедурный язык PL/pgSQL. |

### <a name="miscellaneous-extensions"></a>Прочие расширения

> [!div class="mx-tableFixed"]
| **Расширение** | **Описание** |
|---|---|
| [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Предоставляет средства для анализа того, что происходит в общем буферном кэше в режиме реального времени. |
| [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Предоставляет способ загрузки реляционных данных в буферный кэш. |
| [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Предоставляет средства для отслеживания статистики выполнения всех инструкций SQL, выполняемых сервером. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Оболочка для внешних данных, используемая для доступа к данным на внешних серверах PostgreSQL. |

### <a name="postgis"></a>PostGIS

> [!div class="mx-tableFixed"]
| **Расширение** | **Описание** |
|---|---|
| [PostGIS](http://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Пространственные и географические объекты для PostgreSQL. |
| address\_standardizer, address\_standardizer\_data\_us | Используются для анализа адреса в составных элементы. Используется для поддержки шага нормализации геокодирования адресов. |
| [grouting](http://pgrouting.org/) | Расширяет геопространственную базу данных PostGIS / PostgreSQL для предоставления функции геопространственной маршрутизации. |

## <a name="next-steps"></a>Дальнейшие действия
Если вы не видите расширение, которое вы хотите использовать, сообщите нам. Вы можете проголосовать за существующие запросы или оставить свои предложения на нашем [форуме отзывов клиентов](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
