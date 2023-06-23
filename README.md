# ode4j experimental version on GWT libGDX

Note: after doing extensive perfromance tests, I have decided to abandon this effort.  Currently this is the only working 3D physics library working in the official libGDX GWT backend that I know of.  However the perforamance for games is lacking.

![image](https://github.com/antzGames/ode4j-GWT-Compatible-libGDX/assets/10563814/809301b1-33b9-4e83-bace-7c2489e0b899)

https://user-images.githubusercontent.com/10563814/234369007-e2f0699a-a9a5-4551-a006-426dc7d285e8.mp4

You can test the demos on your browser [here](https://antzgames.itch.io/physics-in-a-browser).

This repository hosts an experimental version of [Open Dynamics Engine for Java](https://github.com/tzaeschke/ode4j) (ode4j v0.4.1) 3D physics library working on libGDX's GWT backend. Some of the original ode4j code was modified to compile and run properly on libGDX's GWT backend, therefore I cannot guarantee everything is working properly.  More importantly keeping up to date with ode4j updates will be difficult.

If you want to use ode4j only on libGDX Desktop/Android/iOS backends then I recommend you use [odej4](https://github.com/tzaeschke/ode4j) directly.  However if you want cross platform support (i.e include GWT support) then you could use this library for all platforms.

I created this repository to play with a 3D physics engine that works on libGDX's GWT backend for game JAMS and other fun stuff.  I do not recommend to use in important projects.

### What I changed from ode4j's codebase

Here is a brief summary of what I had to change to get ode4j to work on libGDX's backend:

![image](https://user-images.githubusercontent.com/10563814/234086366-4d4b1e61-31ee-422b-a402-3c885914a510.png)

I also removed most of the cpp (C++) packages and classes.

## Where to get ODE/ode4j documentation and help

ODE official manual: http://ode.org/wiki/index.php/Manual

By far the most useful part is the [HOWTO](http://ode.org/wiki/index.php/HOWTO) section

ode4j discord channel : https://discord.gg/UFXJcXv2P8 ode4j/Java

ode4j contains also some features that are not present in ODE, such as a ragdoll and heightfields with holes. See ode4j's [Wiki](https://github.com/tzaeschke/ode4j/wiki/Functionality-beyond-ODE).

The [ODE forum](https://groups.google.com/forum/#!forum/ode-users) is useful for questions around physics and general API usage.

The [ode4j forum](https://groups.google.com/forum/?hl=en#!forum/ode4j) is for problems and functionality specific to ode4j/Java. 

There is also the [old website](https://tzaeschke.github.io/ode4j-old/), including some [screenshots](https://tzaeschke.github.io/ode4j-old/ode4j-features.html).

Here is a [Youtube video](https://www.youtube.com/watch?v=ENlpu_Jjp3Q) of a list of games that used ODE from 2002-2015.  You will be suprised how many of your favorite games used this physcis libary.

## What you don't get

This is a 3D physics library only.  You will have to implement your own draw calls.  ode4j demos use an unoptimized custom drawing helper classes based on LWJGL.  Even ode4j's documentation says that thier render implementation has poor performance and is not optimized.  Regardless, there was no point migrating the drawing helper classes over because we use libGDX.

Becasue I did not migrate the draw helper classes, every demo from ode4j will not work.  Do not worry though, this repo has a few of the demos migrated over to use libGDX rendering classes.

## Math classes

Ode4j has its own math classes similar to libGDX's Vector3, Matrix3, Matrix4, and Quaternion.

I added a math utility class called [Ode2GDXMathUtils](https://github.com/antzGames/ode4j-GWT-Compatible-libGDX/blob/master/core/src/main/java/com/antz/ode4libGDX/util/Ode2GdxMathUtils.java).  Use the following methods to create the libGDX Quaternion from ode4j's QuanternionC or DMatrix3C:

```java
  Quaternion q1 = Ode2GdxMathUtils.getGdxQuaternion(odeQuaternion);
  Quaternion q2 = Ode2GdxMathUtils.getGdxQuaternion(odeMat3);
  ```

In addition ode4j uses double and not float like most of libGDX's math classes.

## Demos

You can test the demos on your browser [here](https://antzgames.itch.io/physics-in-a-browser).

```F1``` key will cycle to the next demo.

The following modified od4j demos are included:

* `jBullet migration` - [JamesTKhan's libGDX jBullet tutorial example](https://www.youtube.com/watch?v=O0Deshj2-KU), but using odej4 library. (Picture below)
* `DemoCrash` - destroy a wall of cubes with a cannon.
* `DemoRagdoll` - Rigid bodies and joints that you can apply to a humanoid character, to simulate behaviour such as impact collisions
 and character death.
* `DemoTrimeshHeightfield` - apply a terrain mesh and see objects roll off the terrain.
* `MundusHeightField` - same demo as above but uses [Mundus](https://github.com/JamesTKhan/Mundus) terrain.

I am migrating new demos every week.

FYI, the original ode4j demos have Z UP which is a pain.  During demo migration I either rotated the camera `camera.up.set(Vector.Z)` or reconfiged the simuation (gravity, positions, rotations) to have Y UP.  Both worked but eventaully its best to implement the second option.  In addition, libGDX primitive 3D shapes are created on the Y-axis orientation, and ode4j is on the z-axis.  You need to be careful that your physics objects and libGDX render objects have the same orientation.

The demos have been tested on GWT, Desktop and Android.

![odeGif](https://user-images.githubusercontent.com/10563814/235331595-2bffb58e-b429-44d8-b7fb-d3e5d89c0bca.gif)


## GWT Performance

To test performance for yourself, modify the [DemoCrashScreen](https://github.com/antzGames/ode4j-GWT-Compatible-libGDX/blob/master/core/src/main/java/com/antz/ode4libGDX/screens/DemoCrashScreen.java).

It currently creates 75 boxes and I get 60 frame per second (fps) on a Chrome based browser.  You can increase the size of the wall, resulting in more boxes by modifying these two constants:

```java
    private static final float WALLWIDTH = 12;		// width of wall
    private static final float WALLHEIGHT = 10;		// height of wall
```

For me if when I generate over 200 boxes my fps on Chrome goes down significantly.  Please note I removed ode4j's threading support for all platforms.

## Repo structure

A [libGDX](https://libgdx.com/) project generated with [gdx-liftoff](https://github.com/tommyettinger/gdx-liftoff).

Use IntelliJ or Android Studio as your IDE for minimal issues building.  You may have to your tweak the gradle version/plugin.  I used Gradle Version 7.6 and Android Gradle Plugin Version 7.0.4

This project was generated with a template including simple application launchers and a main class extending `Game` that sets the first screen.

My modified ode4j v0.4.1 [source code](https://github.com/antzGames/ode4j-GWT-Compatible-libGDX/tree/master/core/src/main/java/org/ode4j) is included in this repo.  Currently no work has been done to make and publish this as a library.  Still too experiemental for that.  Once I have used this in a few game JAMS, I might make a library out of it.

### Platforms

- `core`: Main module with the application logic shared by all platforms.
- `lwjgl3`: Primary desktop platform using LWJGL3.
- `android`: Android mobile platform. Needs Android SDK.
- `html`: Web platform using GWT and WebGL. Supports only Java projects.

This project uses [Gradle](http://gradle.org/) to manage dependencies.
The Gradle wrapper was included, so you can run Gradle tasks using `gradlew.bat` or `./gradlew` commands.
Useful Gradle tasks and flags:

- `android:lint`: performs Android project validation.
- `build`: builds sources and archives of every project.
- `cleanEclipse`: removes Eclipse project data.
- `cleanIdea`: removes IntelliJ project data.
- `clean`: removes `build` folders, which store compiled classes and built archives.
- `eclipse`: generates Eclipse project data.
- `html:dist`: compiles GWT sources. The compiled application can be found at `html/build/dist`: you can use any HTTP server to deploy it.
- `html:superDev`: compiles GWT sources and runs the application in SuperDev mode. It will be available at [localhost:8080/html](http://localhost:8080/html). Use only during development.
- `idea`: generates IntelliJ project data.
- `lwjgl3:jar`: builds application's runnable jar, which can be found at `lwjgl3/build/libs`.
- `lwjgl3:run`: starts the application.
- `test`: runs unit tests (if any).

## ODE, ode4j and other Licenses

### Licensing & Copyright for ODE and ode4j

This library is under copyright by Tilmann Zäschke (Java port), Russell L. Smith (copyright holder of the original ODE code), Francisco Leon (copyright holder of the original GIMPACT code) and Daniel Fiser (copyright holder of the original libccd).

This library is free software; you can redistribute it and/or modify it under the terms of EITHER:
(1) The GNU Lesser General Public License as published by the Free Software Foundation; either version 2.1 of the License, or (at your option) any later version. The text of the GNU Lesser General Public License is included with this library in the file LICENSE.TXT.
(2) The BSD-style license that is included with this library in the files ODE-LICENSE-BSD.TXT and ODE4j-LICENSE-BSD.TXT.

This library is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the files LICENSE.TXT, ODE-LICENSE-BSD.TXT, GIMPACT-LICENSE-BSD.TXT, GIMPACT-LICENSE-LGPL.TXT, ODE4J-LICENSE-BSD.TXT and LIBCCD_BSD-LICENSE for more details.

The LICENSE.TXT, ODE-LICENSE-BSD.TXT, GIMPACT-LICENSE-BSD.TXT, GIMPACT-LICENSE-LGPL.TXT, LIBCCD_BSD-LICENSE and ODE4J-LICENSE-BSD.TXT files are available in the source code.

## Legal

ode4j:
Copyright (c) 2009-2017 Tilmann Zäschke ode4j@gmx.de.
All rights reserved.

Like the original ODE, ode4j is licensed under LGPL v2.1 and BSD 3-clause. Choose whichever license suits your needs. 

### ode4j contains Java ports of the following software

[ODE/OpenDE](http://www.ode.org/):
Copyright  (c) 2001,2002 Russell L. Smith
All rights reserved.

GIMPACT (part of ODE/OpenDE):
Copyright of GIMPACT (c) 2006 Francisco Leon. C.C. 80087371.
email: projectileman(AT)yahoo.com

[LIBCCD](https://github.com/danfis/libccd):
Copyright (c) 2010 Daniel Fiser <danfis(AT)danfis.cz>;
3-clause BSD License

[Turbulenz Engine](https://github.com/turbulenz/turbulenz_engine):
Copyright (c) 2009-2014 Turbulenz Limited; MIT License
