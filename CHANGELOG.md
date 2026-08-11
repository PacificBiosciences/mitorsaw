# v0.2.13
## Changes
- Added a new option `--downsample` that enables downsampling of reads from the input BAM files

## Fixed
- Fixed an issue where invalid read mappings were not filtered during putative variant identification
- Added a warning when a BAM/CRAM index (.bai/.crai) is missing or older than the alignment file

# v0.2.12
## Changes
- Reads that fail remapping to the consensus or reference are now skipped with a warning instead of generating an error

## Fixed
- Fixed debug IGV BAM writing for reads with missing QUAL values

# v0.2.11
## Fixed
- Improved the error message for when the reference FASTA index (.fai) is missing
- Mitorsaw will now skip any reads missing a sequence with a warning instead of erroring

# v0.2.10
## Fixed
- Fixed an issue where deletion events spanning the 0 position of the reference chrM would cause a panic. These are now reported as two separate events in the output VCF file: one at the end of chrM and one at the start.

# v0.2.9
## Changes
- Added `--disable-hp-filter` to the haplotype subcommand to allow heteroplasmic homopolymer expansion/contraction variants

# v0.2.8
## Changes
- Relaxed `--minimum-maf` lower bound from 0.01 to 0.0, allowing the MAF filter to be more sensitive or effectively disabled

# v0.2.7
## Fixed
- Fixed an issue where large deletions near the end of the reference chrM may be excluded from reporting

# v0.2.6
## Fixed
- Fixed an issue where overlapping variants were not properly marked as incompatible, generating nonsense consensus sequences
- Fixed an issue where a large number of heteroplasmic variants could create excessive run-times during haplotype identification

# v0.2.5
## Fixed
- Fixed an issue where overlapping deletions would lead to elevated partial fingerprinting in the debug outputs

# v0.2.4
## Changes
- Added an option to manually set the sample name in the output VCF: `--sample-name`

## Fixed
- Fixed the VCF sample name to report the BAM read group SM value

# v0.2.3
## Fixed
- Fixed an issue caused by high allele frequency, large deletion variants

# v0.2.2
## Fixed
- Fixed an issue where the tool would panic if the max iterations on consensus was reached instead of returning the last result
- Fixed an issue where the consensus could "ping-pong" between two answers and not converge
- Fixed an issue caused by reads that are unexpectedly large (>75 kbp) by filtering them

# v0.2.1
## Fixed
- Fixed a panic caused by putative deletion at the first reference chrM base

# v0.2.0
## Changes
- Added better support for large SV deletions, which would often panic or error in previous versions. Additionally, large SV deletions are no longer incorporated into the primary consensus. These will always show up as secondary consensus sequences.
- Added a new `coverage_stats.json` file to the debug outputs

## Fixed
- Changed output VCF version to v4.3 to enable loading into IGV

# v0.1.1
## Changes
- Initial alpha release
