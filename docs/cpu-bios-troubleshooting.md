# CPU clock troubleshooting and BIOS update

## System and symptom

- **CPU:** Intel Core Ultra 5 250K Plus (18 cores)
- **Motherboard:** Gigabyte B860 DS3H WIFI6E rev. 1.0
- **Host:** Ubuntu
- **Initial BIOS:** F10, as reported during troubleshooting
- **Symptom:** A slow desktop and a sustained CPU clock near 400 MHz under load

This record documents the measurements and observed recovery. The version shown
after the BIOS update was not captured in this record, so the updated firmware
version is not asserted here.

## Diagnosis

1. Checked CPU and thermal telemetry. Under sustained CPU load, `turbostat`
   reported roughly **399–400 Bzy_MHz**, about **49% Busy**, approximately
   **7.27 W** package power, and **36 °C** package temperature.
2. Checked frequency scaling. The reported `scaling_max_freq` values were
   about **1.68 GHz / 1.32 GHz** even with
   `intel_pstate max_perf_pct=100`. A BIOS reset had not cleared the
   sustained 400 MHz behavior.
3. Low temperatures made overheating an unlikely explanation for that test.
   A faulty rear case fan was a separate issue; no test proved it caused
   the CPU clock limit.

## Action and result

Updated the motherboard BIOS through Gigabyte Q-Flash using the BIOS file for
the **exact motherboard model and revision**. An earlier Q-Flash attempt
could not read the file on the USB drive; preparing a FAT-formatted USB drive
with the extracted BIOS file allowed the update process to proceed.

After the update, a repeat CPU load test reported **100% Busy** and
approximately **4,597–5,086 Bzy_MHz** across observed samples, with a
**64–65 °C** package temperature and roughly **113 W** package power.
The 400 MHz under-load limit was gone. The before/after evidence strongly
associates the BIOS update with the recovery; it does not isolate a specific
firmware bug or prove that no other setting changed during the update.

## What I learned

- Measure clock speed **under load**, since low clocks at idle are normal.
- Compare frequency, utilization, power, and temperature together.
- Confirm the motherboard model and hardware revision before selecting a BIOS
  image, and verify results with the same type of load test afterward.
