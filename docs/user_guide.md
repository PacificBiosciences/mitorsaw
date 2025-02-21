# User Guide
Table of contents:

* [Quickstart](#quickstart)
* [Supported upstream processes](#supported-upstream-processes)
* [Output files](#output-files)
* [FAQ](#faq)

# Quickstart
```bash
mitorsaw haplotype \
    --reference {REFERENCE} \
    --bam {IN_BAM} \
    --output-vcf {OUT_VCF} \
    --output-hap-stats {OUT_STATS}
```

Parameters:
* `--reference {REFERENCE}` - a FASTA file containing the reference genome, which must be GRCh38 and contain chrM
* `--bam {IN_BAM}` - path to a BAM file containing reads from a single sample, this option can be specified multiple times; each BAM file must be indexed prior to running Mitorsaw
* `--output-vcf {OUT_VCF}` - path to the output VCF that will contain the identified mitochondrial variants
* `--output-hap-stats {OUT_STATS}` - path to an output stats file (.json) that contains useful metrics regarding the haplotypes that were identified

## Quickstart Example
This example also includes some auxiliary debug outputs that are useful for visualization and debugging.
For details on each, refer to the [Output files](#output-files) section.

```
mitorsaw haplotype \
    --reference ./human_GRCh38_no_alt_analysis_set.fasta \
    --bam ./HG001.GRCh38.haplotagged.bam \
    --output-vcf ./test_mitorsaw_HG001.vcf.gz \
    --output-hap-stats ./test_mitorsaw_HG001.hap_stats.json \
    --output-debug ./test_mito_debug

[2025-02-07T14:41:34.420Z INFO  mitorsaw::cli::haplotype] Inputs:
[2025-02-07T14:41:34.421Z INFO  mitorsaw::cli::haplotype] 	Database: None
[2025-02-07T14:41:34.421Z INFO  mitorsaw::cli::haplotype] 	Reference: "./human_GRCh38_no_alt_analysis_set.fasta"
[2025-02-07T14:41:34.421Z INFO  mitorsaw::cli::haplotype] 	BAM: "./HG001.GRCh38.haplotagged.bam"
[2025-02-07T14:41:34.421Z INFO  mitorsaw::cli::haplotype] Outputs:
[2025-02-07T14:41:34.421Z INFO  mitorsaw::cli::haplotype] 	Variant calls: "./test_mitorsaw_HG001.vcf.gz"
[2025-02-07T14:41:34.421Z INFO  mitorsaw::cli::haplotype] 	Haplotype stats: "./test_mitorsaw_HG001.hap_stats.json"
[2025-02-07T14:41:34.421Z INFO  mitorsaw::cli::haplotype] 	Debug folder: "./test_mito_debug"
[2025-02-07T14:41:34.421Z INFO  mitorsaw::cli::haplotype] Heuristics:
[2025-02-07T14:41:34.421Z INFO  mitorsaw::cli::haplotype] 	Minimum read count: 3
[2025-02-07T14:41:34.421Z INFO  mitorsaw::cli::haplotype] 	Minimum MAF: 0.1
[2025-02-07T14:41:34.421Z INFO  mitorsaw::cli::haplotype] 	Minimum mapping fraction: 0.9
[2025-02-07T14:41:34.421Z INFO  mitorsaw] Creating debug folder at "./test_mito_debug"...
[2025-02-07T14:41:34.427Z INFO  mitorsaw] Parsing chrM records from 1 BAM files...
[2025-02-07T14:41:34.427Z INFO  mitorsaw::read_parsing] 	Loading ./HG001.GRCh38.haplotagged.bam...
[2025-02-07T14:41:36.186Z INFO  mitorsaw] Loaded 5025 total records.
[2025-02-07T14:41:36.186Z INFO  mitorsaw] Removing putative NUMT records...
[2025-02-07T14:43:13.451Z INFO  mitorsaw] Filtered down to 4861 records.
[2025-02-07T14:43:13.451Z INFO  mitorsaw] Generating mitochondrial consensus and heteroplasmic variants...
[2025-02-07T14:44:57.148Z INFO  mitorsaw] Generating most likely haplotype tree...
[2025-02-07T14:44:57.149Z INFO  mitorsaw] Found 2 unique haplotypes.
[2025-02-07T14:44:57.149Z INFO  mitorsaw] Estimating haplotype abundances...
[2025-02-07T14:44:57.149Z INFO  mitorsaw] Generating haplotype sequences...
[2025-02-07T14:44:57.149Z INFO  mitorsaw] Defining variants for each haplotype...
[2025-02-07T14:44:57.157Z INFO  mitorsaw] Gathering variant summary statistics...
[2025-02-07T14:45:50.027Z INFO  mitorsaw] Saving variants to "./test_mitorsaw_HG001.vcf.gz"...
[2025-02-07T14:45:50.032Z INFO  mitorsaw] VCF file saved.
[2025-02-07T14:45:50.032Z INFO  mitorsaw] Saving haplotype stats to "./test_mitorsaw_HG001.hap_stats.json"...
[2025-02-07T14:45:50.055Z INFO  mitorsaw] Generating debug outputs in "./test_mito_debug"...
[2025-02-07T14:47:31.643Z INFO  mitorsaw::visualization::debug_bam_writer] Writing all records to "./test_mito_debug/mito_igv_custom/custom_alignments.bam"...
[2025-02-07T14:47:46.424Z INFO  mitorsaw::visualization::debug_bam_writer] Building index...
[2025-02-07T14:47:47.942Z INFO  mitorsaw] Process finished successfully.
```

## Recommended resources
Mitorsaw is currently single-threaded.
The most memory intensive part of the process is remapping to the reference genome to search for NUMTs.
In our experience, this step has used up to 20 GB of memory in some datasets.

# Supported upstream processes
The following upstream processes are supported as inputs to Mitorsaw:

* Aligners (BAM files):
  * [pbmm2](https://github.com/PacificBiosciences/pbmm2) (recommended)
  * [minimap2](https://github.com/lh3/minimap2)

Other upstream processes may work with Mitorsaw, but there is no official support for them at this time.

# Output files
## Phased mitochondrial VCF
The primary output from Mitorsaw is a phased mitochondrial VCF following [VCF v4.5 specifications](https://samtools.github.io/hts-specs/VCFv4.5.pdf).
This file includes both homoplasmic and heteroplasmic variation, which can be identified by the genotype (GT) field.
The VCF file currently includes the following fields for each variant:

* Genotype (GT) - The GT field will have one entry per haplotype, corresponding to the haplotype indices in other outputs. The first field is always the consensus haplotype (`hap_0`), which is _typically_ the most abundant haplotype in the dataset. For example, if `GT=0|1|0`, then the consensus (`hap_0`) does not have the variant, the next haplotype (`hap_1`) does have the variant, and the last haplotype (`hap_2`) does not have the variant.
* Total depth (DP) - The DP field is a single number corresponding to the total number of reads that spanned the locus at least once. Note that some longer reads may span the locus multiple times due to the cyclic nature of the mitochondria. These reads are only counted once.
* Allele depth (AD) - The AD field has one entry per allele, corresponding to the number of reads that can be definitively assigned to REF or ALT. This sum of this field is strictly less than or equal to DP. If less than DP, the difference is reads which overlapped the locus but could not be assigned to REF or ALT. This is most common in homopolymer indels.
* Variant allele frequency (VAF) - A single number representing the fraction of reads with the alternate allele. Mathematically, `AD.ALT / (AD.REF + AD.ALT)`.

## Haplotype stats file (`--output-hap-stats`)
The output haplotype stats include metrics generated during the identification of heteroplasmy and non-consensus haplotypes.
The following fields are included in the output JSON file:

* `haplotypes` - A list of haplotyeps and corresponding metrics. `hap_0` is the consensus haplotype, which is usually the most abundant in the sample.
  * `label` - The haplotype identifier, usually `hap_{index}`.
  * `seq_len` - The length of the haplotype when all variants are inserted to the reference mitochondria.
  * `fingerprint_alleles` - An array containing a binary value (0 or 1) indicating the "fingerprint" for the haplotype. Each fingerprint corresponds to heteroplasmic variants in the sample relative to the consensus haplotype, and is guaranteed to be unique for each haplotype. As such, `hap_0` should be all 0s, and every other haplotype should have at least one 1 in the fingerprint.
  * `num_ref_variants` - The total number of variants in this haplotype relative to the reference mitochondria.
  * `estimated_abundance` - The estimated fraction of reads that match the haplotype, which is typically used to estimate the mitochondria haplotype abundances in the sample.
* `fingerprint_stats` - Statistics corresponding to fingerprints that were identified in the reads.
  * `fingerprint_counts` - A dictionary containing a set of observed fingerprints and the number of times each fingerprint was identified in the reads. Fingerprint values are relative to the consensus haplotype with REF=0, ALT=1, AMBIGUOUS=2, NO_OVERLAP=3. In the below example, we see that 2346 reads match the consensus fingerprint, 2249 have the heteroplasmic variant, 6 overlap the variant but are ambiguous, and 260 reads do not overlap the heteroplasmic variants.
  * `unexplained_fingerprints` - Contains the set of fingerprints that were observed at least once, but that do not match any of the reported haplotypes. These typically correspond to sequencing errors, but those with large abundances may correspond to errors in Mitorsaw haplotype identification.
  * `max_unexplained_cost` - A metric describing the "cost" if no haplotypes were reported by Mitorsaw.
  * `unexplained_cost` - A metric describing the "cost" from the haplotypes that were reported by Mitorsaw. The tool attempts to minimize this cost during haplotype selection by permuting the consensus haplotype with the most common heteroplasmic variation.
  * `unexplained_fraction` - Summary metric which equals `unexplained_cost / max_unexplained_cost`.
  * `passing_explanation` - Binary summary metric indicating if the observed read fingerprints are "explained" by the reported haplotypes. Currently, this metric is defined as `unexplained_fraction <= 0.02`, indicating that the vast majority of reads match a haplotype fingerprint.

Example stats file:
```
{
  "haplotypes": [
    {
      "label": "hap_0",
      "seq_len": 16570,
      "fingerprint_alleles": [
        0
      ],
      "num_ref_variants": 17,
      "estimated_abundance": 0.510553221518985
    },
    {
      "label": "hap_1",
      "seq_len": 16570,
      "fingerprint_alleles": [
        1
      ],
      "num_ref_variants": 18,
      "estimated_abundance": 0.4894467784810151
    }
  ],
  "fingerprint_stats": {
    "fingerprint_counts": {
      "[0]": 2346,
      "[1]": 2249,
      "[2]": 6,
      "[3]": 260
    },
    "unexplained_fingerprints": [],
    "max_unexplained_cost": 4595,
    "unexplained_cost": 0,
    "unexplained_fraction": 0.0,
    "passing_explanation": true
  }
}
```

## Debug files (`--output-debug`)
This option generates a collection of debug files, which are primarily for developers and users interested in deeper discovery on the mitochondria.
Currently, it includes the following outputs:

* `mito_igv_custom` - This includes an IGV session file and corresponding reference and alignment files to visualize some of the underlying algorithms in Mitorsaw. If loaded into IGV, there will be three reference sequences with mappings:
  * "chrM" is the reference sequence with all haplotype sequences aligned to it.
  * "chrM_consensus" is a looped version of the generated consensus for the dataset. Reads are aligned to this looping structure to more cleanly see reads that span the mitochondria but start mid-way through. Each read is tagged with a haplotype label and fingerprint (e.g., `hap_1_0-1`) for visualization.
  * "chrM_reference" is a looped version of the reference mitochondria, but otherwise shows the same information as the mappings in "chrM_consensus".
* `sequence_chrM.fa` - FASTA file containing the generated haplotype sequences for all identified haplotypes.

# FAQ
Placeholder for common questions.
