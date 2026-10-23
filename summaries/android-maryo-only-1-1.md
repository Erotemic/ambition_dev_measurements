# Android profile summary

## Status
- profile-build: 0
- simpleperf-record: 1
- adb-pull-perf: 1
- simpleperf-stat: 1
- gfxinfo-after-record: 1
- gfxinfo-framestats-after-record: 1

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]   20.073s-25.098s frames=165 p50=31.1ms p95=43.4ms p99=50.8ms max=58.6ms
[frame-census]   25.098s-30.125s frames=154 p50=31.2ms p95=50.2ms p99=56.2ms max=62.4ms
[frame-census]   30.125s-35.142s frames=137 p50=33.9ms p95=64.7ms p99=95.2ms max=100.9ms
[frame-census]   35.142s-40.174s frames=91 p50=49.7ms p95=109.2ms p99=130.3ms max=135.2ms
[frame-census]   40.174s-45.214s frames=81 p50=60.1ms p95=96.1ms p99=109.7ms max=114.1ms
[frame-census]   45.214s-50.258s frames=90 p50=54.5ms p95=77.3ms p99=79.8ms max=82.1ms
[frame-census]   50.258s-55.293s frames=96 p50=53.2ms p95=68.1ms p99=72.7ms max=82.8ms
[frame-census]   55.293s-60.311s frames=96 p50=49.8ms p95=71.8ms p99=79.9ms max=80.3ms
```

- hub games: mary_o

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
