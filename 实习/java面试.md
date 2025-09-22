```java
import java.util.Scanner;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        // 读取矩阵大小
        int n = sc.nextInt();
        int m = sc.nextInt();
        
        // 读取矩阵
        char[][] matrix = new char[n][m];
        for (int i = 0; i < n; i++) {
            String line = sc.next();
            matrix[i] = line.toCharArray();
        }
        
        int operations = 0;
        
        // 遍历矩阵的前半部分（包括中心行/列）
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                // 计算对称位置
                int oppositeI = n - 1 - i;
                int oppositeJ = m - 1 - j;
                
                // 如果已经处理过这对位置，则跳过
                if (i > oppositeI || (i == oppositeI && j >= oppositeJ)) {
                    continue;
                }
                
                // 如果对称位置的字符不同
                if (matrix[i][j] != matrix[oppositeI][oppositeJ]) {
                    // 如果其中一个是'o'，需要将其变成'p'
                    if (matrix[i][j] == 'o') {
                        operations++;
                    }
                    if (matrix[oppositeI][oppositeJ] == 'o') {
                        operations++;
                    }
                }
            }
        }
        
        System.out.println(operations);
        sc.close();
```

怎么办

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n = scanner.nextInt();
        int[] a = new int[n];
        for (int i = 0; i < n; i++) {
            a[i] = scanner.nextInt();
        }
        scanner.close();

        // 预处理left数组，left[i] 表示以 i 为结尾的最长非降序子数组的起始位置。如果当前位置 i 的前一个元素小于等于当前位置的元素（即满足非降序），那么 left[i] 等于 left[i-1]，否则 left[i] 就是当前元素的位置。
        int[] left = new int[n];
        left[0] = 0;
        for (int i = 1; i < n; i++) {
            if (a[i - 1] <= a[i]) {
                left[i] = left[i - 1];
            } else {
                left[i] = i;
            }
        }

        // 预处理right数组，right[i] 表示以 i 为起始的最长非降序子数组的结束位置。如果当前位置 i 的元素小于等于下一个元素（即满足非降序），那么 right[i] 等于 right[i+1]，否则 right[i] 就是当前位置。
        int[] right = new int[n];
        right[n - 1] = n - 1;
        for (int i = n - 2; i >= 0; i--) {
            if (a[i] <= a[i + 1]) {
                right[i] = right[i + 1];
            } else {
                right[i] = i;
            }
        }

        // 收集所有下降点，这里遍历数组 a，找出所有下降点（即 a[i] > a[i+1] 的位置），并将其保存到 desPoints 列表中。
        List<Integer> desPoints = new ArrayList<>();
        for (int i = 0; i < n - 1; i++) {
            if (a[i] > a[i + 1]) {
                desPoints.add(i);
            }
        } 

        // 计算第一部分：所有非降序子数组的数量，这一部分的目标是计算所有非降序子数组的数量。current 变量记录当前非降序子数组的长度，part1 变量记录总数。遍历数组时，如果前一个元素小于等于当前元素，current 增加 1；如果不满足，current 重置为 1。
        long part1 = 0;  // 改long
        int current = 1;   
        part1 += current;
        for (int i = 1; i < n; i++) {
            if (a[i - 1] <= a[i]) {  // 这里 等于
                current += 1;
            } else {
                current = 1;
            }
            part1 += current;
        }

        // 计算第二部分：每个下降点贡献的符合条件的子数组数目
        long part2 = 0;
        for (int i : desPoints) {
            int leftStart = left[i];
            int leftEnd = i;
            int rightStart = i + 1;
            int rightEnd = right[rightStart];

            if (rightStart > rightEnd) {
                continue; // 没有右边段，不可能有符合要求的子数组
            }

            int j = rightStart;
            long cnt = 0;
            for (int s = leftStart; s <= leftEnd; s++) {
                while (j <= rightEnd && a[j] <= a[s]) {
                    j++;
                }
                cnt += (j - rightStart);
            }
            part2 += cnt;
        }

        System.out.println(part1 + part2);
    }
}
```



```java
import java.util.*;
import java.io.*;

public class Main {
    static int n;
    static long[] w; // 点权
    static List<Integer>[] adj; // 邻接表
    static long[][] dp; // dp[i][0/1]表示节点i保留/删除的最大点权和
    
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int t = Integer.parseInt(br.readLine().trim());
        StringBuilder sb = new StringBuilder();
        
        while (t-- > 0) {
            n = Integer.parseInt(br.readLine().trim());
            w = new long[n+1];
            adj = new ArrayList[n+1];
            dp = new long[n+1][2];
            
            String[] ws = br.readLine().trim().split(" ");
            for (int i = 1; i <= n; i++) {
                w[i] = Long.parseLong(ws[i-1]);
                adj[i] = new ArrayList<>();
            }
            
            for (int i = 1; i < n; i++) {
                String[] edge = br.readLine().trim().split(" ");
                int u = Integer.parseInt(edge[0]);
                int v = Integer.parseInt(edge[1]);
                adj[u].add(v);
                adj[v].add(u);
            }
            
            dfs(1, 0);
            sb.append(dp[1][0]).append("\n");
        }
        
        System.out.print(sb);
    }
    
    static void dfs(int u, int fa) {
        dp[u][0] = w[u]; // 保留当前节点
        
        // 只有非根节点且度数为1或2时才可以删除
        if (u != 1 && (adj[u].size() == 1 || adj[u].size() == 2)) {
            dp[u][1] = 0; // 可以删除
        } else {
            dp[u][1] = Long.MIN_VALUE/2; // 不可删除
        }
        
        int childCount = 0;
        
        for (int v : adj[u]) {
            if (v == fa) continue; // 跳过父节点
            childCount++;
            
            dfs(v, u);
            dp[u][0] += Math.max(dp[v][0], dp[v][1]); // 保留u，子节点可保留可删除
            
            // 度数为2的节点被删除时，其唯一子节点必须保留
            if (u != 1 && adj[u].size() == 2 && childCount == 1) {
                dp[u][1] = dp[v][0];
            }
        }
        
        // 度数为1的节点删除后贡献为0（叶子节点）
        if (u != 1 && adj[u].size() == 1) {
            dp[u][1] = 0;
        }
    }
}
```



这两道题挺难，不过感觉要是做出来一道 会加分













