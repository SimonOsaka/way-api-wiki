## [Caffeine缓存](https://www.cnblogs.com/xingzc/p/9567159.html)

在本文中，我们来看看 [Caffeine](https://link.jianshu.com/?t=https%3A%2F%2Fgithub.com%2Fben-manes%2Fcaffeine) — 一个高性能的 Java 缓存库。

缓存和 Map 之间的一个根本区别在于缓存可以回收存储的 item。

回收策略为在指定时间删除哪些对象。此策略直接影响缓存的命中率 — 缓存库的一个重要特征。

Caffeine 因使用 Window TinyLfu 回收策略，提供了一个近乎最佳的命中率。

# 填充策略（Population）

Caffeine 为我们提供了三种填充策略：手动、同步和异步

## 手动加载（Manual）

```
Cache<String, Object> manualCache = Caffeine.newBuilder()
        .expireAfterWrite(10, TimeUnit.MINUTES)
        .maximumSize(10_000)
        .build();

String key = "name1";
// 根据key查询一个缓存，如果没有返回NULL
graph = manualCache.getIfPresent(key);
// 根据Key查询一个缓存，如果没有调用createExpensiveGraph方法，并将返回值保存到缓存。
// 如果该方法返回Null则manualCache.get返回null，如果该方法抛出异常则manualCache.get抛出异常
graph = manualCache.get(key, k -> createExpensiveGraph(k));
// 将一个值放入缓存，如果以前有值就覆盖以前的值
manualCache.put(key, graph);
// 删除一个缓存
manualCache.invalidate(key);

ConcurrentMap<String, Object> map = manualCache.asMap();
cache.invalidate(key);
```

Cache接口允许显式的去控制缓存的检索，更新和删除。

我们可以通过cache.getIfPresent(key) 方法来获取一个key的值，通过cache.put(key, value)方法显示的将数控放入缓存，但是这样子会覆盖缓原来key的数据。更加建议使用cache.get(key，k - > value) 的方式，get 方法将一个参数为 key 的 Function (createExpensiveGraph) 作为参数传入。如果缓存中不存在该键，则调用这个 Function 函数，并将返回值作为该缓存的值插入缓存中。get 方法是以阻塞方式执行调用，即使多个线程同时请求该值也只会调用一次Function方法。这样可以避免与其他线程的写入竞争，这也是为什么使用 get 优于 getIfPresent 的原因。

**注意**：如果调用该方法返回NULL（如上面的 createExpensiveGraph 方法），则cache.get返回null，如果调用该方法抛出异常，则get方法也会抛出异常。

可以使用Cache.asMap() 方法获取ConcurrentMap进而对缓存进行一些更改。

## 同步加载（Loading）

```
LoadingCache<String, Object> loadingCache = Caffeine.newBuilder()
        .maximumSize(10_000)
        .expireAfterWrite(10, TimeUnit.MINUTES)
        .build(key -> createExpensiveGraph(key));
    
String key = "name1";
// 采用同步方式去获取一个缓存和上面的手动方式是一个原理。在build Cache的时候会提供一个createExpensiveGraph函数。
// 查询并在缺失的情况下使用同步的方式来构建一个缓存
Object graph = loadingCache.get(key);

// 获取组key的值返回一个Map
List<String> keys = new ArrayList<>();
keys.add(key);
Map<String, Object> graphs = loadingCache.getAll(keys);
```

LoadingCache是使用CacheLoader来构建的缓存的值。

批量查找可以使用getAll方法。默认情况下，getAll将会对缓存中没有值的key分别调用CacheLoader.load方法来构建缓存的值。我们可以重写CacheLoader.loadAll方法来提高getAll的效率。

**注意**：您可以编写一个CacheLoader.loadAll来实现为特别请求的key加载值。例如，如果计算某个组中的任何键的值将为该组中的所有键提供值，则loadAll可能会同时加载该组的其余部分。

## 异步加载（Asynchronously Loading）

```
AsyncLoadingCache<String, Object> asyncLoadingCache = Caffeine.newBuilder()
            .maximumSize(10_000)
            .expireAfterWrite(10, TimeUnit.MINUTES)
            // Either: Build with a synchronous computation that is wrapped as asynchronous
            .buildAsync(key -> createExpensiveGraph(key));
            // Or: Build with a asynchronous computation that returns a future
            // .buildAsync((key, executor) -> createExpensiveGraphAsync(key, executor));

 String key = "name1";

// 查询并在缺失的情况下使用异步的方式来构建缓存
CompletableFuture<Object> graph = asyncLoadingCache.get(key);
// 查询一组缓存并在缺失的情况下使用异步的方式来构建缓存
List<String> keys = new ArrayList<>();
keys.add(key);
CompletableFuture<Map<String, Object>> graphs = asyncLoadingCache.getAll(keys);
// 异步转同步
loadingCache = asyncLoadingCache.synchronous();
```

AsyncLoadingCache是继承自LoadingCache类的，异步加载使用Executor去调用方法并返回一个CompletableFuture。异步加载缓存使用了响应式编程模型。

如果要以同步方式调用时，应提供CacheLoader。要以异步表示时，应该提供一个AsyncCacheLoader，并返回一个CompletableFuture。

synchronous()这个方法返回了一个LoadingCacheView视图，LoadingCacheView也继承自LoadingCache。调用该方法后就相当于你将一个异步加载的缓存AsyncLoadingCache转换成了一个同步加载的缓存LoadingCache。

默认使用ForkJoinPool.commonPool()来执行异步线程，但是我们可以通过Caffeine.executor(Executor) 方法来替换线程池。

# 驱逐策略（eviction）

Caffeine提供三类驱逐策略：基于大小（size-based），基于时间（time-based）和基于引用（reference-based）。

## 基于大小（size-based）

基于大小驱逐，有两种方式：一种是基于缓存大小，一种是基于权重。

```
// Evict based on the number of entries in the cache
// 根据缓存的计数进行驱逐
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
    .maximumSize(10_000)
    .build(key -> createExpensiveGraph(key));

// Evict based on the number of vertices in the cache
// 根据缓存的权重来进行驱逐（权重只是用于确定缓存大小，不会用于决定该缓存是否被驱逐）
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
    .maximumWeight(10_000)
    .weigher((Key key, Graph graph) -> graph.vertices().size())
    .build(key -> createExpensiveGraph(key));
```

我们可以使用Caffeine.maximumSize(long)方法来指定缓存的最大容量。当缓存超出这个容量的时候，会使用[Window TinyLfu策略](https://link.jianshu.com/?t=https%3A%2F%2Fgithub.com%2Fben-manes%2Fcaffeine%2Fwiki%2FEfficiency)来删除缓存。

我们也可以使用权重的策略来进行驱逐，可以使用Caffeine.weigher(Weigher) 函数来指定权重，使用Caffeine.maximumWeight(long) 函数来指定缓存最大权重值。

maximumWeight与maximumSize不可以同时使用。

## 基于时间（Time-based）

```
// Evict based on a fixed expiration policy
// 基于固定的到期策略进行退出
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
    .expireAfterAccess(5, TimeUnit.MINUTES)
    .build(key -> createExpensiveGraph(key));
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
    .expireAfterWrite(10, TimeUnit.MINUTES)
    .build(key -> createExpensiveGraph(key));

// Evict based on a varying expiration policy
// 基于不同的到期策略进行退出
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
    .expireAfter(new Expiry<Key, Graph>() {
      @Override
      public long expireAfterCreate(Key key, Graph graph, long currentTime) {
        // Use wall clock time, rather than nanotime, if from an external resource
        long seconds = graph.creationDate().plusHours(5)
            .minus(System.currentTimeMillis(), MILLIS)
            .toEpochSecond();
        return TimeUnit.SECONDS.toNanos(seconds);
      }
      
      @Override
      public long expireAfterUpdate(Key key, Graph graph, 
          long currentTime, long currentDuration) {
        return currentDuration;
      }
      
      @Override
      public long expireAfterRead(Key key, Graph graph,
          long currentTime, long currentDuration) {
        return currentDuration;
      }
    })
    .build(key -> createExpensiveGraph(key));
```

Caffeine提供了三种定时驱逐策略：

- expireAfterAccess(long, TimeUnit):在最后一次访问或者写入后开始计时，在指定的时间后过期。假如一直有请求访问该key，那么这个缓存将一直不会过期。
- expireAfterWrite(long, TimeUnit): 在最后一次写入缓存后开始计时，在指定的时间后过期。
- expireAfter(Expiry): 自定义策略，过期时间由Expiry实现独自计算。

**缓存的删除策略使用的是惰性删除和定时删除**。这两个删除策略的时间复杂度都是O(1)。

测试定时驱逐不需要等到时间结束。我们可以使用Ticker接口和Caffeine.ticker(Ticker)方法在缓存生成器中指定时间源，而不必等待系统时钟。如：

```
FakeTicker ticker = new FakeTicker(); // Guava's testlib
Cache<Key, Graph> cache = Caffeine.newBuilder()
    .expireAfterWrite(10, TimeUnit.MINUTES)
    .executor(Runnable::run)
    .ticker(ticker::read)
    .maximumSize(10)
    .build();

cache.put(key, graph);
ticker.advance(30, TimeUnit.MINUTES)
assertThat(cache.getIfPresent(key), is(nullValue());
```

## 基于引用（reference-based）

[强引用，软引用，弱引用概念说明](https://link.jianshu.com/?t=http%3A%2F%2Fblog.csdn.net%2Fxiaolyuh123%2Farticle%2Fdetails%2F78779234)请点击连接，这里说一下各各引用的区别：

Java4种引用的级别由高到低依次为：强引用 > 软引用 > 弱引用 > 虚引用

| 引用类型 | 被垃圾回收时间 | 用途           | 生存时间          |
| -------- | -------------- | -------------- | ----------------- |
| 强引用   | 从来不会       | 对象的一般状态 | JVM停止运行时终止 |
| 软引用   | 在内存不足时   | 对象缓存       | 内存不足时终止    |
| 弱引用   | 在垃圾回收时   | 对象缓存       | gc运行后终止      |
| 虚引用   | Unknown        | Unknown        | Unknown           |

```
// Evict when neither the key nor value are strongly reachable
// 当key和value都没有引用时驱逐缓存
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
    .weakKeys()
    .weakValues()
    .build(key -> createExpensiveGraph(key));

// Evict when the garbage collector needs to free memory
// 当垃圾收集器需要释放内存时驱逐
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
    .softValues()
    .build(key -> createExpensiveGraph(key));
```

我们可以将缓存的驱逐配置成基于垃圾回收器。为此，我们可以将key 和 value 配置为弱引用或只将值配置成软引用。

**注意**：AsyncLoadingCache不支持弱引用和软引用。

Caffeine.weakKeys() 使用弱引用存储key。如果没有其他地方对该key有强引用，那么该缓存就会被垃圾回收器回收。由于垃圾回收器只依赖于身份(identity)相等，因此这会导致整个缓存使用身份 (==) 相等来比较 key，而不是使用 equals()。

Caffeine.weakValues() 使用弱引用存储value。如果没有其他地方对该value有强引用，那么该缓存就会被垃圾回收器回收。由于垃圾回收器只依赖于身份(identity)相等，因此这会导致整个缓存使用身份 (==) 相等来比较 key，而不是使用 equals()。

Caffeine.softValues() 使用软引用存储value。当内存满了过后，软引用的对象以将使用最近最少使用(least-recently-used ) 的方式进行垃圾回收。由于使用软引用是需要等到内存满了才进行回收，所以我们通常建议给缓存配置一个使用内存的最大值。 softValues() 将使用身份相等(identity) (==) 而不是equals() 来比较值。

**注意**：Caffeine.weakValues()和Caffeine.softValues()不可以一起使用。

# 移除监听器（Removal）

概念：

- 驱逐（eviction）：由于满足了某种驱逐策略，后台自动进行的删除操作
- 无效（invalidation）：表示由调用方手动删除缓存
- 移除（removal）：监听驱逐或无效操作的监听器

## 手动删除缓存：

在任何时候，您都可能明确地使缓存无效，而不用等待缓存被驱逐。

```
// individual key
cache.invalidate(key)
// bulk keys
cache.invalidateAll(keys)
// all keys
cache.invalidateAll()
```

## Removal 监听器：

```
Cache<Key, Graph> graphs = Caffeine.newBuilder()
    .removalListener((Key key, Graph graph, RemovalCause cause) ->
        System.out.printf("Key %s was removed (%s)%n", key, cause))
    .build();
```

您可以通过Caffeine.removalListener(RemovalListener) 为缓存指定一个删除侦听器，以便在删除数据时执行某些操作。 RemovalListener可以获取到key、value和RemovalCause（删除的原因）。

删除侦听器的里面的操作是使用Executor来异步执行的。默认执行程序是ForkJoinPool.commonPool()，可以通过Caffeine.executor(Executor)覆盖。当操作必须与删除同步执行时，请改为使用CacheWrite，CacheWrite将在下面说明。

**注意**：由RemovalListener抛出的任何异常都会被记录（使用Logger）并不会抛出。

# 刷新（Refresh）

```
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
    .maximumSize(10_000)
    // 指定在创建缓存或者最近一次更新缓存后经过固定的时间间隔，刷新缓存
    .refreshAfterWrite(1, TimeUnit.MINUTES)
    .build(key -> createExpensiveGraph(key));
```

刷新和驱逐是不一样的。刷新的是通过LoadingCache.refresh(key)方法来指定，并通过调用CacheLoader.reload方法来执行，刷新key会异步地为这个key加载新的value，并返回旧的值（如果有的话）。驱逐会阻塞查询操作直到驱逐作完成才会进行其他操作。

与expireAfterWrite不同的是，refreshAfterWrite将在查询数据的时候判断该数据是不是符合查询条件，如果符合条件该缓存就会去执行刷新操作。例如，您可以在同一个缓存中同时指定refreshAfterWrite和expireAfterWrite，只有当数据具备刷新条件的时候才会去刷新数据，不会盲目去执行刷新操作。如果数据在刷新后就一直没有被再次查询，那么该数据也会过期。

刷新操作是使用Executor异步执行的。默认执行程序是ForkJoinPool.commonPool()，可以通过Caffeine.executor(Executor)覆盖。

如果刷新时引发异常，则使用log记录日志，并不会抛出。

# Writer

```
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
  .writer(new CacheWriter<Key, Graph>() {
    @Override public void write(Key key, Graph graph) {
      // write to storage or secondary cache
    }
    @Override public void delete(Key key, Graph graph, RemovalCause cause) {
      // delete from storage or secondary cache
    }
  })
  .build(key -> createExpensiveGraph(key));
```

CacheWriter允许缓存充当一个底层资源的代理，当与CacheLoader结合使用时，所有对缓存的读写操作都可以通过Writer进行传递。Writer可以把操作缓存和操作外部资源扩展成一个同步的原子性操作。并且在缓存写入完成之前，它将会阻塞后续的更新缓存操作，但是读取（get）将直接返回原有的值。如果写入程序失败，那么原有的key和value的映射将保持不变，如果出现异常将直接抛给调用者。

CacheWriter可以同步的监听到缓存的创建、变更和删除操作。加载（例如，LoadingCache.get）、重新加载（例如，LoadingCache.refresh）和计算（例如Map.computeIfPresent）的操作不被CacheWriter监听到。

**注意**：CacheWriter不能与weakKeys或AsyncLoadingCache结合使用。

## 可能的用例（Possible Use-Cases）

CacheWriter是复杂工作流的扩展点，需要外部资源来观察给定Key的变化顺序。这些用法Caffeine是支持的，但不是本地内置。

### 写模式（Write Modes）

CacheWriter可以用来实现一个直接写（write-through ）或回写（write-back ）缓存的操作。

write-through式缓存中，写操作是一个同步的过程，只有写成功了才会去更新缓存。这避免了同时去更新资源和缓存的条件竞争。

write-back式缓存中，对外部资源的操作是在缓存更新后异步执行的。这样可以提高写入的吞吐量，避免数据不一致的风险，比如如果写入失败，则在缓存中保留无效的状态。这种方法可能有助于延迟写操作，直到指定的时间，限制写速率或批写操作。

通过对write-back进行扩展，我们可以实现以下特性：

- 批处理和合并操作
- 延迟操作并到一个特定的时间执行
- 如果超过阈值大小，则在定期刷新之前执行批处理
- 如果操作尚未刷新，则从写入后缓冲器（write-behind）加载
- 根据外部资源的特点，处理重审，速率限制和并发

可以参考一个简单的[例子](https://link.jianshu.com/?t=https%3A%2F%2Fgithub.com%2Fben-manes%2Fcaffeine%2Ftree%2Fmaster%2Fexamples%2Fwrite-behind-rxjava)，使用RxJava实现。

### 分层（Layering）

CacheWriter可能用来集成多个缓存进而实现多级缓存。

多级缓存的加载和写入可以使用系统外部高速缓存。这允许缓存使用一个小并且快速的缓存去调用一个大的并且速度相对慢一点的缓存。典型的off-heap、file-based和remote 缓存。

受害者缓存（Victim Cache）是一个多级缓存的变体，其中被删除的数据被写入二级缓存。这个delete(K, V, RemovalCause) 方法允许检查为什么该数据被删除，并作出相应的操作。

### 同步监听器（Synchronous Listeners）

同步监听器会接收一个key在缓存中的进行了那些操作的通知。监听器可以阻止缓存操作，也可以将事件排队以异步的方式执行。这种类型的监听器最常用于复制或构建分布式缓存。

# 统计（Statistics）

```
Cache<Key, Graph> graphs = Caffeine.newBuilder()
    .maximumSize(10_000)
    .recordStats()
    .build();
```

使用Caffeine.recordStats()，您可以打开统计信息收集。Cache.stats() 方法返回提供统计信息的CacheStats，如：

- hitRate()：返回命中与请求的比率
- hitCount(): 返回命中缓存的总数
- evictionCount()：缓存逐出的数量
- averageLoadPenalty()：加载新值所花费的平均时间

# Cleanup

缓存的删除策略使用的是惰性删除和定时删除，但是我也可以自己调用cache.cleanUp()方法手动触发一次回收操作。cache.cleanUp()是一个同步方法。

# 策略（Policy）

在创建缓存的时候，缓存的策略就指定好了。但是我们可以在运行时可以获得和修改该策略。这些策略可以通过一些选项来获得，以此来确定缓存是否支持该功能。

## Size-based

```
cache.policy().eviction().ifPresent(eviction -> {
  eviction.setMaximum(2 * eviction.getMaximum());
});
```

如果缓存配置的时基于权重来驱逐，那么我们可以使用weightedSize() 来获取当前权重。这与获取缓存中的记录数的Cache.estimatedSize() 方法有所不同。

缓存的最大值(maximum)或最大权重(weight)可以通过getMaximum()方法来读取，并使用setMaximum(long)进行调整。当缓存量达到新的阀值的时候缓存才会去驱逐缓存。

如果有需用我们可以通过hottest(int) 和 coldest(int)方法来获取最有可能命中的数据和最有可能驱逐的数据快照。

## Time-based

```
cache.policy().expireAfterAccess().ifPresent(expiration -> ...);
cache.policy().expireAfterWrite().ifPresent(expiration -> ...);
cache.policy().expireVariably().ifPresent(expiration -> ...);
cache.policy().refreshAfterWrite().ifPresent(expiration -> ...);
```

ageOf(key，TimeUnit) 提供了从expireAfterAccess，expireAfterWrite或refreshAfterWrite策略的角度来看条目已经空闲的时间。最大持续时间可以从getExpiresAfter(TimeUnit)读取，并使用setExpiresAfter(long，TimeUnit)进行调整。

如果有需用我们可以通过hottest(int) 和 coldest(int)方法来获取最有可能命中的数据和最有可能驱逐的数据快照。

# 测试（Testing）

```
FakeTicker ticker = new FakeTicker(); // Guava's testlib
Cache<Key, Graph> cache = Caffeine.newBuilder()
    .expireAfterWrite(10, TimeUnit.MINUTES)
    .executor(Runnable::run)
    .ticker(ticker::read)
    .maximumSize(10)
    .build();

cache.put(key, graph);
ticker.advance(30, TimeUnit.MINUTES)
assertThat(cache.getIfPresent(key), is(nullValue());
```

测试的时候我们可以使用Caffeine..ticker(ticker)来指定一个时间源，并不需要等到key过期。

FakeTicker这个是guawa test包里面的Ticker，主要用于测试。依赖：

```
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava-testlib</artifactId>
    <version>23.5-jre</version>
</dependency>
```

# 常见问题（Faq）

## 固定数据（Pinning Entries）

固定数据是不能通过驱逐策略去将数据删除的。当数据是一个有状态的资源时（如锁），那么这条数据是非常有用的，你有在客端使用完这个条数据的时候才能删除该数据。在这种情况下如果驱逐策略将这个条数据删掉的话，将导致资源泄露。

通过使用权重将该数据的权重设置成0，并且这个条数据不计入maximum size里面。 当缓存达到maximum size 了以后，驱逐策略也会跳过该条数据，不会进行删除操作。我们还必须自定义一个标准来判断这个数据是否属于固定数据。

通过使用Long.MAX_VALUE（大约300年）的值作为key的有效时间，这样可以将一条数据从过期中排除。自定义到期必须定义，这可以评估条目是否固定。

将数据写入缓存时我们要指定该数据的权重和到期时间。这可以通过使用cache.asMap()获取缓存列表后，再来实现引脚和解除绑定。

## 递归调用（Recursive Computations）

在原子操作内执行的加载，计算或回调可能不会写入缓存。 ConcurrentHashMap不允许这些递归写操作，因为这将有可能导致活锁（Java 8）或IllegalStateException（Java 9）。

解决方法是异步执行这些操作，例如使用AsyncLoadingCache。在异步这种情况下映射已经建立，value是一个CompletableFuture，并且这些操作是在缓存的原子范围之外执行的。但是，如果发生无序的依赖链，这也有可能导致死锁。

# 示例代码：

```
package com.xiaolyuh.controller;

import com.alibaba.fastjson.JSON;
import com.github.benmanes.caffeine.cache.*;
import com.google.common.testing.FakeTicker;
import com.xiaolyuh.entity.Person;
import com.xiaolyuh.service.PersonService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.OptionalLong;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ConcurrentMap;
import java.util.concurrent.Executor;
import java.util.concurrent.TimeUnit;

@RestController
public class CaffeineCacheController {

    @Autowired
    PersonService personService;

    Cache<String, Object> manualCache = Caffeine.newBuilder()
            .expireAfterWrite(10, TimeUnit.MINUTES)
            .maximumSize(10_000)
            .build();

    LoadingCache<String, Object> loadingCache = Caffeine.newBuilder()
            .maximumSize(10_000)
            .expireAfterWrite(10, TimeUnit.MINUTES)
            .build(key -> createExpensiveGraph(key));

    AsyncLoadingCache<String, Object> asyncLoadingCache = Caffeine.newBuilder()
            .maximumSize(10_000)
            .expireAfterWrite(10, TimeUnit.MINUTES)
            // Either: Build with a synchronous computation that is wrapped as asynchronous
            .buildAsync(key -> createExpensiveGraph(key));
    // Or: Build with a asynchronous computation that returns a future
    // .buildAsync((key, executor) -> createExpensiveGraphAsync(key, executor));

    private CompletableFuture<Object> createExpensiveGraphAsync(String key, Executor executor) {
        CompletableFuture<Object> objectCompletableFuture = new CompletableFuture<>();
        return objectCompletableFuture;
    }

    private Object createExpensiveGraph(String key) {
        System.out.println("缓存不存在或过期，调用了createExpensiveGraph方法获取缓存key的值");
        if (key.equals("name")) {
            throw new RuntimeException("调用了该方法获取缓存key的值的时候出现异常");
        }
        return personService.findOne1();
    }

    @RequestMapping("/testManual")
    public Object testManual(Person person) {
        String key = "name1";
        Object graph = null;

        // 根据key查询一个缓存，如果没有返回NULL
        graph = manualCache.getIfPresent(key);
        // 根据Key查询一个缓存，如果没有调用createExpensiveGraph方法，并将返回值保存到缓存。
        // 如果该方法返回Null则manualCache.get返回null，如果该方法抛出异常则manualCache.get抛出异常
        graph = manualCache.get(key, k -> createExpensiveGraph(k));
        // 将一个值放入缓存，如果以前有值就覆盖以前的值
        manualCache.put(key, graph);
        // 删除一个缓存
        manualCache.invalidate(key);

        ConcurrentMap<String, Object> map = manualCache.asMap();
        System.out.println(map.toString());
        return graph;
    }

    @RequestMapping("/testLoading")
    public Object testLoading(Person person) {
        String key = "name1";

        // 采用同步方式去获取一个缓存和上面的手动方式是一个原理。在build Cache的时候会提供一个createExpensiveGraph函数。
        // 查询并在缺失的情况下使用同步的方式来构建一个缓存
        Object graph = loadingCache.get(key);

        // 获取组key的值返回一个Map
        List<String> keys = new ArrayList<>();
        keys.add(key);
        Map<String, Object> graphs = loadingCache.getAll(keys);
        return graph;
    }

    @RequestMapping("/testAsyncLoading")
    public Object testAsyncLoading(Person person) {
        String key = "name1";

        // 查询并在缺失的情况下使用异步的方式来构建缓存
        CompletableFuture<Object> graph = asyncLoadingCache.get(key);
        // 查询一组缓存并在缺失的情况下使用异步的方式来构建缓存
        List<String> keys = new ArrayList<>();
        keys.add(key);
        CompletableFuture<Map<String, Object>> graphs = asyncLoadingCache.getAll(keys);

        // 异步转同步
        loadingCache = asyncLoadingCache.synchronous();
        return graph;
    }

    @RequestMapping("/testSizeBased")
    public Object testSizeBased(Person person) {
        LoadingCache<String, Object> cache = Caffeine.newBuilder()
                .maximumSize(1)
                .build(k -> createExpensiveGraph(k));

        cache.get("A");
        System.out.println(cache.estimatedSize());
        cache.get("B");
        // 因为执行回收的方法是异步的，所以需要调用该方法，手动触发一次回收操作。
        cache.cleanUp();
        System.out.println(cache.estimatedSize());

        return "";
    }

    @RequestMapping("/testTimeBased")
    public Object testTimeBased(Person person) {
        String key = "name1";
        // 用户测试，一个时间源，返回一个时间值，表示从某个固定但任意时间点开始经过的纳秒数。
        FakeTicker ticker = new FakeTicker();

        // 基于固定的到期策略进行退出
        // expireAfterAccess
        LoadingCache<String, Object> cache1 = Caffeine.newBuilder()
                .ticker(ticker::read)
                .expireAfterAccess(5, TimeUnit.SECONDS)
                .build(k -> createExpensiveGraph(k));

        System.out.println("expireAfterAccess：第一次获取缓存");
        cache1.get(key);

        System.out.println("expireAfterAccess：等待4.9S后，第二次次获取缓存");
        // 直接指定时钟
        ticker.advance(4900, TimeUnit.MILLISECONDS);
        cache1.get(key);

        System.out.println("expireAfterAccess：等待0.101S后，第三次次获取缓存");
        ticker.advance(101, TimeUnit.MILLISECONDS);
        cache1.get(key);

        // expireAfterWrite
        LoadingCache<String, Object> cache2 = Caffeine.newBuilder()
                .ticker(ticker::read)
                .expireAfterWrite(5, TimeUnit.SECONDS)
                .build(k -> createExpensiveGraph(k));

        System.out.println("expireAfterWrite：第一次获取缓存");
        cache2.get(key);

        System.out.println("expireAfterWrite：等待4.9S后，第二次次获取缓存");
        ticker.advance(4900, TimeUnit.MILLISECONDS);
        cache2.get(key);

        System.out.println("expireAfterWrite：等待0.101S后，第三次次获取缓存");
        ticker.advance(101, TimeUnit.MILLISECONDS);
        cache2.get(key);

        // Evict based on a varying expiration policy
        // 基于不同的到期策略进行退出
        LoadingCache<String, Object> cache3 = Caffeine.newBuilder()
                .ticker(ticker::read)
                .expireAfter(new Expiry<String, Object>() {

                    @Override
                    public long expireAfterCreate(String key, Object value, long currentTime) {
                        // Use wall clock time, rather than nanotime, if from an external resource
                        return TimeUnit.SECONDS.toNanos(5);
                    }

                    @Override
                    public long expireAfterUpdate(String key, Object graph,
                                                  long currentTime, long currentDuration) {

                        System.out.println("调用了 expireAfterUpdate：" + TimeUnit.NANOSECONDS.toMillis(currentDuration));
                        return currentDuration;
                    }

                    @Override
                    public long expireAfterRead(String key, Object graph,
                                                long currentTime, long currentDuration) {

                        System.out.println("调用了 expireAfterRead：" + TimeUnit.NANOSECONDS.toMillis(currentDuration));
                        return currentDuration;
                    }
                })
                .build(k -> createExpensiveGraph(k));

        System.out.println("expireAfter：第一次获取缓存");
        cache3.get(key);

        System.out.println("expireAfter：等待4.9S后，第二次次获取缓存");
        ticker.advance(4900, TimeUnit.MILLISECONDS);
        cache3.get(key);

        System.out.println("expireAfter：等待0.101S后，第三次次获取缓存");
        ticker.advance(101, TimeUnit.MILLISECONDS);
        Object object = cache3.get(key);

        return object;
    }

    @RequestMapping("/testRemoval")
    public Object testRemoval(Person person) {
        String key = "name1";
        // 用户测试，一个时间源，返回一个时间值，表示从某个固定但任意时间点开始经过的纳秒数。
        FakeTicker ticker = new FakeTicker();

        // 基于固定的到期策略进行退出
        // expireAfterAccess
        LoadingCache<String, Object> cache = Caffeine.newBuilder()
                .removalListener((String k, Object graph, RemovalCause cause) ->
                        System.out.printf("Key %s was removed (%s)%n", k, cause))
                .ticker(ticker::read)
                .expireAfterAccess(5, TimeUnit.SECONDS)
                .build(k -> createExpensiveGraph(k));

        System.out.println("第一次获取缓存");
        Object object = cache.get(key);

        System.out.println("等待6S后，第二次次获取缓存");
        // 直接指定时钟
        ticker.advance(6000, TimeUnit.MILLISECONDS);
        cache.get(key);

        System.out.println("手动删除缓存");
        cache.invalidate(key);

        return object;
    }

    @RequestMapping("/testRefresh")
    public Object testRefresh(Person person) {
        String key = "name1";
        // 用户测试，一个时间源，返回一个时间值，表示从某个固定但任意时间点开始经过的纳秒数。
        FakeTicker ticker = new FakeTicker();

        // 基于固定的到期策略进行退出
        // expireAfterAccess
        LoadingCache<String, Object> cache = Caffeine.newBuilder()
                .removalListener((String k, Object graph, RemovalCause cause) ->
                        System.out.printf("执行移除监听器- Key %s was removed (%s)%n", k, cause))
                .ticker(ticker::read)
                .expireAfterWrite(5, TimeUnit.SECONDS)
                // 指定在创建缓存或者最近一次更新缓存后经过固定的时间间隔，刷新缓存
                .refreshAfterWrite(4, TimeUnit.SECONDS)
                .build(k -> createExpensiveGraph(k));

        System.out.println("第一次获取缓存");
        Object object = cache.get(key);

        System.out.println("等待4.1S后，第二次次获取缓存");
        // 直接指定时钟
        ticker.advance(4100, TimeUnit.MILLISECONDS);
        cache.get(key);

        System.out.println("等待5.1S后，第三次次获取缓存");
        // 直接指定时钟
        ticker.advance(5100, TimeUnit.MILLISECONDS);
        cache.get(key);

        return object;
    }

    @RequestMapping("/testWriter")
    public Object testWriter(Person person) {
        String key = "name1";
        // 用户测试，一个时间源，返回一个时间值，表示从某个固定但任意时间点开始经过的纳秒数。
        FakeTicker ticker = new FakeTicker();

        // 基于固定的到期策略进行退出
        // expireAfterAccess
        LoadingCache<String, Object> cache = Caffeine.newBuilder()
                .removalListener((String k, Object graph, RemovalCause cause) ->
                        System.out.printf("执行移除监听器- Key %s was removed (%s)%n", k, cause))
                .ticker(ticker::read)
                .expireAfterWrite(5, TimeUnit.SECONDS)
                .writer(new CacheWriter<String, Object>() {
                    @Override
                    public void write(String key, Object graph) {
                        // write to storage or secondary cache
                        // 写入存储或者二级缓存
                        System.out.printf("testWriter:write - Key %s was write (%s)%n", key, graph);
                        createExpensiveGraph(key);
                    }

                    @Override
                    public void delete(String key, Object graph, RemovalCause cause) {
                        // delete from storage or secondary cache
                        // 删除存储或者二级缓存
                        System.out.printf("testWriter:delete - Key %s was delete (%s)%n", key, graph);
                    }
                })
                // 指定在创建缓存或者最近一次更新缓存后经过固定的时间间隔，刷新缓存
                .refreshAfterWrite(4, TimeUnit.SECONDS)
                .build(k -> createExpensiveGraph(k));

        cache.put(key, personService.findOne1());
        cache.invalidate(key);

        System.out.println("第一次获取缓存");
        Object object = cache.get(key);

        System.out.println("等待4.1S后，第二次次获取缓存");
        // 直接指定时钟
        ticker.advance(4100, TimeUnit.MILLISECONDS);
        cache.get(key);

        System.out.println("等待5.1S后，第三次次获取缓存");
        // 直接指定时钟
        ticker.advance(5100, TimeUnit.MILLISECONDS);
        cache.get(key);

        return object;
    }

    @RequestMapping("/testStatistics")
    public Object testStatistics(Person person) {
        String key = "name1";
        // 用户测试，一个时间源，返回一个时间值，表示从某个固定但任意时间点开始经过的纳秒数。
        FakeTicker ticker = new FakeTicker();

        // 基于固定的到期策略进行退出
        // expireAfterAccess
        LoadingCache<String, Object> cache = Caffeine.newBuilder()
                .removalListener((String k, Object graph, RemovalCause cause) ->
                        System.out.printf("执行移除监听器- Key %s was removed (%s)%n", k, cause))
                .ticker(ticker::read)
                .expireAfterWrite(5, TimeUnit.SECONDS)
                // 开启统计
                .recordStats()
                // 指定在创建缓存或者最近一次更新缓存后经过固定的时间间隔，刷新缓存
                .refreshAfterWrite(4, TimeUnit.SECONDS)
                .build(k -> createExpensiveGraph(k));

        for (int i = 0; i < 10; i++) {
            cache.get(key);
            cache.get(key + i);
        }
        // 驱逐是异步操作，所以这里要手动触发一次回收操作
        ticker.advance(5100, TimeUnit.MILLISECONDS);
        // 手动触发一次回收操作
        cache.cleanUp();

        System.out.println("缓存命数量：" + cache.stats().hitCount());
        System.out.println("缓存命中率：" + cache.stats().hitRate());
        System.out.println("缓存逐出的数量：" + cache.stats().evictionCount());
        System.out.println("加载新值所花费的平均时间：" + cache.stats().averageLoadPenalty());

        return cache.get(key);
    }

    @RequestMapping("/testPolicy")
    public Object testPolicy(Person person) {
        FakeTicker ticker = new FakeTicker();

        LoadingCache<String, Object> cache = Caffeine.newBuilder()
                .ticker(ticker::read)
                .expireAfterAccess(5, TimeUnit.SECONDS)
                .maximumSize(1)
                .build(k -> createExpensiveGraph(k));

        // 在代码里面动态的指定最大Size
        cache.policy().eviction().ifPresent(eviction -> {
            eviction.setMaximum(4 * eviction.getMaximum());
        });

        cache.get("E");
        cache.get("B");
        cache.get("C");
        cache.cleanUp();
        System.out.println(cache.estimatedSize() + ":" + JSON.toJSON(cache.asMap()).toString());

        cache.get("A");
        ticker.advance(100, TimeUnit.MILLISECONDS);
        cache.get("D");
        ticker.advance(100, TimeUnit.MILLISECONDS);
        cache.get("A");
        ticker.advance(100, TimeUnit.MILLISECONDS);
        cache.get("B");
        ticker.advance(100, TimeUnit.MILLISECONDS);
        cache.policy().eviction().ifPresent(eviction -> {
            // 获取热点数据Map
            Map<String, Object> hottestMap = eviction.hottest(10);
            // 获取冷数据Map
            Map<String, Object> coldestMap = eviction.coldest(10);

            System.out.println("热点数据:" + JSON.toJSON(hottestMap).toString());
            System.out.println("冷数据:" + JSON.toJSON(coldestMap).toString());
        });

        ticker.advance(3000, TimeUnit.MILLISECONDS);
        // ageOf通过这个方法来查看key的空闲时间
        cache.policy().expireAfterAccess().ifPresent(expiration -> {

            System.out.println(JSON.toJSON(expiration.ageOf("A", TimeUnit.MILLISECONDS)));
        });
        return cache.get("name1");
    }
}
```

英文不好，有些翻译的不准确的请不吝赐教。。。

参考：

- [https://github.com/ben-manes/caffeine/blob/master/README.md](https://link.jianshu.com/?t=https%3A%2F%2Fgithub.com%2Fben-manes%2Fcaffeine%2Fblob%2Fmaster%2FREADME.md)
- [http://oopsguy.com/2017/10/25/java-caching-caffeine/](https://link.jianshu.com/?t=http%3A%2F%2Foopsguy.com%2F2017%2F10%2F25%2Fjava-caching-caffeine%2F)
- [http://blog.csdn.net/mazhimazh/article/details/19752475](https://link.jianshu.com/?t=http%3A%2F%2Fblog.csdn.net%2Fmazhimazh%2Farticle%2Fdetails%2F19752475)

源码：
[https://github.com/wyh-spring-ecosystem-student/spring-boot-student/tree/releases](https://link.jianshu.com/?t=https%3A%2F%2Fgithub.com%2Fwyh-spring-ecosystem-student%2Fspring-boot-student%2Ftree%2Freleases)

spring-boot-student-cache-caffeine 工程