# Informe de vulnerabilidades - OWASP (SQL Injection)

## Alcance
- Aplicacion: login en [SQL Injection/login.php](SQL%20Injection/login.php)
- Entorno: Docker (php:apache) en [SQL Injection/docker-compose.yaml](SQL%20Injection/docker-compose.yaml)
- Fecha: 19/02/2026

---

## 1) SQL Injection (A03:2021 - Injection)

### Evidencia en el codigo
- La consulta se construye concatenando datos del usuario:
  - `$query = "SELECT * FROM users WHERE name = '$username' AND passwd = '$password'";`
- Esto permite inyectar SQL en `username` y/o `password`.

### Explotacion
1. Abre la web en http://localhost/
2. En el campo **Usuario** escribe:
   ```
   ' OR '1'='1' --
   ```
3. En **Contraseña** escribe cualquier texto (por ejemplo `x`)
4. Pulsa **Iniciar Sesion**

**Resultado esperado**: el login se valida sin conocer credenciales reales y se muestra “Inicio de sesión exitoso”.

#### Captura requerida
- **Captura 1**: formulario con el payload en Usuario y el resultado “Inicio de sesión exitoso”.

---

### Mitigacion
Medidas correctas para eliminar la vulnerabilidad:
- Usar consultas preparadas con `PDO->prepare()` y `bind/execute`.
- No concatenar directamente los valores de usuario.
- Validar y normalizar entradas.
- Evitar mostrar datos sensibles o consultas en la respuesta.

#### Codigo corregido (login.php)
```php
<?php
$db = new PDO("sqlite:/var/www/html/data.db");
$db->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

if ($_SERVER["REQUEST_METHOD"] === "POST") {
  $username = isset($_POST["username"]) ? trim($_POST["username"]) : "";
  $password = isset($_POST["password"]) ? trim($_POST["password"]) : "";

  if ($username === "" || $password === "") {
    echo "Usuario y contraseña son obligatorios";
  } else {
    $stmt = $db->prepare("SELECT id, name FROM users WHERE name = :username AND passwd = :password");
    $stmt->execute([
      ":username" => $username,
      ":password" => $password,
    ]);

    $row = $stmt->fetch(PDO::FETCH_ASSOC);
    if ($row) {
      $safeName = htmlspecialchars($row["name"], ENT_QUOTES, "UTF-8");
      echo "Inicio de sesión exitoso<br>";
      echo "Bienvenido, " . $safeName;
    } else {
      echo "Usuario o contraseña incorrectos";
    }
  }
}
?>
```

### Verificacion de mitigacion
1. Repetir el mismo payload:
   ```
   ' OR '1'='1' --
   ```
2. Resultado esperado: **“Usuario o contraseña incorrectos”**.

#### Captura requerida
- **Captura 2**: mismo payload, pero el login falla tras la mitigacion.

---

## 2) Divulgacion de informacion sensible (A01:2021 - Broken Access Control / A09:2021 - Security Logging / A02:2021 - Cryptographic Failures)

### Evidencia en el codigo
- Se muestra la consulta SQL ejecutada:
  - `echo "Consulta ejecutada: " . $query`
- Se imprimen datos sensibles:
  - `Contraseña: ...`

### Explotacion
1. Haz un intento de login valido o invalido.
2. Observa que la pagina imprime:
   - La consulta SQL exacta.
   - Credenciales.

**Resultado esperado**: se revela la estructura SQL y credenciales, lo que facilita ataques y fuga de datos.

#### Captura requerida
- **Captura 3**: pagina mostrando “Consulta ejecutada” y/o datos de usuarios.

---

### Mitigacion
- Eliminar cualquier `echo` que exponga SQL o credenciales.
- Mostrar solo mensajes genericos (“Credenciales invalidas”).
- Si se necesita diagnostico, enviar logs al servidor, no a la respuesta web.
- No mostrar contraseñas en ningun caso.

#### Codigo corregido
```php
<?php
// No mostrar consultas ni credenciales en la respuesta
echo "Usuario o contraseña incorrectos";
// Si necesitas diagnostico, registra en servidor:
// error_log("Fallo de login para usuario: " . $username);
?>
```

### Verificacion de mitigacion
1. Repetir un intento de login.
2. Resultado esperado: **no** aparece consulta ni contraseñas en pantalla.

#### Captura requerida
- **Captura 4**: pagina con mensaje generico, sin SQL ni credenciales.

---

## 3) Ausencia de proteccion ante fuerza bruta (A07:2021 - Identification and Authentication Failures)

### Evidencia en el comportamiento
- El login permite intentos ilimitados.
- No hay captcha, bloqueo temporal ni rate limit.

### Explotacion
1. Realizar multiples intentos consecutivos con usuarios comunes.
2. No se aplica bloqueo ni demora.

**Resultado esperado**: se pueden automatizar intentos sin restricciones.

#### Captura requerida
- **Captura 5**: varios intentos consecutivos sin bloqueo (se puede mostrar historial de intentos en la misma pagina o en la consola del navegador).

---

### Mitigacion
- Limitar intentos por IP/usuario (rate limit).
- Introducir bloqueo temporal o captcha tras N fallos.
- Registrar intentos fallidos y alertas.

#### Codigo corregido
```php
<?php
session_start();

$maxAttempts = 5;
$blockSeconds = 300; // 5 minutos
$now = time();

if (!isset($_SESSION["login_attempts"])) {
  $_SESSION["login_attempts"] = 0;
  $_SESSION["login_first_attempt"] = $now;
}

// Reinicia la ventana si paso el bloqueo
if ($now - $_SESSION["login_first_attempt"] > $blockSeconds) {
  $_SESSION["login_attempts"] = 0;
  $_SESSION["login_first_attempt"] = $now;
}

if ($_SESSION["login_attempts"] >= $maxAttempts) {
  echo "Demasiados intentos. Intenta mas tarde.";
  exit;
}

// En cada fallo de login:
// $_SESSION["login_attempts"]++;
?>
```

### Verificacion de mitigacion
1. Tras varios intentos fallidos, el sistema debe bloquear temporalmente o requerir captcha.

#### Captura requerida
- **Captura 6**: bloqueo o mensaje de limitacion tras varios intentos.

---

## Resumen de vulnerabilidades
- SQL Injection
- Divulgacion de informacion sensible
- Ausencia de proteccion ante fuerza bruta

---

## Notas de capturas
- Usa el navegador mostrando URL y resultado visible.
- Para cada captura, nombra el archivo como:
  - `01_sql_injection_explotacion.png`
  - `02_sql_injection_mitigada.png`
  - `03_info_leak_explotacion.png`
  - `04_info_leak_mitigada.png`
  - `05_bruteforce_explotacion.png`
  - `06_bruteforce_mitigada.png`
