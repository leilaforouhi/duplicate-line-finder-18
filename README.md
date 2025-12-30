#!/usr/bin/env python3
"""
Duplicate Line Finder
- Scans text files
- Finds duplicate lines across files
- Saves results into duplicates.txt
"""

import os
from collections import defaultdict

TEXT_EXTS = (".txt", ".md", ".py")

def main():
    seen = defaultdict(list)

    for root, _, files in os.walk("."):
        if ".git" in root:
            continue
        for name in files:
            if name.endswith(TEXT_EXTS):
                path = os.path.join(root, name)
                try:
                    with open(path, "r", encoding="utf-8", errors="ignore") as f:
                        for i, line in enumerate(f, 1):
                            clean = line.strip()
                            if clean:
                                seen[clean].append(f"{path}:{i}")
                except Exception:
                    pass

    duplicates = {k: v for k, v in seen.items() if len(v) > 1}

    with open("duplicates.txt", "w", encoding="utf-8") as f:
        for line, locations in duplicates.items():
            f.write(line + "\n")
            for loc in locations:
                f.write(f"  - {loc}\n")
            f.write("\n")

    print(f"Found {len(duplicates)} duplicated lines.")
    print("Results saved to duplicates.txt")

if __name__ == "__main__":
    main()
