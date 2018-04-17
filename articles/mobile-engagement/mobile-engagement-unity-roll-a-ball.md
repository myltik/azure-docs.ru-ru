---
title: Учебник по Unity. Игра Roll a Ball
description: Шаги по созданию классической игры Roll a Ball на платформе Unity, что является необходимым условием для всех руководств по Службам мобильного взаимодействия для Unity.
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 0afd0eca-f74a-43aa-bba8-436a0265c312
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 52d5962645b1408fdba61ec1bf4e4f682b80905b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a id="unity-roll-a-ball"></a>Создание игры Roll a Ball на платформе Unity
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

В этом руководстве рассматриваются основные шаги для слегка измененного [руководства по игре Roll a Ball на Unity](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial). Этот пример игры содержит шарообразный объект "игрок", которым управляет пользователь приложения. Цель игры — "собрать" подбираемые объекты, сталкивая объект игрока с ними. Для работы с примером предполагается знакомство со средой редактора Unity. Если вы столкнетесь с проблемами, обращайтесь к полному учебнику. 

### <a name="setting-up-the-game"></a>Настройка игры
Ниже приведены шаги из [учебника по Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. Откройте **редактор Unity** и щелкните **New** (Создать). 
   
    ![][51] 
2. Заполните поля **Project name** (Имя проекта) & **Location** (Расположение), выберите **3D** и щелкните **Create project** (Создать проект).
   
    ![][52]
3. Сохраните сцену по умолчанию, только что созданную в новом проекте, под именем **MiniGame** в новой папке **\_Scenes**, расположенной в папке **Assets**.
   
    ![][53]
4. Создайте игровое поле, щелкнув **3D Object -> Plane** (Трехмерный объект -> Плоскость), и переименуйте этот объект плоскости в **Ground**.
   
    ![][1]
5. Сбросьте компонент преобразования для этого объекта **Ground** , чтобы оно был в начале координат. 
   
    ![][3]
6. Снимите флажок **Show Grid** (Показать сетку) в меню **Gizmos** (Приспособления) для объекта **Ground**.
   
    ![][4]
7. Обновите компонент **Scale** (Масштаб) для объекта **Ground**, присвоив значения [X = 2, Y = 1, Z = 2]. 
   
    ![][5]
8. Добавьте в проект новый объект (**3D Object -> Sphere** (Трехмерный объект -> Сфера)) и переименуйте этот объект сферы в **Player**. 
   
    ![][6]
9. Выберите объект **Player** и щелкните **Reset Transform** (Сбросить преобразование), как и для объекта плоскости. 
10. Щелкните **Transform -> Position -> Y Coordinate** (Преобразование -> Положение -> Координата по оси Y), чтобы задать координату Y = 0,5 для объекта Player.  
    
    ![][7]
11. Создайте новую папку **Materials** в проекте, где мы создадим материал для раскраски игрока. 
12. Создайте в этой папке новый **материал** с именем **Background**. 
    
    ![][8]
13. Обновите цвет этого материала, обновив его свойство **Albedo** . Можно выбрать RGB-значения [0, 32, 64]. 
    
    ![][9]
14. Перетащите этот материал в представлении сцены, чтобы применить цвет к объекту **Ground** . 
    
    ![][10]
15. Наконец, присвойте значение 60 параметру объекта направленного света (**Transform -> Rotation -> Y** (Преобразование -> Поворот -> Ось Y)) для яркости. 
    
    ![][12]

### <a name="moving-the-player"></a>Перемещение игрока
Ниже приведены шаги из [учебника по Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. Добавьте компонент **RigidBody** в объект **Player**. 
   
    ![][13]
2. Создайте новую папку **Scripts** в проекте. 
3. Щелкните **Add Component -> New Script -> C# Script** (Добавить компонент -> Новый скрипт -> Скрипт C#). Назовите его **PlayerController** и щелкните **Create and Add** (Создать и добавить). Будет создан сценарий, который будет присоединен к объекту Player.  
   
    ![][14]
4. Переместите этот сценарий в папку **Scripts** проекта. 
5. Откройте сценарий для редактирования в привычном редакторе сценариев, измените код сценария с помощью следующего кода, а затем сохраните его. 
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
6. Обратите внимание, что в приведенном выше сценарии используется свойство **Speed** . В редакторе Unity измените это свойство скорости, присвоив ему значение 10.  
   
    ![][15]
7. Щелкните **Play** (Воспроизвести) в редакторе Unity. Теперь вы должны иметь возможность управлять шаром с помощью клавиатуры, он должен вращаться и катиться по поверхности. 

### <a name="moving-the-camera"></a>Перемещение камеры
Ниже представлены шаги из [руководства по Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141), с помощью которых мы привяжем объект **Main Camera** к объекту **Player**. 

1. Присвойте **Transform.Position** значения X = 0, Y = 10,5, Z = -10.  
2. Присвойте **Transform.Rotation** значения X = 45, Y = 0, Z = 0.  
   
    ![][16]
3. Добавьте новый скрипт **CameraController** для **MainCamera** и переместите его в папку Scripts. 
   
    ![][17]
4. Откройте сценарий для редактирования и добавьте в него следующий код.
   
        using UnityEngine;
        using System.Collections;
   
        public class CameraController : MonoBehaviour {
   
            public GameObject player;
   
            private Vector3 offset;
   
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
   
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
5. В среде Unity перетащите переменную Player в слот Player объекта Main Camera, чтобы два связать их. 
   
    ![][18]
6. Теперь, если щелкнуть "Play" (Воспроизвести) в редакторе Unity и повернуть объект шара игрока, вы увидите, что камера следует за ним.  

### <a name="setting-up-the-play-area"></a>Настройка игровой области
Ниже приведены шаги из [учебника по Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141). Мы создадим стены вокруг поверхности, чтобы объект шара игрока не мог выкатиться за пределы игровой области. 

1. Щелкните **Create -> Create Empty -> Game Object** (Создать -> Создать пустой -> Игровой объект), чтобы создать объект, и назовите его **Walls**.
   
    ![][19]
2. В объекте Walls создайте новый объект, щелкнув **3D Object -> Cube** (Трехмерный объект -> Куб), и назовите его West wall. 
   
    ![][20]
3. Обновите параметры объекта West wall, щелкнув **Transform -> Position** (Преобразование -> Положение) и **Transform -> Scale** (Преобразование -> Масштаб). 
   
    ![][21]
4. Скопируйте объект West wall, чтобы создать объект **East wall** с измененным преобразованием положения и масштабом. 
   
    ![][22]
5. Скопируйте объект East wall, чтобы создать объект **North wall** с измененным преобразованием положения и масштабом. 
   
    ![][23]
6. Скопируйте объект North wall, чтобы создать объект **South wall** с измененным преобразованием положения и масштабом. 
   
    ![][24]

### <a name="creating-collectible-objects"></a>Создание подбираемых объектов
Ниже приведены шаги из [учебника по Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141). Мы создадим несколько привлекательных объектов, которые составят набор подбираемых объектов, которые объект шара игрока должен "собирать", сталкиваться с ними. 

1. Создайте новый **трехмерный куб** и назовите этот объект Pickup. 
2. Настройте параметры объекта Pickup, щелкнув **Transform -> Rotation** (Преобразование -> Поворот)  &  **Transform -> Scale** (Преобразование -> Масштаб). 
   
    ![][25]
3. Создайте и присоедините к нему **новый скрипт на C#** с именем **Rotator**. Обязательно поместите этот сценарий в папку Scripts. 
   
    ![][26]
4. Откройте этот сценарий для редактирования и измените его следующим образом. 
   
        using UnityEngine;
        using System.Collections;
   
        public class Rotator : MonoBehaviour {
   
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }
5. Теперь перейдите в режим воспроизведения в редакторе Unity, и ваш Pickup должен начать вращаться вокруг своей оси.
6. Создайте новую папку **Prefabs** 
   
    ![][27]
7. Перетащите объект **Pickup** в папку Prefabs.
   
    ![][28]
8. Создайте новый **пустой игровой объект** с именем **Pickups**. Сбросьте его положение до начала координат, а затем перетащите в него объект Pickup.  
   
    ![][29]
9. Скопируйте объект **Pickup**, разбросав его копии по объекту **Ground** вокруг объекта **Player**, изменяя значения **X и Z для Transform.Position** соответствующим образом. 
   
    ![][30]
10. Создайте **новый материал** **Pickup** и сделайте его красным, обновив **свойство Albedo** точно так же, как мы сделали с объектом Ground. 
    
    ![][31]
11. Примените материал к 4 объектам Pickup.
    
    ![][32]

### <a name="collecting-the-pickup-objects"></a>Сбор объектов Pickup
Ниже приведены шаги из [учебника по Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141). Мы изменим объект Player, чтобы он мог "собирать" объекты Pickup, сталкиваясь с ними. 

1. Откройте для редактирования сценарий **PlayerController** , присоединенный к объекту Player, и измените его следующим образом.  
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour {
   
            public float speed;
   
            private Rigidbody rb;
   
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
   
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
   
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
   
                rb.AddForce (movement * speed);
            }
   
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }
2. Создайте новый **тег** **Pick Up** (он должен соответствовать скрипту).  
   
    ![][33]
   
    ![][34]
3. Примените этот **тег** объекту Prefab Pickup. 
   
    ![][35]
4. Установите флажок **IsTrigger** для объекта Prefab.
   
    ![][36]
5. Добавьте твердое тело в объект Pickup Prefab. Для оптимизации производительности мы заменим статический коллайдер динамическим. 
   
    ![][37]
6. Наконец, установите флажок свойства **IsKinematic** объекта Prefab. 
   
    ![][38]
7. Щелкните **Play** (Воспроизвести) в редакторе Unity, и вы сможете поиграть в игру **Roll a Ball**, перемещая объект игрока. Для этого с помощью клавиш клавиатуры задавайте направление его движения. 

### <a name="updating-the-game-for-mobile-play"></a>Изменение игры для мобильных платформ
Приведенные выше разделы завершают основной учебник по Unity. Теперь мы изменим игру, чтобы сделать ее совместимой с мобильными устройствами. Обратите внимание, что пока что для тестирования игры мы использовали ввод с клавиатуры. Теперь мы изменим это, чтобы игроком можно было управлять движениями телефона, используя акселерометр для ввода. 

Откройте скрипт **PlayerController** для редактирования и измените метод **FixedUpdate**, чтобы использовать перемещение акселерометра для перемещения объекта Player. 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

Этот учебник завершает изучение основ создания игр с помощью Unity, и вы можете развернуть созданную игру на устройстве на свой выбор, чтобы сыграть в нее. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png    
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png    
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png













