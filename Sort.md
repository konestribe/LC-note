
# Merge sort
 - Time complexity is O(nlogn). 
 - Space complexity is O(1) constant.
 - Each time we divide candidate into two parts. We recursively divide and sort each part of them, then merge them based on relative order.
## 148. Sort List
 - [link](https://leetcode.com/problems/sort-list/)
 - Special linked list middle node search, using fast/slow pointer
 ```java
 public ListNode sortList(ListNode head) {
        if(head == null || head.next == null) return head;
        ListNode root = new ListNode(0, head);
        ListNode slow = root;
        ListNode fst = root;
        while(fst != null && fst.next != null && fst.next.next != null) {
            slow = slow.next;
            fst = fst.next.next;
        }
        ListNode hd2 = slow.next;
        slow.next = null;
        return merge(sortList(root.next), sortList(hd2));
    }
    
    private ListNode merge(ListNode hd1, ListNode hd2) {
        ListNode root = new ListNode();
        ListNode pt = root;
        while(hd1 != null && hd2 != null) {
            if(hd1.val < hd2.val) {
                pt.next = hd1;
                hd1 = hd1.next;
            } else{
                pt.next = hd2;
                hd2 = hd2.next;
            }
            pt = pt.next;
        }
        if(hd1 == null) {
            pt.next = hd2;
        } else{
            pt.next = hd1;
        }
        return root.next;
    }
 ```
