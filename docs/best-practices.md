# Mejores Prácticas en Jetpack Compose

Esta sección recopila las mejores prácticas y patrones recomendados para desarrollar aplicaciones robustas y mantenibles con Jetpack Compose.

## Arquitectura y organización

### Estructura de archivos recomendada

```
app/src/main/java/com/tuapp/
├── ui/
│   ├── components/          # Componentes reutilizables
│   ├── screens/            # Pantallas de la app
│   ├── theme/              # Tema y estilos
│   └── navigation/         # Configuración de navegación
├── data/                   # Capa de datos
├── domain/                 # Lógica de negocio
└── MainActivity.kt
```

### Separación de responsabilidades

| Componente | Responsabilidad | Ejemplo |
|------------|-----------------|---------|
| **Screen** | Gestión de estado y coordinación | `HomeScreen.kt` |
| **Component** | UI reutilizable sin estado | `CustomButton.kt` |
| **ViewModel** | Lógica de negocio y estado | `HomeViewModel.kt` |

=== "✅ Screen (Stateful)"

    ```kotlin
    @Composable
    fun HomeScreen(
        viewModel: HomeViewModel = hiltViewModel()
    ) {
        val uiState by viewModel.uiState.collectAsState()
        
        HomeContent(
            uiState = uiState,
            onUserAction = viewModel::handleUserAction
        )
    }
    ```

=== "✅ Component (Stateless)"

    ```kotlin
    @Composable
    fun HomeContent(
        uiState: HomeUiState,
        onUserAction: (UserAction) -> Unit
    ) {
        Column {
            when (uiState) {
                is HomeUiState.Loading -> LoadingIndicator()
                is HomeUiState.Success -> SuccessContent(
                    data = uiState.data,
                    onItemClick = { onUserAction(UserAction.ItemClick(it)) }
                )
                is HomeUiState.Error -> ErrorMessage(uiState.message)
            }
        }
    }
    ```

## Rendimiento

### Evitar recomposiciones innecesarias

!!! tip "Estabilidad de parámetros"
    Compose recompone cuando detecta cambios en los parámetros. Usa tipos estables para optimizar el rendimiento.

```kotlin
// ❌ Parámetro inestable
@Composable
fun UserList(users: List<User>) { ... }

// ✅ Parámetro estable
@Composable
fun UserList(users: ImmutableList<User>) { ... }

// ✅ Alternativa con key
@Composable
fun UserList(users: List<User>) {
    LazyColumn {
        items(users, key = { it.id }) { user ->
            UserItem(user = user)
        }
    }
}
```

### Uso correcto de remember

```kotlin
@Composable
fun ExpensiveComputation(data: String) {
    // ❌ Se ejecuta en cada recomposición
    val result = processData(data)
    
    // ✅ Solo se ejecuta cuando data cambia
    val result = remember(data) {
        processData(data)
    }
    
    Text(result)
}
```

## Patrones de UI

### Composables con múltiples estados

```kotlin
data class ButtonState(
    val isLoading: Boolean = false,
    val isEnabled: Boolean = true,
    val text: String
)

@Composable
fun StatefulButton(
    state: ButtonState,
    onClick: () -> Unit,
    modifier: Modifier = Modifier
) {
    Button(
        onClick = if (state.isLoading) { {} } else onClick,
        enabled = state.isEnabled && !state.isLoading,
        modifier = modifier
    ) {
        if (state.isLoading) {
            Row(verticalAlignment = Alignment.CenterVertically) {
                CircularProgressIndicator(
                    modifier = Modifier.size(16.dp),
                    strokeWidth = 2.dp
                )
                Spacer(modifier = Modifier.width(8.dp))
                Text("Cargando...")
            }
        } else {
            Text(state.text)
        }
    }
}
```

### Manejo de errores

```kotlin
@Composable
fun ErrorBoundary(
    error: Throwable?,
    onRetry: () -> Unit,
    content: @Composable () -> Unit
) {
    if (error != null) {
        Card(
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp),
            colors = CardDefaults.cardColors(
                containerColor = MaterialTheme.colorScheme.errorContainer
            )
        ) {
            Column(
                modifier = Modifier.padding(16.dp),
                horizontalAlignment = Alignment.CenterHorizontally
            ) {
                Icon(
                    Icons.Default.Error,
                    contentDescription = null,
                    tint = MaterialTheme.colorScheme.error
                )
                Spacer(modifier = Modifier.height(8.dp))
                Text(
                    text = "Algo salió mal",
                    style = MaterialTheme.typography.titleMedium
                )
                Text(
                    text = error.message ?: "Error desconocido",
                    style = MaterialTheme.typography.bodyMedium
                )
                Spacer(modifier = Modifier.height(16.dp))
                Button(onClick = onRetry) {
                    Text("Reintentar")
                }
            }
        }
    } else {
        content()
    }
}
```

## Testing

### Testing de Composables

```kotlin
@RunWith(AndroidJUnit4ClassRunner::class)
class HomeScreenTest {
    
    @get:Rule
    val composeTestRule = createComposeRule()
    
    @Test
    fun homeScreen_displaysWelcomeMessage() {
        composeTestRule.setContent {
            HomeScreen()
        }
        
        composeTestRule
            .onNodeWithText("Bienvenido")
            .assertIsDisplayed()
    }
    
    @Test
    fun homeScreen_clickButton_triggersNavigation() {
        val mockNavigation = mockk<() -> Unit>(relaxed = true)
        
        composeTestRule.setContent {
            HomeButton(onClick = mockNavigation)
        }
        
        composeTestRule
            .onNodeWithText("Ir a perfil")
            .performClick()
        
        verify { mockNavigation() }
    }
}
```

!!! warning "Accesibilidad en testing"
    Usa `contentDescription` en elementos interactivos para facilitar tanto la accesibilidad como el testing.

## Navegación

### Configuración con Navigation Compose

```kotlin
@Composable
fun AppNavigation() {
    val navController = rememberNavController()
    
    NavHost(
        navController = navController,
        startDestination = "home"
    ) {
        composable("home") {
            HomeScreen(
                onNavigateToProfile = {
                    navController.navigate("profile")
                }
            )
        }
        composable("profile") {
            ProfileScreen(
                onNavigateBack = {
                    navController.popBackStack()
                }
            )
        }
    }
}
```

## Recursos adicionales

Para profundizar en estos conceptos:

- [Documentación oficial de Compose](https://developer.android.com/jetpack/compose)
- [Samples de Compose en GitHub](https://github.com/android/compose-samples)
- Vuelve a [Gestión de Estado](state-management.md) para casos más avanzados
- Revisa los [Componentes UI](components.md) para más elementos

!!! success "¡Felicidades!"
    Has completado la guía de Jetpack Compose. Ahora tienes las herramientas necesarias para crear aplicaciones Android modernas y eficientes.