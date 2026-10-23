# Android profile summary

## Status
- profile-build: 0
- simpleperf-record: 0
- adb-pull-perf: 0
- simpleperf-device-callgraph: 124
- simpleperf-device-flat-comm-dso-symbol: 0
- simpleperf-host-callgraph: 127
- simpleperf-host-flat-comm-dso-symbol: 127
- simpleperf-stat: 0
- gfxinfo-after-record: 0
- gfxinfo-framestats-after-record: 0
- perf.data bytes: 8812723

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 23111. Samples lost: 0.

## Top simpleperf report lines
```text
6.00%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.48%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.06%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
0.85%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.77%     android_main     /data/app/~~NtIyRzriwJNQ6uhX6RL22A==/org.erotemic.ambition.sandbox-XZ2DUz72c30U_oRK8giymA==/lib/arm64/libambition_app.so  libambition_app.so[+b5fdf0c]
0.74%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memcmp_aarch64
0.55%     Compute Task Po  /data/app/~~NtIyRzriwJNQ6uhX6RL22A==/org.erotemic.ambition.sandbox-XZ2DUz72c30U_oRK8giymA==/lib/arm64/libambition_app.so  libambition_app.so[+b5fdf0c]
0.45%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa4879435f0]
0.45%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            memset
0.41%     android_main     /data/app/~~NtIyRzriwJNQ6uhX6RL22A==/org.erotemic.ambition.sandbox-XZ2DUz72c30U_oRK8giymA==/lib/arm64/libambition_app.so  libambition_app.so[+140b5b98]
0.37%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            scudo::Allocator<scudo::AndroidConfig, &(scudo_malloc_postinit)>::deallocate(void*, scudo::
0.31%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            scudo::Allocator<scudo::AndroidConfig, &(scudo_malloc_postinit)>::allocate(unsigned long, s
0.29%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796e324]
0.29%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!bb16f0bc38febaa4b4ae4f72493245!4783c89!
0.29%     android_main     /data/app/~~NtIyRzriwJNQ6uhX6RL22A==/org.erotemic.ambition.sandbox-XZ2DUz72c30U_oRK8giymA==/lib/arm64/libambition_app.so  libambition_app.so[+f535760]
0.27%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c370]
0.27%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48892b8a4]
0.26%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            scudo::Allocator<scudo::AndroidConfig, &(scudo_malloc_postinit)>::quarantineOrDeallocateChu
0.26%     Compute Task Po  /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.26%     Compute Task Po  /data/app/~~NtIyRzriwJNQ6uhX6RL22A==/org.erotemic.ambition.sandbox-XZ2DUz72c30U_oRK8giymA==/lib/arm64/libambition_app.so  libambition_app.so[+14139990]
0.26%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!260addb415166054346467f72a0679!4783c89!
0.25%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!097ece140a689b3ba2b5661cb74dce!4783c89!
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]  305.859s-310.875s frames=228 p50=22.4ms p95=33.2ms p99=37.2ms max=40.6ms archetypes=2342 (+0) entities=4708
[frame-census]  310.875s-315.878s frames=255 p50=18.5ms p95=31.9ms p99=37.7ms max=66.0ms archetypes=2342 (+0) entities=2951
[frame-census]  315.878s-320.885s frames=263 p50=18.3ms p95=29.4ms p99=33.9ms max=63.6ms archetypes=2342 (+0) entities=2903
[frame-census]  320.885s-325.896s frames=261 p50=18.0ms p95=30.4ms p99=35.6ms max=69.8ms archetypes=2342 (+0) entities=3031
[frame-census]  325.896s-330.906s frames=266 p50=18.4ms p95=28.3ms p99=35.6ms max=78.0ms archetypes=2342 (+0) entities=2702
[frame-census]  330.906s-335.928s frames=250 p50=19.8ms p95=31.0ms p99=41.1ms max=57.3ms archetypes=2342 (+0) entities=2410
[frame-census]  335.928s-340.935s frames=275 p50=18.0ms p95=27.3ms p99=32.0ms max=37.0ms archetypes=2342 (+0) entities=2412
[frame-census]  340.935s-345.952s frames=251 p50=19.1ms p95=30.8ms p99=41.8ms max=54.6ms archetypes=2342 (+0) entities=2487
[frame-census]  345.952s-350.967s frames=267 p50=18.5ms p95=28.5ms p99=34.0ms max=36.4ms archetypes=2342 (+0) entities=3054
[frame-census]  350.967s-355.968s frames=260 p50=18.6ms p95=30.0ms p99=36.4ms max=62.2ms archetypes=2342 (+0) entities=2915
[frame-census]  355.968s-360.974s frames=258 p50=18.9ms p95=29.4ms p99=34.0ms max=70.9ms archetypes=2342 (+0) entities=2977
[frame-census]  360.974s-365.989s frames=254 p50=18.9ms p95=31.7ms p99=41.3ms max=63.9ms archetypes=2342 (+0) entities=2492
```

- hub games: all

## simpleperf stat excerpt
```text
Performance counter statistics:
#             count  event_name         # count / runtime
  237129.879605(ms)  cpu-clock          #            
          2,310,622  context-switches   # 9.672 K/sec
            287,662  page-faults        # 1.204 K/sec
Total test time: 120.016957 seconds.
```

## gfxinfo excerpt
```text
Total frames rendered: 0
Janky frames: 0 (0.00%)
Janky frames (legacy): 0 (0.00%)
50th percentile: 4950ms
90th percentile: 4950ms
95th percentile: 4950ms
99th percentile: 4950ms
Number Missed Vsync: 0
HISTOGRAM: 5ms=0 6ms=0 7ms=0 8ms=0 9ms=0 10ms=0 11ms=0 12ms=0 13ms=0 14ms=0 15ms=0 16ms=0 17ms=0 18ms=0 19ms=0 20ms=0 21ms=0 22ms=0 23ms=0 24ms=0 25ms=0 26ms=0 27ms=0 28ms=0 29ms=0 30ms=0 31ms=0 32ms=0 34ms=0 36ms=0 38ms=0 40ms=0 42ms=0 44ms=0 46ms=0 48ms=0 53ms=0 57ms=0 61ms=0 65ms=0 69ms=0 73ms=0 77ms=0 81ms=0 85ms=0 89ms=0 93ms=0 97ms=0 101ms=0 105ms=0 109ms=0 113ms=0 117ms=0 121ms=0 125ms=0 129ms=0 133ms=0 150ms=0 200ms=0 250ms=0 300ms=0 350ms=0 400ms=0 450ms=0 500ms=0 550ms=0 600ms=0 650ms=0 700ms=0 750ms=0 800ms=0 850ms=0 900ms=0 950ms=0 1000ms=0 1050ms=0 1100ms=0 1150ms=0 1200ms=0 1250ms=0 1300ms=0 1350ms=0 1400ms=0 1450ms=0 1500ms=0 1550ms=0 1600ms=0 1650ms=0 1700ms=0 1750ms=0 1800ms=0 1850ms=0 1900ms=0 1950ms=0 2000ms=0 2050ms=0 2100ms=0 2150ms=0 2200ms=0 2250ms=0 2300ms=0 2350ms=0 2400ms=0 2450ms=0 2500ms=0 2550ms=0 2600ms=0 2650ms=0 2700ms=0 2750ms=0 2800ms=0 2850ms=0 2900ms=0 2950ms=0 3000ms=0 3050ms=0 3100ms=0 3150ms=0 3200ms=0 3250ms=0 3300ms=0 3350ms=0 3400ms=0 3450ms=0 3500ms=0 3550ms=0 3600ms=0 3650ms=0 3700ms=0 3750ms=0 3800ms=0 3850ms=0 3900ms=0 3950ms=0 4000ms=0 4050ms=0 4100ms=0 4150ms=0 4200ms=0 4250ms=0 4300ms=0 4350ms=0 4400ms=0 4450ms=0 4500ms=0 4550ms=0 4600ms=0 4650ms=0 4700ms=0 4750ms=0 4800ms=0 4850ms=0 4900ms=0 4950ms=0
GPU HISTOGRAM: 1ms=0 2ms=0 3ms=0 4ms=0 5ms=0 6ms=0 7ms=0 8ms=0 9ms=0 10ms=0 11ms=0 12ms=0 13ms=0 14ms=0 15ms=0 16ms=0 17ms=0 18ms=0 19ms=0 20ms=0 21ms=0 22ms=0 23ms=0 24ms=0 25ms=0 4950ms=0
```

## Symbol candidates
```text
# libambition_app.so candidates
/home/joncrall/code/ambition/target/aarch64-linux-android/android-profile/deps/libambition_app.so
/home/joncrall/code/ambition/target/aarch64-linux-android/android-profile/libambition_app.so
/home/joncrall/code/ambition/target/aarch64-linux-android/release/deps/libambition_app.so
/home/joncrall/code/ambition/target/aarch64-linux-android/release/libambition_app.so
/home/joncrall/code/ambition/target/android/ambition_platformer2d_actor_monolith_android/app/build/intermediates/merged_jni_libs/debug/mergeDebugJniLibFolders/out/arm64-v8a/libambition_app.so
/home/joncrall/code/ambition/target/android/ambition_platformer2d_actor_monolith_android/app/build/intermediates/merged_native_libs/debug/mergeDebugNativeLibs/out/lib/arm64-v8a/libambition_app.so
/home/joncrall/code/ambition/target/android/ambition_platformer2d_actor_monolith_android/app/build/intermediates/stripped_native_libs/debug/stripDebugDebugSymbols/out/lib/arm64-v8a/libambition_app.so
/home/joncrall/code/ambition/target/android/ambition_platformer2d_actor_monolith_android/app/src/main/jniLibs/arm64-v8a/libambition_app.so
/home/joncrall/code/ambition/target/release/deps/libambition_app.so
```
