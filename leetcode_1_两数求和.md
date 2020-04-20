临时上传一个错误答案
```
//先排序，不能二次轮询，同一个元素不能使用两遍
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int result[] = new int[2];
        if (nums.length >2){
            for(int i = 0; i < nums.length; i++) {
                if(nums[i] > nums.length){
                    //System.out.println("target大于nums最大值");
                    break;
                }else{
                    for(int j = i + 1; j < nums.length; j++){
                        if(nums[i] + nums[j] == target){
                            System.out.println(i+","+j);
                            result[0] = i;
                            result[1] = j;
                            System.out.println("["+i+","+j+"]");
                        }else{
                            //System.out.println("不存在");
                        }
                    }
                }

            }

           return new int[]{result[0], result[1]};
        }else{
            //System.out.println("数组过小");
            return result;
        }

    }
}
```
