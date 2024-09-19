# Learning Jetpack Compose

### 1. When is a Composable Function Invoked?

A composable function is invoked in two scenarios:

- **During Initial Composition**: This happens when the UI is first built.
  
- **During Recomposition**: This occurs when a state that the function depends on changes, triggering the function to re-run.

During recomposition, the function is called again, but Jetpack Compose efficiently skips over parts of the UI that don’t depend on the changed state, updating only the necessary elements.

### 2. What is `mutableStateOf`?

```kotlin
@StateFactoryMarker
fun <T : Any?> mutableStateOf(
    value: T,
    policy: SnapshotMutationPolicy<T> = structuralEqualityPolicy()
): MutableState<T>
```
`mutableStateOf` returns a new `MutableState` initialized with the provided value.

The `MutableState` class is a single-value holder, whose reads and writes are observed by Jetpack Compose. Additionally, writes to it are transacted as part of the **Snapshot system**.

There are three equivalent ways to declare a `MutableState` object in a composable:

```kotlin
val mutableState = remember { mutableStateOf(default) }
var value by remember { mutableStateOf(default) }
val (value, setValue) = remember { mutableStateOf(default) }
```

```kotlin
@Composable
fun HelloContent() {
    Column(modifier = Modifier.padding(16.dp)) {
        var name by remember { mutableStateOf("") }
        if (name.isNotEmpty()) {
            Text( // <- only this text will be recomposed, when the name changes
                text = "Hello, $name!",  
                modifier = Modifier.padding(bottom = 8.dp),
                style = MaterialTheme.typography.bodyMedium
            )
        }
        Text( // <- this text will NOT be recomposed, when the name changes
            text = "Hello Damian",
            modifier = Modifier.padding(bottom = 8.dp),
            style = MaterialTheme.typography.bodyMedium
        )
    }
}
```
- **Avoid using mutable objects** like `ArrayList<T>` or `mutableListOf()` as state in Compose. These are not observable by Compose and won't trigger recompositions when they change, which may lead to stale data in your app.
  
  Instead, use an observable data holder like `State<List<T>>` and an immutable list like `listOf()`.

You can specify a policy to determine how state values are compared for equality:

1. **`referentialEqualityPolicy`**  
   Treats values as equivalent if they are referentially (===) equal.
   
2. **`structuralEqualityPolicy`** (default)  
   Treats values as equivalent if they are structurally (==) equal.
   
3. **`neverEqualPolicy`**  
   Treats values as never equivalent, ensuring the state is always considered changed.
