
|Pattern|Anchor problems (LeetCode #)|What it unlocks|
|---|---|---|
|**Hash map lookup**|Two Sum (1), Longest Consecutive Sequence (128)|Anything asking "have I seen X before?"|
|**Prefix sum + hash map**|Subarray Sum Equals K (560), Product of Array Except Self (238)|Subarray sum/count problems, range queries|
|**Kadane / running state**|Maximum Subarray (53), Best Time to Buy and Sell Stock (121)|Best subarray or best single pass problems|
|**Two pointers (sorted)**|3Sum (15), Container With Most Water (11), Trapping Rain Water (42)|Pair/triplet problems, shrinking from both ends|
|**Sliding window**|Minimum Size Subarray Sum (209), Max Consecutive Ones III (1004)|Longest/shortest contiguous subarray under a condition|
|**Monotonic stack/deque**|Daily Temperatures (739), Sliding Window Maximum (239)|"Next greater/smaller" and window max/min|
|**Binary search**|Search in Rotated Sorted Array (33), Koko Eating Bananas (875)|Sorted-ish arrays and "binary search on the answer"|
|**Sort + sweep intervals**|Merge Intervals (56)|All interval overlap, scheduling, and meeting-room problems|
|**In-place tricks**|Sort Colors (75), Rotate Array (189), First Missing Positive (41)|Partitioning, reversal, using indices as a hash|
|**Heap / top-K**|Top K Frequent Elements (347)|Any "k largest/most frequent" problem|

Two more topics tend to show up alongside arrays. Learn House Robber (198) and Longest Increasing Subsequence (300) for 1D DP, and Subsets (78) and Combination Sum (39) for backtracking.

That's about 20 problems. Here is how I'd use them:

1. **Solve one anchor per pattern yourself.** Spend up to 30 minutes on it, then read the solution if you're stuck.
2. **Write down the trigger.** For example, "contiguous subarray + a condition on the sum" points to sliding window, or to prefix sum if there are negative numbers. Recognizing the trigger is the real skill.
3. **Test yourself on one unseen problem per pattern.** If you can name the pattern before coding, you've learned it. If not, do one more problem from that pattern.
4. **Revisit the ones you missed** after a few days instead of adding new problems.

If you want a ready-made list, the **Blind 75** and **NeetCode 150** are curated this way, and NeetCode groups them by pattern. If you're short on time, the first four rows of the table (hash map, prefix sum, Kadane, two pointers) plus sliding window and binary search cover the majority of array questions in interviews.

Are you preparing for a specific interview or timeline? I can trim this down further if you have only a couple of weeks.