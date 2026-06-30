# Huffman Coding Visualizer & Archive Engine

## Overview
This project provides a professional-grade demonstration of **Huffman Coding**, a highly efficient, lossless data compression algorithm. The system includes both a visualizer for real-time tree construction and an archive engine capable of decoding custom Huffman-compressed ZIP files.

## 🧠 Core Principles
Huffman coding achieves compression by assigning variable-length binary codes to symbols based on their frequency of occurrence [cite: huffman_entropy_report.pdf].
* **Frequency Analysis:** Frequent characters (e.g., 'E', ' ') receive short bit codes (e.g., '01'), while rare characters receive longer codes [cite: huffman_entropy_report.pdf].
* **Binary Tree Topology:** The system builds a binary tree bottom-up by repeatedly merging the two least frequent nodes until a single root remains [cite: huffman_entropy_report.pdf].
* **Prefix-Free Encoding:** By traversing from the root to the leaves, the system generates paths where no complete code is the prefix of another, ensuring unambiguous decoding [cite: huffman_entropy_report.pdf].

## 📂 ZIP Archive Processing Engine
The application processes ZIP files through two distinct operational layers:

### Layer A: The Transport Layer (DEFLATE Extraction)
Utilizing the `JSZip` library, the application parses ZIP headers to reverse the DEFLATE algorithm. This restores the internal archive contents to their original byte structures before further processing [cite: huffman_entropy_report.pdf].

### Layer B: Custom Routing & Heuristics
Once unpacked, the `unpackArchiveHandler` determines the processing mode:
* **Targeted Extraction:** If the archive contains a `bitstream.txt` and a `schema.json`, the system injects these directly into the decoding pipeline.
* **Dynamic Fallback:** For generic files, the script captures the raw `Uint8Array`, calculates byte frequencies on-the-fly, and generates a fresh, optimized Huffman tree specifically for that file before passing it to the decoder.

## 🌳 Memory-Based Tree Reconstruction
To decode compressed data, the system reconstructs the branching topology in browser memory [cite: HuffmanVisualizer_v2.zip/js/script.js]. The `runDecoderPipeline` follows this logic:

1.  **Node Initialization:** Initializes a `rootDirectoryMockNode` as the tree's entry point.
2.  **Topological Mapping:** Iterates through the JSON schema map, parsing the binary path string (e.g., '01') for every character.
3.  **Branch Instantiation:** * For each bit ('0' or '1'), the system checks for the existence of a child node.
    * If a node is missing, the system instantiates a new blank node, effectively carving the binary path into memory [cite: HuffmanVisualizer_v2.zip/js/script.js].
4.  **Leaf Assignment:** Upon reaching the final bit of a path, the system assigns the corresponding symbol key to that node, completing the reconstruction.
