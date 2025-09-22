# 第一题

一家大型互联网公司需要优化负载均衡系统。公司的服务器集群需要处理大量用户请求，每台服务器有不同的处理能力。为了避免某些服务器过载而其他服务器空闲，需要设计一个算法来判断是否可以合理分配请求，使得每台服务器的负载率都不超过指定的阈值。
给定n台服务器，每台服务器的处理能力为capacity[i]`(表示第i台服务器每秒可以处理的请求数)。现在有`requests`个请求需要分配。
为了保证服务质量，每台服务器的负载率不能超过`maxLoad`(0.1<maxLoad <=1.0)。负载率定义为:实际分配的请求数/服务器处理能力。
请判断是否存在一种分配方案，使得所有请求都能被处理，且每台服务器的负载率都不超过
maxLoad`.

输入格式:
-`capacity:一个整数数组，表示每台服务器的处理能力
-requests:一个整数，表示需要处理的请求总数
-`maxLoad:一个浮点数，表示最大允许的负载率

输出格式:
-返回一个布尔值，表示是否存在合理的分配方案
示例 1:》
输入:capacity=[10,20,30],requests = 45,maxLoad=0.8 输出:true
解释:可以这样分配:-服务器1:8个请求(负载率 8/10=0.8)-服务器2:16个请求
(负载率 16/20=0.8)-服务器3:21个请求
(负载率21130=0.7)总共8+16+21=45个请求
示例 2:
输入:capacity=[10,20,30],requests = 50,maxLoad=0.8输出:false解释:最大可处理请求数为 10*0.8+20*0.8+30*0.8=8+16+24=48<50
示例 3:
输入:capacity=[100,200],requests=150
maxLoad =0.5 输出:true
解释:每台服务器使用50%容量:50+100=
150

**思路分析：**
 每台服务器允许的最大请求数 = `capacity[i] * maxLoad`。我们只需要计算所有服务器的最大可承载请求总数，看是否大于等于 `requests` 即可。
 如果可以承载，返回 true；否则返回 false。

**实现要点：**

1. 遍历 `capacity` 数组，计算每台服务器在 `maxLoad` 下的最大可分配请求数。
2. 累加所有服务器的最大可分配请求数。
3. 判断总和是否大于等于 `requests`。

``` java
public class Main {
    /**
     * 判断是否存在合理的分配方案
     * @param capacity 每台服务器的处理能力
     * @param requests 需要处理的请求总数
     * @param maxLoad 最大允许的负载率
     * @return 是否存在合理的分配方案
     */
    public static boolean canDistributeRequests(int[] capacity, int requests, double maxLoad) {
        int maxRequests = 0;
        for (int c : capacity) {
            maxRequests += (int) (c * maxLoad);
        }
        return maxRequests >= requests;
    }

    // 测试用例
    public static void main(String[] args) {
        System.out.println(canDistributeRequests(new int[]{10, 20, 30}, 45, 0.8)); // true
        System.out.println(canDistributeRequests(new int[]{10, 20, 30}, 50, 0.8)); // false
        System.out.println(canDistributeRequests(new int[]{100, 200}, 150, 0.5)); // true
    }
}
```

# 第二题

短视频作为平台的重要资源，需要推荐系统在有限的推荐位中选择视频，以最大化广告收益。每个视频有以下属性:
-时长:视频播放需要的分钟数
收益:推荐该视频预期获得的广告收益
(元)
平台有以下限制:
1奢.推荐位的总时长是有限的(比如用户平均停讙储30分钟)
2.为了保证内容多样性，相同类型的视频最多推荐2个
请设计算法，选择视频组合使得总收益最大。函数签名:public int maxVideoRevenue(int[lvideos, int!l types, int totalMinutes) 

参数说明:
-`videos[i]=[duration,revenue]:第i个视频的时长和收益
`typesi`:第i个视频的类型编号
totalMinutes`:推荐位总时长限制
返回值:
拎大可能的总收益(整数)
示例解析
示例1:
输入:
videos =[[10,50], [15,70], [10,40],[20,90]types = [1, 1, 1, 2]
totalMinutes = 30
输出:120
解释:
视频0:10分钟，收益50元，类型1
视频1:15分钟，收益70元，类型1
类型1视频2:10分钟，收益40元，类型1
视频3:20分钟，收益90元，类型2
最优方案:选择视频1和视频3
类型1的视频中选收益最高的视频1(70元)
再选类型2的视频3(90元)
总时长:15+20=35分钟，超出限制
重新选择:选择视频0、视频1
都是类型1，选收益最高的两个
总时长:10+15=25分钟≤30分钟总收益:50+70=120元

### Copilot said: 这是一道“带有类别限制的多重0-1背包问题”。   需要考虑三点限制： 1. 总时长不得超过totalMinutes； 2. 每种类型最多推荐2个视频； 3. 收益最大化。 ## 算法思路 1.

这是一道“带有类别限制的多重0-1背包问题”。
 需要考虑三点限制：

1. 总时长不得超过totalMinutes；
2. 每种类型最多推荐2个视频；
3. 收益最大化。

## 算法思路

1. 首先将所有视频按照类型进行分组。
2. 每个类型仅能选2个视频。对于每种类型，枚举所有选0个、选1个、选2个视频的组合（数量很少，最多C(n,2)）。
3. 对所有类型的组合，做0-1背包（每个类型可以贡献“0-2个视频组合”作为“物品”）。
4. 使用动态规划：dp[j] 表示总时长为j时的最大收益。

# 示例1 对的，示例2也对

```java
import java.util.*;

public class MaxVideoRevenue {
    public int maxVideoRevenue(int[][] videos, int[] types, int totalMinutes) {
        // 1. 按类型分组
        Map<Integer, List<int[]>> typeMap = new HashMap<>();
        for (int i = 0; i < videos.length; i++) {
            typeMap.computeIfAbsent(types[i], k -> new ArrayList<>()).add(videos[i]); // [duration, revenue]
        }

        // 2. 每种类型至多选2个，预处理出所有选0个、1个、2个的所有合法组合
        List<List<int[]>> options = new ArrayList<>();
        for (List<int[]> group : typeMap.values()) {
            List<int[]> cur = new ArrayList<>();
            // 选0个
            cur.add(new int[]{0, 0});
            // 选1个
            for (int i = 0; i < group.size(); i++) {
                cur.add(new int[]{group.get(i)[0], group.get(i)[1]});
            }
            // 选2个
            for (int i = 0; i < group.size(); i++) {
                for (int j = i + 1; j < group.size(); j++) {
                    int dura = group.get(i)[0] + group.get(j)[0];
                    int rev = group.get(i)[1] + group.get(j)[1];
                    cur.add(new int[]{dura, rev});
                }
            }
            options.add(cur);
        }

        // 3. 多重背包DP
        int[] dp = new int[totalMinutes + 1];
        for (List<int[]> groupOption : options) {
            int[] ndp = Arrays.copyOf(dp, dp.length);
            for (int[] opt : groupOption) {
                int dura = opt[0], rev = opt[1];
                for (int j = totalMinutes; j >= dura; j--) {
                    ndp[j] = Math.max(ndp[j], dp[j - dura] + rev);
                }
            }
            dp = ndp;
        }
        return dp[totalMinutes];
    }

    // 测试
    public static void main(String[] args) {
        MaxVideoRevenue solver = new MaxVideoRevenue();
        int[][] videos = {{10, 50}, {15, 70}, {10, 40}, {20, 90}};
        int[] types = {1, 1, 1, 2};
        int totalMinutes = 30;
        System.out.println(solver.maxVideoRevenue(videos, types, totalMinutes)); // 输出 120
    }
}
```

