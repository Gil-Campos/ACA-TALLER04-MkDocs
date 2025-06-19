# Componentes UI Fundamentales

Jetpack Compose ofrece una amplia gama de componentes para construir interfaces de usuario modernas y atractivas.

## Componentes básicos

### Text

El componente más básico para mostrar texto:

```kotlin
@Composable
fun TextExamples() {
    Column {
        Text("Texto simple")
        Text(
            text = "Texto estilizado",
            color = Color.Blue,
            fontSize = 20.sp,
            fontWeight = FontWeight.Bold
        )
        Text(
            text = "Texto con estilo personalizado",
            style = MaterialTheme.typography.headlineMedium
        )
    }
}
```

### Button y variantes

=== "Button básico"
```kotlin
@Composable
fun ButtonExample() {
    Button(onClick = { /* Acción */ }) {
        Text("Presionar")
    }
}
```
=== "OutlinedButton"
```kotlin
@Composable
fun OutlinedButtonExample() {
    OutlinedButton(onClick = { /* Acción */ }) {
        Text("Botón con borde")
    }
}
```
=== "TextButton"
```kotlin
@Composable
fun TextButtonExample() {
    TextButton(onClick = { /* Acción */ }) {
        Text("Botón de texto")
    }
}
```

## Componentes de entrada

### TextField y OutlinedTextField

| Componente | Uso recomendado | Ejemplo |
|------------|-----------------|---------|
| `TextField` | Formularios densos | Campos de búsqueda |
| `OutlinedTextField` | Formularios espaciados | Formularios de registro |

```kotlin
@Composable
fun InputExamples() {
    var text by remember { mutableStateOf("") }
    
    Column(modifier = Modifier.padding(16.dp)) {
        OutlinedTextField(
            value = text,
            onValueChange = { text = it },
            label = { Text("Nombre") },
            placeholder = { Text("Ingresa tu nombre") }
        )
        
        Spacer(modifier = Modifier.height(16.dp))
        
        TextField(
            value = text,
            onValueChange = { text = it },
            label = { Text("Email") }
        )
    }
}
```

!!! tip "Estado en Compose"
    Recuerda usar `remember` y `mutableStateOf` para mantener el estado de los campos de entrada. Aprende más en la sección de [Gestión de Estado](state-management.md).

## Layouts

### Column, Row y Box

```kotlin
@Composable
fun LayoutExamples() {
    // Column: elementos verticalmente
    Column {
        Text("Elemento 1")
        Text("Elemento 2")
    }
    
    // Row: elementos horizontalmente
    Row {
        Text("Izquierda")
        Spacer(modifier = Modifier.weight(1f))
        Text("Derecha")
    }
    
    // Box: elementos superpuestos
    Box {
        Text("Fondo")
        Text(
            "Frente",
            modifier = Modifier.align(Alignment.Center)
        )
    }
}
```

### LazyColumn y LazyRow

Para listas eficientes con muchos elementos:

```kotlin
@Composable
fun LazyListExample() {
    LazyColumn {
        items(100) { index ->
            Card(
                modifier = Modifier
                    .fillMaxWidth()
                    .padding(horizontal = 16.dp, vertical = 4.dp)
            ) {
                Text(
                    text = "Elemento $index",
                    modifier = Modifier.padding(16.dp)
                )
            }
        }
    }
}
```

!!! warning "Rendimiento"
    Usa `LazyColumn` en lugar de `Column` con `verticalScroll()` para listas largas para mejor rendimiento.

## Navegación entre componentes

Para navegar entre pantallas, consulta nuestra guía completa en [Mejores Prácticas](best-practices.md).