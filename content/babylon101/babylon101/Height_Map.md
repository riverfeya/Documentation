---
ID_PAGE: 22141
PG_TITLE: 16. Height Map
---
## Введение

В этом уроке наша цель - понять карты высот и научиться создавать реалистичную землю.

![HeightMap](/img/how_to/HeightMap/14.png)

_Final result_

## How can I do this ?

* **Introduction**
  Эти горы очень легко генерировать с помощью Babylon.js и только с одной функцией. Но прежде чем мы это сделаем, мы должны создать новый материал, как мы делали много раз прежде:

  ```javascript
  // Создайте материал с нашей текстурой земли.
  var groundMaterial = new BABYLON.StandardMaterial("ground", scene);
  groundMaterial.diffuseTexture = new BABYLON.Texture("Earth__land.jpg", scene);

  // Это показывает, как мы применили бы этот материал к плоскости. 
  // В нашем последнем примере мы заменим это CreateGroundFromHeightMap.
  var groundPlane = BABYLON.Mesh.CreatePlane("groundPlane", 200.0, scene);

  // Когда наша новая сетка будет прочитана, примените наш материал.
  groundPlane.material = groundMaterial;
  ```

![HeightMap2](/img/how_to/HeightMap/14-1.png)

_Our material, a texture, applied to the plane_

* **Explanations of a height map**

  Понимание карт высот является основной целью этого урока. Карта высот - это просто изображение в градациях серого, подобное тому, которое мы будем использовать:

  ![HeightMap3](/img/how_to/HeightMap/worldHeightMap.jpg)

  Это изображение теперь будет использоваться для создания нашей земли, используя различные варианты серого нашей картины. Это изображение является данными высоты для вашей местности. Цвет каждого пикселя интерпретируется как расстояние смещения или «высота» от «пола» вашей сетки. Итак, чем белее пиксель, тем выше будет ваша гора.

  Чтобы помочь вам создать эти карты высоты оттенков серого, вы можете использовать программное обеспечение, такое как “Terragen”, или ”Picogen”.

* **Javascript code**
  Теперь давайте посмотрим на эту мощную функцию с именем “CreateGroundFromHeightMap”:
  ```javascript
  // Создайте материал с нашей текстурой земли.
  var groundMaterial = new BABYLON.StandardMaterial("ground", scene);
  groundMaterial.diffuseTexture = new BABYLON.Texture("Earth__land.jpg", scene);

  // Используйте CreateGroundFromHeightMap, чтобы создать карту высот 200 единиц на 200
  // единиц, с 250 подразделения в каждом из `x` и `z` направлениях, общим числом 62,500 разбиений.
  var ground = BABYLON.Mesh.CreateGroundFromHeightMap("ground", "worldHeightMap.jpg", 200, 200, 250, 0, 10, scene, false, successCallback);

  // Когда наша новая сетка будет создана, применим наш материал.
  ground.material = groundMaterial;
  ```
  
  Здесь много параметров:
  * _Name_
  * _Height map picture url_
  * _Width of mesh_
  * _Height of mesh_
  * _Number of subdivisions_: увеличить сложность этой меша, чтобы улучшить визуальное качество результата

  ![HeightMap4](/img/how_to/HeightMap/14-2.png)

  * _Minimum height_ : Самый низкий уровень меша
  * _Maximum height_ : самый высокий уровень меша
  * _Scene_: the actual scene
  * _Updatable_: указывает, может ли эта сетка динамически обновляться в будущем (Boolean)
  * _successCallback_ : будет вызван после создания карты высот и создания данных вершин. Это функция с сеткой в ​​качестве первой переменной.

  Теперь у нас есть прекрасный трехмерный вид на Землю!

  ![HeightMap4](/img/how_to/HeightMap/14-3.png)

  В моем примере я добавил скайбокс (как мы узнали ранее [here](/How_To/Environment)), и прожектор для имитации солнечной активности.

  Вот еще один пример, показывающий, чего вы можете достичь с помощью карт высот BabylonJS:

  ![HeightMap5](/img/how_to/HeightMap/14-4.png)

* **Tips**
  Когда пользователь манипулирует камерой, может быть неловко, если они могут видеть под землей или если они уменьшают изображение за пределами скайбокса. Итак, чтобы избежать такой ситуации, мы можем ограничить движение камеры:

  ```javascript
  var camerasBorderFunction = function () {
          //Angle
          if (camera.beta < 0.1)
              camera.beta = 0.1;
          else if (camera.beta > (Math.PI / 2) * 0.9)
              camera.beta = (Math.PI / 2) * 0.9;

    //Zoom
          if (camera.radius > 150)
              camera.radius = 150;

          if (camera.radius < 30)
              camera.radius = 30;
      };

      scene.registerBeforeRender(camerasBorderFunction);
  ```

Вы можете быть заинтересованы в посещении [**the playground demo**]( https://www.babylonjs-playground.com/?14) что идет с этим уроком.

## Next step
Отлично сработано! Теперь ваша сцена выглядит хорошо, и вы можете представить множество новых пейзажей! Еще одна важная вещь, которую вам нужно знать,[как создавать тени](/babylon101/Shadows). Тени придадут вашей сцене прекрасный эффект рендеринга, поэтому не забывайте о них!
