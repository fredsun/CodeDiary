* 自己的正确答案:
* 执行用时167ms, 内存消耗39.7MB, 时间复杂度O(n2);
```
//先排序，不能直接二次轮询，同一个元素不能使用两遍
//考虑数组大小条件，多考虑了数组排序
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int result[] = new int[2];
        if (nums.length >=2){
            for(int i = 0; i < nums.length; i++) {
                if(i > nums.length){
                    // System.out.println("target大于nums最大值");
                    break;
                }else{
                    for(int j = i + 1; j < nums.length; j++){
                        if(nums[i] + nums[j] == target){
                            System.out.println(i+","+j);
                            result[0] = i;
                            result[1] = j;
                            System.out.println("["+i+","+j+"]");
                        }else{
                            // System.out.println("不存在");
                        }
                    }
                }

            }

            return new int[]{result[0], result[1]};
        }else{
            // System.out.println("数组过小");
            return result;
        }

    }
}
```

* 标准答案1 —— 暴力法
* 执行用时102ms, 内存消耗39.7MB
```
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[j] == target - nums[i]) {
                    return new int[] { i, j };
                }
            }
        }
        throw new IllegalArgumentException("No two sum solution");
    }
}
```

* 标准答案2 —— 两遍哈希表
* 执行用时3ms, 内存消耗40.1MB
* 时间复杂度O(n), 空间复杂度O(n);
* 哈希表的优越性在于查找时间复杂度为O(1);
```
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            map.put(nums[i], i);
        }
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (map.containsKey(complement) && map.get(complement) != i) {
                return new int[] { i, map.get(complement) };
            }
        }
        throw new IllegalArgumentException("No two sum solution");
    }
}
```


* 标准答案3 —— 一遍哈希表
* 执行用时3ms, 内存消耗39.2MB, 但是只击败了87.26%
* 时间复杂度O(n), 空间复杂度O(n)
```
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (map.containsKey(complement)) {
                return new int[] { map.get(complement), i };
            }
            map.put(nums[i], i);
        }
        throw new IllegalArgumentException("No two sum solution");
    }
}
```

* 超过99.59%的答案
* 提前扩充HashMap, 防止rehash
* / 0.75F + 1.0F 参考自[CSDN](https://blog.csdn.net/weixin_44591035/article/details/103648284), HashMap会在容量达到负载因子即loadFactor默认0.75的时候rehash到比当前大的下一个2的幂，提前扩大可以减少扩容的几率, 空间换性能
```
class Solution {
   public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>((int) ((float) nums.length / 0.75F + 1.0F));
        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(target - nums[i])) {
                return new int[]{map.get(target - nums[i]), i};
            }
            map.put(nums[i], i);
        }
        throw new IllegalArgumentException("No two sum value");
    }
}
```
