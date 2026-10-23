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
- perf.data bytes: 2781861

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 4489. Samples lost: 0.

## Top simpleperf report lines
```text
4.81%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.89%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.40%     android_main     /data/app/~~oN5qIlgrJ4j5gIUEh5Grmw==/org.erotemic.ambition.sandbox-FtGNUOXrV7uh_bR58J0ISA==/lib/arm64/libambition_app.so  libambition_app.so[+c704354]
0.85%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.74%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
0.49%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memcmp_aarch64
0.49%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            pthread_getspecific
0.38%     android_main     /data/app/~~oN5qIlgrJ4j5gIUEh5Grmw==/org.erotemic.ambition.sandbox-FtGNUOXrV7uh_bR58J0ISA==/lib/arm64/libambition_app.so  libambition_app.so[+14b84c24]
0.36%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796e324]
0.33%     android_main     /data/app/~~oN5qIlgrJ4j5gIUEh5Grmw==/org.erotemic.ambition.sandbox-FtGNUOXrV7uh_bR58J0ISA==/lib/arm64/libambition_app.so  libambition_app.so[+c703f8c]
0.33%     android_main     /data/app/~~oN5qIlgrJ4j5gIUEh5Grmw==/org.erotemic.ambition.sandbox-FtGNUOXrV7uh_bR58J0ISA==/lib/arm64/libambition_app.so  libambition_app.so[+ffed940]
0.33%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa4879435f0]
0.29%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c370]
0.29%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            memset
0.29%     android_main     /data/app/~~oN5qIlgrJ4j5gIUEh5Grmw==/org.erotemic.ambition.sandbox-FtGNUOXrV7uh_bR58J0ISA==/lib/arm64/libambition_app.so  libambition_app.so[+14b84c20]
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]   50.132s-55.136s frames=106 p50=45.6ms p95=66.3ms p99=81.3ms max=85.7ms
[frame-census]   55.136s-60.197s frames=116 p50=42.2ms p95=57.9ms p99=83.5ms max=150.3ms
[frame-census]   60.197s-65.209s frames=103 p50=37.1ms p95=130.9ms p99=172.9ms max=185.6ms
[frame-census]   65.209s-70.253s frames=139 p50=34.8ms p95=52.1ms p99=59.6ms max=60.5ms
[frame-census]   70.253s-75.279s frames=109 p50=44.6ms p95=70.8ms p99=76.5ms max=81.6ms
[frame-census]   75.279s-80.304s frames=181 p50=23.8ms p95=52.1ms p99=69.0ms max=144.7ms
[frame-census]   80.304s-85.332s frames=150 p50=32.9ms p95=47.9ms p99=50.8ms max=51.2ms
[frame-census]   85.332s-90.346s frames=159 p50=30.6ms p95=45.5ms p99=52.4ms max=72.0ms
[frame-census]   90.346s-95.356s frames=178 p50=28.1ms p95=36.6ms p99=39.9ms max=45.1ms
```

- hub games: all

## simpleperf stat excerpt
```text
Performance counter statistics:
#            count  event_name         # count / runtime
  49904.704511(ms)  cpu-clock          #            
           494,232  context-switches   # 9.831 K/sec
            88,190  page-faults        # 1.754 K/sec
Total test time: 30.015957 seconds.
```

## gfxinfo excerpt
```text
Total frames rendered: 4
Janky frames: 2 (50.00%)
Janky frames (legacy): 3 (75.00%)
50th percentile: 61ms
90th percentile: 89ms
95th percentile: 89ms
99th percentile: 89ms
Number Missed Vsync: 0
HISTOGRAM: 5ms=0 6ms=0 7ms=0 8ms=1 9ms=0 10ms=0 11ms=0 12ms=0 13ms=0 14ms=0 15ms=0 16ms=0 17ms=0 18ms=0 19ms=0 20ms=0 21ms=0 22ms=0 23ms=1 24ms=0 25ms=0 26ms=0 27ms=0 28ms=0 29ms=0 30ms=0 31ms=0 32ms=0 34ms=0 36ms=0 38ms=0 40ms=0 42ms=0 44ms=0 46ms=0 48ms=0 53ms=0 57ms=0 61ms=1 65ms=0 69ms=0 73ms=0 77ms=0 81ms=0 85ms=0 89ms=1 93ms=0 97ms=0 101ms=0 105ms=0 109ms=0 113ms=0 117ms=0 121ms=0 125ms=0 129ms=0 133ms=0 150ms=0 200ms=0 250ms=0 300ms=0 350ms=0 400ms=0 450ms=0 500ms=0 550ms=0 600ms=0 650ms=0 700ms=0 750ms=0 800ms=0 850ms=0 900ms=0 950ms=0 1000ms=0 1050ms=0 1100ms=0 1150ms=0 1200ms=0 1250ms=0 1300ms=0 1350ms=0 1400ms=0 1450ms=0 1500ms=0 1550ms=0 1600ms=0 1650ms=0 1700ms=0 1750ms=0 1800ms=0 1850ms=0 1900ms=0 1950ms=0 2000ms=0 2050ms=0 2100ms=0 2150ms=0 2200ms=0 2250ms=0 2300ms=0 2350ms=0 2400ms=0 2450ms=0 2500ms=0 2550ms=0 2600ms=0 2650ms=0 2700ms=0 2750ms=0 2800ms=0 2850ms=0 2900ms=0 2950ms=0 3000ms=0 3050ms=0 3100ms=0 3150ms=0 3200ms=0 3250ms=0 3300ms=0 3350ms=0 3400ms=0 3450ms=0 3500ms=0 3550ms=0 3600ms=0 3650ms=0 3700ms=0 3750ms=0 3800ms=0 3850ms=0 3900ms=0 3950ms=0 4000ms=0 4050ms=0 4100ms=0 4150ms=0 4200ms=0 4250ms=0 4300ms=0 4350ms=0 4400ms=0 4450ms=0 4500ms=0 4550ms=0 4600ms=0 4650ms=0 4700ms=0 4750ms=0 4800ms=0 4850ms=0 4900ms=0 4950ms=0
GPU HISTOGRAM: 1ms=0 2ms=0 3ms=2 4ms=1 5ms=1 6ms=0 7ms=0 8ms=0 9ms=0 10ms=0 11ms=0 12ms=0 13ms=0 14ms=0 15ms=0 16ms=0 17ms=0 18ms=0 19ms=0 20ms=0 21ms=0 22ms=0 23ms=0 24ms=0 25ms=0 4950ms=0
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
/home/joncrall/code/ambition/target/debug/deps/libambition_app.so
/home/joncrall/code/ambition/target/profiling/deps/libambition_app.so
/home/joncrall/code/ambition/target/release/deps/libambition_app.so
```
