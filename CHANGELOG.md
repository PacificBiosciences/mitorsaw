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
