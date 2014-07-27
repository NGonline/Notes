[TOC]

----------
# Philosophy

## General Programming
- Programming is about managing complexity.
- You can not look at Java as just a collection of features – some of the features make no sense in isolation. You can use the sum of the parts only if you are thinking about design, not simply coding.
- Procedural programs tend to be confusing because the terms of expression are oriented more toward the computer than to the problem you're solving.
- When designing a system, your goal is to find or create a set of classes in which each class has a specific use and is neither too big (encompassing so much functionality that it's unwieldy to reuse) nor annoyingly small (you can't use it by itself or without adding functionality).
- It's important to realize that program development is an incremental process. It relies on experimentation; you can do as much analysis as you want, but you still won't know all the answers when you set out on a project.
- Java certainly has static type checking, but it also has a significant amount of dynamic typing.

### Difference to Other Languages
- Java is probably the first language whose main design goal is to conquer the complexity of developing and maintaining programs.
- There is a goal of reducing complexity for the programmer.
- Many language design decisions were made with complexity in mind, but at some point there were always other issues that were considered essential to be added into the mix:
 - C++: backwards-compatible with C, as well as efficient;
 - VB: tied to BASIC, which wasn't really designed to be an extensible language, so all extensions piled produced some truly unmaintainable syntax;
 - Perl: backwards-compatible with awk, sed, grep, and other Unix tools. Thus it's "write-only code".
- The Java language assumes that you want to do only object-oriented programming.

## Object-Oriented Programming
- OOP is part of this movement toward using the computer as an expressive medium.
- Characteristics of OOP: 
1. Everything is an object;
2. A program is a bunch of objects telling each other what to do by sending messages;
3. Each object has its own memory made up of other objects (building complexity into a program while hiding it behind the simplicity of objects);
4. Every object has a type;
5. All objects of a particular type can receive the same messages (substitutability).
The difference is that a programmer defines a class to fit a problem rather than being forced to use an existing data type that was designed to represent a unit of storage in a machine.

### Abstraction
- These languages are big improvements over assembly language, but their primary abstraction still requires you to think in terms of the structure of the computer rather than the structure of the problem.
- The programmer must establish the association between the machine model ("solution space") and the model of the problem that is actually being solved ("problem space").
- The alternative to modeling the machine is to model the problem you are trying to solve: 
 - LISP: all problems are ultimately lists;
 - APL: all problems are algorithmic;
 - Prolog: cast all problems into chains of decisions;
- OO is general enough that the programmer is not constrained to any particular type of problem.
- The idea is that the program is allowed to adapt itself to the lingo of the problem by adding new types of objects so when you read the code describing the solution, you're reading words that also express the problem.
- OOP allows you to describe the problem in terms of the problem, rather than in terms of the computer where the solution will run.
- Each object looks quite a bit like a little computer. (与solution space的联系).

### Polymorphism
- Adding new types is the most common way to extend an object-oriented program to handle new situation.
- When the message is sent, the programmer doesn't want to know what piece of code will be executed.
- Object-oriented languages use the concept of late binding. The compiler does ensure that the method exists and performs type checking on the arguments and return value, but it doesn't know the exact code to execute.
- In Java, dynamic binding is the default behavior.

# Basics

## Reference
- Java object identifiers are actually "object reference". You are not passing by reference, You are "passing an object reference by value":
```
void foo(T input){
    // will change the object
    someChange(input);
    // will not change the identifier binding in the caller method
    input = new T();
}
```
- You may create only the reference, but not an object.
```
String s;
```
- The usual exception is primitive data types, you actually pass by value of them.
- If you don't want the returned member object to be modified (usually private members), you must return a full copy of it (you could use `Clonable` interface and clone method if your class defines it).
```
public Object clone(){
    CloneClass o = null;
    try{
        o = (CloneClass)super.clone();
        o.fields = xxx;
    } catch(CloneNotSupportedException e){
        e.printStackTrace();
    }
    return o;
}
```

## Primitive Type
- Instead of creating the variable by using `new`, an "automatic" variable is created that is not a reference.
- Java determines the size of each primitive type. These sizes don't change from on machine architecture to another.
- All numeric types are signed.
- `void` has a wrapper type `Void`.
- The "wrapper" classes for the primitive data types allow you to make a non-primitive object on the heap to represent that primitive type.
- All wrapper classes and `String` are immutable. Every time you try to modify it, Java creates a new version:
```
Map<Integer,Integer> m = new HashMap<Integer,Integer>();
m.put(1, 1);
Integer i = m.get(1);
i++;
System.out.println(m);  // still {i=1}
```
- High-precision arithmetic classes `BigInteger` and `BigDecimal` have no primitive analogue.
- When a primitive data type is a member of a class, it is guaranteed to get a default value if you do not initialize it. This guarantee does not apply to local variables. If you forget, you will get a compile-time error.
- You can not use a non-boolean as if it were a `boolean` in a logical expression as you can in C and C++. `while(x = y)` will conveniently give you a compile-time error.
- If you try to initialize a variable with a value bigger than it can hold (regardless of the numerical form of the value), the compiler will give you an error message. Calling method is similar:
```
void f(int a) {}
public static void main(String[] args){
    double d = 0.0;
    f(b);   // error
}
```
- `POSSITIVE_INFINITE` (`NEGTIVE_INFINITE`) in float-point primitive types are guarenteed to be greater (smaller) than other values except for `NaN`.
- All comparison with `NaN` return `false`. Note that divide by 0 throws an exception rather than returns `NaN`

### Literals
- A trailing character after a literal value establishes its type. (l,L,f,F,d,D).
- There is no literal representation for binary numbers. This is easily accomplished with `static toBinaryString()` methods. When passing smaller types to Integer.toBinaryString(), the type is automatically converted to an `int`.
- The compiler normally takes exponential numbers as doubles. `float f = 1e-43` will give you an error. `float f = 1e-43f` will be ok.

### Autoboxing and Unboxing
- During autoboxing, the compiler automatically calls the `valueOf()` method of the wrapper class.
- For `Integer`, the JVM caches all integers between -128 and `IntegerCache.high`:
```
public static Integer valueOf(int i) {
    if(i >= -128 && i <= IntegerCache.high)
        return IntegerCache.cache[i + 128];
    else
        return new Integer(i);
}
```
`IntegerCache.high` is lower than 127:
```
private static class IntegerCache {
    static final int high;
    static final Integer cache[];
    static {
        final int low = -128;
            // high value may be configured by property
        int h = 127;
        if (integerCacheHighPropValue != null) {
             // 得到系统参数integerCacheHighPropValue的值
            int i = Long.decode(integerCacheHighPropValue).intValue();
             // 指定的参数不能小于127，否则会设为127
            i = Math.max(i, 127);
            h = Math.min(i, Integer.MAX_VALUE - -low);
        }
        //  127=< high <=Integer.MAX_VALUE-128 
        high = h;
        //  填充满Integer缓存
        cache = new Integer[(high - low) + 1];
        int j = low;
        for(int k = 0; k < cache.length; k++)
            cache[k] = new Integer(j++);
    }
    private IntegerCache() {}
}
```
so:
```
public class T {
	public static void main(String[] agrs){
		int i = 128;
		int j = 128;
		System.out.println(i==j);   // true
		
		Integer a = 126;
		Integer b = 126;
		System.out.println(a==b);   // true

		Integer one = 128;
		Integer two = 128;
		System.out.println(one == two); // false
	}
}
```
- So when should you use autoboxing and unboxing? Use them only when there is an "impedance mismatch" between reference types and primitives, for example, when you have to put numerical values into a collection. It is **not** appropriate to use autoboxing and unboxing for **scientific computing**, or other performance-sensitive numerical code. An Integer is not a substitute for an int; autoboxing and unboxing **blur the distinction** between primitive types and reference types, but they do not eliminate it.
- `Interger.parseInt(String)` returns an `int` primitive. `Integer.valueOf(String)` uses `parseInt()` internally, and returns an `Integer`. They both throws `NumberFormatException`.

## Scope
- A variable defined within a scope is available only to the end of that scope.
- In Java, same variable name in nested scopes are not allowed.
- When creating a Java object using `new`, it hangs around past the end of the scope.

## Java Operator
- Almost all operators work only with primitives. The exceptions are `=`, `==`, and `!=`, which work with all objects. In addition, the `String` class supports `+` and `+=`.
- When the compiler sees a `String` followed by a `+` followed by a non-String, it attempts to convert the non-String into a `String`.

### Assignment
- An lvalue must be a distinct, named variable. There must be a physical space to store the value.
- Assignment of primitives copy the contents from one place to another. Assignment of objects actually copying a reference from one place to another.
- Aliasing is a fundamental way that Java works with objects.

### Relational Operator
- The operator == and != compare object references.
- `equals()` exists for all objects except primitives, which works fine with == and !=. The default behavior of `equals()` is to compare references, unless you override it.

### Bitwise Operators
- The `boolean` type is treated as a one-bit value.
- `~` takes one's complement operation.

### Shift Operators
- They can be used solely with primitive, integral types.
- `>>` is signed right shift, `>>>` is unsigned right shift.
- If you shift a `char`, `byte`, `short`, it will be promoted to `int` before the shift take place, and the result will be an `int`. If you use `<<=`, `>>=`, or `>>>=`, the result will be truncated (no error).
```
byte b = -1;
b >>>= 10;  // still 11111111 (promoted to int)
Integer.toBinaryString(b);  // will print binary code for -1 (promoted to int)
b = 10<<2;  // ok
int a = 1;
b = a<<2;   // error
```
### Casting Operators
- In Java, casting is safe, with the exception of narrowing conversion. Here the compiler forces you to use a cast. With a widening conversion an explicit cast is not needed.
- Java allows you to cast any primitive type to any other primitive type, except for `boolean`.
- Casting from `float` or `double` to an integral value always truncates the number (towards 0).

### Promotion
- If you perform any mathematical or bitwise operations on primitive data types that are smaller than an `int`, those values will be promoted to `int` before performing the operations.
- In general, the largest data type in an expression is the one that determines the size of the result that expression.
- Compound assignments do not require casts for `char`, `byte`, or `short`, even though they are performing promotions that have the same results as the direct arithmetic operations.
```
byte b = 3;
b += 10000;
```

### sizeof
- Java does not need a `sizeof()` operator, because all the data types are the same size on all machines.

## Controlling Execution

## Iteration
- `static random()` generates a `double` within [0,1). A `Random` object generates integers.
```
double r = Math.random();
java.util.Random random = new Random();
int r2 = random.nextInt(100);
```
- Foreach syntax only works with arrays and objects that are `Iterable`.
- Although `range()` allows the use of the foreach syntax in more places, it is a little less efficient.

## goto
- The only place a label is useful in Java is right before an iteration statement:
```
label1:
outer-iteration{
    inner-iteration{
        break;
        continue;
        continue label1;    // back to label1
        break label1;    // not re-enter label1
    }
}
```
- The only reason to use labels in Java is when you have nested loops and you want to `break` or `continue` through more than one nested level.

## switch
 - For non-integral types, Java SE5's new enum feature are designed to work nicely with `switch`.
 - Cases can be "stacked" on top of each other to provide multiple matches for a particular piece of code.

# Annotation
- Also known as metadata.
- Annotations allow you to store extra information about your program in a format that is tested and verified by the compiler.
- Three general-purpose built-in annotations defined in **java.lang**:
1. `@Override`
2. `@Deprecated`
3. `@SupressWarnings`
- The metadata lets you have the advantage of:
 - Cleaner looking code;
 - Compile-time type checking;
 - The annotation API to help building processing tools.
- Anytime you create descriptor classes or interfaces that involve repetitive work, you can usually use annotations to automate and simplify the process.
- The compiler will ensure that you have a definition for the annotation in your build path.
- Annotations don't support inheritance. Using `getDeclaredAnnotations()` is the only way you can approximate polymorphic behavior:
```
Annotation[] anns = filed.getDeclaredAnnotations();
if(anns[0] instanceof SQLInteger){
    // ...
}
```

## Meta-Annotation
- There are currently four meta-annotation defined in the Java Language. The meta-annotations are for annotating annotations:
1. `@Target`: where it can be applied. `ElementType`;
2. `@Retention`: how long the annotation information is kept. `RetentionPolicy`:
 - `SOURCE`: discarded by the compiler;
 - `CLASS`: available in the class file by the compiler but discarded by the VM;
 - `RUNTIME`: retained by the VM, so may be read reflectively.
3. `@Documented`: include this annotation in the Javadocs;
4. `@Inherited`: allow subclasses to inherit parent annotations.
- You can specify a comma-seperated list of any combination of enum `ElementType` or `RetentionPolicy`.
- Whenever you use an external descriptor file, you end up with two separate sources of information about a class. Using annotation, you can keep all of the information in the source file:
```
// An object/relation mapping example
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Constraints{
    boolean primaryKey() default false;
    boolean allowNull() default true;
    boolean unique() default false;
}
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface SQLString{
    int value() default 0;
    String name() default "";
    Constraints constraints() default @Constraints; // nested annotation
}

@DBTable(name = "MEMBER")
public class Member{
    @SQLString(30) String Name;    // shortcut feature
    @SQLString(value = 30, constraints = @Constraints(primaryKey = true)) String handle;
    // ...
}
```
- When using multiple annotations, you cannot use the same annotation twice.

## Annotation Elements
- Annotation elements look like interface methods, except that you can declare default values:
```
@Target(ElementType.METHOD)
@Retention(TetentionPolicy.RUNTIME)
public @interface UseCase{
    public int id();
    public String description() default "no description";
}
```
Because `id` is type-checked by the compiler, it is a reliable way of linking a tracking database to the use case document and the source code.
```
public class PasswordUtils{
    @UseCase(id = 48)
    public String encryptPassword(String password){}
}
```
- If there is only one element in an annotation, it must be named as `value()`. And you can skip its name and `=` when using this annotation:
```
@NeedTest(true) 
```
- An annotation without any elements is called a marker annotation.
- Allowed types for annotation elements:
 - All primitives;
 - String;
 - Class;
 - enums;
 - Annotations
 - Array of any of the above.
- Note that wrapper classes are not allowed, but because of autoboxing (unboxing) this isn't really a limitation.
- No elements can have an unspecified value. They must either have default values or values provided by the class that uses the annotation.
- None of the non-primitive type elements are allowed to take `null` as a value, neither defined in source code nor as default value. You can get around this by checking for specific values (like "" or -1).

## Annotation Processors
- Java SE5 provides extensions to the reflection API to help you create annotation processors.
```
import java.lang.reflect.*;
public static void trackUseCases(Class<?> cl){
    for(Method m : cl.getDeclaredMethods()){
        UseCase uc = m.getAnnotation(UseCase.class);
        if(uc != null){ // none such annotation returns null
            // ...
        }
    }
}
```

## Annotation Processing Tool
- By default, **apt** compiles the source files when it has finished processing them.
- When your annotation processor creates a new source file, that file is itself checked for annotations in a new round of processing.
- Each annotation you write will need its own processor, but the **apt** tool can easily group several annotation processors together.
- **apt** works by using an `AnnotationProcessorFactory` to create the right kind of annotation processor for each annotation it finds.
- With annotation we can include the unit tests inside the class to be tested. The additional benefit is being able to test `private` methods as easily as `public` ones.
- The easiest way to create non-embedded tests is with inheritance.
- Java assertions normally have to be enabled with the `-ea` flag on the `java` command line, but `@Unit` automatically enables them:
```
assert methodTwo() == 2: "methodTwo must equal 2";
```
- `@Unit` creates an object of the class under test using the default constructor. The test is called for that object, and then the object is discarded to prevent side effects from leaking into other unit tests.
- In JUnit, you need to somehow tell the unit testing tool what it is that you need to test.
- Tests can be called like:
```
public static void main(String[] args) throws Exception{
    OSExecute.command("java TestProcessor ClassToBeTested1 ClassToBeTested2 ...");
}
```


# Array
- A Java array is guaranteed to be initialized and cannot be accessed outside of its range.
- Compiler guarantees initialization of primitives because it zeros the memory for that array.
- Array is also inherited from `Object`.
- Three issues that distinguish arrays from other types of containers:
 - Efficiency: most efficient way to store and randomly access; the cost of speed is that the size is fixed.
 - Type: before generics, arrays are superior to containers because you get compile-time type checking. Although pre-generic containers will also check in the run time, error in compile time is still nicer.
 - Ability to hold primitives: with autoboxing containers can act as if they are able to told primitives: ```
 int[] intergers = {1, 2, 3, 4, 5};
 List<Integer> intList = new ArrayList<Ingeter>(Arrays.asList(1, 2, 3, 4, 5));
 ```
- You should prefer containers to arrays when programming in recent versions of Java. Only when it's proven that performance is an issue (and that switching to an array will make a difference) should you refactor to arrays.
- Regardless of what type of array you're working with, the array identifier is actually a reference to a true object that's created on the heap.
- All arrays have an intrinsic member `length`. `length` can only tell you how many elements can be placed in the array.
- For returning multiple results, languages like C and C++ are difficult to return an array because it becomes messy to control the lifetime of the array. In Java, you just return the array.
- Note that all arrays are considered to implement the interface `clonable`.

## Initialization
- When creating an array of objects, you are really creating an array of references, and each of those references is automatically initialized to `null`.
- There are several ways to initialize an array: 
```
int a1[];    // reference
int a2 = {1,2,3,4,5};    // storage allocation, equivalent of using new
a1 = a2;    // copying reference
a1 = new int[10];    // new works with array of primitives
Integer[] a3 = new Integer[10];    // array of references
Integer[] a4 = {    // aggregate initialization
    new Integer(1),
    new Integer(2),
    3,    // autoboxing; final comma is optional
};
Integer[] a5 = new Integer[]{   // dynamic aggregate initialization
    new Integer(1),
    new Integer(2),
    3,    // autoboxing; final comma is optional
};
```
- For multidimentional arrays:
```
int [][] a = {
    { 1, 2, 3, },
    { 4, 5, 6, },
};
System.out.println(Arrays.deepToString(a)); // turns multidimensional array into String

int [][][] a = new int[2][2][4];    // primitives are automatically initialized

int [][] b = new int[2][];  // ragged array
for(int i=0; i<2; i++)
    b[i] = new int[rand.nextInt(10)];
    
Object[][] objects = {  // non-primitive ragged array
    { new Object(), new Object() },
    { new Object() };
};
```

## Arrays and Generics
- You can parameterize the type of the array itself:
```
class ClassParameter<T>{
    public T[] f(T[] arg) { return arg; }
}
class MethodParameter<T>{
    public static <T> T[] f(T[] arg) { return arg; }
}
```
- In general, arrays and generics do not mix well. Erasure removes the parameter type information, while arrays must know the exact type that they hold.
```
Peel<Banana>[] peels = new Peel<Banana>[10];    // illegal!
```
- However, the compiler will let you create a reference to such an array. You can create an array of the non-generified type and cast it:
```
List<String>[] ls;
List[] la = new List[10];
ls = (List<String>[])la;    // "unchecked" warning
ls[0] = new ArrayList<String>();    // no error
ls[1] = new ArrayList<Integer>();   // compile-time error
Object[] objects = ls;  // List<String> is an Object, so assignment is ok
objects[1] = new ArrayList<Integer>();  // now no error
List<Integer> ilist = (List<Integer>[])new List[10];    // ok but with "unchecked" warning
```
- If you know you're not going to upcast and your needs are relatively simple, it is possible to create an array of generics, which will provide basic compile-time type checking. However, a generic container will virtually always be a better choise.
- You can not create an array of a generic type:
```
public class ArrayOfGenerics<T>{
    T[] array;
    public ArrayOfGenerics(int size){
        array = new T[size];    // illegal! Attempt to create arrays of erased type, thus are unknown types
        array = (T[])new Object[size];  // "unchecked" warning
    }
    public <U> U[] makeArray() { return new U[20]; }    // illegal!
}
```
The "unchecked" warning is caused by the following reason. If I create a `String[]`, Java will enforced at both compile time and run time that I can only place `String` objects in that array. However, if I create an `Object[]`, I can put anything into that array except primitive types.

## Fill an Array
- The Java standard library `Arrays` class has a rather travial `fill()` method. It only duplicate a single value or reference into each location:
```
int[] a = new int[5];
String[] b = new String[5];
Arrays.fill(a, 11);
Arrays.fill(b, "Hello");    // all same reference
Arrays.fill(b, 3, 5, "World");
```

## Data Generators
- If a tool uses a `Generator`, you can produce any kind of data via your choise of `Generator` (an example of Strategy design pattern)
```
// Generator is defined in <Thinking in Java>
public class CountingGenerator{
    // nested in so they may use the same name as the object types they are generating
    // note for the explicit type names
    public static class Integer implements Generator<java.lang.Integer>{
        private int value = 0;
        public java.lang.Integer next() { return value++; }
    }
    public static class String implements Generator<java.lang.String>{
        // ...
    }
    // ...
}
public class GeneratorsTest{
    public static int size = 10;
    public static void test(Class<?> surroundingClass){
        for(Class<?> type : surroundingClass.getClasses()){
            try{
                Generator<?> g = <Generator<?>)type.newInstance();
                for(int i=0; i<size; i++)
                    System.out.printf(g.next() + " ");
                System.out.println();
            }catch(Exception e){
                throw new RuntimeException(e);
            }
        }
    }
    public static void main(String[] args){
        test(CountingGenerator.class);
    }
}
```
- Two ways of producing an array of `Object`:
```
public class Generated{
    // fill an existing array
    public static <T> T[] array(T[] a, Generator<T> gen){
        return new CollectionData<T>(gen, a.length).toArray(a);
    }
    // create a new array
    @SuppressWarnings("unchecked")
    public static <T> T[] array(Class<T> type, Generator<T> gen, int size){
        T[] a = (T[])java.lang.reflect.Array.newInstance(type, size);
        return new CollectionData<T>(gen, size).toArray(a);
    }
}
```
All `Collection` subtypes have a `toArray()` method (all shallow copy).

## Arrays Utilities
- `Arrays` class holds a set of `static` utility methods for arrays.
- `deepEquals()` compare two multidimensional arrays.
- Copying isn't part of `Arrays`. `System.arraycopy()` is overloaded to handle all types. But if you copy arrays of objects, then only then references get copied -- shallow copy.
```
System.arraycopy(source, start1, dest, start2, num);

```
- `equals()` has been **overloaded** to all types.To be equal, the arrays must have the same length, and each element must be equivalent to each corresponding element in the other array, using the `equals()` for each element. For primitives, the wrapper class `equals()` is used.
```
Integer[] s = new Integer[10];
int[] t = new int[10];
Arrays.fill(s, 0);
Arrays.equals(s, t);    // illegal! Not applicable
```
- The Strategy design pattern is used for comparison in sorting. Java has two ways to provide comparison functionality:
1. Ensure the class implementing the `java.lang.Comparable` interface with `int compareTo()` method, which returns positive value if the current object is greater than the argument. Typically `this > input` returns positive value.
2. Create a separate class that implements an interface `Comparator` with `compare()` and `equals()` methods. Since every `Object` has `equals()`, you can just use the default one. Typically `first > second` returns positive value.
```
// reverseOrder() returns a generic Comparator
Arrays.sort(a, Collections.reverseOrder());
Arrays.sort(sa, String.CASE_INSENSITIVE_ORDER);
```
- The sorting algorithm that's used in the Java standard library is designed to be optimal for the particular type -- a Quicksort for primitives, and a stable merge sort for objects.
- Once an array is sorted, you can perform `Arrays.binarySearch()`. On an unsorted array, the result will be unpredictable. You must provide the same `Comparator` as `sort()`.
- It produces a negative value representing the place that the element should be inserted if you are maintaining the sorted array: -(insertion point)-1.
- There is no guarentee which of the duplicate elements will be found.

# Class
- All you do in Java is define classes, make objects of those classes, and send massages to those objects.

## Access Control
- A primary consideration in object-oriented design is to "separate the things that change from the things that stay the same".
- Hiding the implementation reduces program bugs.
- Access control allows the library designer to change the internal workings of the class without worrying about how it will affect the client programmer.
- Wrapping data and methods within classes in combination with implementation hiding is often called encapsulation.

### Access Specifier
- The only way to grant access to a member is to:
 - Make the member `public`.
 - Give the member package access and put the other classes in the same package.
 - Make the member `protected` and let other classes inherit from it.
 - Provide "accessor/mutator" methods.
- Java treats files without `package` keywords as implicitly part of the "default package" for that directory, and thus they provide package access to all the other files **in that directory**.
- The consistent use of `private` is very important, especially where multi-threading is concerned.
- Any `private` methods in a class are implicitly `final`.
- Any method that you're certain is only a "helper" method for that class can be made `private`.
- For clarity, you might put the `public` members at the beginning, followed by the `protected`, package-access, and `private` members.
- `protected` also provides package access.

### Class Access
- There can be only one `public` class per compilation unit. It can have as many supporting package-access classes as you want.
- The name of the `public` class must exactly match the name of the file.
- It is possible, though not typical, to have a compilation unit with no `public` class at all.
- A top-level class cannot be `private` or `protected` (except for inner class). If you don't want anyone else to have access to that class, you can make all the constructor `private`:
```
class Soup{
    private Soup() {}
    private static Soup ps = new Soup();
    ...
}
```
- Putting a `main()` in each class allows easy testing for each class. Even if you have a lot of classes in a program, only the `main()` for the class invoked on the command line will be called. Even if a class has package access, a `public main()` is accessible.

## Constructor
- If you create a class that has no constructors, the compiler will automatically create a default constructor for you. Otherwise, it will not synthesize one.
- The synthesized default constructor is automatically given the same access as the class.
- In a constructor, the `this` keyword can be used to make an explicit call to the constructor that matches the argument list:
```
Flower(String s, int petals){
    this(petals);
    this.s = s;
    ......
}
```
You cannot call two constructors. In addition, the constructor call must be the first thing you do.

- The compiler won't let you call a constructor from inside any method other than a constructor.
- The constructor is actually a `static` method. So to be precise, a class is first loaded when any one of its `static` members is accessed.
- Java automatically inserts calls to the default base-class constructor in the derived-class constructor:
```
class Art{
    Art() {}
}
class Drawing extends Art{
    Drawing() {}    // automatically call Art() first
}
```
- The call to the base-class constructor must be the first thing you do in the derived-class constructor.

## Method
- The act of calling a method is commonly referred to as sending a message to an object.
- There is a secret first argument `this` passed to a non-static method, which produces the reference to the object that the method has been called for.

### Overloading
- For overloading with primitives, if you have a data type that is smaller than the argument in the method, that data type is promoted to the smallest possible type.
- If your argument is wider, then you must perform a narrowing conversion with a cast. Otherwise the compiler will issue an error message.

### Variable Argument List
- Java SE5 added this feature:
```
class A{
    static void foo1(Object[] args){}   // pre-Java SE5 code
    static void foo2(Object... args){}
    public static void main(String[] args){
        foo2(47, 3.14f, 11.11);
        foo2((Object[])new Integer[]{1,2,3,4,5});   // warning if no casting
    }
}
```
- It's possible to pass zero arguments to a vararg list. This is helpful when you have optional trailing arguments:
```
f(1, "one");
f(2, "one", "two");
f(0);
```
- You can use varargs with a specified type other than `Object`.
- Varargs do work in harmony with autoboxing:
```
public class AutoboxingVarargs{
    public static void f(Integer... args){}
    public static void main(String[] args){
        f(1,2,3,4,5);
    }
}
```
- The compiler uses autoboxing to match the overloaded method, and it calls the most specifically matching method. When you call `f()` without arguments, it has no way of knowing which one to call.
- You should generally only use a variable argument list on one version of an overloaded method. Or consider not doing it at all.

## Static
- `static` means that particular field or method is not tied to any particular object instance of that class. You can name it via an object, or refer to it directly through its class name.
- Using the class name is the preferred way to refer to a static variable. Not only does it emphasize that variable's static nature, but in some cases it gives the compiler better opportunities for optimization.
- `static` means that there is no **this** for that particular method. It can only access other static methods and fields.
- Statics are pragmatic. Whether or not they are "proper OOP" should be left to the theoreticians.
- If a field is a `static` primitive and you don't initialize it, it gets the standard initial value for its type. If it is a reference to an object, the default initialization value is `null`.

## final

### Final Data
- A constant is useful for two reasons:
 - It can be compile-time constant that won't ever change. The calculation can be performed at compile time, eliminating some runtime overhead.
 - It can be a value initialized at run time that you don't want changed.
- With a primitive, `final` makes the value a constant; with an object reference (include arrays which is also objects), `final` makes the reference a constant, the object itself can be modified.
- `final static` primitives with constant initial values (as compared to random values) are named with all capitals by convention, with words separated by underscores.
- The blank `final` fields must be initialized before it is used, and the compiler ensures this (either with an expression at the point of definition of the field or in every constructor). They provide much more flexibility since those fields can be different for each object.

### Final Method
- Two reasons for `final` methods:
 - To prevent any inheriting class from changing its meaning.
 - To allow the compiler to turn any calls to that method into inline calls. It is now generally discouraged since recent virtual machine can detect these situations and optimize away the extra indirection.
- Any `private` methods in a class are implicitly `final`.

### Final Classes
- You don't want to inherit from this class or allow anyone else to do so.
- All methods in a `final` class are implicitly `final`.

## Abstract Classes and Methods
- If you create an object of an abstract class, you will get an error message from the compiler.
- It's possible to make a class `abstract` without including any `abstract` methods.
- Abstract classes are also useful refactoring tools, since they allow you to easily move common methods up the inheritance hierarchy.

## Interface
- I believe that an interface should have more meaning than a mechanical duplication of method combinations, so I tend to wait until I see the value added by an interface before creating one.
- The interfaces are completely abstract classes. They are used to establish a "protocol" between classes.
- An interface can also contain fields, but these are implicitly `static` and `final`. So it is a convenient tool for creating groups of constant values:
```
public interface Months{
    int JANUARY=1, FEBRUARY=2, MARCH=3;
}
```
Those fields cannot be "blank finals", but they can be initialized with non-constant expressions.

- The methods in an interface are `public` even if you don't say it. So when you implement an interface, the methods from the interface must be defined as `public`.
- Whenever a method works with a class instead of an interface, you are limited to using that class or its subclasses.
- One of the core reasons for interfaces is to upcast to more than one base type. The other is the same as abstract class, to prevent the client programmer from making an object of this class.
- If it's possible, you should always prefer interfaces to abstract classes.
- An appropriate guideline is to prefer classes to interfaces. If it becomes clear that interfaces are necessary, then refactor.

### "Multiple Inheritance" in Java
- There is no storage associated with an interface -- there's nothing to prevent many interfaces from being combined. But only one of the classes can have an implementation. The concrete class must come first, then the interfaces.
- When you want to create an object, all the definitions must first exist. Even though the derived class does not explicitly provide a definition for some interface, the definition can come along with the base class:
```
interface CanFight{
    void fight();
}
interface CanSwim{
    void swim();
}
class ActionCharacter{
    public void fight() {}
}
class Hero extends ActionCharacter implements CanFight, CanSwim{
    // no fight() and still works
    public void swim() {}
}
```
- An identical method in different interfaces is not a problem. If the method have the same signature but different return types, the compiler will give an error massage.

### Extending an Interface
- You can easily add new method declarations to an interface, and combining several interfaces into one by using inheritance.
- `extends` can refer to multiple base interfaces when building a new **interface**: `interfaceVampire extends DangerousMonster, Lethal { /*...*/ }`

### Nested Interfaces
- Nested interfaces can be `private`. So implementing a `private` interface is a way to force the definition of the methods in that interface without adding any type information (i.e. without allowing any upcasting).
```
class A{
    private interface D { void f(); }
    public class DImp implements D { void f(){}}
    public D getD() { return new DImp(); }
    public void receiveD(D d) {}
}
```
The only thing you can do with a returned private interface reference is:
```
A a = new A();
// A.D d = a.getD(); not allowed
// a.getD().f(); not allowed
a.receiveD(a.getD());
```
- Since all interface elements must be `public`, an interface nested within another interface is automatically `public`.
- When you implement an interface, you are not required to implement any interfaces nested within.

## Reusing
- Composition is often referred to as a "has-a" relationship. If the composition happens dynamically, it's usually called aggregation. You can change the member objects at run time, to dynamically change the behavior of your program. Inheritance does not have this flexibility since the compiler must place compile-time restrictions on classes created with inheritance.
- Extending the interface can be described as an is-like-a relationship.

### Composition Syntax
- You are simply reusing the functionality of the code, not its form.
- Composition is generally used when you want the functionality of an existing class inside your new class, but not its interface. For this effect, you embed `private` objects of existing classes.
- Sometimes it makes sense to make the member objects `public` to allow the class user to directly access the composition of your new class.

### Inheritance Syntax
- You literally take the form of the existing class and add code to it without modifying the existing class.
- Pure substitution (substitution principle): inheritance should override only base-class methods and not add new methods that aren't in the base class (is-a relationship).
- When you inherit, you take an existing class and make a special version of it.
- If you must upcast, then inheritance is necessary.

### Delegation
- Delegation is midway between inheritance and composition. You place a member object in the class you're building, but at the same time you expose all the methods from the member object in you new class.
```
public class SpaceShipControls{
    void control(int velocity) {}
}
public class SpaceShipDelegation{
    private SpaceShipControls ssc = new SpaceShipControls();
    // Delegated method
    public void control(int velocity){
        ssc.control(velocity);
    }
}
```
A SpaceShip isn't really "a type of" SpaceShipControls. It's more accurate to say that a SpaceShip contains SpaceShipControls.

- Although the Java language doesn't support delegationg, development tools often do.

## Inheritance
- In overridden methods, Java's `super` keyword can be used to call the base-class version.
- Use inheritance to express differences in behavior, and fields to express variations in state.

### Overloading and Name Hiding
- Unlike C++, redefining the method name in the derived class will not hide any of the base-class versions. Thus overloading works regardless of whether the method was defined at this level or in a base class.

### Overriding
- `@override` annotation will make the compiler produce an error message if you accidentally overload instead of overriding.
- Overriding can only occur if something is part of the base-class interface. You can define a method with the same signature as a private method in the base class, but it is not overriding.
- Java SE5 includes covariant return types:
```
interface OrdinaryGetter{
    Base get();
}
interface DerivedGetter extends OrdinaryGetter{
    Derived get();  // return type of overridden method is allowed to vary
}
```

## Polymorphism
- It provides another dimension of separation of interface from implementation, to decouple what from how. It deals with decoupling gin terms of types. Also called dynamic binding or late binding or runtime binding.
- When a language implements late binding, there must be some mechanism to determine the type of the object at run time and to call the appropriate method.
- All method binding in Java uses late binding unless the method is `static` or `final` (including `private`).
- In a well-designed OOP program, most of all of your methods will communicate only with the base-class interface. Such a program is extensible.
- Even if the client methods is in a separate file and new methods are added to the interface of the library base class, it will still work correctly even without recompiling.
- "Overriding" `private` methods or accessing a field directly will not gain polymorphism.
- `static` method doesn't behave polymorphically. It is not associated with the individual objects.
- Java SE5 adds covariant return types, which means that an overridden method in a derived class can return a type derived from the type returned by the base-class method.

### Polymorphic Methods inside Constructors
- It calls a method in a derived class (even when it's in the base-class constructor). If you do this inside a constructor, you can call a method that might manipulate members that haven't been initialized yet (those defined in the derived class). C++ produces more rational behavior in this situation.
- The only safe methods to call inside a constructor are those that are `final` in the base class (including `private`).

### Extension
- Unlike pure substitution, extension shows an "is-like-a" relationship. It has the same fundamental interface -- but it has other features that require additional methods to implement.
- Often you'll get into a situation in which you need to rediscover the exact type of the object so you can access the extended methods of that type, using RTTI.

## Inner Class
- Inner classes are distinctly different from composition. It knows about and can communicate with the surrounding class. An object of the inner class has a link to the enclosing object that made it --- without any special qualifications.
- Typically, an outer class will have a method that returns a reference to an inner class. If you want to make an object of the inner class any where except from within a non-static method of the outer class, you must specify the type of that object as `OuterClass.InnerClass`.
- The most compelling reason for inner classes is: each inner class can independently inherit from an implementation. Thus, the inner class is not limited by whether the outer class is already inheriting from an implementation. That is, inner classes effectively allow you to inherit from more than one non-interface:
```
class D{}
abstract class E{}
class Z extends D{
    E makeE() { return new E(){}; }
}
```
- Additional features:
1. The inner class can have multiple instances, each with its own state information that is independent of the outer-class object.
2. A single outer class can have several inner classes, each of which implements the same interface or inherits from the same class in a different way.
3. The point of creation of the inner-class object is not tied to the creation of the outer-class object.
4. There is no potentially confusing "is-a" relationship with the inner class.
- The inner classes also produce **.class** files to contain the informationi for their `Class` objects (Outer$Inner.class). If inner classes are anonymous, the compiler simply starts generating numbers as inner-class identifiers. Be care that $ is a meta-character to the Unix shell.

### .this and .new
- The inner class secretly captures a reference to the particular object of the enclosing class that was responsible for creating it. An object of an inner class can be created only in association with an object of the enclosing class when the inner class is non-static.
- A plain "this" would be Inner's this:
```
public class Outer{
    public class Inner{
        public Outer outer() { return Outer.this; };    // reference to the outer-class object
    }
}
```
- To create an object of the inner class directly, you must use an object of the outer class (or make the inner class `static`):
```
public class Outer{
    public class Inner{}
    public static void main(String[] args){
        Outer outer = new Outer();
        Outer.Inner inner = outer.new Inner();  // no need (and cannot) to say outer.new Outer.Inner()
    }
}
```
- If you make a nested class (a static inner class), then it doesn't need a reference to the outer-class object.

### Upcasting
- Normal (non-inner) classes cannot be made `private` or `protected`.
- When you get a reference to the base class or the interface, it's possible that you can't evne find out the exact type:
```
class Outer{
    // Only referenced as Interface outside this class
    private class Inner implements Interface{}
}
```
- The `private` inner class provides a way for the class designer to completely prevent any type-coding dependencies and to completely hide details about implementation. In addition, extension is useless form the client programmer's perspective, providing an opportunity for Java compiler to generate more efficient code.

### Inner Class in Method and Scope
- Two reasons for doing this:
1. You are implementing an interface of some kind so that you can create and return a reference.
2. You want to create a class to aid in your solution, but you don't want it publicly available.
- Local inner class within the scope of a method:
```
public class Parcel{
    public Destination destination(String s){
        class PDestination implements Destination{
            private String label;
            private PDestination(String whereTo){
                label = whereTo;
            }
            public String readLabel(){ return label; }
        }
        return new PDestination(s);
    }
}
```
- Local inner class within any arbitrary scope:
```
public class Parcel{
    private void internalTracking(boolean b){
        if(b){
            class TrackingSlip{ // not created conditionally, always get compiled
                private String id;
                TrackingSlip(String s){
                    id = s;
                }
                String getSlip() { return id; }
            }
            TrackingSlip ts = new TrackingSlip("slip");
            String s = ts.getSlip();
        }
        // Can't use it here! Out of scope
    }
}
```
- It doesn't matter how deeply an inner class may be nested --- it can transparently access all of the members of all the classes it is nested within:
```
class MNA{
    private void f() {}
    class A{
        private void g() {}
        public class B{
            void h(){
                g();
                f();
            }
        }
    }
}
```

### Anonymous Inner Classes
```
public class Parcel{
    public Contents contents(){
        return new Contents(){
            private int i = 11;
            public int value() { return i; }
        };  // Semicolon required in this case
    }
}
```
The syntax means to create an object of an anonymous class that's inherited from `Contents`. The reference returned is automatically upcast to a `Contents` reference.

- If you're defining an anonymous inner class and want to use an object that's defined outside the anonymous inner class, the compiler requires that the argument reference be `final`. If you forget, you'll get a compile-time error.
- You can't have a named constructor in an anonymous class (since there's no name), but with instance initialization, you can, in effect, create a constructor for an anonymous inner class (but can't be overloaded of cource):
```
pubic class Parcel{
    public Destination destination(final String dest, float price){
        return new Destination(price){  // used in the base constructor, not in the anonymous class, so don't have to be final
            private String label;
            // Instance initialization for each object
            {
                label = dest;
            }
            public String readLabel() { return label; }
        }
    }
}
```
- Anonymous inner classes are somewhat limited compared to regular inheritance, because they can either extend a class or implement an interface, but not both. And if you do implement an interface, you can only implement one.

### Nested Classes
- You don't need an outer-class object in order to create an object of a nested class, and you can't access a non-static outer-class object from an object of a nested class.
- Ordinary inner classes cannot have static data, static fields, or nested classes. However, nested classes can have all of these.
- A nested class can b epart of an interface. Any class you put inside an interface is automatically `public` and `static`. You can even implement the surrounding interface in the inner class. It's convenient when you want to create some common code to by used with all different implementations of that interface.
- You can use a nested class to hold your test code:
```
public class TestBed{
    public void f(){ System.out.println("f()"); }
    public static class Tester{
        public static void main(String[] args){
            TestBed t = new TestBed();
            t.f();
        }
    }
}
```
You can simply delete TestBed$Tester.class before packaging things up to exclude it in your shipping product.

### Closures & Callbacks
- A closure is a callable object that retains information from the scope in which it was created. It has permission to manipulate all the members, even `private` ones.
- With a callback, some other object is given a piece of information that allows it to call back into the originating object at some later point. The closure provided by the inner class is a good solution --- more flexible and far safer than a pointer.
```
interface Incrementable{
    void increment();
}
class MyIncrement{
    public void increment() {};
}
class Callee extends MyIncrement{
    public void increment(){    // cannot be overriden for use by Incrementable
        super.increment();
    }
    // make Callee an Incrementable, provide a safe hook back into Callee
    private class Closure implements Incrementable{
        public void increment(){
            Callee2.this.increment();
        }
    }
    Incrementable getCallbackReference(){
        return new Closure();
    }
}
```
- The value of the callback is in its flexibility; you can dynamically decide what methods will be called at run time.

### Inner Classes & Control Frameworks
- An application framework is a class or a set of classes that's designed to solve a particular type of problem. This is an example of the Template Method design pattern. It calls one or more overrideable methods to complete the action of that algorithm.
- A control framework is a particular type of application framework dominated by the need to respond to events (event-driven system, e.g. graphical user interface).
- A control framework may contain no specific information about what it's controlling. That information is supplied during inheritance:
```
public abstract class Event{
    private long eventTime;
    protected final long delayTime;
    public Event(long delayTime){
        this.delayTime = delayTime;
        start();
    }
    public void start(){
        eventTime = System.nanoTime()+delayTime;
    }
    public boolean ready(){
        return System.nanoTime()>=eventTime;
    }
    public abstract void action();
}
```
- Inner classes allow you to have multiple derived versioins of the same base class within a single class:
```
public class GreenhouseControls extends Controller{
    private boolean light = false; // will need two references if LightOn and LightOff are not inner class
    public class LightOn extends Event{ /* ... */ }
    public class LightOff extends Event{ /* ... */ }
}
```

### Inheriting from Inner Classes
- The "secret" reference to the enclosing class object must be initialized, and yet in the derived class there's no longer a default object to attach to:
```
class WithInner{
    class Inner{}
}
public class InheritInner extends WithInner.Inner{
    // InheritInner(){} // Won't compile
    InheritInner(WithInner wi){
        wi.super(); // provides the necessary reference
    }
}
```
- The inheriting inner class with the same name as the inherited one will not override it. The two inner classes are completely separate entities, each in its own namespace. But it's still possible to explicitly inherit from the inner class:
```
class Egg{
    protected class Yolk{
        public Yolk() {System.out.println("Egg.Yolk()");}
        public void f() {System.out.println("Egg.Yolk.f()");}
    }
    private Yolk y = new Yolk();
    public Egg() {System.out.println("New Egg()");}
    public void insertYolk(Yolk yy) {y = yy;}
    public void g() {y.f();}
}
public class BigEgg extends Egg{
    public class Yolk extends Egg.Yolk{
        public Yolk() {System.out.println("BigEgg.Yolk()");}
        public void f() {System.out.println("BigEgg.Yolk.f()");}    // override
    }
    public BigEgg() {insertYolk(new Yolk());}
    public static void main(String[] args){
        Egg e = new BigEgg();
        e.g();
    }
}
```
will output:
```
Egg.Yolk()
New Egg()
Egg.Yolk()
BigEgg.Yolk()
BigEgg.Yolk.f()
```

### Local Inner Classes
- A local inner class cannot have an access specifier because it isn't part of the outer class, but it does have access to the final variables int the current code block and all the members of the enclosing class (same as anonymous inner class).
```
public class Outer{
    private int count = 0;
    Counter getCounter(final String name){
        class LocalCounter implements Counter{
            public LocalCounter() {}    // can have a constructor
            public int next(){
                System.out.println(name);   // access local final
                return count++; // access members of the enclosing class
            }
        }
        return new LocalCounter();
    }
}
```
- Since the name of the local inner class is not accessible outside, the only justification for using a local inner class instead of an anonymous inner class:
1. You need a named constructor and/or an overloaded constructor.
2. You need to make more than one object of that class.

# Concurrency
- Concurrency is arguably deterministic but effectively nondeterministic. You may not be able to write test code that will generate failure conditions for your concurrent program.
- One of the convenient features of concurrency at the language level is that the programmer doesn't need to worry about whether there are many processors or just one.
- Java is a multithreaded language, and concurrency issues are present whether you are aware of them or not. The problems that you solve with concurrency can be roughly classified as "speed" and "design manageability".
- Concurrency can often improve the performance of programs running on a single processor.
 - From a performance standpoint it makes no sense to use concurrency on a single-processor machine unless one of the tasks might block.
 - Event-driven programming: without concurrency, the only way to produce a responsive user interface is for all tasks to periodically check for user input. By creating a separate thread of execution to respond to user input, even though this thread will be blocked most of the time, the protgram guarantees a certain level of responsiveness.
- Processes are very attractive because the operating system usually isolates one process from another so they cannot interfaere with each other, which makes programming with processes relatively easy. In functional languages, each function call produces no side effects and can thus be driven as an independent task.
- The fundamental difficulty in writing multithreaded programs is coordinating the use of share resources between different thread-driven tasks. Threading creates tasks within the single process. One advantage that this provided was operating system transparency (for early systems that did not support multitasking).
- The design of your program (like simulations) can be greatly simplified. They generally imvolve many interacting elements, each with "a mind of its own".
- Java's threading is preemptive, which means that a scheduling mechanism provides time slices for each thread. So you can generally assume that you will not have enough threads available to provice one for each element in a large simulation. A typical approach is the use of cooperative multithreading.
- In general, threads enable you to create a more loosely coupled design.

## Basic Threading
- 

# Containers
- A container will expand itself whenever necessary to accommodate everything you place inside it.
- The basic types of `Collection` are `List`, `Set`, `Queue`, `Map`.
- In pre-Java SE5, the compiler allowed you to insert an different type into a container.
- Normally, the Java compiler will give you a warning if you do not use generics. Annotation `@SuppressWarnings(unchecked)` will suppress the warning. Note that this is placed on the method taht generates the warning, rather than the entire class.
- If you do not explicitly say what class you are inheriting from, you automatically inherit from `Object`. When you use `get()`, you will get an `Object`.
- You cannot use primitives with containers.
- ![Container Taxonomy](http://www.linuxtopia.org/online_books/programming_books/thinking_in_java/TIJ325.png "Container Taxonomy")
The solid arraws show that a class can produce objects of the class the arrow is pointing to. The long-dashed boxes represent `abstract` classes.
- There is no need to use the legacy classes `Vector`, `Hashtable`, `Stack` in new code.
- `Collections.shuffle()` method does not affect the original array, but only shuffles the references. This is only true because the `randomized()` method wraps an `ArrayList` around the result of `Arrays.asList()`:
```
Random rand = new Random(47);
Integer[] arr = {1, 2, 3, 4, 5};
List<Integer> list1 = new ArrayList<Integer>(Arrays.asList(arr));
Collections.shuffle(list1, rand);    // list1: randomized, arr: in order
List<Integer> list2 = Arrays.asList(arr);
Collections.shuffle(list2, rand);    // both randomized (in the same order)
```
`Arrays.asList()` produces a `List` object that uses the underlying array as its physical implementation.

## Collection
- The `Collection` interface generalizes the idea of a sequence -- a way of holding a group of objects.
- Different interfaces act differently on the same method. e.g. `add()` in `List` and `Set`.
- `Arrays` cannot be resized, thus no `add()` or `delete()` method. If you use them, you will get an "Unsupported Operation" error at run time.
- All `Collection`s can be traversed using the foreach syntax.
- There is no `get()` method in `Collection`, since it also includes `Set` which maintains its own internal ordering.

### Adding Groups of Elements
```
Collection<Integer> collection = new ArrayList<Integer>(Arrays.asList(1,2,3,4,5));
Interger[] moreInts = {6,7,8,9,10};
collection.addAll(Arrays.asList(moreInts));
// Following methods run much faster, but need a Collection object before.
Collection.addAll(collection,11,12,13,14,15);
Collection.addAll(collection,moreInts);
```
- `Arrays.asList()` takes a best guess about the resulting type of the `List`, and may cause a problem:
```
// Won't compile, return a type of List<Derived1>
// List<Base> obj = Arrays.asList(new Derived1(), new Derived2());
// Explicit type argument specification
List<Base> obj = Arrays.<Base>asList(new Derived1(), new Derived2());
```
`Collections.addAll()` works fine because it knows from the first argument what the target type is. The behavior of this convenience method is identical to that of `c.addAll(Arrays.asList(elements))`, but this method is likely to run significantly faster under most implementations.

- Maps are more complex. The Java standard library does not provide any way to automatically initialize them, except from the contents of another map.
- The `fill()` just duplicates a single object reference throughout the container, and only works for `List` objects. But the resulting list can be passed to a constructor or to an `addAll()` method, which is part of every `Collection` subtype:
```
List<Integer> list = new ArrayList<Integer>(Collections.nCopies(4, 1));
Collections.fill(list, 2);
```

#### A Generator Solution
- Virtually all `Collection` subtypes have a constructor that takes another `Collection` object, from which it can fill the new container.
```
public class CollectionData<T> extends ArrayList<T>{
    public CollectionData(Generator<T> gen, int quantity){
        for(int i=0; i<quantity; i++)
            add(gen.next());
    }
    public static <T> CollectionData<T> list(Generator<T> gen, int quantity){
        return new CollectionData<T>(gen, quantity);
    }
}
```
The resulting container can then be passed to the constructor for any `Collection`. `addAll()` can also be used to populate an existing `Collection`. This class is an example of Adapter design pattern; it adapts a `Generator` to the constructor for a `Collection`.
- `Map` generator requires a `Pair` class since a pair of objects must be produced by each call to `next()`:
```
public class Pair<K,V>{
    public final K key;
    public final V value;
    public Pair(K k, V v){
        key = k;
        value = v;
    }
}
```
The key and value fields are made `public` and `final` so that Pair becomes a read-only Data Transfer Object (or Messenger).

### Optional Operations
- The methods that perform various kinds of addition and removal are optional operations in the `Collection` interface. If not implemented, calling these methods will not perform meaningful behavior, but throw exceptions instead.
- It appears that compile-time type safety is discarded, but is not quite that bad. The compiler still restricts you to calling only the methods in that interface. It's not like a dynamic language, in which you can call any method for any object, and find out at run time whether a partivular call will work.
- The unsupported operation prevents an explosion of interfaces in the design. However:
1. `UnsupportedOperationException` must be a rare event.
2. There should be reasonable likelihood that it will appear at implementation time, rather than after you've shipped the product to the customer.
- Note that unsupported operations are only detectable at run time, and therefore represent dynamic type checking.
- `Arrays.asList()` produces a `List` that is backed by a fixed-size array. You can always pass the result of it as a constructor argument to any `Collection`, but the only supported operations are the ones that don't change the size of the array.
- The "unmodifiable" methods in the `Colletions` class wrap the container in a proxy that produces an `UnsopportedOperationException` to produce a constant container object.
- `Arrays.asList()` returns a fixed-sized `List`, whereas `Collections.unmodifiableList()` produces a list that cannot be changed.
- The documentation for a method that takes a container as an argument should specify which of the optional methods must be implemented.

## Iterator
- An iterator is an object whose job is to move through a sequence and select each object in that sequence withou the client programmer knowing or caring about the underlying structure of that sequence.
- The Java iterator can move in only one direction.
```
Iterator<Pet> it = pets.iterator();
while(it.hasNext()){
    Pet p = it.next();     // return, then move
    // ...
}
for(Pet p : pets)    // can read and write, but cannot modify the container object itself
    // ...
```
- An `Iterator` will alse remove the last element produced by `next()`, which means you must call `next()` before you call `remove()`. `remove()` is "optional" method. Not all `Iterator` implementation must implement it.
- Iterator separate the operation of traversing a sequence from the underlying structure of that sequence. We sometimes say that iterators unify access to containers.
- `Iterator` needs `next()` to point to the next element after `add()` and `remove()` (`add()` only for `ListIterator`):
```
ListIterator<String> it = a.listIterator();
it.add("xxx");
it.next();  // move to an element after "xxx"
it.remove();
it.next();  // must move to an element after remove()
it.set("xxx");
```

### ListIterator
- `Iterator` can only move forward, `ListIterator` is bidirectional. You can replace the last element it visited, and create one that starts out pointing to an index **n**;
```
while(it.hasNext()){
    // previousIndex() returns the current index
    System.out.println(it.next()+", "+it.nextIndex()+", "+it.previousIndex()+"; ");
}
```

### Adapter Method
- To add one or more new ways to use a iterable class in a foreach statement.
```
class ReversibleArrayList<T> extends ArrayList<T>{
    public ReversibleArrayList(Collection<T> c) { super(c) };
    public Iterable<T> reversed(){
        return new Iterable<T>(){
            public Iterator<T> iterator(){
                return new Iterator<T>(){
                    int current = size()-1;
                    public boolean hasNext() { return current > -1; }
                    public T next() { return get(current--); }
                    public void remove() {    // Not Implemented
                        throw new UnsupportedOperationException();
                    }
                };
            }
        };
    }

    public static void main(String[] args){
        .....
        for(String s : ral)
        ...
        for(String s : ral.reversed())
        ...
    }
}
```

### Relation with Collection
- The Standard C++ Library has no common base class for its containers -- all commonality between containers is achieved through iterators. In Java, the two approaches are bound together, since implementing `Collection` also means prividing an `iterator()` method. `Collection` is iterable.
- The use of `Iterator` becomes compelling when you implement a non-Collection class, in which it would be difficult or annoying to implement all `Collection` interface.
- The foreach syntax also works with any collection object. The reason it works is that Java SE5 introduced a new interface called `Iterable`, which contains an `iterator()` method and is what foreach uses to move through a sequence.
- `Map`s have not been made `Iterable`.
- A foreach statement works with an array or anything `Iterable`, but that doesn't mean that an array is automatically an `Iterable`. (can use `Arrays.asList(...)` to convert).
```
public class IterableClass implements Iterable<T>{
    protected T[] arr = ......
    public Iterator<T> iterator(){
        return new Iterator<T>(){
            private int index = 0;
            public boolean hasNext(){
                return index < arr.length;
            }
            public T next() { return arr[index++]; }
            public void remove() {    // Not Implemented
                throw new UnsupportedOperationException();
            }
        };
    }
}
```

## List
- When deciding whether an element is part of a `List`, discovering the index of an element, and removing an element from a `List` by reference, the `equals()` method is used, which by default acts like `==` if is not overriden.
- `toArray()` is overloaded. The no-argument version returns an arrays of `Object`. If you pass an array to the overloaded version, it will produce an array of the target type. If the argument array is too small, it will create a new array.
- The tagging interface `java.util.RandomAccess` is attached to `ArrayList` but not to `LinkedList`.
- A `List` backed by an array (`Arrays.asList()`) is slightly faster than an `ArrayList`.
- The best approach is probably to choose an `ArrayList` as your default and to change to a `LinkedList` if you need its extra functionality or you discover performance problems due to many insertions and removals from the middle of the list. If you are working with a fixed-sized group of elements, either use a `List` backed by an array, or if necessary, an actual array.
- `CopyOnWriteArrayList` is a special implementation of `List` used in concurrent programming.

### ArrayList
- `ArrayList` excels at randomly accessing elements.

### LinkedList
- `LinkedList` provides faster insertions and deletions from the middle, and has a larger feature set.
- `LinkedList` adds methods that allow it to be used as a stack, a `Queue` or a deque.
- `removeFirst()` and `remove()` throws `NoSuchElementException` for an empty list, while `poll()` returns `null` if so.

#### Stack
- Inheritance is inappropriate because it would produce a class with all methods.
```
public class Stack<T>{
    private LinkedList<T> storage = new LinkedList<T>();
    public void push(T v) { storage.addFirst(v); }
    public T peek() { storage.getFirst(v); }
    public T pop() { return storage.removeFirst(); }
    public boolean empty() { return storage.isEmpty(); }
    public String toString() { return storage.toString(); }
}
```
- `java.util.Stack` exists, but `LinkedList` produces a better stack. Be careful for the name collision.

## Queues
- Queues are especially important in concurrent programming.
- `LinkedList` implements the `Queue` interface: `peek()`, `remove()`, and `offer()`;
- Concurrency-based `Queue`s are: `ArrayBlockingQueue`, `ConcurrentLinkedQueue`, `LinkedBlockingQueue`, `PriorityBlockingQueue`
- There is no explicit interface for a deque in the Java, but `LinkedList` supports deque operations. You can create a `Deque` class using composition, and simply expose the relevant methods.

### PriorityQueue
- The default sorting uses the natural order of the objects in the queue, but you can modify the order by providing your own `Comparator` or implementing `Comparable`. The `PriorityQueue` ensures that when you call `peek()`, `poll()`, or `remove()`, the element you get will be the one with the highest priority.
```
priorityQueue = new PriorityQueue<Integer>(N, Collections.reverseOrder());
```
```
class ToDoList extends PriorityQueue<ToDoList.ToDoItem>{
    static class ToDoItem implements Comparable<ToDoItem>{
        private char primary;
        private String item;
        public ToDoItem(String td, char pri){
            primary = pri;
            item = td;
        }
        public int compareTo(ToDoItem arg){
            if(primary > arg.primary)
                return +1;
            if(primary == arg.primary)
                return 0;
            return -1;
        }
    }
}
```
- Priority queue typically sort on insertion (maintaining a heap), but they may also perform the selection upon removal (find the smallest one during each `remove()`). The choice of algorithm could be important if priority can change while waiting in the queue.

## Set
- `Set` has the same interface as `Collection`.
- Elements added to a `Set` must at least define `equals()`. For good programming style, you should always override `hashCode()` when you override `equals()`.
```
class SetType{
    int i;
    public SetType(int n) { i = n; }
    public boolean equals(Object o){
        return o instanceof SetType && (i==((SetType)o).i);
    }
    public int hashCode() { return i; }
    public String toString(){
        return Integer.toString(i);
    }
}
```
- The `get()` method of `Set` takes `Object` rather than generic type parameter because, in some cases, `equals()` can allow objects from diffenrent inheritance hierarchy to be equal.
- Iteration is usually faster with a `TreeSet` than a `HashSet`.
- `LinkedHashSet` is more expensive for insertions than `HashSet`.

### TreeSet
- `TreeSet` keeps elements sorted into a red-black tree data structure, whereas `HashSet` uses the hashing function. 
- Elemnts must also implement the `Comparable` interface.
```
class TreeType extends SetType implements Comparable<TreeType>{
    pubilc TreeType(int n) { super(n); }
    public int compareTo(TreeType arg){
        return (arg.i<i ? -1 : (arg.i==i ? 0 : 1));
    }
}
```
- In `compareTo()`, `return i-i2` cannot work. It only works properly with unsigned integer. If both are large positive integer, it will overflow.
- `Comparator` is an object that establishes order.
```
Set<String> set = TreeSet<String>(String.CASE_INSENSITIVE_ORDER);
Collections.addAll(set, "A B C D E F G".split(" "));
System.out.println(set.contails("d"));
```

### SortedSet
- The elements are guaranteed to be in sorted order, not insertion order:
```
SortedSet<String> sortedSet = new TreeSet<String>();
Collections.addAll(sortedSet, "one two three four five six seven eight".split(" "));
String low = "one";
String high = "six";
SortedSet mid = sortedSet.subSet(low, high);    // a view of [one,seven]
SortedSet head = sortedSet.headSet(low);   // a view of [eight,five,four]
SortedSet tail = sortedSet.tailSet(high);   // a view of [six,three,two]
```
Changes of `mid` or `head` or `tail` will be reflected in the original `sortedSet`.

### HashSet
- Lookup is typically the most important operation for a `Set`, so you'll usually choose a `HashSet` implementation, which is optimized for rapid lookup.
- Elements must also define `hashCode()`. If not, there is not even a runtime error. The only reliable way to ensure the correctness of such a program is to incorporate unit tests.

### LinkedHashSet
- `LinkedHashSet` also uses hashing for lookup speed, but appears to maintain elements in insertion order using a linked set.

## Map
- You can test a `Map` to see if it contains a key or a value with `containsKey()` and `containsValue()`.
- Looking for nonexistent key in a map returns `null`.
- It is quit easy to combine containers to quickly produce powerful data structures, like `Map<Person, List<Pet>>`.
- The standard Java library contains: `HashMap`, `TreeMap`, `LinkedHashMap`, `WeakHashMap`, `ConcurrentHashMap`, and `IdentityHashMap`
- Any key must have an `equals()` method.
- The `keySet()` method produces a `Set` backed by the keys in the `Map`, and `entrySet()` will provide a view into the `Map`. Any changes in such kind of returned `Collection` will be reflected in the associated `Map`. When making your own `Map`, the correct implementation of those methods should not make copies of the keys and values.
- If you want to make your own type of `Map`, you must also define an implementation of `Map.Entry`:
```
public class MapEntry<K,V> implements Map.Entry<K,V>{
    private K key;
    private V value;
    ...
    public boolean equals(Object o){
        if(!(o instanceof MapEntry)) return false;
        MapEntry me = (MapEntry)o;
        return (key==null ? me.getKey()==null : key.equals(me.getKey())) &&
               (value==null ? me.getValue()==null " value.equals(me.getValue()));
    }
}
```
Note that the `equals()` method in `MapEntry` must check both keys and values. `hashCode()` should also combine the value of both fields.

### TreeMap
- `TreeMap` is the only available `SortedMap`. It has Extra `firstKey()`, `lastKey()`, `subMap()`, `headMap()`, `tailMap()`.
- The only `Map` with the `subMap()` method, which allows you to return a portion of the tree.
- You can call `keySet()` to get a set view of the keys, then `toArray()` to produce an array of those keys. You can then use the `static` method `Arrays.binarySearch()` to rapidly find objects in your sorted array.

### HashMap
- `HashMap` can take `null` as key while `TreeMap` cannot.
- Performance can be adjusted via constructors that allow you to set the capacity and load factor of the hash table.
- `Object`'s `hashCode()` method by default just uses the address of its object.
- An appropriate override for `hashCode()` only won't work until you override the `equals()`. A proper `equals()` must satisfy the following five conditions:
1. Reflective: `x.equals(x)` should return `true`
2. Symmetric: `x.equals(y)` returns `true` iff `y.equals(x)` returns `true`
3. Transitive: if `x.equals(y)` returns `true` and `y.equals(z)` returns `true`, then `x.equals(z)` should return `true`
4. Consistent
5. For any non-null key, `x.equals(null)` should return `false`
- The default `Object.equals()` simply compares object addresses.
- The `hashCode()` is not required to return a unique identifier, but the `equals()` method must strictly determine whether two objects are equivalent.
- When overriding `equals()`, `instanceof` actually quietly does a second sanity check to see if the object is `null`:
```
public boolean equals(Object o){
    return o instanceof ClassType &&
        (field == ((ClassType)o).field);
}
```
- Perfect hashing function (no collision) is implemented in Java SE5 `EnumMap` and `EnumSet`, because an `enum` defines a fixed number of instances.
- A prime number is not actually the ideal size for hash buckets, and recent implementations in Java use a power-of-two size (after extensive testing). Division or remainder is the slowest operation on a modern processor.
- You don't control the creation of the actual value that's used to index into the array of buckets when overriding `hashCode()`. The returned value will be further processed in order to create the bucket index.
- If your `hashCode()` depends on mutable data in the object, the user must be made aware that changing the data will produce a different key. In addition, you will probably not want to generate a `hashCode()` that is based on unique object information (e.g. using the value of `this`).
```
String[] hellos = "Hello Hello".split(" ");
System.out.println(hellos[0].hashCode()==hellos[1].hashCode()); // true
```
- You should lean toward speed reather than uniqueness --- but between `hashCode()` and `equals()`, the identity of the object must be completely resolved.
- Basic recipe for generating a decent `hashCode()` in *Effective Java Programming Language Guide*:
1. Store some constant nonzero value, say 17, in `result`
2. For each significant field `f` calculate an `int` hash code:
| Filed Type | Calculation |
| - | - |
|`boolean`|`c=(f?0:1)`|
|`byte`,`char`,`short`,`int`|`c=(int)f`|
|`long`|`c=(int)(f^(f>>>32))`|
|`float`|`c=Float.floatToIntBits(f)`|
|`double`|`long l=Double.doubleToLongBits(f); c=(int)(l^(l>>>32))`|
|`Object`|`c=f.hashCode()`|
|Array|Apply above rules to each element|
3. Combine the hash code(s): `result = 37*result+c`;
4. return `result`;
5. make sure that equal instances have equal hash codes.
- When the specified load factor is reached, the container will automatically increase the capacity and rehash. Thus creating it with an appoapriately large initial capacity will prevent the overhead of automatic rehashing.
- A lightly loaded table will have few collisions and so is optimal for insertions and lookups (but will slow down the process of traversing with an iterator). The default load factor used by `HashMap` is 0.75.

### LinkedHashMap
- When you iterate through it, you get the pairs in insertion order, or in least-recently-used order. The LRU algorithm allows easy creation of programs that do periodic cleanup in order to save space.
```
LinkedHashMap<Integer,String> linkedMap = new LinkedHashMap<Integer,String>(16, 0.75f, true);   // initial capacity, load factor, access order
```
- Only slightly slower than a `HashMap`, except when iterating, where it is faster due to the linked list used to maintain the internal ordering.

### Other Maps
- If no references to a particular key are held outside the `WeakHashMap`, that key may be garbage collected.
- `ConcurrentHashMap` does not involve synchronization locking.
- `IdentityHashMap` uses `==` instead of `equals()` to compare keys. With the increasing of size, the cost of insertion grows slower than other maps.

## Evaluation
- When evaluating different containers, remember that `System.nanoTime()` typically produces values with a granularity that is greater than one.
- Tests should be narrowed as mush as possible. You should ensure your tests run long enough, and take into account that some of the Java HotSpot technologies will only kick in when a program runs for a certain time.
- A profiler may do a better job of performance analysis than you can.

## Utilities
- There are a number of standalone utilities for containers, expressed as `static` methods inside the `java.util.Collections` class.
- `reverseOrder()` returns a `Comparator` that reverses the natural ordering.
- 'rotate()` moves all elements forward by `distance` and take the ones off the end and place them at the beginning.
- `emptyList()`, `emptyMap()`, and `emptySet()` returns an immutable empty container. These are generic, so the result will be parameterized to the desired type.
- If you sort using a `Comparator`, you must `binarySearch()` using the same `Comparator`.
- `singleton(T x)`, `singletonList(T x)`, and `singletonMap(K key, V value)` produces an immutable container with a single entry.
- `copy(List<? super T>, List<? extends T>)` won't erase the extra entries if `dest` is longer than `src`. Note that constructors like `new ArrayList(int)` indicate initial capacity, the size is still 0. It will probably throws an `IndexOutOfBoundException` when used as `dest`.
- `new ArrayList(a)` and `Collections.copy(b, a)` only do a shallow copy. The difference is, that the constructor allocates new memory, and `copy(...)` does not,
- You can pass the original container into a method that hands back a read-only version:
```
Set<String> c = Collections.unmodifiableSortedSet(new TreeSet<String>(data));
System.out.println(c);  // reading is OK
c.add("one");   // changing throws UnsupportedOperationException
```
- Unmodifiable containers allows you to keep it as `private` within a class and to return a read-only reference to that container from a method call, while keeping the modifiable reference within the class to enable changes.

## Synchronizing
- The `Collections` class contains a way to automatically synchronize an entire container.
```
Collection<String> c = Collections.synchronizedCollection(new ArrayList<String>());
Set<String> s = Collections.synchronizedSortedSet(new TreeSet<String>());
```
- It is best to immediately pass the new container through the appropriate "synchornized" method.
- To prevent more than one process from modifying the contents of a container, which may cause disasters, Java container library uses a fail-fast mechanisim that lookes for any changes to the container other than the ones your process is personally responsible for. If it detects that someone else is modifying the container, it immediately produces a `ConcurrentModificationException`, rather than trying to detect a problem later on using a more complex algorithm.
```
Iterator<String> it = c.iterator();
c.add("An object");
try{
    String s = it.next();
} catch(ConcurrentModificationException e){
    System.out.println(e);
}
```
- Note that foreach syntax uses iterator implicitly:
```
for (String s : famous)
    if (s.equals("madehua"))
        famous.remove(s);   // ConcurrentModificationException
```
- You should acquire the iterator after you have added all the elements to the container.
- The `ConcurrentHashMap`, `CopyOnWriteArrayList`, and `COpyOnWriteArraySet` use techniques that avoid `ConcurrentModificatioinException`s.

## Holding References
- The **java.lang.ref** library contains a set of references that are especially useful when you have large objects that may cause memory exhaustion.
- If an object is reachable, it means that somewhere in your program the object can be found. If an object isn't reachable, it's safe to garbage collect that object.
- You use `Reference` objects when you want to continue to hold on to a reference to that object --- you want to reach that object --- but you also want to allow the garbage collector to release that object. You accomplish this by using a `Reference` object as an intermediary (a proxy) between you and the ordinary reference. In addition, there must be no ordinary references to that object (not even wrapped inside the `Reference` objects).
- `SoftReference` is for implementing memory-sensitive caches. The object it pointing to will be garbage collected if there is not enough memory.
- `WeakReference` is for implementing canonicalizing mappings --- where instances of objects can be simultaneously used in multiple places in a program, to save storage --- that do not prevent their keys (or values) from being reclaimed. It does not influence the garbage collector (only strong reference and soft reference count). If the object is collected, it will point to `null`.
- `PhantomReference` is for scheduling pre-mortem cleanup actions in a more flexible way than is possible with the Java finalization mechanism.
- With `SoftReference` and `WeakReference`, you have a choice about whether to place them on a `ReferenceQueue`, but a `PhantomReference` can only be built on a `ReferenceQueue` (the device used for pre-portem cleanup actions, which always produces a `Reference` containing a `null` object). When GC want to collect one objects, the non-strong reference will be added into the `ReferenceQueue` it been built on:
```
public class References{
    private static ReferenceQueue<BigType> rq = new ReferenceQueue<BigType>();
    public static void checkQueue(){
        Reference<? extends BigType> inq = rq.poll();
        if(inq != null)
            System.out.println("In queue: " + inq.get());
    }
    public static void main(String[] args){
        int size = 10;
        LinkedList<SoftReference<BigType>> sa = new LinkedList<SoftReference<BigType>>();
        for(int i=0; i<size; i++){
            sa.add(new SoftReference<BigType>(new BigType(), rq));
            checkQueue();
        }
        LinkedList<WeakReference<BigType>> sa = new LinkedList<WeakReference<BigType>>();
        for(int i=0; i<size; i++){
            sa.add(new SoftReference<BigType>(new BigType(), rq));
            checkQueue();
        }
        SoftReference<BigType> s = new SoftReference<BigType>(new BigType());
        WeakReference<BigType> w = new WeakReference<BigType>(new BigType());
        System.gc();
        LinkedList<PhantomReference<BigType>> pa = new LinkedList<PhantomReference<BigType>>();
        for(int i=0; i<size; i++){
            sa.add(new PhantomReference<BigType>(new BigType(), rq));   // null
            checkQueue();   // rq is not empty, but inq.get() returns null
        }
    }
}
```
- `finalize()` runs before garbage collection. If you create a strong reference in a overriden `finalize()` of the reference, GC cannot collect it. `PhantomReference` is collected after GC, thus having no such problem.

### WeakHashMap
- It hold weak references and make the creation of canonicalized mappings easier.
- In such a mapping, you are saving storage by creating only one instance of a particular value. When the program needs that value, it looks up th existing objet in the mapping and uses that.
- The trigger to allow cleanup is that the key is nolonger in use:
```
Key[] keys = new Key[size];
WeakHashMap<Key,Value> map = new WeakHashMap<Key,Value>();
for(int i=0; i<size; i++){
    Key k = new Key(i);
    Value v = new Value(i);
    if(i%2 == 0)
        keys[i] = k;    // save as strong reference
    map.put(k, v);
}
System.gc();    // GC skips every second entry
```
Note that `Key` must have `hashcode()` and `equals()`.

## Java 1.0/1.1 Containers
- You can think of `Vector` as an `ArrayList` with long, awkward method names, with many flaws.
- `Enumaration` is an interface of iterator, with only two methods: `hasMoreElement()` and `nextElement()`. You can produce an `Enumaration` for any `Collection`:
```
Vector<String> v = new Vector<String>();
Enumeration<String> e = v.elements();   // common way
e = Collections.enumeration(new ArrayList<String>());   // adapt from new containers' iterator
```
- The basic `HashMap` is very similar to the `HashMap`, even down to the method names. There is no reason to use `Hashtable` in new code.
- `Stack` is inherited from `Vector` rather than composition. So it has all of the characteristics and behaviors of a `Vector` plus some extra `Stack` behaviors. You should use a `LinkedList` when you want stack behavior.
- `BitSet` is efficient only from the standpoint of size; if you're looking for efficient access, it is slightly slower than using a native array.
- The minimum size of the `BitSet` is that of a `long`: 64 bits. If you're storing anything smaller, it will be wasteful. Using it should only be decided based on profiling and other metrics. It will expand as you add more elements.
```
BitSet bb = new BitSet();
bb.set(10); // set 10th bit
bb.clear(10);
```
- `EnumSet` is usually a better choice than a `BitSet` if you have a fixed set of flags that you can name, since it allows you to manipulate the names rather than numerical bit locations. The only reasons you should use `BitSet` instead of `EnumSet` is if you don't know how many flags you will need until run time.

# Enumerated Types

## Basic Features
- `values()` method produces an array of the `enum` constants in the order in which they were declared.
- When creating an `enum`, an associated class is produced by the compiler. This class is automatically inherited from `java.lang.Enum`. It's `Comparable` and `Serializable`.
- You can always safely compare `enum` instances using ==.
- `static import` brings all the `enum` instance identifiers into the local namespace, so they don't need to be qualified:
```
// enumerated/Spiciness.java
package enumarated;
public enum Spiciness{ NOT, MILD, MEDIUM, HOT, FLAMING };

//enumerated/Burrito.java
package enumerated;
import static enumerated.Spiciness.*;
/* all enums can be used without 'Spiciness.' before them here */
```
It is not possible to use this technique if the enum is defined in the same file or the default package.

### enum as **class**
- Except for the fact that you can not inherit from it, an enum can be treated much like a regular `class`. It is possible to have a `main()`.
- If you consider the code generated by the compiler -- each enum elements is a `static final` instance of the enum.
- If you are going to define methods you must end the sequence of `enum` instances with a semicolon. Also, Java forces you to define the instances as the first thing in the enum.
- The constructor can only be used to create the `enum` instances that you declare inside; the compiler won't let you use it to create any new instances once the `enum` definition is complete:
```
public enum OzWitch{
    WEST("This is west"),
    EAST("This is east");    // using constructor;
    private String description;
    private OzWitch(String description){    // all access specifiers are the same here
        this.description = description;
    }
    public String getDescription() { return description;}
}
```

### enums in **switch**
- enums can be used in `switch` statements. And you do not have to qualify an instance with its type in a `case` statement:
```
Signal color = Signal.RED;
switch(color){
    case RED:    // 'Signal.' is not required
        color = Signal.GREEN;    // here it is required
        break;
    case GREEN:
        color = Signal.RED;
        break;
}
```
- If you are calling `return` from `case` statements, the compiler will complain if you do not have a `default` -- even if you have covered all the possible values.

### Extension
- Since Java does not support ultiple inheritance, you cannot create an enum via inheritance.
- It is possible to create an enum that implements one or more interfaces.

## The Mystery of **values()**
- There is no `values()` method in `Enum`.
- `values()` is a `static` method that is added by the compiler into the enum type (not in `Enum`). There is also a static initialization clause.

## Special Use

### Random Selection
```
public class Enums{
    private static Random rand = new Random(47);
    public static <T extends Enum<T>> T random(class<T> ec){
        // T should be an enum
        return random(ec.getEnumConstants());
    }
    public static <T> T random(T[] values){
        return values[rand.nextInt(values.length)];
    }
}
```

### Interface for Organization
- You can achieve categorization by grouping the elements together inside an interface and crreating an enumeration based on that interface:
```
public interface Food{
    // Everything is Food
    enum Appetizer implements Food{
        SALAD, SOUP, SPRING_ROLLS;
    }
    enum MainCourse implements Food{
        LASAGNE, BURRITO, PAD_THAI;
    }
}
```
However, it is not as useful as an enum when you want to deal with a set of types. If you want to have an"enum of enums" you can create a surrounding enum with one instance for each enum in `Food`:
```
public enum Course{
    APPETIZER(Food.Appetizer.class),
    MAINCOURSE(Food.MainCourse.class);
    private Food[] values;
    private Course(Class<? extends Food> kind){
        values = kind.getEnumConstants();
    }
}
```
- Another approach to the problem of catogorization is to next enums within enums:
```
enum SecurityCategory{
    STOCK(Security.Stock.class), BOND(Security.Bond.class);
    Security[] values;
    SecurityCategory<Class<? extends Security> kind){
        values = kind.getEnumConstants();
    }
    interface Security{
        enum Stock implements Security { SHORT, LONG, MARGIN }
        enum Bond implements Security { MUNICIPAL, JUNK }
    }
    public Security randomSelection(){
        return Enums.random(values);
    }
    public static void main(String[] args){
        for(int i=0; i<10; i++){
            SecurityCategory category = Enums.random(SecurityCategory.class);
            System.out.println(category.randomSelection());
        }
    }
}
```

### EnumSet
- An enum requires that all its members be unique, so it would seem o have set behavior, but since you cannot add or remove elements, it is not very useful as a set.
- The `EnumSet` is designed for speed, because it must compete effectively with bit flags.
- The `of()` method has been overloaded both with varargs and with individual methods taking two through five explicit arguments. This is an indication of the concern of performance with EnumSet, because a single method using varargs could have solved the problem, but it is slightly less efficient than hving explicit arguments. Note that if you call it with one argument, the compiler will not construct the varargs array and so there is no extra overhead.
- EnumSet is built on top of `long`. But if you have an EnumSet for an enum of over 64 elements, it still works. (maybe it adds another `long` when necessary).

### EnumMap
- Because of the constraints on an enum, an EnumMap can be implemented internally as an array. Thus they are extremely fast, so you can freely use EnumMaps for enum-based lookups.
- You can only call `put()` for keys that are in your enum.
```
// command design pattern
interface Command { void action(); }

public class EnumMaps{
    public static void main(String[] args){
        EnumMap<Alarms,Command> em = 
            new EnumMap<Alarms,Command>(Alarms.class);
        em.put(KITCHEN, new Command(){
            public void action(){ print("Kitchen fire!") }
        }
        em.put(BATHROOM, new Command(){
            public void action(){ print("Bathroom alert!") }
        }
        for(Map.Entry<Alarms,Command> e : en.entrySet()){
            System.out.print(e.getKey() + ": ");
            e.getValue().action();
        }
    }
}
```
- Just as with EnumSet, the order of elements in the EnumMap is determined by their order of definition in the enum.
- An EnumMap allows you to change the value objects, whereas constant-specific methods are fixed at compile time.

### Constant-specific Methods
- It allows you to give each enum instance different behavior by creating methods for each one.
```
public enum ConstantSpecificMethod{
    DATE_TIME{
        String getInfo(){
            // do something
        }
    }
    CLASSPATH{
        String getInfo(){
            // do something else
        }
    }
    abstract String getInfo();
}
```
This is often called table-driven code.
- This suggests that each instance is a distinct type. Each enum instance is being treated as the "base type", but you get polymorphic behavior with the abstract method. However, you can only take the similarity so far. You cannot treat enum instances as class types. Also, they do not behave like ordinary inner classes; you cannot accesss non-static fields or methods in the outer class.
- It is possible to override constant-specific methods, instead of implementing an `abstract` method:
```
public enum OverrideConstantSpecific{
    NUT, BOLT,
    WASHER{
        void f() { System.out.println("Overridden") }
    };
    void f() { System.out.printly("Default"); }
}
```
Although enums do prevent certain types of code, in general you should experiment with them as if they were classes.

### Chains of Responsibility with enums
- You can easily implement a simple Chanin of Responsibility with constant-specific methods. Each instance has different function, and ordered by its ordinal.

### State Machines with enums
- Enumerated types can be ideal for creating state machines.

## Single Dispatch
- Java only performs single dispatching. If you are performing an operation on more than one object whose type is unknown, Java can invoke the dynamic binding mechanism on only one of those types.
- Polymorphism can only occur via method calls, so if you want multiple dispatching, there must be multiple method calls.
```
interface Item{
    Outcome compete(Item it);
    Outcome eval(Paper p);
    Outcome eval(Scissors s);
    Outcome eval(Rock r);
}
class Paper implements Item{
    // double dispatch: compete dispatches this, eval diapatches it
    public Outcome compete(Item it) { return it.eval(this) };
    public Outcome eval(Paper p) { /**/ }
    public Outcome eval(Scissor s) { /**/ }
    public Outcome eval(Rock r) { /**/ }
}
```
- The benefit is the syntactic elegance achieved when making the call -- instead of writing awkward code to determine the type of one or more objects during a call.

### Dispatching with enums
- You cannot use enum instances as argument types. But one approach is to use a constructor to initialize each enum istance with a "row" of outcomes:
```
public enum RoShamBo implements Competitor<RoShamBo>{
    PAPER(DRAW, LOSE, WIN),
    SCISSORS(WIN, DRAW, LOSE),
    ROCK(LOSE, WIN, DRAW);
    private Outcome vPAPER, vSCISSORS, vROCK;
    RoShamBo(Outcome paper, Outcome scissors, Outcome rock){
        this.vPAPER = paper;
        this.vSCISSORS = scissors;
        this.vROCK = rock;
    }
    public Outcome compete(RoShamBo it){
        switch(it){
            default:
            case PAPER: return vPAPER;
            case SCISSORS: return vSCISSORS;
            case ROCK: return vROCK;
        }
    }
}
public class RoShamBoDriver{
    // static to avoid having to specify the type explicitly
    public static <T extends Competitor<T>> void match(T a, T b){
        /* a.compete(b); */
    }
    public static <T extends Enum<T> & Competitor<T>>
    void play(Class<T> rsbClass){
        /**/
    }
}
```
- Only the first dispatch uses a virtual method call. The second dispatch uses a `switch`, but is safe because the enum limits the choices in the `switch` statement.

### Dispatch using Constant-specific Methods
```
public enum RoShamBo implements Competitor<RoShamBo>{
    ROCK{
        public Outcome compete(RoShamBo opponent){
            return compete(SCISSORS, opponent);
        }
    },
    SCISSORS{
        public Outcome compete(RoShamBo opponent){
            return compete(PAPER, opponent);
        }
    },
    PAPER{
        public Outcome compete(RoShamBo opponent){
            return compete(ROCK, opponent);
        }
    };
    Outcome compete(RoShamBo loser, RoShamBo opponent){
        return ((opponent == this) ? Outcome.DRAW
             : (opponent == loser) ? Outcome.WIN
                                   : Outcome.LOSE));
    }
}
```
- The second dispatch is performed by the two-argument version of `compete()`.

### Dispatching with EnumMaps
```
enum RoShamBo implements Competitor<RoShamBo>{
    PAPER, SCISSORS, ROCK;
    static EnumMap<RoShamBo,EnumMap<RoShamBo,Outcome>> table = new
        EnumMap<RoShamBo,EnumMap<RoShamBo,Outcome>>(RoShamBo5.class);
    static{
        for(RoShamBo it : RoShamBo.values())
            table.put(it, new EnumMap<RoShamBo,Outcome>(RoShamBo.class));
        initRow(PAPER, DRAW, LOSE, WIN);
        initRow(SCISSORS, WIN, DRAW, LOSE);
        initRow(ROCK, LOSE, WIN, DRAW);
    }
    static void initRow(RoShamBo it, Outcome vPAPER,
        Outcome vSCISSORS, Outcome vROCK){
        EnumMap<RoShamBo, Outcome> row = RoShamBo.table.get(it);
        row.put(RoShamBo.PAPER, vPAPER);
        row.put(RoShamBo.SCISSORS, vSCISSORS);
        row.put(RoShamBo.ROCK, vROCK);
    }
    public Outcome compete(RoShamBo it){
        // both dispatches in a single statement
        return table.get(this).get(it);
    }
}
```

### Dispatching using 2-D Array
- Using `ordinal()` that produces the index.
- It can only produce a constant output given constant inputs.
- All of the above approaches are different types of tables.

# Exception

## General Concepts
- Use exceptions to:
1. Avoid catching exceptionis unless you know what to do with them.
2. Fix the problem and call the method that caused the exception again.
3. Patch things up and continue
4. Calculate some alternative result instead of what the method was supposed to produce
5. Do whatever you can in the context and rethrow
6. Do whatever you can in the current context and throw a different exception
7. Terminate the program
8. Simplify
9. Make your library and program safer.

### Exception Handling
- A major problem with most error-handling schemes is that they rely on programmer vigilance in following an agreed-upon convention that is not enforced by the language.
- Because it uses a separate execution path, it doesn't need to interfere with your normally executing code.
- An exception cannot be ignored, so it's guaranteed to be dealt with at some point.
- Exceptions provide a way to reliably recover from a bad situation.
- Exception handling isn't an object-oriented feature.
- The ideal time to catch an error is at compile time. However, not all errors can.
- Exceptions tend to reduce the complexity of error-handling code. You only need to handle the problem in one place --- exception handler.

### In Java
- In Java, exception handling was wired in from the beginning and you're forced to use it. If you don't write your code to properly handle exceptions, you'll get a compile-time error message.
- The basic philosophy of Java is that badly formed code will not be run.
- When you throw an exception:
1. The exception object is created;
2. The current path of execution is stopped and the reference for the exception object is ejected;
3. The exception-handling mechanism takes over and begins to look for an exception handler.
- There two constructors in all standard exceptions: default and the ones taking a string argument. In either case, an exception object is returned, and the method or scope exits.
- You can throw any type of `Throwable`. The information about the error is represented both inside the exception object and in the name of it.
- There are many libraries (like I/O) that you can't use without handling exceptions.
- A consistent error-reporting system means that you no longer have to worry about some errors slipping through the cracks (as long as you don't swallow the exceptions).

### Termination vs. Resumption
- If you want resumption-like behavior in Java, don't throw an exception when you encounter an error. Instead, call a method that fixes the problem. Alternatively, place your `try` block inside a `while` loop that keeps reentering the `try` block until the result is satisfactory.
- A resumptive handler would need to be aware of where the exception is thrown, and contain non-generic code specific to the throwing location.

### Alternative Approaches
- Do not catch an exception unless you know what to do with it. One of the important goals of exception handling is to move the error-handling code away from the point where the errors occur.
- Checked exceptions complicate this scenario a bit, because they force you to add `catch` clauses in places where you may not be ready to handle an error. This results in the "harmful if swallowed" problem:
```
try{
	// ...
} catch(ObligatoryException e){
	// Gulp!
}
```
- This topic is not only complicated, it is also an issue of some volatility.
- In C, errors are ignored in code and using debuggers to track down problems.
- In C++ the exception specificationi is not part of the type information of a function. The only compile-time checking is to ensure that exception specifications are used consistently. No compile-time checking occurs to determin whether or not the function or method will actually throw that exception, or whether the exception specification is complete. That validation does happen, but only at run time (call `unexpected()`). Because of the use of templates, exception specifications are not used at all in the Standard C++ Library.
- Java effectively invented the checked exception. It has been suggested that the subtle difficulties begin to appear when programs start to get large. This would encourage programmers to subvert the exception-handling mechanisms and to write spurious code to suppress exceptions.
- There are very significant productivity benefits to reducing the compile-time constraints upon the programmer. Indeed, reflection and generics are requited to compensate for the overconstraining nature of static typing. The value of an automated build process and unit testing give you far more leverage that you get by trying to turn everything into a syntax error.
- If you find that some checked exceptions are getting in your way, or especially if you find yourself being forced to catch exceptions, but you don't know what to do with them, there are some alternatives.
- Passing exceptions to the console:
```
public static void main(String[] args) throws Exception{
	// ...
}
```
- Converting checked to unchecked exceptions:
```
try{
	// ...
} catch(IDontKnowWhatToDoWithThisCheckedException e){
	throw new RuntimeException(e);
}
```
Note that you can still handle the specific exception by using `getCause()`:
```
catch(RuntimeExcepion e){
	try{
		throw e.getCause();
	} catch(SomeException re){
		// ...
	}
}
```
- Another solution is to create your own subclass of `RuntimeException`. This way, it doesn't need to be caught, but someone can catch it if they want to.

## Catching Exceptions
- Guarded region is a section of code that might produce exceptions and is followed by the code to handle those exceptions.
- With exception handleing, you put everything is a `try` block and capture all the exceptions in one place. The goal of code is not confuced with the error checking.
- Within the `try` block, a number of different method calls might generate the same exception, but you need only one handler.

### Exception
- You can catch any exception by catching the base-class exception type `Exception` at the end of the list of handlers (there are other types of base exceptions, but `Exception` is the base that's pertinent to virtually all programming activities).
- `getClass()` with `getName()` (includes package name) and `getSimpleName()` are also helpful.
- Normally, the following methods provide successively more information:
1. `getMessage()`
2. `getLocalizedMessage()`
3. `toString()`
4. `printStackTrace()`

### Stack Trace
- `getStackTrace()` returns an array of stack trace elements, each representing one stack frame.
- `fillInStackTrace()` in `Throwable` records information about the current state of the stack frames. Useful when an application is rethrowing an error or exception.
```
try{
	throw new Exception();
}catch(Exception e){
	for(StackTraceElement ste : e.getStackTrace())
		System.out.println(ste.getMethodName());
}
```
- `printStackTrace()` produces information about the sequence of methods that were called:
```
try{
    f();
}catch(MyException e){
    e.printStackTrace(System.out);  // default is System.err
}
```

### Rethrowing an Exception
- After rethrowing the reference, any further `catch` clauses for the same `try` block are still ignored.
- If you want to install new stack trace information, you can do so by calling `fillInStackTrace()`, which returns a `Throwable` object that it creates by stuffing the current stack information into the old exception object.
```
throw (Exception)e.fillInStackTrace();	// becomes the new point of origin of the exception
```
- It's also possible to rethrow a different exception from the one you caught.
- Catching one exception and throw another, but still keep the information about the originating exception is called exception chaining.
- The cause is intended to be the originating exception.
- The only `Throwable` subclasses that provide the cause argument in the constructor are: `Error`, `Exception`, and `RuntimeException`. If you want to chain any other exception types, you do it through the `initCause()` method rather that the constructor.
```
catch(NullPointerException e){
	DynamicFieldsException dfe = new DynamicFieldsException();
	dfe.initCause(e);
	throw dfe;	// printStackTrace() will show the cause
}
```
```
catch(NoSuchFieldException e){
	// such exception here is a programming error, so converted
	throw new RuntimeException(e);
}
```

## Exception Matching
- If you try to "mask" the derived-class exceptions by putting the base-class `catch` clause first, the compiler will give you an error message.

## Standard Java Exception
- There two general types of `Throwable` objects. `Error` represents compile-time and system errors that you don't worry about catching. `Exception` is the basic type that can be thrown from any of the standard Java library class methods and from your methods and runtime accidents.
- Note that some `Exception`s are created to support other libraries other than `java.lang`.

### RuntimeException
- They are always thrown automatically by Java and you don't need to include them in your exception specifications.
- `RuntimeException` is unchecked exceptions. Because they indicate bugs, you don't usually catch it --- it's dealt with automatically. It represents a programming error which is:
1. An error you cannot anticipate (like `null` reference `NullPointerException`)
2. An error you should have checked for (like `ArrayIndexOutOfBoundsException`)
- If a `RuntimeException` gets all the way out to `main()` without being caught, `printStackTrace()` is called for that exception as the program exits.
- They help in the debugging process.

## Performing Cleanup with finally
- C++ exception handling does not have the `finally` clause because it relies on destructors to accomplish this short of cleanup.
- The `finally` clause always runs. If you place your `try` block in a loop, you can establish a condition that must be met before you continue the program.
```
while(true){
	try{
		if(count++ == 0)
			throw new ThreeException();
	} catch(ThreeException e){
		System.out.println("ThreeException");
	} finally{
		if(count == 2)	// out of "while"
			break;
	}
}
```
- The `finally` clause is necessary when you need to get to set something other than memory back to its original state.
- Even in cases in which the exceptioin is not caught in the current set of `catch` clauses, `finally` will be executed before the exception-handling mechanism continues its search for a handler at the next higher level.
- Because `finally` is always executed, it's possible to return from multiple points within a method and still guarantee that important cleanup will be performed:
```
public static void f(int i){
	try{
		if(i == 1)
			return;
		if(i == 2)
			return;
		return;
	} finally{
		System.out.print("Performing cleanup");
	}
}
```
- It's possible for an exception to simply be lost in Java. C++ treats the situation in which a second exception is thrown before the first one is handled as a dire programming error:
```
try{
	try{
		throw new Exception1();	// lost
	} finally{
		throw new Exception2();
	}
} catch(Exception e){	// only catch Exception2
	// ...
}
```
```
try{
	throw new RuntimeException();
} finally{
	return;	// "return" inside the finally block will silence any thrown exception
}
```

## Creating New Exceptions
- The most trivial way to create a new type of exception is just to let the compiler create the default constructor for you.
- The most important thing about an exception is the class name.
- If you send output to `System.err`, it will not be redirected along with `System.out` so the user is more likely to notice it.

### Exceptions and Logging
- The easiest way to write to a `Logger` is just to call the method associated with the level of logging message:
```
import java.util.logging.*;
import java.io.*;
class LoggingException extends Exception{
    private static Logger logger = Logger.getLogger("LoggingException");
    public LoggingException(){
        StringWriter trace = new StringWriter();
        printStackTrace(new PrintWriter(trace));    // doesn't produce a String by default
        logger.severe(trace.toString());    // write to the Logger, send ouput to System.err
    }
}
```
- It's more common that you will be catching and logging someone else's exception:
```
try{
    new NullPointerException();
}catch(NullPointerException e){
    StringWriter trace = new StringWriter();
    e.printStackTrace(new PrintWriter(trace));
    logger.severe(trace.toString());
}
```
- `getMessage()` is something like `toString()` for exception classes, and will be printed at first in the log.

## Exception Specification
- Empty exception specification means that no exceptions are thrown from the method except for the exceptions inherited from `RuntimeException`, which can be thrown anywhere without exception specifications.
- You must either handle the exception or indicate with an exception specification.
- You can claim to throw an exception that you really don't. It's important for creating `abstract` base classes and `interface`s.
- Exceptions that are checked and enforced at compile time are called checked exceptions.
- When you override a method, you can throw only the exceptions that have been specified in the base-class version of the method.
- If you upcast to the base type, then the compiler forces you to catch the exceptions for the base type.
- The "exception specification interface" for a particular method may narrow during inheritance and overriding, but it may not widen --- this is precisely the opposite of the rule for the class interface during inheritance.

### Constructors
- The restriction on exceptionis does not apply to constructors. The derived-class constructor can throw any exception it wants. But it must declare any base-class constrctor exceptios in its exception specification. A derived-class constructor cannot catch exceptions thrown by its base-class constructor.
- If you throw an exception from inside a constructor, the cleanup behaviors might not occur properly. If a constructor fails partway through its execution, it might not have successfully created some part of the object that will be cleaned up in the `final` clause. Passing it on, when appropriate, can certainly simplify coding:
```
public InputFile(String fname) throws Exception{
	try{
		in = new BufferedReader(new FileReader(fname));
	} catch(FileNotFoundException e){
		throw e;	// not open, so need to close
	} catch(Exception e){
		try{
			in.close();
		} catch(IOException e2){
			System.out.println("close unsuccessfully");
		}
		throw e;
	} finally{
		// Don't close it here!!!
	}
}
```
- The safest way to use a class which might throw an exception during construction and which requires cleanup is to use nested `try` blocks:
```
try{
	InputFile in = new InputFile("test.java");
	try{
		String s;
		int i = 1;
		while((s=in.getLine()) != null)
			;
	} catch(Exception e){
		e.printStackTrace(System.out);
	} finally{	// not executed if construction fails, and always executed if it succeeds
		in.dispose();
	}
} catch(Exception e){
	// ...
}
```
- The basic rule is: Right after you create an object that requires cleanup, begin a `try`-`finally`:
```
try{
	NeedsCleanup nc1 = new NeedsCleanup();
	try{
		NeedsCleanup nc2 = new NeedsCleanup();
		try{
			// ...
		} finally{
			nc2.dispose();
		}
	} finally{
		nc.dispose();
	}
}
```
Note that if `dispose()` can throw an exception you might need additional `try` blocks.

# Generics
- An interface still requires that your code work with that particular interface. A parameterized type is a class that the compiler can automatically customize to work with particular types.
- The original intent of generics in programming languages was to allow the programmer the greatest amount of expressiveness possible when writing classes or methods. The Java implementation of generics is not that broad reaching.
- One of the primary motivations for generics is to specify what type of object a container holds, and to have that specification backed up by the compiler. (Using `Object` cannot tell the compiler the specific type). You are only allowed to put objects of the type that you specified in the client code (or a subtype) into the holder.
- When you want to return multiple objects from a method call, generics can save you effort and ensure compile-time type safety by tuple (idea of Data Transfer Object, or Messenger):
```
public class TwoTuple<A,B>{
	public final A first;	// DTO, the recipient of the object is allowed to read but not put new ones in
	public final B second;
	public TwoTuple(A a,B b) { first = a; second = b; }
}
public class ThreeTuple<A,B,C> extends TwoTuple<A,B>{
	public final C third;
	public ThreeTuple(A a,B b,C c){
		super(a,b);
		third = c;
	}
}
```
- One of the limitations of Java generics is that you cannot use primitives as type parameters. However, Java SE5 conveniently added autoboxing and autounboxing.
- I believe the intent of the general-purpose language feature called "generics" is expressiveness, not just creating type-safe container. Type-safe containers come as a side effect of the ability to create more general-purpose code. A badely written program that compiles is still a badly written program. And mistakes like "dog in cat list" is not often.

## Generic Interface
- Generics can work with interfaces. A usual example is generator, which is actually a specialization of the Factory Method:
```
public interface Generator<T> { T next(); }
public class CoffeeGenerator implements Generator<Coffee>, Iterable<Coffee>{
	private Class[] types = {Latte.class, Mocha.class, Cappuccino.class};
	private static Random rand = new Random(47);
	private int size = 0;
	public CoffeeGenerator(int size) { this.size = size; }
	public Coffee next(){
		try{
			return (Coffee) types[rand.nextInt(types.length)].newInstance();
		} catch(Exception e){
			throw new RuntimeException(e);
		}
	}
	class CoffeeIterator implements Iterator<Coffee>{
		int count = size;
		public boolean hasNext() { return count > 0; }
		public Coffee next(){
			count--;
			return CoffeeGenerator.this.next();	// plain this points to the inner class
		}
		public void remove(){
			throw new UnsupportedOperationException();
		}
	};
	public Iterator<Coffee> iterator(){
		return new CoffeeItetator();
	}
}
```

## Generic Method
- If it's possible to make a method generic rather than the entire class, it's probably going to be better to do so. In addition, if a method is `static`, it has no access to the generic type parameters of the class, so if it needs to use genericity it must be a generic method.
```
public <T> void f(T x){ /*...*/ }
```
- It's possible to explicitly specify the type in a generic method. When calling within the same class, you must use `this`; when working with `static` methods, you must use the class name:
```
SomeClass.<Type>staticMethod();
SomeObject.<Type>method();
this.<Type>method();
```
- Generic `Class` object can be used to provide a general-purpose generator:
```
public class BasicGenerator<T> implements Generator<T>{
    private Class<T> type;
    public BasicGenerator(Class<T> type){ this.type = type; }
    public T next(){
        try{
            return newInstance();   // assumes type is a public class
        } catch(Exception e){
            throw new RuntimeException(e);
        }
    }
    public static <T> Generator<T> create(Class<T> type){   // use Class to identify the type
        return new BasicGenerator<T>(type);
    }
}
```
A generic method `create()` allows you to say `Generator<Type> gen = Generator.create(Type.class)` instead of the more awkward `new Generator<MyType>(MyType.class)`.

- Generics can also be used with inner classes and anonymous inner classes:
```
class Customer{
    private Customer() {}
    public static Generator<Customer> generator(){
        return new Generator<Customer>(){
            public Customer next() { return new Customer(); }
        };
    }
}
```
- Parameterized generic object can be "upcast" to an unparameterized one. However, if you were to try to capture it into a parameterized one, the compiler would issue a warning:
```
class Tuple{
    public static <A,B> TwoTuple<A,B> tuple(A a, B b){
        return new TwoTuple<A,B>(a, b);
    }
    public static <A,B,C> ThreeTuple<A,B,C> tuple(A a, B b, C c){
        return new ThreeTuple<A,B,C>(a, b, c);
    }
}
public class Test{
    static TwoTuple<String,Integer> f(){
        return tuple("hi", 47);
    }
    static TwoTuple f2(){
        return tuple("hi", 47);
    }
    public static void main(String[] args){
        TwoTuple<String,Integer> ttsi = f();
        TwoTuple<String,Integer> ttsi2 = f2();  // warning
    }
}
```

### Type Argument Inference
- With a generic method, you don't usually have to specify the parameter types because of the type argument inference of the compiler. For calls that use primitive types, autoboxing comes into play.
- Type inference doesn't work for anything other that assignment. If you pass the result of a generic method call as an argument, the compiler will treat the method call as though the return value is assigned to a variable of type `Object`.
```
public class New{
    public static <K,V> Map<K,V> map(){
        return new HashMap<K,V>();
    }
    static void f(Map<String, List<? extends String>> m) {}
    public static void main(String[] args){
        Map<String, List<String>> sls = New.map();  // Type Argument Inference
        f(New.map());   // compile error!
    }
}
```

## Erasure
- Erasure is not a language feature. If generics had been part of Java 1.0, it would have used reification to retain the type parameters as first-class entities.
- In an erasure-based implementation, generic types are treated as second-class types that cannot be used in some important contexts. They can be checked at compile time as they supposed to be, but erased at run time. So they are still useful, just not as useful as they could be.
- Although you can say `ArrayList.class`, you cannot say `ArrayList<Integer>.class`:
```
Class c1 = new ArrayList<String>().getClass();
Class c2 = new ArrayList<Integer>().getClass();
System.out.println(c1 == c2);   // true
```
- The cold truth is: there's no information about generic parameter types available inside generic code:
```
class Quark<Q> {}
public class LostInformation{
    public static void main(String[] args){
        Quark<Integer> quark = new Quark<Integer>();
        System.out.println(Arrays.toString(quark.getClass().getTypeParameters()));  // only placeholder [Q]
    }
}
```
- Inside the generic, the only thing that you know is that you're using an object.

### Comparison with C++
- The C++ compiler checks when you instantiate the template, the template code knows the type of its template parameters:
```
template<class T> class Manipulator{
    T obj;
public:
    Manipulator(T x) { obj = x; }
    void manipulate() { obj.f(); }
};
class HasF{
public:
    void f() {}
}
int main(){
    HasF hf;
    Manipulator<HasF> manipulator(hf);
    manipulator.manipulate();
}
```
- Java generics are different, the `manipulate()` method will not compile. We must assist the generic class by giving it a bound that tells the compiler to only accept types that conform to that bound:
```
class Manipulator<T extends HasF>{
    private T obj;
    public Manipulator(T x) { obj = x; }
    public void manipulate() { obj.f(); }
}
```
- A generic type parameter erases to its first bound. The compiler actually replaces the type parameter with its erasure, so `T` erases to `HasF`.
- Generics are effective at the boundaries of a class or method. In the interiors, erasure usually makes generics unusable. If a class has a method that returns `T`, then generics are helpful because they will return the exact type.

### Migration Compatibility
- The generic types are present only during static type checking, after which type annotations such as `List<T>` are erased to `List`, and ordinary type variables are erased to `Object` unless a bound is specified.
- The core motivation for erasure is that it allows generified clients to be used with non-generified libraries, and vice versa, which is called migration compatibility.
- Java generics not only must support backwards compatibility --- existing code are still legal, but also must support migration compatibility --- the libraries can become generic and doesn't break code and applications that depend upon them.
- To achieve migrationi compatibility, each library and application must be independent of all the others regarding whether generics are used.

### The Problem with Erasure
- Generic types cannot be used in operations that explicitly refer to runtime types, such as casts, `instanceof` operations, and `new` expression.
- The use of generics is not enforced when you might want it to be:
```
class GenericBase<T> {
    private T element;
    public void set(T arg) { arg = element; }
    public T get() { return element; }
}
class Derived1<T> extends GenericBase<T> {}
class Derived2 extends GenericBase{}    // No warning until set() is called
class Derived3 extends GenericBase<?> {}    // Error
```
- `Class<T>` in a generic class is just being stored as a `Class`:
```
public class ArrayMaker<T>{
    private Class<T> kind;
    ...
    @SuppressWarnings("unchecked")
    T[] create(int size){
        return (T[])Array.newInstance(kind, size);  // don't have the type information, thus must be cast, which produces a warning
    }
}
```
- Creating a container is different:
```
List<T> create(){   // no warning although <T> is erased inside. If changed to new ArrayList(), a warning is given
    return new ArrayList<T>();
}
```
- Even though the compiler is unable to know anything about `T` inside `create()`, it can still ensure --- at compile time --- the internal consistency in the way that the type is used within the method or class:
```
public class FilledListMaker<T>{
    List<T> create(T t, int n){
        List<T> result = new ArrayList<T>();
        for(int i=0; i<n; i++)
            result.add(t);
        return result;
    }
    public void main(String[] args){
        FilledListMaker<String> m = FilledListMaker<String>();
        List<String> l = m.create("Hello", 10); // "Hello" will be checked, other types won't compile
    }
}
```
- What matters at run time is the boundaries. These are the points at which the compiler performes type checks at compile time, and inserts casting code. The bytecodes of common non-generic code and its generic version is almost identical. In generic code, the checking is performed by the compiler (so not in the bytecode), and casting code (of the returned object) is automatically inserted by the compiler (thus still there).
- A class cannnot implement two variants of the same generic interface. Because of erasure, these are both the same interface. It becomes annoying when you are working with some of the very fundamental Java interfaces.
```
// base class hijacks an interface
public class ComparablePet implements Comparable<ComparablePet>{
    public int compareTo(ComparablePet arg) { return 0; }
}
class Cat extends COmparablePet implements Comparable<Cat>{ // error
    public int compareTo(Cat arg) { return 0; }
}
```
Once the `ComparablePet` argument is established for `Comparable`, no other implementing class can ever be compared to anything but a `ComparablePet`.

- Overloading the method produces the identical type signature because of erasure:
```
// won't compile
public class UseList<W,T>{
    void f(List<T> v) {}
    void g(List<W> v) {}
}
```

### Compensating for Erasure
- Anything that requires the knowledge of the exact type at run time won't work. Occasionally you can program around, but sometimes you must compensate for erasure by introducing a type tag:
```
public class ClassTypeCapture<T> {
    Class<T> kind;
    public ClassTypeCapture(Class<T> kind){
        this.kind = kind;
    }
    public boolean f(Object arg){
        return kind.isInstance(arg);
    }
}
```
- The attempt to create a `new T()` won't work, partly because of erasure, and partly because the compiler cannot verify that `T` has a default constructor. The solution is to pass in a factory object:
```
class ClassAsFactory<T> {
    T x;
    public ClassAsFactory(Class<T> kind){
        try{
            x = kind.newInstance(); // need a default constructor
        } catch(Exception e){
            throw new RuntimeException(e);
        }
    }
}
```
Note that `Class<T>` is an implicit factory object.

- Another approach is the Template Method design pattern:
```
abstract class GenericWithCreate<T> {
    final T element;
    GenericWithCreate() { element = create(); } // template method
    abstract T create();    // wait for implementation
}
class X{}
class Creator extends GenericWithCreate<X>{
    X create() { return new X(); }
}
public class CreatorGeneric{
    public static void main(String[] args){
        Creator C = new Creator();
    }
}
```

## Generic Arrays
- Using `Array.newInstance()` is recommended approach for creating arrays in generics.
- Generally, you cannot create arrays of generics. You can define a reference in a way that the compiler accepts, but you can never create an array of that exact type:
```
class Generic<T>{}
public class ArrayOfGenericReference{
    static Generic<Integer>[] gia;  // just Generic[]
}
```
- Also, create an array of `Object` and cast that to the desired array type won't run. It produces a `ClassCastException`. Arrays keep track of their actual type at the point of creation. The casting information only exists at compile time. The only way to successfully create an array of a generic type is to create a new array of the erased type, and cast that:
```
@SuppressWarnings("unchecked")
Generic<Integer>[] gia = (Generic<Integer>[])new Object[10];   // compile, but won't run (at run time, still Object[])
@SuppressWarnings("unchecked")
Generic<Integer>[] gia = (Generic<Integer>[])new Generic[10];  // works!
```
- The general solution is to use an `ArrayList` everywhere you are tempted to create an array of generics. You can get the behavior of an array but the compile-time type safety afforded by generics.
- Because of erasure, the runtime type of the array can only be `Object[]`, even the return value:
```
public class GenericArray<T>{
    private T[] array;
    @SuppressWarnings("unchecked")
    public GenericArray(int sz){
        array = (T[])new Object[sz];
    }
    public T get(int i) { return array[i] }
    public T[] rep() { return array; }  // actual runtime type is Object[]
    public static void main(String[] args){
        GenericArray<Integer> gai = new GenericArray<Integer>(10);
        Integer[] ia = gai.rep();   // ClassCastException!
        Object[] oa = gai.rep();    // OK
    }
}
```
- It's better to use an `Object[]` inside the collection, and add a cast to `T` when you use an array element. The advantage is that it's less likely that you'll forget the runtime type of that array:
```
public class GenericArray<T>{
    private Object[] array;
    ...
    @SuppressWarnings("unchecked")
    public T get(int i){
        return (T)array[i]; // the internal representation is Object rather that T
    }
    @SuppressWarnings("unchecked")
    public T[] rep(){
        return (T[])array;
    }
}
```
- A type token can be passed in order to recover from the erasure:
```
public class GenericArray<T>{
    private T[] array;
    @SuppressWarnings("unchecked")
    public GenericArray(Class<T> type, int sz){ // type is not kept as a member, so not erased
        array = (T[])Array.newInstance(type, sz);   // create the actual type of array
    }
    public T get(int index) { return array[index]; }
    public T[] rep() { return array; }
    public static void main(String[] args){
        GenericArray<Integer> gai = new GenericArray<Integer>(Integer.class, 10);
        Integer[] ia = gai.rep();   // now it works
    }
}
```
- There are casts from `Object` arrays to parameterized types everywhere in Java SE5 standard libraries and produce lots of warnings. When you look at library code, you cannot assume that it's an example that you should follow in your own code.
- Autoboxing solves some problems, but not all:
```
Byte[] p = {1,2,3,4,5}; // autoboxing converses the elements one by one
Set<Byte> m = new HashSet<Byte>(Arrays.asList(p));  // ok
Set<Byte> my = new HashSet<Byte>(Arrays.<Byte>asList(1,2,3,4)); // constructs an Integer array first; autoboxing doesn't apply to arrays
```

## Bounds
- Because erasure removes type information, the only methods you can call for an unbounded generic parameter are those available for `Object`.
```
interface HasColor {}
class Dimension {}
// Multiple bounds, class must be first, then interfaces:
class ColoredDimension<T extends Dimension & HasColor>{
    // can use methods from Dimension and HasColor
}
interface Weight {}
// you can have only one concrete class
class Solid<T extends Dimension & HasColor & Weight>{
    // ...
}
```

### Wildcard
- Wildcards are limited to a single bound:
```
List<? extends Super1 & Super2> l;  // illegal!
```
- Unlike arrays, generics do not have build-in convariance:
```
Fruit[] fruit = new Apple[10];  // run time: Apple, compile time: Fruit
fruit[0] = new Fruit(); // can compile, ArrayStoreException at run time
fruit[0] = new Orange();    // can compile, ArrayStoreException at run time
List<Fruit> flist = new ArrayList<Apple>(); // compile error
```
- Sometimes, you'd like to establish some kind of upcasting relationship between the two containers. This is what wildcards allow:
```
List<? extends Fruit> flist = new ArrayList<Apple>();   // covariance allowed
flist.add(new Apple()); // compile error, can't add anything
flist.add(new Fruit()); // compile error, can't add anything
flist.add(null);    // legal but useless
```
The wildcard refers to a definite type, so it means "some specific type which the `flist` reference doesn't specify". But in order to upcast to `flist`, that type is a "don't actually care". Since the compiler don't know what type the `List` is holding (maybe a `List<Orange>`), you can't safely add any object.

- On the other hand, if you call method that returns `Fruit`, that's safe because you know that anything in the `List` must at least be of type `Fruit`, so the compiler allows it.
- While `add()` takes an argument of the generic parameter type, `contains()` and `indexOf()` take arguments of type `Object`. So when you specify an `ArrayList<? extends Fruit>, the argument for `add()` becomes `? extends Fruit`. So calling `contains()` and `indexOf()` is fine since no wildcard is involved:
```
List<? extends Fruit> flist = Arrays.asList(new Apple());
Apple a = (Apple)flist.get(0);  // ok
flist.contains(new Apple());    // argument is Object
flist.indexOf(new Apple()); // argument is Object
```
- The compiler is only paying attention to the types of objects that are passed and returned. It is not analyzing the code to see if you perform any actual writes or reads.
```
public class Holder<T>{
    private T value;
    public Holder(T val) { value = val; }
    public void set(T val) { value = val; }
    public T get() { return value; }
    public boolean equals(Object obj) { return value.equals(obj); }
    public static void main(String[] args){
        Holder<Apple> apple = new Holder<Apple>(new Apple());
        apple.set(new Apple());
        Holder<Fruit> fruit = apple;    // error
        Holder<? extends Fruit> fruit = apple;
        Fruit p = fruit.get();
        d = (Apple)fruit.get(); // returns Fruit, no warning, but risk of ClassClassException
        try{
            Orange c = (Orange)fruit.get(); // No warning, cause a ClassClassException
        } catch(Exception e){}
        fruit.set(new Apple()); // error
        fruit.set(new Fruit()); // error
        fruit.equals(d);    // ok
    }
}
```

### Supertype Wildcard
- You can specify `<? super MyClass>` or even using a type parameter `<? super T>`. But note that you can not give a generic parameter a supertype bound <T super MyClass>.
- It allows you to safely pass a typed object into a generic type:
```
public class SuperType{
    static void writeTo(List<? super Apple> apples{
        apples.add(new Apple());
        apples.add(new Fruit());    // error
    }
}
```
- Supertype bounds (lower bound) relax the constraints on what you can pass into a method:
```
static <T> void writeExact(List<T> list, T item){
    list.add(item);
}
static <T> void writeWithWildcard(List<? super T> list, T item){
    list.add(item);
}
public static void main(String[] args){
    List<Fruit> fruit = new ArrayList<Fruit>();
    writeExact(fruit, new Apple()); // error, incompatible types: found Fruit, required Apple
    writeWithWildcard(fruit, new Apple());
}
```
- Subtype bounds (upper bound) relax the constraints on what you can get from a method:
```
static class Reader<T>{
    T readExact(List<T> list){
        return list.get(0);
    }
}
static class CovariantReader<T>{
    T readCovariant(List<? extends T> list){
        return list.get(0);
    }
}
public static void main(String[] args){
    Reader<Fruit> fruitReader = new Reader<Fruit>();
    CovariantReader<Fruit> fruitReader2 = new CovariantReader<Fruit>();
    List<Apple> apple = new ArrayList<Apple>();
    Fruit a = fruitReader.readExact(apple); // error
    Fruit b = frtuiReader2.readCovariant(apple);
}
```
A static method adapts to each call, but if you have a class, then its type is established when the class is instantiated.

### Unbounded Wildcard
- <?> can be thought of as a decoration: "I don't mean here that I'm using a raw type, but that in this case the generic parameter can hold any type."
```
static List list1;
static List<?> list2;
static List<? extends Object> list3;
static void assign1(List list){
    list1 = list;   // ok
    list2 = list;   // ok
    list3 = list;   // warning: unchecked conversion
}
static void assign2(List<?> list){
    list1 = list;   // ok
    list2 = list;   // ok
    list3 = list;   // ok
}
static void assign3(List<? extends Object> list){
    list1 = list;   // ok
    list2 = list;   // ok
    list3 = list;   // ok
}
```
- When you are dealing with multiple generic parameters, it's sometimes important to allow one parameter to be any type while establishing a particular type for the other parameter.
- `List` actually means "a raw `List` that holds any `Object` type," whereas `List<?>` means "a non-raw `List` of some specific type, but we just don't know what that type is."
```
static void rawArgs(Holder holder, Object arg){
    holder.set(arg);    // warning: unchecked call to set(T) as a member of the raw type Holder
    Object obj = holder.get();  // ok, but type information has been lost
}
static void unboundedArg(Holder<?> holder, Object arg){
    holder.set(arg);    // error
    Object obj = holder.get();  // ok, but type information has been lost
}
static <T> wildSubtype(Holder<? extends T> holder, T arg){
    holder.set(arg);    // error
    T t = holder.get();
}
static <T> wildSupertype(Holder<? super T> holder, T arg){
    holder.set(arg);
    T t = holder.get(); // error
}
```
You can pass an object of any type into `set()` of a raw type, and that object is upcast to `Object`. So anytime you have a raw type, you give up compile-time checking.:
```
static <T> T exact1(Holder<T> holder){
    return holder.get();
}
static <T> T exact2(Holder<T> holder, T arg){
    holder.set(arg);
    return holder.get();
}
static public void main(String[] args){
    Holder raw = new Holder<Long>();
    Holder<Long> qualified = new Holder<Long>();
    Holder<?> unbounded = new Holder<Long>();
    Holder<? extends Long> bounded = new Holder<Long>();
    Long lng = 1L;
    
    Object r1 = exact1(raw);    // warning
    Long r2 = exact1(qualified);
    Object r3 = exact1(unbounded);  // must return Object
    Long r4 = exact1(bounded);
    
    Long r5 = exact2(raw, lng); // warnings: unchecked convension from Holder to Holder<Long>; unchecked method invocation
    Long r6 = exact2(qualified, lng);
    Long r7 = exact2(unbounded, lng);   // error
    Long r8 = exact2(bounded, lng); // error
}
```

- The benefit of using exact types instead of wildcard types is that you can do more with the generic parameters. But using wildcards allows you to accept a broader range of paramaterized types as arguments.
- Capture conversion in particular requires the use of <?> rather than a raw type:
```
static <T> void f1(Holder<T> holder){
    T t = holder.get();
    System.out.println(t.getClass().getSimpleName());
}
static void f2(Holder<?> holder){
    f1(holder); // call with captured type
}
public static void main(String[] args){
    Holder raw = new Holder<Integer>(1);
    f1(raw);    // warning
    f2(raw);    // no warning, print Integer
    Holder rawBasic = new Holder();
    rawBasic.set(new Object()); // warning
    f2(rawBasic);   // no warning, print Object
    Holder<?> wildcarded = new Holder<Double>(1.0);
    f2(wildcarded); // no warning, print Double
}
```
It's possible for the compiler to infer the actual type parameter, so that the method can turn around and call another method that uses the exact type. Capture conversion requires you to pass a specific type along with the `Holder<?>`. It only works in situations where, within the method, you need to work with the exact type. Note that you cannot return `T` from `f2()`, because `T` is unknown for `f2()`.

### Self-bounded Types
- Generics in Java are about arguments and return types, so it can prodece a base class that uses the derived type for its arguments and return types. It can also use the derived type for field types, even though those will be erased to `Object`:
```
public class BasicHolder<T> {
    T element;
    void set(T arg) { element=arg; }
    T get() { return element; }
    void f(){
        System.out.println(element.getClass().getSimplename());
    }
}
// curiously recurring generic (CRG)
class Subtype extends BasicHolder<SubType> {}
public class CRGWithBasicHolder{
    public static void main(String[] args){
        Subtype st1 = new Subtype();
        st1.set(new Subtype());
        Subtype st2 = st1.get();
        st1.f();    // print Subtype
    }
}
```
If `BasicHolder` is not generic, you should use the base class `BasicHolder` for arguments and return values. With CRG, the base class substitutes the derived class for its parameters. The generic base class becomes a kind of template for common functionality for all its arguments and return values.

- Self-bounding takes the extra step of forcing the generic to be used as its own bound argument.
```
class SelfBounded<T extends SelfBounded<T>>{ /*...*/ }
class A extends SelfBounded<A> {}
class B extends SelfBounded<A> {}   // also ok
class C extends SelfBounded<C> {
    C setAndGet(C arg){
        set(arg);
        return get();
    }
}
class D {}
class E extends SelfBounded<D> {}   // error: Type parameter D is not within its bound
class F extends SelfBounded {}  // ok, but can't force the idiom
```
- It's also possible to use self-bounding for generic methods:
```
static <T extends SelfBounded<T>> T f(T arg){
    return arg.set(arg).get();
}
public static void main(String[] args){
    A a = f(new A());
}
```
- The value of self-bounding types is that they produce covariant argument types --- method argument types vary to follow the subclasses.
```
class OrdinarySetter{
    void set(Base base) {}
}
class DerivedSetter extends OrdinarySetter{
    void set(Derived derived) {}    // overloaded, not overridden
}

interface SelfBoundSetter<T extends SelfBoundSetter<T>>{
    void set(T arg);
}
interface Setter extends SelfBoundSetter<Setter>{}
public class CovariantArgumentTest{
    void test(Setter s1, Setter s2, SelfBoundSetter sbs){
        s1.set(s2);
        s1.set(sbs);    // error, no method with that signature
    }
}
```
- Without self-bounding, you overload on argument types. If you use self-bounding, you only end up with one version of a method, which takes the exact argument type.
- Self-bounding types also produce the exact derived type as a return value, which is not vary important since covariant return types were introduced in Java SE5.
```
interface GenericGetter<T extends GenericGetter<T>>{
    T get();
}
interface Getter extends GenericGetter<Getter> {}
public class GenericsAndReturnTypes{
    void test(Getter g){
        Getter result = g.get();
        GenericGetter gg = g.get();
    }
}
```

## Issues

### Dynamic Type Safety
- Because you can pass generic containers to pre-Java SE5 code, there's still the possibility that old-style code can corrupt your containers. Checked container will throw a `ClassCastException` at the point you try to insert an improper object. The raw container will only inform you when you pulled the object out.
```
@SuppressWarnings("unchecked")
static void oldStyleMethod(List probablyDogs){
    probablyDogs.add(new Cat());
}
public static void main(String[] args){
    List<Dog> dogs1 = new ArrayList<Dog>();
    oldStyleMethod(dogs1);  // accept a Cat
    List<Dog> dogs2 = Collections.checkedList(new ArrayList<Dog>(), Dog.class); // checked container in java.util.Collections
    try{
        oldStyleMethod(dogs2);  // throw an exception
    } catch(Exception e){
        System.out.println(e);
    }
}
```

### Exception
- A generic class can't directly or indirectly inherit from `Throwable`. Because of erasure, a `catch` clause cannot catch an exception of a generic type, because the exact type of the exception must be known at both compile time and run time.
- Type parameters can be used in the `throws` clause of a method declaration. Otherwise, you would be unable to write some code generically because of the checked exceptions.
```
interface Processor<T,E extends Exception>{
    void process<List<T> resultCollector) throws E;
}
class Failure1 extends Exception {}
class Processor1 implements Processor<String,Failure1>{
    public void process(List<String> resultCollector) throws Failure1{
        throw new Failure1();
    }
}
```

### Mixin
- This is often something you do at the last minute, which makes it convenient to easily assemble classes.
- Mixins consistently apply characteristics and behaviors across multiple classes. If you want to change something in a mixin class, those changes are then applied across all the classes where the mixin is applied. Mixins have part of the flavor of aspect-oriented programming (AOP).
- In C++, you can easily create mixins because C++ remembers the type of its template parameters (you can also use multiple inheritance to implement mixins):
```
template<class T> class TimeStamped : public T{
    long timeStamp;
public:
    TimeStamped() { timeStamp = time(0); }
    long getStamp() { return timeStamp; }
};
template<class T> class SerialNumbered : public T{
    long serialNumber;
    static long counter;
public:
    SerialNumbered() { serialNumber = counter++; }
    long getSerialNumber() { return serialNumber; }
};
template<class T> long SerialNumbered<T>::counter = 1;

class Basic{
    string value;
public:
    void set(string val) { value = val; }
    string get() { return value; }
};
int main(){
    TimeStamped<SerialNumbered<Basic> > mixin;
    // it has all the methods of the mixed-in types
    mixin.getStamp();
    mixin.getSerialNumber();
    mixin.get();
}
```
- Java generics forgets the base-class type, so a generic class cannot inherit directlty from a generic parameter. A commonly suggested solutioni is to use interfaces to produce the effect of mixins:
```
interface TimeStamped { long getStamp(); }
class TimeStampedImp implements TimeStamped{
    private final long timeStamp;
    public TimeStampedImp(){
        timeStamp = new Date().getTime();
    }
    public long getStamp() { return timeStamp; }
}
interface SerialNumbered { long getSerialNumber(); }
class SerialNumberedImp implements SerialNumbered{
    private static long counter = 1;
    private final long serialNumber = counter++;
    public long getSerialNumber() { return serialNumber; }
}
class Mixin extends TimeStamped inplementes SerialNumbered{
    private SerialNumbered serialNumber = new SerialNumberedImp();  // using delegation
    public long getSerialNumber() { return serialNumber.getSerialNumber(); }
}
```
- Or using Decorator design pattern:
```
class Basic{
    private String value;
    public void set(String val) { value=val; }
    public String get() { return value; }
}
class Decorator extends Basic{
    protected Basic basic;
    public Decorator(Basic basic) { this.basic=basic; }
    public void set(String val) { basic.set(val); }
    public String get() { return basic.get(); }
}
class TimeStamped extends Decorator{
    private final long timeStamp;
    public TimeStamped(Basic basic){
        super(basic);
        timeStamp = new Date().getTime();
    }
    public long getStamp() { return timeStamp; }
}
class SerialNimbered extends Decorator{
    private static long counter = 1;
    private final long serialNumber = counter++;
    public SerialNumbered(BVasic basic) { super(basic); }
    public long getSerialNumber() { return serialNumber; }
}
public class Decoration{
    public static void main(String[] args){
        TimeStamped t = new TimeStamped(new SerialNumbered(new Basic()));
        //t.getSerialNumber() not available
    }
}
```
It only effectively works with one layer of decoration (the final one). Thus, Decorator is only a limited solution to the problem addressed by mixins.

- Dynamic Proxies models mixins more closely than does the Decorator.
```
class MixinProxy implements InvocationHandler{  // see Type Information
    Map<String,Object> delegatesByMethod;
    public MixinProxy(TwoTuple<Object,Class<?>>... pairs){
        delegatesByMethod = new HashMap<String,Object>();
        for(TwoTuple<Object,Class<?>> pair : pairs){
            for(Method method : pair.second.getMethods()){
                String methodName = method.getName();
                if(!delegatesByMethod.containsKey(methodName))
                    delegatesByMethod.put(methodName, pair.first);
            }
        }
    }
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable{
        String methodName = method.getName();
        Object delegate = delegatesByMethod.get(methodName);
        return method.invoke(delegate, args);
    }
    @SuppressWarnings("unchecked")
    public static Object newInstance(TwoTuple... pairs){
        Class[] interfaces = new Class[pairs.length];
        for(int i=0; i<pairs.length; i++)
            interfaces[i] = (Class)pairs[i].second;
        ClassLoader cl = pairs[0].first.getClass().getClassLoader();
        return Proxy.newProxyInstance(cl, interfaces, new MixinProxy(pairs));   // needs interface
    }
}

public class DynamicProxyMixin{
    public static void main(String[] args){
        Object mixin = MixinProxy.newInstance(
            tuple(new BasicImp(), Basic.class),
            tuple(new TimeStampedImp(), TimeStamped.class),
            tuple(new SerialNumberedImp(), SerialNumbered.class));
        Basic b = (Basic)mixin;
        TimeStamped t = (TimeStamped)mixin;
        SerialNumbered s = (SerialNumbered)mixin;
    }
}
```
Each class must be implementation of an interface because of the constraints of dynamic proxies. And you're forced to downcast to the appropriate type before you can call methods, which makes it still not quite as nice as the C++ approach.

### Latent Typing
- Writing general code needs way to loosen the constraints on the types that our code works with, without losing the benefit of static type checking.
- Because of erasure, a bounded generic might be no different from specifying a class or interface.
- One solution that some programming languages provide is called latent typing or structural typing or duck typing. It only requiring that a subset of methods be implemented, not a particular class or interface. Latent typing is a code organization and reuse mechanism.
- In Python, latent typing is checked in runtime.
```
class Dog:
    def speak(self):
        print 'Arf!'
    def sit(self):
        print 'Sitting'
class Robot:
    def speak(self):
        print 'Click!'
    def fight(self):
        print 'Boom!'
def perform(anything):
    anything.speak()
perform(Dog())
perform(Robot())
```
- In C++, latent typing is checked in compile time.
```
class Dog{
public:
    void speak() {}
    void sit() {}
};
class Robot{
public:
    void speak() {}
    void fight() {}
};
template<class T> void perform(T anything){
    anything.speak();
}
```
- C++ and Python both ensure that types cannot be misused and are thus considered to be strongly typed (it's more safe to say that C++ is strongly typed with a trap door because of casts), which is necessary for latent typing. In this case, Java generics were simply not necessary.
- Bounded generic code can still be applied across different type hierarchies in Java, with some extra effort. One approach is reflection:
```
class Mine{
    public void speak() {}
    public void fight() {}
    public String toString() { return "Mine"; }
}
class SmartDog{
    public void sit() {}
    public void speak() {}
    public String toString() { return "SmartDog" }
}
class CommunicateReflectively{
    public static void perform(Object speaker){
        Class<?> spkr = speaker.getClass();
        try{
            try{
                Method speak = spkr.getMethod("speak");
                speak.invoke(speaker);
            } catch(NoSuchMethodException e){
                System.out.println(speaker + "cannot speak");
            }
        } catch(Exception e){
            throw new RuntimeException(speaker, e);
        }
    }
}
```
- If you can achieve compile-time type checking, that's usually more desirable. So another approach is to use Java SE5 varargs:
```
public class Apply{
    public static <T, S extends Iterable<? extends T>> void apply(S seq, Method f, Object... args){
        try{
            for(T t:seq)
                f.invoke(t, args);
        } catch(Exception e){
            throw new RuntimeException(e);  // Failures are programmer errors
        }
    }
}
class FilledList<T> extends ArrayList<T>{
    public FilledList<Class<? extends T> type, int size){
        try{
            for(int i=0; i<size; i++)
                add(type.newInstance());    // assumes default constructor
        } catch(Exception e){
            throw new RuntimeException(e);
        }
    }
}
class ApplyTest{
    public static void main(String[] args) throws Exception{
        List<Shape> shapes = new ArrayList<Shape>();
        for(int i=0; i<10; i++)
            shapes.add(new Shape());
        Apply.apply(shapes, Shape.class.getMethod("rotate"));
    }
}
```
- We can also use a factory interface to deal with the default constructor assumption (but all classes used in `FilledList` must implement this interface). The type token technique is recommended in the Java literature. But we must observe that the use of reflection may be slower than a non-reflection implementation, since so much is happening at run time.
- In effect, latent typing creates an implicit interface containing the desired methods. So we can simulate latent typing with adapters:
```
interface Addable<T> { void add(T t); }
// to adapt a base type (like Collection), you must use composition
class AddableCollectionAdapter<T> implements Addable<T>{
    private Collection<T> c;
    public AddableCollectionAdapter(AddableCollectionAdapter c){ this.c = c; }
    public void add(T item) { c.add(item); }
}
// to adapt a specific type, you can use inheritance
class AddableSimpleQueue<T> extends SimpleQueue<T> implements Addable<T>{
    public void add(T item) { super.add(item); }
}
```
- True generic code can be get by performing general operations using Strategy design pattern with a functional style of programming.
```
interface Combiner<T> { T combine(T x, T y); }-
public class Functional{
    public static <T> T reduce(Iterable<T> seq, Combiner<T> combiner){
        Iterator<T> it = seq.iterator();
        if(it.hasNext()){
            T result = it.next();
            while(it.hasNext())
                result = combiner.combine(result, it.next());
            return result;
        }
        return null;
    }
}
// function object
static class IntegerAdder implements Combiner<Integer>{
    public Integer combine(Integer x, Integer y){
        return x+y;
    }
}
public static void main(String[] args){
    List<Integer> li = Arrays.asList(1,2,3,4);
    Integer result = reduce(li, new IntegerAdder());
}
```

# IO
- After Java 1.0, the original byte-oriented library was supplemented with `char`-oriented, Unicode-based I/O classes. It's rather important to understand the evolution of the I/O library.

## The `File` Class
- It does not refer to a file, but assists you with file directory issues. It can represent either the name of a particular file or the names of a set of files in a directory.
- `list()` can return a full list or act as a directory filter:
```
class DirFilter implements FilenameFilter{
    private Pattern pattern;
    public DirFilter(String regex) { pattern = Pattern.compile(regex); }
    @Override
    public boolean accept(File dir, String name){
        return pattern.matcher(name).matches();
    }
}
public class DirList{
    public static void main(String[] args){
        File path = new File(".");
        String[] list;
        if(args.length == 0)
            list = path.list(); // full list
        else
            list = path.list(new DirFilter(args[0]));   // directory filter
    }
}
```
- `FilenameFilter` interface contains only one method `accept()`, which is "called back" in `list()`. This structure is also an example of Strategy design pattern, since `list()` implements basic functionality, and `FilenameFilter` is a strategy. It is called for each of the file names in the directory object.
- Making `FilenameFilter` class tightly bound to `DirList` is a better design:
```
public class DirList{
    public static void main(final String[] args){
        File path = new File(".");
        String[] list;
        if(args.length == 0)
            list = path.list(); // returns String[]
        else
            list = path.list(new FilenameFilter(){
                    private Pattern pattern = Pattern.compile(args[0]);
                    public boolean accept(File dir, String name){
                        return pattern.matcher(name).matches();
                    }
                }
    }
}
```
- Anonymous inner classes allow the creation of specific, one-off classes to solve problems. On the other hand, it is not always as easy to read, so you must use it judiciously.
- `File` objects are more useful than file names because `File` objects contain more information.
```
public static class TreeInfo{
    public List<File> files = new ArrayList<File>();
    public List<File> dirs = new ArrayList<File>();
    void addAll(TreeInfo other){
        files.addAll(other.files);
        files.addAll(other.dirs);
    }
}
static TreeInfo recurseDirs(File startDir, String regex){
    TreeInfo result = new TreeInfo();
    for(File item : startDir.listFiles()){  // returns File[]
        if(item.isDirectory()){
            result.dirs.add(item);
            result.addAll(recurseDirs(item, regex));
        }
        else
            if(item.getName().matches(regex))
                result.files.add(item);
    }
    return result;
}
```
- Some of the other methods available with the `File` class:
```
File old = new File(args[1]);
File rname = new File(args[2]);
old.renameTo(rname);
System.out.println(rname.getAbsolutePath());
System.out.println(rname.length());
System.out.println(rname.lastModified());
File f = new File(args[0]);
if(f.exists())
    f.delete();
else
    f.mkdirs(); // create an entire directory path
```
- In Java, it appears that you are supposed to use a `File` object to determine whether a file exists, because if you open it as a `FileOutputStream` or `FileWriter`, it will always get overwritten.

## Input and Output
- Everything derived from the `InputStream` or `Reader` classes has basic methods called `read()` for reading a single **byte** or an array of **byte**s. Likewise, everything derived from `OutputStream` or `Writer` class has basic methods called `write()` for writing a single **byte** or an array of **byte**s. They exist so that other classes can use them to provide more useful interfaces.
- You'll rarely create your stream object by using a single class, but instead will layer multiple objects together to provide your desired functionality (Decorator design pattern). But that is the primary reason that Java's I/O library is confusing.
- In Java 1.0, the library designers started by deciding that all classes that had anything to do with input would be inherited from `InputStream`, and all that were associated with output would be inherited from `OutputStream`.
- A decorator must have the same interface as the object it decorates (`InputStream` and `OutputStream`), but the decorator can also extend the interface, which occurs in several of the "filter" classes.
- Decorators give you much more flexibility while you're writing a program, but they add complexity to your code. The reason that the Java I/O library is awkward to use is that you must create many classes --- the "core" I/O type plus all the decorators --- in order to get the single I/O object that you want.
- `InputStream` represent classes that produce input from different sources: array of bytes `ByteArrayInputStream`, `String` object `StringBufferInputStream`, file `FileInputStream`, pipe `PipedInputStream`, sequence of other streams `SequenceInputStream`, and Internet connection.
- `FilterInputStream` is an abstreact class that is an interface for decorators that provide useful functionality to the other `InputStream` classes.
- There is not `StringBufferOutputStream`, but you can create `String` using the array of `bytes`.
- `FilterOutputStream` provides a base class for "decorator" classes that attach attributes or useful interfaces to output streams.
- Since `finalize()` featurn didn't work out the way the Java designers originally envisioned it, so the only safe approach is to explicitly call `close()` for files. You should guards it in a `finally` clause to guarantee that the file will be properly closed.

### FilterInputStream and FilterOutputStream
- The constructors of `FilterInputStream` takes an `InputStream` to provide further attributes, while those of `FilterOutputStream` takes an `OutputStream`.
- `DataInputStream` allows you to read different types of primitive data as well as `String` objects. Along with its companion `DataOutputStream`, it allows you to move primitive data from one place to another via a stream.
- `BufferedInputStream` prevent a physical read every time. It doesn't provide an interface per se. It just adds buffering to the process. You'll need to buffer your input almost every time. You can assign optional buffer size in the constructor.
- `LintNumberInputStream` keeps track of line numbers. You can call `getLineNumber()` and `setLineNumber(int)`.
- `PushbackInputStream` has a non-byte pushback buffer so that you can push back the last character read. It is generally used in the scanner for a compiler.
- `DataOutputStream` and `BufferedOutputStream` are complements to the input methods.
- `PrintStream` focuses on format. This is different from `DataOutputStream`, whose goal is to put data elements on a stream in a way that `DataInputStream` can portably reconstruct them.
- `PrintStream` can be problematic because it traps all `IOException`s. You must explicitly test the error status with `checkError()`. Also, it doesn't internationalize properly and doesn't handle line breaks in a platform-independent way. These problems are solved with `PrintWriter`.

### Reader and Writer
- The `InputStream` and `OutputStream` classes still provide valuable functionality in the form of byte-oriented I/O (so not deprecated), whereas the `Reader` and `Writer` classes provide Unicode-compliant, character-based I/O. In particular, the **java.util.zip** libraries are byte-oriented rather than char-oriented.
- There are times when you must use classes from the `byte` hierarchy in combination with classes in the "character" hierarchy. `InputStreamReader` adapts an `InputStream` to a `Reader`, and `OutputStreamWriter` converts an `OutputStream` to a `Writer`.
- `Reader` and `Writer` is important for internationalization. The old I/O stream hierarchy supports only 8-bit byte streams.
- You should try to use the `Reader` and `Writer` classes whenever you can. Note that there is `StringWriter` but no `StringBufferOutputStream`.
- They also use the idea of decorator subclasses --- but not exactly like `FilterInputStream` and `FilterOutputStream`:
|Java 1.0 class|Java 1.1 class|
| - | - |
|`FilterInputStream`|`FilterReader`|
|`FilterOutputStream`|`FilterWriter`|
|`BufferedInputStream`|`BufferedReader`|
|`BufferedOutputStream`|`BufferedWriter`|
|`PrintStream`|`PrintWriter`|
|`LineNumberInputStream`|`ListNumberReader`|
|`StreamTokenizer`|`StreamTokenizer` with the constructor taking a `Reader` instead|
|`PushbackInputStream`|`PustbackReader`|

- Whenever you want to use `readLine()`, you should use `BufferedReader`. Other than this, `DataInputStream` is still a "preferred" member of the I/O library.
- One `PrintWriter` constructor also has an option to perform automatic flushing, which happens after every `println()` if the constructor flag is set.

### RandomAccessFile
- It is used for files containing records of known size so that you can move from on record to another using `seek()`, then read or change them. It has no association with `DataInputStream` and `DataOutputStream` hierarchies.
- There is no support for write-only files. It must be just randomly reading (`"r"`) or reading and writing (`"rw"`).
- `BufferedInputStream` allows you to `mark()` a position and `reset()`, but this is limited and not very useful.
- Most of the `RandomAccessFile` functionality is superseded as of JDK1.4 with the `nio` memory-mapped files.

## Typical I/O Usage

### Buffered Input File
- You can use `FileReader` to open a file and use `BufferedReader` for speed:
```
BufferedReader in = new BufferedReader(new FileReader(filename));
```
`BufferedReader` provides `readLine()` and if it returns `null`, you're at the end of the file. Note that it will delete the line break.

- `FileReader` use the default charset of the system. If you want to select some decoding charset, use `new BufferedReader(new InputStreamReader(new FileInputStream(fileName), charset));`

### Input from Memory
- `BufferedInputFile.read()` is used to create a `StringReader`, whose `read()` method returns the next character as an `int`:
```
StringReader in = new StringReader(BufferedInputFile.read("xxx.java"));
int c;
while((c=in.read()) != -1)
    System.out.print((char)c);  // must be cast to print properly
```

### Formatted Memory Input
- To read "formatted" data, you use a `DataInputStream`, which is a byte-oriented I/O class:
```
try{
    DataInputStream in = new DataInputStream(
        new ByteArrayInputStream(
            BufferedInputFile.read("xxx.java").getBytes()));
    while(true)
        System.out.print((char)in.readByte());
} catch(EOFException e){
    System.err.println("End of stream");
}
```
- You can use `available()` method to find out how many more characters are available:
```
while(in.available() != 0)
    System.out.println((char)in.readByte());
```
- Note that `available()` works differently depending on what sort of medium you're reading from; it's literally "the number of bytes that can be read without blocking". With a file, this means the whole file.

### Basic File Output
- Buffering tends to dramatically increase performance of I/O operations:
```
PrintWriter out = new PrintWriter(new BufferedWriter(new FileWriter(file)));
Strint s;
int lineCount = 1;
while((s=in.readLine()) != null)
    out.println(lineCount++ + ": " + s);
out.close();
```
- If you don't call `close()` for all your output files, you might discover that the buffers don't get flushed, so the file will be incomplete.
- Java SE5 added a helper constructor to `PrintWriter` to avoid all the decoration by hand:
```
// equivalent:
PrintWriter out = new PrintWriter(new BufferedWriter(new FileWriter("XXX")));
PrintWriter out = new PrintWirter("XXX");
```
- Unfortunately, other commonly written tasks were not given shortcuts. **java.util.Scanner** class introduced in Java SE5 can also solve the decoration problem of reading text files (but not for writing).

### Storing and Recovering Data
- `DataOutputStream` and `DataInputStream` are byte-oriented and thus require `InputStream`s and `OutputStream`s:
```
DataOutputStream out = new DataOutputStream(new BufferedOutputStream(new FileOutputStream("data.txt")));
out.writeDouble(3.14);
out.writeUTF("That was pi");
out.close();
DataInputStream in = new DataInputStream(new BufferedInputStream(new FileInputStream("data.txt")));
System.out.println(in.readDouble());
System.out.println(in.readUTF());
```
- Java guarantees accurately recovering regardless of what different platforms write and read the data.
- `writeUTF()` and `readUTF()` use UTF-8 encoding to avoid tremendous waste of space and bandwidth of Unicode when working with ASCII. The length of the string is stored in the first two bytes of the UTF-8 string. However, they use a special variation of UTF-8 for Java. So for non-Java program, you must write special code in order to read the string properly.
- You must either have a fixed format for the data in the file, or extra information must be stored in the file. Note that object serialization or XML may be easier ways to store and retrieve complex data structures.

### Reading and Writing Random-Access Files
- When using `RandomAccessFile`, you must know the layout of the file so that you can manipulate it properly. It has specific methods to read and write primitives and UTF-8 strings.
```
RandomAccessFile rf = new RandomAccessFile("xxx", "rw");
double a = rf.readDouble();
String s = rf.readUTF();
rf.writeUTF("The end of the file");
rf.seek(5*8);   // 40 byte from the head
rf.writeDouble(3.14);
```
- It implements the `DataInput` and `DataOutput` interfaces but separate from the rest of the I/O hierarchy. It doesn't support decoration.

### Reading Binary File
```
BufferedInputStream bf = new BufferedInputStream(new FileInputStream(bFile));
try{
    byte[] data = new byte[bf.avalible()];
    bf.read(data);
    return data;
} finally{
    bf.close();
}
```

## Standard I/O
- The value of standard I/O is that programs can easily be chained together.
- `System.out` is already pre-wrapped as a `PrintStream` object. `System.err` is likewise a `PrintStream`. But `System.in` is a raw `InputStream` with no wrapping, which must be wrapped before you can read from it. You'll typically read input a line at a time using `readLine()`, by wrapping `System.in` in a `BufferedReader`:
```
BufferedReader stdin = new BufferedReader(new InputStreamReader(System.in));
```
- If you want to convert `System.out` into a `PrintWriter`, it's important to use the two-argument version of the constructor and set the second argument to `true` in order to enable automatic flushing.

### Redirecting Standard I/O
- The Java `System` class allows you to redirect the standard I/O and error I/O streams using simple `static` method calls: `setIn(InputStream)`, `setOut(PrintStream)`, and `setErr(PrintStream)`.
```
PrintStream console = System.out;
PrintStream out = new PrintStream(new BufferedOutputStream(new FileOutputStream("xxx")));
System.setOut(out);
...
out.close();
System.setOut(console); // restore the system output
```
- I/O redirection manipulates streams of bytes, not streams of characters; thus, `InputStream`s and `OutputStream`s are used rather than `Reader`s and `Writer`s.
- Two types of errors can occur with executing other operating system programs from inside Java:
1. the normal errors that result in exceptions --- we will just rethrow a runtime exception;
2. errors from the execution of the process itself --- we want to report these errors with a separate exception
```
try{
    Process process = new ProcessBuilder("some command").start();
    BufferedReader results = new BufferedReader(new InputStreamReader(process.getInputStream()));
    ...
    BufferedReader errors = new BufferedReader(new InputStreamReader(process.gerErrorStream()));
} catch(Exception e){
    ...
}
```
`getInputStream()` captures the standard **output** stream from the program as it executes.

## New I/O
- **java.nio** packages has one goal: speed. In fact, the "old" I/O packages have been reimplemented using **nio** in order to take advantage of this speed increase.
- The speed comes from using structures that are colser to the operating system's way of performing I/O: channels and buffers.
- You don't interact directly with the channel; you interact with the buffer and send the buffer into the channel. The only kind of buffer that communicates directly with a channel is a `ByteBuffer` --- that is, a buffer that holds raw bytes.
- Three of the classes in the "old" I/O have been modified so that they produce a `FileChannel`: `FileInputStream`, `FileOutputStream`, and `RandomAccessFile`. Note that these are the byte manipulation streams.
```
FileChannel fc = new RandomAccessFile("data.txt", "rw").getChannel();
fc.position(fc.size()); // move to the end
fc.write(ByteBuffer.wrap("some more".getBytes("UTF-8")));  // UTF-8, Unicode will cause problem for byte
fc.close();
fc = new FileInputStream("data.txt").getChannel();
ByteBuffer buff = ByteBuffer.allocate(1024);
fc.read(buff);  // read to buffer
buff.flip();    // set limit to current position, and then the position is set to 0
while(buff.hasRemaining())
    System.out.print((char)buff.get()); // note that char is 2-byte
```
- When using `wrap()`, the underlying array is not copied. The `ByteBuffer` is "backed by" the array.
- For read-only access, you must explicitly allocate a `ByteBuffer`. The size of the `ByteBuffer` should be significant (1024 is quite a bit small). It's possible to go for even more speed by `allocateDirect()` instead of `allocate()` to produce a "direct" buffer that may have an even higher coupling with the operating system, but the overhead is greater and its actual implementation varies from one operating system to another.
- The **java.nio.channels.Channels** class has utility methods to produce `Reader`s and `Writer`s from channels:
- If we were to use the buffer for further `read()` operations, we'd also have to call `clear()` to prepare it for each `read()`:
```
FileChannel in = new FileInputStream(args[0]).getChannel();
FileChannel out = new FileInputStream(args[1]).getChannel();
ByteBuffer buffer = ByteBuffer.allocate(BSIZE);
while(in.read(buffer) != -1){
    buffer.flip();  // prepare for writing
    out.write(buffer);
    buffer.clear(); // prepare for reading
}
```
- More ideal way to handle this kind of simple file-copying operation:
```
in.transferTo(0, in.size(), out);
// or
// out.transferFrom(in, 0, in.size());
```

### Converting Data
- The buffer contains plain bytes, and to turn these into characters, we must either encode them as we put them in or decode them as they come out of the buffer.
```
// direct input and output
FileChannel fc = new FileOutputStream("data.txt").getChannel();
fc.write(ByteBuffer.wrap("Some text".getBytes()));
fc.close();
fc = new FileInputStream("data.txt").getChannel();
ByteBuffer buff = ByteBuffer.allocate(BSIZE);
fc.read(buff);
buff.flip();
System.out.println(buff.asCharBuffer());    // doesn't work
// encode using system's default
buff.rewind();  // go back to the beginning
String encoding = System.getProperty("file.encoding");
System.out.println(Charset.forName(encoding).decode(buff)); // right output
// encoding
fc = new FileOutputStream("data.txt").getChannel();
fc.write(ByteBuffer.wrap("Some text".getBytes("UTF-16BE")));    // will result in something printable
fc.close();
fc = new FileInputStream("data.txt").getChannel();
buff.clear();
fc.read(buff);
buff.flip();
System.out.println(buff.asCharBuffer());    // now ok
// CharBuffer to write through
fc = new FileOutputStream("data.txt").getChannel();
buff = ByteBuffer.allocate(20); // enough for 10 chars
buff.asCharBuffer().put("Some text");
fc.write(buff);
fc.close();
fc = new FileInputStream("data.txt").getChannel();
buff.clear();
fc.read(buff);
buff.flip();
System.out.println(buff.asCharBuffer());
```
- **java.nio.charset.Charset** class provides tools for encoding into many different types of character sets.

### Fetching Primitives
- Although a `ByteBuffer` only holds bytes, it contains methods to produce each of the diffenrent types of primitive values from the bytes it contains:
```
// allocation automatically zeroes the buffer
ByteBuffer bb = ByteBuffer.allocate(BSIZE);
for(int i=0; i<bb.limit(); i++)
    if(bb.get() != 0)
        System.out.println("nonzero");   // never printed
// char array
bb.asCharBuffer().put("xxx!");
char c;
while((c=bb.getChar()) != 0)
    ...
// int
bb.rewind();
bb.asIntBuffer().put(123456);
System.out.println(bb.getInt());
```

### View Buffers
- The `ByteBuffer` is still the actual storage that's backing the view, so any changes you make to the view are reflected in modifications to the data in the `ByteBuffer`.
```
ByteBuffer bb = ByteBuffer.allocate(BSIZE);
IntBuffer ib = bb.asIntBuffer();
ib.put( new int[]{1, 2, 3, 4, 5} );
ib.put(3, 7);   //1,2,3,7,5
ib.flip();
while(ib.hasRemaining()){
    int i = ib.get();
    System.out.println(i);
}
```
- Once the underlying `ByteBuffer` is filled with primitives via a view buffer, it can be written directly to a channel. You can just as easily read from a channel and use a view buffer to convert everything to a particular type of primitive:
```
ByteBuffer bb = ByteBuffer.wrap( new byte[]{0, 0, 0, 0, 0, 0, 0, 'a'} );
bb.rewind();
while(bb.hasRemaining())
    System.out.print(bb.position() + "->" + bb.get() + " ");   // 0->0 ... 7->97
DoubleBuffer db = ((ByteBuffer)bb.rewind()).asDoubleBuffer();
while(db.hasRemaining())
    System.out.print(db.position() + "->" + db.get() + " ");   // 0->4.8E-322
```
- A `ByteBuffer` stores data in big endian form, and data sent over a network always uses big endian order. You can change the endian-ness of a `ByteBuffer` using `order()` with `ByteOrder.BIG_ENDIAN` or `ByteOrder.LITTLE_ENDIAN`. For a `ByteBuffer` of `00000000 01100001`, if you read the data as a `short(ByteBuffer.asShortBuffer())`, you will get 97. If you change to little endian, you will get 24832.
```
ByteBuffer bb = ByteBuffer.wrap(new byte[6]);
bb.asCharBuffer().put("abc");
System.out.println(Arrays.toString(bb.array()));    // [0, 97, 0, 98, 0, 99]
bb.rewind;
bb.order(ByteOrder.LITTLE_ENDIAN);
bb.asCharBuffer().put("abc");
System.out.println(Arrays.toString(bb.array()));    // [97, 0, 98, 0, 99, 0]
```
Note that the `array()` method is optional, and you can only cal it on a buffer that is backed by an array; otherwise, you'll get an `UnsupportedOperationException`.

### Data Manipulation with Buffers
- The diagram illustrates the relationships between the **nio** classes:
![nio classes](http://www.linuxtopia.org/online_books/programming_books/thinking_in_java/TIJ329.png "nio classes")

- Note that `ByteBuffer` is the only way to move data into and out of channels. You cannot convert a primitive-typed buffer to a `ByteBuffer`. However, since you are able to move primitive data into and out of a `ByteBuffer` via a view buffer, this is not really a restriction.
- A `Buffer` consists of data and four indexes to access and manipulate this data efficiently: mark, position, limit, and capacity. e.g. `clear()` sets the position to zero and limit to capacity; `flip()` sets limit to position and position to zero; `remaining()` returns (limit-position).
```
private static void symmetricScramble(charBuffer buffer){
    while(buffer.hasRemaining()){
        buffer.mark();  // set mark in position
        char c1 = buffer.get(); // position++
        char c2 = buffer.get(); // position++
        buffer.reset(); // set the value of position to mark
        buffer.put(c2).put(c1); // position += 2
    }
    public static void main(String[] args){
        char[] data = "UsingBuffers".toCharArray();
        ByteBuffer bb = ByteBuffer.allocate(data.length*2);
        CharBuffer cb = bb.asCharBuffer();
        cb.put(data);
        System.out.println(cb.rewind());    // set position to the start of the buffer
        symmetricScramble(cb);
        System.out.println(cb.rewind());    // sUniBgfuefsr
        symmetricScramble(cb);
        System.out.println(cb.rewind());
    }
}
```

### Memory-mapped Files
- Memory-mapped files allow you to create and modify files that are too big to bring into memory. You can pretend that the entire file is in memory.
```
static int length = 0x8FFFFFF;  // 128MB probably larger than OS will allow in memory
public static void main(String[] args) throws Exception{
    MappedByteBuffer out = new RandomAccessFile("test.dat", "rw").getChannel()
        .map(FileChannel.MapMode.READ_WRITE, 0, length);
    for(int i=0; i<length; i++)
        out.put((byte)'x');
    for(int i=length/2; i<length/2+6; i++)
        System.out.print((char)out.get(i));
}
```
Note that you must specify the starting point and the length of the region that you want to map in the file; this means that you have the option to map smaller regions of a large file.

- `MappedByteBuffer` is inherited from `ByteBuffer`, so it has all of its methods.
- This way a very large file (up to 2GB) can easily be modified. Note that the file-mapping facilities of the underlying operating system are used to maximize performance. Although the performance of "old" stream I/O has been improved by implementing it with **nio**, mapped file access tends to be dramatically faster, even though the setup for mapped files can be expensive.
```
private static int numOfInts = 4000000;
private static int numOfUbuffInts = 200000;
private abstract static class Tester{
    public void runTest(){  // Template Method
        try{
            long start = System.nanoTime();
            test();
            double duration = System.nanoTime() - start;
            System.out.format("%.2f\n", duration/1.0e9);
        } catch(IOException e){
            throw new RuntimeException(e);
        }
    }
    public abstract void test() throws IOException;
}
private static Tester[] tests = {
    new Tester(){   // stream write 0.56
        public void test() throws IOException{
            DataOutputStream dos = new DataOutputStream(new BufferedOutputStream(new FileOutputStream(new File("temp.tmp"))));
            for(int i=0; i<numOfInts; i++)
                dos.writeInt(i);
            dos.close();
        }
    },
    new Tester(){   // mapped write 0.12
        public void test() throws IOException{
            FileChannel fc = new RandomAccessFile("temp.tmp", "rw").getChannel();
            IntBuffer ib = fc.map(FileChannel.MapMode.READ_WRITE, 0, fc.size().asIntBuffer());
            for(int i=0; i<numOfInts; i++)
                ib.put(i);
            fc.close();
        }
    },
    new Tester(){   // stream read 0.80
        public void test() throws IOException{
            DataInputStream dis = new DataInputStream(new BufferedInputStream(new FIleInputStream("temp.tmp")));
            for(int i=0; i<numOfInts; i++)
                dis.readInt();
            dis.close();
        }
    },
    new Tester(){   // mapped read 0.07
        public void test() throws IOException{
            FileChannel fc = new FileInputStream(new File("temp.tmp")).getChannel();
            IntBuffer ib = fc.map(FIleChannel.MapMode.READ_ONLY, 0, fc.size()).asIntBuffer();
            while(ib.hasRemaining())
                ib.get();
            fc.close();
        }
    },
    new Tester(){   // stream read/write 5.32
        public void test() throws IOException{
            RandomAccessFile raf = new RandomAccessFile(new FIle("temp.tmp"), "rw");
            raf.writeInt(1);
            for(int i=0; i<numOfUbuffInts; i++){
                raf.seek(raf.length() - 4);
                raf.writeInt(raf.readInt());
            }
            raf.close()
        }
    },
    new Tester(){   // mapped read/write 0.02
        public void test() throws IOException{
            FileChannel fc = new RandomAccessFile(new FIle("temp.tmp"), "rw").getChannel();
            IntBuffer ib = fc.map(FileChannel.MapMode.READ_WRITE, 0, fc.size()).asIntBuffer();
            ib.put(0);
            for(int i=1; i<numOfUbuffInts; i++)
                ib.put(ib.get(i-1));
            fc.close();
        }
    }
};
```
- All output in file mapping must use a `RandomAccessFile`.

### File Locking
- The file locks are visible to other operating system processes bacause java file locking maps directly to the native operating system locking facility.
- You get a `FileLock` on the entire file by calling either `tryLock()` or `lock()` on a `FileChannel` (`SocketChannel`, `DatagramChannel`, and `ServerSocketChannel` do not need locking since they are inherently single-process entities).
- `tryLock()` is non-blocking. It tries to grab the lock, but if it cannot (when some other process already holds the same lock and it is not shared), it simply returns from the method call.
```
FileOutputStream fos = nwe FileOutputStream("file.txt");
FileLock fl = fos.getChannel().tryLock();
if(fl != null){
    TimeUnit.MILLISECONDS.sleep(100);
    fl.release();
}
fos.close();
```
- `lock()` blocks until the lock is acquired, or the thread that invoked `lock()` is interrupted, or the channel on which the `lock()` method is called is closed.
- It is also possible to lock a part of the file. Although the zero-argument locking methods adapt to changes in the size of a file, locks with a fixed size do not change if the file size changes.
- If the operating system does not support shared locks and a request is made for one, an exclusive lock is used instead. The type of lock (shared or exclusive) can be queried using `FileLock.isShared()`.
- Several threads can lock different portions of a mapped file:
```
public class LockingMappedFiles{
    static final int LENGTH = 0x8FFFFFF;    // 128MB
    static FileChannel fc;
    public static void main(String[] args) throws Exception{
        fc = new RandomAccessFile("test.dat", "rw").getChannel();
        MappedByteBuffer out = fc.map(FileChannel.MapMode.READ_WRITE, 0, LENGTH);
        for(int i=0; i<LENGTH; i++)
            out.put((byte)'x');
        new LockAndModify(out, 0, 0+LENGTH/3);
        new LockAndModify(out, LENGTH/2, LENGTH/2+LENGTH/4);
    }
    private static class LockAndModify extends Thread{
        private ByteBuffer buff;
        private int start, end;
        LockAndModify(ByteBuffer mbb, int start, int end){
            this.start = start;
            this.end = end;
            mbb.limit(end); // set up the buffer region
            mbb.position(start);    // set up the buffer region
            buff = mbb.slice(); // create a slice to be modified
            start();
        }
        public void run(){
            try{
                FileLock fl = fc.lock(start, end, false);   // non-shared, critical section with exclusive access
                while(buff.position() < buff.limit()-1)
                    buff.put((byte)(byff.get()+1));
                fl.release();
            } catch(IOException e){
                throw new RuntimeException(e);
            }
        }
    }
}
```

## Compression
- These classes are not derived from the `Reader` and `Writer` classes, but `InputStream` and `OutputStream`. This is because the compression library works with bytes, not characters. You can mix the two types of streams with `InputStreamReader` and `OutputStreamWriter`.
- The GZIP interface is simple and thus is probably more appropriate when you have a single stream of data that you want to compress:
```
import java.util.zip.*;
import java.io.*;

BufferedReader in = new BufferedReader(new FileReader("input.txt"));
BufferedOutputStream out = new BufferedOutputStream(new GZIPOutputStream(new FileOutputStream("text.gz")));
int c;
while((c=in.read()) != -1)  // read one char
    out.write(c);
in.close();
out.close();
BufferedReader in2 = new BufferedReader(new InputStreamReader(new GZIPInputStream(new FileInputStream("text.gz"))));
String s;
while((s=in2.readLint()) != null)
    System.out.println(s);
```
- The library that supports the Zip format is more extensive. With it you can easily store multiple files, and there's even a separate class to make the process of reding a Zip file easy.
- The `ZipEntry` object contains an extensive interface that allows you to get and set all the data available on that particular entry in your Zip file. But it does not support password, and only supports an interface for CRC (not Adler).
```
public static void main(String[] args) throws IOException{
    FileOutputStream f = new FileOutputStream("text.zip");
    CheckedOutputStream csum = new CheckedOutputStream(f, new Adler32());
    ZipOutuputStream zos = new ZipOutputStream(csum);
    BufferedOutputStream out = new BufferedoutputStream(zos);
    zos.setComment("A test of Java Zipping");   // but no way to recover
    for(String arg : args){
        BufferedReader in = new BufferedReader(new FileReader(arg));
        zos.putNextEntry(new ZipEntry(arg));
        int c;
        while((c=in.read()) != -1)
            out.write(c);
        in.close;
        out.flush();
    }
    out.close();
    // checksum valid only after closed
    System.out.println(csum.getChecksum().getValue());
    // extract the file
    FileInputStream fi = new FileInputStream("test.zip");
    CheckedInputStream csumi = new CheckedInputStream(fi, new Adler32());
    ZipInputStream in2 = new ZipInputStream(csumi);
    BufferedInputStream bis = new BufferedInputStream(in2);
    ZipEntry ze;
    while((ze = in2.getNextEntry()) != null){
        System.out.println("Reading file " + ze);
        int x;
        while((x=bis.read()) != -1)
            System.out.write(x);
    }
    System.out.println(csumi.getChecksum().getValue());
    bis.close;
    // alternative way to open and read
    ZipFile zf = new ZipFIle("test.zip");
    Enumeration e = zf.entries();
    while(e.hasMoreElements()){
        ZipEntry ze2 = (ZipEntry)e.nextElement();
        ... // extract the data as before
    }
}
```
- There are two `Checksum` types: `Adler32` is faster and `CRC32` is slower but slightly more accurate.

### Java ARchives
- The Zip format is also used in the JAR file format. JAR files are cross-platform, so you don't need to worry about platform issues. You can also include audio and image files as well as class files.
- On the Internet, only one server request is necessary and transfer is faster with JAR. And each entry in a JAR file can be digitally signed for security.
- You can create your own manifest file; otherwise, the **jar** program will do it for you.
- **c** creates a new or empty archieve; **f** redirects the input and output to file; **0** only stores the files without compression (use to create a JAR file that you can put in your classpath).
- Unlike Zip, you can't add or update files to an existing JAR file. However, it's cross-platform (a problem that sometimes plagues Zip utilities).

## Object Serialization
- Of course, you can get a similar efect by writing the information to a file or to a database, but in the spirit of making everything an object, it would be quite convenient to declare an object to be "persistent", and have all the details taken care of for you.
- The serialization mechanism automatically compensates for differences in operating systems.
- It is lightweight. You must explicitly serialize and deserialize the objects in your program. If you need a more serious persistence mechanism, consider a tool like Hibernate.
- Two major features:
 - Java's Remote Method Invocation (RMI): allows objects that live on other machines to behave as if they live on your machine.
 - JavaBrean: its state information is generally configured at design time, and must be later recovered when the program is started.
- Many standard library classes were changed to make them serializable, including all of the wrappers for the primitive types, all of the container classes, and many others. Even `Class` objects can be serialized.
- It not only saves an image of your object, but it also follows all the references contained in your object and saves those objects. And it includes arrays of references to objects as well as member objects. Java uses an optimized algorithm that traverses the web of objects.
- `readObject()` cannot know what it is reading, so it returns an object that must be cast.
```
ObjectInputStream in = new ObjectInputStream(new FileInputStream(args[0]));
List<Widget> shapes = (List<Widget>)in.readObject();    // warning
List<Widget> lw2 = List<Widget>.class.cast(in.readObject());    // error
List<Widget> lw3 = List.class.cast(in.readObject());    // ok
List<Widget> lw4 = (List<Widget>)List.class.cast(in.readObject());  // warning
```
- You can also write all the primitive data types using the same methods as `DataOutputStream` (they share the same interface).
- You can also write and read through a `ByteArray`:
```
public class Worm implements Serializable{
    private static random rand = new Random(47);
    private Integer[] d = {rand.nextInt(10), rand.nextInt(10)};
    private Worm next;
    ...
    
    public static void main(String[] args){
        ByteArrayOutputStream bout = new ByteArrayOutputStream();
        ObjectOutputStream out = new ObjectOutputStream(bout);
        out.writeObject("Worm storage\n");
        out.writeObject(new Worm());
        out.flush();
        ObjectInputStream in = new ObjectInputStream(new ByteArrayInputStream(bout.toByteArray()));
        s = (String)in.readObject();
        Worm w = (Worm)in.readObject(); // really does contain all parts
    }
}
```
- Note that no constructor, not even the default constructor, is called in the process of deserializing a `Serializable` object.
- When you want to find the class of the recovered object, the JVM must be able to find the **.class** file. Otherwise, you will get a `ClassNotFoundException`:
```
Object mystery = in.readObject();
System.out.println(mystery.getClass());
```

### Controlling Serialization
- You can control the process of serialization by implementing the `Externalizable` interface. It extends the `Serializable` interface and adds two methods, `writeExternal()` and `readExternal()`. They are automatically called during serialization and deserialization.
- When recovered, `Externalizable` object's default constructor is called, then `readExternal()` is called. This is different from recovering a `Serializable` object in which the object is constructed entirely from its stored bits.
```
class Blip implements Externalizable{
    private Blip(){ // not public
        System.out.println("Constructor");
    }
    public void writeExternal(ObjectOutput out) throws IOException{
        System.out.println("writeExternal()");
    }
    public void readExternal(ObjectInput in) throws IOException, ClassNotFoundException{
        System.out.println("readExternal()");
    }
    public static void main(String[] args){
        ObjectOutputStream o = new ObjectOutputStream(new FileOutputStream("Blip.out"));
        o.writeObject(new Blip());
        o.close();
        ObjectInputStream in = new ObjectInputStream(new FileInputStream("Blip.out"));
        Blip b = (Blip)in.readObject(); // ClassNotFoundException
    }
}
```
- If you are inheriting from an `Externalizable` object, you'll typically call the base-class versions of `writeExternal()` and `readExternal()` to provice proper storage and retrieval of the base-class components.
- There is no default behavior that writes any of the member objects for an `Externalizable` object. It won't take place automatically:
```
public class Blip implements Externalizable{
    private int i;
    private String s;
    public Blip() {}
    public void writeExternal(ObjectOutput out) throws IOException{
        // you must do this
        out.writeObject(s);
        out.writeInt(i);
    }
    public void readExternal(ObjectInput in) throws IOException, ClassNotFoundException{
        // you must do this
        s = (String)in.readObject();
        i = in.readInt();
    }
}
```
- Once it has been serialized, even `private`, it's possible for someone to access it by reading a file. You can implement `Externalizable` and explicitly serialize only the necessary parts inside `writeExternal()`.
- If you're working with a `Serializable` object, all serialization happens automatically. You can use the `transient` keyword to prevent some subobject from automatically saving and restoring.
```
public class Logon implements Serializable{
    private Date date = new Date();
    private String username;
    private transient String password;
    public Logon(String name, String pwd){
        username = name;
        password = pwd;
    }
    
    public static void main(String[] args){
        Logon a = new Logon("Hulk", "littlePony");
        ObjectOutputStream o = new ObjectOutputStream(new FileOutputStream("Logon.out"));
        o.writeObject(a);
        o.close();
        ObjectInputStream in = new ObjectInputStream(new FileInputStream("Logon.out"));
        a = (Logon)in.readObject(); // date is unchanged, password is null
    }
}
```
- `writeObject()` and `readObject()` will automatically be called when the object is serialized and deserialized. They are not part of a base class or the `Serializable` interface, and they are `private` and `void`. The `writeObject()` and `readObject()` methods of `ObjectOutputStream` and `ObjectInputStream` call your objects `writeObject()` and `readObject()` via reflection.
- When you call `ObjectOutputStream.writeObject()`, the `Serializable` object that you pass it to is interrogated using reflection to see if it implements its own `writeObject()`. If so, the normal serialization process is skipped and the custom one is called. The same situation exists for `readObject()`.
- You can choose to perform the default serialization by calling `defaultWriteObject()` inside your `writeObject()`. Likewise, inside `readObject()` you can call `defaultReadObject()`.
```
public class SerialCtl implements Serializable{
    private String a;
    private transient String b;
    ...
    private void writeObject(ObjectOutputStream stream) throws IOException{
        stream.defaultWriteObject();    // must be the first operation if needed
        stream.writeObject(b);
    }
    private void readObject(ObjectInputStream stream) throws IOException{
        stream.defaultReadObject(); // must be the first operation if needed
        b = (String)stream.readObject();
    }
}
```
- It's possible to change the version of a serializable class, but it requires an extra depth of understanding. The versioning mechanism is too simple to work reliably in all situations, especially with JavaBeans. So the current serialization support is appropiate for short term storage or RMI between applications.

### Persistence
- It's possible to use object serialization to and from a `byte` array as a way of doing a "deep copy" of any object that's `Serializable`.
```
List<ComplexType> object1 = new ArrayList<ComplexType>();
object1.add(new ComplexType());
System.out.println(object1);    // print the address
ByteArrayOutputStream buf1 = new ByteArrayOutputStream();
ObjectOutputStream o1 = new ObjectOutputStream(buf1);
o1.writeObject(object1);
o1.writeObject(object1);

ByteArrayOutputStream buf2 = new ByteArrayOutputStream();
ObjectOutputStream o2 = new ObjectOutputStream(buf2);
o2.writeObject(object1);

ObjectInputStream in1 = new ObjectInputStream(new ByteArrayInputStream(buf1.toByteArray()));
List<ComplexType> object2 = (ComplexType)in1.readObject();
System.out.println(object2);    // different from object1, including its subobjects
List<ComplexType> object3 = (ComplexType)in1.readObject();
System.out.println(object3);    // same address as object2

ObjectInputStream in2 = new ObjectInputStream(new ByteArrayInputStream(buf2.toByteArray()));
List<ComplexType> object4 = (ComplexType)in2.readObject();
System.out.println(object4);    // different from object2, including its subobjects
```
- As long as you're serializing everything to a single stream, you'll recover the same web of objects that you wrote, with no accidental duplication of objects. If you change the state of your objects in between the time you write the first and the last, the objects will be written in whatever state they are in and with whatever connections they have to other objects at the time you serialize them.
- The safest thing to do if you want to save the state of a system is to serialize as an "atomic" operation. Put all the objects that comprise the state of your system in a single container and simply write that container out in one operation.
- `Class` is `Serializable`, so it should be easy to store the `static` fields by simply serializing the `Class` object, but it's not. Even you serialize and deserialize the `Class` objects, the `static`s won't get serialized at all. You should add `serializeStaticState()` and `deserializeStaticState()` and explicitly call them.
```
class Circle extends Shape{
    private static int color = 1;   // will be 1 after deserialized
    ...
}
class Square extends Shape{
    private static int color;   // will be 0 because of initialization durinig deserialization
    public Circle(){
        color = 1;
    }
}
class Line extends Shape{
    private static int color;
    public static void serializeStaticState(ObjectOutputStream os) throws IOException{
        os.writeInt(color);
    }
    public static void deserializeStaticState(ObjectInputStream os) throws IOException{
        color = os.readInt();
    }
}
```
- If you have a security issue, `private` fields should be marked as `transient`. But then you should design a secure way to store and restore explicitly.

## XML
- Converting data to XML format allows it to be consumed by a large variety of platforms and languages.
- XOM is simple and emphasizes XML correctness. `Element` class and `appendChild()` method will construct XMLs. `Serializer` class can turn XML into a more readable form.

## Preferences
- The preferences API is much closer to persistence than it is to object serialization, because it automatically stores and retrieves your information.
- You can only  hold primitives and strings, and the length of each stored string can't be longer than 8k. As the name suggests, the Preferences API is designed to store and retrieve user preferences and program-configuration settings.
- Preferences are key-value sets stored in a hierarchy of nodes.
```
import java.util.prefs.*;
public class PreferencesDemo{
    public static void main(String[] args) throws Exception{
        Preferences prefs = Preferences.userNodeForPackage(PreferencesDemo.class);
        prefs.put("Location", "Oz");
        int count = prefs.getInt("Count", 0);
        prefs.putInt("Count", ++count);
        System.out.println(prefs.get("Count", 0));  // each time you run this program, count will be increased
    }
}
```
- `userNodeForPackage()` and `systemNodeForPackage()` are quite the same.
- You don't need to use the current class as the node identifier, but that's the usual practice.
- Once you create the node, it's available for either loading or reading data. The second argument of `get()` is the default value.
- The Preferences API uses appropriate system resource to accomplish its task (storing the data), and these will vary depending on the OS. In Windows, the registry is used.

# Objects
- One of the challenges of OOP is to create a one-to-one mapping between the elements in the problem space and objects in the solution space.
- The requests you can make of an object are defined by its interface, and the type is what determines the interface.
- One of the best ways to think about objects is as "service providers".
- In a good OO design, each object does one thing well, but doesn't try to do too much.
- It is helpful to break up the playing field into class creators and client programmers.

## Object Lifetime

### Dynamic Memory Allocation
- Java uses dynamic memory allocation, exclusively (except for primitive types). Every time you want to create an object, you use the new operator to build a dynamic instance of that object.
- In dynamic approach, you don't know until run time how many objects you need, what their lifetime is, or what their exact type is.
- At run time, the amount of time required to allocate storage on the heap can be noticeably longer than the time to create storage on the stack.
- The dynamic approach makes the generally logical assumption that objects tend to be complicated, so the extra overhead of finding storage and releasing that storage will not have an important impact on the creation of an object.

### Garbage Collector
- If you create it on the heap the compiler has no knowledge of its lifetime. Java provides a garbage collector to automatically discover when an object is no longer in use and destroy it.
- The storage of an object never gets released unless your program nears the point of running out of storage.
- If your program completes and the garbage collector never gets around to releasing the storage, the storage will be returned to the operating system en masse as the program exits.
- Garbage collection is only about memory.
- The GC can have a significant impact on increasing the speed of object creation. Allocating storage for heap objects in Java can be nearly as fast as creating storage on the stack in other languages. There is a little extra overhead for bookkeeping, but it is nothing like searching for storage.
- The garbage collector rearranges things and makes it possible for the high-speed, infinite-free-heap model to be used while allocating storage.
- The garbage collector does add a runtime cost, the expense of which is difficult to put into perspective because of the historical slowness of Java interpreters.

## Singly Rooted Hierarchy
- All classes should ultimately be inherited from a single base class in virtually all OOP languages except for C++.
- All objects can easily be created on the heap, and argument passing is greatly simplified.
- A singly rooted hierarchy makes it much easier to implement a garbage collector. You'll never end up with an object whose type you cannot determine.

## Initialization and Creation
- In Java, creation and initialization are unified concepts -- you cannot have one without the other.
- Primitive field is garanteed to get a default value. In the case of a method's local variables, this guarantee comes in the form of a compile-time error.
- You can assign the value at the point you define the variable in the class.
```java
public class MethodInit{
    Depth d = new Depth();
    // int j = g(i); is illegal forward reference, i has not been initialized
    int i = f();    // methods can be used
    int j = g(i);
    int f() { return 1; }
    int g(int n) { return  10*n; }
}
```
Every object of type MethodInit will get these same initialization values.

- The order of initializtion is determined by the order that the variables are defined within the class. The variables are initialized before any methods can be called -- even the constructor. But if some field use a method as initialization, the fields in that method will be initialized first.
- The instance initialization is necessary to support the initialization of anonymous inner classes.
- Array elements of primitive types are automatically initialized to "empty" values.
- Four ways to initialize the references:
 - At the point the objects are defined (always initialized before the constructor).
 - In the constructor.
 - Lazy initialization: right before actually needed.
 - Using instance initialization.
- In more traditional languages, programs are loaded all at once. The process of initialization in these languages must be carefully controlled so that the order of initialization of statics doesn't cause trouble.
- Java doesn't have this problem. The compiled code for each calss exists in its own separate file. That file isn't loaded until the code is needed.
- All the `static` objects and the `static` code block will be initialized in textual order.
 
### Creation
- Order of creating an object:
 - The first time an object is created or its static method or field is accessed, the Java interpreter must locate the `.class`.
 - All `static` initializers are run.
 - The construction process allocates enough storage for the object.
 - The storage is wiped to zero.
 - Initializations that occur at the point of field definition are executed (including instance initialization).
 - Constructors are executed.
- Order of constructor calls for a complex object:
 - The base-class constructor is called.
 - Member initializers are called in the order of declaration.
 - The body of the derived-class constructor is called.
- You must be able to assume that all the members of the base class are valid when you're in the derived class.
- In the process of loading a class, if the loader notices that it has a base class, it will load it then. This will happen whether or not you're going to make an object of that base class. Next, the `static` initialization in the root base class is performed, and then the next derived class, and so on.
- The `static` initialization (including explicit static initialization) occurs only if it is necessary (creating an object, or referring to the static fields). These code is guaranteed to be excuted only once.
```java
package com.jiangtao.object;
public class Test1 {
	public static int k = 0;
	public static Test1 t1 = new Test1("t1");
	public static Test1 t2 = new Test1("t2");
	public static int i = print("i");
	public static int n = 99;
	public int j = print("j");
	{
		print("构造块");
	}
	static {
		print("静态块");
	}

	public Test1(String str) {
		System.out.println((++k) + ":" + str + "   i=" + i + "    n=" + n);
		++i;
		++n;
	}

	private static int print(String str) {
		System.out.println((++k)+":"+str+"   i="+i+"   n="+n);
		++n;
		return ++i;
	}

	public static void main(String[] args) {
        Test1 t=new Test1("init");
	}
}
```
outputs as:
```
1:j   i=0   n=0
2:构造块   i=1   n=1
3:t1   i=2    n=2
4:j   i=3   n=3
5:构造块   i=4   n=4
6:t2   i=5    n=5
7:i   i=6   n=6
8:静态块   i=7   n=99
9:j   i=8   n=100
10:构造块   i=9   n=101
11:init   i=10    n=102
```

## Finalization and Cleanup
- Suppose your object allocates special memory without using `new`. The garbage collector only knows how to release memory allocated with `new`. And `finalize()` performs some important cleanup at the time of garbage collection.
- Your objects might not get garbage collected, and garbage collection is not destruction.
- Finalization is needed to special cases in which your object can allocate storage in some way other than creating an object. This can happen primarily through native methods, which are a way to call non-Java code from Java.
- If you want some kind of cleanup performed other than storage release, you must still explicitly call an appropriate method in Java. Remember that neigher garbage collection nor finalization is guaranteed.
- `System.gc()` is used to force finalization.
- `finalize()` can be used to discover some portion of the object that should be under some condition before cleaned up.
- You should generally assume that the base-class version of `finalize()` will also be doing something important:
```
protected void finalize(){
    super.finalize();
    ...
}
```

### Proper Cleanup
- If you want something cleaned up for a class, you must guard against an exception by putting such cleanup in a `finally` clause:
```
try{
    // Code and exception handling...
}finally{
    // cleanup
}
```
- The `try` indicactes a guarded region. The code in the `finally` clause following this region is always executed, no matter how the try block exits.
- The cleanup method should first perform all of the cleanup work in the reverse order of creation, then call the base-class cleanup method:
```
public void dispose(){
    t.dispose();
    c.dispose();
    for(int i=lines.length-1; i>=0; i--)
        lines[i].dispose();
    super.dispose();
}
```
- Reference counting may be necessary to keep track of the number of objects that are still accessing a shared object:
```
class Shared{
    private int refcount = 0;
    public void addRef() { refcount++; }
    protected void dispose(){
        if(--refcount == 0)
            // cleanup
    }
}
```

## Storage

### Register
- In Java, you don't have direct control, nor do you see any evidence in your programs that registers even exist.

### Stack
- While some Java storage exists on the stack – in particular, object references (and primitive-type objects) – Java objects themselves are not placed on the stack.

### Heap
- Unlike the stack, the compiler doesn't need to know how long that storage must stay on the heap.
- It may take more time to allocate and clean up heap storage than stack storage.

### Constant Storage
- Sometimes constants are cordoned off by themselves so that they can be optionally placed in read-only memory, in embedded system. An example of this is the string pool. All literal strings and string-valued constant expressions are interned automatically and put into special static storage.

### Non-RAM Storage
- The two primary examples are streamed objects and persistent objects.
- These types of storage is turning the objects into something that can exist on the other medium, and yet can be resurrected into a regular RAM-based object when necessary.

# String
- The size of `char` is two bytes, to support Unicode characters.
- Java string uses UTF-16 encoding, which is not compatible with ASCII.
- When you call the `String(byte[])` constructor you ask Java to convert the `byte[]` to a String using the platform default encoding. Since the platform default encoding is usually a 1-byte encoding such as ISO-8859-1 or a variable-length encoding such as UTF-8, it can easily convert that 1 byte to a single character.
- When converting between `byte[]` and `char[]`/`String` or between `InputStream` and `Reader` or between `OutputStream` and `Writer`, you should always specify which encoding you want to use. If you don't, then your code will be platform-dependent.
- If an expression begins with a `String`, then all operands that follow must be `String`.
```
x = y = z = 1;
System.out.println(""+x+y+z);    // 111 rather than 3
```
- Objects of the `String` class are immutable. Every method that appears to modify it actually creates and returns a brand new `String` object.
- If the contents don't need to changing, the method will just return a reference to the original `String`.
- `String str = "xxx"` is in constant segment; `String str = new String("XXX")` is in heap:
```
String str1 = new String("abc");
String str2 = new String("abc");
System.out.println(str1==str2); // false
```
- When the `intern()` method is invoked, if the pool already contains a string equal to this String object as determined by the equals(Object) method, then the string from the pool is returned. Otherwise, this String object is added to the pool and a reference to this String object is returned.
- `trim()` returns a copy of the string, with leading and trailing whitespace (including `'\n'`, `'\t'`, `'\r'`, `'\v'`, ...) omitted.

## StringBuilder
- Concatenated overloaded '+' will create a lot of objects and cause efficiency issues. The compiler will use `StringBuilder` and call `toString()` to replace it.
- If looping is involved, the compiler may produce on `StringBuilder` for each loop:
```
String result = "";
for(int i=0; i<fields.length; i++){
	result += fields[i];	// create one StringBuilder each loop
}
return result;
```
```
StringBuilder result = new StringBuilder();
for(int i=0; i<fields.length; i++){
	result.append(fields[i]);
}
return result.toString();
```
- When creating your own `toString()` method, if it's simple you can generally rely on the compiler to build the result in a reasonable fashion. But if not (like containing loops) you should wcplicitly use a `StringBuilder` in your `toString()`.
- `StringBuilder` was introduced in Java SE5. Prior to this, Java used `StringBuffer`, which ensured thread safety but was significantly more expensive.

## toString()
- Refering to `this` in `toString()` may cause unintended recursion. You'd say `super.toString()`.

## Formatting Output
- `System.out.format()` is introduced in Java SE5, available to `PrintStream` or `PrintWriter` objects.
```
System.out.format("%d %f", x, y);
System.out.printf("%d %f", x, y);	// equivalent
```

### Formatter
- You can think of `Formatter` as a translator that converts your format string and data into the desired result.
```
PrintStream outAlias = System.out;
Formatter formatter = new Formatter(outAlias);
formatter.format("%d %f", 1, 1.2);
```
- Format specifiers: `%[argument_index$][flags][width][.precision]conversion`
 - By default the data is right justified, but this can be overridden by including a '-' in the flages section
 - For `String`s, the precision specifies the maximum number of characters; for floating point numbers, precision specifies the number of decimal places, **rounding** if there are too many or pending zeroes if too few.
 - Conversions are invalid for particular variable types, executing them will trigger an exception.
 -'h' for conversion prints Hash code as hex. For non-`Boolean` argument types, 'b' will print `true` if it is not `null`, even the numeric value of zero.
- `String.format()` is a `static` method. It instantiates a `Formatter` and pass your arguments to it and returns a `String`.
- A `DecimalFormat` comprises a pattern:
```
DecimalFormat f = new DecimalFormat("#,##0.00;(#,##0.00)");
double d = 1.125;
System.out.println(f.format(d));
```
where `0` represents digit; `#` represents digit with zero shown as absent; `.` represents decimal separator or monetary decimal separator; `-` represents minus sign; `,` represents grouping separator; and `;` separates positive and optional negative subpatterns (in this case, negative numbers will be surrounded by `()` without `-`). You can use `setRoundingMode()` to change rounding mode. The default value is `RoundingMode.HALF_EVEN`:
```
DecimalFormat f = new DecimalFormat("#,##0.00");
System.out.println(f.format(1.125)); // 1.12
System.out.println(f.format(1.135)); // 1.14
```

## Regular Expression

### Basics
- '\\\\' means regular expression backslash. If you want to insert a literal backslash, use '\\\\\\\\' (first parse to regex '\\\\', second parse to '\\')
- '\t' and '\\t' can both be compiled to tab in regular expression. The first one is escaped to '<tab>' before regex compilation, and the second one is first escaped to '\t'.
- A complete list of constructs for building regular expressions can be found in the JDK documentation for **java.util.regex.Pattern**
| Character | Meaning |
| - | - |
| . | any character|
| ? | one or none |
| + | one or more|
| * | any times including none |
| \| | or |
| ^ | begin of a line |
| $ | end of a line |
| [abc] | any of the characters abc |
| [^abc] | any character except abc |
| [a-c] | range |
| [abc[def]] | union |
| [a-z&&[abc]] | intersection |
| {} | copies of previous regex |
| d | digits |
| D | non-digit character |
| w | word character |
| W | non-word characters|
| s | whitespace character |
| S | non-whitespace character |
| b | word boundary |
| B | non-word boundary |
| G | end of the previous match |
```
`-?\\d+` // integer number.
"+911".matches("(-|\\+)?\\d+"); // true, () is needed
```
- `split()`, `replaceFirst()`, and `replaceAll()` are useful with regular expressions.
- Many regular expression operations take `CharSequence` arguments (interface for `CharBuffer`, `String`, `StringBuffer`, `StringBuilder`).
- `(?!)` means look-ahead assertion. `"(?!000|001)\\d{3}"` matches all three-digit string except "000" and "001".

### Quantifiers
- A quantifier describes the way that a pattern absorbs input text:
 - Greedy: keep going until it's matched the largest possible string;
 - Reluctant: matches the minimum number of characters necessary;
 - Possessive: prevent backtracking to prevent from running away and also make it execute more efficiently
| Greedy | Reluctant | Possessive | Matches |
| - | - | - | - |
| X? | X?? | X?+ | one or none |
| X*| X*? | X*+ | zero or more |
| X+ | X+? | X++ | one or more |
| X{n} | X{n}? | X{n}+ | exactly n time |
| X{n,} | X{n,}? | X{n,}+ | at least n times |
| X{n,m} | X{n,m}? | X{n,m}+ | n to m times |

### Pattern and Matcher
- In general, you'll compile regular expression objects rather than using the fairly limited `String` utilities:
```
import java.util.regex.*;
public class Test{
    public static void main(String[] args){
        Pattern p = Pattern.compile("a+");
        Matcher m = p.matcher("aaaabbaa");
        while(m.find()){
            // m.group() shows the matched part
            // m.start() is the index of head of the last match (including matches(), lookingAt(), find())
            // m.end()-1 is the index of tail of the last match (including matches(), lookingAt(), find())
			// m.matches() is successful if the pattern matches the entire input
			// m.lookingAt() is successful if the pattern matches the beginning part of the input
			// m.find(int) tells the character position and resets the search position
        }
    }
}
```
- Groups are regular expressions set off by parentheses that can be called up later: `A(B(C))D` has three groups, group 0 is `ABCD`, group 1 is `BC`, group 2 is `C`. `group(int)` returns the given group number during the previous match operation. If the match was successful, but the group failed to match any part, then `null` is returned.
- Invoking `start()` or `end()` following an unsuccessful matching operation produces an `IllegalStateException`.
- Pattern flags:
| Compile Flag | Effect |
| - | - |
| Pattern.CASE_INSENSITIVE (?i) | match without regard to case |
| Pattern.COMMENTS (?x) | whitespace and embedded comments starting with # are ignored |
| Pattern.DOTALL (?s) | `.` includes a line terminator |
| Pattern.MULTILINE (?m) | not taking the entire input as one line for `^` and `$` |
| Pattern.UNICODE_CASE (?u) | CASE_INSENSITIVE for Unicode |
| Pattern.UNIX_LINES (?d) | only the '\n' line terminator is recognized in the behavior of `.`, `^`, and `$` |
```
// equivalent expressions
Pattern p = Pattern.compile("^java", Pattern.CASE_INSENSITIVE | Pattern.MULTILINE);
Pattern p = Pattern.compile("(?i)(?m)^java");
Pattern p = Pattern.compile("(?im)^java");
```
- `split()` divides an input string into an array of `String` objects:
```
Pattern.compile("!!").split("a!!b!!c!!d", 3);	// max number of splits is 3: [a, b, c!!d]
```
- `appendReplacement(StringBuffer, String)` performs step-by-step replacements. It allows you to call methods and perform other processing (`replaceFirst()` and `replaceAll()` are only able to put in fixed strings).
```
StringBuffer sbuf = new StringBuffer();
while(m.find())
	m.appendReplacement(sbuf, someChange(m.group()));	// incrementally replace the matches and keep the unmatched scaned part
m.appendTail(sbuf);	// put in the remainder
```
It also allows you to refer to captured groups directly in the replacement string by saying "$g", where 'g' is the group number.

- An existing `Matcher` object can be applied to a new character sequence using the `reset()` methods.
```
Matcher m = p.matcher("");
for(String line : new TextFile(fileName)){
	m.reset(line);
	while(m.find()){
		// ...
	}
}
```

## Scanning Input
- `Scanner` is an interface introduced in Java SE5 to describe "something that has a `read()` method". A plain `next()` returns the next `String` token, and there are "next" methods for all primitive types (except `char`) as well as for `BigDecimal` and `BigInteger`. There are corresponding `hasNext` methods.
- One of the assumptions made by the `Scanner` is that an `IOException` signals the end of input, and so these are swallowed by the `Scanner`. The most recent exception is avilable through the `ioException()` method, so you are able to examine it.
- By default, a `Scanner` splits input tokens along whitespace. `useDelimiter()` can be used to change the dilimiter.
- The pattern is matched against the next input token only, so if your pattern contains a delimiter it will never be matched.

# Type Information
- In Java, every cast is checked. If it isn't the type you think it is, you will get a `ClassCastException`.
- Traditional RTTI assumes that you have all the types available at compile time. The reflection mechanism allows you to discover and use class information solely at run time.
```
List<Shape> shapeList = Arrays.asList(new Circle(), new Square(), new Triangle());
```
The container is actually holding everything as an `Object`. At compile time, the container and the Java generic system enforces the elements to be `Shape`. At run time, the automatical cast ensures it.

- The intent of OO programming is to use polymorphic method calls everywhere you can, and RTTI only when you must.
- Putting a new feature in a base class might mean that, for the benefit of one particular class, all of the other classes derived from the base require some meaningless stub of a method. This makes the interface less clear. You can place the method in the specific class where it's appropriate, and use RTTI to get that feature.
- When one of your objects reacts to the general-purpose code in the base class in a horribly inefficient way, you can pick out that type using RTTI and write case-specific code to improve the efficiency.
- RTTI needs exception handling. Of course it's worth trying to write code that can be statically checked when you can. But there are always things that can happen at run time and throw exceptions. And the existence of a consistent error-reporting model empowers us to write dynamic code using reflection. That's on of the important facilities that separate Java from languages like C++.

## The Class Object
- There is one `Class` object for each class that is part of your program. To make an object of that class, the JVM uses a subsystem called a class loader.
- There is only one primordial class loader, which is part of the JVM implementation. The primordial class loader loads trusted classes, including Java API classes.
- If you have special needs (such as loading classes in a special way to support Web server applications or downloading classes across a network), then you have a way to hook in additional class loaders.
- All classes are loaded into the JVM dynamically upon the first use of a class. Note that the constructor is also a `static` method.
- The class loader first checks to see if the `Class` object is loaded, then finds the **.class** file with that name. After the bytes for the class are loaded, they are verified to ensure that they have not been corrupted and do not comprise bad Java code.
- Once the `Class` object is in memory, it is used to create all objects of that type.
```
try{
	Class.forName("xxx");	// static method
} catch(ClassNotFoundException e){
	// ...
}
```
- You can fetch the `Class` reference by calling a method that's part of the `Object` root class: `getClass()`.
- The `newInstance()` method of `Class` is a way to implement a "virtual constructor". You will get an `Object` reference pointing to an object of the target type. The class must have a default constructor. If not, you will get an exception in runtime. Java has no way to assert such a thing at compile time. A common suggestion is to define a factory interface that has a method that generates object.

### Class Literal
- It's not only a simpler, but also safer way to produce the reference to the `Class` object, since it't checked at compile time (no need to be placed in a `try` block). Because it eliminates the `forName()` method call, it's also more efficient: `SomeType.class`.
- There is a standard field called `TYPE` that exists for each of the primitive wrapper classes: 'int.class` is equivalent to `Interger.TYPE`.
- `.class` doesn't automatically initialize the `Class` object. There are three steps in preparing a class for use:
1. Loading: performed by the class loader, creates a `Class` object from the bytecodes.
2. Linking: verifies the bytecodes and allocates storage for `static` fields.
3. Initialization: initialize the superclass, execute `static` initializer and `static` initialization block.
- Initialization is delayed until the first reference to a `static` method or to a non-compile-time constant:
```
class Initable{
	static final int staticFinal = 47;	// reference will not trigger initialization
	static final int staticFinal2 = ClassInitialization.rand.nextInt(1000);	// reference will trigger initialization
}
```
- If you get the superclass, the compiler will only allow you to say that the superclass reference is "some class that is a superclass of the it":
```
Class<? super FancyToy> up = FancyToy.class.getSuperclass();
// Class<Toy> up2 = FancyToy.class.getSuperclass(); will not compile
```
The method do return a `Toy`, but compiler doesn't know the specific type of `FancyToy`'s superclass.

## Casting
- Java SE5 adds a casting syntax. It's useful where you can't just use a ordinary cast. This usually happens when you are writing generic code, and you've stored a `Class` reference that you want to use to cast with at a later time.
```
Building b = new House();
Class<House> houstType = House.class;
// equivalent casts
House h = houseType.cast(b);
h = (House) b;
```

### Checking Before a Cast
- The classic cast uses RTTI (C++ does not) to make sure the cast is correct and will throw a `ClassCastException` if not.
- The compiler will not allow you to perform a downcast assignment without using an explicit cast since it only sees the superclass type.
- `instanceof` is the third form of RTTI in Java (other than classic cast and `Class`):
```
if(x instanceof Dog)
	((Dog)x).bark();
```
Note that it produces `false` if the left-hand argument is `null`.

- `@SupressWarnings` annotation can be placed onto a method to suppress the compile-time warning for casting:
```
try{
	for(String name : typeNames)
		types.add((Class<? extends Pet>)Class.forName(name));	// no way for compiler to know whether this is legal
} catch(ClassNotFoundException e){
	throw new RuntimeException(e);
}
```
- You can compare it to a named type only, and not to a `Class` object with `instanceof`. But this is not as great a restriction as you might think, since your design is probably flawed if you end up with writing a lot of `instanceof` expressions.
- The `Class.isInstance()` method provides a way to dynamically test the type of an object:
```
for(Map.Entry<Class<? extends Pet>,Integer> pair : entrySet())
	if(pair.getKey().isInstance(pet))
		put(pair.getKey(), pair.getValue()+1);
```
- We can use `Class.isAssignableFrom()` (reverse of `Class.isInstance()`) to create a general-purpose tool to count the number of objects of a type:
```
// using a HashMap to record the counts
public class TypeCounter extends HashMap<Class<?>,Integer>{
	private Class<?> baseType;
	public TypeCounter(Class<?> baseType){
		this.baseType = baseType;
	}
	public void count(Object obj){
		Class<?> type = obj.getClass();
		if(!baseType.isAssignableFrom(type))
			throw new RuntimeException();
		countClass(type);
	}
	private void countClass(Class<?> type){
		Integer quantity = get(type);
		put(type, quantity == null ? 1: quantity+1);
		Class<?> superClass = type.getSuperclass();
		if(superClass!=null && baseType.isAssignableFrom(superClass))	// counting recursively
			countClass(superClass);
	}
}
```
Note that there is no need to know about the hierarchy of that base type.

### Generic Class Reference
- To constrain the type the `Class` object that the `Class` reference is pointing to:
```
Class<Interger> genericIntClass = int.class;
// genericIntClass = double.class; is illegal
// Class<Number> genericNumberClass = int.class; is also illegal
```
- Wildcard is used to loosen the conatraints:
- In Java SE5, `Class<?>` is preferred over plain `Class` because it indicates that you are not just using a non-specific class reference by accident. It doesn't produce a compiler warning in:
```
Class<?> intClass = int.class;
```
- The generic syntax to `Class` references is only to provide compile-time type checking.
```
Class<? extends Number> bounded = int.class;
```

### instanceof vs. Class Equivalence
- `instanceof` concerns about the inheritance, while `Class` equivalence compares the actual `Class` object.
- There is only one `Class` object for each type, so `==` and `.equals()` are exactly the same.

## Reflection
- There is still limitation of RTTI, the type must be known at compile time in order for you to detect it and do something useful. In a larger programming world, you will need to know information about the objects that is not even available to you at compile time:
1. Component-based programming: Java provides a structure for it through JavaBeans. e.g. Integrated Development Environment.
2. Remote Method Invocation: it allows a Java program to have objects distributed across many machines.
- The true difference between RTTI and reflection is that with RTTI, the compiler opens and examines the **.class** file at compile time; with reflection, the **.class** file is unavailable at compile time, it is opened and examined by the runtime environment.
```
import java.lang.reflect.*;
...
try{
	Class<?> c = Class.forName("XXX");
	Method[] methods = c.getMethods();	// show public methods
	Constructor[] ctors = c.getConstructors();	// show public constructors
} catch(ClassNotFoundException e){
	// ...
}
```
Note that the synthesized default constructor has the same access as the class.
```
interface Interface{
	void doSomething();
}
class RealObject implements Interface{
	public void doSomething() {}
}
class SimpleProxy implements Interface{
	private Interface proxied;
	public SimpleProxy(Interface proxied){
		this.proxied = proxied;
	}
	public void doSomething(){
		proxied.doSomething();
	}
}
```
- All calls made on a dynamic proxy are redirected to a single invocation handler, which has the job of discovering what the call is and deciding waht to do about it.
```
class DynamicProxyHandler implements InvocationHandler{
	private Object proxied;
	public DynamicProxyHandler(Object proxied){
		this.proxied = proxied;
	}
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable{
		// you can filter for certain method calls
		// if( some filter for method or args)
			// ...
		return method.invode(proxied, args);	// reflection for proxied.method(args)
	}
}
```

### Null Objects
- Sometimes checking for `null` is fine, and sometimes you can reasonably assume that you won't encounter `null`, and sometimes even detecting aberrations via `NullPointerException` is acceptable.
- Even though the Null Object will respond to all messages that the "real" object will respond to, you still need a way to test for nullness. The simplest way to do this is to create a tagging interface: `public interface Null{}`. This allows `instanceof` to detect the Null Object, and more importantly, does not require you to add an `isNull()` method to all your classes.
- In general, the NULL Object will be a Singleton. You have the option of detecting the generic `Null` or the more specific `NullPerson` using `instanceof`, but with the Singleton approach you can also just use `equals()` or `==` to compare to `Person.NULL`.
```
public Person{
	public final name;
	public Person(String name){
		this.name = name;
	}
	public String toString(){
		return name;
	}
}
public static class NullPerson extends Person implements Null{
	private NullPerson() { super("None"); }
	public String toString { return "NullPerson" }
}
public static final Person NULL = new NullPerson();
```
```
if(person == null)
	person = Person.NULL;
```
- If you are working with interfaces instead of concrete classes, it's possible to use a `DynamicProxy` to automatically create the Null Objects.
```
class NullRobotProxyHandler implements InvocationHandler{
	private Robot proxied = new NRobot();
	NullRobotProxyHandler(Class<? extends Robot> type){
		// ...
	}
	private class NRobot implements Null, Robot{
		// ...
	}
	public Object invoke(Object proxy, Method method, Object[] args) throw Throwable{
		return method.invoke(proxied, args);
	}
}
public class NullRobot{
	public static Robot newNullRobot(Class<? extends Robot> type){
		return (Robot)Proxy.newProxyInstance{	// java.lang.reflect.proxy
			NullRobot.class.getClassLoader(),
			new Class[]{ Null.class, Robot.class },
			new NullRobotProxyHandler(type));
		}
	}
	public static void main(String[] args){
		Robot[] bots = {
			new SnowRemovalRobot(),
			newNullRobot(SnowRemovalRobot.class)	// proxy
		};
	}
}
```
The proxy fulfills the requirements of the `Robot` and `Null` interfaces, and is related to the specific name of the type that it proxies. (simple NULL just relates to the base type).

- Logical variations of the Null Object are Mock Object and the Stub. Mock Objects tend to be lightweight and self-testing; Stubs just return stubbed data, are typically heavyweight and are often reused between tests.

### Interfaces and Type Information
- An improtant goal of `interface` keyword is to allow the programmer to isolate components. But with type information it's possible to get around that --- interfaces are not airtight guarantees of decoupling. We can call methods that is not in the interface.
```
interface A{}
class B implements A{
	public void f() {}
}
public class InterfaceViolation{
	public static void main(String[] args){
		A a = new B();
		// a.f(); compile error
		if(a instanceof B){
			B b = (B)a;
			b.f();
		}
	}
}
```
- The easiest approach is to use package access of the implementation, so the client outside the package may not see it:
```
class B implements A{
	// ...
}
```
- But it's still possible to reach in and call all of the methods using reflection, even `private` ones.
```
static void callHiddenMethod(Object a, String methodName) throws Exception{
	Method g = a.getClass().getDeclaredMethod(methodName);
	g.setAccessible(true);
	g.invoke(a);
}
static void changeHiddenVariable(Object a, String variableName) throws Exception{
	Field f = a.getClass().getDeclaredField(variableName);
	f.setAccessible(true);
	f.set(o, "XXX");	// suppose it's String
	f.setInt(o, 123);	// suppose it's integer
}
```
- Distributing compiled code is no solution, `javap -private C` will print all members; private inner class and anonymous class don't hide anything from reflection either.
- `final` fields are actually safe from change. The runtime system accepts any attempts at change without complaint, but nothing actually happens.
- The fact that you always have a back door into a class may allow you to solve certain types of problems that could otherwise be difficult or impossible.

# Web

## Client/Server
- A key to the client/server concept is that the repository of information is centrally located so that it can be changed and so that those changes will propagate out to the information consumers.
- Programmers offload processing tasks, to minimize latency, often to the client machine, but sometimes to other machines at the server site, using so-called middleware. (It is also used to improve maintainability).

### Client-side Programming
- Web browser incorporates the ability to run programs on the client end, called client-side programming.
- Basic HTML contains simple mechanisms for data gathering as well as a button that could only be program- med to reset the data on the form or "submit" the data on the form back to the server through the Common Gateway Interface.
- The value of the plug-in for client-side programming is that it allows an expert programmer to develop extensions and add those extensions to a browser without the permission of the browser manufacturer.
- With a scripting language, you embed the source code for your client-side program directly into the HTML page, and the plug-in that interprets that language is automatically activated while the HTML page is being displayed. They load very quickly. - The trade-off is that your code is exposed for everyone to see (and steal).
- Java allows client-side programming via the applet and with Java Web Start. The applet is downloaded automatically as part of a Web page.
- The .NET platform is roughly the same as the Java Virtual Machine and Java libraries, and C# bears unmistakable similarities to Java.
- Intranets provide much greater security than the Internet. If your program is running on the Internet, you need something cross-platform and secure. If you're running on an intranet, you might have a different set of constraints. The most sensible approach to take is the shortest path that allows you to use your existing code base, rather than trying to recode your programs in a new language.

### Server-side Programming
- Java-based Web servers allow you to perform all your server-side programming in Java by writing what are called servlets.
- Servlets and their offspring, JSPs, eliminate the problems of dealing with differently abled browsers.

# Others

## Name Visibility
- The language prevents name clashes for you. The Java creators want you to use your Internet domain name in reverse to produce an unambiguous name for a library.
- `import` tells the compiler to bring in a package, which is a library of classes.
- `java.lang` is implicitly included in every Java code file.
- When you are creating a standalone program, one of the classes in the file must have the same name as the file. That class musst contain a method called `main()` with this signature and return type: `public static void main(String[] args)`.
- In order to use a library, usually you should add the root directory of it to CLASSPATH. You must put the actual name of the JAR file in the classpath, not just the path where it's located.
- Java will not always look at the current directory. If you don't hava a `.` as one of the paths in the CLASSPATH, Java won't look there.
- If the names collide, the compiler complains and forces you to be explicit.
- `import static` imports static methods and fields.

### Package
- A package contains a group of classes, organized together under a single namespace. Compilation units with no package names are in the "unnamed" or dafault package.
- Conditional compilation can be accomplished by changing the package that's imported in order to change the code used in the program from the debug version to the production version.
- While compiling, you should make a directory corresponding to the package name. Or use `javac -d` to generate such directory. You should turn to the project directory (father of the root of the package directory) to run: `java ./package/path/PublicClassName`

## Compiling and Running
- IBM's "jikes" compiler is significantly faster than Sun's javac (although if you are building groups of files using Ant, there is not too much of a difference).
- Each compilation unit must have a name eding in **.java**, and inside the unit there can be a `public` class that must have the same name as the file. There can be only one `public` class in each compilation unit.
- After compilation, you get a **.class** file for each class in the **.java** file. A working program is a bunch of **.class** files, which can be packaged and compressed into a Java ARchive (JAR) file. The Java nterpreter is responsible for finding, loading, and interpreting these files.
- The `package` and `import` keywords allow you to do, as a library designer, to divide up the single global namespace so you won't have clashing names.
- The Java interpreter proceeds as follows:
 - Find the environment variable CLASSPATH.
 - Starting from the root, take the package name and replace each dot with a slash to generate a path name.
 - Concatenate the path name to the various entries in the CLASSPATH, look for the `.class` file.
- You can decompile the code using `javap -c` to produce the JVM bytecodes.

## Comments and Embedded Documentation
- Javadoc extract the comments and put them into an HTML file. You can write your own Javadoc handlers, called doclets.

### Syntax
- All of the Javadoc commands occur only within `/**` comments.
 - Standalone doc tags are commands that start with an `@` and are placed at the beginning of a comment line. (A leading `*` is ignored).
 - Inline doc tags can appeat anywhere within a Javadoc comment and also start with an `@` but are surrounded by curly braces.
```
/**
 * @deprecated  As of JDK 1.1, replaced by {@link #setBounds(int,int,int,int)}
 */
```
- Javadoc will process comment documentation for only `public` and `protected` members. Comments for `private` and package-access members are ignored.
- Embedded HTML allows you full use of HTML; however, the primary motive is to let you format code.
- Within the documentation comment, asterisks at the beginning of a line are thrown away by Javadoc, along with leading spaces.

# Reference
[1]	Thinking in Java Ed.4
[2]	
