# LFU (Least Frequently Used) cache replacement scheme

LFU (Least Frequently Used) is a cache replacement scheme, that prioritizes cache replacement based on frequency of item-usage. For item insertion into cache memory, it uses the following startegy: 

  - **if cache is not full**:
      - insert item into cache memory block
  - **if cache is full**:
      - The item that is least-times accessed, gets evicted
      - When more than one item from the cache, share the same minimum frequency of usage, then the one, that is least recently used, gets evicted.
