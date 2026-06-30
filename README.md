# huffman-algorithm-tree-zip
this is a simple demonstration of a huffman algorithm and how files are compressed
1. A Basic Description of Huffman Coding
Huffman coding is a highly efficient, lossless data compression algorithm. Instead of using a fixed number of bits to store every character (like standard 8-bit ASCII), it assigns variable-length binary codes to characters based on how often they appear in the data.

The Principle: Characters that appear frequently (like the letter 'E' or spaces) are given very short bit codes (e.g., 01). Characters that appear rarely (like 'Z' or 'Q') are given much longer bit codes (e.g., 11010). By replacing frequent 8-bit characters with 2-bit or 3-bit codes, the overall file size shrinks dramatically.

The Tree: The algorithm achieves this by building a binary tree from the bottom up. It counts the frequencies of all characters, takes the two least frequent, and merges them into a parent node. It repeats this process until only one root node remains.

The Codes: Once the tree is built, you trace the path from the root to each leaf node (where the characters live). Every time you branch left, you add a 0 to the code; every time you branch right, you add a 1. Because no character's path is a prefix of another character's path, the computer knows exactly when one character's code ends and the next begins.

2. How ZIP Files are Decoded in Your Website
When a user uploads a .zip file into your decoder engine, your application actually navigates two distinct layers of data extraction:

Layer A: The Transport Layer (DEFLATE via JSZip)
Standard ZIP archives compress files using an algorithm called DEFLATE (which combines LZ77 dictionary matching with standard Huffman coding). Your application relies on the JSZip library to handle this heavy lifting.
When JSZip.loadAsync() processes the uploaded package, it parses the ZIP block headers, reverses the DEFLATE algorithm, and restores the files inside the archive back to their original byte structures.

Layer B: The Custom Routing Logic
Once JSZip has unpacked the internal files, your script's unpackArchiveHandler looks inside the archive to decide how to process the data:

Targeted Extraction: It scans the archive for specific structural files—namely, a binary data file (like bitstream.txt) and a dictionary file (like schema.json). If it finds both, it extracts them as plain strings and immediately injects them into your custom decoding pipeline.

Dynamic Arbitrary Fallback: If the user uploaded a ZIP containing a random file (e.g., an image or a generic text document), your script intercepts the raw byte array of that file using .async("uint8array"). It then dynamically calculates the byte frequencies on the fly, sorts them into a priority queue, and generates a fresh, optimized Huffman tree specifically for that arbitrary file before sending it to the decoder.

3. How the Website Constructs the Huffman Tree
During the decoding phase, your application rebuilds the Huffman tree entirely from scratch using only the JSON schema map (e.g., {"A": "1", "B": "00"}). It does not use character frequencies for this step; it uses the exact binary paths.

Here is the exact step-by-step logic your runDecoderPipeline function uses to build the tree in memory:

Creating the Root: It initializes a blank starting point called rootDirectoryMockNode. This node has no symbol and serves as the top of the tree.

Path Tracing: The script loops through every character inside the JSON schema. For each character, it looks at its binary path (for example, "01").

Branch Instantiation: Starting from the root, it iterates through the bits of that specific path:

If the bit is a "0", it checks if a leftChild exists. If not, it creates a new blank node there. It then moves the internal pointer down to that left child.

If the bit is a "1", it checks if a rightChild exists. If not, it creates a new blank node there, moving the pointer down to the right.

Assigning the Leaf: Once the script reaches the end of that specific binary path, it assigns the target character (the symbolKey) to that final node.

By repeating this for every item in the JSON map, the script physically reconstructs the exact branching topology of the original Huffman tree in the browser's memory, which is then used to safely decode the incoming bitstream and render the visual graph.
