<h1 align="center">Informe de vulnerabilidades - OWASP (SQL Injection)</h1>

<img width="1140" height="600" alt="image" src="https://github.com/user-attachments/assets/fe2199db-c35b-4fbf-ad7d-d532ae78161a" />


---

## SQL Injection para extraer usuarios y contrasenas

### Evidencia en el codigo
- La consulta se construye concatenando datos del usuario:
  - `$query = "SELECT * FROM users WHERE name = '$username' AND passwd = '$password'";`
- Esto permite inyectar SQL en `username` y/o `password`.

### Explotacion (consulta unica)
1. Abre la web en http://localhost/login.php
2. En el campo **Usuario** escribe:
  ```
  ' UNION ALL SELECT 1,'dummy','dummy' UNION ALL SELECT id,name,passwd FROM users -- 
  ```
3. En **Contraseña** escribe cualquier texto (por ejemplo `x`)
4. Pulsa **Iniciar Sesion**

**Resultado**: se listan todos los usuarios y contrasenas devueltos por la consulta.

**Nota**: el login evalua `fetchColumn() > 0`, por eso el primer valor debe ser `1` y el comentario debe terminar en `-- ` (con espacio) para anular el resto de la sentencia.

<img width="1918" height="193" alt="image" src="https://github.com/user-attachments/assets/40006b14-be68-468d-83c6-bf1250218df4" />

---

### Mitigacion
Medidas correctas para eliminar la vulnerabilidad:
- Usar consultas preparadas con `PDO->prepare()` y `bind/execute`.
- No concatenar directamente los valores de usuario.
- Evitar mostrar consultas o credenciales en la respuesta.

#### Codigo corregido (login.php)
```php
<?php
$db = new PDO("sqlite:/var/www/html/data.db");
$db->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

if ($_SERVER["REQUEST_METHOD"] === "POST") {
  $username = isset($_POST["username"]) ? trim($_POST["username"]) : "";
  $password = isset($_POST["password"]) ? trim($_POST["password"]) : "";

  if ($username === "" || $password === "") {
    echo "Usuario y contrasena son obligatorios";
  } else {
    $stmt = $db->prepare("SELECT id, name FROM users WHERE name = :username AND passwd = :password");
    $stmt->execute([
      ":username" => $username,
      ":password" => $password,
    ]);

    $row = $stmt->fetch(PDO::FETCH_ASSOC);
    if ($row) {
      $safeName = htmlspecialchars($row["name"], ENT_QUOTES, "UTF-8");
      echo "Inicio de sesion exitoso<br>";
      echo "Bienvenido, " . $safeName;
    } else {
      echo "Usuario o contrasena incorrectos";
    }
  }
}
?>
```

### Verificacion de mitigacion
1. Repetir la misma consulta:
  ```
  ' UNION ALL SELECT 1,'dummy','dummy' UNION ALL SELECT id,name,passwd FROM users -- 
  ```

<img width="1918" height="142" alt="image" src="https://github.com/user-attachments/assets/91958657-0545-4ac7-abce-999bd8ce9e85" />
