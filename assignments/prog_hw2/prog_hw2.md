# Programming HW2: RocksDB SST File Parser

Due: **4/25, 4:59:59PM**

## Objective

Your task is to complete the provided Python skeleton code to implement a
functional RocksDB SST (Sorted String Table) file parser for `format_version=5`.
Given a key, the parser should be able to locate the corresponding value within
the SST file.

## Background

RocksDB SST files contain key-value pairs stored in sorted order. The file
format consists of three main components:

- **Footer:** Contains metadata, including the magic number, version, and block
  handles.
- **Index Blocks:** Maps keys to data block handles.
- **Data Blocks:** Contains the actual key-value pairs.

The parser will read the SST file, locate the index block using the footer, find
the correct data block using the index block, and extract the value associated
with the given key.

**See the recitation (on Courseworks) for more details on the RocksDB SST file
format.**

## File structure

We provide three files:

- `helpers.py`: Contains various utility functions and variables.
- `impl.py`: Where you will implement the core parser logic.
- `parser.py`: A driver for the functions that you will implement in `impl.py`.

**You should only modify `impl.py`. Do not modify the other files.**

## Part 1: Parsing the Footer

### Implement `parse_footer(data)`

- Goal: Parse the SST file footer to verify:
  - Magic number matches `MAGIC_NUMBER`.
  - Version matches `FORMAT_VERSION`.
- Steps:
  1. Verify the last 8 bytes contain the magic number.
  2. Check the version number.
  3. Call `parse_footer_handles()` to get block handles.
- Error Handling: Raise errors for invalid format:
  - If the magic number is incorrect, raise `RocksDBFormatError` with
    `"Magic number does not match"`.
  - If the version is incorrect, raise `RocksDBFormatError` with
    `"Format version does not match"`.

### Implement `parse_footer_handles(data)`

- Goal: Extract the **metaindex** and **index block handles** from the
  footer. Return the metaindex block handle first and then the index block.
- Hints:
  - Use `decode_varint64()` to decode offset and size.

## Part 2: Parsing the Index Block

### Implement `parse_index(data, key)`

- Goal: Perform a **linear scan** within the index block to find the
  corresponding data block handle for the given key.
- Steps:
  - Find the end of the index block data (i.e., without the restart array and
    block trailer).
  - Iterate over the index block data and check each entry.
  - Compare reconstructed keys to the target key.
- Notes:
  - Make sure to handle delta-compressed index entries.
  - Return `None` if no corresponding entry for the key is found.

## Part 3: Parsing the Data Block

### Implement `parse_data(data, key)`

- Goal: Perform a **linear scan** in the data block to retrieve the value
  associated with the given key.
- Steps:
  - Find the end of the data block data (i.e., without the restart array and
    block trailer).
  - Iterate over the data block and check each entry.
  - Compare keys and extract the corresponding value when found.

## Testing

We will provide a set of SST files to test with, along with more details on
their contents. This will be posted on Ed.

## Submission

This is a solo project. Every student should submit their own work.

Submit your parser implementation on Gradescope as a single Python file named
`impl.py`, based on the skeleton code provided on Courseworks. In order to be
properly graded, **the file must be named `impl.py` and functions must be
named as specified in the prompt.**

We will use our own copies of the files `helpers.py` and `parser.py` to test
your implementation. Do not modify these files.

The autograder automatically checks for cheating. Students that are caught
cheating will receive a 0.

Good luck!
