# Installing Mitorsaw
## From conda
The easiest way to install Mitorsaw is through [conda](https://docs.conda.io/projects/conda/en/stable/user-guide/install/index.html):

```bash
# create a brand new conda environment and install latest Mitorsaw
conda create -n mitorsaw -c bioconda mitorsaw
# OR install latest into current conda environment
conda install mitorsaw
# OR install a specific version into current conda environment
conda install mitorsaw=0.1.1
```

## From GitHub
Conda updates usually lag the GitHub release by a couple days.
Use the following instructions to get the most recent version directly from GitHub:

1. Navigate to the [latest release](https://github.com/PacificBiosciences/mitorsaw/releases/latest) and download the tarball file (e.g. `mitorsaw-{version}-x86_64-unknown-linux-gnu.tar.gz`).
2. Decompress the tar file.
3. (Optional) Verify the md5 checksum.
4. Test the binary file by running it with the help option (`-h`).
5. Visit the [User guide](./user_guide.md) for details on running Mitorsaw.

### Example with v0.1.1
```bash
# modify this to update the version
VERSION="v0.1.1"
# get the release file
wget https://github.com/PacificBiosciences/mitorsaw/releases/download/${VERSION}/mitorsaw-${VERSION}-x86_64-unknown-linux-gnu.tar.gz
# decompress the file into folder ${VERSION}
tar -xzvf mitorsaw-${VERSION}-x86_64-unknown-linux-gnu.tar.gz
cd mitorsaw-${VERSION}-x86_64-unknown-linux-gnu
# optional, check the md5 sum
md5sum -c mitorsaw.md5
# execute help instructions
./mitorsaw -h
```
