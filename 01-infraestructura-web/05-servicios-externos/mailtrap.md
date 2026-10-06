# Mailtrap: Testing Seguro de Correos Electrónicos

**Mailtrap** es una herramienta de "sandbox" que intercepta correos electrónicos de prueba desde entornos de desarrollo o staging, evitando que lleguen accidentalmente a buzones reales de clientes mientras pruebas tu aplicación.

## Variables de Entorno (.env)
En tu proyecto backend, nunca *hardcodees* las credenciales. Usa tu archivo `.env`:

```env
# Mailtrap SMTP Credentials
SMTP_HOST=sandbox.smtp.mailtrap.io
SMTP_PORT=2525
SMTP_USER=tu_usuario_de_mailtrap
SMTP_PASS=tu_password_de_mailtrap
```

## Ejemplo Básico con Nodemailer (Express/Node.js)

```javascript
const nodemailer = require('nodemailer');

const transporter = nodemailer.createTransport({
  host: process.env.SMTP_HOST,
  port: process.env.SMTP_PORT,
  auth: {
    user: process.env.SMTP_USER,
    pass: process.env.SMTP_PASS
  }
});

const enviarCorreoPrueba = async () => {
  try {
    const info = await transporter.sendMail({
      from: '"Mi App" <no-reply@miapp.com>', // Remitente ficticio
      to: "usuario@ejemplo.com", // Destinatario ficticio (Mailtrap lo interceptará)
      subject: "Correo de prueba ✔",
      html: "<b>¡Hola mundo desde Nodemailer!</b>",
    });
    console.log("Mensaje enviado: %s", info.messageId);
  } catch (error) {
    console.error("Error enviando correo:", error);
  }
};
```
