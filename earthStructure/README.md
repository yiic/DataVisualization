3D Earth Structure
-----
**Intro:**  

This demo illustrates the inner structure of the earth in 3D. It using OrbitControls so that users can drag to spin and zoom in/out. It also has GUI for users to switch the axis helper off and on. 
 ([view this demo](https://yiic.github.io/my-three.js-projects/earthStructure/earthStructure.html))
 
 
![result picture](pic_result.png)
 
### Encountered Problems and Solutions ###
Objects created from three.js are not solid. However, layers in earth structure need to be solid. The following picture shows that the "hollow sphere".
 
 
![hollow sphere1](pic_hollow1.png)
 
 
To create solid spheres, the way [WestLangley](https://stackoverflow.com/questions/37090942/how-to-render-clipped-surfaces-as-solid-objects/37093210#37093210) provided does not work in this case. This way only change the texture and reflective effect of "cutting off" surface. So, this only work for none nesting object. If there are objects inside of each other, when we cutting off the outer object, the inner object is revealed in a way that still make the objects look like "hollow" objects.  The following picture shows the effect using this way. 


![hollow sphere2](pic_hollow2.png)



I use the library ["ThreeCSG.js"](https://github.com/chandlerprall/ThreeCSG). It's a CSG plugin for Three.js. The layers of crust, mantle, outer core and inner core are subtracting with each other in order to implement the effect of nesting layers. The outer surface is just a normal "hollow" sphere with the imported world map texture. In order to have the clip effect, the three solid layers crust, mantle, outer core are clipped by a cube while the outer surface "world map" is clipped by three planes. The clipIntersection needs to be set true. The following code is for generating layer of Mantle and is using ThreeCSG.js 

```js
    var geometry = new THREE.SphereGeometry( 0.96, 24, 24 ); // Take more time to load when increase the last two indexs.
    var mantle_mesh = new THREE.Mesh( geometry );
    var mantle_bsp = new ThreeBSP(mantle_mesh);

    var mantle_clip1 = mantle_bsp.subtract(cube_bsp);
    var mantle_clip2 = mantle_clip1.subtract(core_clip2);

    var mantle_result = mantle_clip2.toMesh(new THREE.MeshLambertMaterial({
        shading: THREE.SmoothShading,
        color: new THREE.Color( 0xff8533 )
    }));
```

To create a sphere, 3 parameters are needed to be set. The parameters are radius, widthSegments, and heightSegments. 

```js
    var geometry = new THREE.SphereGeometry( 0.96, 24, 24 );
```

Adjust the radius can control the thickness of each layer in the earth. widthSegments and heightSegments can determine the "smooth" degree of the sphere. It should be noted that if the width segment and the height segment are set too large, it will take more time to load the page. So I choose to set them as 24 for solid spheres. There outer surface is just a normal sphere without using CSG.  For this reason, in order to make the outer layer to be smooth and  less ''blocked'',  the segments number can be set as a larger number. 


**Sources/Libraries need to be included:**
- three.js
- stats.min.js
- OrbitControls.js
- ThreeCSG.js
- dat.gui.js