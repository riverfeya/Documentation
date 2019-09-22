---
ID_PAGE: 22131
PG_TITLE: 15. Environment
---
# Environment

Вы прошли долгий путь, узнали о формах, огнях, спрайтах, частицах, материалах. Но в ваших сценах чего-то не хватает: подходящая среда. Это первый из трех последовательных How_To, которые говорят о факторах и эффектах окружения сцены. Мы начнем с простой сцены `clearColor` (background color), затем кратко поговорим о scene `ambientColor`, затем на 6-texture skyboxes, а затем туман, чтобы создать иллюзию глубины ваших сцен.

![Environment](/img/how_to/13.jpg)

_A picture showing Babylon.js fog in action_

## Как я могу это сделать?

Мы поговорим об этом приятном эффекте тумана, в ближайшее время. Во-первых, я хочу познакомить вас с двумя интересными [scene class object](/classes/3.0/Scene):

* `scene.clearColor` - изменяет 'background' color.
* `scene.ambientColor` - изменяет цвет используется в нескольких эффектах, включая окружающее освещение.

Оба они очень полезны и мощны сами по себе.

### Изменение Background color (`scene.clearColor`)

Свойство 'clearColor' на сцене объект является самым элементарным из environment properties/adjustments. Проще говоря, это то, как вы меняете цвет фона сцены. Вот как это делается:

```javascript
scene.clearColor = new BABYLON.Color3(0.5, 0.8, 0.5);
```
Или, может быть, вы хотите использовать один из наших предустановленных цветов и избегать использования ключевое слово `new` :
```javascript
scene.clearColor = BABYLON.Color3.Blue();
```
Этот цвет и свойство не используются ни в каких расчетах для окончательных цветов mesh, materials, textures, или что-нибудь еще. Это просто цвет фона сцены.

### Изменение Ambient color (`scene.ambientColor`)

Напротив, свойство `ambientColor` на сцене объект очень мощный и влиятельный environment property/adjustment. Во-первых, давайте посмотрим на его синтаксис:

```javascript
scene.ambientColor = new BABYLON.Color3(0.3, 0.3, 0.3);
```
Как видите, он установлен в том же формате, что и `clearColor`, но `ambientColor` используется в нескольких расчетах для определения окончательных цветов элементов сцены.&nbsp; В основном, он используется в сочетании с `StandardMaterial.ambientColor` мешей для определения FINAL `ambientColor` материала меша. 

Вы увидите что когда нет `scene.ambientColor`, тогда `StandardMaterial.ambientColor` и `StandardMaterial.ambientTexture` появляются ничего не делая.&nbsp; Задайте некоторое значение `scene.ambientColor` , как в примере выше, и `StandardMaterial.ambientColor`/`StandardMaterial.ambientTexture` станет активным на сетках, где вы применили такие.

По умолчанию , `scene.ambientColor` установлен в `Color3(0, 0, 0)`, что означает, что нет `scene.ambientColor`.

(Пожалуйста, смотрите раздел по ambientColors в нашем [Unleash the Standard Material](https://www.eternalcoding.com/?p=303) tutorial, for more information.)

### Skybox

Чтобы создать идеальную иллюзию прекрасного солнечного неба, мы собираемся создать простую коробку, но со специальной текстурой. Существует два способа создания скайбокса. Давайте начнем с руководства, чтобы понять, как все работает под капотом, а затем мы сможем использовать автоматический.

#### Manual creation
Во-первых, наша коробка, ничего нового, просто обратите внимание на отключение [backface culling](http://en.wikipedia.org/wiki/Back-face_culling):
```javascript
var skybox = BABYLON.Mesh.CreateBox("skyBox", 100.0, scene);
var skyboxMaterial = new BABYLON.StandardMaterial("skyBox", scene);
skyboxMaterial.backFaceCulling = false;
skyboxMaterial.disableLighting = true;
skybox.material = skyboxMaterial;
```

Далее мы устанавливаем свойство `infiniteDistance` . Это заставляет скайбокс следовать позиции нашей камеры.
```javascript
skybox.infiniteDistance = true;
```

Теперь мы должны удалить все отражения света на нашей коробке (солнце не отражается на небе!):
```javascript
skyboxMaterial.disableLighting = true;
```

Затем мы применяем нашу специальную текстуру неба к нему. Эта текстура должна быть подготовлена ​​для скайбокса, в специальном каталоге с именем «скайбокс» в нашем примере.:
```javascript
skyboxMaterial.reflectionTexture = new BABYLON.CubeTexture("textures/skybox", scene);
skyboxMaterial.reflectionTexture.coordinatesMode = BABYLON.Texture.SKYBOX_MODE;
```
(Подробнее об отражении текстур можно найти в нашем [Unleash the Standard Material](https://www.eternalcoding.com/?p=303) tutorial.)

В этой папке `/skybox` , мы должны найти 6 текстур неба, по одной на каждую грань нашего бокса. Каждое изображение должно быть названо в соответствии с face: “skybox_nx.png”, “skybox_ny.png”, “skybox_nz.png”, “skybox_px.png”, “skybox_py.png”, “skybox_pz.png”.

Skybox textures не должно быть текстурой одного неба. Вы можете искать в Интернете скайбоксы и находить здания, холмы, горы, деревья, озера, планеты, звезды, вы называете это (все можно красиво использовать) как часть текстур скайбокса, но некоторые требуют оплаты.

Вы также можете использовать файлы dds, чтобы указать свой скайбокс. Эти специальные файлы могут содержать всю информацию, необходимую для настройки текстуры куба.:

```javascript
skyboxMaterial.reflectionTexture = new BABYLON.CubeTexture("/assets/textures/SpecularHDR.dds", scene);
```

Последнее замечание: если вы хотите, чтобы ваш скайбокс отображался позади всего остального, задайте для &#39;renderGroupId` скайбокса значение&#39; 0 &#39;, а для любого другого объекта визуализируемого объекта renderGroupId больше нуля, например:
```javascript
skybox.renderingGroupId = 0;

// Some other mesh
myMesh.renderingGroupId = 1;
```

Более подробную информацию о группах рендеринга и порядке рендеринга можно найти [here](/resources/Transparency_and_How_Meshes_Are_Rendered).

#### Automatic creation
Теперь, когда мы понимаем, как можно создать скайбокс, давайте перейдем к более простому способу.:

```javascript
var envTexture = new BABYLON.CubeTexture("/assets/textures/SpecularHDR.dds", scene);
scene.createDefaultSkybox(envTexture, true, 1000);
```

* [Playground Example Skybox Helper](https://www.babylonjs-playground.com/#BH23ZD#1)

Check out [scene helpers](/how_to/fast_build) for more information on this and other helpers.

### Fog

Туман довольно продвинутый эффект, но туман в Babylon.js был максимально упрощен. Теперь очень легко добавить туман в свои сцены. Сначала мы определяем режим тумана следующим образом:

```javascript
scene.fogMode = BABYLON.Scene.FOGMODE_EXP;
```

Вот доступные режимы:
- `BABYLON.Scene.FOGMODE_NONE` - default one, fog is deactivated.
- `BABYLON.Scene.FOGMODE_EXP` - плотность тумана следует экспоненциальной функции.
- `BABYLON.Scene.FOGMODE_EXP2` - то же самое, что выше, но быстрее.
- `BABYLON.Scene.FOGMODE_LINEAR` - плотность тумана соответствует линейной функции.

-> Если вы выбираете `EXP`, или `EXP2` режим, тогда вы можете определить опцию density (default is `0.1`):
```javascript
scene.fogDensity = 0.01;
```
-> В противном случае, если вы выберете режим `LINEAR`, вы можете определить, где начинается туман и где он заканчивается.:
```javascript
scene.fogStart = 20.0;
scene.fogEnd = 60.0;
```

Наконец, независимо от режима, вы можете указать цвет тумана (default is `BABYLON.Color3(0.2, 0.2, 0.3)`):
```javascript
scene.fogColor = new BABYLON.Color3(0.9, 0.9, 0.85);
```
Видите, мы говорили, что это легко.

Если вы хотите увидеть и поиграть с игровой площадкой для этого урока, вы можете [**click right here**]( https://www.babylonjs-playground.com/?13).

## Next step
Теперь у вас должна быть красивая сцена, но кроме ваших 3D-моделей, ваш мир довольно плоский, и это позор для вашей сцены. Итак, в нашем следующем уроке по окружающей среде мы собираемся превратить вашу плоскую землю в красивые горы. Чтобы узнать это, перейдите на [here!](/babylon101/Height_Map)

# Further Reading

[Environment Overview](/features/Environment)

[Scene Fast Build](/how_to/fast_build)
