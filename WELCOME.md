# Welcome to DeGenRESOLVE online

This codespace is a small Linux machine (4 cores, 16 GB RAM) running **DeGenRESOLVE v1.0.1**
with the exact tool versions its published results were produced with. The real desktop app
runs on a virtual screen that you open in a browser tab.

## 1. Open the app

When VS Code asks **"Do you trust the authors of the files in this folder?"**, click
**Trust Folder & Continue**. If it then asks whether to allow automatic tasks, click
**Allow**. That opens the *DeGenRESOLVE run monitor* terminal, which shows progress and keeps
the codespace awake during long runs.

The desktop may open in a new browser tab by itself. If it doesn't:

1. Open the **Ports** tab in the panel at the bottom of this window.
2. Hover over **DeGenRESOLVE desktop (6080)** and click the globe icon (*Open in Browser*).
3. If the page shows a **Connect** button, click it.

DeGenRESOLVE starts with the desktop. On a brand-new codespace that can take a minute
after this window opens. If the window isn't there, type `degenresolve-gui` in the
terminal, or right-click the desktop and choose **DeGenRESOLVE**.

## 2. Try the demo (5–7 minutes)

`demo_data/` holds two real influenza A barcodes and the 37-record reference panel that
DeGenRESOLVE ships as its test dataset.

1. The app's **Input Directory** is already set to `demo_data`. If it isn't, go to
   **Analysis → Browse** and pick `/workspaces/DeGenRESOLVE-online/demo_data`.
2. **Configuration → Load Configuration** → `demo_data/demo_settings.json`. These are the
   settings of the reference run, including *Influenza* filter mode.
3. **Analysis → Start.** Progress shows in the app and in the *DeGenRESOLVE run monitor*
   terminal below.
4. When it finishes, check that it reproduced the published reference run exactly:

   ```bash
   degenresolve-compare demo_data
   ```

## 3. Analyse your own data

1. Drag your run folder from your computer into **`my_data/`** in the Explorer on the left.
   It needs `fastq_pass/barcodeXX/*.fastq.gz` and a `reference/` folder with exactly one
   `.fasta` (see `my_data/README.md`).
2. In the app: **Analysis → Browse** → `my_data/<your run>`, set your parameters in
   **Configuration**, then **Start**.

## 4. Download your results

Results are written to `<run>/results/` and `<run>/log/`. To download them as one file:

```bash
degenresolve-zip my_data/<your run>
```

Then right-click the `.zip` in the Explorer and choose **Download**. You can also right-click
any single file, such as a consensus FASTA, and download it directly.

Lowercase bases in a consensus FASTA are **low-confidence calls** (depth < 3 or quality < 10),
not repeats. See the in-app *Help → Bench Scientist Guide*.

## 5. Stop the codespace when you're done

This codespace runs on **your own GitHub account's free monthly allowance**: 120 core-hours on
GitHub Free, so about 30 hours a month on this 4-core machine. GitHub stops it after
30 minutes without activity. To stop it now, click **Codespaces** in the bottom-left corner
→ **Stop Current Codespace**. Delete it at <https://github.com/codespaces> once you've
downloaded what you need, because stored codespaces count against your free storage.

## Your data

Files you upload stay in this codespace, under your GitHub account. Nothing is sent to the
DeGenRESOLVE authors. Only upload data you are allowed to process on a cloud service. For
example, do not upload identifiable patient information.

---

DeGenRESOLVE by Shoaib Saikat · MIT licence ·
[source code](https://github.com/Shoaib-Saikat/DeGenRESOLVE-ONT-Sequencing-Data-Analyzer)
