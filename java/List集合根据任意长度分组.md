# List集合根据任意长度分组

```java
public static <T> List<List<T>> listSplit(List<T> list, int num) {
    //按每num个一组分割
    Integer MAX_SEND = num;
    //计算切分次数
    int limit = (list.size() + MAX_SEND - 1) / MAX_SEND;
    //使用流遍历操作
    List<List<T>> mglist = new ArrayList<>();
    Stream.iterate(0, n -> n + 1).limit(limit).forEach(i -> {
        mglist.add(list.stream().skip(i * MAX_SEND).limit(MAX_SEND).collect(Collectors.toList()));
    });
    return mglist;
}
```

