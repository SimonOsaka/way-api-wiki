## [java8中的stream对List的一些操作](https://www.cnblogs.com/nihaofenghao/p/9324562.html)

```java
package com.fh;

public class Student {

    private String age;
    private Integer sex;
    public String getAge() {
        return age;
    }
    public void setAge(String age) {
        this.age = age;
    }
    public Integer getSex() {
        return sex;
    }
    public void setSex(Integer sex) {
        this.sex = sex;
    }

}
```

```java
package com.fh;

public class Demo {

    private String name;
    private String age;
    private Integer sex;
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public String getAge() {
        return age;
    }
    public void setAge(String age) {
        this.age = age;
    }
    public Integer getSex() {
        return sex;
    }
    public void setSex(Integer sex) {
        this.sex = sex;
    }
    public Demo(String age, Integer sex) {
        super();
        this.age = age;
        this.sex = sex;
    }



}
```

```java
package com.fh;

import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

public class Test {

    @org.junit.Test
    public void test() {
         List<Student> list = new ArrayList<>();  
         Student student1 = new Student();student1.setAge("12");student1.setSex(0);  
         Student student2 = new Student();student2.setAge("13");student2.setSex(2);  
         Student student3 = new Student();student3.setAge("11");student3.setSex(1);  
         Student student4 = new Student();student4.setAge("18");student4.setSex(1);  
         Student student5 = new Student();student5.setAge("18");student5.setSex(0);  
         Student student6 = new Student();student6.setAge("18");student6.setSex(2);  
         Student student7 = new Student();student7.setAge("18");student7.setSex(2);  
         list.add(student1);  
         list.add(student2);  
         list.add(student3);  
         list.add(student4);  
         list.add(student5);  
         list.add(student6);  
         list.add(student7);  
         List<Demo> demos = new ArrayList<Demo>();
         demos = printData(demos, list);
//         printSexequal0(demos);
//         filterAge(demos);
//         sort(demos);
//         pour(demos);
//         pour2(demos);
//         moreSort(demos);
//         morePour(demos);
         groupByAge(demos);

    }

    /**
     * 数据打印
     * @param demos
     * @param list
     */
    public List<Demo> printData(List<Demo> demos ,List<Student> list) {
        demos = list.stream().map(student -> new Demo(student.getAge(),student.getSex())).collect(Collectors.toList());
        /*demos.forEach(demo ->{
            System.out.println(demo.getAge());
        });*/
        return demos;
    }

    /**
     * 打印性别为0的数据
     * @param demos
     */
    public void printSexequal0(List<Demo> demos) {
        List<Demo> collect = demos.stream().filter(demo -> demo.getSex() == 0).distinct().collect(Collectors.toList());
        collect.forEach(item ->{
            System.out.println("\n"+item.getAge()+":"+item.getSex());
        });
    }

    /**
     * 过滤年龄大于12的信息
     * @param demos
     */
    public void filterAge(List<Demo> demos) {
        List<Demo> collect = demos.stream().filter(demo -> Integer.valueOf(demo.getAge())>12).collect(Collectors.toList());
        collect.forEach(demo ->{
            System.out.println(demo.getAge()+":"+demo.getSex());
        });
    }

    /**
     * 数据排序
     * @param demos
     */
    public void sort(List<Demo> demos) {
        List<Demo> collect = demos.stream().sorted((s1,s2) -> s1.getAge().compareTo(s2.getAge())).collect(Collectors.toList());
        collect.forEach(demo -> {
            System.out.println(demo.getAge());
        });
    }

    /**
     * 倒叙
     * @param demos
     */
    public void pour(List<Demo> demos) {
        ArrayList<Demo> demoArray = (ArrayList<Demo>)demos;
        demoArray.sort(Comparator.comparing(Demo::getAge).reversed());
        demoArray.forEach(demo -> {
            System.out.println(demo.getAge());
        });
    }

    /**
     * 倒叙2
     * @param demos
     */
    public void pour2(List<Demo> demos) {
        ArrayList<Demo> demoArray = (ArrayList<Demo>)demos;
        Comparator<Demo> comparator = (h1,h2) -> h1.getAge().compareTo(h2.getAge());
        demoArray.sort(comparator.reversed());
        demoArray.forEach(demo -> {
            System.out.println(demo.getAge());
        });
    }

    /**
     * 多条件排序--正序
     * @param demos
     */
    public void moreSort(List<Demo> demos) {
        demos.sort(Comparator.comparing(Demo::getSex).thenComparing(Demo::getAge));
        demos.forEach(demo ->{
            System.out.println(demo.getSex()+":"+demo.getAge());
        });
    }

    /**
     * 多条件倒叙
     * @param demos
     */
    public void morePour(List<Demo> demos) {
        demos.sort(Comparator.comparing(Demo::getSex).reversed().thenComparing(Demo::getAge));
        demos.forEach(demo ->{
            System.out.println(demo.getSex()+":"+demo.getAge());
        });
    }

    /**
     * 分组
     * @param demos
     */
    public void groupByAge(List<Demo> demos) {
        Map<String, List<Demo>> collect = demos.stream().collect(Collectors.groupingBy(Demo::getAge));
        collect.forEach((key,value)->{
            value.forEach(demo ->{
                System.out.println(key+":"+demo.getSex());
            });
        });
    }
}
```

代码是网上找的，自己写了一遍，记录一下

```java
    @SuppressWarnings("unused")
    public void filterTest(List<Demo> demos) {
        String age = "12";
        String sex = "0";
        List<Demo> collect = demos.stream().filter(demo -> {
            if(age!=null) {
                return Integer.parseInt(demo.getAge())>Integer.parseInt(age);
            }else {
                return true;
            }
        }).filter(demo ->{
            if(sex!=null) {
                return demo.getSex() ==Integer.parseInt(sex);
            }else {
                return true;
            }
        }).collect(Collectors.toList());

        collect.forEach(demo ->{
            System.out.println(demo.getAge()+":"+demo.getSex());
        });
    }
```

 多条件排序和去重复测试

```java
@Test
    public void testMoreSort() {
        List<Entrys> list = new ArrayList<>();
        Entrys item = new Entrys();
        item.setOne(1);
        item.setTwo(1);
        Entrys item1 = new Entrys();
        item1.setOne(1);
        item1.setTwo(2);
        Entrys item2 = new Entrys();
        item2.setOne(1);
        item2.setTwo(3);
        Entrys item3 = new Entrys();
        item3.setOne(1);
        item3.setTwo(4);

        Entrys item4 = new Entrys();
        item4.setOne(2);
        item4.setTwo(1);
        Entrys item5 = new Entrys();
        item5.setOne(2);
        item5.setTwo(2);
        Entrys item6 = new Entrys();
        item6.setOne(2);
        item6.setTwo(3);
        Entrys item7 = new Entrys();
        item7.setOne(2);
        item7.setTwo(4);

        Entrys item8 = new Entrys();
        item8.setOne(3);
        item8.setTwo(1);
        Entrys item9 = new Entrys();
        item9.setOne(3);
        item9.setTwo(2);
        Entrys item10 = new Entrys();
        item10.setOne(3);
        item10.setTwo(3);
        Entrys item11 = new Entrys();
        item11.setOne(4);
        item11.setTwo(4);


        list.add(item1);
        list.add(item2);
        list.add(item3);
        list.add(item4);
        list.add(item5);
        list.add(item6);
        list.add(item7);
        list.add(item8);
        list.add(item9);
        list.add(item10);
        list.add(item11);
        list.sort(Comparator.comparing(Entrys::getOne).thenComparing(Entrys::getTwo));
        list.forEach(demo ->{
            System.out.println(demo.getOne()+":"+demo.getTwo());
        });

    }



    @Test
    public void testDic() {
        List<Models> list = new ArrayList<>();
        Models model = new Models();
        model.setName("111");
        model.setAge("238");
        model.setSemi_fid("xxxcqqccbbb");
        model.setTid("111111");
        Models model1 = new Models();
        model1.setName("11122312");
        model1.setAge("237");
        model1.setSemi_fid("xxxccqcbbb");
        model1.setTid("11111133");
        Models model2 = new Models();
        model2.setName("111333123");
        model2.setAge("236");
        model2.setSemi_fid("xxxccqcbbb");
        model2.setTid("111111");
        Models model3 = new Models();
        model3.setName("1113213");
        model3.setAge("236");
        model3.setSemi_fid("xxxccqqcbbb");
        model3.setTid("111111");
        Models model4 = new Models();
        model4.setName("feng");
        model4.setAge("25");
        model4.setSemi_fid("xxxcccqqqbbb");
        model4.setTid("111111");

        Models model5 = new Models();
        model5.setName("hao");
        model5.setAge("23");
        model5.setSemi_fid("xxxcccqqqbbb");
        model5.setTid("111111");

        list.add(model);
        list.add(model1);
        list.add(model2);
        list.add(model3);
        list.add(model4);
        list.add(model5);

        //顺序不变
        List<Models> collect = list.stream().distinct().collect(Collectors.toList());
        collect.forEach(demo ->{
            System.out.println(demo.getName()+":"+demo.getAge()+":"+demo.getSemi_fid()+":"+demo.getTid());
        });
        //顺序发生变化
//        List<ClassEntity> distinctClass = classEntities.stream().
//        collect(
//        Collectors.collectingAndThen(
//        Collectors.toCollection(
//        () -> new TreeSet<>(
//        Comparator.comparing(o -> o.getProfessionId() + ";" + o.getGrade()
//        ))), ArrayList::new));
/*        ArrayList<Models> collect = list.stream().
        collect(
                Collectors.collectingAndThen(
                        Collectors.toCollection(
                                () ->new TreeSet<Models>(
                                        Comparator.comparing(demo -> demo.getSemi_fid()+";"+demo.getTid())
                                        )
                                ), ArrayList::new)
                );

        collect.forEach(demo ->{
            System.out.println(demo.getName()+":"+demo.getAge()+":"+demo.getSemi_fid()+":"+demo.getTid());
        });*/

    }


}

class Entrys{

    private int one;

    private int two;

    public int getOne() {
        return one;
    }

    public void setOne(int one) {
        this.one = one;
    }

    public int getTwo() {
        return two;
    }

    public void setTwo(int two) {
        this.two = two;
    }
```

```java
package com.voole.forerunner;

public class Models {

    private String name;

    private String semi_fid;

    private String tid;

    private String age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSemi_fid() {
        return semi_fid;
    }

    public void setSemi_fid(String semi_fid) {
        this.semi_fid = semi_fid;
    }

    public String getTid() {
        return tid;
    }

    public void setTid(String tid) {
        this.tid = tid;
    }

    public String getAge() {
        return age;
    }

    public void setAge(String age) {
        this.age = age;
    }

    @Override
    public int hashCode() {
        return (this.getSemi_fid()+this.getTid()).hashCode();
    }

    @Override
    public boolean equals(Object obj) {
        if(obj instanceof Models) {
            Models item = (Models)obj;
            if(this.getSemi_fid().equals(item.getSemi_fid()) && this.getTid().equals(item.getTid())) {
              return true;    
            }else {
                return false;
            }
        }else {
            return false;
        }
    }







}
```

---

# [java8新特性练手--从菜鸟教程中](https://www.cnblogs.com/nihaofenghao/p/9341962.html)

```java
package com.fh.jdk8;

import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

import org.junit.Test;

public class Learning01 {

    @Test
    public void sort() {
        List<String> list = new ArrayList<>();
        list.add("feng");
        list.add("hao");
        list.add("haha");
        list.add("xixi");
        sort7(list);
        sort8(list);
    }

    public void sort7(List<String> list) {
        Collections.sort(list, new Comparator<String>() {

            @Override
            public int compare(String o1, String o2) {
                return o1.compareTo(o2);
            }

        });
        for (String string : list) {
            System.out.println(string);
        }
    }
    public void sort8(List<String> list) {
        Collections.sort(list, (o1,o2) -> o1.compareTo(o2));
        list.forEach(demo ->{
            System.out.println(demo);
        });
    }

    /**
     * lambda表达式学习--闭包
     */
    @Test
    public void lambda() {
        Learning01 instance = new Learning01();
        MathOperation add = (int a,int b) -> a+b;
        MathOperation sub = (a,b) ->a-b;
        MathOperation mul = (a,b) ->{return a * b;};
        MathOperation div = (a,b) -> a/b;
        System.out.println(instance.operator(1, 2, add));


        GreetingService service = (message) ->
        System.out.println("hello "+message);

        service.sayMessgae("hahah");
    }

    public int operator(int a,int b,MathOperation mathOperation) {
        return mathOperation.operation(a, b);
    }

    interface MathOperation {
        int operation(int a,int b);
    }

    interface GreetingService {
        void sayMessgae(String message);
    }


    @Test
    public void test22() {
        Person person = new Man();
        person.print();

    }

}
```

```java
package com.fh.jdk8;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.time.Month;
import java.time.ZoneId;
import java.time.ZonedDateTime;
import java.util.Arrays;
import java.util.Base64;
import java.util.IntSummaryStatistics;
import java.util.List;
import java.util.Random;
import java.util.UUID;
import java.util.stream.Collectors;

import org.junit.Test;

/**
 * Stream API test
 * @author Administrator
 *
 */
public class Learning02 {

    @Test
    public void test01() {
        List<String> list = Arrays.asList("1","2","3","","4","5","6");
        List<String> collect = list.stream().filter(demo ->!demo.isEmpty()).collect(Collectors.toList());
        collect.forEach(demo ->{
            System.out.println(demo);
        });
    }

    @Test
    public void test02() {
        Random random = new Random();
        random.ints().limit(1).forEach(System.out::println);
    }

    @Test
    public void test03() {
        List<Integer> asList = Arrays.asList(2,2,3,4,5,6,7);
        List<Integer> collect = asList.stream().map(demo -> demo*demo).distinct().collect(Collectors.toList());
        collect.forEach(demo ->{
            System.out.println(demo);
        });
        System.out.println("*********");
        collect.stream().filter(demo -> demo>5).collect(Collectors.toList()).forEach(demo->{
            System.out.println(demo);
        });
    }

    @Test
    public void test04() {
        List<String> list = Arrays.asList("ha","niin","hhh","jj","ll");
        String string = list.parallelStream().findFirst().get();
        System.out.println(string);
    }

    @Test
    public void test05() {
        List<String> list = Arrays.asList("ha","niin","hhh","jj","ll");
        String string = list.parallelStream().filter(demo ->!demo.isEmpty()).collect(Collectors.joining(","));
        System.out.println(string);
    }

    @Test
    public void test06() {
        List<Integer> list = Arrays.asList(2,3,4,5,6,6,7,8,8,8,8,8);
        IntSummaryStatistics summer = list.stream().mapToInt(demo ->demo).summaryStatistics();
        System.out.println("average:"+summer.getAverage());
        System.out.println("count:"+summer.getCount());
        System.out.println("max:"+summer.getMax());
        System.out.println("min:"+summer.getMin());
        System.out.println("sum:"+summer.getSum());
    }

    @Test
    public void test07() {
        LocalDateTime currentTime = LocalDateTime.now();
//        System.out.println(currentTime);
//        System.out.println(currentTime.toLocalDate());
//        System.out.println(currentTime.toLocalTime());
        Month month = currentTime.getMonth();
        int day = currentTime.getDayOfMonth();
        int second = currentTime.getSecond();
//        System.out.println("月:"+month+"天:"+day+"秒:"+second);

        LocalDateTime withYear = currentTime.withDayOfMonth(10).withYear(2018);
//        System.out.println("withDayOfMonth:"+withYear);
        /*2018-07-20T15:18:33.054
        2018-07-20
        15:18:33.054
        月:JULY天:20秒:33
        withDayOfMonth:2018-07-10T15:18:33.054*/
        LocalDate of = LocalDate.of(2018, Month.DECEMBER, 20);
        System.out.println(of);

        LocalTime times = LocalTime.of(12, 12);
        System.out.println(times);

        LocalTime parse = LocalTime.parse("15:22:22");
        System.out.println(parse);
    }

    @Test
    public void test08() {
        ZonedDateTime parse = ZonedDateTime.parse("2015-12-03T10:15:30+05:30[Asia/Shanghai]");
        System.out.println(parse);

        ZoneId of = ZoneId.of("Europe/Paris");
        System.out.println(of);

        ZoneId systemDefault = ZoneId.systemDefault();
        System.out.println(systemDefault);
    }

    @Test
    public void test09() {
        try {
            //编码
            String encodeToString = Base64.getEncoder().encodeToString("run?java8".getBytes("utf-8"));
            System.out.println(encodeToString);
            //解码
            byte[] decode = Base64.getDecoder().decode(encodeToString);
            System.out.println(new String(decode,"utf-8"));

            String urlEncoder = Base64.getUrlEncoder().encodeToString("http://baidu.com".getBytes("utf-8"));
            System.out.println(urlEncoder);

            StringBuilder builder = new StringBuilder();
            for (int i = 0; i < 20; i++) {
                builder.append(UUID.randomUUID().toString());
            }
            String mime = Base64.getMimeEncoder().encodeToString(builder.toString().getBytes("utf-8"));
            System.out.println(mime);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }


}
```

---

# [使用Java8 合并List>为一个Map?](https://segmentfault.com/q/1010000010380854)

```java
Map<String,Object> h1 = new HashMap<>();
h1.put("12","fdsa");
h1.put("123","fdsa");
h1.put("124","fdsa");
h1.put("125","fdsa");

Map<String,Object> h2 = new HashMap<>();
h2.put("h12","fdsa");
h2.put("h123","fdsa");
h2.put("h124","fdsa");
h2.put("h125","fdsa");

Map<String,Object> h3 = new HashMap<>();
h3.put("h312","fdsa");
h3.put("h3123","fdsa");
h3.put("h3124","fdsa");
h3.put("h3125","fdsa");

List<Map<String,Object>> lists = new ArrayList<>();
lists.add(h1);
lists.add(h2);
lists.add(h3);
```

```java
private Map<String,Object> megerListMap(List<Map<String,Object>> listsMap){
        Map<String,Object> map = new HashMap<>();
        listsMap.forEach(x->{
            x.forEach((y,z)->{
                map.put(y, z);
            });
        });
        return map;
}
```
