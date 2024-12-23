

## Space Invaders in ~~OPENGL~~ SDL?
#### Questions
Is opengl the best choice for this? - No, use SDL
Resources for this project?
Is this a good way to learn C?
#### Resources
- [LearnOpenGL - OpenGL](https://learnopengl.com/Getting-started/OpenGL)
- [SDL2/Introduction - SDL Wiki](https://wiki.libsdl.org/SDL2/Introduction)
- [Space Invaders 1978 callgraph](https://blog.loadzero.com/demo/si79cs3.html)
- Interesting article about memory alignment - [link](https://hps.vi4io.org/_media/teaching/wintersemester_2013_2014/epc-14-haase-svenhendrik-alignmentinc-paper.pdf)

#### Progress
###### Setup and initialization
Using SDL3 for long term support. Build from source. Should probably figure out a build system.

Hopefully I can use SDL as a dynamically linked library. [Chapter 16. Using Libraries with GCC | Red Hat Product Documentation](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/7/html/developer_guide/gcc-using-libraries#gcc-using-libraries_using-library-gcc) This is a good resource from Red Hat on Dynamic vs Static linking

- Processes for static vs dynamic linking are pretty much the same. Just for static you delete the `.dylib` or `.so` file in your `lib` directory to force the linker to use the `.a` which will statically link the library