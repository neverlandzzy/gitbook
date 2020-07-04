# 在MacOS Catalina上编译OpenJDK 8

#### **错误**

1. HEAP\_REGION\_SET\_FORCE\_VERIFY undefined

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
2. test

