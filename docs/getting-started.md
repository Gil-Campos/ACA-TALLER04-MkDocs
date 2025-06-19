# Primeros Pasos con Jetpack Compose

Esta sección te guiará a través de la configuración inicial y la creación de tu primer Composable.

## Configuración del proyecto

### Dependencias necesarias

=== "Gradle (Kotlin DSL)"

    ```kotlin
    android {
        compileSdk 34
        
        defaultConfig {
            minSdk 21
            targetSdk 34
        }
        
        compileOptions {
            sourceCompatibility = JavaVersion.VERSION_1_8
            targetCompatibility = JavaVersion.VERSION_1_8
        }
        
        kotlinOptions {
            jvmTarget = "1.8"
        }
        
        buildFeatures {
            compose = true
        }
        
        composeOptions {
            kotlinCompilerExtensionVersion = "1.5.8"
        }
    }
    
    dependencies {
        val composeBom = platform("androidx.compose:compose-bom:2024.02.00")
        implementation(composeBom)
        
        implementation("androidx.compose.ui:ui")
        implementation("androidx.compose.ui:ui-tooling-preview")
        implementation("androidx.compose.material3:material3")
        implementation("androidx.activity:activity-compose:1.8.2")
        
        debugImplementation("androidx.compose.ui:ui-tooling")
        debugImplementation("androidx.compose.ui:ui-test-manifest")
    }
    ```

=== "Gradle (Groovy)"

    ```groovy
    android {
        compileSdk 34
        
        defaultConfig {
            minSdk 21
            targetSdk 34
        }
        
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        
        kotlinOptions {
            jvmTarget = '1.8'
        }
        
        buildFeatures {
            compose true
        }
        
        composeOptions {
            kotlinCompilerExtensionVersion '1.5.8'
        }
    }
    
    dependencies {
        def compose_bom = platform('androidx.compose:compose-bom:2024.02.00')
        implementation compose_bom
        
        implementation 'androidx.compose.ui:ui'
        implementation 'androidx.compose.ui:ui-tooling-preview'
        implementation 'androidx.compose.material3:material3'
        implementation 'androidx.activity:activity-compose:1.8.2'
        
        debugImplementation 'androidx.compose.ui:ui-tooling'
        debugImplementation 'androidx.compose.ui:ui-test-manifest'
    }
    ```

!!! warning "Versión mínima"
    Asegúrate de usar Android Studio Arctic Fox (2020.3.1) o superior para el soporte completo de Jetpack Compose.

## Tu primer Composable

Los Composables son funciones que describen la UI. Aquí tienes un ejemplo básico:

```kotlin
@Composable
fun Greeting(name: String) {
    Text(text = "¡Hola $name!")
}

@Preview(showBackground = true)
@Composable
fun GreetingPreview() {
    MyAppTheme {
        Greeting("Android")
    }
}
```

Características clave de un Composable

Anotación @Composable: Marca que la función puede ser usada en Compose
Funciones puras: No tienen efectos secundarios
Reutilizables: Pueden ser llamadas múltiples veces
Componibles: Se pueden combinar para crear UIs complejas

Estructura básica de una app
```
kotlinclass MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MyAppTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    Greeting("Jetpack Compose")
                }
            }
        }
    }
}
```
Para más información sobre componentes específicos, consulta la sección de [Componentes UI](components.md).