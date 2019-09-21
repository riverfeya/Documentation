---
ID_PAGE: 22151
PG_TITLE: 13. Raycasts
---

# Raycasts 

Лучи как солнечные лучи.
Используется для проверки столкновения или пересечения в сцене между сетками и тонкой линией.

В предыдущем уроке мы использовали его для выбора сетки мышью (луч проходит от камеры до положения мыши в 3D),
используя функцию scene.pick(scene.pointerX, scene.pointerY) : 
//doc.babylonjs.com/How_To/picking_collisions

Но здесь мы увидим, что мы можем бросить луч из любой точки и в любом направлении. 
Например, в стрелялке от третьего лица : столкновения между нашими пулями и препятствиями.

**Документация классов :**

//doc.babylonjs.com/classes/3.0/ray
Вы должны сначала создать луч.

//doc.babylonjs.com/classes/3.0/scene
Метод scene.pickWithRay() бросает луч в вашу сцену, чтобы выбрать сетку.

//doc.babylonjs.com/classes/3.0/pickinginfo
И получить информацию о попадании.

______

## Определить первый меш, которого коснулся луч ##

 https://www.babylonjs-playground.com/#KNE0O#84

![Raycast simple](/img/how_to/raycast01.jpg)

Во всех наших playgrounds, мы представим, что наш персонаж - это главная бокс в центре.
Он будет непрерывно стрелять лазерными лучами вперед и обнаруживать, в какого противника (другие боксы) попадет.
Таким образом, с помощью мыши вам не нужно щелкать, а двигать, чтобы повернуть прямоугольник с этой функцией тригонометрии mousemovef (l34). 
Луч требует, когда его создали : origin, direction и length. 

Сначала мы устанавливаем *box.isPickable* в false чтобы луч не касался бокса изнутри (l16).
Потому что мы устанавливаем отправную точку (origin) луча в центре бокса.

Самая важная часть - получить хороший вектор направления. (l57) :
		
```
var forward = new BABYLON.Vector3(0,0,1);		
forward = vecToLocal(forward, box);
	
var direction = forward.subtract(origin);
direction = BABYLON.Vector3.Normalize(direction);
```
		
Мы хотим вектор направленный вперед относительно пространства и ориентации бокса. 
Затем, чтобы получить направление, мы вычитаем его из origin, the box position.
Функция vecToLocal предназначена для преобразования позиции с точки зрения меша путем умножения вектора на матрицу меша.

затем, мы создаем луч со всеми данными элементами и length = 100 например (l65) :

```var ray = new BABYLON.Ray(origin, direction, length);```

Наконец, мы получаем точку попадания луча, если он касается сетки (l68) :

```var hit = scene.pickWithRay(ray);```

И если ударить по мешу, мы делаем то, что хотим с информацией о выборе, например, получаем имя меша, положение точки etc...
Здесь мы изменим его размер, потому что это смешнее ! 

---

**Вы не обязаны устанавливать box.isPickable в false**, если вам нужно позже проверить пересечение лучей на этом боксе, например. 
Вы можете установить origin point вектора перед боксом, направление немного дальше и длина, которую вы хотите (l55) :

 https://www.babylonjs-playground.com/#KNE0O#17


-----

## Предикат функии ##

Это фильтр, чтобы выбрать, какие сетки будут выбираться :

 https://www.babylonjs-playground.com/#KNE0O#18

![Raycast predicate](/img/how_to/raycast02.jpg)

Я добавил новый function predicate (l54) :

  ```
  function predicate(mesh){
        if (mesh == box2 || mesh == box){
            return false;
        }
        return true;
    }
```

и в параметре здесь :

```scene.pickWithRay(ray, predicate);```

Аргумент isPickable false становится неактуальным, поэтому мы должны избегать box. Мы также избегаем box2 для тестирования и позволяем остальным (box3 и box4 by default).

И результат, только box3, второй синий позади, и box4 будет расти. Так что работает нормально, как будто box2 был прозрачным для луча!

---

Есть еще один необязательный аргумент метода pickWithRay.
Это логическое **fastCheck** (false по умолчанию).
True вернет первый меш, который пересекается с лучом (в порядке массива мешей), а не ближайший меш к начальной точке луча.


-----

## Triangle predicate ## 

Наичная с Babylon.js v4.0 вы можете определить собственный predicate для фильтра выбранных треугольников для проверки на входящий луч. Predicate будет вызван с 3 вершинами каждого лица и предстоящего луча:

```
scene.pick(scene.pointerX, scene.pointerY, null, false, null, (p0, p1, p2, ray) => {
    var p0p1 = p0.subtract(p1);
    var p2p1 = p2.subtract(p1);
    var normal = BABYLON.Vector3.Cross(p0p1, p2p1);
    return (BABYLON.Vector3.Dot(ray.direction, normal) < 0);
  });
```

В этом примере мы отфильтровываем все треугольники, которые не обращены к камере..

Живой пример: https://www.babylonjs-playground.com/#EES9W5

-----

## Множественный pick ## 

МЫ можем использовать *scene.multiPickWithRay* если мы не хотим, чтобы луч останавливался у первого препятствия : 

 https://www.babylonjs-playground.com/#KNE0O#19

![Raycast multipick](/img/how_to/raycast02.jpg)

Результатом пиккинга будет массив (l68).
Таким образом, мы делаем цикл, чтобы изменить все меши который попали под луч и вы можете видеть, что размер двух синих боксов меняется. 
Это как сильная пуля ! 

---

Другой метод заключается в непосредственном использовании **Ray class**.

Чтобы изменить луч в локальное пространство :

```Ray.Transform(ray, matrix) → Ray```

Проверка пересечения :

```Ray.intersectsMesh(mesh, fastCheck) → PickingInfo```

-----

## Debugging 

Может быть трудно понять, где начинается луч и каково его направление. Чтобы помочь вам отладить, вы можете использовать RayHelper.

Вы можете использовать статическую функцию, чтобы создать и показать один:

```
BABYLON.RayHelper.CreateAndShow(ray, scene, new BABYLON.Color3(1, 1, 0.1));
```

или вы можете использовать более подробную версию:

```
var rayHelper = new BABYLON.RayHelper(ray);
rayHelper.show(scene);
```

Помощник также может быть прикреплен к сетке, чтобы отслеживать его направление.:

```
var localMeshDirection = new BABYLON.Vector3(0, 0, -1);
var localMeshOrigin = new BABYLON.Vector3(0, 0, -.4);
var length = 3;
rayHelper.attachToMesh(box, localMeshDirection, localMeshOrigin, length);
```

 https://www.babylonjs-playground.com/#ZHDBJ#48

## Next step

Часто бывает достаточно иметь 2D-фигуры в вашей сцене и [**sprites**](/babylon101/Sprites) are next.


# Further Reading

## Basic - L1

[Mesh Overview](/features/Shapes)

## External

[Forum Pick with Ray](http://www.html5gamedevs.com/topic/26503-scenepickwithray-blues/)  
[Forum World Local Ray](http://www.html5gamedevs.com/topic/26602-worldlocalray/)  
[Forum Gobal to Local](http://www.html5gamedevs.com/topic/7599-convert-global-coordinates-to-local-coordinates/)



