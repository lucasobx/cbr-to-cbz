# cbr-to-cbz

A simple shell script that converts `.cbr` files to `.cbz`, regardless of whether they are actually RAR or ZIP archives internally. Files are processed in parallel for better performance.

## Dependencies

- `unrar`
- `zip` / `unzip`

## Installation

```bash
chmod +x cbrtocbz
sudo mv cbrtocbz /usr/local/bin/
```

## Usage

```bash
# Convert files in the current directory
cbrtocbz

# Convert files in a specific directory
cbrtocbz /path/to/files
```
