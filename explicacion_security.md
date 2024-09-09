
# Explicación Detallada de SecurityConfig.java

## Anotaciones
- `@Configuration`: Indica que esta clase define beans de configuración que son gestionados por el contenedor de Spring.
- `@EnableWebSecurity`: Habilita la seguridad web de Spring Security en esta aplicación.
- `@RequiredArgsConstructor`: Genera automáticamente un constructor con argumentos para los campos finales y anotados con `@NonNull`. 

## Beans y Dependencias
- `UserDetailsService`: Interfaz proporcionada por Spring Security para recuperar los detalles del usuario (como nombre de usuario, roles, etc.) desde la base de datos o cualquier otra fuente.
- `AuthenticationManagerBuilder`: Este objeto se usa para configurar la autenticación y agregar detalles de cómo cargar los usuarios y sus credenciales.

## Método `authenticationManager`
Este método declara un bean de tipo `AuthenticationManager`. El `AuthenticationManager` es responsable de autenticar las solicitudes entrantes, usando la configuración proporcionada en `AuthenticationConfiguration`.

## Método `filterChain`
Este es el núcleo de la configuración de seguridad en Spring Security:

- **Deshabilitar CSRF**: Se llama a `http.csrf().disable();` para desactivar la protección contra ataques CSRF, que generalmente es útil en aplicaciones donde no se maneja la autenticación basada en sesión, como en este caso con JWT.

- **Política de sesión**: Se define la política de sesión como `STATELESS`, lo que significa que no se mantendrá el estado de las sesiones, es decir, no se guarda información del usuario en el servidor. Esto es típico de una arquitectura que utiliza tokens JWT.

- **Filtros personalizados**:
  - `CustomAuthenticationFilter`: Filtro de autenticación personalizado que procesa las solicitudes de inicio de sesión en la ruta `/api/login`. Este filtro maneja la autenticación JWT.
  - `CustomAuthorizationFilter`: Filtro que gestiona la autorización y se coloca antes del filtro estándar de autenticación. Este filtro revisa si el usuario tiene permisos para acceder a ciertos recursos en función de su rol.

- **Permisos y Autorización**:
  - La URL `/api/login/**` es accesible para todos sin autenticación.
  - Los usuarios con el rol `ROLE_USER` tienen permiso para hacer solicitudes `GET` a `/api/users`.
  - Los usuarios con el rol `ROLE_ADMIN` tienen permiso para hacer solicitudes `POST` a `/api/users`.
  - Todas las demás rutas requieren autenticación. (Es decir, que requiere que el usuario esté logueado, con independencia del role que le hayamos asignado. Básicamente es un "si sé quién eres, puedes entrar.")

## Integración de Filtros
- Se añade el `CustomAuthenticationFilter` al flujo de seguridad usando `http.addFilter(customAuthenticationFilter)`.
- El `CustomAuthorizationFilter` se añade antes del filtro estándar de autenticación, usando `http.addFilterBefore`.

Este archivo configura tanto la autenticación como la autorización usando filtros personalizados y JWT, con control de roles en las rutas.
