# CastorGUI V1.5

Библиотека для отображения GUI игрs как слой поверх холста HTML/CSS.

* [Demo online of this extension](http://www.babylon.actifgames.com/demoCastorGUI/)
* [Tutorial](https://github.com/BabylonJS/Extensions/tree/master/CastorGUI)
* [Documentation](https://bitbucket.org/JSbabylon/castorgui/src/3f588b5473f34f58fe1b8d3a095d0ba78a278d99/doc/?at=master)

## Features:

Выберите или создайте темы.

Создание GUI (элемент html5 и css3):
* texture (текстуры)
* text (текст)
* label (метки)
* window draggable (with заголовком и кнопкой close)
* dialog (с кнопкой close)
* panel (простой dialog без кнопки close)
* button (кнопка)
* slider (слайдер)
* progress (прогресс-бар)
* meter (измерительный прибор)
* select color (совместимый с IE и Edge)
* spinner (спинер)
* radio button (радио-кнопка)
* checkbox 
* textfield
* textarea
* fieldset
* select with options

## Краткий обзор использования

Мы должны создать GUIManager, который позволяет восстановить origin холста и предоставляет другие основные вещи.
GUIManager может иметь CSS, который отформатирует все, что вы хотите настроить в своем графическом интерфейсе.
Также можно варианты добавить тему в третий параметр.

```javascript
var canvas = document.getElementById("game");
var css = "button {cursor:pointer;}";
var options = {themeRoot: "../style/", themeGUI: "default"};
var guisystem = new CASTORGUI.GUIManager(canvas, css, options);
```
Затем мы создаем элементы интерфейса. например, текстуры и диалог с текстом:

```javascript
var myFunction = function() { alert("Yes, this work!"); };
var guiTexture = new CASTORGUI.GUITexture("life", "data/image.png", {w:50,h:50,x:10,y:0}, guisystem, myFunction);

var optionsDialog = {w: (guisystem.getCanvasWidth().width - 20), h: 100, x: 8, y: (guisystem.getCanvasWidth().height - 110)};
var dialog = new CASTORGUI.GUIDialog("dialog", optionsDialog, guisystem);
dialog.setVisible(true);

var text = new CASTORGUI.GUIText("textDialog", {size:15, text:"Display text here"}, guisystem, false);
dialog.add(text);
```
Это все. Все работает одинаково с одинаковой простотой.
