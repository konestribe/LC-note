# Trie
99% of the time trie will be used in string questions. The scenario we using it will be: instead of using substring to compare tokens, we use trie to combine multiple single token combinations into once, which save computation complexity. For trie, time complexity would be O(h), h is max length of the string inside trie.

## Using Trie to detect token

### LC 140. Word Break II
 - Practise: [link](https://leetcode.com/problems/word-break-ii/)
 - Solution:
   - requirement: using string from wordDict to construct string s, split token by white space. Example: Input: s = "catsanddog", wordDict = ["cat","cats","and","sand","dog"] Output: ["cats and dog","cat sand dog"]  
   - We construct a trie to store tokens, and use DFS to backtrack all possible tokens. If eventually when we running out of char, and head is root, indicating we matched all tokens already, we store it into return list.
   ```java
       class Node{
        String s;
        Node[] trie;
        public Node() {
            trie = new Node[26];
        }
    }
    Node root;
    
    private void constructTrie(String s) {
        Node pt = root;
        int idx = 0;
        while(idx < s.length()) {
            char cur = s.charAt(idx++);
            if(pt.trie[cur - 'a'] == null) pt.trie[cur - 'a'] = new Node();
            pt = pt.trie[cur - 'a'];
        }
        pt.s = s;
    }
    
    private boolean dfs(String s, int idx, Node node) {
        if(idx == s.length()) {
            return node == root;
        }
        char cur = s.charAt(idx);
        if(node.trie[cur - 'a'] == null) return false;
        boolean curHit = node.trie[cur - 'a'].s != null ? dfs(s, idx+1, root) : false;
        return curHit || dfs(s, idx+1, node.trie[cur - 'a']);
    }
    
    List<String> ret;
    public List<String> wordBreak(String s, List<String> wordDict) {
        root = new Node();
        for(String str: wordDict) {
            constructTrie(str);
        }
        ret = new ArrayList<>();
        dfs(s, 0, root, new StringBuilder());
        return ret;
    }
    
    private void dfs(String s, int index, Node node, StringBuilder sb) {
        if(index == s.length()) {
            if(node == root) {
                sb.setLength(sb.length()-1);
                ret.add(sb.toString());
            }
            return;
        }
        char cur = s.charAt(index);
        if(node.trie[cur - 'a'] == null) return;
        node = node.trie[cur - 'a'];
        
        if(node.s != null) {
            int len = sb.length();
            sb.append(node.s).append(' ');
            dfs(s, index+1, root, sb);
            sb.setLength(len);
        }
        dfs(s, index+1, node, sb);
    }
   ```
