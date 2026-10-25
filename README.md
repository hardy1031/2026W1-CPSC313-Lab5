# Goal:

Make the y86 `heapsort` much faster while keeping it a correct heapsort.

- Full credit: **−66% cycles** on *Pipelined with stalling* and **−55% cycles** on *Pipelined with forwarding + branch prediction*, compared with the original code.
- Rules: the entry label is `heapsort` (`%rdi` = number of elements, array at `heap`), callee-saved registers are preserved, the function ends with `ret` (no `halt`), and nothing is hardcoded to the sample data.

# Approach:

1. Inline the helper functions (`get_element`, `put_element`, `swap`, `check_child`, `extract_max`) to remove `call`/`ret` overhead.
2. Use byte offsets instead of indices, keep loaded values and constants in registers, and use only caller-saved registers.
3. Use bottom-tested loops and `cmovXX` to avoid branch mispredictions.
4. Reorder instructions to avoid load-use and other data-hazard stalls.

# Quick Test:

1. Load `heapsort.ys` in the course y86 simulator, and set both Clock speed and cycles per update to at least 100.
2. Run on both pipelines and record the cycle count.
3. Check that `heap` is sorted in ascending order.
4. Repeat with other data in `heap`/`size`: 0–3 elements, duplicates, sorted, reversed, and negative values.
5. Target: stalling ≤ 0.34× the original, forwarding ≤ 0.45× the original.
6. Submit to the autograder only once, since it has a 15-minute cooldown.

