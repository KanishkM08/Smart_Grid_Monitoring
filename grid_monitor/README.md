# Grid Monitor: Zephyr OS Micro-PMU + NILM load monitor (STM32 Nucleo-F446RE)

Tested build target: Zephyr v4.2.0, Zephyr SDK 0.17.0 (arm-zephyr-eabi), board `nucleo_f446re`.

## Quick start (workspace layout)

    my_workspace/
      grid_monitor/      <- this folder
      zephyr/ modules/   <- created by "west update"

    python -m venv .venv  &&  activate it
    pip install west "cmake<4" ninja pyelftools pyyaml
    west init -l grid_monitor
    west update
    pip install -r zephyr/scripts/requirements-base.txt
    west sdk install            (or install the Zephyr SDK 0.17.0 ARM toolchain by hand)
    west build -b nucleo_f446re grid_monitor
    west flash

Serial console: 115200 baud, 8N1 (ST-LINK virtual COM port). Type `grid help`.

## Layout

    src/main.c          start-up order
    src/sampler.c       dual-ADC + TIM2 trigger + circular DMA (STM32 HAL)
    src/protect.c       protection thread (prio 0), relay trip, zbus publisher
    src/cycle.c         mains-cycle detector, P/Q/PF maths      (host-tested)
    src/dsp.c           FFT features, classifier, transient tracker (host-tested)
    src/dsp_task.c      DSP/FFT thread (prio 2)
    src/telemetry.c     fault logging to flash + LED (prio 5)
    src/logstore.c      NVS: settings + fault log
    src/grid_shell.c    the "grid" shell commands
    boards/nucleo_f446re.overlay   relay pin, flash partitions, freed peripherals
    Kconfig, prj.conf   build configuration
    tools/              grid_console.py (serial capture), analyze_dump.py (PC analysis)
    tests/host/         unit tests that run on a PC:  make -C tests/host test

## WARNING
Parts of the hardware connect to 230 V AC. Bring up with low-voltage signals first and
have the wiring checked by a supervisor. Read the Interfacing Guide.
