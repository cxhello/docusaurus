---
id: stream
---

# Stream流常用方法

```java
import lombok.Data;

/**
 * @author cxhello
 * @date 2021/1/20
 */
@Data
public class User {

    /**
     * 主键ID
     */
    private Long id;

    /**
     * 姓名
     */
    private String name;

    /**
     * 年龄
     */
    private Integer age;
    
}
```

1. 获取用户所有的ID

```java
List<User> userList = new ArrayList<>();
List<Long> idList = userList.stream().map(User::getId).collect(Collectors.toList());
```

2. 指定 key-value，value 是对象中的某个属性值

```java
List<User> userList = new ArrayList<>();
Map<Long, String> map = userList.stream().collect(Collectors.toMap(User::getId, User::getName));
```

3. 指定key-value，value是对象本身，User-> User 是一个返回本身的 lambda 表达式

```java
List<User> userList = new ArrayList<>();
Map<Long, User> map = userList.stream().collect(Collectors.toMap(User::getId, User -> User));
```

4. 根据年龄分组，key 为年龄，value 为年龄的用户

```java
List<User> userList = new ArrayList<>();
Map<Integer, List<User>> map = userList.stream().collect(groupingBy(User::getAge));
```

5. 按照年龄倒序分组，里面的年龄也是倒序的数据

```java
List<User> userList = new ArrayList<>();
Map<Integer, List<User>> map = userList.stream()
		.sorted(Comparator.comparing(User::getAge, Comparator.nullsLast(Comparator.reverseOrder())))
		.collect(groupingBy(User::getAge, LinkedHashMap::new, Collectors.toList()));
```

6. 筛选出年龄大于 18 的用户

```java
List<User> userList = new ArrayList<>();
List<User> resultUserList = userList.stream().filter(user -> user.getAge() > 18).collect(Collectors.toList());
```

7. 拷贝到新的对象集合中

```java
import lombok.Data;

/**
 * @author cxhello
 * @date 2021/1/20
 */
@Data
public class UserVO {

    /**
     * 姓名
     */
    private String name;

    /**
     * 年龄
     */
    private Integer age;

}
```

```java
List<User> userList = new ArrayList<>();
List<UserVO> userVoList = userList.stream().map(user -> {
	UserVO userVo = new UserVO();
	userVo.setName(user.getName());
	userVo.setAge(user.getAge());
	return userVo;
}).collect(Collectors.toList());
```
