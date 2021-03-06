[TOC]

# Introduction
- You might think that the intent of archetectural patterns would be something like "to design buildings." But Alexander makes it clear that the intent of architectural patterns is to serve and inspire people who will occupy buildings and towns.
- It's very easy to fall into a habit of thinking that the particular algorithm or piece of code that you happen to partly or thoroughly understand is antually going to be the bottleneck in your system. But unless you've run actual tests, typically with a profiler, you can't really know what's going on. And even if you are right, that a piece of code is very inefficient, unless the piece of code you're thinking about happens to fall into that 10% part that cost 90% time, it isn't going to be important.
- We should forget about small efficiencies, say about 97% of the time: Premature optimization is the root of all evil. (Donald Knuth)
- The GoF patterns often have a "context object" that the client programmer interacts with.
- The context object often acts as a little facade to hide the complexity of the rest of the pattern, and in addition it will often be the controller that manages the operation of the pattern.
- The context object allows you to use the pattern in a composition (rather than inheritance), and that may be it's primary value.
- The basic concept of a pattern can be seen as the basic concept of program design: adding a layer of abstraction. One of the compelling motivations behind this is to separate things that change from things that stay the same.
- The most difficult part of developing an elegant and cheap-to-maintain design is in discovering "the vector of change" (vector refers to the maximum gradient), or put another way, discovering where your greatest cost is.
- The goal of design patterns is to isolate changes in your code. So in fact, inheritance and composition themselves can also be considered patterns.
- In Python, all the exceptions appeared, none were accidentally "disappeared". If you want to catch an exception, you can, but you are not forced to write reams of code all the time. They go up to where you want to catch them, or go all the way out if you forget. But they don't vanish, which is the worst of all possible cases. In C++, exception is not the exclusive approach. And in Java, programmers usually ignore some exceptions because of the annoyances.

# Strategy
Creating a method that behaves differently depending on the argument object that you pass it.
```
class Processor{
    public String name() { return getClass().getSimpleName(); }
    Object process(Object input) { return input; }
}
class Upcase extends Processor{
    String process(Object input) { return ((String)input).toUpperCase(); }
}
class Downcase extends Processor{
    String process(Object input) { return ((String)input).toLowerCase(); }
}
public class Apply{
    public static void process(Processor p, Object s){
        System.out.println(p.process(s));
    }
    public static String s = "abc";
    public static void main(String[] args){
        process(new Upcase(), s);
        process(new Downcase(), s);
    }
}
```

# Adapter
The adapter constructor takes the interface that you have, and produces an object that has the interface you need.
```
class FilterAdapter implements Processor{
    Filter filter;
    public FilterAdapter(Filter filter){
        this.filter = filter;
    }
    public String name() { return filter.name(); }
    public Waveform process(Object input){
        return filter.process((Waveform)input);
    }
}
public class FilterProcessor{
    public static void main(String[] args){
        Waveform w = new Waveform();
        Apply.process(new FilterAdapter(new LowPass(1.0)), w);
        Apply.process(new FilterAdapter(new HighPass(2.0)), w);
    }
}
```

## Adapter Method
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

# Decorator
- A decorator must have the same interface as the object it decorates, but the decorator can also extend the interface.
- Decorators give you much more flexibility while you're writing a program, but they add complexity to your code.

# Factory Method
```
interface Service {}
interface ServiceFactory {}
class Implementation1 implements Service {
    Implementation1() {}    // Package access
}
class Implementation2 implements Service{
    Implementation2() {}
}
class Implementation1Factory implements ServiceFactory{
    public Service getService(){
        return new Implementation1();
    }
}
class Implementation2Factory implements ServiceFactory{
    public Service getService(){
        return new Implementation2();
    }
}
public class Factories{
    public static void serviceConsumer(ServiceFactory fact){
        Service s = fact.getService();  // No need to know the exact type anywhere
        // some action
    }
    public static void main(String[] args){
        serviceConsumer(new Implementation1Factory());
        serviceConsumer(new Implementation2Factory());
    }
}
```
The constructors can be `private` via inner class:
```
class Implementation1 implements Service{
    private Implementation1() {}
    public static ServiceFactory factory =
        new ServiceFactory(){
            public Service getService() {
                return new Implementation1();
            }
        };
    }
}
class Implementation2 implements Service{
    private Implementation2() {}
    public static ServiceFactory factory =
        new ServiceFactory(){
            public Service getService() {
                return new Implementation2();
            }
        };
    }
}
```

# Flyweight
You use a flyweight when the ordinary solution requires too many objects, or when producing normal objects takes up too much space. Instead of everything in the object being contained within the object, some or all of the object is looked up in a more efficient external table (or produced through some other calculation that saves space).
```
public class Countries{
    // DATA is a public two-dimensional array
    public static final String[][] DATA = {
        {"ALGERIA","Algiers"},
        {"ANGOLA","Luanda"},
        {"CHINA","Beijing"},
        {"CUBA","Havana"},
        // ...
    };
    private static class FlyweightMap extends AbstractMap<String,String>{
        private static class Entry implements Map.Entry<String,String>{
            int index;
            Entry(int index) { this.index = index; }
            public boolean equals(Object o){
                return DATA[index][0].equals(o);
            }
            public String getKey() { return DATA[index][0]; }
            public String getValue() { return DATA[index][1]; }
            public String setValue(String value){
                throw new UnsupportedOperationException();
            }
            public int hashCode(){
                return DATA[index][0].hashCode();
            }
        }
        
        static class EntrySet extends AbstractSet<Map.Entry<String,String>>{
            private int size;
            EntrySet(int size){
                if(size < 0)
                    this.size = 0;
                else if(size > DATA.length)
                    this.size = DATA.length;
                else
                    this.size = size;
            }
            public int size() { return size; }
            private class Iter implements Iterator<Map.Entry<String,String>>{
                private Entry entry = new Entry(-1);
                public boolean hasNext(){
                    return entry.index < size-1;
                }
                public Map.Entry<String,String> next(){
                    entry.index++;
                    return entry;
                }
                public void remove(){
                    throw new UnsupportedOperationException();
                }
                public Iterator<Map.Entry<String,String>> iterator(){
                    return new Iter();
                }
            }
            private static Set<Map.Entry<String,String>> entries =
                new EntrySet(DATA.length);
            public Set<Map.Entry<String,String>> entrySet(){
                return entries;
            }
        }
        
        static Map<String,String> select(final int size){
            return new FlyweightMap(){
                public Set<Map.Entry<String,String>> entrySet(){
                    return new EntrySet(size);
                }
            };
        }
        static Map<String,String> map = new FlyweightMap();
        public static Map<String,String> capitals(){
            return map;
        }
        public static Map<String,String> capitals(int size){
            return select(size);
        }
        static List<String> names = new ArrayList<String>(map.keySet());
        public static List<String> names() { return names; }
        public static List<String> names(int size){
            return new ArrayList<String>(select(size).keySet());
        }
    }
}
```

### Proxy
- A proxy typically acts as a go-between. It can be helpful anytime you'd like to separate extra operations into a different place than the "real object", and especially when you want to easily change from not using the extra operations to using them.
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

### Decorator
- Decorators are often used when, in order to satisfy every possible combination, simple subclassing produces so many classes that it becomes impractical.
- A decorating class can also add methods, but as you shall see, this is limited.

### Template Method
- Functionality implemented in the base class uses one or more `abstract` methods defined in derived classes.

### Singleton
- Thread-safe singlegon:
```
public class Singleton { 

    private volatile static Singleton uniqueInstance;
 
    private Singleton() {}
        
    public static Singleton getInstance() { 
	if(uniqueInstance == null) {
        //只有第一次才彻底执行这里的代码
	   synchronized() {
	      //再检查一次
	      if(uniqueInstance == null)
		uniqueInstance = new Singleton();
   	   }
	}
         return uniqueInstance;
    }
}
```
