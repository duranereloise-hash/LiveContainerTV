# LiveContainerTV — сборка

## Что сделано

В `LCMachOUtils.m`:
- **Патч LC_BUILD_VERSION**: при установке tvOS-приложения меняет platform с `PLATFORM_APPLETVOS` (3) на `PLATFORM_IOS` (1), а `LC_VERSION_MIN_TVOS` → `LC_VERSION_MIN_IPHONEOS`
- **Патч Info.plist**: меняет `UIDeviceFamily` (убирает 3, добавляет 1,2), чистит tvOS-специфичные `UIRequiredDeviceCapabilities`

В `LCAppInfo.m`:
- Вызов `LCPatchAppBundleForTVOS` перед патчем Mach-O

## Как собрать

1. **macOS + Xcode 15.4+** (iOS 18 SDK или новее)
2. В `xcconfigs/Global.xcconfig` поменять `DEVELOPMENT_TEAM` на свой Apple Team ID:
   ```
   DEVELOPMENT_TEAM[config=Debug] = ТВОЙ_ТИМАЙД
   ```
3. Открыть `LiveContainer.xcodeproj`, выбрать схему `LiveContainer`, девайс (только на устройство, не симулятор), **Run**

Либо собрать через CLI:
```bash
xcodebuild -project LiveContainer.xcodeproj \
  -scheme LiveContainer \
  -configuration Debug \
  -archivePath build/LiveContainer.xcarchive \
  archive

xcodebuild -exportArchive \
  -archivePath build/LiveContainer.xcarchive \
  -exportPath build/ \
  -exportOptionsPlist exportOptions.plist
```

## Использование

1. Установить собранный `.ipa` через SideStore/AltStore (обязательно выбрать **Keep App Extensions**)
2. Открыть LiveContainer, нажать `+`, выбрать `.ipa` tvOS-приложения
3. Запустить — LiveContainer применит патчи автоматически

## Известные ограничения

- Некоторые tvOS-приложения могут крашиться из-за вызова UIKit API, недоступного на iOS
- Приложения с Siri Remote framework (GameController) могут требовать JIT
- Multitasking для tvOS-приложений не тестировался