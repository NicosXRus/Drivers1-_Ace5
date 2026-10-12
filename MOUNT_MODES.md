# ⚙️ Mount Modes Documentation / Документация режимов монтирования

This document describes the mount modes implemented in the current Ace5DV2 package. The implementation in the installation scripts is the source of truth; kernel and provider capabilities can vary by device.

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

### Сравнение режимов

| Параметр | 💿 Legacy / Static Mount | ⚡ OverlayFS Image | 📂 OverlayFS Data | 🧩 Metamodule (auto) |
| :--- | :--- | :--- | :--- | :--- |
| **Реализация** | Выборочные bind-mount файлов и каталогов | EROFS-образ + OverlayFS | Каталог + OverlayFS | Монтирование через обнаруженный метамодуль/провайдер |
| **Хранение данных** | `odm/` в каталоге модуля | `odm.img` (EROFS) | `overlay/odm/` | `odm/` или `system/odm/` — зависит от провайдера |
| **Loop-устройство** | Нет | Да: образ подключается через `loop` | Нет | Зависит от провайдера |
| **Требования** | Доступность целей в `/odm` и поддержка bind-mount | `mkfs.erofs`, поддержка EROFS и OverlayFS в ядре | Поддержка OverlayFS в ядре | Совместимый установленный/включённый провайдер |
| **Горячее применение** | Не предусмотрено этим режимом | Не предусмотрено этим режимом | Не предусмотрено этим режимом | Только для поддерживаемых провайдеров NoMount и Hybrid Mount |

### 1. 💿 Legacy / Static Mount

Режим генерирует `post-fs-data.sh`, который использует `mount --bind`. Он монтирует заданные каталоги (`lib64/camera`, отдельные каталоги `etc` и `firmware/fastchg`), а для остальных файлов — отдельные файлы, только если соответствующий файл уже существует в `/odm`.

**Особенности**
- Не создаёт образ и не использует loop-устройство.
- Зависит от наличия целевых каталогов/файлов в `/odm`.
- Это выборочное монтирование, а не полная подмена всего дерева `/odm`.

### 2. ⚡ OverlayFS Image

На этапе установки содержимое `odm/` упаковывается в `odm.img` файловой системы EROFS с параметрами `lz4hc`, уровень 12, fragments и блок 65536. Для ряда каталогов выставляется opaque-флаг OverlayFS. Исходный каталог `odm/` затем удаляется.

При загрузке образ монтируется только для чтения через loop в `/mnt/ace5dv/odm`, после чего OverlayFS объединяет его с `/odm`.

**Требования и ограничения**
- На этапе установки необходима команда `mkfs.erofs`.
- Требуются поддержка EROFS и OverlayFS в ядре и возможность монтирования образа через loop.
- Образ компилируется при установке, поэтому установка может занять больше времени, чем OverlayFS Data.

### 3. 📂 OverlayFS Data

Каталог `odm/` перемещается в `overlay/odm/`. При загрузке он используется как верхний слой в OverlayFS над `/odm`; образ EROFS не создаётся.

**Особенности**
- Нет сборки образа и loop-устройства.
- Файлы остаются распакованными и их проще редактировать.
- Требуется поддержка OverlayFS в ядре.

### 4. 🧩 Metamodule (auto)

Этот пункт — интеграция с метамодулями, а не отдельная реализация OverlayFS. Скрипт пытается обнаружить активный провайдер монтирования (например, через `/data/adb/metamodule`, поле `metamodule` в `module.prop` или известные ID модулей). Выбранный провайдер определяет способ монтирования и расположение дерева ODM.

Поддерживаемые профили провайдеров в текущем коде:
- **NoMount** (`nomount`)
- **Hybrid Mount** (`hybrid_mount`)
- **Mountify** (`mountify`)
- **Magic Mount rs** (`magic_mount_rs`)
- **Folk Mount API** (`builtin`)
- Ручные варианты размещения: `odm/` и `system/odm/`

Если активный провайдер не обнаружен, установщик предлагает выбрать другой режим или выбрать профиль вручную. Ручной профиль предполагает, что соответствующий движок установлен и включён отдельно.

**Hot Apply:** скрипт интеграции пытается поддерживать применение без перезагрузки для NoMount и Hybrid Mount. Для Hybrid Mount проверяется доступность VFS-бэкенда; если он не отвечает, изменения будут применены после перезагрузки. Для остальных провайдеров горячее применение не заявлено.

### Рекомендации по выбору

- Выбирайте **Legacy**, если нужна выборочная подмена файлов через bind-mount и доступна совместимая структура `/odm`.
- Выбирайте **OverlayFS Image**, если устройство поддерживает EROFS, OverlayFS и loop, а предпочтительнее хранить данные в образе.
- Выбирайте **OverlayFS Data**, если поддерживается OverlayFS и важны простота редактирования и отсутствие сборки образа.
- Выбирайте **Metamodule (auto)**, если на устройстве уже настроен совместимый метамодуль. Не выбирайте его как синоним Mountify: Mountify — только один из распознаваемых провайдеров.

---

## <a id="english"></a>🇬🇧 English

### Mount modes comparison

| Parameter | 💿 Legacy / Static Mount | ⚡ OverlayFS Image | 📂 OverlayFS Data | 🧩 Metamodule (auto) |
| :--- | :--- | :--- | :--- | :--- |
| **Implementation** | Selective file and directory bind mounts | EROFS image + OverlayFS | Directory + OverlayFS | Mounting delegated to a detected provider |
| **Storage** | `odm/` in the module | EROFS `odm.img` | `overlay/odm/` | `odm/` or `system/odm/`, provider-dependent |
| **Loop device** | No | Yes | No | Provider-dependent |
| **Requirements** | Existing targets under `/odm`; bind-mount support | `mkfs.erofs`, kernel EROFS and OverlayFS support | Kernel OverlayFS support | Compatible installed/enabled provider |
| **Hot Apply** | Not implemented by this mode | Not implemented by this mode | Not implemented by this mode | Supported only by NoMount and Hybrid Mount integration |

### 1. 💿 Legacy / Static Mount

The generated `post-fs-data.sh` uses `mount --bind`. It bind-mounts selected directories (`lib64/camera`, selected `etc` directories and `firmware/fastchg`). For other files, it mounts each file only when the corresponding target already exists under `/odm`.

This is selective mounting, not a replacement of the entire `/odm` tree. It does not build an image or use a loop device.

### 2. ⚡ OverlayFS Image

During installation, `odm/` is packed into an EROFS image named `odm.img` using `lz4hc` compression (level 12), fragments and a 65536-byte block setting. Overlay opaque attributes are applied to selected directories and the source `odm/` directory is removed.

At boot, the image is mounted read-only through a loop device at `/mnt/ace5dv/odm`, then OverlayFS combines it with `/odm`.

Requirements: `mkfs.erofs` during installation, kernel EROFS and OverlayFS support, and loop mounting support. Image creation can make installation slower than OverlayFS Data.

### 3. 📂 OverlayFS Data

The installer moves `odm/` to `overlay/odm/`. At boot, this directory is used as the upper layer in an OverlayFS mount over `/odm`. No EROFS image is built and no loop device is used.

Files remain unpacked and are easier to modify, but the kernel must support OverlayFS.

### 4. 🧩 Metamodule (auto)

This is integration with mount metamodules, not a separate OverlayFS implementation. The installer attempts to detect an active provider (for example, through `/data/adb/metamodule`, the `metamodule` field in `module.prop`, or known module IDs). The provider determines the mount mechanism and ODM tree layout.

Recognized providers/profiles include **NoMount**, **Hybrid Mount**, **Mountify**, **Magic Mount rs**, and **Folk Mount API**, plus manual `odm/` and `system/odm/` layouts. If no provider is detected, the installer offers fallback modes or manual provider selection. A manually selected provider must be installed and enabled separately.

**Hot Apply** is attempted only for NoMount and Hybrid Mount. Hybrid Mount's VFS backend is checked; if it is unavailable, changes are deferred until reboot. Hot Apply is not claimed for the other providers.

### Choosing a mode

- **Legacy**: selective bind mounts when matching targets already exist under `/odm`.
- **OverlayFS Image**: when EROFS, OverlayFS and loop mounting are supported and image-based storage is preferred.
- **OverlayFS Data**: when OverlayFS is supported and unpacked, editable files are preferred.
- **Metamodule (auto)**: when a compatible mount provider is already configured. Mountify is one provider, not a top-level mode of its own.

---

## <a id="українська"></a>🇺🇦 Українська

| Параметр | 💿 Legacy / Static Mount | ⚡ OverlayFS Image | 📂 OverlayFS Data | 🧩 Metamodule (auto) |
| :--- | :--- | :--- | :--- | :--- |
| **Реалізація** | Вибіркові bind-mount файлів і каталогів | Образ EROFS + OverlayFS | Каталог + OverlayFS | Монтування через виявлений провайдер |
| **Зберігання** | `odm/` у модулі | `odm.img` | `overlay/odm/` | `odm/` або `system/odm/`, залежно від провайдера |
| **Loop-пристрій** | Ні | Так | Ні | Залежить від провайдера |
| **Вимоги** | Наявні цілі в `/odm` | EROFS, OverlayFS, `mkfs.erofs`, loop | OverlayFS у ядрі | Сумісний активний провайдер |

Legacy монтує вибрані каталоги й наявні файли через `mount --bind`. OverlayFS Image пакує `odm/` в EROFS-образ `odm.img`, який під час завантаження монтується через loop і накладається на `/odm`. OverlayFS Data переміщує дерево до `overlay/odm/` і використовує його без створення образу. Metamodule (auto) передає монтування виявленому метамодулю; доступність Hot Apply залежить від провайдера (у поточній реалізації — NoMount і Hybrid Mount).

---

## <a id="հայերեն"></a>🇦🇲 Հայերեն

| Պարամետր | 💿 Legacy | ⚡ OverlayFS Image | 📂 OverlayFS Data | 🧩 Metamodule (auto) |
| :--- | :--- | :--- | :--- | :--- |
| **Իրականացում** | Ընտրված bind-mount-եր | EROFS պատկեր + OverlayFS | Թղթապանակ + OverlayFS | Հայտնաբերված մատակարար |
| **Պահպանում** | `odm/` | `odm.img` | `overlay/odm/` | `odm/` կամ `system/odm/` |
| **Loop սարք** | Ոչ | Այո | Ոչ | Կախված մատակարարից |
| **Պահանջներ** | Գոյություն ունեցող թիրախներ `/odm`-ում | EROFS, OverlayFS, loop, `mkfs.erofs` | OverlayFS միջուկում | Ակտիվ համատեղելի մատակարար |

Legacy-ը ընտրողաբար կապում է ֆայլերն ու պանակները։ OverlayFS Image-ը ստեղծում է EROFS պատկեր, իսկ OverlayFS Data-ն օգտագործում է չփաթեթավորված `overlay/odm/` պանակը։ Metamodule (auto)-ն մոնտաժումը փոխանցում է հայտնաբերված մետամոդուլին։ Hot Apply-ը հասանելի է միայն NoMount/Hybrid Mount ինտեգրման համար։

---

## <a id="中文"></a>🇨🇳 中文

| 参数 | 💿 Legacy | ⚡ OverlayFS Image | 📂 OverlayFS Data | 🧩 Metamodule (auto) |
| :--- | :--- | :--- | :--- | :--- |
| **实现方式** | 选择性 bind mount 文件和目录 | EROFS 镜像 + OverlayFS | 目录 + OverlayFS | 交由检测到的挂载提供程序处理 |
| **存储** | 模块中的 `odm/` | `odm.img` | `overlay/odm/` | `odm/` 或 `system/odm/`，取决于提供程序 |
| **Loop 设备** | 否 | 是 | 否 | 取决于提供程序 |
| **要求** | `/odm` 中存在对应目标 | EROFS、OverlayFS、loop、`mkfs.erofs` | 内核支持 OverlayFS | 已安装并启用兼容提供程序 |

Legacy 仅挂载指定目录及已存在的目标文件。OverlayFS Image 会把 `odm/` 打包为 EROFS 镜像，并在启动时通过 loop 设备挂载。OverlayFS Data 直接使用 `overlay/odm/`，不生成镜像。Metamodule (auto) 将挂载交给检测到的元模块；Hot Apply 仅针对 NoMount 和 Hybrid Mount 集成尝试。

---

## <a id="日本語"></a>🇯🇵 日本語

| 項目 | 💿 Legacy | ⚡ OverlayFS Image | 📂 OverlayFS Data | 🧩 Metamodule (auto) |
| :--- | :--- | :--- | :--- | :--- |
| **方式** | 選択したファイル／ディレクトリを bind mount | EROFS イメージ + OverlayFS | ディレクトリ + OverlayFS | 検出されたプロバイダーに委任 |
| **保存先** | モジュール内 `odm/` | `odm.img` | `overlay/odm/` | プロバイダーにより `odm/` または `system/odm/` |
| **loop デバイス** | なし | あり | なし | プロバイダーによる |
| **要件** | `/odm` に対応する対象が存在 | EROFS、OverlayFS、loop、`mkfs.erofs` | カーネルの OverlayFS | 有効な互換プロバイダー |

Legacy は対象が存在するファイル／ディレクトリを選択的に bind mount します。OverlayFS Image は `odm/` を EROFS イメージにし、起動時に loop 経由でマウントします。OverlayFS Data はイメージを作らず `overlay/odm/` を使用します。Metamodule (auto) は検出されたメタモジュールにマウントを委任します。

---

## <a id="español"></a>🇪🇸 Español

| Parámetro | 💿 Legacy | ⚡ OverlayFS Image | 📂 OverlayFS Data | 🧩 Metamodule (auto) |
| :--- | :--- | :--- | :--- | :--- |
| **Implementación** | Bind mounts selectivos | Imagen EROFS + OverlayFS | Carpeta + OverlayFS | Proveedor detectado |
| **Almacenamiento** | `odm/` del módulo | `odm.img` | `overlay/odm/` | `odm/` o `system/odm/`, según el proveedor |
| **Dispositivo loop** | No | Sí | No | Depende del proveedor |
| **Requisitos** | Destinos existentes en `/odm` | EROFS, OverlayFS, loop y `mkfs.erofs` | OverlayFS en el kernel | Proveedor compatible activo |

Legacy monta selectivamente archivos y directorios existentes. OverlayFS Image empaqueta `odm/` en una imagen EROFS montada mediante loop al arrancar. OverlayFS Data usa `overlay/odm/` sin crear una imagen. Metamodule (auto) delega el montaje al proveedor detectado.

---

## <a id="العربية"></a>🇦🇪 العربية

| المعيار | 💿 Legacy | ⚡ OverlayFS Image | 📂 OverlayFS Data | 🧩 Metamodule (auto) |
| :--- | :--- | :--- | :--- | :--- |
| **التنفيذ** | ربط انتقائي للملفات والمجلدات | صورة EROFS مع OverlayFS | مجلد مع OverlayFS | مزوّد تركيب يتم اكتشافه |
| **التخزين** | `odm/` داخل الوحدة | `odm.img` | `overlay/odm/` | `odm/` أو `system/odm/` حسب المزوّد |
| **جهاز loop** | لا | نعم | لا | حسب المزوّد |
| **المتطلبات** | وجود الأهداف المقابلة في `/odm` | EROFS وOverlayFS وloop و`mkfs.erofs` | دعم OverlayFS في النواة | مزوّد متوافق ومفعّل |

يربط Legacy الملفات والمجلدات المحددة فقط. ينشئ OverlayFS Image صورة EROFS ويثبتها عبر loop عند الإقلاع. يستخدم OverlayFS Data المجلد غير المضغوط `overlay/odm/`. يفوّض Metamodule (auto) عملية التركيب إلى المزوّد المكتشف.

---

## <a id="हिन्दी"></a>🇮🇳 हिन्दी

| पैरामीटर | 💿 Legacy | ⚡ OverlayFS Image | 📂 OverlayFS Data | 🧩 Metamodule (auto) |
| :--- | :--- | :--- | :--- | :--- |
| **कार्यान्वयन** | चुनिंदा bind mounts | EROFS इमेज + OverlayFS | फ़ोल्डर + OverlayFS | खोजे गए प्रदाता को सौंपा जाता है |
| **स्टोरेज** | मॉड्यूल का `odm/` | `odm.img` | `overlay/odm/` | प्रदाता के अनुसार `odm/` या `system/odm/` |
| **Loop डिवाइस** | नहीं | हाँ | नहीं | प्रदाता पर निर्भर |
| **आवश्यकताएँ** | `/odm` में लक्ष्य मौजूद हों | EROFS, OverlayFS, loop, `mkfs.erofs` | कर्नेल में OverlayFS | सक्रिय संगत प्रदाता |

Legacy चुनिंदा फ़ाइलों और फ़ोल्डरों को bind mount करता है। OverlayFS Image EROFS इमेज बनाता है; OverlayFS Data बिना इमेज के `overlay/odm/` का उपयोग करता है। Metamodule (auto) माउंटिंग को मिले हुए मेटामॉड्यूल को सौंपता है।

---

## <a id="polski"></a>🇵🇱 Polski

| Parametr | 💿 Legacy | ⚡ OverlayFS Image | 📂 OverlayFS Data | 🧩 Metamodule (auto) |
| :--- | :--- | :--- | :--- | :--- |
| **Implementacja** | Selektywne bind-mounty | Obraz EROFS + OverlayFS | Katalog + OverlayFS | Wykryty dostawca montowania |
| **Przechowywanie** | `odm/` modułu | `odm.img` | `overlay/odm/` | `odm/` lub `system/odm/`, zależnie od dostawcy |
| **Urządzenie loop** | Nie | Tak | Nie | Zależnie od dostawcy |
| **Wymagania** | Istniejące cele w `/odm` | EROFS, OverlayFS, loop i `mkfs.erofs` | OverlayFS w jądrze | Aktywny zgodny dostawca |

Legacy montuje wybrane pliki i katalogi. OverlayFS Image tworzy obraz EROFS montowany przez loop podczas uruchamiania. OverlayFS Data używa rozpakowanego `overlay/odm/`. Metamodule (auto) przekazuje montowanie wykrytemu dostawcy.

---

## <a id="deutsch"></a>🇩🇪 Deutsch

| Parameter | 💿 Legacy | ⚡ OverlayFS Image | 📂 OverlayFS Data | 🧩 Metamodule (auto) |
| :--- | :--- | :--- | :--- | :--- |
| **Implementierung** | Selektive Bind-Mounts | EROFS-Image + OverlayFS | Verzeichnis + OverlayFS | Erkannter Mount-Provider |
| **Speicherort** | `odm/` im Modul | `odm.img` | `overlay/odm/` | Je nach Provider `odm/` oder `system/odm/` |
| **Loop-Gerät** | Nein | Ja | Nein | Providerabhängig |
| **Voraussetzungen** | Vorhandene Ziele unter `/odm` | EROFS, OverlayFS, Loop und `mkfs.erofs` | OverlayFS im Kernel | Aktiver kompatibler Provider |

Legacy bind-mountet ausgewählte Dateien und Verzeichnisse. OverlayFS Image erstellt ein EROFS-Image, das beim Start über Loop eingebunden wird. OverlayFS Data verwendet `overlay/odm/` ohne Image. Metamodule (auto) übergibt das Mounting an den erkannten Provider.
