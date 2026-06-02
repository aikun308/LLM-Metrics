# Data-Driven Characterization and Insights of Network Metrics

This repository contains the code and prompt templates for the paper:

**Data-Driven Characterization and Insights of Network Metrics from a Systematic LLM-Enabled Multi-Agent Literature Mining**

The project implements an LLM-enabled multi-agent literature-mining pipeline for analyzing network metrics in papers published in **IEEE/ACM Transactions on Networking**. The pipeline identifies network scenarios, extracts network performance metrics, infers inter-metric correlations, performs semantic metric deduplication, and generates the statistical and visualization results reported in the paper.

The accompanying paper analyzes **782 IEEE/ACM Transactions on Networking papers from 2022 to 2024** and reports a large-scale network-metric inventory, scenario-wise metric distributions, per-paper metric-correlation heatmaps, corpus-wide metric clustering, and scenario-specific metric structures.

---

## Project Overview

The code supports the following workflow:

1. **Paper preprocessing**
   - Load legally obtained PDF files.
   - Remove references, figure/table captions, formula-heavy fragments, headers, footers, and other non-informative content.
   - Split cleaned text into overlapping chunks.

2. **LLM-enabled multi-agent extraction**
   - **Scenario Identification Agent**: assigns each paper to a network scenario.
   - **Metric Extraction Agent**: extracts network metrics, units, and metric explanations.
   - **Correlation Inference Agent**: infers pairwise metric relationships from local textual evidence.
   - **Metric Deduplication Agent**: groups semantically equivalent metrics and selects canonical metric names.

3. **Aggregation and analysis**
   - Aggregate per-paper extraction results.
   - Count metric frequencies before and after deduplication.
   - Compute scenario-level metric statistics.
   - Build metric correlation matrices.

4. **Visualization**
   - Generate paper figures, including scenario distributions, rank-frequency plots, Sankey diagrams, per-paper heatmaps, corpus-wide metric clustering, and scenario-specific clustering/heatmap results.

---

## Repository Structure

```text
.
├── Code/
│   ├── api.py
│   ├── integrated.py
│   ├── integrated2.py
│   ├── fig_2.py
│   ├── fig_3.py
│   ├── fig_4.py
│   ├── fig_5.py
│   ├── fig_6.py
│   └── fig_7and8/
│       ├── Internet & IP networks.py
│       ├── Data center networks.py
│       ├── Edge_fog computing networks.py
│       └── Cellular mobile communication networks.py
│
├── prompts/
│   ├── 01_Chinese_scenario_identification.md
│   ├── 01_English_scenario_identification.md
│   ├── 02_Chinese_metric_extraction.md
│   ├── 02_English_metric_extraction.md
│   ├── 03_Chinese_correlation_inference.md
│   ├── 03_English_correlation_inference.md
│   ├── 04_Chinese_metric_deduplication.md
│   └── 04_English_metric_deduplication.md
│
├── examples/
│   └── example_paper[38].json
│
├── output/
│   ├── Detail/
│   │   ├── Integrated_Statistics.txt
│   │   ├── Statistics_of_all_metrics.txt
│   │   └── ... intermediate statistical files
│   ├── fig_4/
│   ├── fig_5/
│   │   ├── input/
│   │   │   ├── 2022_paper94_metrics_relations.pkl
│   │   │   ├── 2024_paper145_metrics_relations.pkl
│   │   │   ├── 2022_paper104_metrics_relations.pkl
│   │   │   └── 2022_paper4_metrics_relations.pkl
│   │   └── ... generated heatmaps
│   └── fig_6/
│
├── ToN paper title list.txt
└── Metrics list.txt
```

---

## Data and Copyright Notice

The original papers analyzed in this study are published in **IEEE/ACM Transactions on Networking** and are subject to publisher copyright restrictions.

Therefore, this repository **does not provide**:

- the full text of the IEEE/ACM Transactions on Networking papers;
- raw PDF files of the papers;
- extracted full-paper text dumps;
- any redistributed copyrighted article content.

The repository only provides the copyright-safe corpus identifier file:

```text
ToN paper title list.txt
```

This file lists the paper titles, publication information, and DOI metadata used to define the 2022–2024 IEEE/ACM Transactions on Networking corpus. Users who want to reproduce the full extraction pipeline must obtain the corresponding papers through legal and authorized access channels, such as institutional subscriptions, publisher access, or author-provided copies.

The example file:

```text
examples/example_paper[38].json
```

is a copyright-safe representative input/output example. It uses paraphrased summaries rather than redistributing original copyrighted paper text.

---

## Environment Requirements

The code was developed for Python-based experimental analysis and visualization.

Recommended environment:

```bash
conda create -n ton-metrics python=3.10
conda activate ton-metrics
```

Install Python dependencies:

```bash
pip install numpy matplotlib seaborn scikit-learn plotly kaleido
pip install langchain-openai langchain-community pypdf
```

For the Sankey diagram in `fig_4.py`, Ghostscript is recommended for IEEE-friendly PDF font embedding:

```bash
conda install -c conda-forge ghostscript
```

or install Ghostscript system-wide and make sure the `gs`, `gswin64c`, or `gswin32c` executable is available in your system path.

For LLM-based extraction and deduplication, set the DeepSeek API key:

```bash
export DEEPSEEK_API_KEY="your_api_key_here"
```

On Windows PowerShell:

```powershell
$env:DEEPSEEK_API_KEY="your_api_key_here"
```

The paper uses the following LLM configuration:

```text
Model: DeepSeek-Chat / DeepSeek-V3.2
Temperature: 0.1
Top-p: 0.8
Max tokens: 4096
Streaming: False
```

For clustering and visualization, the scripts fix the random seed to `42` where applicable.

---

## Path Configuration

Several scripts contain path constants near the top of the file. Before running the scripts, update these paths to match your local repository layout.

Common variables include:

```python
PKL_INPUT_DIR = "path/to/output_pkl/all"
PDF_OUTPUT_DIR = "path/to/output/directory"
INPUT_PKL_PATH = "path/to/intermediate_statistics.pkl"
INPUT_STATS_PKL = "path/to/intermediate_statistics.pkl"
OUTPUT_PDF = "path/to/output/figure.pdf"
```

The original scripts may contain Windows-style absolute paths used during the paper experiments. These should be replaced by local relative or absolute paths before reproduction.

For example:

```python
PKL_INPUT_DIR = "./output/output_pkl/all"
PDF_OUTPUT_DIR = "./output/fig_6"
```

Some intermediate files may use the filename:

```text
场景_指标统计中间数据.pkl
```

If your unzip tool converts Unicode filenames into escaped forms, rename the file back to the expected name or update the corresponding script path.

---

## How to Run

The repository supports two levels of reproduction:

1. **Full pipeline reproduction**, which requires legally obtained paper PDFs and an API key.
2. **Derived-result reproduction**, which uses provided intermediate files and example `.pkl` files where available.

### 1. Run the Per-Paper Extraction Pipeline

Use `Code/api.py` to process one paper or a directory of papers.

```bash
python Code/api.py
```

The script provides an interactive menu:

```text
1. Process a single PDF file
2. Process a directory of PDF files
3. Clear API cache
```

For each input PDF, the script produces:

```text
<paper_name>_metrics_relations.pkl
<paper_name>_results.txt
<paper_name>_heatmap.pdf
api_cache.pkl
```

The main `.pkl` file contains:

```python
{
    "network_type": ...,
    "metrics": ...,
    "metric_explanations": ...,
    "relations": ...,
    "heatmap_path": ...
}
```

This output is the core per-paper data format used by the downstream scripts.

### 2. Aggregate All Per-Paper Results

After generating or preparing all `*_metrics_relations.pkl` files, run:

```bash
python Code/integrated.py
```

Before running, update:

```python
PKL_INPUT_DIR = "path/to/all/per_paper_pkl_files"
PDF_OUTPUT_DIR = "path/to/output/Detail"
```

Main outputs include:

```text
dedup_rules.json
场景_指标统计中间数据.pkl
Integrated_Statistics.txt
网络指标频次幂律拟合图.pdf
```

`integrated.py` performs metric aggregation, semantic deduplication, scenario-level statistics, and corpus-level rank-frequency analysis. The final log-log version of the paper’s rank-frequency figure is generated by `fig_3.py`.

### 3. Export the Metric Inventory

Use `Code/integrated2.py` to export a sorted human-readable metric inventory from the intermediate statistics file:

```bash
python Code/integrated2.py \
  --input "output/Detail/场景_指标统计中间数据.pkl" \
  --output "Metrics list.txt"
```

If the original per-paper `.pkl` files are available, provide their directory to enrich the report with units and explanation examples:

```bash
python Code/integrated2.py \
  --input "output/Detail/场景_指标统计中间数据.pkl" \
  --output "Metrics list.txt" \
  --source-dir "output/output_pkl/all"
```

If only the aggregate intermediate file is available:

```bash
python Code/integrated2.py \
  --input "output/Detail/场景_指标统计中间数据.pkl" \
  --output "Metrics list.txt" \
  --no-source-scan
```

### 4. Generate Paper Figures

Most figure scripts require updating path constants before execution.

Generate Fig. 2:

```bash
python Code/fig_2.py
```

Generate Fig. 3:

```bash
python Code/fig_3.py
```

Generate Fig. 4:

```bash
python Code/fig_4.py
```

Generate Fig. 5 heatmaps from the provided example `.pkl` files:

```bash
python Code/fig_5.py \
  --pkl "output/fig_5/input/2022_paper94_metrics_relations.pkl" \
  --outdir "output/fig_5"

python Code/fig_5.py \
  --pkl "output/fig_5/input/2024_paper145_metrics_relations.pkl" \
  --outdir "output/fig_5"

python Code/fig_5.py \
  --pkl "output/fig_5/input/2022_paper104_metrics_relations.pkl" \
  --outdir "output/fig_5"

python Code/fig_5.py \
  --pkl "output/fig_5/input/2022_paper4_metrics_relations.pkl" \
  --outdir "output/fig_5"
```

Generate Fig. 6:

```bash
python Code/fig_6.py
```

Generate Fig. 7 and Fig. 8 scenario-specific results:

```bash
python "Code/fig_7and8/Internet & IP networks.py"
python "Code/fig_7and8/Data center networks.py"
python "Code/fig_7and8/Edge_fog computing networks.py"
python "Code/fig_7and8/Cellular mobile communication networks.py"
```

---

## Description of Scripts

| Script                                                     | Main Function                                                | Main Inputs                                                  | Main Outputs                                                 | Paper Results Supported                                      |
| ---------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `Code/api.py`                                              | Runs the per-paper LLM extraction pipeline: PDF parsing, content filtering, text chunking, scenario identification, metric extraction, correlation inference, result serialization, and per-paper heatmap generation. | Authorized paper PDFs; `DEEPSEEK_API_KEY`.                   | `*_metrics_relations.pkl`, `*_results.txt`, `*_heatmap.pdf`, `api_cache.pkl`. | Supports the LLM-enabled workflow, per-paper metric examples, and Fig. 5-style heatmaps. |
| `Code/integrated.py`                                       | Aggregates all per-paper `.pkl` files; counts metrics before and after deduplication; performs LLM-assisted semantic deduplication; computes corpus-wide and scenario-level statistics. | Directory of `*_metrics_relations.pkl` files.                | `dedup_rules.json`, `场景_指标统计中间数据.pkl`, `Integrated_Statistics.txt`, auxiliary rank-frequency plot. | Supports corpus statistics, Table IV, Table V, and downstream inputs for Figs. 3, 4, 6, 7, and 8. |
| `Code/integrated2.py`                                      | Exports a sorted metric inventory from the aggregate intermediate `.pkl` file. Optionally scans original per-paper `.pkl` files to enrich units and explanations. | `场景_指标统计中间数据.pkl`; optionally per-paper `.pkl` directory. | `Metrics list.txt` or another user-specified `.txt` report.  | Supports the full metric inventory and manual inspection of high-frequency metrics. |
| `Code/fig_2.py`                                            | Computes scenario distributions and generates the scenario-domain pie chart. | Directory of per-paper `*_metrics_relations.pkl` files.      | `fig_2.pdf`, scenario/statistical report.                    | Fig. 2: network scenario distribution.                       |
| `Code/fig_3.py`                                            | Generates the log-log power-law fit of the metric rank-frequency distribution using deduplicated metric counts. | Aggregate intermediate statistics `.pkl`.                    | `fig_3.pdf`.                                                 | Fig. 3: metric rank-frequency power-law fit.                 |
| `Code/fig_4.py`                                            | Generates the Sankey diagram linking network domains, top scenarios, and top metrics. Uses Plotly and Ghostscript font embedding. | Aggregate intermediate statistics `.pkl`.                    | `fig_4.pdf`.                                                 | Fig. 4: top-10 network scenarios and their top-5 metrics.    |
| `Code/fig_5.py`                                            | Redraws a correlation heatmap from a single per-paper `*_metrics_relations.pkl` file. Supports metric renaming, metric exclusion, optional unit display, and manual correlation overrides. | One `*_metrics_relations.pkl` file.                          | `<paper_name>_heatmap.pdf`.                                  | Fig. 5(a)–Fig. 5(d): representative per-paper metric correlation heatmaps. |
| `Code/fig_6.py`                                            | Performs corpus-wide high-frequency metric clustering using correlation-derived distances, K-means, and t-SNE visualization. | Directory of per-paper `*_metrics_relations.pkl` files.      | `fig_6(a).pdf`, `fig_6(b).pdf`.                              | Fig. 6: corpus-wide metric clustering and cluster-based correlation heatmap. |
| `Code/fig_7and8/Internet & IP networks.py`                 | Runs scenario-specific metric clustering and heatmap generation for Internet and IP networks. | Directory of per-paper `*_metrics_relations.pkl` files.      | `fig_7(a).pdf`, `fig_8(a).pdf`.                              | Fig. 7(a), Fig. 8(a).                                        |
| `Code/fig_7and8/Data center networks.py`                   | Runs scenario-specific metric clustering and heatmap generation for data center networks. | Directory of per-paper `*_metrics_relations.pkl` files.      | `fig_7(b).pdf`, `fig_8(b).pdf`.                              | Fig. 7(b), Fig. 8(b).                                        |
| `Code/fig_7and8/Edge_fog computing networks.py`            | Runs scenario-specific metric clustering and heatmap generation for edge/fog computing networks. | Directory of per-paper `*_metrics_relations.pkl` files.      | `fig_7(c).pdf`, `fig_8(c).pdf`.                              | Fig. 7(c), Fig. 8(c).                                        |
| `Code/fig_7and8/Cellular mobile communication networks.py` | Runs scenario-specific metric clustering and heatmap generation for cellular mobile communication networks. | Directory of per-paper `*_metrics_relations.pkl` files.      | `fig_7(d).pdf`, `fig_8(d).pdf`.                              | Fig. 7(d), Fig. 8(d).                                        |

---

## Inputs and Outputs

### Main Input Files

| File or Directory                 | Description                                                  |
| --------------------------------- | ------------------------------------------------------------ |
| `ToN paper title list.txt`        | Copyright-safe list of IEEE/ACM Transactions on Networking papers used to define the corpus. It includes bibliographic metadata and DOI information, but not paper full text. |
| `Metrics list.txt`                | Generated metric inventory sorted by frequency.              |
| `prompts/*.md`                    | Prompt templates for scenario identification, metric extraction, correlation inference, and metric deduplication. Both Chinese and English prompt versions are provided. |
| `examples/example_paper[38].json` | Copyright-safe representative pipeline example using paraphrased inputs and extracted outputs. |
| `output/fig_5/input/*.pkl`        | Per-paper extraction results used to reproduce the representative heatmaps in Fig. 5. |
| `output/Detail/*`                 | Aggregated statistics and intermediate files used for metric inventory inspection and downstream figure generation. |

### Main Generated Outputs

| Output                             | Description                                                  |
| ---------------------------------- | ------------------------------------------------------------ |
| `*_metrics_relations.pkl`          | Per-paper extraction result containing scenario label, metrics, explanations, and pairwise metric correlations. |
| `*_results.txt`                    | Human-readable per-paper extraction summary.                 |
| `*_heatmap.pdf`                    | Per-paper correlation heatmap.                               |
| `dedup_rules.json`                 | Alias-to-canonical metric mapping produced by the metric deduplication stage. |
| `场景_指标统计中间数据.pkl`        | Aggregate intermediate statistics used by multiple figure-generation scripts. |
| `Integrated_Statistics.txt`        | Detailed corpus-level and scenario-level statistics.         |
| `Statistics_of_all_metrics.txt`    | Detailed sorted metric report generated from the aggregate statistics. |
| `fig_2.pdf`                        | Scenario distribution pie chart.                             |
| `fig_3.pdf`                        | Log-log metric rank-frequency power-law plot.                |
| `fig_4.pdf`                        | Sankey diagram of network domains, scenarios, and metrics.   |
| `fig_5/*.pdf`                      | Representative per-paper metric correlation heatmaps.        |
| `fig_6(a).pdf`, `fig_6(b).pdf`     | Corpus-wide metric clustering and cluster-based heatmap.     |
| `fig_7(a-d).pdf`, `fig_8(a-d).pdf` | Scenario-specific metric clustering and cluster-based heatmaps. |

---

## Reproducing Paper Results

| Paper Element        | Script(s)                                                    | Notes                                                        |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fig. 1               | Not generated by code                                        | Conceptual workflow diagram of the LLM-enabled multi-agent pipeline. |
| Table II             | `Code/api.py`, `Code/integrated.py`                          | Hyperparameter settings are reflected in `LLM_CONFIG`: model, temperature, top-p, max tokens, and streaming mode. |
| Fig. 2               | `Code/fig_2.py`                                              | Generates the network scenario distribution pie chart. Requires the full set of per-paper `.pkl` files. |
| Table III            | `Code/api.py`, `examples/example_paper[38].json`, `output/fig_5/input/*.pkl` | Representative per-paper metric extraction examples. The manuscript table summarizes selected extracted metrics. |
| Fig. 3               | `Code/integrated.py`, `Code/fig_3.py`                        | `integrated.py` produces the aggregate metric counts; `fig_3.py` generates the final log-log power-law plot. |
| Table IV             | `Code/integrated.py`, `Code/integrated2.py`                  | Top high-frequency canonical metrics and definitions are supported by the aggregate statistics and exported metric inventory. |
| Table V              | `Code/integrated.py`                                         | Scenario-level metric counts before and after deduplication are stored in the aggregate statistics. |
| Fig. 4               | `Code/fig_4.py`                                              | Generates the Sankey diagram for the top-10 network scenarios and their top-5 metrics. |
| Fig. 5(a)            | `Code/fig_5.py`                                              | Uses `2022_paper94_metrics_relations.pkl`; representative paper: *Minimizing AoI With Throughput Requirements in Multi-Path Network Communication*. |
| Fig. 5(b)            | `Code/fig_5.py`                                              | Uses `2024_paper145_metrics_relations.pkl`; representative paper: *QoS-Guaranteed Resource Allocation in Mobile Communications: A Stochastic Network Calculus Approach*. |
| Fig. 5(c)            | `Code/fig_5.py`                                              | Uses `2022_paper104_metrics_relations.pkl`; representative paper: *Achieving Per-Flow Fairness and High Utilization With Limited Priority Queues in Data Center*. |
| Fig. 5(d)            | `Code/fig_5.py`                                              | Uses `2022_paper4_metrics_relations.pkl`; representative paper: *An Incentive Mechanism for Sustainable Blockchain Storage*. |
| Fig. 6               | `Code/fig_6.py`                                              | Corpus-wide clustering and cluster-based heatmap for high-frequency metrics. Uses a 5% corpus-level frequency threshold. |
| Fig. 7(a), Fig. 8(a) | `Code/fig_7and8/Internet & IP networks.py`                   | Scenario-specific clustering and heatmap for Internet and IP networks. |
| Fig. 7(b), Fig. 8(b) | `Code/fig_7and8/Data center networks.py`                     | Scenario-specific clustering and heatmap for data center networks. |
| Fig. 7(c), Fig. 8(c) | `Code/fig_7and8/Edge_fog computing networks.py`              | Scenario-specific clustering and heatmap for edge/fog computing networks. |
| Fig. 7(d), Fig. 8(d) | `Code/fig_7and8/Cellular mobile communication networks.py`   | Scenario-specific clustering and heatmap for cellular mobile communication networks. |
| Fig. 9               | Not generated by code                                        | Conceptual SEM framework figure.                             |

---

## Prompt Examples

Prompt templates are provided in the `prompts/` directory.

The four agent types are:

1. **Scenario Identification**
   - `01_Chinese_scenario_identification.md`
   - `01_English_scenario_identification.md`

2. **Metric Extraction**
   - `02_Chinese_metric_extraction.md`
   - `02_English_metric_extraction.md`

3. **Correlation Inference**
   - `03_Chinese_correlation_inference.md`
   - `03_English_correlation_inference.md`

4. **Metric Deduplication**
   - `04_Chinese_metric_deduplication.md`
   - `04_English_metric_deduplication.md`

The file:

```text
examples/example_paper[38].json
```

shows a copyright-safe representative pipeline input/output record. It includes:

- pipeline configuration;
- scenario identification result;
- metric extraction result;
- correlation inference result;
- metric deduplication mapping;
- final code-compatible record;
- quality checks.

The original paper text is not included in this example.

---

## Reproducibility Notes

- Full end-to-end reproduction requires legally obtained paper PDFs. The title list alone is not sufficient to rerun the LLM extraction pipeline.
- LLM outputs may vary across model versions, API providers, or inference settings. The scripts use low temperature, structured prompts, deterministic post-processing, and caching to reduce variation.
- For deterministic visualization, clustering scripts set `RANDOM_SEED = 42`.
- K-means clustering uses repeated initialization where applicable.
- Some scripts depend on intermediate per-paper `.pkl` files. If these files are not included in a lightweight release, regenerate them with `Code/api.py` using authorized PDFs.
- Figure-generation scripts are configured for IEEE/LaTeX-friendly PDF export and avoid Type 3 fonts where possible.
- `fig_4.py` requires `plotly`, `kaleido`, and preferably Ghostscript for post-processing and font embedding.
- If a generated figure differs slightly in layout, check local font availability, Matplotlib version, scikit-learn version, and whether the same intermediate `.pkl` files were used.

