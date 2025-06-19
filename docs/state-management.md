# Gestión de Estado en Jetpack Compose

El estado en Jetpack Compose es cualquier valor que puede cambiar a lo largo del tiempo. La gestión correcta del estado es fundamental para crear aplicaciones reactivas y eficientes.

## Conceptos fundamentales

### State y MutableState

```kotlin
@Composable
fun CounterExample() {
    // Estado local
    var count by remember { mutableStateOf(0) }
    
    Column {
        Text("Contador: $count")
        Button(onClick = { count++ }) {
            Text("Incrementar")
        }
    }
}
```

### Remember y rememberSaveable

| Función | Cuándo usar | Supervive a |
|---------|-------------|-------------|
| `remember` | Estado simple en composables | Recomposición |
| `rememberSaveable` | Estado que debe persistir | Recomposición + cambios de configuración |

```kotlin
@Composable
fun StateExamples() {
    // Se pierde en cambios de configuración
    var temporaryText by remember { mutableStateOf("") }
    
    // Persiste en cambios de configuración
    var persistentText by rememberSaveable { mutableStateOf("") }
    
    Column {
        OutlinedTextField(
            value = temporaryText,
            onValueChange = { temporaryText = it },
            label = { Text("Temporal") }
        )
        
        OutlinedTextField(
            value = persistentText,
            onValueChange = { persistentText = it },
            label = { Text("Persistente") }
        )
    }
}
```

## State Hoisting

El patrón de "elevar el estado" es fundamental en Compose:

=== "❌ Estado no elevado"

    ```kotlin
    @Composable
    fun BadCounter() {
        var count by remember { mutableStateOf(0) }
        
        Button(onClick = { count++ }) {
            Text("Count: $count")
        }
    }
    ```

=== "✅ Estado elevado"

    ```kotlin
    @Composable
    fun GoodCounter(
        count: Int,
        onCountChange: (Int) -> Unit
    ) {
        Button(onClick = { onCountChange(count + 1) }) {
            Text("Count: $count")
        }
    }
    
    @Composable
    fun CounterContainer() {
        var count by remember { mutableStateOf(0) }
        GoodCounter(count = count, onCountChange = { count = it })
    }
    ```

!!! tip "Ventajas del State Hoisting"
    - Facilita el testing
    - Permite reutilización de componentes
    - Hace el flujo de datos más predecible
    - Simplifica la sincronización entre componentes

## ViewModel y Estado de aplicación

Para estado más complejo, usa ViewModel:

```kotlin
class CounterViewModel : ViewModel() {
    private val _count = mutableStateOf(0)
    val count: State<Int> = _count
    
    fun increment() {
        _count.value++
    }
    
    fun decrement() {
        _count.value--
    }
}

@Composable
fun CounterScreen(viewModel: CounterViewModel = viewModel()) {
    val count by viewModel.count
    
    Column {
        Text("Contador: $count")
        Row {
            Button(onClick = { viewModel.decrement() }) {
                Text("-")
            }
            Spacer(modifier = Modifier.width(16.dp))
            Button(onClick = { viewModel.increment() }) {
                Text("+")
            }
        }
    }
}
```

## Efectos secundarios

### LaunchedEffect

Para operaciones asíncronas:

```kotlin
@Composable
fun DataLoadingExample(userId: String) {
    var userData by remember { mutableStateOf<User?>(null) }
    var isLoading by remember { mutableStateOf(true) }
    
    LaunchedEffect(userId) {
        isLoading = true
        userData = userRepository.getUser(userId)
        isLoading = false
    }
    
    if (isLoading) {
        CircularProgressIndicator()
    } else {
        userData?.let { user ->
            UserProfile(user = user)
        }
    }
}
```

### DisposableEffect

Para cleanup cuando el composable sale del árbol:

```kotlin
@Composable
fun LocationTracker() {
    DisposableEffect(Unit) {
        val locationListener = LocationListener { /* handle location */ }
        locationManager.requestLocationUpdates(locationListener)
        
        onDispose {
            locationManager.removeUpdates(locationListener)
        }
    }
}
```

!!! warning "Gestión de memoria"
    Siempre limpia recursos como listeners, conexiones de red, o timers en `onDispose` para evitar memory leaks.

## Flujo de datos unidireccional

El patrón recomendado en Compose sigue un flujo unidireccional:

1. **Estado** desciende
2. **Eventos** ascienden
3. **UI** se actualiza automáticamente

Para implementar este patrón correctamente, consulta nuestras [Mejores Prácticas](best-practices.md).