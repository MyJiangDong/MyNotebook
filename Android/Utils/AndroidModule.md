#### Module外部依赖

```groovy
rootProject.name = "banmacang_android_3-0"
include ':tuikit'
project(':tuikit').projectDir = new File(settingsDir, '../ban_ji_android/tuikit')
```

注: ..代表settingsDir目录的上一级目录

