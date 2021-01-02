# 在MacOS Catalina上编译OpenJDK 8

### **错误**

1. **HEAP\_REGION\_SET\_FORCE\_VERIFY undefined**

   ```bash
    /Users/neverland/jvm/jdk8u/hotspot/src/share/vm/gc_implementation/g1/heapRegionSet.hpp:38:38: note: expanded from macro 'HEAP_REGION_SET_FORCE_VERIFY'
    #define HEAP_REGION_SET_FORCE_VERIFY defined(ASSERT)
   ```

   将 /Users/neverland/jvm/jdk8u/hotspot/src/share/vm/gc\_implementation/g1/heapRegionSet.hpp 中的

   ```cpp
    #define HEAP_REGION_SET_FORCE_VERIFY defined(ASSERT)
   ```

   改为

   ```cpp
    #define HEAP_REGION_SET_FORCE_VERIFY 0
   ```

2. **ordered comparison between pointer and zero**

   ```bash
    /Users/neverland/jvm/jdk8u/hotspot/src/share/vm/opto/lcm.cpp:52:35: error: ordered comparison between pointer and zero ('address' (aka 'unsigned char *') and 'int') 
    if (Universe::narrow_oop_base() > 0) { // Implies UseCompressedOops.
      ~~~~~~~~~~~~~~~~~~~~~~~~~~~ ^ ~
   ```

   将 /Users/neverland/jvm/jdk8u/hotspot/src/share/vm/opto/lcm.cpp 中的

   ```cpp
     if (Universe::narrow_oop_base() > 0)
   ```

   改为

   ```cpp
     if (Universe::narrow_oop_base() != 0)
   ```

3. **ordered comparison between pointer and zero \(‘const TypeInt \*’ and ‘int’\)**

   ```bash
    /Users/neverland/jvm/jdk8u/hotspot/src/share/vm/opto/loopPredicate.cpp:781:73: error: ordered comparison between pointer and zero ('const TypeInt *' and 'int')
    assert(rng->Opcode() == Op_LoadRange || _igvn.type(rng)->is_int() >= 0, "must be");
   ```

   将 /Users/neverland/jvm/jdk8u/hotspot/src/share/vm/opto/loopPredicate.cpp 中的

   ```cpp
    _igvn.type(rng)->is_int() >= 0
   ```

   改为

   ```cpp
    _igvn.type(rng)->is_int()->_lo >= 0
   ```

4. **ordered comparison between pointer and zero \(‘char \*’ and ‘int’\)**

   ```bash
    /Users/neverland/jvm/jdk8u/hotspot/src/share/vm/runtime/virtualspace.cpp:345:14: error: ordered comparison between pointer and zero ('char *' and 'int')
    if (base() > 0) {
     ~~~~~~ ^ ~
     Compiling /Users/nev
   ```

   将 /Users/neverland/jvm/jdk8u/hotspot/src/share/vm/runtime/virtualspace.cpp 中的

   ```cpp
    if (base() > 0) {
   ```

   改为

   ```text
    if (base() ！= 0) {
   ```

5. **Undefined symbols for architecture x86\_64** 

   ```bash
    Undefined symbols for architecture x86_64:
    "_attachCurrentThread", referenced from:
      +[ThreadUtilities getJNIEnv] in ThreadUtilities.o
      +[ThreadUtilities getJNIEnvUncached] in ThreadUtilities.o
      ld: symbol(s) not found for architecture x86_64
      clang: error: linker command failed with exit code 1 (use -v to see invocation)
   ```

    将 /Users/neverland/jvm/jdk8u/jdk/src/macosx/native/sun/osxapp/ThreadUtilities.m 中的

   ```cpp
    inline void attachCurrentThread(void** env) {
   ```

    改为

   ```cpp
    void attachCurrentThread(void** env) {
   ```

6. **runtime fatal error**

   ```bash
   neverland@neverlands-MacBook-Pro bin % ./java -version
   openjdk version "1.8.0-internal-debug"
   OpenJDK Runtime Environment (build 1.8.0-internal-debug-neverland_2020_06_07_15_48-b00)
   OpenJDK 64-Bit Server VM (build 25.60-b23-debug, mixed mode)
   #
   # A fatal error has been detected by the Java Runtime Environment:
   #
   #  SIGILL (0x4) at pc=0x000000010d814508, pid=60529, tid=9219
   #
   # JRE version: OpenJDK Runtime Environment (8.0) (build 1.8.0-internal-debug-neverland_2020_06_07_15_48-b00)
   # Java VM: OpenJDK 64-Bit Server VM (25.60-b23-debug mixed mode bsd-amd64 compressed oops)
   # Problematic frame:
   # V  [libjvm.dylib+0xa14508]  PerfData::~PerfData()+0x8
   #
   # Failed to write core dump. Core dumps have been disabled. To enable core dumping, try "ulimit -c unlimited" before starting Java again
   #
   # An error report file with more information is saved as:
   # /Users/neverland/jvm/jdk8u/build/macosx-x86_64-normal-server-slowdebug/jdk/bin/hs_err_pid60529.log
   #
   # If you would like to submit a bug report, please visit:
   #   http://bugreport.java.com/bugreport/crash.jsp
   #

   [error occurred during error reporting , id 0x4]
   ```

    将 /Users/neverland/jvm/jdk8u/hotspot/src/share/vm/runtime/perfData.cpp 中 void PerfDataManager::destroy\(\) 的 delete p 注释掉

   ```cpp
   void PerfDataManager::destroy() {

     if (_all == NULL)
       // destroy already called, or initialization never happened
       return;

     for (int index = 0; index < _all->length(); index++) {
       PerfData* p = _all->at(index);
   //    delete p;
     }
   ```

### 完成

```bash
## Finished docs (build time 00:02:00)

----- Build times -------
Start 2020-06-07 16:46:16
End   2020-06-07 16:56:57
00:00:21 corba
00:00:40 demos
00:02:00 docs
00:02:11 hotspot
00:01:21 images
00:00:13 jaxp
00:00:18 jaxws
00:02:59 jdk
00:00:28 langtools
00:00:10 nashorn
00:10:41 TOTAL
-------------------------
```

### 验证

```bash
neverland@neverlands-MacBook-Pro bin % ./java -version 
openjdk version "1.8.0-internal-debug"
OpenJDK Runtime Environment (build 1.8.0-internal-debug-neverland_2020_06_07_15_48-b00)
OpenJDK 64-Bit Server VM (build 25.60-b23-debug, mixed mode)
neverland@neverlands-MacBook-Pro bin % pwd
/Users/neverland/jvm/jdk8u/build/macosx-x86_64-normal-server-slowdebug/jdk/bin
```

### 参考

[https://imkiva.com/blog/2018/02/body/building-openjdk8-on-macos/](https://imkiva.com/blog/2018/02/body/building-openjdk8-on-macos/)

[https://xixfeng.github.io/2019/08/30/mac%E7%BC%96%E8%AF%91openjdk9/](https://xixfeng.github.io/2019/08/30/mac%E7%BC%96%E8%AF%91openjdk9/)

[https://blog.csdn.net/wuchengguo0671/article/details/90105316](https://blog.csdn.net/wuchengguo0671/article/details/90105316)

[https://www.jianshu.com/p/d9a1e1072f37](https://www.jianshu.com/p/d9a1e1072f37)

