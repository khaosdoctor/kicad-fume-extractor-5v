# Fume Extractor 5V

KiCad project for a small, battery-powered fume extractor: a 9V battery feeds an
L7805 linear regulator to drive a 5V fan through a BC337 transistor, with a
potentiometer for speed control, a trimmer for calibration, a power switch and
an LED indicator.

This is my first electronics project, so it's a work in progress — expect
multiple revisions as the design gets refined.

## Hardware overview

| Ref  | Part                     | Function                        |
|------|--------------------------|----------------------------------|
| BT1  | 9V battery cell          | Power source                     |
| U1   | L7805ACV                 | 5V linear regulator               |
| Q1   | BC337                    | Fan drive transistor              |
| J1   | 2-pin connector          | Fan output                        |
| RV1  | 500R potentiometer       | Fan speed control                 |
| RV2  | 1K trimmer               | Calibration                       |
| R1   | 350R resistor            | Bias/current limiting             |
| C1/C2| Capacitors               | Regulator input/output filtering  |
| D1   | LED                      | Power indicator                   |
| SW1  | SPST switch              | Power on/off                      |

The board is a 2-layer PCB designed in KiCad 10.

## Generating manufacturing files with KiBot

This project uses [KiBot](https://github.com/INTI-CMNB/KiBot) to generate
gerbers, drill files, pick & place, BOM, documentation and 3D outputs
straight from the KiCad source files, via `kibot.yaml`.

### Outputs produced

| Group           | Outputs                                                        | Directory              |
|-----------------|------------------------------------------------------------------|-------------------------|
| `fabrication`   | Gerbers, Excellon drill files                                    | `Manufacturing/Gerbers` |
| `assembly`      | Pick & place position file, BOM (CSV)                            | `Manufacturing/Assembly`|
| `documentation` | Schematic PDF, PCB PDF, interactive HTML BOM                      | `Documentation`, `bom`  |
| `3d`            | STEP model, top/bottom 3D renders                                 | `3D`                    |

A zipped bundle of gerbers + drill files ready to send to a fab house is also
produced under `Manufacturing/manufacturing_zip.zip`. A JLCPCB-specific BOM
(`bom_jlcpcb`) filtered to parts with an LCSC part number is available but not
run by default.

### Running locally

Install KiCad (10.x) and KiBot, then from the repository root run:

```bash
pip install kibot
kibot -e "fume Extractor.kicad_sch" -b "fume Extractor.kicad_pcb"
```

To generate a single group or output, pass its name as a target, e.g.:

```bash
kibot -e "fume Extractor.kicad_sch" -b "fume Extractor.kicad_pcb" fabrication
```

### Running with Docker

No local KiCad/KiBot install is required if you have Docker:

```bash
docker run --rm -v "$(pwd)":/mnt ghcr.io/inti-cmnb/kicad10_auto_full:latest \
  kibot -e "fume Extractor.kicad_sch" -b "fume Extractor.kicad_pcb"
```

### Running in CI

The [`kibot.yml`](.github/workflows/kibot.yml) workflow runs KiBot on every
push and pull request and uploads the generated files as a build artifact.
