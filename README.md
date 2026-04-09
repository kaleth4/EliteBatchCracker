# EliteBatchCracker

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PowerShell](https://img.shields.io/badge/PowerShell-5.1%2B-blue.svg)](https://docs.microsoft.com/en-us/powershell/)
[![Security](https://img.shields.io/badge/Security-Hardened-red.svg)](https://owasp.org/)
[![DevOps](https://img.shields.io/badge/DevOps-CI%2FCD-green.svg)](https://devops.com/)

> **¡Bienvenido a EliteBatchCracker!**  
> Un ejemplo clásico de script de automatización en Batch llevado al límite de sus capacidades, ahora elevado a estándares de élite en ingeniería de software, seguridad y DevOps. Este proyecto demuestra cómo transformar un script legacy en una herramienta moderna, portable y segura para tareas de auditoría de seguridad (como cracking de contraseñas con John the Ripper).  
> 
> **Advertencia:** Este código es para fines educativos y de pruebas de penetración éticas. Usa con precaución y solo en entornos autorizados. No promueve actividades ilegales.

## 🚀 Descripción General

EliteBatchCracker es una evolución de un script Batch tradicional que automatiza la descarga, configuración y ejecución de herramientas como Perl, John the Ripper (JtR) y 7-Zip (7z). Originalmente diseñado para entornos Windows sin dependencias previas, ahora incorpora mejores prácticas modernas para hacerlo mantenible, seguro y escalable.

- **Portabilidad:** Funciona en Windows x64 sin instalaciones adicionales.
- **Propósito:** Automatización de flujos de trabajo de seguridad, con énfasis en verificación de integridad y optimización de recursos.
- **Tecnologías Clave:** Migrado a PowerShell Core para robustez; compatible con Rust/Go para futuras extensiones.

## 📊 Diagnóstico Técnico: El Estado Actual

### Lo Bueno ✅
- **Portabilidad Extrema:** No requiere instalación previa. Maneja dependencias complejas (Perl, JtR, 7z) de forma dinámica mediante descargas en runtime.
- **Ingenio Técnico:** Usa `FLTMC` para verificar privilegios administrativos y manipulación de registros de Windows para "re-lanzamiento" automático, evitando bloqueos por UAC.

### Lo Malo ❌
- **Fragilidad de Batch:** Lenguaje de 1980 con uso extensivo de `GOTO`, `SETLOCAL ENABLEDELAYEDEXPANSION` y mezcla de lógica de interfaz (UI) con ejecución, lo que complica el mantenimiento y debugging.
- **Riesgos de Seguridad:** Descarga de binarios directamente desde URLs fijas (e.g., raw.githubusercontent.com) expone a ataques de cadena de suministro (Supply Chain Attack). Sin validación de hashes, un compromiso en el repositorio afecta a todos los usuarios.

## 🔄 Mejoras para Nivel Élite: Evolución a una Herramienta Moderna

Si este proyecto evoluciona, abandona el formato `.bat` monolítico por una arquitectura modular en PowerShell Core (o Rust/Go para rendimiento nativo). Aquí las mejoras clave, alineadas con estándares de ingeniería de software (SOLID), seguridad (OWASP) y DevOps (CI/CD con GitHub Actions).

### A. Migración a PowerShell Core (o Rust/Go)
Batch es obsoleto; PowerShell ofrece manejo moderno de datos y errores.

- **Manejo de Objetos:** En lugar de parsear texto con `FOR /F`, trabaja con objetos nativos (e.g., `[PSCustomObject]` para configuraciones).
- **Seguridad Integrada:** Valida hashes SHA-256 de cada herramienta descargada (7zr.exe, john.zip) antes de ejecución. Usa `Invoke-WebRequest` con certificados TLS.
- **Manejo de Errores:** `Try/Catch` real vs. `IF ERRORLEVEL`, con logging estructurado (e.g., a JSON para telemetría).

**Ejemplo de Beneficios en DevOps:** Integra con Azure Pipelines o GitHub Actions para pruebas automatizadas de hashes y escaneo de vulnerabilidades (e.g., con Dependabot).

### B. Modularización (Arquitectura)
Divide el monolito de 500 líneas en módulos reutilizables para escalabilidad.

- **Core:** Lógica principal de cracking y gestión de procesos (e.g., ejecución de JtR con wordlists).
- **Downloader:** Módulo independiente con reintentos exponenciales, proxies y verificación de firmas digitales (e.g., usando `Get-AuthenticodeSignature`).
- **UI:** Interfaz separada: TUI con `Write-Progress` para feedback en consola, o GUI en XAML/WPF para entornos interactivos.

**Estructura de Carpetas Propuesta:**
```
EliteBatchCracker/
├── src/
│   ├── Core.psm1      # Lógica de cracking
│   ├── Downloader.psm1 # Descargas seguras
│   └── UI.psm1        # Interfaz
├── config/
│   └── tools.json     # Configuración de hashes y URLs
├── tests/             # Pruebas unitarias (Pester)
└── docs/              # Documentación adicional
```

### C. Optimización de Recursos
Evita impactos en el sistema con prácticas eficientes.

- **Gestión de Archivos:** Usa enlaces simbólicos (`New-Item -ItemType SymbolicLink`) o variables de entorno temporales (`$env:TEMP`) en lugar de copiar a `%ProgramData%`, minimizando escrituras en disco.
- **Detección Automática:** Identifica arquitectura (x64) y hardware (AVX-512, CUDA) para seleccionar binarios optimizados de JtR (e.g., via `Get-WmiObject Win32_Processor`).
- **DevOps Integration:** Contenedores con Docker for Windows para aislamiento; CI/CD para builds optimizados.

## 💻 Ejemplo: Refactorización de un Bloque

Mira la diferencia entre el Batch original (frágil) y la versión élite en PowerShell (robusta y segura).

### Código Original (Batch)
```batch
IF NOT "%PROCESSOR_ARCHITECTURE%"=="AMD64" (
    ECHO FOR USE WITH x64 SYSTEMS ONLY
    PAUSE & GOTO :EOF
)
```
*Usa el código con precaución.*

### Versión Élite (PowerShell)
```powershell
# Validación robusta y telemetría interna
$arch = [Environment]::GetEnvironmentVariable("PROCESSOR_ARCHITECTURE")
if ($arch -ne "AMD64") {
    Write-Error "Arquitectura $arch no soportada. Se requiere x64."
    # Log para telemetría (opcional: envía a un endpoint seguro)
    return
}

# Descarga segura con validación de Hash
$tools = @{
    "7zr" = @{ url = "https://..."; hash = "A1B2C3..." }
}
try {
    Invoke-RestMethod -Uri $tools.7zr.url -OutFile "$env:Temp\7zr.exe" -UseBasicParsing
    if ((Get-FileHash "$env:Temp\7zr.exe" -Algorithm SHA256).Hash -ne $tools.7zr.hash) {
        throw "Fallo de integridad en la herramienta descargada."
    }
} catch {
    Write-Error "Error en descarga: $($_.Exception.Message)"
    return
}
```
*Usa el código con precaución.*  
Esta versión incluye try/catch, logging y validación criptográfica, elevando la fiabilidad.

## 🛡️ Recomendaciones de Seguridad

Prioriza la hardening para mitigar riesgos en entornos de seguridad.

- **Hardening de Descargas:** Nunca descargues de `raw.githubusercontent.com` sin verificar hashes SHA-256 o firmas GPG. Si el repo se compromete, usa pinned commits o mirrors verificados. Integra escaneo con tools como Trivy en CI/CD.
- **Abstracción de Wordlists:** Evita hardcodear links (e.g., weakpass). Usa un archivo JSON externo o API segura (e.g., RockYou via configuración configurable) para wordlists. Valida orígenes con whitelisting.
- **Mejores Prácticas Adicionales:**
  - **Principio de Menor Privilegio:** Ejecuta solo con admin si es necesario; usa `Start-Process -Verb RunAs` condicionalmente.
  - **Auditoría:** Loggea todas las acciones a un archivo seguro (e.g., con timestamps y hashes).
  - **Cumplimiento:** Alinea con NIST/OWASP; incluye disclaimers legales en el README.

## 📈 Instalación y Uso

1. **Requisitos:** Windows 10+ (x64), PowerShell 5.1+.
2. **Clonación:** `git clone https://github.com/tu-repo/elitebatchcracker.git`
3. **Ejecución:** `.\Run-Elite.ps1 -Mode Audit -WordlistPath config/wordlists.json`
4. **Pruebas:** `Invoke-Pester` para validar módulos.

## 🤝 Contribución

¡Abre issues o PRs! Sigue el [Code of Conduct](CODE_OF_CONDUCT.md) y usa branches feature/*.

## 📄 Licencia

MIT License. Ver [LICENSE](LICENSE) para detalles.

---

*Última actualización: 2023. Desarrollado con ❤️ para la comunidad de ciberseguridad ética.*
