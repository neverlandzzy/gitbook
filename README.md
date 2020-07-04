# 在MacOS Catalina上编译OpenJDK 8

## **错误**

1. **HEAP\_REGION\_SET\_FORCE\_VERIFY undefined**

   ```cpp
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

    ```cpp
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
3. **ordered comparison between pointer and zero (‘const TypeInt *’ and ‘int’)**
    
    ```cpp
    /Users/neverland/jvm/jdk8u/hotspot/src/share/vm/opto/loopPredicate.cpp:781:73: error: ordered comparison between pointer and zero ('const TypeInt *' and 'int')
    assert(rng->Opcode() == Op_LoadRange || _igvn.type(rng)->is_int() >= 0, "must be");
    ```
    

