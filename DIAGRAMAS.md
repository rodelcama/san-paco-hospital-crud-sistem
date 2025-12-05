# Diagrama de Clases - Hospital San Paco

Este documento presenta el diseño de clases para el sistema de gestión hospitalaria "San Paco", basado en los requerimientos y formularios web analizados.

## 1. Justificación del Diseño

Se ha optado por un diseño orientado a objetos utilizando herencia para gestionar los diferentes tipos de usuarios:

*   **Usuario (Abstracta):** Contiene los datos comunes a todas las personas que interactúan con el sistema (nombre, documento, contacto, credenciales).
*   **UsuarioInterno (Extiende Usuario):** Representa a los empleados del hospital. Incluye código de empleado, rol y permisos.
*   **Medico (Extiende UsuarioInterno):** Se separa como una entidad independiente que hereda de `UsuarioInterno`. Esto se debe a que, aunque es un empleado, tiene atributos exclusivos como `especialidad` y es la única entidad que puede tener `Agendas` y ser asignada a `Citas`.
*   **Paciente (Extiende Usuario):** Representa a los usuarios externos. Contiene información clínica básica (tipo de sangre, alergias) y contactos de emergencia.

## 2. Descripción Textual de Clases

### Clases de Usuarios

*   **`Usuario` (Clase Abstracta)**
    *   Atributos: `nombres`, `apellidos`, `tipoDocumento`, `numeroDocumento`, `telefono`, `email`, `direccion`, `ciudad`, `pais`, `usuarioLogin`, `password`.
    *   Métodos: `iniciarSesion()`, `cerrarSesion()`.

*   **`UsuarioInterno` extends `Usuario`**
    *   Atributos: `codigoInterno` (ej. EMP-00123), `rol` (ADMIN, RECEPCIONISTA, OPERARIO, MEDICO), `permisos` (Lista de accesos).
    *   Métodos: `gestionarSistema()`.

*   **`Medico` extends `UsuarioInterno`**
    *   Atributos: `especialidad` (Cardiología, Pediatría, etc.), `registroMedico`.
    *   Relaciones:
        *   Tiene muchas `Agendas`.
        *   Tiene muchas `Citas` asignadas.
    *   Métodos: `consultarAgenda()`, `atenderCita()`.

*   **`Paciente` extends `Usuario`**
    *   Atributos: `tipoSangre`, `alergias`, `enfermedades`, `contactoEmergenciaNombre`, `contactoEmergenciaTelefono`.
    *   Relaciones:
        *   Tiene historial de `Citas`.
    *   Métodos: `agendarCita()`, `verHistorial()`.

### Clases de Gestión

*   **`Cita`**
    *   Atributos: `codigo` (CIT-XXXX), `fecha`, `hora`, `estado` (AGENDADA, CONFIRMADA, ETC), `tipo`, `motivo`, `observaciones`.
    *   Relaciones:
        *   Pertenece a un `Paciente`.
        *   Es atendida por un `Medico`.
    *   Métodos: `confirmar()`, `cancelar()`, `finalizar()`.

*   **`Agenda`**
    *   Atributos: `codigo` (AGD-XXXX), `fechaInicio`, `fechaFin`, `motivo` (Creación, Reemplazo), `observaciones`.
    *   Relaciones:
        *   Asignada a un `Medico`.
    *   Métodos: `generarIntervalos()`.

*   **`Reporte`**
    *   Atributos: `idReporte`, `tipoReporte`, `descripcion`, `fechaGeneracion`, `formato` (PDF, EXCEL), `ubicacionArchivo`.
    *   Relaciones:
        *   Generado por un `UsuarioInterno`.
    *   Métodos: `generar()`, `exportar()`.

## 3. Diagrama Mermaid

```mermaid
classDiagram
    %% Herencia de Usuarios
    Usuario <|-- UsuarioInterno
    Usuario <|-- Paciente
    UsuarioInterno <|-- Medico

    %% Clases Abstractas y Enumeraciones
    class Usuario {
        <<Abstract>>
        -String nombres
        -String apellidos
        -String tipoDocumento
        -String numeroDocumento
        -String telefono
        -String email
        -String direccion
        -String ciudad
        -String pais
        -String usuarioLogin
        -String password
        +boolean iniciarSesion(String login, String pass)
        +void cerrarSesion()
    }

    class UsuarioInterno {
        -String codigoInterno
        -String rol
        -List~String~ permisos
        +void gestionarSistema()
    }

    class Medico {
        -String especialidad
        -String registroMedico
        +List~Agenda~ consultarAgenda()
        +void atenderCita(Cita cita)
    }

    class Paciente {
        -String tipoSangre
        -String alergias
        -String enfermedades
        -String contactoEmergenciaNombre
        -String contactoEmergenciaTelefono
        +void agendarCita(Cita cita)
        +List~Cita~ verHistorial()
    }

    %% Clases de Dominio
    class Cita {
        -String codigo
        -Date fecha
        -String hora
        -String estado
        -String tipo
        -String motivo
        -String observaciones
        +void confirmar()
        +void cancelar()
    }

    class Agenda {
        -String codigo
        -Date fechaInicio
        -Date fechaFin
        -String motivo
        -String observaciones
        +void generarIntervalos()
    }

    class Reporte {
        -String idReporte
        -String tipoReporte
        -String descripcion
        -Date fechaGeneracion
        -String formato
        +void generar()
        +void exportar()
    }

    %% Relaciones
    Medico "1" --> "*" Agenda : tiene
    Medico "1" --> "*" Cita : atiende
    Paciente "1" --> "*" Cita : solicita
    UsuarioInterno "1" ..> "*" Reporte : genera
    Cita "*" --> "1" Paciente : pertenece_a
    Cita "*" --> "1" Medico : asignada_a
```

## 4. Diagrama PlantUML

```plantuml
@startuml

abstract class Usuario {
    - String nombres
    - String apellidos
    - String tipoDocumento
    - String numeroDocumento
    - String telefono
    - String email
    - String direccion
    - String ciudad
    - String pais
    - String usuarioLogin
    - String password
    + boolean iniciarSesion(String login, String pass)
    + void cerrarSesion()
}

class UsuarioInterno {
    - String codigoInterno
    - String rol
    - List<String> permisos
    + void gestionarSistema()
}

class Medico {
    - String especialidad
    - String registroMedico
    + List<Agenda> consultarAgenda()
    + void atenderCita(Cita cita)
}

class Paciente {
    - String tipoSangre
    - String alergias
    - String enfermedades
    - String contactoEmergenciaNombre
    - String contactoEmergenciaTelefono
    + void agendarCita(Cita cita)
    + List<Cita> verHistorial()
}

class Cita {
    - String codigo
    - Date fecha
    - String hora
    - String estado
    - String tipo
    - String motivo
    - String observaciones
    + void confirmar()
    + void cancelar()
}

class Agenda {
    - String codigo
    - Date fechaInicio
    - Date fechaFin
    - String motivo
    - String observaciones
    + void generarIntervalos()
}

class Reporte {
    - String idReporte
    - String tipoReporte
    - String descripcion
    - Date fechaGeneracion
    - String formato
    + void generar()
    + void exportar()
}

' Relaciones de Herencia
Usuario <|-- UsuarioInterno
Usuario <|-- Paciente
UsuarioInterno <|-- Medico

' Relaciones de Asociación
Medico "1" --> "*" Agenda : tiene asignadas
Medico "1" --> "*" Cita : atiende
Paciente "1" --> "*" Cita : solicita
UsuarioInterno "1" ..> "*" Reporte : genera
Cita "*" --> "1" Paciente : pertenece_a
Cita "*" --> "1" Medico : asignada_a

@enduml
```
