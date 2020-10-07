# LFU (Least Frequently Used) cache replacement scheme

LFU (Least Frequently Used) is a cache replacement scheme, that prioritizes cache replacement based on frequency of item-usage. For item insertion into cache memory, it uses the following startegy: 

  - **if cache is not full**:
      - insert item into cache memory block
  - **if cache is full**:
      - The item that is least-times accessed, gets evicted
      - When more than one item from the cache, share the same minimum frequency of usage, then the one, that is least recently used, gets evicted.
      
 ![LFU cache replacement scheme](https://github.com/Jigyansu-Nanda/LFU-cache-replacement-scheme/blob/master/lfu1.png)

# Implementation
```java
class LFUCache {

    class Node {
        int key, val;
        int cnt;
        Node prev, next;
        public Node(int k, int v) {
            key = k;
            val = v;
            cnt = 1;
        }
    }

    class DoublyLinkedList {
        Node head, tail;
        int len;
        public DoublyLinkedList() {
            head = new Node(0, 0);
            tail = new Node(0, 0);
            head.next = tail;
            tail.prev = head;
            len = 0;
        }

        public void addHead(Node node) {
            Node next = head.next;
            head.next = node;
            node.prev = head;
            node.next = next;
            next.prev = node;
            map.put(node.key, node);
            len++;
        }

        public void remove(Node node) {
            Node prev = node.prev;
            Node next = node.next;
            prev.next = next;
            next.prev = prev;
            len--;
            map.remove(node.key);
        }

        public void removeTail() {
            Node prevTail = tail.prev;
            remove(prevTail);
        }
    }

    Map<Integer, Node> map;
    Map<Integer, DoublyLinkedList> freq;
    int size, capacity;
    int maxFreq;
    public LFUCache(int capacity) {
        map = new HashMap<>();
        freq = new HashMap<>();
        this.capacity = capacity;
        size = 0;
        maxFreq = 0;
    }

    public int get(int key) {
        if (map.get(key) == null) return -1;

        Node node = map.get(key);
        int prevFreq = node.cnt;
        DoublyLinkedList previousList = freq.get(prevFreq);
        previousList.remove(node);

        int curFreq = prevFreq + 1;
        maxFreq = Math.max(maxFreq, curFreq);
        DoublyLinkedList currentList = freq.getOrDefault(curFreq, new DoublyLinkedList());
        node.cnt++;
        currentList.addHead(node);

        freq.put(prevFreq, previousList);
        freq.put(curFreq, currentList);
        return node.val;
    }

    public void put(int key, int value) {
        if (capacity == 0) return;
        if (map.get(key) != null) {
            map.get(key).val = value;
            get(key);
            return;
        }

        Node node = new Node(key, value);
        DoublyLinkedList currentList = freq.getOrDefault(1, new DoublyLinkedList());
        currentList.addHead(node);
        size++;

        if (size > capacity) {
            if (currentList.len > 1) {
                currentList.removeTail();
            } else {
                for (int i = 2; i <= maxFreq; i++) {
                    if (freq.get(i) != null && freq.get(i).len > 0) {
                        freq.get(i).removeTail();
                        break;
                    }
                }
            }

            size--;
        }

        freq.put(1, currentList);
    }
}
```
