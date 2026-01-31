# Бранч trackball-processors-update

## Описание
Экспериментальная ветка прошивки ZMK для **Charybdis 4x6** на базе **nice_nano (nRF52840)** с трекболом **PMW3610** и **продвинутой обработкой input processors**.

## Ключевые особенности

### ZMK и Zephyr
- **ZMK**: актуальная **main** ветка (rolling release)
- **Zephyr**: версия **4.1**
- Переход с `nice_nano_v2` на `nice_nano` в связи с изменениями в Zephyr 4.1

### Аппаратная конфигурация
- **Плата**: nice_nano (ProMicro nRF52840)
- **Раскладка**: Charybdis 4x6 (сплит-клавиатура)
- **Трекбол**: PMW3610 (драйвер от badjeff, ревизия **zmk-0.4**)
- **Интерфейс**: SPI (CS: P0.20, SCK: P0.08, MOSI/MISO: P0.17, IRQ: P0.06)

### Настройки трекбола (PMW3610)
```
CPI: 1000
swap-xy: включен
invert-x: включен
invert-y: включен
force-awake: включен
Режим: INPUT_EV_REL (mouse movement)
```

### Режимы работы трекбола (input processors)

#### 1. Снайперский режим (SNIPE layer)
- **Слой**: 3 (snipe_layer)
- **Обработка**: масштабирование 1:3 (замедление)
- **Назначение**: точные операции, прицеливание

#### 2. Режим скролла (NUM_AND_FUN layer)
- **Слой**: 1 (num_and_fun)
- **Обработка**: 
  - Инверсия Y-оси
  - Масштабирование 1:40
  - Конвертация в события прокрутки
- **Назначение**: вертикальная/горизонтальная прокрутка документов

#### 3. Режим мыши (AUTO_MOUSE layer)
- **Слой**: 2 (auto_mouse)
- **Обработка**: масштабирование 3:2 (ускорение)
- **Назначение**: стандартное перемещение курсора

### Архитектура
- Shield-based конфигурация (`charybdis_left`, `charybdis_right`)
- `input-listener` с `CHARYBDIS_TRACKBALL_PROCESSORS` макросом
- Модульная структура с отдельными файлами:
  - `charybdis_layers.h` - определения слоёв
  - `charybdis_trackball_processors.dtsi` - конфигурация обработки ввода

### Bluetooth оптимизация
- `CONFIG_BT_BUF_EVT_RX_COUNT=40` - увеличенный буфер для стабильной связи

## История изменений

### 31.01.2026 - Добавлены input processors для трекбола
- Создан файл `charybdis_layers.h` с определениями слоёв
- Создан файл `charybdis_trackball_processors.dtsi` с конфигурацией обработки трекбола
- Обновлён `charybdis_right.overlay` для использования input processors
- Обновлён `west.yml`: драйвер PMW3610 переведён на ревизию `zmk-0.4`
- Трекбол теперь работает в трёх режимах: snipe (слой 3), scroll (слой 1), move (слой 2)

### Предыдущие изменения
1. Обновление на ZMK main (Zephyr 4.1)
2. Фикс имени платы: `nice_nano_v2` → `nice_nano`
3. Увеличение BT буфера: `CONFIG_BT_BUF_EVT_RX_COUNT=40`
4. Коррекция направления трекбола: добавлен `invert-x`

## Для чего этот бранч
Тестирование продвинутой обработки трекбола с:
- Разными режимами на разных слоях
- Input processors для гибкой настройки поведения
- Снайперским режимом для точных операций
- Нативным скроллом через трекбол

## Важные ссылки
- [ZMK Firmware](https://github.com/zmkfirmware/zmk)
- [PMW3610 Driver by badjeff](https://github.com/badjeff/zmk-pmw3610-driver)
- [Build workflow](.github/workflows/)

## Предупреждение
⚠️ **Экспериментальный бранч!** Main ветка ZMK может содержать нестабильные изменения. Для production используйте фиксированные версии ZMK.
