##### Linq Extension Methods
First install the Dumpify NuGet package to your project. You can do this by running the following command in the Package Manager Console:
```csharp
dotnet add package Dumpify
```

#### Create a GlobalUsings.cs file
Create a GlobalUsings.cs file in the root of your project and add the following code:
```csharp
global using Dumpify;
```

Then you can use the Dumpify extension methods in your LINQ queries. Here is an example:
```csharp

IEnumerable<int> collection = [1, 2, 3, 4, 5];

collection.Dump();

string[] names = ["John", "Jane", "Jack", "Jill"];
names.Dump();


Dictionary<string, int> ages = new Dictionary<string, int>
{
    ["John"] = 25,
    ["Jane"] = 30,
    ["Jack"] = 35,
    ["Jill"] = 40
};

ages.Dump();

```


#### WHERE
```csharp

IEnumerable<int> collection = [1, 2, 3, 4, 5];

// WHERE clause, x > 2 is called a predicate. Predicates are used to filter data.
collection.Where(x => x > 2).Dump();
    
```

#### Returning TypeOf
```csharp

IEnumerable<object> collection = [1, "boy",2, 3, 4, 5];

// WHERE clause, x > 2 is called a predicate. Predicates are used to filter data.
collection.OfType<int>().Dump();
collection.OfType<string>().Dump();
```


##### Partitions
SKIP
```csharp

IEnumerable<object> collection = [1, "boy",2, 3, 4, 5];

collection.Skip(3).Dump(); // Output: 3, 4, 5. i.e. Skip first 3 elements and return the rest.
collection.Take(3).Dump(); // Output: 1, 2, 3. i.e. Take first 3 elements and return them.
collection.SkipLast(3).Dump("SkipLast"); // Output 
collection.TakeLast(3).Dump("TakeLast"); // TakeLast 3 elements from the end of the collection


```

```csharp

IEnumerable<int> collection = [1, 2, 3, 4, 5];

collection.TakeWhile(x=> x < 2).Dump(); 
collection.SkipWhile(x=> x < 2).Dump();  // 2, 3, 4, 5
collection.SkipWhile(x=> x <= 2).Dump(); // 3, 4, 5 
collection.SkipWhile(x=> x < 2).TakeWhile(x=> x < 4).Dump();
```


#### Projection
```csharp

IEnumerable<int> collection = [1, 2, 3, 4, 5];

//select: Projects each element of a sequence into a new form.
collection.Select(x=> x.ToString()).Dump();
collection.Select(x=> x.ToString() + ": a").Dump();

//select with index: Selects the index of the element in the collection.
collection.Select((x, i) => x.ToString() + ": " + i).Dump();

//select many
IEnumerable<IEnumerable<int>> collection2 = [[1, 2, 3], [4, 5, 6], [7, 8, 9]];
collection2.SelectMany(x => x).Dump();
collection2.SelectMany((x, i) => x.Select(y => y.ToString() + ": " + i)).Dump();


//cast: Casts the elements of an IEnumerable to the specified type. i.e converts to IEnumerable<T> where T is the specified type.
IEnumerable<object> collection3 = [1, 2, 3, 4, 5];
collection3.Cast<int>().Dump();

// chunk: Splits the collection into chunks of the specified size.
collection.Chunk(2).Dump(); // [[1, 2], [3, 4], [5]]
collection.Chunk(3).Dump(); // [[1, 2, 3], [4, 5]]

```

Note: The Select and other Linq are deferred execution. This means that the query is not executed until the result is enumerated. This is useful for performance reasons. The query is executed when the result is enumerated. This is called deferred execution.
e.g.
```csharp
IEnumerable<int> collection = [1, 2, 3, 4, 5];
var query = collection.Select(x=> x.ToString()); // The query is not executed here.
query.Dump(); // The query is executed here.
```

#### Existence or Quantification
Any, All, Contains are Immidiate Execution. i.e. they are executed immediately when called.
```csharp

IEnumerable<int> collection = [1, 2, 3, 4, 5];

collection.Any(x => x > 3).Dump(); // Checks if one element is greater than 3, returns True
collection.All(x => x > 3).Dump(); // Checks every element, returns False
collection.Contains(3).Dump(); // Checks if 3 is in the collection, returns True
```

#### Sequence Manipulation
```csharp

IEnumerable<int> collection = [1, 2, 3, 4, 5];

collection.Append(6).Dump(); // 1, 2, 3, 4, 5, 6
collection.Prepend(0).Dump(); // 0, 1, 2, 3, 4, 5
collection.Concat([6, 7, 8]).Dump(); // 1, 2, 3, 4, 5, 6, 7, 8
collection.Reverse().Dump(); // 5, 4, 3, 2, 1
```

#### Aggregation
```csharp
IEnumerable<int> collection = [1, 2, 3, 4, 5];

collection.Count().Dump(); //immediate execution
collection.Where(x => x > 3).TryGetNonEnumeratedCount(out var count).Dump(); //deferred execution
collection.TryGetNonEnumeratedCount(out var res).Dump(); //deferred execution

collection.Max().Dump(); //immediate execution
collection.Max(x => x * -1).Dump(); //immediate execution
collection.Min().Dump(); //immediate execution
collection.MaxBy(x => x * -1).Dump(); //immediate execution: gets the first max value
collection.MinBy(x => x * -1).Dump(); //immediate execution: gets the first min value
collection.Sum().Dump(); //immediate execution
collection.Sum(x => x * -1).Dump(); //immediate execution, sum with predicate
collection.Average().Dump(); //immediate execution
collection.LongCount().Dump(); //immediate execution
collection.Aggregate((x, y) => x * y).Dump(); //immediate execution

//aggregate overload 1
collection.Aggregate(10, (x, y) => x * y).Dump(); //immediate execution

//aggregate overload 2
collection.Aggregate(10, (x, y) => x * y, result => result * 2).Dump(); //immediate execution


IEnumerable<Person> people = [ //collection of Person objects, we use [] to create a collection of objects
    new Person("John", 25),
    new Person("Jane", 30),
    new Person("Joe", 25)
    ];

people.MaxBy(x => x.Age).Dump(); //immediate execution: gets the first max value
people.Max(x => x.Age).Dump(); //immediate execution: 

record Person(string Name, int Age);
```

#### Elemet Operations
```csharp
IEnumerable<int> collection = [1, 2, 3, 4, 5];


//They are all immediate execution methods. They execute the query immediately and return the result.
collection.First().Dump(); // gets the first element of the collection, if the collection is empty, throws an exception. You must have at least one element in the collection.

collection.FirstOrDefault().Dump(); // gets the first element of the collection, if the collection is empty, returns the default value of the type of the collection. For int, it is 0.

//specify your own default value
collection.FirstOrDefault(10).Dump(); // gets the first element of the collection, if the collection is empty, returns 10.

collection.Single().Dump(); // gets the only element of the collection, if the collection has more than one element, throws an exception. If the collection is empty, throws an exception.

collection.SingleOrDefault().Dump(); // gets the only element of the collection, if the collection has more than one element, throws an exception. If the collection is empty, returns the default value of the type of the collection. For int, it is 0.

collection.SingleOrDefault(10).Dump(); // gets the only element of the collection, if the collection has more than one element, throws an exception. If the collection is empty, returns 10.

collection.Last().Dump(); // gets the last element of the collection, if the collection is empty, throws an exception. You must have at least one element in the collection.

collection.LastOrDefault().Dump(); // gets the last element of the collection, if the collection is empty, returns the default value of the type of the collection. For int, it is 0.

collection.ElementAt(2).Dump(); // gets the element at the specified index. If the index is out of range, throws an exception.

collection.ElementAtOrDefault(10).Dump(); // gets the element at the specified index. If the index is out of range, returns the default value of the type of the collection. For int, it is 0.

collection.DefaultIfEmpty().Dump(); // returns the collection itself if it is not empty. If the collection is empty, returns a collection with a single element, which is the default value of the type of the collection. For int, it is 0.
```

##### Conversion methods
```csharp
IEnumerable<int> collection = [1, 2, 3, 4, 5];

collection.ToArray().Dump();
collection.ToList().Dump();
collection.ToDictionary(x => x, x => x).Dump();
collection.ToHashSet().Dump();

IEnumerable<Person> collection2 =[new Person { Name = "John", Age = 25 }, new Person { Name = "Jane", Age = 30 }, new Person { Name = "John", Age = 25 }];
collection.ToLookup(x => x).Dump();
collection2.ToLookup(x => x.Name).Dump();
collection2.ToLookup(x => x.Age)[25].Single().Name.Dump();

record Person
{
    public string Name { get; init; } = string.Empty;
    public int Age { get; init; }
}
```

#### Generation Methods
```csharp
List<Person> collection = [new Person("John", 25), new Person("Jane", 22), new Person("Joe", 30), new Person("Jill", 28), new Person("Jack", 27)];

//turn to IEnumerable
IEnumerable<Person> people = collection.AsEnumerable().Dump("People");
var foo = collection.AsEnumerable().Dump();

IQueryable<Person> queryable = collection.AsQueryable().Dump("Queryable");
var bar = collection.AsQueryable().Dump();



record Person(string Name, int Age);

```

Ex2
```csharp
IEnumerable<int> collection = Enumerable.Range(1, 100);
//Immediate execution
collection.Dump();

IEnumerable<string> collection2 = Enumerable.Repeat("Hello", 10);
//Immediate execution
collection2.Dump();

IEnumerable<int> collection3 = Enumerable.Range(1, 100).Where(x => x % 2 == 0);
//Immediate execution
collection3.Dump();

//Empty collection
IEnumerable<int> collection4 = Enumerable.Empty<int>();
//Immediate execution
collection4.Dump();
```

#### Set Operations
These are immediate execution methods. They execute the query immediately and return the result.
Distinct and DistinctBy are immediate execution methods. They execute the query immediately and return the result.
```csharp
IEnumerable<int> collection = [1, 2, 3, 4, 5, 1,4, 1];

//Distinct
collection.Distinct().ToList().ForEach(Console.WriteLine);
collection.Distinct().Dump();

IEnumerable<Person> people = [  
    new Person(1, "John", 20),
    new Person(2, "Jane", 30),
    new Person(1, "Joe", 40),
    new Person(4, "Jill", 50),
    new Person(5, "Jack", 60),
    new Person(6, "John", 20),
    new Person(7, "John", 20)

];

people.DistinctBy(p => p.Id).Dump();

record Person(int Id, string Name, int Age);
```

##### Methods that compare two collections
```csharp
IEnumerable<int> collection1 = [1, 2, 3, 4];
IEnumerable<int> collection2 = [2, 3, 4, 5, 6, 7, 8];
//Union
IEnumerable<int> union = collection1.Union(collection2);
union.Dump();

// intersection
IEnumerable<int> intersection = collection1.Intersect(collection2);
intersection.Dump();

// difference
IEnumerable<int> difference = collection1.Except(collection2);
difference.Dump();

//ExceptBy
IEnumerable<int> exceptBy = collection1.ExceptBy(collection2, x => x);
exceptBy.Dump();

//intersectBy
IEnumerable<int> intersectBy = collection1.IntersectBy(collection2, x => x);
intersectBy.Dump();

//UnionBy
IEnumerable<int> unionBy = collection1.UnionBy(collection2, x => x);
unionBy.Dump();

//SequenceEqual
bool sequenceEqual = collection1.SequenceEqual(collection2);
sequenceEqual.Dump();
```

#### Joining and Grouping
```csharp
IEnumerable<int> collection1 = [1, 2, 3, 4];
IEnumerable<string> collection2 = ["a", "b", "c", "d"];
IEnumerable<string> collection3 = ["*","&","%","$"];

//Zip
var zipped = collection1.Zip(collection2, (i, s) => i + s).Dump();
collection1.Zip(collection2).Dump();
collection1.Zip(collection2,collection3).Dump();

//NOTE: if the collections are not of the same length, the result will be the length of the shortest collection

//Join
var joined = collection1.Join(collection2, i => i, s => s.Length, (i, s) => i + s).Dump();
collection1.Join(collection2, i => i, s => s.Length, (i, s) => i + s).Dump();


//GroupJoin
var groupJoined = collection1.GroupJoin(collection2, i => i, s => s.Length, (i, s) => new { i, s }).Dump();
collection1.GroupJoin(collection2, i => i, s => s.Length, (i, s) => new { i, s }).Dump();

//Concat
IEnumerable<int> collection4 = [1, 2, 3, 4];
IEnumerable<int> collection5 = [5, 6, 7, 8];

var concatenated = collection4.Concat(collection5).Dump();
collection4.Concat(collection5).Dump();

IEnumerable<Person> people = [new Person { Name = "John", Age = 25 }, new Person { Name = "Jane", Age = 22 }, new Person { Name = "Joe", Age = 25 }];

people.GroupBy(p => p.Age).Dump();

//Last
IGrouping<int, Person> group = people.GroupBy(p => p.Age).Last();
group.Dump();
group.Key.Dump();

foreach (var person in group)
{
    person.Dump();
}


record Person
{
    public string Name { get; init; } = string.Empty;
    public int Age { get; init; }
}
```

#### Ordering
```csharp
IEnumerable<int> collection = [8, 9, 7, 2, 3, 4, 5];

//Sorting
IEnumerable<int> sortedCollection = collection.OrderBy(x => x);

collection.Order().Dump();
collection.OrderBy(x => x).Dump();
collection.OrderBy(x => x < 5).Dump();

//OrderbyDescending
collection.OrderByDescending(x => x).Dump();

//ThenBy
IEnumerable<int> collection2 = [8, 9, 7, 2, 3, 4, 5];
IEnumerable<int> sortedCollection2 = collection2.OrderBy(x => x).ThenBy(x => x < 5);
sortedCollection2.Dump();

//ThenByDescending
IEnumerable<int> collection3 = [8, 9, 7, 2, 3, 4, 5];
IEnumerable<int> sortedCollection3 = collection3.OrderBy(x => x).ThenByDescending(x => x < 5);
sortedCollection3.Dump();

//Reverse
IEnumerable<int> collection4 = [8, 9, 7, 2, 3, 4, 5];
IEnumerable<int> reversedCollection = collection4.Reverse();
reversedCollection.Dump();

//GroupBy
IEnumerable<int> collection5 = [8, 9, 7, 2, 3, 4, 5];
IEnumerable<IGrouping<int, int>> groupedCollection = collection5.GroupBy(x => x);
groupedCollection.Dump();
```

#### Parrallel Linq
```csharp
IEnumerable<int> collection = [8, 9, 7, 2, 3, 4, 5];

//Parrallel LINQ
var result = collection.AsParallel().Where(i => i % 2 == 0).ToArray().Dump();

//Output
// 8
// 2
// 4

//WithDegreeOfParallelism
// Ex:
var result2 = collection.AsParallel().WithDegreeOfParallelism(2).Where(i => i % 2 == 0).ToArray().Dump();

//Output
// 8
// 2
// 4

//WithExecutionMode
// Ex:
var result3 = collection.AsParallel().WithExecutionMode(ParallelExecutionMode.ForceParallelism).Where(i => i % 2 == 0).ToArray().Dump();

//Output
// 8
// 2
// 4

//WithMergeOptions
// Ex:
var result4 = collection.AsParallel().WithMergeOptions(ParallelMergeOptions.NotBuffered).Where(i => i % 2 == 0).ToArray().Dump();

//Output
// 8
// 2


//asOrdered
// Ex:
var result5 = collection.AsParallel().AsOrdered().Where(i => i % 2 == 0).ToArray().Dump();

//Output
// 8
// 2
// 4
```

#### Deferred Execution
```csharp
IEnumerable<int> collection = [1, 2, 3, 4, 5];

//Deferred execution
var query = collection.Select(x => x * 2);
query.Dump();

//Deferred execution
var query2 = collection.Select(x => x * 2).Where(x => x > 5);
query2.Dump();

//Deferred execution
var query3 = collection.Select(x => x * 2).Where(x => x > 5).OrderBy(x => x);
query3.Dump();

//Deferred execution
var query4 = collection.Select(x => x * 2).Where(x => x > 5).OrderBy(x => x).Reverse();
query4.Dump();
```

#### Immediate Execution
```csharp
IEnumerable<int> collection = [1, 2, 3, 4, 5];

//Immediate execution
collection.Select(x => x * 2).Dump();

//Immediate execution
collection.Select(x => x * 2).Where(x => x > 5).Dump();
```

