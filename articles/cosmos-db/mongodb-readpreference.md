---
title: Использование параметров чтения MongoDB в API MongoDB Azure Cosmos DB | Документация Майкрософт
description: Сведения об использовании параметров чтения MongoDB в API MongoDB Azure Cosmos DB.
services: cosmos-db
author: vidhoonv
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: ''
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: viviswan
ms.openlocfilehash: f8c8d068a188052b5e8b66ccb4486a0fc75e2af9
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796671"
---
# <a name="how-to-globally-distribute-reads-using-read-preference-with-the-azure-cosmos-db-mongodb-api"></a>Глобальное распространение операций чтения с применением параметров чтения в API MongoDB в Azure Cosmos DB 

В этой статье описывается, как глобально распространить операции чтения с применением [параметров чтения MongoDB](https://docs.mongodb.com/manual/core/read-preference/) в API MongoDB Azure Cosmos DB. 

## <a name="prerequisites"></a>предварительным требованиям 
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Перейдите к этому [краткому руководству](tutorial-global-distribution-mongodb.md), чтобы ознакомиться с инструкциями по настройке учетной записи Azure Cosmos DB с глобальным распределением на портале Azure и последующему подключению с помощью API MongoDB.

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Откройте окно терминала Git, например Git Bash, и выполните команду `cd`, чтобы перейти в рабочий каталог.  

Затем выполните следующие команды, чтобы клонировать репозиторий с примером. В зависимости от платформы, которую вы хотите использовать, выберите один из следующих примеров репозиториев:

1. [Пример приложения .NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Пример приложения NodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Пример приложения Mongoose](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference).
4. [Пример приложения Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [Пример приложения SpringBoot](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring).


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Выполнение приложения

В зависимости от используемой платформы установите необходимые пакеты и запустите приложение. Чтобы установить зависимости, выполните инструкции в файле сведений, включенном в репозиторий с примерами приложений. Например, в примере приложения NodeJS выполните команды ниже, чтобы установить необходимые пакеты и запустить приложение.

```bash
cd mean
npm install
node index.js
```
Приложение попытается подключиться к источнику MongoDB, и это подключение завершится сбоем из-за недопустимой строки подключения. Выполните инструкции в файле сведений для обновления строки подключения `url`. Кроме того, укажите для параметра `readFromRegion` регион чтения в своей учетной записи Azure Cosmos DB. Следующие инструкции взяты из примера приложения NodeJS:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos DB account's values. 
```

После выполнения этих шагов пример приложения запустится, и мы получим следующие выходные данные:

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>Чтение с использованием режима параметров чтения

MongoDB предоставляет следующие режимы параметров чтения для клиентов:

1. PRIMARY.
2. PRIMARY_PREFERRED.
3. SECONDARY.
4. SECONDARY_PREFERRED.
5. NEAREST.

Подробные сведения о поведении всех режимов параметров чтения MongoDB см. в [этой документации](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior). В Azure Cosmos DB основной регион соответствует региону записи, а дополнительный — региону чтения.

На основе общих сценариев мы рекомендуем использовать следующие параметры:

1. Если требуются **операции чтения с низкой задержкой**, используйте режим параметров чтения **NEAREST**. Этот параметр направляет операции чтения в ближайший доступный регион. Обратите внимание, что если ближайшим регионом является регион записи, то эти операции будут направлены в этот регион.
2. Если требуются **высокая доступность и геораспределение операций чтения** (задержка не является ограничением), используйте режим параметров чтения **SECONDARY_PREFERRED**. Этот параметр направляет операции чтения в доступный регион чтения. Если нет доступных регионов чтения, запросы будут направлены в регион записи.

В следующем фрагменте кода, взятого из примера приложения, показано, как настроить режим параметров чтения "NEAREST" в NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Аналогичным образом в приведенном ниже фрагменте кода показано, как настроить режим параметров чтения "SECONDARY_PREFERRED" в NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Используйте соответствующие репозитории с примерами приложений для других платформ, например [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) и [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Чтение с использованием тегов

Кроме режима параметров чтения, в MongoDB можно использовать теги для направления операций чтения. В Azure Cosmos DB для API MongoDB тег `region` включен по умолчанию как часть ответа `isMaster`:

```json
"tags": {
         "region": "West US"
      }
```

Таким образом, MongoClient может использовать тег `region` с именем региона, чтобы направлять операции чтения в определенные регионы. Для учетных записей Azure Cosmos DB названия регионов можно найти на портале Azure в левой области в разделе **Параметры -> Глобальная репликация данных**. Этот параметр позволяет достичь **изоляции операций чтения** — в случаях, когда клиентскому приложению нужно направлять операции чтения только в определенный регион. Этот параметр идеально подходит для сценариев нерабочего или аналитического типа, которые работают в фоновом режиме и не задействуют критически важные рабочие службы.

В следующем фрагменте кода, взятого из примера приложения, показано, как настроить параметры чтения с тегами в NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Используйте соответствующие репозитории с примерами приложений для других платформ, например [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) и [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

В этой статье вы узнали, как глобально распространить операции чтения с помощью параметров чтения API MongoDB в Azure Cosmos DB.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение дальше, удалите все ресурсы, созданные в ходе работы с этой статьей, на портале Azure, выполнив следующие действия:

1. В меню слева на портале Azure щелкните **Группы ресурсов**, а затем выберите имя созданного ресурса. 
2. На странице группы ресурсов щелкните **Удалить**, в текстовом поле введите имя ресурса для удаления и щелкните **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

* [Перенос данных MongoDB в Azure Cosmos DB](mongodb-migrate.md)
* [Как настроить глобальное распределение Azure Cosmos DB с помощью API MongoDB](tutorial-global-distribution-mongodb.md)
* [Разработка в локальной среде с помощью эмулятора](local-emulator.md)
