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
- perf.data bytes: 2887113

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 4916. Samples lost: 0.

## Top simpleperf report lines
```text
5.57%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.59%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.24%     android_main     /data/app/~~AL37y6CQyQHvua2TaVCUJQ==/org.erotemic.ambition.sandbox-6SBt0GuaWM3Lfl6YBx1M2A==/lib/arm64/libambition_app.so  libambition_app.so[+c717044]
1.02%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.63%     android_main     /data/app/~~AL37y6CQyQHvua2TaVCUJQ==/org.erotemic.ambition.sandbox-6SBt0GuaWM3Lfl6YBx1M2A==/lib/arm64/libambition_app.so  libambition_app.so[+10029844]
0.63%     android_main     /data/app/~~AL37y6CQyQHvua2TaVCUJQ==/org.erotemic.ambition.sandbox-6SBt0GuaWM3Lfl6YBx1M2A==/lib/arm64/libambition_app.so  libambition_app.so[+fc958f4]
0.63%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
0.49%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memcmp_aarch64
0.47%     android_main     /data/app/~~AL37y6CQyQHvua2TaVCUJQ==/org.erotemic.ambition.sandbox-6SBt0GuaWM3Lfl6YBx1M2A==/lib/arm64/libambition_app.so  libambition_app.so[+14bd24a0]
0.35%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796e324]
0.31%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c370]
0.31%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa4879435f0]
0.26%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa4878eb050]
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]   35.072s-40.088s frames=158 p50=31.4ms p95=44.7ms p99=50.1ms max=64.7ms
[frame-census]   40.088s-45.095s frames=164 p50=31.2ms p95=41.8ms p99=45.1ms max=45.6ms
[frame-census]   45.095s-50.135s frames=139 p50=33.2ms p95=60.5ms p99=78.7ms max=84.7ms
[frame-census]   50.135s-55.166s frames=116 p50=41.6ms p95=61.0ms p99=72.3ms max=84.4ms
[frame-census]   55.166s-60.170s frames=141 p50=35.4ms p95=47.5ms p99=52.2ms max=52.5ms
[frame-census]   60.170s-65.187s frames=130 p50=36.4ms p95=59.1ms p99=95.4ms max=129.5ms
[frame-census]   65.187s-70.190s frames=129 p50=33.5ms p95=92.1ms p99=151.3ms max=179.8ms
[frame-census]   70.190s-75.251s frames=100 p50=42.2ms p95=91.9ms p99=142.7ms max=344.5ms
[frame-census]   75.251s-80.281s frames=104 p50=41.2ms p95=97.6ms p99=124.6ms max=134.9ms
[frame-census]   80.281s-85.292s frames=135 p50=35.9ms p95=58.0ms p99=68.3ms max=68.5ms
[frame-census]   85.292s-90.347s frames=105 p50=46.4ms p95=77.6ms p99=93.9ms max=95.6ms
[frame-census]   90.347s-95.377s frames=100 p50=44.5ms p95=88.0ms p99=109.5ms max=125.7ms
```

- hub games: all

## simpleperf stat excerpt
```text
Performance counter statistics:
#            count  event_name         # count / runtime
  44376.068942(ms)  cpu-clock          #             
           383,565  context-switches   # 8.586 K/sec 
            43,167  page-faults        # 966.398 /sec
Total test time: 30.030532 seconds.
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
