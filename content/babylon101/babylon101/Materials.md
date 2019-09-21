---
ID_PAGE: 22051
PG_TITLE: 05. Materials
---

# материалы
Материалы позволяют вам покрывать ваши сетки цветом и текстурой, и им нужен свет, чтобы их можно было увидеть. Один материал может быть использован для покрытия любого количества мешей.


## Реакция на свет
Является ли материал цветом или текстурой, есть разные способы реагирования на свет.

1. Diffuse - основной цвет или текстуру материала при свете;
2. Specular - основной блик, данный материалу светом;
3. Emissive - цвет или фактура материала как бы самосветящиеся;
4. Ambient - цвет или фактуру материала, освещаемого фоновым освещением окружающей среды.

Diffuse и Specular требуют [light source](/features/Lights) для создания.  
Ambient color требует установленного в сцене ambient color, дающего фоновое освещение окружающей среды.
```javascript
scene.ambientColor = new BABYLON.Color3(1, 1, 1);
```

5. Transparency - уровень, который вы можете видеть сквозь материал, можно установить, а для изображений с прозрачными сечениями его можно использовать так, чтобы соответствующие части материала были невидимы. Для этого необходимо установить свойство _alpha_.


## Color
Создать материал, используя

```javascript
var myMaterial = new BABYLON.StandardMaterial("myMaterial", scene);
```
Установите цвет материала, используя один, несколько или все из _diffuseColor_, _specularColor_, _emissiveColor_ и _ambientColor_. Помните, что _ambientColor_ будет применяться только в том случае, если был установлен цвет окружающей сцены.

```javascript
var myMaterial = new BABYLON.StandardMaterial("myMaterial", scene);

myMaterial.diffuseColor = new BABYLON.Color3(1, 0, 1);
myMaterial.specularColor = new BABYLON.Color3(0.5, 0.6, 0.87);
myMaterial.emissiveColor = new BABYLON.Color3(1, 1, 1);
myMaterial.ambientColor = new BABYLON.Color3(0.23, 0.98, 0.53);

mesh.material = myMaterial;
```
### Diffuse Color Example
Чтобы дать представление о том, как рассеивающий цвет материала реагирует на рассеянный свет, в следующем примере игровой площадки показано, как разные цветовые материалы реагируют на рассеянное освещение белого, красного, зеленого и синего цвета.

* [Playground Example - Material Color Reaction to Light Color _direct click or edit to view_](http://www.babylonjs-playground.com/#20OAV9#325)  

This reaction of 

|     |     |
|----|----|
| Yellow Material |  Purple Material |
| Cyan Material | White Material |

на белом, красном, зеленом и синем рассеянных точечных источниках света также можно увидеть на следующем изображении.

![ Spot Light](/img/how_to/Materials/spots1.png)

### Ambient Color Example
In this [playground example](http://www.babylonjs-playground.com/#20OAV9#14) all spheres are lit by the same hemispheric light, with _diffuse_ red and _groundColor_ green. 
The first sphere has no ambient color, the middle has red ambient color defined on its material and the one on the right 
has material with green ambient color. The scene ambient color, which must be present, is white. When a scene ambient color component is set to 0, for example red, then whatever the value for red in the material 
ambient color it will have no effect. 

![Ambient](/img/how_to/Materials/ambient1.png)

### Transparent Color Example
Transparency is achieved by setting a materials _alpha_ property from 0 (invisible) to 1 (opaque).
```javascript
myMaterial.alpha = 0.5;
```
* [Playground Example Transparency](http://www.babylonjs-playground.com/#20OAV9#16)

## Texture
Textures are formed using a saved image.

Create a material using

```javascript
var myMaterial = new BABYLON.StandardMaterial("myMaterial", scene);
```
Установите текстуру материала, используя один, некоторые или все _diffuseTexture_, _specularTexture_, _emissiveTexture_ and _ambientTexture_. 
Notice that _ambientTexture_ is applied without the scene ambient color having been set.

```javascript
var myMaterial = new BABYLON.StandardMaterial("myMaterial", scene);

myMaterial.diffuseTexture = new BABYLON.Texture("PATH TO IMAGE", scene);
myMaterial.specularTexture = new BABYLON.Texture("PATH TO IMAGE", scene);
myMaterial.emissiveTexture = new BABYLON.Texture("PATH TO IMAGE", scene);
myMaterial.ambientTexture = new BABYLON.Texture("PATH TO IMAGE", scene);

mesh.material = myMaterial;
```
Note: When no normals are specified, Babylon's standard material will compute the normals.
### Texture Example
In this [playground example](http://www.babylonjs-playground.com/#20OAV9#15) all spheres are lit by the same hemispheric light, with _diffuse_ red and _groundColor_ green. 
The first sphere has a diffuse texture, the middle an emissive texture and the one on the right 
has material with red diffuse color and an ambient texture.

![Texture](/img/how_to/Materials/texture1.png)

### Transparent Texture Examples
As for colors the transparency is achieved by setting a materials _alpha_ property from 0 (invisible) to 1 (opaque).
```javascript
myMaterial.alpha = 0.5;
```
* [Playground Example Transparency](http://www.babylonjs-playground.com/#20OAV9#17)

In addition the image used for the texture might already have a transparency setting, such as this picture of a dog from wikimedia commons, 
which has a transparent background;

![A dog](/img/how_to/Materials/dog.png)

In this case we set the _hasAlpha_ property of the **texture** to true.
```javascript
myMaterial.diffuseTexture.hasAlpha = true;
```

* [Playground Example Transparent Background](http://www.babylonjs-playground.com/#YDO1F#18)

For the back faces of the cube to be visible through the transparent areas of the front faces we have to deal with back face culling.

## Back Face Culling
This is a method for efficiently drawing the 2D screen rendering of the 3D model. Usually there is no need to draw the back face of a cube, or other object, 
as it will be hidden by the front face. In BabylonJS the default setting is, as you might expect, set to true.

Looking at the images below, when the material property _backFaceCulling_ is true you can see that the transparent areas around the 
dog are still transparent, you can see the background through them. However, you cannot see the images on the back faces as they have been culled (or removed). 
When _backFaceCulling_ is false the back faces are not removed during rendering so they can be seen through the transparent areas of the front faces. 

| Back Face Culling True | Back Face Culling False |
|-----|--------|
| ![BFC True](/img/how_to/Materials/bfc2.png) | ![BFC False](/img/how_to/Materials/bfc1.png) |

* [Playground Example Back Face Culling True](http://www.babylonjs-playground.com/#YDO1F#20)

## WireFrame
Вы можете увидеть сетку в режиме каркаса, используя:
```javascript
materialSphere1.wireframe = true;
 ```
![wireframe](/img/how_to/Materials/04-3.png)


## Next step
Great, your scene is looking better than ever with those materials! Later we will see how to use advanced techniques with materials. But for now, we have to learn [**how to use cameras**](/babylon101/Cameras).

# Further Reading

[Materials Overview](/features/Materials)



