# GUIButton

## new GUIButton(id, options, guimanager, callback, append)
Создает новый GUIButton

#### параметры
Имя | Тип | Описание
---|---|---
**id** | string | id и имя элемента
**options** | json | опции элемента
**guimanager** | GUIManager | gui менеджер
**callback** | function | Trigger - функция по клику (опционально)
**append** | bool | добавляется в &lt;body&gt;. =&gt; True по дефолту (опционально)
---

## Параметры

* **w**: ширина кнопки (в пикселях)
* **h**: высота кнопки (в пикселях)
* **x**: положение кнопки слева (в пикселях)
* **y**: положение кнопки сверху (в пикселях)
* **value**: value text button (string) =&gt; "Ok" by default
* **backgroundImage**: image background кнопки (string) =&gt; null by default
* **backgroundColor**: color background кнопки (string) =&gt; "rgba(0.5, 0.5, 0.5, 0.6)" by default
* **borderRadiusButton**: radius border кнопки (string)  =&gt; "10px" by default
* **borderButton**: border кнопки (string)  =&gt; "2px solid black" by default
* **colorText**: color text кнопки (string)  =&gt; "black" by default
* **zIndex**: depth of the element (int) =&gt; 1 by default
* **tabindex**: Tab order of the field.

## методы

### setVisible(bool, fade) → void
Делает этот GUI element видимым или невидимым

### isVisible() → void
Возвращает виден элемент или нет

### dispose() → void
Удаляет GUIButton и удаляет элемент.
