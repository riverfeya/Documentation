---
ID_PAGE: 22121
PG_TITLE: 14. Particles
---
# Частицы

Этот урок будет говорить о системе частиц в BabylonJS. Частицы часто представляют собой небольшие спрайты, используемые для имитации трудно воспроизводимых явлений, таких как огонь, дым, вода, или абстрактных визуальных эффектов, таких как магический блеск и волшебная пыль. Это сделано, испуская облако очень многих частиц из области. Начиная с версии 3.2 существуют специальные излучатели, которые ограничивают эту область в форме прямоугольника, сферы или конуса. Вы также можете написать свои собственные функции для управления облаком и регионом.

GPU частицы являются последним дополнением к семейству частиц и могут использоваться с соответствующим браузером для повышения производительности.

![Particles](/img/how_to/Particles/12.png)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Particles. 

* [Playground Example - Particle System](https://www.babylonjs-playground.com/#WBQ8EM)

## Creating the Particles

### Particle System
Чтобы выполнить этот магический трюк, сначала нужно создать объект `ParticleSystem`. 

```javascript
var particleSystem = new BABYLON.ParticleSystem("particles", 2000, scene);
```

Это устанавливает количество частиц в системе (емкость). Система частиц также требует текстуры, чтобы частицы могли быть видны, и излучатель, который устанавливает местоположение и распространение частиц от начальной точки. 

Обратите внимание, что вы можете использовать ParticleHelper для создания системы по умолчанию с: `BABYLON.ParticleHelper.CreateDefault(emitter)`.

После завершения вы приводите систему частиц в движение, используя

```javascript
particleSystem.start();
```
и останавливаете ее :

```javascript
particleSystem.stop();
```

Чтобы задержать начало системы частиц, можно установить `particleSystem.startDelay = 3000;` . Это значение определяет задержку в миллисекундах.

Вы также можете отложить запуск системы частиц с помощью `particleSystem.start(3000);`. Это значение с переопределением particleSystem.startDelay field if set.

**Note:** остановка системы частиц предотвратит создание новых частиц, но существующие будут продолжаться. Если вы хотите сбросить систему в пустое состояние, вам также придется позвонить `particleSystem.reset()`

Вы также можете установить время, в течение которого система частиц будет работать, хотя живые частицы могут продолжаться после этого времени..

```javascript
particleSystem.targetStopDuration = 5;
```

После остановки вы можете избавиться от системы частиц. Очень полезно, если вы хотите создать систему частиц с одним выстрелом с определенной targetStopDuration.

```javascript
particleSystem.disposeOnStop = true;
```

### Pre-warming
Начиная с Babylon.js v3.3, теперь вы можете указать период предварительного прогрева, чтобы убедиться, что ваша система находится в правильном состоянии перед рендерингом.

Для этого вам нужно настроить два свойства:
- `system.preWarmCycles`: Получает или задает значение, указывающее сколько циклов (или кадров) должно быть выполнено до первого рендеринга (это значение должно быть установлено до запуска системы). По умолчанию это 0 (тоесть нет pre-warming)
- `system.preWarmStepOffset`: Получает или задает значение, указывающее множитель временного шага для использования в режиме предварительного прогрева (по умолчанию 1)

Так что если вы установите свою систему так:

```
system.preWarmCycles = 100;
system.preWarmStepOffset = 5;
system.start();
```

Он выполнит цикл анимации частиц 100 раз с шагом по времени, установленным в 5 раз быстрее, чем в реальном времени. Чем больше циклов вы хотите, тем медленнее будет запускаться система. Поэтому было бы интересно увеличить шаг по времени, чтобы было меньше циклов. Но имейте в виду, что слишком большой временной шаг создаст проблемы, если жизненный спам частицы будет меньше временного шага.

Вот пример pre-warming: https://www.babylonjs-playground.com/#MX2Z99#8

### Particle Texture 
Чтобы нанести текстуру на частицы, такие как 
![Flare](/img/how_to/Particles/Flare.png)

задайте `particleTexture`

```javascript
particleSystem.particleTexture = new BABYLON.Texture("PATH TO IMAGE", scene);
```

Вы также можете применить маску к текстуре, чтобы отфильтровать некоторые цвета, или отфильтровать часть альфа-канала..

```javascript
particleSystem.textureMask = new BABYLON.Color4(0.1, 0.8, 0.8, 1.0);
```

Этот пример производит следующее 
![TextureMask](/img/how_to/Particles/12-1.png)

Чтобы использовать несколько текстур в сцене, используйте несколько систем частиц, каждая из которых может использовать один и тот же объект-эмиттер. 

### Particle Emitter
Излучатель может быть расположен либо с вектором 3, либо с мешем, и в этом случае положение сетки используется в качестве местоположения эмиттера. 

```javascript
particleSystem.emitter = new BABYLON.Vector3(-1, 2, 3);
```

```javascript
var source = BABYLON.Mesh.CreateBox("source", 1.0, scene);
particleSystem.emitter = source;
```

### World offset
начиная с Babylon.js v4.0, вы можете настроить мировое смещение для ваших частиц с:
```
particleSystem.worldOffset = new BABYLON.Vector3(100, 20, -453);
```

Эта команда сместит частицы, используя смещение (в основном используется, когда вам нужно держать камеру в центре мира, чтобы повысить точность, а затем перемещать мир).

### Положение и распространение
Распространение частиц от излучателя происходит изнутри бокса, размер которой определяется путем установки нижнего, левого, переднего угла и верхнего, правого, заднего угла бокса относительно местоположения излучателя. This is done using `minEmitBox` and `maxEmitBox`

```javascript
particleSystem.minEmitBox = new BABYLON.Vector3(-2, -3, 4); 
particleSystem.maxEmitBox = new BABYLON.Vector3(4, 2, 3); 
```

Коробка может быть свернута в линию в направлении оси, например, оси X

![EmitBox](/img/how_to/Particles/12-2.png)

```javascript
particleSystem.minEmitBox = new BABYLON.Vector3(-1, 0, 0); 
particleSystem.maxEmitBox = new BABYLON.Vector3(1, 0, 0); 
```

 ## Basic Playground Example
 Теперь он может создать систему частиц, хотя она очень скучна. Всего лишь несколько частиц, появляющихся в рассеянном пространстве, дрейфующих вокруг и исчезающих. 

 * [Playground Example - Basic Creation Small Spread](https://www.babylonjs-playground.com/#TUNZFH)
 * [Playground Example - Basic Creation Larger Spread](https://www.babylonjs-playground.com/#TUNZFH#1)

К счастью, очень скоро все может стать интереснее с настройкой дополнительных свойств. Читать дальше.  

## Система тонкой настройки частиц
Узнайте, как изменить время жизни, размер и цвет частиц, их скорость испускания, направление движения (возможно, зависит от силы тяжести). Вы также можете влиять на их вращение, скорость и форму облаков. Ниже вы можете найти [playground examples]() где вы можете изменить некоторые из этих параметров.

### Lifetime
Время, необходимое для исчезновения (или смерти) частиц после испускания, может варьироваться. Как только частица умирает, эта частица подвергается вторичной эмиссии. Их время жизни задается как случайный диапазон между низким и высоким значением, как в 

```javascript
// Life time of each particle (random between...)
particleSystem.minLifeTime = 0.3;
particleSystem.maxLifeTime = 1.5;
```

Начиная с Babylon.js v3.3, Вы также можете определить время жизни соответственно продолжительности системы частиц.
Например, если ваш звонок `system.targetStopDuration = 0.5` тогда вы можете определить время жизни частицы с градиентом, как это:

```
particleSystem.addLifeTimeGradient(0, 0.5);
particleSystem.addLifeTimeGradient(1, 0);
```
Первый параметр определяет градиент (0 означает в начале системы частиц и 1 означает в конце системы частиц). Второй параметр - время жизни частицы. Это означает, что в начале системы частиц частицы получат время жизни, установленное на 0,5. И когда система будет близка к `targetStopDuration` частицы получат время жизни, близкое к 0.

Рекомендуется по крайней мере определить градиент для 0 и 1.

Вы можете добавить столько градиентов, сколько хотите, если значение градиента находится в диапазоне от 0 до 1..

Вы также можете определить более сложную конструкцию, указав два значения для градиента.:

```
particleSystem.addLifeTimeGradient(0, 0.5, 0.8);
particleSystem.addLifeTimeGradient(1.0, 0, 0.1);
```

В этом случае время жизни частицы будет случайно выбрано между двумя значениями, когда будет достигнут градиент.

Чтобы удалить градиент вы можете позвонить `particleSystem.removeLifeTimeGradient(0.5)`.

### Size
Размер частиц также можно варьировать случайным образом в пределах заданного диапазона.

```javascript
// Size of each particle (random between...)
particleSystem.minSize = 0.1;
particleSystem.maxSize = 0.5;
```

Если вы хотите изменить размер частиц, чтобы они не были квадратными, вы можете использовать min/max для ScaleX/Y:
```javascript
// Scale of each particle (random between...)
particleSystem.minScaleX = 0.1;
particleSystem.maxScaleX = 0.5;

particleSystem.minScaleY = 0.2;
particleSystem.maxScaleY = 0.4;
```

Starting с Babylon.js v3.3, Вы также можете определить градиент фактора размера.

Чтобы добавить градиент размера, просто наберите следующий код:

```
particleSystem.addSizeGradient(0, 0.5);
```

Первый параметр определяет градиент (0 означает при рождении частицы и 1 означает при смерти частицы). Второй параметр - это коэффициент, применяемый к начальному размеру частиц. В этом случае частица будет рождена с половиной исходного размера (который вычисляется из minScale и maxScale).
Рекомендуется как минимум определить градиент для 0 и 1:

```
particleSystem.addSizeGradient(0, 0.5);
particleSystem.addSizeGradient(1.0, 3);
```

Вы можете добавить столько градиентов, сколько хотите, если значение градиента находится в диапазоне от 0 до 1..

Вы также можете определить более сложную конструкцию, указав два значения для градиента.:

```
particleSystem.addSizeGradient(0, 0.5, 0.8);
particleSystem.addSizeGradient(1.0, 3, 4);
```

В этом случае размер частицы будет случайно выбран между двумя значениями, когда будет достигнут градиент.

Чтобы удалить градиент вы можете вызвать `particleSystem.removeSizeGradient(0.5)`.

При работе с размером частиц вам может понадобиться переместить ось вращения (она же центр трансформации). По умолчанию масштаб будет исходить из центра частицы, но вы можете масштабировать его сверху или снизу. Чтобы изменить позицию поворота, просто позвоните:

```
particleSystem.translationPivot = new BABYLON.Vector2(0, -0.5); // In this case the scale will come from the bottom of the particle
```

Ниже приведен пример с градиентами размера и поворот установлен на нижний: https://www.babylonjs-playground.com/#L9QWZB#0

### Particle Colors
Существует три цвета, которые можно установить для системы частиц: два, которые объединяются (или смешиваются) в течение времени жизни частицы, и третий, который она принимает непосредственно перед тем, как она исчезает. 

```javascript
particleSystem.color1 = new BABYLON.Color4(0.7, 0.8, 1.0, 1.0);
particleSystem.color2 = new BABYLON.Color4(0.2, 0.5, 1.0, 1.0);
particleSystem.colorDead = new BABYLON.Color4(0, 0, 0.2, 0.0);
```

Начиная с Babylon.js v3.3, Вы также можете определить цветовые градиенты. Если вы определите цветовые градиенты, свойства color1, color2 и colorDead будут игнорироваться.

Чтобы добавить градиент цвета, просто наберите следующий код:

```
particleSystem.addColorGradient(0, new BABYLON.Color4(1, 1, 1, 0));
```

Первый параметр определяет градиент (0 означает при рождении частицы и 1 означает при смерти частицы). Рекомендуется как минимум определить градиент для 0 и 1:

```
particleSystem.addColorGradient(0, new BABYLON.Color4(1, 1, 1, 0));
particleSystem.addColorGradient(1.0, new BABYLON.Color4(1, 1, 1, 1));
```

Вы можете добавить столько градиентов, сколько хотите, если значение градиента находится в диапазоне от 0 до 1..

Вы также можете определить более сложную конструкцию, предоставив два цвета на градиент:

```
particleSystem.addColorGradient(0, new BABYLON.Color4(1, 1, 1, 0), new BABYLON.Color4(1, 0, 1, 0));
particleSystem.addColorGradient(1.0, new BABYLON.Color4(1, 1, 1, 1)new BABYLON.Color4(1, 0, 1, 1));
```

В этом случае цвет частицы будет случайным образом выбираться между двумя цветами, когда будет достигнут градиент.

Чтобы удалить градиент вы можете позвонить `particleSystem.removeColorGradient(0.5)`.

Вот пример цветовых градиентов: https://www.babylonjs-playground.com/#MX2Z99#8

### Particle blending
Существуют разные способы смешивания частиц со сценой, и они устанавливаются с помощью `blendMode`.

```javascript
particleSystem.blendMode = BABYLON.ParticleSystem.BLENDMODE_ONEONE;
```

```javascript
particleSystem.blendMode = BABYLON.ParticleSystem.BLENDMODE_STANDARD;
```

`BLENDMODE_ONEONE` по умолчанию и будет использоваться, если `blendMode` не указано.

* `BLENDMODE_ONEONE` - цвета добавляются без альфа-эффекта, влияющего на результат;
* `BLENDMODE_STANDARD` - цвета добавляются с использованием альфа-частицы (то есть. color * (1 - alpha) + particleColor * alpha).
* `BLENDMODE_ADD` - цвета добавлены, но только цвет частицы использует альфа частицы (то есть. color + particleColor * alpha).
* `BLENDMODE_MULTIPLY` - цвета умножаются и добавляются к (1 - alpha) (то есть. color * particleColor +  1 - alpha). [Demo here](https://playground.babylonjs.com/#KUDH9F#1)
* `BLENDMODE_MULTIPLYADD` - два прохода рендеринга с `BLENDMODE_MULTIPLY` а потом `BLENDMODE_ADD`. [Demo here](https://www.babylonjs-playground.com/#VS5XS7#0)

### Rates
`EmitRate` определяет количество частиц, испускаемых в секунду. Чем больше число, тем плотнее появляется испускаемое облако частиц. Когда частицы умирают, они перерабатываются для повторного выброса. Если их время жизни достаточно велико, а скорость их излучения достаточно высока, возможно, что в эмиссии частиц будет промежуток. 

![emitRate](/img/how_to/Particles/12-3.png)

```javascript
particleSystem.emitRate = 1000;
```

Вы можете остановить непрерывную эмиссию частиц, установив ручной счетчик выбросов.

```javascript
particleSystem.manualEmitCount = 300;
```

В этом случае количество частиц, указанное в счетчике, испускается, и дальнейшие выбросы частиц отсутствуют.

### Direction
Можно указать два направления. Если вы укажете только одно направление, частицы будут случайным образом двигаться в указанном общем направлении. Когда даны оба направления, частицы обычно перемещаются внутри двух направлений.

```javascript
particleSystem.direction1 = new BABYLON.Vector3(-7, 8, 3);
particleSystem.direction2 = new BABYLON.Vector3(7, 8, -3);
```
Направление также может зависеть от силы тяжести.

### Gravity
Может быть применено значение силы тяжести. Например, если отрицательно в направлении Y, частицы будут медленно тянуть вниз.

```javascript
//Установите гравитацию всех частиц (не обязательно вниз)
particleSystem.gravity = new BABYLON.Vector3(0, -9.81, 0);
```

### Rotation
Вы можете определить диапазон угловых скоростей для частиц вокруг оси Z для частиц в радианах в секунду:

```javascript
particleSystem.minAngularSpeed = 0;
particleSystem.maxAngularSpeed = Math.PI;
```

Вы также можете определить начальный угол поворота с помощью:

```
particleSystem.minInitialRotation = 0;
particleSystem.maxInitialRotation = Math.PI;
```

Начиная с Babylon.js v3.3, you can also define angular speed factor gradients.

Чтобы добавить угловой градиент скорости, просто наберите следующий код:

```
particleSystem.addAngularSpeedGradient(0, 0.5);
```

Первый параметр определяет градиент (0 означает при рождении частицы и 1 означает при смерти частицы). Второй параметр - это угловая скорость для использования. В этом случае частица будет рождаться с угловой скоростью, установленной на 0,5 радиана на кадр. Рекомендуется по крайней мере определить градиент для 0 и 1:

```
particleSystem.addAngularSpeedGradient(0, 0.5);
particleSystem.addAngularSpeedGradient(1.0, 3);
```

Вы можете добавить столько градиентов, сколько хотите, если значение градиента находится в диапазоне от 0 до 1..

Вы также можете определить более сложную конструкцию, указав два значения для градиента:

```
particleSystem.addAngularSpeedGradient(0, 0.5, 0.8);
particleSystem.addAngularSpeedGradient(1.0, 3, 4);
```

В этом случае угловая скорость частицы будет случайным образом выбираться между двумя значениями, когда будет достигнут градиент.

Чтобы удалить градиент вы можете вызвать `particleSystem.removeAngularSpeedGradient(0.5)`.


### Speed
Вы можете определить диапазон мощности излучающих частиц и общую скорость движения (0,01 - скорость обновления по умолчанию, более быстрые обновления = более быстрая анимация).

  ```javascript
  particleSystem.minEmitPower = 1;
  particleSystem.maxEmitPower = 3;
  particleSystem.updateSpeed = 0.005;
  ```

### Скорость со временем
Вы можете определить скорость во времени с градиентами. The скорость со временем энергия (или коэффициент), приложенная к направлению частицы. Значение 2 умножит размер направления на два, следовательно, умножив скорость частицы на 2.

Чтобы добавить градиент скорости, просто вызовите следующий код:

```
particleSystem.addVelocityGradient(0, 0.5);
```

Первый параметр определяет градиент (0 означает при рождении частицы и 1 означает при смерти частицы). Второй параметр - это скорость, которую нужно использовать. В этом случае частица родится со скоростью, установленной на 0,5. Рекомендуется по крайней мере определить градиент для 0 и 1:

```
particleSystem.addVelocityGradient(0, 0.5);
particleSystem.addVelocityGradient(1.0, 3);
```

Вы можете добавить столько градиентов, сколько хотите, если значение градиента находится в диапазоне от 0 до 1.

Вы также можете определить более сложную конструкцию, указав два значения для градиента:

```
particleSystem.addVelocityGradient(0, 0.5, 0.8);
particleSystem.addVelocityGradient(1.0, 3, 4);
```

В этом случае скорость частицы будет случайным образом выбираться между двумя значениями, когда будет достигнут градиент.

Вот пример скорости, применяемой к системе частиц: https://www.babylonjs-playground.com/#3W04PW#0

Чтобы удалить градиент вы можете вызвать `particleSystem.removeVelocityGradient(0.5)`. 

### Limit скорости во времени
Вы можете определить limit for скорость во времени с помощью градиентов. Этот предел будет использоваться для проверки текущей скорости частицы, и если предел достигнут, то к скорости будет применен коэффициент.
Вы можете определить этот коэффициент с `particleSystem.limitVelocityDamping`. 

Чтобы добавить градиент предельной скорости, просто наберите следующий код:

```
particleSystem.addLimitVelocityGradient(0, 0.5);
```

Первый параметр определяет градиент (0 означает при рождении частицы и 1 означает при смерти частицы). Вторым параметром является ограничить скорость для использования. В этом случае скорость частицы будет проверяться непосредственно после рождения, и если она больше 0,5, то будет применяться параметр демпфирования (поэтому скорость будет кодовой скоростью * демпфирование).

Рекомендуется как минимум определить градиент для 0 и 1:

```
particleSystem.addLimitVelocityGradient(0, 0.5);
particleSystem.addLimitVelocityGradient(1.0, 3);
```

Вы можете добавить столько градиентов, сколько хотите, если значение градиента находится в диапазоне от 0 до 1.

Вы также можете определить более сложную конструкцию, указав два значения для градиента:

```
particleSystem.addLimitVelocityGradient(0, 0.5, 0.8);
particleSystem.addLimitVelocityGradient(1.0, 3, 4);
```

В этом случае предельная скорость частицы будет случайным образом выбираться между двумя значениями, когда будет достигнут градиент.

Вот пример предельной скорости, примененной к системе частиц: https://www.babylonjs-playground.com/#9GBBPM#2

Чтобы удалить градиент вы можете вызвать `particleSystem.removeLimitVelocityGradient(0.5)`.

### Drag factor (Коэффициент сопротивления)
Вы можете определить коэффициент сопротивления со временем с градиентами. Этот фактор будет использоваться для моделирования воздушного трения, применяя коэффициент сопротивления к направлению частиц. Например, если ваш коэффициент сопротивления установлен на 0,8, тогда только 20% направления частицы будет добавлено к положению частицы.

Чтобы добавить drag градиент просто вызовите следующий код:

```
particleSystem.addDragGradient(0, 0.5);
```

Первый параметр определяет градиент (0 означает при рождении частицы и 1 означает при смерти частицы). Вторым параметром является коэффициент сопротивления использовать. В этом случае положение частицы будет `particle.position = particle.direction * (1.0 - 0.5)`.

Рекомендуется по крайней мере определить градиент для 0 и 1:

```
particleSystem.addDragGradient(0, 0.5);
particleSystem.addDragGradient(1.0, 3);
```

Вы можете добавить столько градиентов, сколько хотите, если значение градиента находится в диапазоне от 0 до 1.

Вы также можете определить более сложную конструкцию, указав два значения для градиента:

```
particleSystem.addDragGradient(0, 0.5, 0.8);
particleSystem.addDragGradient(1.0, 0, 0.1);
```

In this case the коэффициент сопротивления of the particle will randomly be picked between the two values when the gradient will be reached.

Here is an example of коэффициент сопротивления applied to a particle system: https://www.babylonjs-playground.com/#BDW3BF#0

Чтобы удалить градиент вы можете вызвать `particleSystem.removeDragGradient(0.5)`.

### Скорость излучения с течением времени
Вы можете определить скорость испускания частиц с помощью градиентов. Скорость излучения со временем перезапишет значение свойства `system.emitRate`.

Чтобы добавить emit rate градиент просто вызовите следующий код:

```
particleSystem.addEmitRateGradient(0, 10);
```

**Обратите внимание, что градиент скорости излучения будет работать только в том случае, если система имеет определенный срок службы, что означает, что вы должны определить свойство `system.targetStopDuration`**

Первый параметр определяет градиент (0 означает в начале системы и 1 означает в конце системы). Вторым параметром является emit rate использовать. В этом случае система запустится, испуская 10 частиц за кадр.
Рекомендуется по крайней мере определить градиент для 0 и 1:

```
particleSystem.addEmitRateGradient(0, 10);
particleSystem.addEmitRateGradient(1.0, 500);
```

Вы можете добавить столько градиентов, сколько хотите, если значение градиента находится в диапазоне от 0 до 1.

Вы также можете определить более сложную конструкцию, указав два значения для градиента:

```
particleSystem.addEmitRateGradient(0, 5, 10);
particleSystem.addEmitRateGradient(1.0, 800, 1000);
```

В этом случае скорость излучения будет случайным образом выбираться между двумя значениями, когда будет достигнут градиент.

Вот пример градиентов скорости излучения, применяемых к системе частиц: https://www.babylonjs-playground.com/#3NM14X#0

Чтобы удалить градиент вы можете вызвать `particleSystem.removeEmitRateGradient(0.5)`. 

### Start size over time
Чтобы добавитьn start size градиент просто вызовите следующий код:

```
particleSystem.addStartSizeGradient(0, 2);
```

**Обратите внимание, что начальный размер градиента будет работать только в том случае, если система имеет определенное время жизни, что означает, что вы должны определить свойство `system.targetStopDuration`**

Первый параметр определяет градиент (0 означает в начале системы и 1 означает в конце системы). Вторым параметром является start size scale использовать. В этом случае система начнет испускать частицы размером в 2 раза больше исходного размера.. (eg. если размер установлен в 2, а начальный размер установлен в 3, результирующий размер будет 6)
Рекомендуется по крайней мере определить градиент для 0 и 1:

```
particleSystem.addStartSizeGradient(0, 10);
particleSystem.addStartSizeGradient(1.0, 500);
```

Вы можете добавить столько градиентов, сколько хотите, если значение градиента находится в диапазоне от 0 до 1.

Вы также можете определить более сложную конструкцию, указав два значения для градиента:

```
particleSystem.addStartSizeGradient(0, 5, 10);
particleSystem.addStartSizeGradient(1.0, 800, 1000);
```

В этом случае начальный размер будет случайно выбран между двумя значениями, когда будет достигнут градиент.

Вот пример градиентов начального размера, примененных к системе частиц: https://www.babylonjs-playground.com/#3NM14X#14

Чтобы удалить градиент вы можете вызвать `particleSystem.removeStartSizeGradient(0.5)`. 

### Ramp gradients
Вы можете использовать градиенты линейного изменения, чтобы изменить цвет частицы на основе альфа.

Ramp gradients довольно мощные, но требуют немного настройки.

Сначала вы должны объявить ramp gradients:
```
system.addRampGradient(0.0, new BABYLON.Color3(1, 1, 1));
system.addRampGradient(0.09, new BABYLON.Color3(209/255, 204/255, 15/255));
system.addRampGradient(0.18, new BABYLON.Color3(221/255, 120/255, 14/255));
system.addRampGradient(0.28, new BABYLON.Color3(200/255, 43/255, 18/255));
system.addRampGradient(0.47, new BABYLON.Color3(115/255, 22/255, 15/255));
system.addRampGradient(0.88, new BABYLON.Color3(14/255, 14/255, 14/255));
system.addRampGradient(1.0, new BABYLON.Color3(14/255, 14/255, 14/255));
```

Эти градиенты будут использоваться для создания ramp color texture.

Затем вам нужно включить их:
```
system.useRampGradients = true;
```

По умолчанию альфа-значение частицы (построен из `textureAlpha * particleColorAlpha`) используется для получения ramp color (Alpha это индекс в ramp gradients list) используя эту формулу: `finalColor = textureColor * particleColor * rampColor[alphaIndex]`.

Но чтобы дать вам больше контроля, вы можете использовать функцию переназначения, чтобы изменить переназначение этого альфа-индекса:
```
system.addColorRemapGradient(0, 0, 0.1);
system.addColorRemapGradient(0.2, 0.1, 0.8);
system.addColorRemapGradient(0.3, 0.2, 0.85);
system.addColorRemapGradient(0.35, 0.4, 0.85);
system.addColorRemapGradient(0.4, 0.5, 0.9);
system.addColorRemapGradient(0.5, 0.95, 1.0);
system.addColorRemapGradient(1.0, 0.95, 1.0);
```

Градиенты переназначения цветов определяют минимальное и максимальное значения, которые будут меняться со временем (в зависимости от того, сколько градиентов вы добавляете). Альфа-индекс затем переназначается из [min, max] в [0, 1] с этой формулой: `finalAlphaIndex = clamp((alphaIndex - min) / (max - min), 0.0, 1.0)`.

В конечном итоге вы также можете переназначить альфа-значение, сгенерированное на пиксель,:
```
system.addAlphaRemapGradient(0, 0, 0.1);
system.addAlphaRemapGradient(1.0, 0.1, 0.8);
```

Альфа-переназначение вычислит окончательное альфа-значение, используя эту формулу: `finalAlpha = clamp((textureAlpha * particleColorAlpha * rampColor.a - min) / (max - min), 0.0, 1.0)`.

Вы можете найти демо здесь: https://www.babylonjs-playground.com/#VS5XS7#0

### Alignment
По умолчанию все частицы отображаются как billboards. Но вы можете вместо этого выровнять их по направлению частиц `system.isBillboardBased = false`.

Вы можете найти демо [здесь](https://www.babylonjs-playground.com/#EV0SEQ)

Когда billboard включен, вы можете решить либо иметь полный billboard (по всем осям) или только по оси Y с этим кодом:

```
system.billboardMode = BABYLON.ParticleSystem.BILLBOARDMODE_Y;
```

Вы также можете использовать растянутый billboard, который будет похож на режим полного рекламного щита, но с дополнительным вращением, чтобы выровнять частицы по их направлению..

Демонстрация может объяснить этот режим billboard лучше, чем слова: https://www.babylonjs-playground.com/#B9HKG0#0

Вы также можете найти демо stretched billboard particles: https://www.babylonjs-playground.com/#5A4TP5

## Adjustable Playground Examples

* [Playground Example - Adjust Min & Max of EmitBox](https://www.babylonjs-playground.com/#LMG759)
* [Playground Example - Adjust Emit Life Time, Rate, Power and Update Speed](https://www.babylonjs-playground.com/#63PJFT)

## Shape Emitters
Начиная с Babylonjs 3.2 Вы можете сформировать область, из которой испускаются частицы в виде

* Point (точка)
* Box (бокс)
* Sphere (сфера)
* Hemisphere (полусфера)
* Cylinder (цилиндр)
* Cone (конус)

путем добавления определенной функции эмиттера.

### Point Emitter

Чтобы создать точечный эмиттер, вы можете запустить этот код:

```javascript
var pointEmitter = particleSystem.createPointEmitter(new BABYLON.Vector3(-7, 8, 3), new BABYLON.Vector3(7, 8, -3));
```
Метод `createPointEmitter` принимает четыре параметра в следующем порядке

* direction1: Vector3, 
* direction2: Vector3

Возвращенный объект `pointEmitter` может быть использован для изменения значений этих свойств.

```javascript
pointEmitter.direction1 = new BABYLON.Vector3(-5, 2, 1); 
pointEmitter.direction2 = new BABYLON.Vector3(5, 2, 1);  
```

* [Playground Example - Point Emitter](https://www.babylonjs-playground.com/#08YT34)

### Box Emitter

Чтобы создать box эмиттер, который вы используете, например

```javascript
var boxEmitter = particleSystem.createBoxEmitter(new BABYLON.Vector3(-7, 8, 3), new BABYLON.Vector3(7, 8, -3), new BABYLON.Vector3(-1, 0, 0), new BABYLON.Vector3(1, 0, 0));
```
 `createBoxEmitter` метод принимает четыре параметра в следующем порядке

* direction1: Vector3, 
* direction2: Vector3, 
* minEmitBox: Vector3, 
* maxEmitBox: Vector3

Возвращаемый `boxEmitter` Объект может быть использован для изменения значений этих свойств.

```javascript
boxEmitter.direction1 = new BABYLON.Vector3(-5, 2, 1); 
boxEmitter.direction2 = new BABYLON.Vector3(5, 2, 1);  
boxEmitter.minEmitBox = new BABYLON.Vector3(-2, -3, -4);  
boxEmitter.maxEmitBox = new BABYLON.Vector3(2, 3, 4); 
```

* [Playground Example - Box Emitter](https://www.babylonjs-playground.com/#MRRGXL#1)

### Sphere Emitter

Вы можете создать sphere эмиттер с заданным радиусом, 1.2 например,  используя

```javascript
var sphereEmitter = particleSystem.createSphereEmitter(1.2);
```
Возвращаемый `sphereEmitter` объект может быть использован для изменения значения радиуса.

Частицы испускаются в направлении нормалей поверхности, то есть линий от центра сферы через точку поверхности.

* [Playground Example - Sphere Emitter](https://www.babylonjs-playground.com/#MRRGXL#2)

С `sphereEmitter.radiusRange` Вы можете определить, где по радиусу должны испускаться частицы. Значение 0 означает только на поверхности, а значение 1 означает все по радиусу.

Если вы предпочитаете выбрать направление эмиссии, Вы можете создать directed sphere emitter

```javascript
var sphereEmitter = particleSystem.createDirectedSphereEmitter(1.2, new BABYLON.Vector3(1, 1, 1), new BABYLON.Vector3(2, 8, 2));
```

The `createDirectedSphereEmitter` метод принимает три параметра в следующем порядке

* radius: Number,
* direction1: Vector3, 
* direction2: Vector3, 
 

Возвращаемый `sphereEmitter` Объект может быть использован для изменения значений этих свойств.

```javascript
sphereEmitter.radius = 3.4;
sphereEmitter.direction1 = new BABYLON.Vector3(-5, 2, 1); 
sphereEmitter.direction2 = new BABYLON.Vector3(5, 2, -1);    
```

The first parameter is the radius the second is direction1 and third is direction2. (The direction will be generated randomly between direction1 and direction2)

* [Playground Example - Sphere Emitter with Directions](https://www.babylonjs-playground.com/#MRRGXL#3)

### Hemispheric Emitter

Вы можете создать hemispheric эмиттер с заданным радиусом, 1.2 например,  using

```javascript
var hemisphericEmitter = particleSystem.createHemisphericEmitter(1.2);
```
Возвращаемый `hemisphericEmitter` объект может быть использован для изменения значения радиуса.

Частицы испускаются в направлении нормалей поверхности, ie the lines from the center of the hemisphere through a surface point.

* [Playground Example - Hemispheric Emitter](https://www.babylonjs-playground.com/#FHIQYC)

With `hemisphericEmitter.radiusRange` you can define where along the radius the particles should be emitted. A value of 0 means only on the surface while a value of 1 means all along the radius.

### Cylinder Emitter

Вы можете создать cylinder эмиттер с заданным радиусом, height, radiusRange, directionRandomizer with the following:

```javascript
var cylinderEmitter = particleSystem.createCylinderEmitter(1,1,0,0);
```
Возвращаемый `cylinderEmitter` объект может быть использован для изменения значения радиуса, height, etc.

Частицы испускаются в направлении нормалей поверхности, ie outward from the cylinder

* [Playground Example - Cylinder Emitter](https://www.babylonjs-playground.com/#UL4WC0)

With `cylinderEmitter.radiusRange` you can define where along the radius the particles should be emitted. A value of 0 means only on the surface while a value of 1 means all along the radius.
With `cylinderEmitter.directionRandomizer` can change how much to randomize the particles direction.

The `createDirectedCylinderEmitter` метод принимает три параметра в следующем порядке

* radius: Number,
* height: Number,
* radiusRange: Number,
* direction1: Vector3, 
* direction2: Vector3, 
 

Возвращаемый `cylinderEmitter` Объект может быть использован для изменения значений этих свойств.

```javascript
cylinderEmitter.radius = 3.4;
cylinderEmitter.direction1 = new BABYLON.Vector3(-5, 2, 1); 
cylinderEmitter.direction2 = new BABYLON.Vector3(5, 2, -1);    
```

The first parameter is the radius the second is direction1 and third is direction2. (The direction will be generated randomly between direction1 and direction2)

* [Playground Example - Cylinder Emitter with Directions](https://www.babylonjs-playground.com/#UL4WC0#5)

### Cone Emitter

Чтобы создать cone эмиттер, который вы используете, например

```javascript
var coneEmitter = particleSystem.createConeEmitter(2, Math.PI / 3);
```

The `createConeEmitter` method takes two parameters in the following order

* radius: Number;
* angle: Number, measured in radians, the vertex angle of the cone.

The cone is created with its axis along the Y-axis and its vertex at the bottom.

With `coneEmitter.radiusRange` you can define where along the radius the particles should be emitted. A value of 0 means only on the surface while a value of 1 means all along the radius.

The same applies to `coneEmitter.heightRange`: you can define where along the height the particles should be emitted. A value of 0 means only on the top surface while a value of 1 means all along the height.

Here is an example of a particle system emitted only from the outside of a flat cone: https://www.babylonjs-playground.com/#B9HKG0#1

Возвращаемый `coneEmitter` Объект может быть использован для изменения значений этих свойств.

```javascript
coneEmitter.radius = 3.4;
coneEmitter.angle = Math.PI / 2;    
```

With `coneEmitter.emitFromSpawnPointOnly = true` you can force the emitter to only emit particles from the spawn point (the start of the cone).

* [Playground Example - Cone Emitter](https://www.babylonjs-playground.com/#MRRGXL#4)
* [Playground Example - Cone Emitter Rotating](https://www.babylonjs-playground.com/#MRRGXL#5)

## Noise texture
Начиная с Babylon.js v3.3, теперь вы можете использовать noise texture чтобы «возмущать» положение частиц. Текстура шума технически используется для изменения направления частиц:

```
var noiseTexture = new BABYLON.NoiseProceduralTexture("perlin", 256, scene);
noiseTexture.animationSpeedFactor = 5;
noiseTexture.persistence = 2;
noiseTexture.brightness = 0.5;
noiseTexture.octaves = 2;

particleSystem.noiseTexture = noiseTexture;
particleSystem.noiseStrength = new BABYLON.Vector3(100, 100, 100);
```

Наряду с настройкой noiseTexture вы также можете контролировать силу, применяемую к каждой оси с помощью `particleSystem.noiseStrength`.

Demo можно найти здесь: https://www.babylonjs-playground.com/#R1JWLA#3

## GPU Particles

Начиная с Babylon.js v3.2, Вы можете использовать новые функции WebGL2, буфер обратной связи преобразования, чтобы значительно повысить производительность частиц. В то время как обычные частицы используют процессор для анимации и графический процессор для рендеринга, новый API WebGL2 позволяет Babylon.js использовать GPU и для анимации и для рендеринга. С частицами GPU все выгружается на GPU.

К сожалению, эта функция доступна только тогда, когда доступен WebGL2. Вы можете использовать `BABYLON.GPUParticleSystem.IsSupported`, чтобы определить, можно ли использовать частицы GPU. Когда они поддерживаются, частицы GPU могут почти использоваться как обычные частицы:

```javascript
var particleSystem = new BABYLON.GPUParticleSystem("particles", { capacity:1000000 }, scene);
```

Поскольку процессор больше не задействован, вы можете сходить с ума от активных частиц (1000000 in this example). Кроме того, вы можете использовать `icleSystem.activeParticleCount`, чтобы определить количество активных частиц, если вы хотите ограничить использование графического процессора.
**Note:** Sub emitters не поддерживаются в GPU particles.

### Random Texture
Обидно, но нет хорошего способа получить случайные числа при запуске на GPU. Чтобы заполнить этот пробел, Babylon.js создаст текстуру, заполненную тысячами случайных значений. Эти значения будут прочитаны шейдером обновления частиц, чтобы оживить частицы.
По умолчанию используется самый большой поддерживаемый размер текстуры (16K). Вы можете уменьшить размер этой текстуры, инициализируя систему следующим образом:

```javascript
var particleSystem = new BABYLON.GPUParticleSystem("particles", { capacity:1000000, randomTextureSize: 4096 }, scene);
```

### Fallback
Поскольку GPUParticleSystem и ParticleSystem совместно используют почти все свои API, легко переключаться с одного на другой, когда WebGL2 не поддерживается. Имейте в виду, что процессор не может анимировать столько частиц, сколько может графический процессор. Таким образом, вам, вероятно, придется уменьшить емкость вашей системы, когда не используется GPUParticleSystem.

### Stopping a GPU Particle System
При вызове `system.stop ()` для объекта `GPUParticleSystem` вы заставите систему прекратить генерировать новые частицы. Но частицы все равно будут визуализироваться, даже если они не видны.

Чтобы полностью остановить `GPUParticleSystem`, вы должны вызвать` dispose () `для него.

### Неподдерживаемые функции
Следующие функции не поддерживаются GPU particles из-за их внутренней природы:
- ManualEmitCount
- Custom effects
- Animation sheets
- disposeOnStop
- Dual values per gradient (only one value is supported)
- Emit rate gradients are not supported
- Start size gradients are not supported

### Playground

* [Playground Example - GPU Particles](https://www.babylonjs-playground.com/#PU4WYI#4)

## Next step
ParticleSystems Это очень мощные и универсальные инструменты, которые могут привнести реалистичность и движение в ваши сцены. Не стесняйтесь использовать их, поскольку они не ресурсоемки.

Оставайтесь с нами, потому что мы собираемся узнать новую, очень интересную вещь: [Configuring your environment](/babylon101/Environment).

# Further Reading

## Basic - L1

[Particles Overview](/features/Particles)  
[Particle Helper](/How_To/ParticleHelper)  
[Mesh Overview](/features/Shapes)

[Как создать анимированные частицы](/how_to/Animate)  
[Как использовать Sub Emitters](/how_to/Sub_Emitters)

[Solid Particle System](/How_To/Solid_Particles)

## Intermediate - L2
[How to Customize the Particle System](/how_to/Customise)  
[How создатьnimated particles](/how_To/Particles/Animate.md)


