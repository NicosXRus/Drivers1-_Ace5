# ⚙️ Mount Modes Documentation / Документация режимов монтирования

---

## <a id="русский"></a>🇷🇺 Русский

### Сравнение режимов монтирования

| Параметр | 💿 Legacy | ⚡ OverlayNative |
| :--- | :--- | :--- |
| **Технология** | Direct Bind Mount (`mount --bind`) | OverlayFS (`mount -t overlay`) + EROFS |
| **Хранение файлов** | Несжатые файлы в директории модуля | Сжатый образ `odm.img` (LZ4HC) |
| **Логика монтирования** | Точечная замена объявленных файлов/папок | Слоистое объединение и изоляция директорий |
| **Использование `loop`-устройств** | ❌ Не используются | 🛠️ 1 виртуальный диск в `/mnt/ace5dv` |
| **Совместимость** | Абсолютная (все ядра) | Совместимые ядра (поддержка EROFS + OverlayFS) |

#### 1. 💿 Legacy Mode
* **Принцип работы:** Напрямую подменяет конкретные файлы и папки через `mount --bind`.
* **Плюсы:** Работает абсолютно на всех ядрах, не требует ресурсов на компиляцию образа при установке.
* **Минусы:** Каждая директория или файл должны явно монтироваться отдельной командой; файлы занимают больше места.

#### 2. ⚡ OverlayNative Mode
* **Принцип работы:** Упаковывает файлы модуля в сжатый образ EROFS (`odm.img`), монтирует его в `/mnt/ace5dv/odm` и накладывает слои OverlayFS поверх системного раздела `/odm`. Для изолируемых каталогов использует пустышку-заглушку `empty`.
* **Плюсы:** Высокая степень сжатия LZ4HC, чистая структура VFS, защита файлов модуля от перезаписи на уровне ядра.
* **Минусы:** Требует чуть больше времени на сборку при установке; необходима поддержка EROFS и OverlayFS в ядре.

> 💡 **Рекомендация:** Используйте **OverlayNative** как основной и наиболее эффективный режим для современных устройств. Переходите на **Legacy** только в случае проблем с работой OverlayFS на кастомных ядрах.

---

## <a id="english"></a>🇬🇧 English

### Mount Modes Comparison

| Parameter | 💿 Legacy | ⚡ OverlayNative |
| :--- | :--- | :--- |
| **Technology** | Direct Bind Mount (`mount --bind`) | OverlayFS (`mount -t overlay`) + EROFS |
| **File Storage** | Uncompressed files in module dir | Compressed image `odm.img` (LZ4HC) |
| **Mount Logic** | Targeted replacement of declared files/folders | Layered merging and directory isolation |
| **`loop` devices usage** | ❌ Not used | 🛠️ 1 virtual disk in `/mnt/ace5dv` |
| **Compatibility** | Universal (all kernels) | Compatible kernels (EROFS + OverlayFS support) |

#### 1. 💿 Legacy Mode
* **How it works:** Directly replaces specific files and directories using `mount --bind`.
* **Pros:** Works universally on all kernels, zero build-time CPU usage during installation.
* **Cons:** Every file or folder must be explicitly mounted line-by-line; takes more storage space.

#### 2. ⚡ OverlayNative Mode
* **How it works:** Packs module files into a compressed EROFS image (`odm.img`), mounts it to `/mnt/ace5dv/odm`, and applies OverlayFS layers over `/odm`. Uses a dynamic `empty` directory to isolate target folders.
* **Pros:** Significant storage savings via LZ4HC, clean VFS structure, read-only kernel-level protection.
* **Cons:** Requires a few seconds to compile the image during installation; needs kernel support for EROFS/OverlayFS.

> 💡 **Recommendation:** Use **OverlayNative** as the primary and most efficient mode for ColorOS / OxygenOS. Switch to **Legacy** only if you encounter issues with OverlayFS on custom kernels.

---

## <a id="español"></a>🇪🇸 Español

### Comparación de Modos de Montaje

| Parámetro | 💿 Legacy | ⚡ OverlayNative |
| :--- | :--- | :--- |
| **Tecnología** | Direct Bind Mount (`mount --bind`) | OverlayFS (`mount -t overlay`) + EROFS |
| **Almacenamiento** | Archivos sin comprimir en el directorio del módulo | Imagen comprimida `odm.img` (LZ4HC) |
| **Lógica de montaje** | Reemplazo puntual de archivos/carpetas | Fusión por capas e aislamiento de directorios |
| **Uso de `loop`** | ❌ No se utilizan | 🛠️ 1 disco virtual en `/mnt/ace5dv` |
| **Compatibilidad** | Universal (todos los núcleos) | Núcleos compatibles (soporte EROFS + OverlayFS) |

#### 1. 💿 Legacy Mode
* **Funcionamiento:** Reemplaza directamente archivos y carpetas mediante `mount --bind`.
* **Ventajas:** Compatibilidad universal en cualquier núcleo, instalación instantánea sin compilación.
* **Desventajas:** Requiere declarar cada archivo o carpeta manualmente; ocupa más espacio en disco.

#### 2. ⚡ OverlayNative Mode
* **Funcionamiento:** Empaqueta los archivos en una imagen comprimida EROFS (`odm.img`), la monta en `/mnt/ace5dv/odm` y aplica capas OverlayFS sobre `/odm`.
* **Ventajas:** Gran ahorro de espacio con LZ4HC, estructura VFS limpia, protección de archivos a nivel de núcleo.
* **Desventajas:** Toma unos segundos compilar la imagen durante la instalación; requiere soporte del núcleo.

> 💡 **Recomendación:** Utilice **OverlayNative** como modo principal y más eficiente para ColorOS / OxygenOS. Cambie a **Legacy** solo si tiene problemas con OverlayFS en kernels personalizados.

---

## <a id="中文"></a>🇨🇳 中文

### 挂载模式对比

| 参数 | 💿 Legacy | ⚡ OverlayNative |
| :--- | :--- | :--- |
| **技术基础** | 直接绑定挂载 (`mount --bind`) | OverlayFS (`mount -t overlay`) + EROFS |
| **文件存储** | 模块目录中的未压缩文件 | 压缩镜像 `odm.img` (LZ4HC) |
| **挂载逻辑** | 指定文件/文件夹的定向替换 | 图层合并与特定目录隔离 |
| **`loop` 设备使用** | ❌ 不使用 | 🛠️ `/mnt/ace5dv` 中的 1 个虚拟盘 |
| **兼容性** | 完全通用 (所有内核) | 兼容内核 (需支持 EROFS + OverlayFS) |

#### 1. 💿 Legacy 模式
* **工作原理：** 通过 `mount --bind` 命令直接替换特定的系统文件和文件夹。
* **优点：** 兼容所有 Android 内核，安装时无需构建镜像，速度极快。
* **缺点：** 每个文件或文件夹都需要单独编写挂载指令；占用较多存储空间。

#### 2. ⚡ OverlayNative 模式
* **工作原理：** 将模块文件打包为 LZ4HC 压缩的 EROFS 镜像 (`odm.img`)，挂载至 `/mnt/ace5dv/odm`，并通过 OverlayFS 覆盖于 `/odm` 分区之上。
* **优点：** 通过 LZ4HC 显著节省存储空间，保持 VFS 干净整洁，内核层只读保护。
* **缺点：** 安装过程中需要几秒钟编译镜像；需要内核支持 EROFS 与 OverlayFS。

> 💡 **建议：** 推荐优先使用 **OverlayNative**，这是适用于 ColorOS / OxygenOS 的主要且最高效的模式。仅在自定义内核遇到 OverlayFS 问题时才切换至 **Legacy**。

---

## <a id="日本語"></a>🇯🇵 日本語

### マウントモードの比較

| パラメータ | 💿 Legacy | ⚡ OverlayNative |
| :--- | :--- | :--- |
| **使用技術** | ダイレクト Bind マウント (`mount --bind`) | OverlayFS (`mount -t overlay`) + EROFS |
| **ストレージ** | モジュールディレクトリ内の非圧縮ファイル | 圧縮イメージ `odm.img` (LZ4HC) |
| **マウントロジック** | 指定されたファイル/フォルダの個別置換 | レイヤー結合とディレクトリの分離 |
| **`loop` デバイス** | ❌ 未使用 | 🛠️ `/mnt/ace5dv` 内の1つの仮想ディスク |
| **互換性** | 完全な互換性 (全カーネル) | 互換カーネル (EROFS + OverlayFS サポートが必要) |

#### 1. 💿 Legacy モード
* **仕組み:** `mount --bind` を使用して、特定のファイルおよびフォルダ stray を直接置き換えます。
* **メリット:** すべてのカーネルで確実に動作し、インストール時のコンパイル処理が不要です。
* **デメリット:** ファイルやフォルダごとにマウント記述が必要で、ストレージ容量をより多く消費します。

#### 2. ⚡ OverlayNative モード
* **仕組み:** ファイルを圧縮 EROFS イメージ (`odm.img`) にパックし、`/mnt/ace5dv/odm` にマウントして OverlayFS レイヤーを `/odm` に適用します。
* **メリット:** LZ4HC 圧縮による容量削減、クリーンな VFS 構造、カーネルレベルでの書き込み保護。
* **デメリット:** インストール時にイメージ構築の時間が数秒かかります。カーネルのサポートが必要です。

> 💡 **推奨：** ColorOS / OxygenOS に最適なメインモードである **OverlayNative** の使用を推奨します。カスタムカーネル等で OverlayFS に問題が発生した場合のみ **Legacy** に切り替えてください。

---

## <a id="українська"></a>🇺🇦 Українська

### Порівняння режимів монтування

| Параметр | 💿 Legacy | ⚡ OverlayNative |
| :--- | :--- | :--- |
| **Технологія** | Пряме Bind-монтування (`mount --bind`) | OverlayFS (`mount -t overlay`) + EROFS |
| **Зберігання файлів** | Нестиснені файли в директорії модуля | Стиснений образ `odm.img` (LZ4HC) |
| **Логіка монтування** | Точкова заміна оголошених файлів/папок | Шарове об'єднання та ізоляція директорій |
| **Використання `loop`** | ❌ Не використовуються | 🛠️ 1 віртуальний диск у `/mnt/ace5dv` |
| **Сумісність** | Абсолютна (усі ядра) | Сумісні ядра (підтримка EROFS + OverlayFS) |

#### 1. 💿 Legacy Mode
* **Принцип роботи:** Напряму замінює конкретні файли та папки за допомогою `mount --bind`.
* **Плюси:** Працює на будь-яких ядрах, не витрачає ресурси на компіляцію образу під час встановлення.
* **Мінуси:** Кожен файл чи папка мають бути явно прописані окремою командою; займає більше місця.

#### 2. ⚡ OverlayNative Mode
* **Принцип роботи:** Упаковує файли у стиснений образ EROFS (`odm.img`), монтує його в `/mnt/ace5dv/odm` та накладає шари OverlayFS поверх системного розділу `/odm`.
* **Плюси:** Значна економія пам'яті завдяки LZ4HC, чиста структура VFS, захист файлів на рівні ядра.
* **Мінуси:** Потребує кількох секунд для збірки образу при встановленні; необхідна підтримка в ядрі.

> 💡 **Рекомендація:** Використовуйте **OverlayNative** як основний та найефективніший режим для прошивок ColorOS / OxygenOS. Перемикайтеся на **Legacy** лише у разі проблем із роботою OverlayFS на кастомних ядрах.

---

## <a id="հայերեն"></a>🇦🇲 Հայերեն

### Մոնտաժման ռեժիմների համեմատություն

| Պարամետր | 💿 Legacy | ⚡ OverlayNative |
| :--- | :--- | :--- |
| **Տեխնոլոգիա** | Ուղիղ Bind մոնտաժում (`mount --bind`) | OverlayFS (`mount -t overlay`) + EROFS |
| **Ֆայլերի պահպանում** | Չսեղմված ֆայլեր մոդուլի թղթապանակում | Սեղմված պատկեր `odm.img` (LZ4HC) |
| **Մոնտաժման տրամաբանություն** | Նշված ֆայլերի/թղթապանակների կետային փոխարինում | Շերտավոր միավորում և դիրեկտորիաների մեկուսացում |
| **`loop` սարքեր** | ❌ Չեն օգտագործվում | 🛠️ 1 վիրտուալ սկավառակ `/mnt/ace5dv`-ում |
| **Համատեղելիություն** | Բացարձակ (բոլոր միջուկները) | Համատեղելի միջուկներ (EROFS + OverlayFS աջակցություն) |

#### 1. 💿 Legacy ռեժիմ
* **Աշխատանքի սկզբունքը:** Ուղղակիորեն փոխարինում է որոշակի ֆայլերը և թղթապանակները `mount --bind`-ի միջոցով:
* **Առավելությունները:** Աշխատում է բոլոր միջուկների վրա, տեղադրման ժամանակ կոմպիլյացիա չի պահանջում:
* **Թերությունները:** Յուրաքանչյուր ֆայլ կամ թղթապանակ պետք է առանձին նշվի; ավելի շատ տեղ է զբաղեցնում:

#### 2. ⚡ OverlayNative ռեժիմ
* **Աշխատանքի սկզբունքը:** Ֆայլերը սեղմում է EROFS պատկերի մեջ (`odm.img`), տեղադրում `/mnt/ace5dv/odm`-ում և OverlayFS շերտերը տարածում `/odm` բաժնի վրա:
* **Առավելությունները:** Հիշողության զգալի տնտեսում LZ4HC-ի շնորհիվ, մաքուր VFS կառուցվածք, պաշտպանություն միջուկի մակարդակով:
* **Թերությունները:** Պահանջում է մի քանի վայրկյան պատկերը հավաքելու համար; պահանջում է EROFS և OverlayFS աջակցություն միջուկում:

> 💡 **Խորհուրդ:** Օգտագործեք **OverlayNative**, քանի որ դա ColorOS / OxygenOS-ի համար հիմնական և ամենաարդյունավետ ռեժիմն է: Անցեք **Legacy**-ին միայն այն դեպքում, եթե կաստոմ միջուկների վրա OverlayFS-ի հետ կապված խնդիրներ ունեք:
> 
