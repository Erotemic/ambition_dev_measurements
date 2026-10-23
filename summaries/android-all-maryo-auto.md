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
- perf.data bytes: 3406035

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 6849. Samples lost: 0.

## Top simpleperf report lines
```text
4.57%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
4.47%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d52634]
2.85%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d52564]
2.38%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d525d0]
2.37%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d4e3d4]
1.47%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d4e0e4]
1.28%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d4e1d8]
1.24%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
0.98%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.85%     android_main     /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+c793090]
0.53%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d4e0dc]
0.47%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
0.45%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d1250c]
0.44%     android_main     /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+101a0b1c]
0.39%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d124c0]
0.37%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d4e0d4]
0.35%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d434d8]
0.34%     android_main     /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+14d8517c]
0.32%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d1254c]
0.32%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memcmp_aarch64
0.31%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d434f8]
0.31%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d4e56c]
0.29%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d426e4]
0.29%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c370]
0.28%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d4b3b8]
0.28%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d4e204]
0.26%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d127f0]
0.26%     Async Compute T  /data/app/~~texwbZN9l_JLNEJXXk8BsQ==/org.erotemic.ambition.sandbox-5fA7PTrVWDlki7Ro3ZwaZA==/lib/arm64/libambition_app.so  libambition_app.so[+11d4e234]
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]   35.088s-41.215s frames=132 p50=33.9ms p95=51.9ms p99=191.8ms max=1442.5ms archetypes=2064 (+1) entities=5371
[frame-census]   41.215s-46.248s frames=138 p50=32.8ms p95=60.1ms p99=104.7ms max=194.8ms archetypes=2064 (+0) entities=5783
[frame-census]   46.248s-51.261s frames=147 p50=34.0ms p95=46.0ms p99=49.1ms max=52.6ms archetypes=2065 (+1) entities=5764
[frame-census]   51.261s-56.311s frames=73 p50=69.1ms p95=79.6ms p99=81.2ms max=86.6ms archetypes=2065 (+0) entities=5745
[frame-census]   56.311s-61.356s frames=69 p50=70.7ms p95=80.6ms p99=86.0ms max=88.7ms archetypes=2066 (+1) entities=5756
[frame-census]   61.356s-66.369s frames=83 p50=64.9ms p95=79.0ms p99=80.6ms max=81.2ms archetypes=2068 (+2) entities=5762
[frame-census]   66.369s-71.438s frames=70 p50=41.3ms p95=72.0ms p99=143.1ms max=2053.7ms archetypes=2073 (+5) entities=5762
[frame-census]   71.438s-76.439s frames=131 p50=37.6ms p95=56.7ms p99=64.4ms max=76.6ms archetypes=2073 (+0) entities=5759
[frame-census]   76.439s-81.460s frames=96 p50=51.8ms p95=77.4ms p99=79.9ms max=80.2ms archetypes=2073 (+0) entities=5750
[frame-census]   81.460s-86.480s frames=71 p50=69.4ms p95=80.2ms p99=81.8ms max=82.1ms archetypes=2073 (+0) entities=5758
[frame-census]   86.480s-91.512s frames=129 p50=37.1ms p95=58.1ms p99=67.8ms max=73.6ms archetypes=2073 (+0) entities=5789
[frame-census]   91.512s-96.540s frames=139 p50=35.6ms p95=51.0ms p99=60.2ms max=65.4ms archetypes=2073 (+0) entities=5771
```

- hub games: all

## simpleperf stat excerpt
```text
Performance counter statistics:
#            count  event_name         # count / runtime
  86001.617549(ms)  cpu-clock          #             
           386,531  context-switches   # 4.478 K/sec 
            19,799  page-faults        # 229.343 /sec
Total test time: 30.028265 seconds.
```

## gfxinfo excerpt
```text
Total frames rendered: 1
Janky frames: 1 (100.00%)
Janky frames (legacy): 1 (100.00%)
50th percentile: 28ms
90th percentile: 28ms
95th percentile: 28ms
99th percentile: 28ms
Number Missed Vsync: 0
HISTOGRAM: 5ms=0 6ms=0 7ms=0 8ms=0 9ms=0 10ms=0 11ms=0 12ms=0 13ms=0 14ms=0 15ms=0 16ms=0 17ms=0 18ms=0 19ms=0 20ms=0 21ms=0 22ms=0 23ms=0 24ms=0 25ms=0 26ms=0 27ms=0 28ms=1 29ms=0 30ms=0 31ms=0 32ms=0 34ms=0 36ms=0 38ms=0 40ms=0 42ms=0 44ms=0 46ms=0 48ms=0 53ms=0 57ms=0 61ms=0 65ms=0 69ms=0 73ms=0 77ms=0 81ms=0 85ms=0 89ms=0 93ms=0 97ms=0 101ms=0 105ms=0 109ms=0 113ms=0 117ms=0 121ms=0 125ms=0 129ms=0 133ms=0 150ms=0 200ms=0 250ms=0 300ms=0 350ms=0 400ms=0 450ms=0 500ms=0 550ms=0 600ms=0 650ms=0 700ms=0 750ms=0 800ms=0 850ms=0 900ms=0 950ms=0 1000ms=0 1050ms=0 1100ms=0 1150ms=0 1200ms=0 1250ms=0 1300ms=0 1350ms=0 1400ms=0 1450ms=0 1500ms=0 1550ms=0 1600ms=0 1650ms=0 1700ms=0 1750ms=0 1800ms=0 1850ms=0 1900ms=0 1950ms=0 2000ms=0 2050ms=0 2100ms=0 2150ms=0 2200ms=0 2250ms=0 2300ms=0 2350ms=0 2400ms=0 2450ms=0 2500ms=0 2550ms=0 2600ms=0 2650ms=0 2700ms=0 2750ms=0 2800ms=0 2850ms=0 2900ms=0 2950ms=0 3000ms=0 3050ms=0 3100ms=0 3150ms=0 3200ms=0 3250ms=0 3300ms=0 3350ms=0 3400ms=0 3450ms=0 3500ms=0 3550ms=0 3600ms=0 3650ms=0 3700ms=0 3750ms=0 3800ms=0 3850ms=0 3900ms=0 3950ms=0 4000ms=0 4050ms=0 4100ms=0 4150ms=0 4200ms=0 4250ms=0 4300ms=0 4350ms=0 4400ms=0 4450ms=0 4500ms=0 4550ms=0 4600ms=0 4650ms=0 4700ms=0 4750ms=0 4800ms=0 4850ms=0 4900ms=0 4950ms=0
GPU HISTOGRAM: 1ms=0 2ms=0 3ms=1 4ms=0 5ms=0 6ms=0 7ms=0 8ms=0 9ms=0 10ms=0 11ms=0 12ms=0 13ms=0 14ms=0 15ms=0 16ms=0 17ms=0 18ms=0 19ms=0 20ms=0 21ms=0 22ms=0 23ms=0 24ms=0 25ms=0 4950ms=0
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
