# Informe de auditoría · Core financiero de la COOPAC Santa Rosa

**SI-084 · Auditoría de Sistemas** · Examen práctico de Unidad I

| | |
|---|---|
| **Apellidos y nombres** | Salas Jimenez, Walter Emmanuel |
| **Código de estudiante** | 2022073896 |
| **URL del repositorio** | `https://github.com/Wsalas651/si084-caso-coopac/tree/examen-u1` |
| **Fecha** | 30/09/2026 |

## 1. Resultados de los procedimientos

| Regla | Resultado, con cifras | ¿Cumple? | Archivo de evidencia |
|---|---|---|---|
| R1 | Publicado en 0.0.0.0, puerto 55432 | No | `evidencias/P1_puertos.txt` |
| R2 | Contraseña en texto plano en docker-compose.yml línea 10 (POSTGRES_PASSWORD). Tiene 10 caracteres, incumple el mínimo de 12 | No | `evidencias/P2_credenciales.txt` |
| R3 | La cuenta `app_core` tiene el atributo Superuser además de `postgres` (1 cuenta no autorizada) | No | `evidencias/P3_roles.txt` |
| R4 | 16 cuentas de 11 personas cesadas (más antiguo 2015-12-18). 22 cuentas sin documento (4 ADMIN) | No | `evidencias/P4_cesados.txt` · `evidencias/P4_genericas.txt` |
| R5 | 23 desembolsos, monto total de 709370.47 por 21 usuarios distintos | No | `evidencias/P5_segregacion.txt` |
| R6 | off y none. Configurados en la línea 11 de docker-compose.yml | No | `evidencias/P6_registro.txt` |
| R7 | Último respaldo exitoso: 2025-11-14. Desde esa fecha hasta el corte 31/12/2025 pasaron 47 días sin respaldo. Falta la tabla `desembolsos` en la restauración (excluida con `--exclude-table` en respaldo.sh) | No | `evidencias/P7_respaldos.txt` · `evidencias/P7_restauracion.txt` |

## 2. Hallazgo 1

| Elemento | Contenido |
|---|---|
| Título | Aprobación de desembolsos por el mismo usuario que los registra |
| Condición | Se encontraron 23 desembolsos por un monto total de 709,370.47, realizados por 21 usuarios distintos, en los que el usuario aprueba lo que él mismo registró. Evidencia: evidencias/P5_segregacion.txt |
| Criterio | R5. Un desembolso que supera el umbral de aprobación no puede aprobarlo quien lo registró. Control A.5.3 Segregación de funciones |
| Causa | Falta de validación a nivel de base de datos o aplicación que impida que usuario_aprueba sea igual a usuario_registra cuando el monto supera el umbral. |
| Efecto | Riesgo inminente de fraude financiero y pérdida económica por un valor identificado de 709,370.47 soles. |
| Recomendación | Implementar reglas y validaciones estrictas que impidan auto-aprobar desembolsos. Responsable: Jefe de Sistemas. Plazo: Inmediato. |

## 3. Hallazgo 2

| Elemento | Contenido |
|---|---|
| Título | Respaldo incompleto y fallido de la base de datos |
| Condición | El último respaldo es del 2025-11-14 (hace 47 días). Además, la tabla "desembolsos" fue excluida del respaldo y no se restauró. Evidencia: evidencias/P7_respaldos.txt y evidencias/P7_restauracion.txt |
| Criterio | R7. Respaldo diario completo, que incluye la tabla de desembolsos. Control A.8.13 Respaldo de la información |
| Causa | El script respaldo.sh excluye explícitamente la tabla "desembolsos", y el log muestra que las últimas tareas fallaron por falta de espacio en disco (No space left on device). |
| Efecto | Pérdida potencial de la información vital de desembolsos y de los últimos 47 días de operaciones en caso de un desastre. |
| Recomendación | Ampliar la capacidad de almacenamiento del dispositivo y corregir el script para incluir todas las tablas. Responsable: Jefe de Sistemas. Plazo: Inmediato. |
