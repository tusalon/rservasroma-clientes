# Setup — rservasroma-clientes APK

## PASO 1 — Crear el repo en GitHub
Crear repo `tusalon/rservasroma-clientes` vacío (sin README).
Luego clonar y copiar estos archivos dentro.

## PASO 2 — Copiar icono de marca
Copiar el icono de Rservasroma a:
```
icons/icon-512x512.png
```

## PASO 3 — Generar el keystore (UNA SOLA VEZ)
Desde PowerShell con Java instalado:
```powershell
keytool -genkey -v `
  -keystore rservasroma-clientes.keystore `
  -alias rservasroma `
  -keyalg RSA -keysize 2048 -validity 10000 `
  -dname "CN=Rservasroma, OU=Mobile, O=Rservasroma, L=Miami, S=FL, C=US"
```
Guardar la contraseña que se use — se necesita después.

## PASO 4 — Obtener el SHA-256 del keystore
```powershell
keytool -list -v -keystore rservasroma-clientes.keystore -alias rservasroma
```
Buscar la línea "SHA256:" y copiar el valor.
Pegar en `.well-known/assetlinks.json` reemplazando REEMPLAZAR_CON_SHA256_DEL_KEYSTORE.

## PASO 5 — Guardar .well-known/assetlinks.json en rservasroma
El archivo `.well-known/assetlinks.json` de este repo debe copiarse al repo
`rservasroma` (el de GitHub Pages) para que Android pueda verificar el dominio.

## PASO 6 — Configurar GitHub Secrets en rservasroma-clientes
En GitHub → Settings → Secrets → Actions, agregar:

| Secret | Valor |
|--------|-------|
| ANDROID_KEYSTORE_BASE64 | `[Convert]::ToBase64String([IO.File]::ReadAllBytes("rservasroma-clientes.keystore"))` en PowerShell |
| ANDROID_KEYSTORE_PASSWORD | La contraseña del keystore |
| ANDROID_KEY_ALIAS | `rservasroma` |
| ANDROID_KEY_PASSWORD | La contraseña de la clave (igual que keystore si se usó la misma) |

Para obtener el base64:
```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("rservasroma-clientes.keystore")) | Set-Clipboard
```

## PASO 7 — Generar el proyecto Android
```powershell
npm install
npm run android:add
```
Luego reemplazar `android/app/src/main/AndroidManifest.xml`
con el contenido de `android-patches/AndroidManifest.xml`.

## PASO 8 — Primer push
```powershell
git add .
git commit -m "feat: setup APK maestro clientes"
git push origin main
```

## PASO 9 — Compilar el APK
En GitHub → Actions → "Build Android APK Clientes" → Run workflow.
Descargar el artifact `rservasroma-clientes.apk`.

## PASO 10 — Probar deep links
1. Instalar el APK en un Android
2. Abrir WhatsApp y pegar: https://tusalon.github.io/rservasroma/?s=gordis-nails
3. Tocar el link → debe abrir el APK directamente (no Chrome)
