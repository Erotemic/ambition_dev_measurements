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
- perf.data bytes: 2709016

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 4338. Samples lost: 0.

## Top simpleperf report lines
```text
4.24%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.89%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.75%     android_main     /data/app/~~cysktlM65cakw6bVmLL5HA==/org.erotemic.ambition.sandbox-g2b8XsSstuQpB2nUnMSuDw==/lib/arm64/libambition_app.so  libambition_app.so[+c702d18]
0.71%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.69%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
0.62%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            pthread_getspecific
0.48%     android_main     /data/app/~~cysktlM65cakw6bVmLL5HA==/org.erotemic.ambition.sandbox-g2b8XsSstuQpB2nUnMSuDw==/lib/arm64/libambition_app.so  libambition_app.so[+14b802bc]
0.41%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memcmp_aarch64
0.37%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796e324]
0.35%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            memset
0.35%     android_main     /data/app/~~cysktlM65cakw6bVmLL5HA==/org.erotemic.ambition.sandbox-g2b8XsSstuQpB2nUnMSuDw==/lib/arm64/libambition_app.so  libambition_app.so[+14b802c0]
0.35%     android_main     /data/app/~~cysktlM65cakw6bVmLL5HA==/org.erotemic.ambition.sandbox-g2b8XsSstuQpB2nUnMSuDw==/lib/arm64/libambition_app.so  libambition_app.so[+c702d08]
0.30%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c370]
0.30%     android_main     /data/app/~~cysktlM65cakw6bVmLL5HA==/org.erotemic.ambition.sandbox-g2b8XsSstuQpB2nUnMSuDw==/lib/arm64/libambition_app.so  libambition_app.so[+14b802b8]
0.28%     android_main     /data/app/~~cysktlM65cakw6bVmLL5HA==/org.erotemic.ambition.sandbox-g2b8XsSstuQpB2nUnMSuDw==/lib/arm64/libambition_app.so  libambition_app.so[+14b80124]
0.28%     android_main     /data/app/~~cysktlM65cakw6bVmLL5HA==/org.erotemic.ambition.sandbox-g2b8XsSstuQpB2nUnMSuDw==/lib/arm64/libambition_app.so  libambition_app.so[+14c02d24]
0.28%     android_main     /data/app/~~cysktlM65cakw6bVmLL5HA==/org.erotemic.ambition.sandbox-g2b8XsSstuQpB2nUnMSuDw==/lib/arm64/libambition_app.so  libambition_app.so[+c702950]
0.25%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!097ece140a689b3ba2b5661cb74dce!4783c89!
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]   35.161s-40.201s frames=99 p50=51.0ms p95=66.6ms p99=70.4ms max=75.2ms
[frame-census]   40.201s-45.261s frames=107 p50=45.4ms p95=72.4ms p99=79.9ms max=80.5ms
[frame-census]   45.261s-50.268s frames=104 p50=49.7ms p95=66.7ms p99=69.3ms max=75.4ms
[frame-census]   50.268s-55.311s frames=97 p50=50.5ms p95=76.7ms p99=84.3ms max=88.8ms
[frame-census]   55.311s-60.319s frames=102 p50=43.9ms p95=81.5ms p99=115.9ms max=176.3ms
[frame-census]   60.319s-65.334s frames=108 p50=46.8ms p95=63.7ms p99=65.8ms max=75.6ms
[frame-census]   65.334s-70.364s frames=118 p50=40.6ms p95=65.6ms p99=78.8ms max=80.0ms
[frame-census]   70.364s-75.380s frames=69 p50=69.0ms p95=117.4ms p99=144.8ms max=149.3ms
[frame-census]   75.380s-80.393s frames=100 p50=44.7ms p95=85.9ms p99=94.4ms max=108.8ms
[frame-census]   80.393s-85.438s frames=88 p50=53.7ms p95=100.8ms p99=115.4ms max=129.5ms
[frame-census]   85.438s-90.438s frames=99 p50=46.8ms p95=77.0ms p99=89.5ms max=98.2ms
[frame-census]   90.438s-95.499s frames=85 p50=51.3ms p95=114.6ms p99=128.3ms max=138.4ms
```

- hub games: mary_o

## simpleperf stat excerpt
```text
Performance counter statistics:
#            count  event_name         # count / runtime
  41651.351708(ms)  cpu-clock          #             
           307,221  context-switches   # 7.329 K/sec 
            17,760  page-faults        # 423.767 /sec
Total test time: 30.003637 seconds.
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
/home/joncrall/code/ambition/target/debug/deps/libambition_app.so
/home/joncrall/code/ambition/target/profiling/deps/libambition_app.so
/home/joncrall/code/ambition/target/release/deps/libambition_app.so
```
