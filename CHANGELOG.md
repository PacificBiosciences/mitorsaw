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
