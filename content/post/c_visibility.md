+++
title = "C 语言符号可见性"
author = ["alilili"]
lastmod = 2023-09-18T00:27:16+08:00
draft = false
+++

{{< highlight C >}}
  __attribute__((visibility("default")))
  __attribute__ ((visibility("hidden")))
{{< /highlight >}}

`main.cc:`

{{< highlight C >}}
extern "C" void fun1();
extern "C" void fun2();
int main()
{
  fun1();
  fun2();
  return 0;
}
{{< /highlight >}}

`Makefile:`

{{< highlight makefile >}}
all:test
test:main.o libvisibility.so
	g++ -o test main.o -lvisibility -L .
main.o::main.cc
	g++ -c main.cc
libvisibility.so:visibility1.o visibility2.o
	g++ -shared -o libvisibility.so visibility1.o visibility2.o
visibility1.o:visibility1.cc
	g++ -fvisibility=hidden -fPIC -c visibility1.cc
visibility2.o:visibility2.cc
	g++ -fvisibility=hidden -fPIC -c visibility2.cc
clean:
	rm -f *.o *.so test
{{< /highlight >}}

`visibility1.cc:`

{{< highlight C >}}
  #include <stdio.h>
  extern "C" void fun1()
  {
    printf("in %s\n",__FUNCTION__);
  }
  __attribute__ ((visibility("hidden"))) void fun1();
  //若编译此文件时使用了参数-fvisibility=hidden，则此行可以省略
{{< /highlight >}}

`visibility2.cc:`

{{< highlight C >}}
  #include <stdio.h>
  extern "C" void fun1();
  extern "C" void fun2()
  {
    fun1();
    printf("in %s\n",__FUNCTION__);
  }
  __attribute__ ((visibility("default"))) void fun2();
  //若编译此文件时没有使用参数-fvisibility或设置参数-fvisibility=default，则此行可以省略
{{< /highlight >}}
