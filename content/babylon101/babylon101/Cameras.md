---
ID_PAGE: 22061
PG_TITLE: Cameras
---
# Cameras

Из многих камер, доступных в Babylon.js, наиболее часто используются две - универсальная камера, используемая для движения от первого лица, и камера с поворотом дуги, которая является орбитальной камерой. Хотя с появлением WebVR это может измениться.

Для контроля ввода пользователем все камеры должны быть прикреплены к холсту после его создания

```javascript
camera.attachControl(canvas, true);
```
Второй параметр является необязательным и по умолчанию **false**. Ult **false** тогда действия по умолчанию для события холста будут предотвращены. Установите значение true, чтобы разрешить действия по умолчанию для холста.

**Notes**

1. A [Gamepad](/How_To/how_to_use_gamepads) может быть использован контроллер.
2. Для сенсорного управления либо [PEP](https://github.com/jquery/PEP) или [hand.js](https://github.com/Deltakosh/handjs) is needed.

## Универсальная камера

Это было введено в версии 2.3 Babylon.js и управляется клавиатурой, мышью, сенсорным или [gamepad](/How_To/how_to_use_gamepads) в зависимости от используемого устройства ввода, при этом не требуется указывать контроллер. Это расширяет и заменяет [Free Camera](/classes/3.0/freecamera),  [Touch Camera](/classes/3.0/touchcamera) и [Gamepad Camera](classes/3.0/gamepadcamera) которые все еще доступны.

Universal Camera теперь является камерой по умолчанию, используемой Babylon.js, если ничего не указано, и это ваш лучший выбор, если вы хотите иметь FPS-подобный элемент управления в вашей сцене. Все демонстрации на babylonjs.com основаны на этой функции. Подключите контроллер Xbox к своему ПК, и с его помощью вы все равно сможете перемещаться по большинству демонстраций.

Действия по умолчанию:

1. Keyboard - The left and right arrows move the camera left and right, and up and down arrows move it forwards and backwards;

2. Mouse - Rotates the camera about the axes with the camera as origin;

3. Touch - Swipe left and right to move camera left and right, and swipe up and down to move it forward and backwards;

4. [gamepad](/How_To/how_to_use_gamepads) - corresponds to device.

**Note**

- Using keys in the Playground requires you to click inside the rendering area to give it the focus.

### Constructing a Universal Camera

```javascript

// Parameters : name, position, scene
    var camera = new BABYLON.UniversalCamera("UniversalCamera", new BABYLON.Vector3(0, 0, -10), scene);

// Targets the camera to a particular position. In this case the scene origin
    camera.setTarget(BABYLON.Vector3.Zero());

// Attach the camera to the canvas
    camera.attachControl(canvas, true);
```
[A Playground Example of a Universal Camera](http://www.babylonjs-playground.com/#12WBC#68)


## Arc Rotate Camera

Эта камера всегда указывает на заданную позицию цели и может вращаться вокруг этой цели с целью в качестве центра вращения. Им можно управлять с помощью курсоров и мыши или сенсорных событий.

Думайте об этой камере как о той, которая находится на орбите относительно своей цели, или, более точно, образно, как спутник-шпион, вращающийся вокруг Земли. Его положение относительно цели (земли) может быть задано тремя параметрами: _альфа_ (радианы) продольного вращения, _бета_ (радианы) широтного вращения и _радиус_ расстояния от позиции цели. Вот иллюстрация:

![arc rotate camera](/img/how_to/camalphabeta.jpg)

Установка _beta_ в 0 или PI может по техническим причинам вызвать проблемы и в этой ситуации _beta_ is offset by 0.1 radians (about 0.6 degrees).

Both _alpha_ and _beta_ increase in a clockwise direction.

The position of the camera can also be set from a vector which will override any current value for _alpha_, _beta_ and _radius_.
This can be much easier than calculating the required angles.

Whether using the keyboard, mouse or touch swipes left right directions change _alpha_ and up down directions change _beta_.

### Constructing an Arc Rotate Camera

```javascript

// Parameters: alpha, beta, radius, target position, scene
	var camera = new BABYLON.ArcRotateCamera("Camera", 0, 0, 10, new BABYLON.Vector3(0, 0, 0), scene);

// Positions the camera overwriting alpha, beta, radius
    camera.setPosition(new BABYLON.Vector3(0, 0, 20));

// This attaches the camera to the canvas
    camera.attachControl(canvas, true);
```
[A Playground Example of an Arc Rotate Camera](http://www.babylonjs-playground.com/#12WBC#69)

Panning with an ArcRotateCamera is also possible by using CTRL + MouseLeftClick, the default action. You can specify to use MouseRightClick instead, by setting _useCtrlForPanning_ to false in the _attachControl_ call :

```javascript
   camera.attachControl(canvas, noPreventDefault, useCtrlForPanning);
```

If required you can also totally deactivate panning by setting :

```javascript
   scene.activeCamera.panningSensibility = 0;
```

## FollowCamera

Follow Camera делает то, что исходит из ее названия. Дайте ему меш в качестве цели, и из любой позиции, в которой он находится в данный момент, он переместится в позицию цели, из которой можно увидеть цель. Когда цель движется, Follow Camera.

Начальная позиция Follow Camera устанавливается при его создании, тогда позиция цели задается тремя параметрами:

1. расстояние от цели - camera.radius;

2. высота над целью - camera.heightOffset;

3. угол в градусах вокруг цели в x y плоскости.

Скорость, с которой камера перемещается в положение цели, задается через ее ускорение (camera.cameraAcceleration) до максимальной скорости (camera.maxCameraSpeed).

### Constructing a Follow Camera

```javascript

// Parameters: name, position, scene
var camera = new BABYLON.FollowCamera("FollowCam", new BABYLON.Vector3(0, 10, -10), scene);

// Расстояние до цели камеры от цели
camera.radius = 30;

// Высота цели камеры над местным происхождением (центром) цели
camera.heightOffset = 10;

// Вращение цели камеры вокруг локального начала (центра) цели в плоскости xy
camera.rotationOffset = 0;

// Ускорение камеры при переходе от текущей позиции к цели
camera.cameraAcceleration = 0.005

// Скорость, с которой ускорение останавливается
camera.maxCameraSpeed = 10

// Это прикрепляет камеру к холсту
camera.attachControl(canvas, true);

// NOTE:: УСТАНОВИТЕ ЦЕЛЬ КАМЕРЫ ПОСЛЕ СОЗДАНИЯ ЦЕЛЕВА И ИЗМЕНЕНИЯ НОТЫ ИЗ BABYLONJS V 2.5
// targetMesh created here.
camera.target = targetMesh;   // version 2.4 and earlier
camera.lockedTarget = targetMesh; //version 2.5 onwards
```
[A Playground Example of a Follow Camera following a moving target](http://www.babylonjs-playground.com/#12WBC#84)


## AnaglyphCameras

These extend the use of the Universal and Arc Rotate Cameras for use with red and cyan 3D glasses. They use post-processing filtering techniques.

### Constructing Anaglyph Universal Camera

```javascript

// Parameters : name, position, eyeSpace, scene
var camera = new BABYLON.AnaglyphUniversalCamera("af_cam", new BABYLON.Vector3(0, 1, -15), 0.033, scene);
```

### Constructing Anaglyph ArcRotateCamera

```javascript

// Parameters : name, alpha, beta, radius, target, eyeSpace, scene
var camera = new BABYLON.AnaglyphArcRotateCamera("aar_cam", -Math.PI/2, Math.PI/4, 20, new BABYLON.Vector3.Zero(), 0.033, scene);
```

The _eyeSpace_ parameter sets the amount of shift between the left eye view and the right eye view. Once you are wearing your 3D glasses, you might want to experiment with this float value.

You can learn all about anaglyphs by visiting a [Wikipedia page that explains it thoroughly](http://en.wikipedia.org/wiki/Anaglyph_3D).

## Device Orientation Camera

This is a camera specifically designed to react to device orientation events such as a modern mobile device being tilted forward or back and left or right.

### Constructing a Device Orientation Camera

```javascript

// Parameters : name, position, scene
   var camera = new BABYLON.DeviceOrientationCamera("DevOr_camera", new BABYLON.Vector3(0, 0, 0), scene);

    // Targets the camera to a particular position
    camera.setTarget(new BABYLON.Vector3(0, 0, -10));

	// Sets the sensitivity of the camera to movement and rotation
	camera.angularSensibility = 10;
	camera.moveSensibility = 10;

    // Attach the camera to the canvas
    camera.attachControl(canvas, true);

```
[A Playground Example of a Device Orientation Camera](http://www.babylonjs-playground.com/#12WBC#81) for those with a correct device.

## Virtual Joysticks Camera

This is specifically designed to react to Virtual Joystick events.
Virtual Joysticks are on-screen 2D graphics that are used to control the camera or other scene items.

### Requires

The third-party file [hand.js](http://handjs.codeplex.com/releases/view/119684).

### Read

[Virtual Joysticks David Rousset Blog](http://blogs.msdn.com/b/davrous/archive/2013/02/22/creating-an-universal-virtual-touch-joystick-working-for-all-touch-models-thanks-to-hand-js.aspx) on David's blog.

### Video

[Virtual Joysticks Camera demo in video](https://www.youtube.com/watch?v=53Piiy71lB0)

![Screenshot of the Virtual Joysticks Camera in action on Espilit](http://david.blob.core.windows.net/babylonjs/VJCBabylon.jpg)

### Complete sample

Here is a complete sample that loads the Espilit demo and switches the default camera to a virtual joysticks camera:

```javascript
document.addEventListener("DOMContentLoaded", startGame, false);
function startGame() {
  if (BABYLON.Engine.isSupported()) {
    var canvas = document.getElementById("renderCanvas");
    var engine = new BABYLON.Engine(canvas, true);

    BABYLON.SceneLoader.Load("Espilit/", "Espilit.babylon", engine, function (newScene) {

      var VJC = new BABYLON.VirtualJoysticksCamera("VJC", newScene.activeCamera.position, newScene);
      VJC.rotation = newScene.activeCamera.rotation;
      VJC.checkCollisions = newScene.activeCamera.checkCollisions;
      VJC.applyGravity = newScene.activeCamera.applyGravity;

      // Wait for textures and shaders to be ready
      newScene.executeWhenReady(function () {
        newScene.activeCamera = VJC;
        // Attach camera to canvas inputs
        newScene.activeCamera.attachControl(canvas);
        // Once the scene is loaded, just register a render loop to render it
        engine.runRenderLoop(function () {
          newScene.render();
        }),
      }),
    }, function (progress) {
    // To do: give progress feedback to user.
    }),
  }
}
```

If you switch back to another camera, don’t forget to call the dispose() function first. Indeed, the VirtualJoysticks are creating a 2D canvas on top of the 3D WebGL canvas to draw the joysticks with cyan and yellow circles. If you forget to call the dispose() function, the 2D canvas will remain, and will continue to use touch events input.


## VR Device Orientation Cameras

A new range of cameras.
[A Playground Example of a VR Device Orientation Camera](http://www.babylonjs-playground.com/#12WBC#80) for those with a correct device.

### Constructing the VR Device Orientation Free Camera

```javascript
//Parameters: name, position, scene, compensateDistortion, vrCameraMetrics
var camera = new BABYLON.VRDeviceOrientationFreeCamera ("Camera", new BABYLON.Vector3 (-6.7, 1.2, -1.3), scene);
```

### Constructing the VR Device Orientation Arc Rotate Camera

```javascript
//Parameters: name, alpha, beta, radius, target, scene, compensateDistortion, vrCameraMetrics
var camera = new BABYLON.VRDeviceOrientationArcRotateCamera ("Camera", Math.PI/2, Math.PI/4, 25, new BABYLON.Vector3 (0, 0, 0), scene);
```

### Constructing the VR Device Orientation Gamepad Camera

```javascript
//Parameters: name, position, scene, compensateDistortion, vrCameraMetrics
var camera = new BABYLON.VRDeviceOrientationGamepadCamera("Camera", new BABYLON.Vector3 (-10, 5, 14));
```

## WebVR Free Camera

The new virtual reality camera

```javascript

// Parameters : name, position, scene, webVROptions
    var camera = new BABYLON.WebVRFreeCamera("WVR", new BABYLON.Vector3(0, 1, -15), scene);
```

This camera deserves a page to itself so here it is [Using the WebVR Camera](/How_To/WebVR_Camera);

## FlyCamera

Эта камера имитирует свободное движение в трехмерном пространстве, подобно «призраку в космосе». Она поставляется с возможностью постепенного исправления крена, а также с возможностью имитации разворотных кругов.

Its defaults are:

1. Keyboard - The __A__ and __D__ keys move the camera left and right. The __W__ and __S__ keys move it forward and backward. The __E__ and __Q__ keys move it up and down.

2. Mouse - Rotates the camera about the Pitch and Yaw (X, Y) axes with the camera as origin. Holding the __right mouse-button__ rotates the camera about the Roll (Z) axis with the camera as origin.

### Constructing a Fly Camera

```javascript

// Parameters: name, position, scene
var camera = new BABYLON.FlyCamera("FlyCamera", new BABYLON.Vector3(0, 5, -10), scene);

// Airplane like rotation, with faster roll correction and banked-turns.
// Default is 100. A higher number means slower correction.
camera.rollCorrect = 10;
// Default is false.
camera.bankedTurn = true;
// Defaults to 90° in radians in how far banking will roll the camera.
camera.bankedTurnLimit = Math.PI / 2;
// How much of the Yawing (turning) will affect the Rolling (banked-turn.)
// Less than 1 will reduce the Rolling, and more than 1 will increase it.
camera.bankedTurnMultiplier = 1;

// This attaches the camera to the canvas
camera.attachControl(canvas, true);
```

## Customizing inputs

The cameras rely upon user inputs to move the camera. If you are happy with the camera presets Babylon.js is giving you, just stick with it.

If you want to change user inputs based upon user preferences, customize one of the existing presets, or use your own input mechanisms.  Those cameras have an input manager that is designed for those advanced scenarios. Read [customizing camera inputs](/How_To/Customizing_Camera_Inputs) to learn more about tweaking inputs on your cameras.

## Next step
You have now learned how to use many cameras, and learned some advanced input options available on our two most-used cameras. You can control how you see your scene, you can choose your input and viewing devices, and you now know how to move cameras around. To give your scene a more realistic effect, we are now going to learn [**how to manage lights**](/babylon101/Lights). See you soon.

# Further Reading

[Cameras Overview](/features/Cameras)
