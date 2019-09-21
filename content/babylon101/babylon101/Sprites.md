---
ID_PAGE: 22082
PG_TITLE: 09. Sprites
---

# Sprites

В этом уроке мы научимся манипулировать Sprites. Sprites это 2D image/animation, и мы будем использовать их для отображения изображения с альфа-каналом. Sprites всегда лицом к камере.

В наше время, sprites часто используются для отображения анимированных персонажей и для частиц, а также для моделирования сложных 3D-объектов, таких как деревья.

Отдельные спрайты собираются вместе в один файл изображения, называемый spritesheet или texture atlas. 

* Uniform spritesheet это тот, где все спрайты имеют одинаковый размер и расположены по порядку в файле. Когда вы читаете термин spritesheet в документации, вы обычно можете предположить, что он относится к uniform spritesheet. Uniform spritesheet контролируется [Sprite Manager](#sprite-manager).
* Упакованный spritesheet это тот, где спрайты могут быть разных размеров и часто упакованы таким образом, чтобы минимизировать общий размер файла. Обычно полный термин _packed spritesheet_ будет использоваться для такого spritesheet. Packed spritesheet управляется [Sprite Packed Manager](#sprite-packed-manager). Это доступно в BJS version 4.1

Для спрайтов использование одного из этих менеджеров обязательно, даже для одного спрайта. Они оптимизируют ресурсы GPU, группируя в одном месте несколько экземпляров спрайта.

![Elements](/img/how_to/Sprites/08.png)

_Final result_


## Sprite Manager

Для спрайтов того же размера вы используете

```javascript
// Создать менеджер спрайтов
var spriteManagerTrees = new BABYLON.SpriteManager("treesManager", "Assets/Palm-arecaceae.png", 2000, 800, scene);
```

При создании диспетчера спрайтов вы должны выбрать несколько параметров:
* Name: имя для этого менеджера.
* URL-адрес 2D-изображения (в большинстве случаев вам нужно использовать формат изображения, содержащий альфа-канал, например .PNG).
* Емкость этого менеджера: максимальное количество экземпляров в этом менеджере (в нашем примере мы можем создать 2000 экземпляров деревьев).
* Размер ячейки, соответствующий размеру вашего изображения, как мы увидим ниже. Обратите внимание, что cellSize может быть числом или объектом из свойств width и height. (Позже вы сможете также указать `spriteManager.cellWidth` и `spriteManager.cellHeight`)
* Реальная сцена, к которой мы добавим этого менеджера.

Чтобы привести другой пример, посмотрите на этот фрагмент:
```javascript
var spriteManagerPlayer = new BABYLON.SpriteManager("playerManager","Assets/Player.png", 2, {width: 64, height: 64}, scene);
```

На этот раз мы хотим только 2 экземпляра, и мы сказали, что размер нашего спрайта составляет 64x64. Вот как выглядит наше изображение:

![Elements](/img/how_to/Sprites/08-1.png)

Каждое изображение спрайта должно содержаться в квадрате размером 64 пикселя, не больше и не меньше.

## Sprite Packed Manager

Для спрайтов разных размеров вам нужен файл изображения и файл JSON, содержащий данные о положении спрайтов в упакованной таблице спрайтов. Файл изображения и файл JSON должны иметь одинаковые имена и находиться в одной папке, например, pack1.png и pack1.json.

Например:
```javascript
var spm = new BABYLON.SpritePackedManager("spm", "pack1.png", 4, scene);
```
Параметры:
* Name: имя для этого менеджера.
* URL-адрес 2D-изображения (в большинстве случаев вам нужно использовать формат изображения, содержащий альфа-канал, например .PNG).
* Емкость этого менеджера: максимальное количество экземпляров в этом менеджере (в нашем примере 4).
* Сцена, к которой мы добавим этот менеджер.

Также можно напрямую ссылаться на существующий объект JSON правильного формата. В этом случае объект JSON передается в качестве дополнительного параметра. Например:

Например:
```javascript
var spm = new BABYLON.SpritePackedManager("spm", "pack1.png", 4, scene, JSONObject);
```

### Packed Format

![Packed Spritesheet](/img/how_to/Sprites/pack1.png);

Формат JSON для вышеуказанного файла основан на формате, созданном с использованием _TexturePacker_ приложение с установленной структурой выходного файла JSON(Hash) и Trim до None и Allow Rotation в Off. Для указанного выше packed spritesheet _TexturePacker_ outputs

```javascript
{   "frames": {
	
		"eye.png": {
			"frame": {"x":0,"y":148,"w":400,"h":400},
			"rotated": false,
			"trimmed": false,
			"spriteSourceSize": {"x":0,"y":0,"w":400,"h":400},
			"sourceSize": {"w":400,"h":400}
		},
		"redman.png": {
			"frame": {"x":0,"y":0,"w":55,"h":97},
			"rotated": false,
			"trimmed": false,
			"spriteSourceSize": {"x":0,"y":0,"w":55,"h":97},
			"sourceSize": {"w":55,"h":97}
			},
		"spot.png": {
			"frame": {"x":199,"y":0,"w":148,"h":148},
			"rotated": false,
			"trimmed": false,
			"spriteSourceSize": {"x":0,"y":0,"w":148,"h":148},
			"sourceSize": {"w":148,"h":148}
		},
		"triangle.png": {
			"frame": {"x":55,"y":0,"w":144,"h":72},
			"rotated": false,
			"trimmed": false,
			"spriteSourceSize": {"x":0,"y":0,"w":144,"h":72},
			"sourceSize": {"w":144,"h":72}
		}
	},
	"meta": {
		"app": "https://www.codeandweb.com/texturepacker",
		"version": "1.0",
		"image": "pack1.png",
		"format": "RGBA8888",
		"size": {"w":400,"h":548},
		"scale": "1",
		"smartupdate": "$TexturePacker:SmartUpdate:c5944b8d86d99a167f95924d4a62d5c3:3ed0ae95f00621580b477fcf2f6edb75:5d0ff2351eb79b7bb8a91bc3358bcff4$"
	}
}
```

SpritePackedManager использует только свойство frame для каждого спрайта, поэтому минимальный формат JSON

```javascript
{   "frames": {
		"eye.png": {
			"frame": {"x":0,"y":148,"w":400,"h":400}
		},
		"redman.png": {
			"frame": {"x":0,"y":0,"w":55,"h":97}
			},
		"spot.png": {
			"frame": {"x":199,"y":0,"w":148,"h":148}
		},
		"triangle.png": {
			"frame": {"x":55,"y":0,"w":144,"h":72}
		}
	}
}
```

## Create A Sprite Instant

Для обоих менеджеров мы можем создать экземпляры спрайта, связанные с менеджером. Создать экземпляр так же просто, как:

```javascript
var sprite = new BABYLON.Sprite("sprite", manager);
```

который использует первый спрайт на листе

С помощью uniform spritesheet и SpriteManager Вы указываете, какой спрайт использовать будет cellIndex, считая от верхнего спрайта слева по порядку вправо и вниз.

Например

```javascript
var sprite = new BABYLON.Sprite("sprite", manager);
sprite.cellIndex = 2;
```

С помощью packed spritesheet и SpritePackedManager Вы можете использовать cellIndex, как указано выше, или cellRef, имя спрайта. 

For example
```javascript
var sprite = new BABYLON.Sprite("sprite", manager);
sprite.cellRef = "spot.png";
```

Вы можете изменить его размер, ориентацию или отражение:
```javascript
sprite.size = 0.3;
sprite.angle = Math.PI/4;
sprite.invertU = -1;
```

Начиная с Babylon.js v2.1, Вы можете определить для спрайта width и height:
```
sprite.width = 0.3;
sprite.height = 0.4;
```

вы можете манипулировать им, как и любыми другими сетками:
```javascript
sprite.position.y = -0.3;
```

## Sprite Animation

Одним из преимуществ спрайтов является анимация. Самый прямой путь заключается в использовании uniform spritesheet и SpriteManager. Вам нужно только загрузить один большой файл изображения, который будет содержать все анимационные изображения, одно рядом с другим. Просто будьте внимательны с размером пикселя, который вы указали в менеджере.
Например, с помощью этой таблицы игроков:

![Elements](/img/how_to/Sprites/08-2.png)

Мы можем использовать это для анимации наших игроков в более чем 40 позициях, в зависимости от ситуации (ходьба, прыжки, ...). 

Если вы хотите начать анимацию, просто вызовите эту функцию:
```javascript
player.playAnimation(0, 43, true, 100);
```
Спрайт игрока будет анимирован с кадра 0 до кадра 43. Третий параметр указывает, будет ли эта анимация цикличной или нет, верна ли петля. Последний параметр - это задержка между кадрами (чем она меньше, тем быстрее анимация).

* [Playground Example Animation of Uniform Sprites**]( https://www.babylonjs-playground.com/?8).

Также можно использовать playAnimation со спрайтами из packed spritesheet. Стоит дважды проверить, что спрайты для анимации являются последовательными и в правильном порядке в файле JSON.

## Packed SpriteSheet Playground Examples

* [Playground Example Direct JSON Object Full TexturePacker Format](https://www.babylonjs-playground.com/#K5KTWA)
* [Playground Example Direct JSON Object Minimum Format](https://www.babylonjs-playground.com/#K5KTWA#1)
* [Playground Example JSON File](https://www.babylonjs-playground.com/#K5KTWA#2)
* [Playground Example JSON File Multiple Sprites](https://www.babylonjs-playground.com/#K5KTWA#4)
* [Playground Example JSON File Animated Slide Show](https://www.babylonjs-playground.com/#K5KTWA#5)

* [Playground Example Multi Pick Sprites](https://www.babylonjs-playground.com/#5GX5DZ)

## Next Step
Узнав о спрайтах, пришло время перейти к использованию их в классическом 3D : [particles](/babylon101/Particles).

# Further Reading

[Mesh Overview](/features/Shapes)
