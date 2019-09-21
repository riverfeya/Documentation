# Cell material

![Toon material](/img/extensions/materials/toon.png)

## Использование материала клетки

Cell материал можно найти здесь: [https://github.com/BabylonJS/Babylon.js/blob/master/dist/preview release/materialsLibrary/babylon.cellMaterial.js](https://github.com/BabylonJS/Babylon.js/blob/master/dist/preview release/materialsLibrary/babylon.cellMaterial.js)

Демо можно найти здесь:  https://www.babylonjs-playground.com/#36VUUE

## Создание и настройка материала ячейки

Вы можете настроить специальные свойства материала:

```
// Create a new cell material
var cell = new BABYLON.CellMaterial("cell", scene);

// Set up the diffuse texture
cell.diffuseTexture = new BABYLON.Texture("textures/amiga.jpg", scene);

// Set up diffuse color
cell.diffuseColor = new BABYLON.Color3(1, 0.5, 0);

// Set the material to be high level (default value is true)
// The high level can compute until 5 different lighting levels
// If false, only two levels are available
cell.computeHighLevel = true;
```
