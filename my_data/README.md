# Put your own runs here

Drag a run folder from your computer into this `my_data` folder in the Explorer on the left.
DeGenRESOLVE expects this layout:

```
my_run/
├── fastq_pass/
│   ├── barcode01/   *.fastq.gz
│   └── barcode02/   *.fastq.gz
└── reference/
    └── reference.fasta      (exactly one .fasta or .fa)
```

Then, in the DeGenRESOLVE window: **Analysis → Browse** → choose `my_data/my_run` → **Start**.

Files here live only in your codespace. Nothing is uploaded to the authors, and nothing in
this folder is committed to the repository.
