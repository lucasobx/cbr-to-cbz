# cbr-to-cbz

A simple shell script that converts `.cbr` files to `.cbz`, regardless of whether they are actually RAR or ZIP archives internally. Files are processed in parallel for better performance.

## Dependencies

`unrar` / `zip` / `unzip` / `7zip`

## Installation

```bash
chmod +x cbrtocbz
sudo mv cbrtocbz /usr/local/bin/
```

## Usage
Convert all .cbr files in the current directory and subdirectories
```bash
cbrtocbz
```
