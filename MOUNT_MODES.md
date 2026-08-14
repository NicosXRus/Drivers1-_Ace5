# ⚙️ Mount Modes Documentation / Документация режимов монтирования

---

## Navigation / Навигация

* 🇬🇧 [English](#english)
* 🇷🇺 [Русский](#русский)
* 🇺🇦 [Українська](#українська)
* 🇦🇲 [Հայերեն](#հայերեն)
* 🇨🇳 [中文](#中文)
* 🇯🇵 [日本語](#日本語)
* 🇪🇸 [Español](#español)
* 🇦🇪 [العربية](#العربية)
* 🇮🇳 [हिन्दी](#हिन्दी)
* 🇵🇱 [Polski](#polski)
* 🇩🇪 [Deutsch](#deutsch)

---

## <a id="русский"></a>🇷🇺 Русский

### Сравнение режимов монтирования

| Параметр | 💿 Legacy | ⚡ OverlayImg | 📂 OverlayData | 🔗 Mountify |
| :--- | :--- | :--- | :--- | :--- |
| **Технология** | Direct Bind Mount | OverlayFS + EROFS | OverlayFS (напрямую) | System Bind Mount |
| **Хранение** | Папка модуля | Образ `odm.img` | Папка `overlay/odm` | Перемещение в `/system` |
| **`loop`-диски** | ❌ Нет | 🛠️ 1 диск | ❌ Нет | ❌ Нет |
| **Совместимость** | Все ядра | Ядра с EROFS+OverlayFS | Ядра с OverlayFS | Все ядра |

#### 1. 💿 Legacy Mode
* **Принцип:** Прямой монтинг через `mount --bind`.
* **Плюсы:** Работает везде, мгновенная установка.

#### 2. ⚡ OverlayImg Mode
* **Принцип:** Упаковывает файлы в сжатый EROFS образ (`odm.img`), монтирует через loop-устройство и оверлеит `/odm`.
* **Плюсы:** Экономия памяти (LZ4HC), защита файлов на уровне ядра.

#### 3. 📂 OverlayData Mode
* **Принцип:** Монтирует распакованную папку `/data/adb/modules/.../overlay/odm` через OverlayFS напрямую.
* **Плюсы:** Быстрая установка без компиляции образа, легко модифицировать файлы.

#### 4. 🔗 Mountify Mode
* **Принцип:** Переносит файлы в `/system/odm` и подменяет каталоги через `.replace` и bind-mount.
* **Плюсы:** Высшая стабильность на стоковых ядрах.

---

## <a id="english"></a>🇬🇧 English

### Mount Modes Comparison

| Parameter | 💿 Legacy | ⚡ OverlayImg | 📂 OverlayData | 🔗 Mountify |
| :--- | :--- | :--- | :--- | :--- |
| **Technology** | Direct Bind Mount | OverlayFS + EROFS | OverlayFS (Direct) | System Bind Mount |
| **Storage** | Module folder | Image `odm.img` | Folder `overlay/odm` | Moved to `/system` |
| **`loop` usage** | ❌ No | 🛠️ 1 device | ❌ No | ❌ No |
| **Compatibility** | All kernels | EROFS+OverlayFS kernels | OverlayFS kernels | All kernels |

#### 1. 💿 Legacy Mode
* Direct `mount --bind` of files and folders. Universal compatibility.

#### 2. ⚡ OverlayImg Mode
* Packs files into LZ4HC compressed EROFS image (`odm.img`) and overlays over `/odm`.

#### 3. 📂 OverlayData Mode
* Overlays uncompressed folder directly from `/data/adb/modules/.../overlay/odm`. Fast installation.

#### 4. 🔗 Mountify Mode
* Moves `/odm` into `/system/odm` with `.replace` directory isolation. Highest stability.

---

## <a id="українська"></a>🇺🇦 Українська

| Параметр | 💿 Legacy | ⚡ OverlayImg | 📂 OverlayData | 🔗 Mountify |
| :--- | :--- | :--- | :--- | :--- |
| **Технологія** | Direct Bind Mount | OverlayFS + EROFS | OverlayFS (Прямий) | System Bind Mount |
| **Зберігання** | Папка модуля | Образ `odm.img` | Папка `overlay/odm` | Переміщення в `/system` |
| **Сумісність** | Усі ядра | Ядра з EROFS+OverlayFS | Ядра з OverlayFS | Усі ядра |

---

## <a id="հայերեն"></a>🇦🇲 Հայերեն

| Պարամետր | 💿 Legacy | ⚡ OverlayImg | 📂 OverlayData | 🔗 Mountify |
| :--- | :--- | :--- | :--- | :--- |
| **Տեխնոլոգիա** | Direct Bind Mount | OverlayFS + EROFS | OverlayFS (Ուղիղ) | System Bind Mount |
| **Պահպանում** | Մոդուլի թղթապանակ | Պատկեր `odm.img` | Թղթապանակ `overlay/odm` | Տեղափոխում `/system` |
| **Համատեղելիություն** | Բոլոր միջուկները | EROFS+OverlayFS միջուկներ | OverlayFS միջուկներ | Բոլոր միջուկները |

---

## <a id="中文"></a>🇨🇳 中文

| 参数 | 💿 Legacy | ⚡ OverlayImg | 📂 OverlayData | 🔗 Mountify |
| :--- | :--- | :--- | :--- | :--- |
| **技术基础** | Direct Bind Mount | OverlayFS + EROFS | OverlayFS (直接挂载) | System Bind Mount |
| **存储方式** | 模块目录 | 镜像 `odm.img` | 文件夹 `overlay/odm` | 移动至 `/system` |
| **兼容性** | 所有内核 | 支持 EROFS+OverlayFS 内核 | 支持 OverlayFS 内核 | 所有内核 |

---

## <a id="日本語"></a>🇯🇵 日本語

| パラメータ | 💿 Legacy | ⚡ OverlayImg | 📂 OverlayData | 🔗 Mountify |
| :--- | :--- | :--- | :--- | :--- |
| **技術** | Direct Bind Mount | OverlayFS + EROFS | OverlayFS (直接) | System Bind Mount |
| **ストレージ** | モジュールフォルダ | イメージ `odm.img` | フォルダ `overlay/odm` | `/system` へ移動 |
| **互換性** | 全カーネル | EROFS+OverlayFS カーネル | OverlayFS カーネル | 全カーネル |

---

## <a id="español"></a>🇪🇸 Español

| Parámetro | 💿 Legacy | ⚡ OverlayImg | 📂 OverlayData | 🔗 Mountify |
| :--- | :--- | :--- | :--- | :--- |
| **Tecnología** | Direct Bind Mount | OverlayFS + EROFS | OverlayFS (Directo) | System Bind Mount |
| **Almacenamiento** | Carpeta del módulo | Imagen `odm.img` | Carpeta `overlay/odm` | Mover a `/system` |
| **Compatibilidad** | Todos los kernels | Kernels con EROFS+OverlayFS | Kernels con OverlayFS | Todos los kernels |

---

## <a id="العربية"></a>🇦🇪 العربية

| المعيار | 💿 Legacy | ⚡ OverlayImg | 📂 OverlayData | 🔗 Mountify |
| :--- | :--- | :--- | :--- | :--- |
| **التقنية** | Direct Bind Mount | OverlayFS + EROFS | OverlayFS (مباشر) | System Bind Mount |
| **التخزين** | مجلد الموديول | صورة `odm.img` | مجلد `overlay/odm` | نقل إلى `/system` |
| **التوافقية** | جميع النوى | نوى تدعم EROFS+OverlayFS | نوى تدعم OverlayFS | جميع النوى |

---

## <a id="हिन्दी"></a>🇮🇳 हिन्दी

| पैरामीटर | 💿 Legacy | ⚡ OverlayImg | 📂 OverlayData | 🔗 Mountify |
| :--- | :--- | :--- | :--- | :--- |
| **तकनीक** | Direct Bind Mount | OverlayFS + EROFS | OverlayFS (प्रत्यक्ष) | System Bind Mount |
| **स्टोरेज** | मॉड्यूल फोल्डर | इमेज `odm.img` | फोल्डर `overlay/odm` | `/system` में स्थानांतरित |
| **संगतता** | सभी कर्नेल | EROFS+OverlayFS कर्नेल | OverlayFS कर्नेल | सभी कर्नेल |

---

## <a id="polski"></a>🇵🇱 Polski

| Parametr | 💿 Legacy | ⚡ OverlayImg | 📂 OverlayData | 🔗 Mountify |
| :--- | :--- | :--- | :--- | :--- |
| **Technologia** | Direct Bind Mount | OverlayFS + EROFS | OverlayFS (Bezpośredni) | System Bind Mount |
| **Przechowywanie** | Folder modułu | Obraz `odm.img` | Folder `overlay/odm` | Przeniesiono do `/system` |
| **Kompatybilność** | Wszystkie jądra | Jądra z EROFS+OverlayFS | Jądra z OverlayFS | Wszystkie jądra |

---

## <a id="deutsch"></a>🇩🇪 Deutsch

| Parameter | 💿 Legacy | ⚡ OverlayImg | 📂 OverlayData | 🔗 Mountify |
| :--- | :--- | :--- | :--- | :--- |
| **Technologie** | Direct Bind Mount | OverlayFS + EROFS | OverlayFS (Direkt) | System Bind Mount |
| **Speicher** | Modul-Ordner | Image `odm.img` | Ordner `overlay/odm` | Verschoben nach `/system` |
| **Kompatibilität** | Alle Kernel | Kernel mit EROFS+OverlayFS | Kernel mit OverlayFS | Alle Kernel |
