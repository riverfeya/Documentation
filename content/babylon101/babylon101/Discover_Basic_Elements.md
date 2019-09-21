---
ID_PAGE: 22011
PG_TITLE: 02. Set Shapes
---

# Набор фигур 101

Это формы, которые обычно уже имеют имена в повседневном использовании. Это бокс (или кубоид), сфера, цилиндр, конус, правильные многоугольники, плоскость и специальная горизонтальная плоскость, называемой землей. Чуть менее известны, но также включены в набор форм тор, тороидальный узел и многогранник. Вам нужно будет дождаться следующего раздела, чтобы узнать, как создавать фигуры, которые не имеют обычных имен, но формируются с помощью наборов данных и параметров. Это называется _parametric shapes_.

В курсе 101 вы встретите только ограниченное количество заданных форм, начиная с этой страницы с boxes, spheres, planes и ground. Также вы просто будете использовать метод _MeshBuilder_ метод создания формы, а не прежний устаревший метод _Mesh_. Как создать все заданные фигуры, используя любой метод, и преимущества и недостатки обоих можно найти, выполнив [Further Reading](#further_reading).

## метод MeshBuilder

Общая подход для создания набора форм:

var _shape_ = BABYLON.MeshBuilder.Create<i>Shape</i>(name, options, scene);

Параметр options позволяет вам делать такие вещи, как установить размер фигуры и указать, можете ли вы обновить его или нет. Конкретные примеры ниже.

### Box

```javascript
var box = BABYLON.MeshBuilder.CreateBox("box", {}, scene); // default box

var myBox = BABYLON.MeshBuilder.CreateBox("myBox", {height: 5, width: 2, depth: 0.5}, scene);
```

option|value|default value
--------|-----|------------
size|_(number)_ размер каждой стороны бох|1
height|_(number)_ размер по высоте, перезаписывает свойство _size_ |size
width|_(number)_ размер ширины, перезаписывает свойство _size_|size
depth|_(number)_ размер глубины,  перезаписывает свойство _size_|size
faceColors|_(Color4[])_ массив 6 _Color4_, по одному на каждую грань бокса|Color4(1, 1, 1, 1) для каждой стороны
faceUV|_(Vector4[])_ array of 6 _Vector4_, one per box face| UVs(0, 0, 1, 1) для каждой стороны
updatable|_(boolean)_ true если меш updatable|false
sideOrientation|_(number)_ side orientation|DEFAULTSIDE

* [Playground Example of a Cuboid](https://www.babylonjs-playground.com/#3QW4J1#1)

### Sphere

```javascript
var sphere = BABYLON.MeshBuilder.CreateSphere("sphere", {}, scene); //default sphere

var mySphere = BABYLON.MeshBuilder.CreateSphere("mySphere", {diameter: 2, diameterX: 3}, scene);
```

option|value|default value
--------|-----|-------------
segments|_(number)_ количество горизонтальных сегментов|32
diameter|_(number)_ диаметр сферы|1
diameterX|_(number)_ диаметр по оси X , перезаписывает свойство _diameter_ |diameter
diameterY|_(number)_ диаметр по оси Y , перезаписывает свойство _diameter_ |diameter
diameterZ|_(number)_ диаметр по оси Z , перезаписывает свойство _diameter_ |diameter
arc|_(number)_ отношение окружности (latitude) между 0 и 1|1
slice|_(number)_ ratio of the height (longitude) между 0 и 1|1
updatable|_(boolean)_ истина, если сетка обновляемая|false
sideOrientation|_(number)_ side orientation|DEFAULTSIDE

* [Playground Example of an Ellipsoid](https://www.babylonjs-playground.com/#K6M44R)

### Plane

```javascript
var plane = BABYLON.MeshBuilder.CreatePlane("plane", {}, scene); // default plane

var myPlane = BABYLON.MeshBuilder.CreatePlane("myPlane", {width: 5, height: 2}, scene);
```

option|value|default value
--------|-----|-------------
size|_(number)_ side size of the plane|1
width|_(number)_ size of the width|size
height|_(number)_ size of the height|size
updatable|_(boolean)_ истина, если сетка обновляемая|false
sideOrientation|_(number)_ side orientation|DEFAULTSIDE
frontUVs|_(Vector4[])_  array of Vector4, **ONLY WHEN sideOrientation:BABYLON.Mesh.DOUBLESIDE set** | Vector4(0,0, 1,1) 
backUVs|_(Vector4[])_  array of Vector4, **ONLY WHEN sideOrientation:BABYLON.Mesh.DOUBLESIDE set** | Vector4(0,0, 1,1) 
sourcePlane|_(Plane)_ source plane (maths) the mesh will be transformed to|null

* [Playground Example of a Plane](https://www.babylonjs-playground.com/#LXZPJK)

_sourcePlane_ is a unique option for a plane mesh and provides a method to orientate and position it. For now just consider its orientation which on creation is the vector (0, 0, 1). Now should you want the orientation to be the vector (0, -1, 1) then you create a source plane using

```javascript
var sourcePlane = new BABYLON.Plane(0, -1, 1, 0);
sourcePlane.normalize();
```
Это создает математическую плоскость, которая используется в качестве источника ориентации. Четвертый параметр - это расстояние, перемещаемое в направлении вектора ориентации. На этом этапе оставьте как 0. 

* [Playground Example with Source Plane](https://www.babylonjs-playground.com/#LXZPJK#2)

[More Information on Source Plane](LINK)

### Ground

```javascript
var ground = BABYLON.MeshBuilder.CreateGround("ground", {}, scene); //default ground

var myGround = BABYLON.MeshBuilder.CreateGround("myGround", {width: 6, height: 4, subdivisions: 4}, scene);
```

option|value|default value
--------|-----|-------------
width|_(number)_ size of the width|1
height|_(number)_ size of the height|1
updatable|_(boolean)_ истина, если сетка обновляемая|false
subdivisions|_(number)_ number of square subdivisions|1

* [Playground Example of Ground](https://www.babylonjs-playground.com/#MJ6YSM)

Вариант _CreateGround_ это [_CreateGroundFromHeightMap_](/babylon101/Height_map) что позволяет вам формировать волнистую почву, а не плоский план.

## Face Colors or UV

Это доступно только для ограниченного числа мешей, которые имеют различные грани, такие как прямоугольник, но не сфера. Это позволяет придать каждой грани этих мешей индивидуальный цвет или изображение. [Find out about Face Colors and UV](/How_To/CreateBox_Per_Face_Textures_And_Colors).

## Updatable

Если меш имеет параметр updatable установленный в true это означает, что можно изменить данные, связанные с каждой вершиной сетки, и тем самым изменить форму сетки. Подробнее смотри [How to Update Vertices](/How_To/Updating_Vertices.html)


## Side Orientation
  
Опция side orientation используется для изменения способа просмотра каждой стороны меша.  

Есть четыре возможных значения для этой опции :  

  * BABYLON.Mesh.FRONTSIDE,
  * BABYLON.Mesh.BACKSIDE,
  * BABYLON.Mesh.DOUBLESIDE,
  * BABYLON.Mesh.DEFAULT который является значением по умолчанию и равен FRONTSIDE currently.

В следующих примерах вы можете сравнить DEFAULT и DOUBLESIDE перемещая указатель на экране влево и вправо, чтобы вращать плоскости.
* [Playground Example of a DEFAULT Plane](https://www.babylonjs-playground.com/#LXZPJK)
* [Playground Example of a DOUBLESIDE Plane](https://www.babylonjs-playground.com/#LXZPJK#1)


## Front и Back UV

Когда сетка имеет опцию sideOrientation настоящее и установлено DOUBLESIDE тогда на передней и задней панели могут отображаться разные изображения. Подробнее смотри [How To Display Front and Back Images](LINK)

## Coming Soon 

Когда вы создаете сетку, она всегда центрируется в начале координат и по осям. Вы хотите, чтобы дать ему другое положение и вращение. Если вы не можете ждать, пропустите следующий шаг и перейдите к [Как изменить положение и вращение](/babylon101/Position)

# Следующий шаг 

Теперь, когда у вас есть несколько фигур на поясе, пришло время узнать о [Shapes Less Set in Their Ways](/babylon101/Parametric_Shapes)

# Further Reading

## Basic - L1
[Как создать формы из набора форм с помошью MeshBuilder](/How_To/Set_Shapes)  
[как создавать формы устаревшим методом](/How_To/Legacy_Set)  
[Преимущества и недостатки](/features/Shapes#ways-of-creating-a-predefined-mesh)


