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
#### 直接查找算法（就是遍历数组进行查找，效率很低）
```
public class Test{
       static int search(int[] a , int key){
              int index = -1;
              for(int i = 0 ; i < a.length ; i++){
                     if(a[i] == key){
                            index = i;
                            break;
                     }
              }
              return index;
       }
       public static void main(String[] args){
              int[] a = {1 , 23 , 4 , 5 , 6 , 7, 8 , 9};
              int index = search(a , 5);
              if(index == -1){
                     System.out.println("没找到");
              }else{
                     System.out.println("找到了；当前索引为：" + index);
              }
       }
}
```
#### 快速排序算法（效率高）
```
public class Test{
       static void quickSort(int[] a , int start , int end){
              if(start < end){
                     int base = a[start];
                     int temp;
                     int i = start, j = end;
                     do{
                            while((a[i] < base) && (i < end)){
                                   i++;
                            }
                            while((a[j] > base) && (j > start)){
                                   j--;
                            }
                            if(i <= j){
                                   temp = a[i];
                                   a[i] = a[j];
                                   a[j] = temp;
                                   i++;
                                   j--;
                            }
                     }while(i <= j);
                     if(start < j){
                            quickSort(a , start , j);
                     }
                     if(end > i){
                            quickSort(a , i , end);
                     }
              }
       }
       public static void main(String[] args){
              int[] a = {4 , 6 ,2 , 5 , 1 , 0 , 9 , 7 , 3 , 8 };
              quickSort(a , 0 , a.length - 1);
              for(int x : a){
                     System.out.println(x + "");
              }
       }
}
```
#### 插入排序算法
```
public class Test{
       public static void main(String[] args){
              int[] a = {4 , 3 , 5 , 7 , 1 , 6 , 8 , 2 , 10 , 9};
              int size = a.length , temp , j;
              for(int i = 1 ; i < size ; i++){
                     temp = a[i];
                     for(j = i ; j > 0 && temp < a[j - 1] ; j--){
                            a[j] = a[j - 1];
                     }
                     a[j] = temp;
              }
              for(int i : a){
                     System.out.println(i);
              }
       }
}
```
#### 选择排序算法
```
public class Test{
       public static void main(String[] args){
              int[] a = {4 , 3 , 5 , 7 , 1 , 6 , 8 , 2  , 10 , 9};
              int size = a.length;
              for(int i = 0 ; i < size -1 ; i++){
                     int min = i;
                     for(int j = i+1 ; j < size ; j++){
                            if(a[j] < a[min]){
                                   min = j;
                            }
                     }
                     if(min != i){
                            int temp = a[i];
                            a[i] = a[min];
                            a[min] = temp;
                     }
              }
              for(int i : a){
                     System.out.println(i);
              }
       }
}
```

