---
title: Руководство. Создание стека MEAN на виртуальной машине Linux в Azure | Документация Майкрософт
description: В этом руководстве описано, как создать стек MongoDB, Express, AngularJS и Node.js (MEAN) на виртуальной машине Linux в Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/08/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6a9adcd03c5f75b4065273c4ccf657b01bc3d96d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-create-a-mongodb-express-angularjs-and-nodejs-mean-stack-on-a-linux-virtual-machine-in-azure"></a>Руководство. Создание стека MongoDB, Express, AngularJS и Node.js (MEAN) на виртуальной машине Linux в Azure.

В этом руководстве описано, как реализовать стек MongoDB, Express, AngularJS и Node.js (MEAN) на виртуальной машине Linux в Azure. Созданный вами стек MEAN позволяет добавлять, удалять и перечислять книги в базе данных. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины Linux
> * Установка Node.js
> * установка MongoDB и настройка сервера;
> * установка Express и настройка маршрутов на сервер;
> * получение доступа к маршрутам с помощью AngularJS;
> * Выполнение приложения

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0.30 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).


## <a name="create-a-linux-vm"></a>Создание виртуальной машины Linux

Создайте группу ресурсов с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) и создайте виртуальную машину Linux с помощью команды [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере с помощью интерфейса командной строки Azure создается группа ресурсов с именем *myResourceGroupMEAN* в расположении *eastus*. Создается виртуальная машина с именем *myVM* с ключами SSH, если они не существуют в расположении ключей по умолчанию. Чтобы использовать определенный набор ключей, используйте параметр --ssh-key-value.

```azurecli-interactive
az group create --name myResourceGroupMEAN --location eastus
az vm create \
    --resource-group myResourceGroupMEAN \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password 'Azure12345678!' \
    --generate-ssh-keys
az vm open-port --port 3300 --resource-group myResourceGroupMEAN --name myVM
```

После создания виртуальной машины в Azure CLI отображается информация следующего вида. 

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroupMEAN/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.72.77.9",
  "resourceGroup": "myResourceGroupMEAN"
}
```
Запишите значение `publicIpAddress`. Этот адрес используется для доступа к виртуальной машине.

Используйте следующую команду для создания сеанса SSH с виртуальной машиной. Убедитесь, что используется правильный общедоступный IP-адрес. В примере выше виртуальная машина имела следующий IP-адрес: 13.72.77.9.

```bash
ssh azureuser@13.72.77.9
```

## <a name="install-nodejs"></a>Установка Node.js

[Node.js](https://nodejs.org/en/) — это среда выполнения JavaScript на базе ядра JavaScript Chrome V8. В этом руководстве Node.js используется для настройки маршрутов Express и контроллеров AngularJS.

На виртуальной машине с помощью оболочки Bash, открытой с использованием SSH, установите Node.js.

```bash
sudo apt-get install -y nodejs
```

## <a name="install-mongodb-and-set-up-the-server"></a>Установка MongoDB и настройка сервера
[MongoDB](http://www.mongodb.com) хранит данные в гибких документах, похожих на JSON-файлы. Поля в базе данных могут отличаться в разных документах, а структура данных может изменяться с течением времени. В нашем примере приложения мы добавляем записи в MongoDB, которые содержат имя книги, номер ISBN, имя автора и количество страниц. 

1. На виртуальной машине с помощью оболочки Bash, открытой с использованием SSH, установите ключ MongoDB.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
    echo "deb [ arch=amd64 ] http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
    ```

2. Обновите диспетчер пакетов с помощью ключа.
  
    ```bash
    sudo apt-get update
    ```

3. Установите MongoDB.

    ```bash
    sudo apt-get install -y mongodb
    ```

4. Запустите сервер.

    ```bash
    sudo service mongodb start
    ```

5. Также необходимо установить пакет [синтаксического анализа текста](https://www.npmjs.com/package/body-parser-json), который поможет обработать JSON, передаваемый в запросах на сервер.

    Установите диспетчер пакетов npm.

    ```bash
    sudo apt-get install npm
    ```

    Установите пакет синтаксического анализа текста.
    
    ```bash
    sudo npm install body-parser
    ```

6. Создайте папку с именем *Books* и добавьте в нее файл с именем *server.js*, который содержит конфигурацию для веб-сервера.

    ```node.js
    var express = require('express');
    var bodyParser = require('body-parser');
    var app = express();
    app.use(express.static(__dirname + '/public'));
    app.use(bodyParser.json());
    require('./apps/routes')(app);
    app.set('port', 3300);
    app.listen(app.get('port'), function() {
        console.log('Server up: http://localhost:' + app.get('port'));
    });
    ```

## <a name="install-express-and-set-up-routes-to-the-server"></a>Установка Express и настройка маршрутов на сервер

[Express](https://expressjs.com) — это небольшая гибкая платформа веб-приложений Node.js, которая предоставляет функции для веб-приложений и мобильных приложений. В этом руководстве Express используется для передачи сведений о книге в базу данных MongoDB и обратно. [Mongoose](http://mongoosejs.com) представляет собой простое решение на основе схемы для моделирования данных приложения. В этом руководстве Mongoose используется, чтобы предоставить схему книг для базы данных.

1. Установите Express и Mongoose.

    ```bash
    sudo npm install express mongoose
    ```

2. В папке *Books* создайте папку с именем *apps* и добавьте файл с именем *routes.js* с заданными экспресс-маршрутами.

    ```node.js
    var Book = require('./models/book');
    module.exports = function(app) {
      app.get('/book', function(req, res) {
        Book.find({}, function(err, result) {
          if ( err ) throw err;
          res.json(result);
        });
      }); 
      app.post('/book', function(req, res) {
        var book = new Book( {
          name:req.body.name,
          isbn:req.body.isbn,
          author:req.body.author,
          pages:req.body.pages
        });
        book.save(function(err, result) {
          if ( err ) throw err;
          res.json( {
            message:"Successfully added book",
            book:result
          });
        });
      });
      app.delete("/book/:isbn", function(req, res) {
        Book.findOneAndRemove(req.query, function(err, result) {
          if ( err ) throw err;
          res.json( {
            message: "Successfully deleted the book",
            book: result
          });
        });
      });
      var path = require('path');
      app.get('*', function(req, res) {
        res.sendfile(path.join(__dirname + '/public', 'index.html'));
      });
    };
    ```

3. В папке *apps* создайте папку с именем *models* и добавьте файл с именем *book.js* с заданной конфигурацией модели книги.  

    ```node.js
    var mongoose = require('mongoose');
    var dbHost = 'mongodb://localhost:27017/test';
    mongoose.connect(dbHost);
    mongoose.connection;
    mongoose.set('debug', true);
    var bookSchema = mongoose.Schema( {
      name: String,
      isbn: {type: String, index: true},
      author: String,
      pages: Number
    });
    var Book = mongoose.model('Book', bookSchema);
    module.exports = mongoose.model('Book', bookSchema); 
    ```

## <a name="access-the-routes-with-angularjs"></a>получение доступа к маршрутам с помощью AngularJS;

[AngularJS](https://angularjs.org) представляет собой веб-платформу для создания динамических представлений в веб-приложениях. В этом руководстве AngularJS используется для подключения веб-страницы к Express и выполнения действий в базе данных книг.

1. Перейдите обратно в каталог *Books* (`cd ../..`), а затем создайте папку с именем *public* и добавьте файл с именем *script.js* с заданной конфигурацией контроллера.

    ```node.js
    var app = angular.module('myApp', []);
    app.controller('myCtrl', function($scope, $http) {
      $http( {
        method: 'GET',
        url: '/book'
      }).then(function successCallback(response) {
        $scope.books = response.data;
      }, function errorCallback(response) {
        console.log('Error: ' + response);
      });
      $scope.del_book = function(book) {
        $http( {
          method: 'DELETE',
          url: '/book/:isbn',
          params: {'isbn': book.isbn}
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
      $scope.add_book = function() {
        var body = '{ "name": "' + $scope.Name + 
        '", "isbn": "' + $scope.Isbn +
        '", "author": "' + $scope.Author + 
        '", "pages": "' + $scope.Pages + '" }';
        $http({
          method: 'POST',
          url: '/book',
          data: body
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
    });
    ```
    
2. В папке *public* создайте файл с именем *index.html* с заданной веб-страницей.

    ```html
    <!doctype html>
    <html ng-app="myApp" ng-controller="myCtrl">
      <head>
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
        <script src="script.js"></script>
      </head>
      <body>
        <div>
          <table>
            <tr>
              <td>Name:</td> 
              <td><input type="text" ng-model="Name"></td>
            </tr>
            <tr>
              <td>Isbn:</td>
              <td><input type="text" ng-model="Isbn"></td>
            </tr>
            <tr>
              <td>Author:</td> 
              <td><input type="text" ng-model="Author"></td>
            </tr>
            <tr>
              <td>Pages:</td>
              <td><input type="number" ng-model="Pages"></td>
            </tr>
          </table>
          <button ng-click="add_book()">Add</button>
        </div>
        <hr>
        <div>
          <table>
            <tr>
              <th>Name</th>
              <th>Isbn</th>
              <th>Author</th>
              <th>Pages</th>
            </tr>
            <tr ng-repeat="book in books">
              <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
              <td>{{book.name}}</td>
              <td>{{book.isbn}}</td>
              <td>{{book.author}}</td>
              <td>{{book.pages}}</td>
            </tr>
          </table>
        </div>
      </body>
    </html>
    ```

##  <a name="run-the-application"></a>Выполнение приложения

1. Перейдите обратно в каталог *Books* (`cd ..`) и запустите сервер, выполнив следующую команду:

    ```bash
    nodejs server.js
    ```

2. Откройте веб-браузер и перейдите по адресу, предоставленному для виртуальной машины. (например, *http://13.72.77.9:3300*). Вы должны увидеть страницу вроде этой:

    ![Запись о книге](media/tutorial-mean/meanstack-init.png)

3. Введите данные в текстовые поля и нажмите кнопку **Добавить**. Например: 

    ![Добавление записи о книге](media/tutorial-mean/meanstack-add.png)

4. После обновления страницы вы увидите страницу вроде этой:

    ![Список записей о книгах](media/tutorial-mean/meanstack-list.png)

5. Можно нажать кнопку **Удалить**, чтобы удалить запись о книге из базы данных.

## <a name="next-steps"></a>Дополнительная информация

В рамках этого руководства вы создали веб-приложение, отслеживающее записи о книгах, с помощью стека MEAN на виртуальной машине Linux. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины Linux
> * Установка Node.js
> * установка MongoDB и настройка сервера;
> * установка Express и настройка маршрутов на сервер;
> * получение доступа к маршрутам с помощью AngularJS;
> * Выполнение приложения

Перейдите к следующему руководству, чтобы узнать, как защитить веб-серверы с помощью SSL-сертификатов.

> [!div class="nextstepaction"]
> [Secure a web server with SSL certificates on a Linux virtual machine in Azure](tutorial-secure-web-server.md) (Защита веб-сервера на виртуальной машине Linux в облаке Azure с помощью SSL-сертификата)
