# Gestor de Filas Fertya

Este proyecto despliega un sitio estático en Firebase Hosting desde la carpeta `public/`.

## Qué incluye

- `public/`: páginas HTML, CSS y lógica de Firebase.
- `firebase.json`: configuración de Hosting.
- `.firebaserc`: apunta al proyecto de Firebase `gestor-fila`.
- `.github/workflows/firebase-hosting-deploy.yml`: workflow de GitHub Actions para deploy automático en cada push a `main`.

## Configuración

1. Crea un Secret en GitHub llamado `FIREBASE_SERVICE_ACCOUNT` con el JSON de la cuenta de servicio de Firebase.
2. Asegurate de que el repo tiene el workflow en `.github/workflows/firebase-hosting-deploy.yml`.

## Deploy automático

Cada push a la rama `main` ejecuta el workflow y despliega el contenido de `public/` a Firebase Hosting.

## Deploy manual

Si querés desplegar localmente también podés usar Firebase CLI:

```powershell
npm install -g firebase-tools
firebase login
firebase deploy --only hosting
```

> Nota: la URL pública del proyecto es la de `gestor-fila` (`https://gestor-fila.firebaseapp.com`).
