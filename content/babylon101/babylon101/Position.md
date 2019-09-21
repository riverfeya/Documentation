---
PG_TITLE: 04 Position, Rotation и Scaling
---

# Position, Rotation and Scaling

Курс 101 рассматривает только настройку меша _position_, _rotation_, и _scaling_. [Further Reading](#further-reading) покажет вам ряд методов для перемещения и вращения меша на заданную величину.

Какими бы ни были используемые методы, им требуется система отсчета, средство для описания положения, поворота или масштабирования и что-то, чтобы помочь визуализировать последствия применения этих методов. Визуализации поможет **Pilot** построенная асимметричная форма.

![The Pilot](/img/how_to/Mesh/pilot.jpg)
The Pilot

## Frames of Reference

Babylon.js использует две системы отсчета, **world axes** и **local axes**. Оrigin **world axes** никогда не меняется.

На всех диаграммах и playgrounds ось X - красная, ось Y - зеленая, а ось Z - синяя. 

Когда меши создаются, их центр помещается в начало **world axes** и их положение всегда находится относительно **world axes**.

 **local axes** перемещаются вместе с мешем. Origin  **local axes** всегда находится в созданном центре меша независимо от его положения. Центры вращения и масштабирования сетки находятся в начале **local axes**, Однако с помощью либо TransformNode или матрицу, чтобы установить [pivot point](/How_To/Pivots) они могут быть изменены на эту точку.

## Vectors

Всеl positions, rotations и scaling даны как 3-х мерные векторы используя _new BABYLON.Vector3(x, y, z)_ и может быть установлен отдельно.

## The Pilot

После его создания позиция пилота находится в начале координат мира с нулем вращения для всех осей, и его масштабирование равно единице, обе **мировые оси** и **локальные оси пилота** совпадают.

![Creation of Pilot](/img/how_to/Mesh//pilot1.jpg)

## Position

Положение места пилота относительно **world axes** используя вектор (x, y, z).  **local axes** перемещаются вместе с pilot.

```javascript
pilot.position = new BABYLON.Vector3(2, 3, 4);
```

или отдельно по компонентам

```javascript
pilot.position.x  =  2;
pilot.position.y  =  3;
pilot.position.z  =  4;
```

Локальные и мировые оси остаются в одной ориентации.

![pilot position](/img/babylon101/pilot1.jpg)

* [Playground Example for Position](https://www.babylonjs-playground.com/#UBWFJT#2)

## Rotation

WARNING: Вращение в трехмерном пространстве всегда сложно. Порядок, в котором вращения применяются к фигуре, изменяет конечную ориентацию фигуры, и вам также необходимо знать, какая система отсчета используется. Существует много различных соглашений для применения поворотов в 3D-моделировании. Для получения более подробной информации об этих соглашениях в Babylon.js см. [Applying Rotations Convention BJS](/resources/rotation_conventions).

В Babylon.js вращения задаются

```javascript
 pilot.rotation = new BABYLON.Vector3(alpha, beta, gamma);
``` 
или

```javascript
pilot.rotation.x  =  alpha; //rotation around x axis
pilot.rotation.y  =  beta;  //rotation around y axis
pilot.rotation.z  =  gamma; //rotation around z axis
```
где alpha, beta, и gamma это углы измеряющиеся в радианах.

ПАУЗА ДЛЯ ОСМЫСЛЕНИЯ: Сразу же, так как три поворота даны относительно трех разных осей, вам нужно спросить - «в каком порядке они применяются к каким системам отсчета и в каком направлении?»

Любое из следующих двух соглашений может рассматриваться как использование вращения в Babylon.js, поскольку оба приводят к одному и тому же результату.

### Соглашения 1 - **Local Axes**

Для **local axes** используя _rotation_ turns меш с центром вращения в начале координат **local axes** в порядке осей y, x, z о **local axes**. Все повороты против часовой стрелки, если смотреть в направлении положительной оси. 

Следующая последовательность изображений показывает начальную стартовую позицию пилота с последующим вращением &pi;/2 вокруг локальной оси Y, then &pi;/2 вокруг локальной оси х и, наконец, вращение &pi;/2 вокруг локальной оси Z.

![Local Rotation](/img/babylon101/pilotL.jpg)

Меньшие оси представляют направление **world axes**.

### Соглашения 2 - **World Axes**

По сравнению с соглашением 1 центр вращения не изменяется, но оси вращения.

For **world axes** using _rotation_ turns the mesh with the centre of rotation at the origin of the **local axes** in the axes order z, x, y about axes parallel to the **world axes**. All rotations are anticlockwise when looking in the direction of the positive axis. 

The following sequence of images shows the initial starting position of the pilot followed by a rotation of &pi;/2 about the world z axis, then &pi;/2 about the world x axis и, наконец, вращение &pi;/2 about the world y axis.

![World Rotation](/img/babylon101/pilotW.jpg)

* [Playground Example - Rotation](http://www.babylonjs-playground.com/#1ZMJQV#2) 

### Summary

Для _rotation_, как бы вы ни думали об этом, результаты всегда одинаковы. Все следующие команды дают одинаковый результат:

```javascript
pilot.rotation = new BABYLON.Vector3(alpha, beta, gamma);

pilot.rotation.x  =  alpha;
pilot.rotation.y  =  beta;
pilot.rotation.z  =  gamma;

pilot.rotation.z  =  gamma;
pilot.rotation.x  =  alpha;
pilot.rotation.y  =  beta;

pilot.rotation.y  =  beta;
pilot.rotation.z  =  gamma;
pilot.rotation.x  =  alpha;
```

* [Playground Example - Positioned, Scaled, and Rotated Boxes](http://www.babylonjs-playground.com/?3)

## Sequencing Rotations

Вопрос теперь в том, что делать, если вы хотите, чтобы последовательность вращений начиналась с одного вокруг оси x, затем вокруг оси y, затем вокруг оси z?

Для **world axes** используйте [rotate method](/features/Position,_Rotation,_Scaling). Для вращений вокруг **local axes**, Babylon.js есть оба метода _rotate_ и _addRotation_ . 

Вы можете связать последовательность вращений, используя _addRotation_. Этот метод предусматривает одно значение вращения вокруг одной оси, последовательность которой может быть применена от первой к последней, как показано в следующем примере.

```javascript 
mesh.addRotation(Math.PI/2, 0, 0).addRotation(0, Math.PI/2, 0).addRotation(0, 0, Math.PI/2);
```

Следующая последовательность изображений показывает результат добавления отдельных вращений один за другим для вышеупомянутого playground, начиная с начальной позиции пилота с последующим вращением &pi;/2 вокруг локальной x axis, затем &pi;/2 вокруг локальной оси Y и, наконец, вращение &pi;/2 вокруг локальной оси Z.

![Added Rotations](/img/babylon101/pilotA.jpg)

The smaller axes represent the direction of the **world axes**.

In general _mesh.addRotation(alpha, beta, gamma)_ needs at least two of _alpha, beta, gamma_ to be 0 where _alpha_ is a rotation about the local x axis, _beta_ вокруг локальной оси Y and _gamma_ вокруг локальной оси Z.

## RotationQuaternions
An alternative to _rotations are [_rotationQuaternions_](/resources/rotation_conventions#quaternions) though they can be tricky to use but can overcome some gimbal lock problems. Using both on a mesh is not possible see [warning](/resources/rotation_conventions#warning)

## Scaling

Scaling along the x, y, and z **local axes** is set using

```javascript
mesh.scaling = new BABYLON.Vector3(scale_x, scale_y, scale_z);
```
 or individually with

 ```javascript
 mesh.scaling.y = 5;
 ```

 The following image shows a unit cube rotated about the z axis and scaled along the local y axis.

 ![scaled](/img/babylon101/scaling1.jpg)


## Next step
Now you know how to create and move objects in a scene, but all your meshes have the same 'skin'. Not for long, if you read our next tutorial about [materials](/babylon101/materials).

# Further Reading

[Rotate and Translate Overview](/features/Position,_Rotation,_Scaling)  


