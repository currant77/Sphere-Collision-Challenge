# Sphere-Collision-Challenge
Design for a program that calculates and illustrates the point of collision between a moving sphere and a stationary sphere. 
Written in C# for Unity. 


Instructions:
	- Use Q / A keys to increase / decrease the size of the moving sphere
	- Use W / S keys to increase / decrease the size of the stationary sphere
	- Use E/ D keys to increase / decrease the distance between the two spheres
	- Use arrow keys to adjust the direction of the moving sphere. Use right / 
	  left arrow keys to sweep the angle of movement horizontally; the up / down 
	  keys to sweep it vertically

Assumptions / Constraints:
	- Made both spheres so that they could not become smaller than MIN_DIAMETER is size 
	  (prevent sphere from disappearing or their scale becoming negative)
	- Made it so that the initial position of the two spheres could not intersect 
	  (collision doesn't really make sense if the sphere are already overlapping)
	- Restricted initial positions of the two spheres to a single axis, but this
	  could easily be extended to a general case
