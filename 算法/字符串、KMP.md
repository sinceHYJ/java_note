```java
/**
 * @ClassName KMPSearch
 * @Author heyingjie
 * @Date 2019/1/17 8:56
 * @Description 字符串搜索的KMP算法
 */
public class KMPSearch {

    int[] next(String s) {
        int slen = s.length();
        int[] next = new int[slen];

        // 初始化
        int k = -1, j = 1;
        // 求前面的不包含当前字符的最大匹配长度
        next[0] = -1;
        // 计算
        // 从1开始因为，0被赋初值
        while (j < slen) {
            if (k == -1 ||
                    s.charAt(j - 1) == s.charAt(k)) {
                next[j++] = ++k;
            } else {
                k = next[k];
            }
        }

        return next;
    }

    int KMPSearch(String p, String s) {
        int result = -1;

        int plen = p.length();
        int slen = s.length();

        // 初始化
        int pi = 0, si = 0;
        int[] next = next(s);
        while (pi < plen && si < slen) {
            if (si == -1 ||
                    p.charAt(pi) == s.charAt(si)) {
                pi++;
                si++;
            } else {
                si = next[si];
            }
        }

        if (si == slen) {
            result = pi - si;
        }
        return result;
    }

    /**
     * 暴力破解
     *
     * @param p
     * @param s
     * @return
     */
    int BFSearch(String p, String s) {
        int result = -1;

        int plen = p.length();
        int slen = s.length();

        // 初始化
        int pi = 0, si = 0;
        int[] next = next(s);
        while (pi < plen && si < slen) {
            if (si == -1 ||
                    p.charAt(pi) == s.charAt(si)) {
                pi++;
                si++;
            } else {
                pi = pi - si + 1;
                si = 0;
            }
        }

        if (si == slen) {
            result = pi - si;
        }
        return result;
    }


    public static void main(String[] args) {
        KMPSearch kmp = new KMPSearch();
        /*
        int[] nextj = kmp.next("abab");
        for (int i = 0; i < nextj.length; i++) {
            System.out.println(nextj[i]);
        }
        */
        String p = "BBC ABCDAB ABCDABDDABDE";
        String s = "ABCDABD";
        // int r = kmp.KMPSearch(p, s);
        int r = kmp.BFSearch(p, s);
        System.out.println(r);
    }

}

```