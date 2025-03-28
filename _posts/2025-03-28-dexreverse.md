---
title: "一些古老的脱壳知识"
date: 2025-03-28
---

## so文件的背景知识

共享库的加载和初始化是由动态链接器（dynamic linker，如 ld.so）负责的。为了支持库的初始化操作，编译器和链接器会将一些特殊的代码段组织到特定的段中，例如 .init 和 .fini。

.init 段：包含库的初始化代码，在共享库被加载时执行。
.fini 段：包含库的清理代码，在共享库被卸载时执行。
这些段的作用类似于 C++ 中的全局对象构造函数和析构函数。

init_array 的出现是为了替代传统的 .init 段，提供更灵活的初始化机制。相比于 .init 段只能包含一个单一的初始化入口点，init_array 可以包含多个初始化函数，并且支持更复杂的初始化逻辑。

## JNI函数的绑定机制

1. 当Java程序通过System.loadLibrary加载一个动态链接库时，JVM会执行以下步骤：

    1. 加载共享库 ：JVM会调用操作系统的动态链接器（如dlopen）将共享库加载到内存中。
    2. 解析符号表 ：JVM会读取共享库的符号表，查找符合JNI命名规则的函数。
    3. 命名规则为 Java_com_example_MyClass_myMethod
   
2. 除了自动检索外，JNI还支持通过RegisterNatives显式注册本地方法。这种方式允许开发者手动将Java方法与本地函数绑定

```
static JNINativeMethod methods[] = {
    {"myMethod", "()V", (void*)&native_myMethod}
};

JNIEXPORT jint JNICALL JNI_OnLoad(JavaVM* vm, void* reserved) {
    JNIEnv* env;
    if (vm->GetEnv((void**)&env, JNI_VERSION_1_6) != JNI_OK) {
        return -1;
    }
    jclass clazz = env->FindClass("com/example/MyClass");
    env->RegisterNatives(clazz, methods, sizeof(methods) / sizeof(methods[0]));
    return JNI_VERSION_1_6;
}
```

方式2通常通过JNI_OnLoad函数调用. JNI_OnLoad的被调用实际是JVM对Java部分完成了初始化之后, 刚好能够正常实现JNI绑定的时机.

## so代码隐藏

通常在init段里调用一个解密函数, 将JNI_OnLoad函数指向的空间的代码解密. 这样避免了对JNI_OnLoad函数的静态分析. 而JNI函数又是通过JNI_OnLoad函数里的代码动态注册的, 因此可以将JNI函数的信息在静态代码中隐藏.

## DEX文件加壳基本原理

将DEX加密后放在资源文件中. 然后在APP运行时, load解密后动态加载.

此外, 通过hook自身空间的系统函数, 可以将资源文件等也进行加密, 实际上就是约等于改变系统初始化的工作流程, 自定义一些hook操作.

复杂一点的, 可以自定义DexClassLoader. 这样DEX文件本身的结构就不需要符合标准了. 也可以将一些类的实现抽取到so中, 在运行时通过ClassLoader动态执行对应的代码逻辑.

https://github.com/zyq8709/DexHunter DexHunter通过在JVM层面, 利用DefineClass和FindClass这些系统本身的解析函数, 来遍历内存中所有有效的类, 抽取里面的指令来实现脱壳, 绕过了外层的这些加密逻辑.

进一步的加壳方案, 将Method的实现代码, 只有被执行到的时候, 才动态解密, 否则不释放. 这样DexHunter这种在DEX加载完就去dump的方案就没有办法奏效了.

相对的, https://github.com/spriteviki/Dex2oatHunter 在运行时去动态的dump, 只要Method解出来过, 就会被dump下来.

传说中的加壳, Java2C, 通过将smali指令抽出, 转译成等价的C代码, 然后通过编译器将C代码优化, 这样就很难还原了.
 