---
PG_TITLE: 01. First Steps
---

# Первые шаги

Babylon.JS это отличный способ кодировать 3D окружение в WEB HTML5 canvas элементе. 

## Игровая площадка

Это самый быстрый и простой способ создать свою собственную сцену. Создать 3D-сцену легко, просто добавьте камеру, источники света и 3D-фигуры (меши), и все! 

[Playground](http://babylonjs-playground.com)это веб-сайт, который имеет все необходимое для создания собственной сцены или редактирования существующей. [More on the Playground](/features/Playground).

Шаблон для создания сцены на игровой площадке:

```javascript
var createScene = function () {

    // Создать пространство сцены
    var scene = new BABYLON.Scene(engine);

    // Добавьте камеру на сцену и прикрепите ее к canvas
    var camera = new BABYLON.ArcRotateCamera("Camera", Math.PI / 2, Math.PI / 2, 2, BABYLON.Vector3.Zero(), scene);
    camera.attachControl(canvas, true);

    // Добавить свет на сцену
    var light1 = new BABYLON.HemisphericLight("light1", new BABYLON.Vector3(1, 1, 0), scene);
    var light2 = new BABYLON.PointLight("light2", new BABYLON.Vector3(0, 1, -1), scene);

    // Здесь вы создаете и манипулируете мешами
    var sphere = BABYLON.MeshBuilder.CreateSphere("sphere", {}, scene);

    return scene;

};
```

Обо всем остальном, заботится Playground.

* [Playground example of above code](http://www.babylonjs-playground.com/#WG9OY#1)

## Ваш собственный HTML

При написании собственного HTML вам просто нужно embed функцию createScene в структуру HTML страницы с помощью тэга &lt; script &gt; наряду с несколькими другими вещами. Вам нужно будет загрузить BabylonJS JavaScript код. Также для использования на планшетах и ​​мобильных устройствах BabylonJS использует события указателя, а не события мыши, поэтому необходимо также загрузить систему событий PEP. 

Кроме того, элемент canvas должен быть добавлен к Body, так как именно здесь будет отображаться трехмерная сцена, а в код добавляется ссылочная переменная * canvas *. Вам также необходимо сгенерировать движок BabylonJS перед функцией для создания сцены.

Наконец, добавьте код для вызова сцены. Это позволяет механизму непрерывно отображать сцену в цикле и изменять ее размер, если размер браузера был изменен.

### HTML template

```javascript
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">

    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
        <title>Babylon Template</title>

        <style>
            html, body {
                overflow: hidden;
                width: 100%;
                height: 100%;
                margin: 0;
                padding: 0;
            }

            #renderCanvas {
                width: 100%;
                height: 100%;
                touch-action: none;
            }
        </style>

        <script src="https://preview.babylonjs.com/babylon.js"></script>
        <script src="https://preview.babylonjs.com/loaders/babylonjs.loaders.min.js"></script>
        <script src="https://code.jquery.com/pep/0.4.3/pep.js"></script>
    </head>

   <body>
   
	<canvas id="renderCanvas" touch-action="none"></canvas> //touch-action="none" for best results from PEP
	
	<script>
        var canvas = document.getElementById("renderCanvas"); // Get the canvas element 
        var engine = new BABYLON.Engine(canvas, true); // Generate the BABYLON 3D engine

        /******* Add the create scene function ******/
        var createScene = function () {

            // Создать пространство сцены
            var scene = new BABYLON.Scene(engine);

            // Добавьте камеру на сцену и прикрепите ее к холсту
            var camera = new BABYLON.ArcRotateCamera("Camera", Math.PI / 2, Math.PI / 2, 2, new BABYLON.Vector3(0,0,5), scene);
            camera.attachControl(canvas, true);

            // Добавить свет на сцену
            var light1 = new BABYLON.HemisphericLight("light1", new BABYLON.Vector3(1, 1, 0), scene);
            var light2 = new BABYLON.PointLight("light2", new BABYLON.Vector3(0, 1, -1), scene);

            //Добавить и управлять мешами в сцене
            var sphere = BABYLON.MeshBuilder.CreateSphere("sphere", {diameter:2}, scene);

            return scene;
        };
        /******* End of the create scene function ******/	

        var scene = createScene(); //Call the createScene function

        // Зарегистрируйте цикл рендеринга для многократного рендеринга сцены
        engine.runRenderLoop(function () { 
                scene.render();
        });

        // Следите за событиями изменения размера браузера / холста
        window.addEventListener("resize", function () { 
                engine.resize();
        });
	</script>
   
   </body>

</html>
```

## примечания

1. В приведенных выше примерах используется более новый метод MeshBuilder для создания форм где переменные для формы задаются в параметре объекта параметров и имеют некоторые преимущества по сравнению со старой формой BABYLON.Mesh.Create.... который использует список параметров для переменных формы. Большая часть Playgrounds используйте старый метод, так как многие были созданы до появления MeshBuilder. 

2. The use of PEP for pointer events is more recent advice, older advice was to use a system called hand.js. Both work, although hand.js is no longer maintained. You may still find references to hand.js in the documentation. 

# Next Step

Now you are ready to go further and learn how to create more shapes like spheres, cylinders, boxes, etc. by visiting [Set Shapes](/babylon101/Discover_Basic_Elements)

# Further Reading External
[BabylonJS Forum](https://forum.babylonjs.com)  
[PEP and hand.js](http://www.html5gamedevs.com/topic/22474-how-does-babylonjs-get-pointer-events-working/#comment-127993)  

# Useful Links External

[BabylonJS Main Website](http://www.babylonjs.com/)  
[BabylonJS Playground](http://babylonjs-playground.com)  
[PEP Github](https://github.com/jquery/PEP)  
[hand.js Github](https://github.com/Deltakosh/handjs)  




