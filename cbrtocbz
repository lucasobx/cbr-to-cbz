#!/bin/bash
set -euo pipefail

# Check dependencies
for cmd in unrar unzip zip; do
    command -v "$cmd" &>/dev/null || { echo "Missing dependency: $cmd"; exit 1; }
done

detect_type() {
    local file="$1"
    local type
    type=$(file --brief --mime-type "$file")

    if [[ "$type" == "application/x-rar"* ]]; then
        echo "rar"
        return
    fi

    if [[ "$type" == "application/zip"* ]]; then
        echo "zip"
        return
    fi

    # Fallback: read magic bytes
    local magic
    magic=$(xxd -p -l 4 "$file" 2>/dev/null || od -A n -N 4 -t x1 "$file" | tr -d ' ')

    # RAR: 52617221 = "Rar!"
    if [[ "$magic" == "52617221"* ]]; then
        echo "rar"
        return
    fi

    # ZIP: 504b0304 = "PK"
    if [[ "$magic" == "504b"* ]]; then
        echo "zip"
        return
    fi

    echo "unknown"
}

process() {
    local file="$1"
    local base="${file%.cbr}"
    local cbz="${base}.cbz"

    local type
    type=$(detect_type "$file")

    mkdir -p "$base"

    local extracted=false

    case "$type" in
        rar)
            unrar x -inul "$file" "$base/" && extracted=true
            ;;
        zip)
            unzip -qq "$file" -d "$base/" && extracted=true
            ;;
        *)
            echo "Error: unknown format for $file"
            rmdir "$base"
            return
            ;;
    esac

    if $extracted; then
        rm -f "$file"
        if (cd "$base" && zip -qr "../$cbz" .); then
            rm -rf "$base"
            echo "Done: $cbz"
        else
            echo "Error compressing $file. Folder kept."
        fi
    else
        echo "Extraction error for $file."
        rm -rf "$base"
    fi
}

export -f detect_type
export -f process

shopt -s nullglob
files=(*.cbr)

if [[ ${#files[@]} -eq 0 ]]; then
    echo "No .cbr files found."
    exit 0
fi

# Parallelize with up to N simultaneous processes
N=$(nproc 2>/dev/null || echo 4)
printf '%s\n' "${files[@]}" | xargs -P "$N" -I{} bash -c 'process "$@"' _ {}
