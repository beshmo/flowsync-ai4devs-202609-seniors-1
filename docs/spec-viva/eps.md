## Purpose

Permitir que una persona cree una cuenta, inicie sesión, mantenga su sesión activa entre visitas y la cierre, y proteger tanto la API como la interfaz para que solo quien tiene una sesión válida acceda a los datos de su cuenta.

## Requirements

### Requirement: Registro de cuenta

El sistema SHALL permitir crear una cuenta nueva a partir de un email, una contraseña y su confirmación, y SHALL iniciar sesión automáticamente tras crearla.

#### Scenario: Alta con datos válidos

- **WHEN** se envía una petición `POST /api/v1/auth/signup` con un email no registrado previamente, una contraseña de entre 8 y 32 caracteres y una confirmación que coincide con ella
- **THEN** la respuesta tiene estado 200, incluye los datos de la cuenta creada (identificador, nombre, email, iniciales y fechas de alta y actualización) y un token de acceso; ese token queda operativo de inmediato para llamar a rutas protegidas

#### Scenario: Alta sin nombre completo

- **WHEN** se envía `POST /api/v1/auth/signup` con el campo del nombre completo vacío o ausente de valor (pero presente en la petición), junto con email, contraseña y confirmación válidos
- **THEN** la cuenta se crea igualmente, sin nombre asociado, y sus iniciales se calculan a partir del email

#### Scenario: Email ya registrado

- **WHEN** se envía `POST /api/v1/auth/signup` con un email que ya pertenece a otra cuenta
- **THEN** la respuesta tiene estado 422 e indica, para el campo del email, que ya existe una cuenta con ese correo

#### Scenario: Contraseñas que no coinciden

- **WHEN** se envía `POST /api/v1/auth/signup` con una confirmación de contraseña distinta de la contraseña
- **THEN** la respuesta tiene estado 422 e indica, para el campo de confirmación, que las contraseñas no coinciden

#### Scenario: Contraseña fuera de longitud

- **WHEN** se envía `POST /api/v1/auth/signup` con una contraseña de menos de 8 o de más de 32 caracteres
- **THEN** la respuesta tiene estado 422 e indica el límite de longitud incumplido para el campo de contraseña

#### Scenario: Formulario de registro en pantalla

- **WHEN** una persona sin sesión abre la pantalla de registro
- **THEN** ve un formulario con campos para nombre completo (marcado como opcional), email, contraseña con una pista de "entre 8 y 32 caracteres" y repetición de contraseña, además de un enlace para ir a la pantalla de inicio de sesión

#### Scenario: Aviso local de contraseñas distintas antes de enviar

- **WHEN** en la pantalla de registro se envía el formulario con la contraseña y su repetición distintas entre sí
- **THEN** se muestra bajo el campo de repetición el aviso de que las contraseñas no coinciden, y no se realiza ninguna petición al servidor

#### Scenario: Errores del servidor bajo cada campo

- **WHEN** en la pantalla de registro se envía el formulario y el servidor devuelve un error de validación asociado a uno o varios de los campos visibles (nombre, email, contraseña o repetición)
- **THEN** cada error se muestra bajo su campo correspondiente, en castellano, y el botón de enviar vuelve a estar disponible

### Requirement: Inicio de sesión

El sistema SHALL permitir iniciar sesión con un email y una contraseña ya registrados, y SHALL rechazar cualquier combinación que no corresponda a una cuenta existente sin revelar cuál de los dos datos es incorrecto.

#### Scenario: Inicio de sesión con credenciales correctas

- **WHEN** se envía `POST /api/v1/auth/login` con el email y la contraseña de una cuenta existente
- **THEN** la respuesta tiene estado 200 e incluye los datos de la cuenta y un token de acceso operativo para llamar a rutas protegidas

#### Scenario: Credenciales incorrectas

- **WHEN** se envía `POST /api/v1/auth/login` con un email que no existe, o con un email existente y una contraseña que no le corresponde
- **THEN** la respuesta tiene estado 400, sin indicar cuál de los dos datos falló

#### Scenario: Formulario de inicio de sesión en pantalla

- **WHEN** una persona sin sesión abre la pantalla de inicio de sesión
- **THEN** ve un formulario con campos de email y contraseña, un botón para entrar y un enlace para ir a la pantalla de registro

#### Scenario: Aviso de credenciales incorrectas en pantalla

- **WHEN** en la pantalla de inicio de sesión se envía el formulario con un email y una contraseña que no corresponden a ninguna cuenta
- **THEN** se muestra un aviso indicando que el email o la contraseña no son correctos, sin señalar un campo concreto, y el botón de enviar vuelve a estar disponible

### Requirement: Acceso a rutas protegidas

El sistema SHALL exigir un token de acceso válido para consultar el perfil de la cuenta y para cerrar sesión, y SHALL rechazar la petición cuando el token falta o no es válido.

#### Scenario: Consulta del perfil con token válido

- **WHEN** se envía `GET /api/v1/account/profile` con la cabecera de autorización portando un token de acceso obtenido por registro o inicio de sesión
- **THEN** la respuesta tiene estado 200 e incluye los datos de la cuenta asociada a ese token (identificador, nombre, email, iniciales y fechas de alta y actualización)

#### Scenario: Ruta protegida sin token o con token inválido

- **WHEN** se envía `GET /api/v1/account/profile` o `POST /api/v1/account/logout` sin cabecera de autorización, o con un token que no corresponde a ninguna sesión activa
- **THEN** la respuesta tiene estado 401

#### Scenario: Un token de acceso no caduca por sí solo

- **WHEN** se usa un token de acceso obtenido por registro o inicio de sesión, en peticiones sucesivas separadas en el tiempo, sin que de por medio se haya cerrado sesión con él
- **THEN** cada una de esas peticiones sigue siendo aceptada

### Requirement: Cierre de sesión

El sistema SHALL invalidar el token de acceso usado en la petición de cierre de sesión, de modo que deje de servir para acceder a rutas protegidas.

#### Scenario: Cierre de sesión con token válido

- **WHEN** se envía `POST /api/v1/account/logout` con la cabecera de autorización portando un token de acceso activo
- **THEN** la respuesta tiene estado 200 con un mensaje de confirmación, y ese mismo token deja de ser aceptado en peticiones posteriores a rutas protegidas

#### Scenario: Cerrar sesión desde la pantalla de perfil

- **WHEN** una persona con sesión activa pulsa el botón de cerrar sesión en la pantalla de perfil
- **THEN** el botón queda deshabilitado mientras se procesa, y la persona termina en la pantalla de inicio de sesión sin sesión activa

### Requirement: Persistencia de la sesión en el navegador

El sistema SHALL recordar la sesión de una persona entre recargas y visitas del navegador mientras el token siga siendo válido, y SHALL cerrarla automáticamente cuando el backend ya no lo reconozca.

#### Scenario: Recarga de página con sesión activa

- **WHEN** se recarga la pantalla estando con sesión iniciada
- **THEN** se muestra brevemente un indicador de carga y, a continuación, la sesión se mantiene sin pedir credenciales de nuevo

#### Scenario: Recarga con un token que el backend ya no reconoce

- **WHEN** se recarga la pantalla con un token guardado que el backend rechaza por inválido
- **THEN** la sesión se cierra, la persona termina en la pantalla de inicio de sesión y ve un aviso explicando que su sesión caducó y debe volver a iniciarla

#### Scenario: Recarga sin poder contactar con el servidor

- **WHEN** se recarga la pantalla con un token guardado y el servidor no responde (por ejemplo, está caído o no hay red)
- **THEN** la persona ve la pantalla de inicio de sesión con un aviso de que no se pudo restaurar la sesión, sin que el token guardado se elimine

### Requirement: Navegación según el estado de sesión

El sistema SHALL dirigir a cada persona a las pantallas que corresponden a su estado de sesión: fuera de alcance las pantallas de cuenta para quien no tiene sesión, y fuera de alcance las pantallas de acceso para quien ya la tiene.

#### Scenario: Intentar ver el perfil sin sesión

- **WHEN** una persona sin sesión activa intenta entrar en la pantalla de perfil
- **THEN** es dirigida a la pantalla de inicio de sesión

#### Scenario: Intentar ver login o registro con sesión activa

- **WHEN** una persona con sesión activa intenta entrar en la pantalla de inicio de sesión o en la de registro
- **THEN** es dirigida a la pantalla de perfil

#### Scenario: Ruta desconocida

- **WHEN** se visita cualquier dirección de la aplicación que no corresponde a ninguna pantalla conocida
- **THEN** se redirige a la pantalla de perfil (y desde ahí, si no hay sesión, a la de inicio de sesión)

### Requirement: Perfil de la cuenta

El sistema SHALL mostrar a la persona con sesión activa los datos de su propia cuenta.

#### Scenario: Ver los datos de la cuenta propia

- **WHEN** una persona con sesión activa abre la pantalla de perfil
- **THEN** ve sus iniciales, su nombre completo (o la indicación de que no tiene nombre registrado), su email y la fecha en la que se dio de alta


---

# PARTE B

1. Contabilidad de requisitos.

- Cuántos requisitos escribió el agente: 8
- Cuántos comprobaste tú abriendo el código: 3

2. Las incoherencias que aparecieron al escribirla.

- Credenciales inválidas en login → 400 sin indicar qué campo falló (confirmado en el código de @adonisjs/auth, no solo por el comentario del frontend).
- Token sin ruta protegida → 401.
- El token de acceso no tiene expiresIn configurado, así que no caduca por sí solo (solo por logout explícito).

3. Lo que no supiste decidir si era un bug o el contrato.

- Según mi criterio, el escenario: "Un token de acceso no caduca por sí solo", es un bug.
