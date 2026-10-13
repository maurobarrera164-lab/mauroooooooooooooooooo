const express = require('express');
const sqlite3 = require('sqlite3').verbose();
const path = require('path');
const app = express();

app.use(express.urlencoded({ extended: true }));

// 1. Conexión a la base de datos SQLite (archivo local)
const db = new sqlite3.Database('./biblioteca.db');

// 2. Crear la tabla de préstamos si no existe
db.run(`CREATE TABLE IF NOT EXISTS prestamos (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    socio TEXT,
    libro TEXT,
    fecha_prestamo TEXT,
    fecha_devolucion TEXT
)`);

// Servir el formulario HTML
app.get('/', (req, res) => {
    res.sendFile(path.join(__dirname, 'index.html'));
});

// 3. Extraer los datos del formulario y agregarlos (INSERT) a la base de datos
app.post('/guardar', (req, res) => {
    const { socio, libro, fecha_prestamo, fecha_devolucion } = req.body;
    
    const stmt = db.prepare("INSERT INTO prestamos (socio, libro, fecha_prestamo, fecha_devolucion) VALUES (?, ?, ?, ?)");
    stmt.run(socio, libro, fecha_prestamo, fecha_devolucion);
    stmt.finalize();

    res.send("<h1>¡Registro de biblioteca guardado con éxito!</h1><a href='/'>Volver</a>");
});

// Iniciar el servidor
app.listen(3000, () => console.log('Servidor corriendo en http://localhost:3000'));




<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Cuestionario de Biblioteca</title>
</head>
<body>
    <h2>Registro de Préstamo - Biblioteca</h2>
    <form action="/guardar" method="POST">
        <label>Nombre del Socio:</label><br>
        <input type="text" name="socio" required><br><br>

        <label>Título del Libro:</label><br>
        <input type="text" name="libro" required><br><br>

        <label>Fecha de Préstamo:</label><br>
        <input type="date" name="fecha_prestamo" required><br><br>

        <label>Fecha de Devolución:</label><br>
        <input type="date" name="fecha_devolucion" required><br><br>

        <button type="submit">Registrar</button>
    </form>
</body>
</html>
