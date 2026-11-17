# Grid Monitor Streamlit Dashboard — live waveform update

This version adds voltage and current plots. It polls the existing Zephyr shell command `grid dump 128` approximately every 700 ms and plots the latest rolling ADC samples.

**Limitation:** this is periodic snapshot polling, not gap-free continuous streaming. The existing shell command returns finite batches. True continuous waveform streaming requires a dedicated firmware telemetry mode. V/A plots use the dump header's `kv`/`ki` calibration factors and a rolling-mean offset estimate; validate calibration against known instruments before relying on the values.

## Update your Windows project
1. Stop Streamlit with `Ctrl+C`.
2. Extract this ZIP.
3. Copy the contents of `Grid_Monitor_Dashboard_Live` over your existing `C:\FINAL_PROJECT\Grid_Monitor_Dashboard` folder, replacing the files. Keep `.venv-dashboard` if it is inside that folder.
4. In PowerShell, activate your existing environment and run:

```powershell
.\.venv-dashboard\Scripts\Activate.ps1
python -m pip install --upgrade -r requirements.txt
streamlit run app.py
```

Close PuTTY before connecting to the COM port from Streamlit.

## Use
Connect to the Nucleo, enable **Live waveform updates**, and let the rolling graph collect samples. Start with 128 samples per snapshot. The first 1024 samples are shown as raw ADC counts while the dashboard estimates the channel offsets; then it shows provisional voltage/current estimates.
