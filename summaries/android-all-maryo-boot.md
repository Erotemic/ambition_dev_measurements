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
- perf.data bytes: 2804545

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 4706. Samples lost: 0.

## Top simpleperf report lines
```text
5.52%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.76%     android_main     /data/app/~~Caomsp4lqgAvpEHeJ2XcnA==/org.erotemic.ambition.sandbox-1TK6e68lYcgMeaT-2RkV7w==/lib/arm64/libambition_app.so  libambition_app.so[+c72d250]
1.49%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.21%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
1.08%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memcmp_aarch64
0.87%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
0.47%     android_main     /data/app/~~Caomsp4lqgAvpEHeJ2XcnA==/org.erotemic.ambition.sandbox-1TK6e68lYcgMeaT-2RkV7w==/lib/arm64/libambition_app.so  libambition_app.so[+100caaac]
0.38%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            pthread_getspecific
0.36%     android_main     /data/app/~~Caomsp4lqgAvpEHeJ2XcnA==/org.erotemic.ambition.sandbox-1TK6e68lYcgMeaT-2RkV7w==/lib/arm64/libambition_app.so  libambition_app.so[+14c9d9e0]
0.34%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796e324]
0.30%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa487a27338]
0.28%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            memset
0.28%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa4879435f0]
0.25%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa4878eb050]
0.25%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796e324]
0.25%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            scudo::Allocator<scudo::AndroidConfig, &(scudo_malloc_postinit)>::allocate(unsigned long, s
0.25%     android_main     /data/app/~~Caomsp4lqgAvpEHeJ2XcnA==/org.erotemic.ambition.sandbox-1TK6e68lYcgMeaT-2RkV7w==/lib/arm64/libambition_app.so  libambition_app.so[+14c9d9dc]
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]   40.109s-45.137s frames=155 p50=31.1ms p95=54.0ms p99=63.9ms max=79.5ms archetypes=2133 (+4) entities=5773
[frame-census]   45.137s-50.165s frames=137 p50=35.0ms p95=56.3ms p99=67.8ms max=71.1ms archetypes=2133 (+0) entities=5793
[frame-census]   50.165s-55.186s frames=140 p50=34.3ms p95=54.3ms p99=69.5ms max=71.5ms archetypes=2133 (+0) entities=5792
[frame-census]   55.186s-60.220s frames=182 p50=27.3ms p95=42.3ms p99=46.7ms max=59.2ms archetypes=2147 (+14) entities=3181
[frame-census]   60.220s-65.234s frames=204 p50=24.4ms p95=36.0ms p99=41.9ms max=47.6ms archetypes=2170 (+23) entities=3269
[frame-census]   65.234s-70.248s frames=191 p50=26.1ms p95=37.8ms p99=43.1ms max=45.2ms archetypes=2176 (+6) entities=3291
[frame-census]   70.248s-75.266s frames=176 p50=27.3ms p95=43.6ms p99=52.6ms max=55.1ms archetypes=2177 (+1) entities=3321
[frame-census]   75.266s-80.295s frames=146 p50=33.6ms p95=53.4ms p99=65.0ms max=65.3ms archetypes=2178 (+1) entities=3283
[frame-census]   80.295s-85.309s frames=168 p50=26.7ms p95=55.6ms p99=75.2ms max=129.5ms archetypes=2210 (+32) entities=3186
[frame-census]   85.309s-90.311s frames=196 p50=24.4ms p95=34.4ms p99=69.5ms max=96.0ms archetypes=2210 (+0) entities=3168
[frame-census]   90.311s-95.324s frames=196 p50=25.9ms p95=35.5ms p99=38.5ms max=41.1ms archetypes=2210 (+0) entities=3236
[frame-census]   95.324s-100.351s frames=172 p50=29.2ms p95=43.2ms p99=49.5ms max=56.7ms archetypes=2221 (+11) entities=3234
```

- hub games: all

## simpleperf stat excerpt
```text
Performance counter statistics:
#            count  event_name         # count / runtime
  51839.103382(ms)  cpu-clock          #             
           544,933  context-switches   # 10.430 K/sec
            57,404  page-faults        # 1.099 K/sec 
Total test time: 30.003109 seconds.
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
