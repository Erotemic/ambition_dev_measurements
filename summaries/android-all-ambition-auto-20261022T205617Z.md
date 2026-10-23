# Android profile summary

## Status
- profile-build: 0
- simpleperf-record: 0
- adb-pull-perf: 0
- simpleperf-device-callgraph: 0
- simpleperf-device-flat-comm-dso-symbol: 0
- simpleperf-host-callgraph: 127
- simpleperf-host-flat-comm-dso-symbol: 127
- simpleperf-stat: 0
- gfxinfo-after-record: 0
- gfxinfo-framestats-after-record: 0
- perf.data bytes: 4391374

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 9721. Samples lost: 0.

## Top simpleperf report lines
```text
5.35%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.05%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.01%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.89%     android_main     /data/app/~~oWv_cL9EZQLZhCgRAyIcpA==/org.erotemic.ambition.sandbox-Rby0aYdREeBJov5xBnll9Q==/lib/arm64/libambition_app.so  libambition_app.so[+b394d94]
0.84%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memcmp_aarch64
0.82%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
0.40%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            memset
0.38%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa4879435f0]
0.37%     AudioTrack       /apex/com.android.runtime/lib64/bionic/libm.so                                                                            __powf_finite
0.35%     Compute Task Po  /data/app/~~oWv_cL9EZQLZhCgRAyIcpA==/org.erotemic.ambition.sandbox-Rby0aYdREeBJov5xBnll9Q==/lib/arm64/libambition_app.so  libambition_app.so[+b394d94]
0.35%     android_main     /data/app/~~oWv_cL9EZQLZhCgRAyIcpA==/org.erotemic.ambition.sandbox-Rby0aYdREeBJov5xBnll9Q==/lib/arm64/libambition_app.so  libambition_app.so[+13d604e0]
0.34%     android_main     /data/app/~~oWv_cL9EZQLZhCgRAyIcpA==/org.erotemic.ambition.sandbox-Rby0aYdREeBJov5xBnll9Q==/lib/arm64/libambition_app.so  libambition_app.so[+13d604e8]
0.32%     android_main     /data/app/~~oWv_cL9EZQLZhCgRAyIcpA==/org.erotemic.ambition.sandbox-Rby0aYdREeBJov5xBnll9Q==/lib/arm64/libambition_app.so  libambition_app.so[+f064884]
0.29%     Compute Task Po  /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.27%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            scudo::Allocator<scudo::AndroidConfig, &(scudo_malloc_postinit)>::deallocate(void*, scudo::
0.26%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!097ece140a689b3ba2b5661cb74dce!4783c89!
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]  110.504s-115.509s frames=85 p50=51.7ms p95=120.4ms p99=134.5ms max=145.2ms archetypes=2308 (+0) entities=7064
[frame-census]  115.509s-120.549s frames=73 p50=66.8ms p95=117.2ms p99=128.2ms max=132.6ms archetypes=2308 (+0) entities=7109
[frame-census]  120.549s-125.619s frames=70 p50=60.6ms p95=137.0ms p99=144.4ms max=146.7ms archetypes=2308 (+0) entities=7109
[frame-census]  125.619s-130.647s frames=96 p50=42.0ms p95=136.3ms p99=167.1ms max=168.4ms archetypes=2308 (+0) entities=2339
[frame-census]  130.647s-135.696s frames=119 p50=41.1ms p95=62.4ms p99=68.9ms max=69.1ms archetypes=2308 (+0) entities=2345
[frame-census]  135.696s-140.710s frames=126 p50=38.7ms p95=60.5ms p99=64.7ms max=90.0ms archetypes=2308 (+0) entities=2106
[frame-census]  140.710s-145.719s frames=202 p50=23.9ms p95=39.8ms p99=46.5ms max=64.3ms archetypes=2308 (+0) entities=2235
[frame-census]  145.719s-150.737s frames=204 p50=23.2ms p95=41.6ms p99=47.3ms max=67.1ms archetypes=2308 (+0) entities=2188
[frame-census]  150.737s-155.756s frames=203 p50=24.1ms p95=40.3ms p99=52.9ms max=60.1ms archetypes=2308 (+0) entities=2252
[frame-census]  155.756s-160.792s frames=221 p50=21.0ms p95=37.0ms p99=63.3ms max=121.2ms archetypes=2308 (+0) entities=2240
[frame-census]  160.792s-165.822s frames=208 p50=22.8ms p95=37.1ms p99=53.0ms max=78.3ms archetypes=2308 (+0) entities=1953
[frame-census]  165.822s-170.836s frames=226 p50=21.2ms p95=35.3ms p99=42.6ms max=43.2ms archetypes=2308 (+0) entities=1955
```

- hub games: all

## simpleperf stat excerpt
```text
Performance counter statistics:
#            count  event_name         # count / runtime
  95033.788101(ms)  cpu-clock          #             
           723,074  context-switches   # 7.562 K/sec 
            79,204  page-faults        # 828.359 /sec
Total test time: 60.033450 seconds.
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
/home/joncrall/code/ambition/target/android/ambition_platformer2d_actor_monolith_android/app/build/intermediates/merged_jni_libs/debug/mergeDebugJniLibFolders/out/arm64-v8a/libambition_app.so
/home/joncrall/code/ambition/target/android/ambition_platformer2d_actor_monolith_android/app/build/intermediates/merged_native_libs/debug/mergeDebugNativeLibs/out/lib/arm64-v8a/libambition_app.so
/home/joncrall/code/ambition/target/android/ambition_platformer2d_actor_monolith_android/app/build/intermediates/stripped_native_libs/debug/stripDebugDebugSymbols/out/lib/arm64-v8a/libambition_app.so
/home/joncrall/code/ambition/target/android/ambition_platformer2d_actor_monolith_android/app/src/main/jniLibs/arm64-v8a/libambition_app.so
/home/joncrall/code/ambition/target/release/deps/libambition_app.so
```
