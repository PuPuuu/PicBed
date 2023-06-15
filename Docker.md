## Dockerfile

每执行一次RUN就会在docker上新建一层镜像

容器启动时执行的命令 需要用CMD来执行一些容器启动时的命令，注意与RUN的区别，CMD是在`docker run`执行的时候使用，而RUN则是在`docker build`的时候使用，还有，**划重点**，一个Dockerfile只有最后一个CMD会起作用。