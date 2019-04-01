## Optional

设计初衷是为了让使用者一样看出来该类是否允许为空。其实是一种设计上的改变。将 **空对象与平常对象**在使用上不会产生不同的区别，也减少了层层```if else```的判断。



## 常用方法解释说明

```java
Optional of(T t); // 将对象包装成Optional对象。当t为null时报空指针异常。
Optional ofNullable(T value); // 将对象包装成Optional对象，允许value为null。
T get()； // 获取Optional里面的值，若value为null，抛出NoSuchElementException异常。其实此方法并无设计上的改进，与平常直接使用值时坑为空位一样的道理。
boolean isPresent(); // 判断是否有值
void ifPresent(Consumer<? super T> consumer)；// 若存在值，则对改值进行消费。(也就是做一些操作)。
Optional<T> filter(Predicate<? super T> predicate); // 根据某条件过滤值。
T orElse(T other); // 如果有值，则返回包装的值，否则则返回参数other。
T orElseGet(Supplier<? extends T> other)； // 相较于上面的方法，则采用一定的过程产生功能值。
<X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) throws X; //类似于上面的方法，不存在则抛出异常
Optional<T> empty()；// 创建一个空的Optional对象
```



## 2个与书上的不同的方法理解

```java
Optional<U> map(Function<? super T, ? extends U> mapper);// 自己试验这个方法是可以连接使用的。
public<U> Optional<U> map(Function<? super T, ? extends U> mapper) {
        Objects.requireNonNull(mapper);
        if (!isPresent())
            return empty();
        else {
            return Optional.ofNullable(mapper.apply(value));
        }
    }
```

```java
Optional<U> flatMap(Function<? super T, Optional<U>> mapper); // 扁平流实现

public<U> Optional<U> flatMap(Function<? super T, Optional<U>> mapper) {
        Objects.requireNonNull(mapper);
        if (!isPresent())
            return empty();
        else {
            return Objects.requireNonNull(mapper.apply(value));
        }
    }
```



并不会像书上所说的那样会产生嵌套的Optional对象。

自己实验的例子如下：

```java
public static void main(String[] args) {
        Insurace insurace = new Insurace(1, "保险名称1");

        Car c1 = new Car(1, "汽车名称1", insurace);
        Car c2 = new Car(1, "汽车名称2", insurace);

        Person p1 = new Person(c1, "1");
        Person p2 = new Person(null, "2");

        Optional<Person> person = Optional.of(p1);

        Optional<String> insuraceName = person.map(Person::getCar)
                .map(Car::getInsurace)
                .map(Insurace::getName);

        System.out.println(insuraceName.get());
  
  			Optional<Insurace> insuraceName = person.flatMap(t -> {return Optional.of(insurace);});

    }
// 会正常输出保险名称1
```



类Insurace：

```java
public class Insurace {

    int id;
    String name;

    public Insurace(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Insurace{" + "id=" + id + ", name='" + name + '\'' + '}';
    }
}
```



类Car：

```java
public class Car {

    int carId;
    String carName;
    Insurace insurace;

    public Car(int carId, String carName, Insurace insurace) {
        this.carId = carId;
        this.carName = carName;
        this.insurace = insurace;
    }

    public int getCarId() {
        return carId;
    }

    public void setCarId(int carId) {
        this.carId = carId;
    }

    public String getCarName() {
        return carName;
    }

    public void setCarName(String carName) {
        this.carName = carName;
    }

    public Insurace getInsurace() {
        return insurace;
    }

    public void setInsurace(Insurace insurace) {
        this.insurace = insurace;
    }
}
```

类persoon：

```jva
public class Person {

    Car car;
    String personId;

    public Person(Car car, String personId) {
        this.car = car;
        this.personId = personId;
    }

    public Car getCar() {
        return car;
    }

    public void setCar(Car car) {
        this.car = car;
    }

    public String getPersonId() {
        return personId;
    }

    public void setPersonId(String personId) {
        this.personId = personId;
    }
}
```

