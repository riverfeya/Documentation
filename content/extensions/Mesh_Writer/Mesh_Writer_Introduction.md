# Mesh Writer

Генерация букы в BABYLON meshes.

### Basic-Usage
	Writer = BABYLON.MeshWriter(scene, {scale:scale});
	text1  = new Writer( 
	                "ABC",
	                {
	                    "anchor": "center",
	                    "letter-height": 50,
	                    "color": "#1C3870",
	                    "position": {
	                        "z": 20
	                    }
	                }
	            );

&#9679; Смотрите пример playground:  https://www.babylonjs-playground.com/#PL752W#1

### Getting the code

Copy https://github.com/BabylonJS/Extensions/blob/master/MeshWriter/meshwriter.min.js or https://github.com/BabylonJS/Extensions/blob/master/MeshWriter/meshwriter.js и встроить его в вашу последовательность загрузки.

### Superconstructor - BABYLON.MeshWriter()

После того как MeshWriter был загружен, BABYLON.MeshWriter объявляется.  Вызывается с одним или двумя параметрами.
- **scene** &nbsp; required
- **preferences** &nbsp; optional &nbsp; Объект предпочтений может указывать до трех значений

	      FIELD                 DEFAULT
	    default-font           Helvetica
	    scale                      1
	    letter-origin         "letter-center"

The call to BABYLON.MeshWriter returns a constructor.  Call it "**Writer**".

### Constructor - new Writer()

new Writer() is called with a string and an (optional) options parameter.&nbsp; The options object conforms to normal BabylonJS structures and terminology.

	      FIELD                 DEFAULT
	    font-family             default-font
	    anchor                  left
	    letter-height           100
	    letter-thickness        1
	    color                   #808080              # hits emissive only
	    alpha                   1
	    position
	        x                   0
	        y                   0
	        z                   0
	    colors                                       # if you need to control more than just emissive
	        diffuse             #F0F0F0
	        specular            #000000
	        ambient             #F0F0F0
	        emissive            color                # from option field 'color' above


**new Writer()** builds a mesh with material that is inserted into the scene.&nbsp; This is a multi-step process with interim meshes and holes per letter.&nbsp;  These meshes are sucked into an SPS and then disposed.&nbsp; At the end, one mesh, one material and one SPS have been added to the scene.

**new Writer()** also returns a **writer** instance with useful methods.&nbsp; See below.

### Instance

Each **writer** instance has methods to allow one to retrieve the BabylonJS objects or to get/set attributes of the SPS.

	   getSPS
	   getMesh
	   getMaterial
	   color                   # sets or gets color but no change to material
	   alpha                   # sets or gets alpha but no change to material
	   setColor                # set emissive color and change color value
	   setAlpha                # change value and material
	   overrideOpac            # change material but not value
	   resetOpac               # sets material to current value
	   dispose                 

### Usage Hints

If you wish to do extensive things with position, rotation or animation, retrieve the meshes and materials from the instance using the methods shown above.&nbsp; The output from **new Writer()** is an SPS with one particle for each character.

Colors:&nbsp; With most lighting, it is enough just to use the "color" field to specify the letter coloring.&nbsp; However, programmers may specify all four color types by putting a "colors" object in the options object.

Unless you have a specific need, do not specify a font.&nbsp; The default font, Helvetica, has the most extensive characters and the fewest faces; it will be the most efficient if you have a lot of text.&nbsp; Jura was added because the author likes it for numbers.

