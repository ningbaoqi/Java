#### 二分法查找（必须是有序的数组才能使用二分法查找）
```
public class Test{
       static int binarySearch(int[] a , int key){
              int low = 0;
              int high = a.length;
              while(low < high){
                     int middle = (low + high) / 2;
                     if(a[middle] == key){
                            return middle;
                     }else if(a[middle] < key){
                            low = middle + 1;
                     }else if(a[middle] > key){
                            high = middle -1;
                     }
              }
              return -1;
       }
       public static void main(String[] args){
              int[] a = {3 , 6 , 9 , 5 , 1,  2 , 8 , 7 , 0 , 4};
              Arrays.sort(a);
              int key = 2;
              int index = binarySearch(a , key);
              if(index == -1){
                     System.out.println("没找到");
              }else{
                     System.out.println("找到了，索引为：" + index);
              }
       }
}
```



