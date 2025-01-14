

## Galaga in ~~OPENGL~~ SDL?
#### Questions
Is opengl the best choice for this? - No, use SDL
Resources for this project?
Is this a good way to learn C?
#### Resources
- [LearnOpenGL - OpenGL](https://learnopengl.com/Getting-started/OpenGL)
- [SDL2/Introduction - SDL Wiki](https://wiki.libsdl.org/SDL2/Introduction)
- [Space Invaders 1978 callgraph](https://blog.loadzero.com/demo/si79cs3.html)
- Interesting article about memory alignment - [link](https://hps.vi4io.org/_media/teaching/wintersemester_2013_2014/epc-14-haase-svenhendrik-alignmentinc-paper.pdf)
- Sample implementation of Galaga in C using ncurses [Index of ~ggolish/current-courses/cs256/code/projects/galaga](https://cs.indstate.edu/~ggolish/past-courses/cs256-summer2019/code/?dir=./projects/galaga)
- Interesting guide on C programming https://beej.us/guide/bgc/html/split/hello-world.html
- Article on spatial partitioning to make bullet collision more efficient https://gameprogrammingpatterns.com/spatial-partition.html
	- Quad trees for collisions? [here](https://jimkang.com/quadtreevis/) <- interactive site
	- Really helpful step by step breakdown of quad tree operations as well as interactive simulations [here](https://opendsa-server.cs.vt.edu/ODSA/Books/Everything/html/PRquadtree.html)
	- Benchmarks of different spatial partitioning algorithms [here](https://0fps.net/2015/01/23/collision-detection-part-3-benchmarks/). The conclusion is that quad trees might not be the fastest, oh well. Too late. I should've done a grid system, maybe next time.
	- [Quad trees with values that span multiple quadrants](https://pvigier.github.io/2019/08/04/quadtree-collision-detection.html)
	- [C Structure Packing](http://www.catb.org/esr/structure-packing/)
#### Progress
###### Setup and initialization
Using SDL3 for long term support. Build from source. Should probably figure out a build system.

Hopefully I can use SDL as a dynamically linked library. [Chapter 16. Using Libraries with GCC | Red Hat Product Documentation](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/7/html/developer_guide/gcc-using-libraries#gcc-using-libraries_using-library-gcc) This is a good resource from Red Hat on Dynamic vs Static linking

- Processes for static vs dynamic linking are pretty much the same. Just for static you delete the `.dylib` or `.so` file in your `lib` directory to force the linker to use the `.a` which will statically link the library


### Handling Collisions
We need to check to see if any of the currently fired bullets have hit any of the currently spawned enemies. Naturally the brute force method for this requires looping through every single bullet and then for every bullet loop through every enemy. 

# Quad Tree Image Compression?
### Resources
- Good article on quad trees https://romanglushach.medium.com/what-is-a-quadtree-and-how-it-works-6286791fb46a
- Interesting blog about animations and what not - https://lisyarus.github.io/blog/