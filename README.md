# Project 2
## Introduction
This project is to animate a helicopter and look at it in two different 3D views.

## Requirements:
1. Draw a helicopter. (Don't worry -- this won't be as hard as it sounds.)
2.
    - The helicopter's 2 blades must be scaled properly.
    - The helicopter's 2 blades must be oriented properly.
    - The helicopter's 2 blades must be rotating properly.
4. Allow two views: an "Outside" view of the entire scene and an "Inside" view from the helicopter cockpit. Toggle between them with a pop-up menu. (You can also use keyboard hits, but in-addition-to, not instead-of.) For each view, use a different call to **gluLookAt( )** to position the eye. A good eye poition for your Inside View is (-0.4, 1.8, -4.9) . A good look-at position for your Inside View is (-0.4, 1.8, -10.) .
5. Keep the same Xrot, Yrot, and Scale features as we've used before, **but only in the Outside View**. Do not use Xrot, Yrot, and Scale in the Inside View.
6. Use **gluPerspective( )**, not **glOrtho( )**.
7. Put some sort of 3D scene for your Inside Eye to see while looking outside the helicopter (off in the -Z direction). One or more GLUT wireframe objects might work well. Or, whatever you did in the first project. Or some sort of colored grid. Or, all of these things.
8. Use the graphics programming strategy where the **Display( )** function looks at a collection of global variables and draws the scene correctly. The other parts of the program simply set the global variables and post a redisplay.
9. The code for creating the helicopter geometry is shown below in the **Geometry** section. The helicopter body extends along the Z axis. The heicopter cockpit points in -Z.
10. There must be two blades drawn on the helicopter body: a large blade on the roof, oriented in the X-Z plane, and a small one on the tail, oriented in the Y-Z plane. Create a single blade display list and then transform and instance it twice to get the two blades in the scene. The code for creating the single blade geometry is shown below in the **Geometry** section. The single blade is in the X-Y plane, centered at (0.,0.,0.), with a radius of 1.0.
11.
    - The top helicopter blade is to have a final radius of **5.0** and be attached at: **(0.,2.9,-2.)**.
    - The rear helicopter blade is to have a final radius of **3.0** and be attached at: **(.5,2.5,9.)**.
11. Both blades need to rotate. The rotation rate is up to you, but the smaller one must rotate **2X** as fast as the larger one. That is, for whatever amount you advance the rotation the large blade's rotation angle, advance the small blade's rotation angle **2X** that amount.
12. Pay close attention to the overall transformation sequence. You can re-use some of the transformations that you have already created by using **glPushMatrix( )** and **glPopMatrix( )**.
13. Parameterize your scene as much as you can with `#define`'s or `const`'s, It makes it easier to make changes later.
14. Use GLUT pop-up menus for all options.

## Positioning Everything:
- Remember how we did it in class: write down in words what you want to happen and then program it backwards.
- For the helicopter, you might have a word-transformation sequence like this:
  1. If you are in the Outside view, apply the Xrot, Yrot, and Scale transformations as before.
  2. Assign a good eye, look, and up position with **gluLookAt**.
  3. Then draw the helicopter.
- For a blade, you might have a word-transformation sequence like this:
  1. Scale the blade to be the right size.
  2. Rotate the blade to be in its proper orientation.
  3. Rotate the blade by its current spinning rotation angle.
  4. Translate the blade to where it needs to be on the helicopter.
  5. If you are in the Outside view, apply the Xrot, Yrot, and Scale transformations as before.
  6. Assign a good eye, look, and up position with **gluLookAt**.
  7. Then draw the blade.
  8. and so on.

## Supplying the Geometry
- [Right-click here](http://cs.oregonstate.edu/~mjb/cs550/Projects//heli.550) and select Save Link As... (or something like that) to get the file called heli.550. Save it into your project folder and then **#include** it in your C/C++ code just after your list of global variables, like this: `#include "heli.550"`
- The **heli.550** file has the structure definitions in it, so you won't need to define them yourself.
- If you want to draw a wireframe helicopter, use the following code when you create your helicopter display list:
```
HeliList = glGenLists( 1 );
glNewList( HeliList, GL_COMPILE );
int i;
struct edge *ep;
struct point *p0, *p1;

glPushMatrix( );
glTranslatef( 0., -1., 0. );
glRotatef(  97.,   0., 1., 0. );
glRotatef( -15.,   0., 0., 1. );
glBegin( GL_LINES );
	for( i=0, ep = Heliedges; i < Helinedges; i++, ep++ )
	{
		p0 = &Helipoints[ ep->p0 ];
		p1 = &Helipoints[ ep->p1 ];
		glVertex3f( p0->x, p0->y, p0->z );
		glVertex3f( p1->x, p1->y, p1->z );
	}
glEnd( );
glPopMatrix( );
glEndList( );
```

- If you want to draw a polygon helicopter, use the following code when you create your helicopter display list:
```
HeliList = glGenLists( 1 );
glNewList( HeliList, GL_COMPILE );
int i;
struct point *p0, *p1, *p2;
struct tri *tp;
float p01[3], p02[3], n[3];

glPushMatrix( );
glTranslatef( 0., -1., 0. );
glRotatef(  97.,   0., 1., 0. );
glRotatef( -15.,   0., 0., 1. );
glBegin( GL_TRIANGLES );
	for( i=0, tp = Helitris; i < Helintris; i++, tp++ )
	{
		p0 = &Helipoints[ tp->p0 ];
		p1 = &Helipoints[ tp->p1 ];
		p2 = &Helipoints[ tp->p2 ];

		// fake "lighting" from above:

		p01[0] = p1->x - p0->x;
		p01[1] = p1->y - p0->y;
		p01[2] = p1->z - p0->z;
		p02[0] = p2->x - p0->x;
		p02[1] = p2->y - p0->y;
		p02[2] = p2->z - p0->z;
		Cross( p01, p02, n );
		Unit( n, n );
		n[1] = fabs( n[1] );
		n[1] += .25;
		if( n[1] > 1. )
			n[1] = 1.;
		glColor3f( 0., n[1], 0. );

		glVertex3f( p0->x, p0->y, p0->z );
		glVertex3f( p1->x, p1->y, p1->z );
		glVertex3f( p2->x, p2->y, p2->z );
	}
glEnd( );
glPopMatrix( );
glEndList( );
```

In the **glColor3f( )** call, take whatever R, G, B triple you want the helicopter to have and scale them all by n[1]. In the above example, Joe Graphics wanted a green helicopter.

Then in Display( ), say:
`glCallList( HeliList );`
where you used to call up the BoxList.

- To create the single blade, from which the other two blades will be instanced, use the following code when you create your blade display list:
```
// blade parameters:

#define BLADE_RADIUS		 1.0
#define BLADE_WIDTH		 0.4

// draw the helicopter blade with radius BLADE_RADIUS and
//	width BLADE_WIDTH centered at (0.,0.,0.) in the XY plane

BladeList = glGenLists( 1 );
glNewList( BladeList, GL_COMPILE );
glBegin( GL_TRIANGLES );
	glVertex2f(  BLADE_RADIUS,  BLADE_WIDTH/2. );
	glVertex2f(  0., 0. );
	glVertex2f(  BLADE_RADIUS, -BLADE_WIDTH/2. );

	glVertex2f( -BLADE_RADIUS, -BLADE_WIDTH/2. );
	glVertex2f(  0., 0. );
	glVertex2f( -BLADE_RADIUS,  BLADE_WIDTH/2. );
glEnd( );
glEndList( );
```

Then glCallList the BladeList twice in Display( ) along with the appropriate glPushMatrix, glPopMatrix, glScalef, glRotatef, and glTranslatef calls.

## Getting Started:
Not sure where to start? Read on!
1. Draw the helicopter at the origin.
2. Draw at least one other scene element somewhere out along -Z. This is where the Inside View will be looking.
3. Start by using the stationary Outside View to view the scene. Give gluLookAt( ) some good values.
    - Play with these so that when your program starts up, your are seeing the helicopter and your whole scene from a good angle.
4. Pay careful attention to:
    - Make the near clipping plane location very small, e.g., 0.1
    - Make the far clipping plane location big-ish (not huge), e.g., 1000.
    - Be sure to set some non-BLACK color right before drawing things.
    - Be careful how far the eye is from the helicopter. Too close and you might get the helicopter clipped off. Too far and the helicopter will look like a little fly speck.
5. After that works, designate **Animate( )** as the Idle Function in InitGraphics( ). Have a global variable, say **BladeAngle**, that gets incremented in **Animate( )**. Use that variable in **glRotatef( )** calls in **Display( )** to rotate the blades. Be sure that **Animate( )** posts a re-display.
6. An even better way to do the animation is to put this code up in the global variables:
```
float Time;
#define MS_IN_THE_ANIMATION_CYCLE	10000
```
and this code in Animate( ):
```
int ms = glutGet( GLUT_ELAPSED_TIME );	// milliseconds
ms  %=  MS_IN_THE_ANIMATION_CYCLE;
Time = (float)ms  /  (float)MS_IN_THE_ANIMATION_CYCLE;        // [ 0., 1. )
```

where **MS_IN_THE_ANIMATION_CYCLE** is how many milliseconds are in the animation cycle. (10000 ms = 10 seconds, in this case.) This sets **Time** to be between 0. and 1., which you can then use to set animation parameters in the Display( ) function. For example:
```
glRotatef( 360.*Time,   . . . );
```
The advantage of this is that you will get the same number of milliseconds in the animation cycle regardless of how fast or slow the CPU you run this on is.

7. After that works, add the Inside View by testing what view mode you are in and then using a different call to **gluLookAt( )**. Don't use Xrot, Yrot, and Scale if you are in the Inside Mode. (You can control this with an if-statement.)

## Those Vector-Manipulation Functions
The lighting of the helicopter surfaces use two functions, **Cross( )** and **Unit( )**. They are in your sample code already.

## A Debugging Suggestion:
- One thing that has always helped Joe Graphics debug animation programs is to have a "freeze" option, toggled with the '**f**' key. This freezes the animation so you can really look at your helicopter and blades and see if they are being drawn correctly. Remember what the current freeze status is with a boolean global variable:
```
bool	Frozen;
```
Set **Frozen** to false in **Reset( )**. Then, freezing the animation is just a matter is setting the Idle Function to **NULL**. To un-freeze it, set the Idle Function back to **Animate( )**. So, in the **Keyboard( )** callback, you could say something like:
```
case 'f':
case 'F':
	Frozen = ! Frozen;
	if( Frozen )
		glutIdleFunc( NULL );
	else
		glutIdleFunc( Animate );
	break;
```

## Turn-in:
Use Canvas to turn in:
1. Your .cpp file
2. A short PDF report containing:
    - Your name
    - Your email address
    - Project number and title
    - A description of what you did to get the display you got
    - A cool-looking screen shot from your program showing both Outside and Inside views.
    - The link to the Kaltura video demonstrating that your project does what the requirements ask for. If you can, we'd appreciate it if you'd narrate your video so that you can tell us what it is doing.
3. To see how to turn these files in to Canvas, go to our Project Notes noteset, and go the the slide labeled **How to Turn In a Project on Canvas**.
4. Be sure that your video's permissions are set to unlisted.. The best place to set this is on the OSU Media Server.
5. A good way to test your video's permissions is to ask a friend to try to open the same video link that you are giving us.
6. The video doesn't have to be made with Kaltura. Any similar tool will do.

## Grading:
Feature | Points
---|---
Correctly draw the helicopter body | 15
Correctly scale the blades | 20
Correctly position the blades | 20
Correctly rotate the blades | 20
Recognizable Inside View | 25
**Potential Total** | **100**
