---
ID_PAGE: 24847
PG_TITLE: 03. Parametric Shapes
---

# Параметрические фигуры 101

Эти формы или сетки определяются параметрами или математическими данными. Они включают в себя линии, систему линий, ленты, трубы, экструзии, токарные формы и неправильные многоугольники. В курсе 101 вы встретите только линии и будете использовать только метод _MeshBuilder_ , а не устаревший метод _Mesh_. Hблагодаря созданию всех параметрических фигур с использованием любого метода, а преимущества и недостатки обоих можно найти, выполнив [Further Reading](#further_reading). 

## Lines

Lines являются серией отрезков в 3D где конец одного отрезка является началом следующего отрезка. Lines описаны рядом пунктов в 3D пространстве.

Эти три точки (0, 0, 0), (0, 1, 1), (0, 1, 0) сформируют два отрезка.

Эти точки задаются векторами положения, которые в Babylon.js построены как объекты Vector3 объекты и размещаются в порядке в массиве для передачи в метод _CreateLines_.

```javascript
var myPoints = [];

var point1 = new BABYLON.Vector3(0, 0, 0);
myPoints.push(point1);
var point2 = new BABYLON.Vector3(0, 1, 1);
myPoints.push(point2);
var point3 = new BABYLON.Vector3(0, 1, 0);
myPoints.push(point3);

//или возможная альтернатива

var myPoints =[
    new BABYLON.Vector3(0, 0, 0),
    new BABYLON.Vector3(0, 1, 1),
    new BABYLON.Vector3(0, 1, 0)
];

```

Массив точек ДОЛЖЕН быт передан в функцию _CreateLines_ как опция _points_

```javascript
//создание линий
var lines = BABYLON.MeshBuilder.CreateLines("lines", {points: myPoints}, scene);
```
* [Playground Example of Lines](https://www.babylonjs-playground.com/#165IV6#60)
* [Playground Example of a Spiral from Lines](https://www.babylonjs-playground.com/#165IV6#61)

Вы можете использовать пунктирные линии с _CreateDashedLines_ и добавлять количество тире в опции _dashNb_ .

* [Playground Example of a Spiral from Dashed Lines](https://www.babylonjs-playground.com/#165IV6#62)


Опции для создания _CreateLines_

option|value|default value
--------|-----|-------------
points|_(Vector3[])_  массив Vector3, пути линий **REQUIRED** |
updatable|_(boolean)_ true если меш updatable|false
instance|_(LineMesh)_ экземпляр line mesh для updated|null
colors|_(Color4[])_ массив Color4, цвет каждой точки|null
useVertexAlpha|_(boolean)_ false если alpha blending не обязательно (faster)|true

For _CreateDashedLines_ the options are

options|value|default value
--------|-----|-------------
points|_(Vector3[])_  array of Vector3, the path of the line **REQUIRED** |
dashSize|_(number)_  size of the dashes|3
gapSize|_(number)_  size of the gaps|1
dashNb|_(number)_  intended number of dashes|200
updatable|_(boolean)_ true if the mesh is updatable|false
instance|_(LineMesh)_ an instance of a line mesh to be updated|null


### Updatable Option

Линии и пунктирные линии имеют возможность обновления. Если _true_ можно изменить данные, связанные с каждым vertex of the Lines и так изменить путь линий. Для получения дополнительной информации см. [Updating Vertices](/How_To/Updating_Vertices.html).

### Instance Option

Lines также есть опция экземпляра, которая означает, что есть альтернативный способ обновить путь линий, просто передавая новый набор точек. Чтобы это работало, создание оригинальных строк должно иметь обновляемую опцию как _true_ и созданные строки передаются как значение экземпляра опции. Количество точек в массиве ДОЛЖНЫ оставаться теми же самыми. 

Example :
```javascript
//creates lines
var lines = BABYLON.MeshBuilder.CreateLines("lines", {points: myArray, updatable: true}, scene);

// updates the existing instance of lines : no need for the parameter scene here
lines = BABYLON.MeshBuilder.CreateLines("lines", {points: myNewArray, instance: lines});
```
* [Playground Example of a Spiral from Lines](https://www.babylonjs-playground.com/#165IV6#63)

Большинство, но не все параметрические формы имеют опцию _instance_ и таким образом можно обновить свой меш таким образом.


# Next Step

В предыдущем разделе о set shapes, вы видели, что при создании они сидят друг на друге на origin. Это часто имеет место и с параметрическими формами. Так что пришло время перейти к позиционным сеткам, где это необходимо, а также узнать о rotation и scaling. Готовы? Конечно ты! [Position, Rotation and Scaling](/babylon101/Position)

# Further Reading

[How to Create Parametric Shapes with MeshBuilder](/How_To/Parametric_Shapes)  
[How to Create Parametric Shapes Legacy Method](/How_To/Legacy_Param)  
[Advantages and Disadvantages](/features/Shapes#ways-of-creating-a-predefined-mesh)



