<div align="center">

# BBK ROM Porting Guide

**Port ColorOS / OxygenOS / RealmeUI 15 to your device: system and vendor, step by step.**

![ColorOS](https://img.shields.io/badge/ColorOS-15-00C853?style=for-the-badge)
![OxygenOS](https://img.shields.io/badge/OxygenOS-15-F5010C?style=for-the-badge)
![RealmeUI](https://img.shields.io/badge/RealmeUI-6.0-FFC915?style=for-the-badge)

</div>

---

## Overview

This guide has two parts:

1. **System port**: take an OPPO / OnePlus / Realme OTA, dump it, rebuild the partition layout and edit the props.
2. **Vendor and ODM**: set up your device's vendor (and odm) so it works with the ported ROM.

OTA download links for ColorOS 15, OxygenOS 15 and RealmeUI 6.0 are in the last section.

Click a section below to expand it.

## Tools

| Tool | Used for |
| --- | --- |
| `aria2c` | Fast OTA downloads |
| `payload-dumper-go` | Dumping images out of `payload.bin` |
| `crb` + `crb debloater` | Extracting / building partitions and debloating |

---

<details>
<summary><b>Part 1: System Port</b></summary>

<br>

### 1. Get the OTA

1. Get the OTA from **4pda**, or use the OTA links section below.
2. Download it with `aria2c`:

   ```bash
   aria2c -x16 -s16 -j5 --file-allocation=none <ota_link>
   ```

3. Download **payload-dumper-go**.
4. Extract `payload.bin` from the OTA and dump it (drag `payload.bin` onto the exe).

### 2. Prepare the partitions

5. Make a folder called `images` and copy all the `my_*` images, plus `system`, `system_ext` and `product`, into it.
6. Extract everything in `crb`.
7. Go to `system` and delete all the `my_*` folders.
8. Add all the `my_*` partitions to the **root of `system`**.

### 3. Edit props and add components

9. Open `my_manifest/build.prop`, delete the `first.api` line and add your device name in `market.name`.
10. Add `apex` to `system_ext/apex`.
11. Add `overlays` to `my_product`.
12. Open `my_product/build.prop`, search for `oplusrom.display` and add your name (for example `15.0 | by king`).
13. In the same file, erase the `screenhole` line.
14. Still in `my_product`, search for `lcd.density` and set it to `440`, then remove these lines:

    ```properties
    ro.density.screenzoom.fdh
    ro.density.screenzoom.qdh
    ro.oplus.density.fhd_default
    ro.oplus.density.qhd_default
    ro.oplus.resolution.low
    ro.oplus.resolution.high
    ```

15. Add the following to `system/build.prop`:

    ```properties
    import /my_bigball/build.prop
    import /my_carrier/build.prop
    import /my_engineering/build.prop
    import /my_heytap/build.prop
    import /my_manifest/build.prop
    import /my_product/build.prop
    import /my_region/build.prop
    import /my_stock/build.prop
    ```

### 4. Debloat and build

16. Debloat using the **crb debloater**.
17. Build `system`, `system_ext` and `product`.
18. Done.

> [!IMPORTANT]
> If your device uses a **legacy partition** layout, add `product` and `system_ext` into the root of `system` **before** building (step 17).

</details>

<details>
<summary><b>Part 2: Vendor and ODM</b></summary>

<br>

A tutorial for ColorOS 15 vendor setup.

1. Get your device's stock vendor, or a vendor from an AOSP ROM (MIUI / HyperOS / AOSP, Android 10 to 15), and extract it.
2. Get the vendor from the OPLUS device you're porting from and extract it.
3. From the OPLUS vendor, copy `group` and `passwd` from `vendor/etc` to your device's `vendor/etc`.
4. Open `odm/etc/build.prop` and add these lines, replacing the `<placeholders>`:

   ```properties
   ro.build.device_family=<OP + CPU model, e.g. OPSM8150>
   ro.product.oplus.cpuinfo=<CPU model>
   ro.product.brand=OPPO
   ro.product.manufacturer=OPPO
   ro.product.model=<device codename>
   ro.vendor.oplus.market.name=<device name>
   ro.vendor.oplus.camera.frontCamSize=<front cam size in MP>
   ro.vendor.oplus.camera.backCamSize=<back cam sizes in MP, e.g. 50MP+50MP>
   ro.oplus.display.screenSizeInches.primary=<screen size in inches>
   ro.build.version.oplusrom=V15.0
   ro.vendor.audio.policy.engine.odm=true
   ```

   > [!WARNING]
   > Some devices have the odm inside `vendor/odm`.

5. Build `vendor` and `odm`, or just `vendor`. Done.

</details>

<details>
<summary><b>OTA Links</b></summary>

<br>

### ColorOS 15

| Device | Version | Download |
| --- | --- | --- |
| OnePlus Ace 3V | 15.0.0.820CN | [Download](https://gauss-componentotacostmanual-cn.allawnfs.com/remove-d9b022f7162acbc7d240b60a09aca5a9/component-ota/25/05/08/32d9d10485854d66a2bb98a6f0869de3.zip) |
| OPPO Find X7 Ultra | 15.0.0.820CN | [Download](https://gauss-componentotacostmanual-cn.allawnfs.com/remove-5ff7b73f02e3a7734e51088bd92adadf/component-ota/25/05/08/e57470c32fb740208864b5f78d8a3136.zip) |
| OPPO Find X7 Ultra Satellite Edition | 15.0.0.822CN | [Download](https://www.coolapk.com/link?url=https%3A%2F%2Fgauss-componentotacostmanual-cn.allawnfs.com%2Fremove-dd4f76b1606782a6425e497e10360f47%2Fcomponent-ota%2F25%2F05%2F27%2Fd711c0332e184da2bb83a89bf573221d.zip) |

### OxygenOS 15

| Device | Version | Download |
| --- | --- | --- |
| OnePlus 12R | 15.0.0.801EU | [Download](https://gauss-componentotacostmanual-eu.allawnofs.com/remove-5c5d9bcf3317fc5d6ad3a8606be00e4d/component-ota/25/04/17/c4488027bf0249bcac8ad63d5d7b74ac.zip) |

### RealmeUI 6.0

| Device | Version | Download |
| --- | --- | --- |
| Realme GT 7 Pro | 15.0.0.250CN | [Download](https://gauss-opexcostmanual-cn.allawnfs.com/remove-cba4bae03eab9d1362a2a40a032cf2d0/component-ota/25/04/10/6d78e7ec28884d47b69c2c7bd449337d.zip) |

</details>

---

## Disclaimer

Flashing modified ROMs can brick your device. Back up your data first and follow these steps at your own risk.
