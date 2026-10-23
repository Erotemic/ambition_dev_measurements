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
- perf.data bytes: 5071387

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 12709. Samples lost: 0.

## Top simpleperf report lines
```text
5.83%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
2.78%     Async Compute T  /data/app/~~ramwcGNqNQU2ItFMYtJpTA==/org.erotemic.ambition.sandbox-kKBtQkQ9yFbel60ljr_Kfg==/lib/arm64/libambition_app.so  libambition_app.so[+11d740ac]
2.05%     Async Compute T  /data/app/~~ramwcGNqNQU2ItFMYtJpTA==/org.erotemic.ambition.sandbox-kKBtQkQ9yFbel60ljr_Kfg==/lib/arm64/libambition_app.so  libambition_app.so[+11d73fdc]
1.65%     Async Compute T  /data/app/~~ramwcGNqNQU2ItFMYtJpTA==/org.erotemic.ambition.sandbox-kKBtQkQ9yFbel60ljr_Kfg==/lib/arm64/libambition_app.so  libambition_app.so[+11d74048]
1.61%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.42%     Async Compute T  /data/app/~~ramwcGNqNQU2ItFMYtJpTA==/org.erotemic.ambition.sandbox-kKBtQkQ9yFbel60ljr_Kfg==/lib/arm64/libambition_app.so  libambition_app.so[+11d6e8cc]
0.99%     Async Compute T  /data/app/~~ramwcGNqNQU2ItFMYtJpTA==/org.erotemic.ambition.sandbox-kKBtQkQ9yFbel60ljr_Kfg==/lib/arm64/libambition_app.so  libambition_app.so[+11d6e6d0]
0.93%     Async Compute T  /data/app/~~ramwcGNqNQU2ItFMYtJpTA==/org.erotemic.ambition.sandbox-kKBtQkQ9yFbel60ljr_Kfg==/lib/arm64/libambition_app.so  libambition_app.so[+11d6e5dc]
0.88%     android_main     /data/app/~~ramwcGNqNQU2ItFMYtJpTA==/org.erotemic.ambition.sandbox-kKBtQkQ9yFbel60ljr_Kfg==/lib/arm64/libambition_app.so  libambition_app.so[+c78ad40]
0.70%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
0.65%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.39%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memcmp_aarch64
0.38%     android_main     /data/app/~~ramwcGNqNQU2ItFMYtJpTA==/org.erotemic.ambition.sandbox-kKBtQkQ9yFbel60ljr_Kfg==/lib/arm64/libambition_app.so  libambition_app.so[+101a5af0]
0.35%     android_main     /data/app/~~ramwcGNqNQU2ItFMYtJpTA==/org.erotemic.ambition.sandbox-kKBtQkQ9yFbel60ljr_Kfg==/lib/arm64/libambition_app.so  libambition_app.so[+14da7428]
0.35%     Async Compute T  /data/app/~~ramwcGNqNQU2ItFMYtJpTA==/org.erotemic.ambition.sandbox-kKBtQkQ9yFbel60ljr_Kfg==/lib/arm64/libambition_app.so  libambition_app.so[+11d6e5d4]
0.33%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            memset
0.31%     android_main     /data/app/~~ramwcGNqNQU2ItFMYtJpTA==/org.erotemic.ambition.sandbox-kKBtQkQ9yFbel60ljr_Kfg==/lib/arm64/libambition_app.so  libambition_app.so[+14e2b238]
0.31%     Async Compute T  /data/app/~~ramwcGNqNQU2ItFMYtJpTA==/org.erotemic.ambition.sandbox-kKBtQkQ9yFbel60ljr_Kfg==/lib/arm64/libambition_app.so  libambition_app.so[+11d30c78]
0.28%     android_main     /data/app/~~ramwcGNqNQU2ItFMYtJpTA==/org.erotemic.ambition.sandbox-kKBtQkQ9yFbel60ljr_Kfg==/lib/arm64/libambition_app.so  libambition_app.so[+14da7424]
0.26%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            pthread_getspecific
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]  130.475s-135.505s frames=195 p50=25.3ms p95=36.1ms p99=45.0ms max=52.4ms archetypes=2091 (+0) entities=3252
[frame-census]  135.505s-140.507s frames=178 p50=27.3ms p95=42.5ms p99=47.7ms max=48.4ms archetypes=2091 (+0) entities=3248
[frame-census]  140.507s-145.532s frames=194 p50=25.4ms p95=38.2ms p99=43.6ms max=49.3ms archetypes=2091 (+0) entities=3252
[frame-census]  145.532s-150.534s frames=203 p50=23.3ms p95=35.9ms p99=39.6ms max=40.9ms archetypes=2091 (+0) entities=3248
[frame-census]  150.534s-155.553s frames=199 p50=24.6ms p95=37.5ms p99=41.5ms max=46.2ms archetypes=2091 (+0) entities=3252
[frame-census]  155.553s-160.577s frames=195 p50=25.4ms p95=35.3ms p99=45.0ms max=45.9ms archetypes=2091 (+0) entities=3248
[frame-census]  160.577s-165.583s frames=193 p50=25.5ms p95=37.1ms p99=41.2ms max=42.0ms archetypes=2091 (+0) entities=3252
[frame-census]  165.583s-170.584s frames=202 p50=24.3ms p95=36.0ms p99=40.5ms max=54.9ms archetypes=2091 (+0) entities=3250
[frame-census]  170.584s-175.608s frames=205 p50=23.5ms p95=37.1ms p99=42.9ms max=49.8ms archetypes=2091 (+0) entities=3250
[frame-census]  175.608s-180.623s frames=195 p50=25.2ms p95=41.4ms p99=45.1ms max=46.9ms archetypes=2091 (+0) entities=3250
[frame-census]  180.623s-185.631s frames=184 p50=27.8ms p95=36.9ms p99=43.4ms max=50.2ms archetypes=2091 (+0) entities=3250
[frame-census]  185.631s-190.639s frames=194 p50=24.9ms p95=38.9ms p99=43.7ms max=46.8ms archetypes=2091 (+0) entities=3250
```

- hub games: all

## simpleperf stat excerpt
```text
Performance counter statistics:
#             count  event_name         # count / runtime
  104524.055477(ms)  cpu-clock          #             
          1,057,063  context-switches   # 10.031 K/sec
             71,836  page-faults        # 681.684 /sec
Total test time: 60.028738 seconds.
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
