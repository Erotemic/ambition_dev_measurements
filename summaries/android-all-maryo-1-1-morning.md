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
- perf.data bytes: 2863183

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 5439. Samples lost: 0.

## Top simpleperf report lines
```text
9.23%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
2.17%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.08%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
0.92%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.74%     android_main     /data/app/~~t1uvofp4jIckUOUyuLxQkw==/org.erotemic.ambition.sandbox-28SpHkEnjUNzBHgrHlItJQ==/lib/arm64/libambition_app.so  libambition_app.so[+10029844]
0.63%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa4879435f0]
0.51%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            memset
0.51%     android_main     /data/app/~~t1uvofp4jIckUOUyuLxQkw==/org.erotemic.ambition.sandbox-28SpHkEnjUNzBHgrHlItJQ==/lib/arm64/libambition_app.so  libambition_app.so[+fc958f4]
0.48%     android_main     /data/app/~~t1uvofp4jIckUOUyuLxQkw==/org.erotemic.ambition.sandbox-28SpHkEnjUNzBHgrHlItJQ==/lib/arm64/libambition_app.so  libambition_app.so[+c717044]
0.46%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa4878eb050]
0.44%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c370]
0.40%     Compute Task Po  /data/app/~~t1uvofp4jIckUOUyuLxQkw==/org.erotemic.ambition.sandbox-28SpHkEnjUNzBHgrHlItJQ==/lib/arm64/libambition_app.so  libambition_app.so[+14c56298]
0.40%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            scudo::Allocator<scudo::AndroidConfig, &(scudo_malloc_postinit)>::deallocate(void*, scudo::
0.39%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            scudo::Allocator<scudo::AndroidConfig, &(scudo_malloc_postinit)>::allocate(unsigned long, s
0.39%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            scudo::Allocator<scudo::AndroidConfig, &(scudo_malloc_postinit)>::quarantineOrDeallocateChu
0.33%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796e324]
0.31%     android_main     /data/app/~~t1uvofp4jIckUOUyuLxQkw==/org.erotemic.ambition.sandbox-28SpHkEnjUNzBHgrHlItJQ==/lib/arm64/libambition_app.so  libambition_app.so[+14bd24a0]
0.31%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!097ece140a689b3ba2b5661cb74dce!4783c89!
0.31%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!260addb415166054346467f72a0679!4783c89!
0.29%     Compute Task Po  /data/app/~~t1uvofp4jIckUOUyuLxQkw==/org.erotemic.ambition.sandbox-28SpHkEnjUNzBHgrHlItJQ==/lib/arm64/libambition_app.so  libambition_app.so[+c717044]
0.29%     android_main     /data/app/~~t1uvofp4jIckUOUyuLxQkw==/org.erotemic.ambition.sandbox-28SpHkEnjUNzBHgrHlItJQ==/lib/arm64/libambition_app.so  libambition_app.so[+14bcf908]
0.28%     android_main     /data/app/~~t1uvofp4jIckUOUyuLxQkw==/org.erotemic.ambition.sandbox-28SpHkEnjUNzBHgrHlItJQ==/lib/arm64/libambition_app.so  libambition_app.so[+14c2d748]
0.28%     android_main     /data/app/~~t1uvofp4jIckUOUyuLxQkw==/org.erotemic.ambition.sandbox-28SpHkEnjUNzBHgrHlItJQ==/lib/arm64/libambition_app.so  libambition_app.so[+14c562b4]
0.28%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!940f66b05f2a8911c494435445d321!4783c89!
0.28%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796e324]
0.26%     android_main     /data/app/~~t1uvofp4jIckUOUyuLxQkw==/org.erotemic.ambition.sandbox-28SpHkEnjUNzBHgrHlItJQ==/lib/arm64/libambition_app.so  libambition_app.so[+134d32ac]
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]   30.046s-35.062s frames=304 p50=16.5ms p95=24.0ms p99=26.5ms max=27.4ms
[frame-census]   35.062s-40.069s frames=302 p50=16.4ms p95=23.4ms p99=26.7ms max=29.0ms
[frame-census]   40.069s-45.073s frames=299 p50=16.5ms p95=23.2ms p99=27.7ms max=51.7ms
[frame-census]   45.073s-50.077s frames=301 p50=16.3ms p95=24.6ms p99=26.8ms max=30.5ms
[frame-census]   50.077s-55.082s frames=299 p50=16.7ms p95=23.3ms p99=25.7ms max=30.2ms
[frame-census]   55.082s-60.089s frames=291 p50=17.2ms p95=22.8ms p99=25.5ms max=32.4ms
[frame-census]   60.089s-65.093s frames=295 p50=16.8ms p95=24.3ms p99=27.8ms max=79.5ms
[frame-census]   65.093s-70.097s frames=247 p50=17.2ms p95=28.9ms p99=77.3ms max=398.1ms
[frame-census]   70.097s-75.110s frames=187 p50=25.5ms p95=36.6ms p99=63.5ms max=132.8ms
[frame-census]   75.110s-80.130s frames=186 p50=27.3ms p95=38.0ms p99=45.2ms max=51.4ms
[frame-census]   80.130s-85.137s frames=186 p50=27.0ms p95=36.7ms p99=41.9ms max=46.0ms
[frame-census]   85.137s-90.157s frames=170 p50=28.8ms p95=44.5ms p99=49.0ms max=51.5ms
```

- hub games: all

## simpleperf stat excerpt
```text
Performance counter statistics:
#            count  event_name         # count / runtime
  52871.440776(ms)  cpu-clock          #             
           570,652  context-switches   # 10.707 K/sec
           102,406  page-faults        # 1.921 K/sec 
Total test time: 30.006742 seconds.
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
