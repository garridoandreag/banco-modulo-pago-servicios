# Especificación del Proyecto: Modulo bancario de Pago de Servicios de Luz y Agua

## 1. Descripción general
Este proyecto consiste en desarrollar un módulo de sistema web bancario que permita a los cuentahabientes realizar el pago de servicios públicos de electricidad y agua potable desde su banca en línea.

El sistema permitirá:

- Realizar pagos de servicios.
- Consultar historial de pagos.
- Generar comprobantes de pago.
- Permitir a las empresas de servicios consultar pagos recibidos.

## 2. Objetivos
- Facilitar el pago de servicios a los cuentahabientes.
- Mostrar historial de pagos realizados por servicio.
- Generar comprobante de pago realizado. 
- Permitir a las empresas de servicios consultar pagos realizados a sus cuentas.

## 3. Alcance

### Incluye
- Autenticacion de usuarios.
- Historial de pago de servicios.
- Formulario por cada pago de servicios.
- Menu de seleccion de servicios a pagar con las siguientes opciones: Electricidad y Agua Potable.
- Menu de seleccion de empresas por tipo de servicio.

- Pagina Home despues de inicio de sesión.
- Menu lateral con navegacion.
- Gestión de pagos por empresa de servicio.

### No incluye

- Integración real con sistemas externos de facturación.
- Pagos con tarjetas de crédito.
- Pagos internacionales.
- Aplicación móvil.

## 4. Roles de usuario
- Cuentahabiente
- Administrador
- Empresa de Servicios

### 4.1 Administrador
Puede: 
- Crear, editar y desactivar usuarios
- Asignar roles
- Ver todos los registros

### 4.2 Cuentahabiente
Puede:
- Iniciar sesión
- Ver cuentas y saldos.
- Consultar historial de pagos por cuenta
- Seleccionar la cuenta bancaria a debitar
- Realizar pagos a empresa de servicios ingresando numero de contador
- Consultar comprobantes de pago


### 4.3 Empresa de Servicios
Puede:
- Iniciar sesión
- Consultar pagos realizados a su empresa
- Ver pagos asociados a un número de contador
- Establecer la tarifa mensual del servicio

Restricciones: 
- La empresa de servicio no puede ver el nombre del cuentahabiente, únicamente: numero de contador, monto pagado y fecha del pago.

## 5. Módulos del sistema

### 5.1 Autenticación y acceso
- Inicio de sesion
- Cierre de sesion
- Recuperacion de contraseña
- Control de acceso por roles

### 5.2 Gestión de usuarios
- Crear usuario
- Editar usuario
- Cambiar estado de usuario de activo/inactivo
- Asignar rol

### 5.3 Pago de servicios
- Seleccion de tipo de servicio
- Seleccion de empresa
- Formulario de Pago
- Generacion de comprobante
- Registro en historial

- Servicios disponibles:
    - Electricidad: 
        - Energuate de Occidente
        - Energuate de Oriente
        - Empresa Electrica de Guatemala

    - Agua Potable:
        - Empagua
        - Agua de Oriente
        - Agua de Occidente


### 5.4 Gestion de pagos
- Consulta de pagos por número de contador
- Historial de pagos
- Visualización de pagos atrasados

## 6. Requerimientos funcionales
### RF-001 Inicio de sesion
El sistema debe permitir el inicio de sesión mediante nombre de cuenta y contraseña.

### RF-002 Acceso y Roles
El sistema debe restringir funcionalidades según el rol del usuario autenticado.

### RF-003 Gestion de Usuarios
El sistema debe permitir crear, editar, consultar y desactivar usuarios.

### RF-004 Registro de pago
El sistema debe registrar cada pago con fecha, hora, usuario, estado, numero de contador, empresa, cuenta debitada y monto pagado.

### RF-005 Formulario de Pago de Servicios
El formulario de pagos debe incluir empresa de servicio (preseleccionada), cuenta bancaria a debitar, numero de contador y monto a pagar. 

Reglas:
- Nombre de la empresa, debe ser preseleccionado de un menu anterior.
- Cuenta a debitar, dropdown con todas las cuentas del cuentahabiente.
- Numero de contador, entrada de texto.
- Monto a pagar debe generarse automáticamente basado en: tarifa mensual de la empresa, historial de pagos del contador y meses pendientes de pago.

### RF-006 Gestion de Pagos por empresa
El modulo de gestion de pagos para el rol de empresa de servicio, debe permitir ver el historial de pagos por numero de contador, monto pagado y fecha de pago realizado.

El sistema debe resaltar los numeros de contador con atraso en el pago.

## 7. Requerimientos no funcionales

### RNF-001 Seguridad
- Contraseñas cifradas
- Validación de permisos por rol
- Protección contra inyección SQL y XSS
- Manejo seguro de sesiones

### RNF-002 Usabilidad
- Interfaz responsive
- Formularios claros
- Mensajes de error comprensibles
- Navegación simple mediante menú lateral

## 8. Entidades principales

### rol
- id serial PRIMARY KEY
- nombre VARCHAR(50) UNIQUE, NOT NULL
- estado boolean DEFAULT TRUE
- fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP
- fecha_actualizacion TIMESTAMP

Relacion: 
- 1 a N (usuario)

Seeds:
- administrador
- cuentahabiente
- empresa_servicio

### usuario
- id serial PRIMARY KEY
- nombre_completo VARCHAR(150) NOT NULL
- nombre_usuario  VARCHAR(50) UNIQUE, NOT NULL
- correo VARCHAR(150) UNIQUE, NOT NULL
- password_hash TEXT NOT NULL
- rol_id INTEGER NOT NULL
- estado BOOLEAN DEFAULT TRUE
- fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP
- fecha_actualizacion TIMESTAMP

Relaciones: 
- FOREIGN KEY rol_id: rol(id)
- 1 a N (cuenta_bancaria)
- 1 a N (pago)

### cuenta_bancaria
- id SERIAL PRIMARY KEY
- usuario_id INTEGER NOT NULL
- numero_cuenta VARCHAR(20) UNIQUE, NOT NULL
- saldo NUMERIC(12,2) NOT NULL
- tipo_cuenta VARCHAR(50) NOT NULL
- estado BOOLEAN DEFAULT TRUE
- fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP

Relacion: 
- FOREIGN KEY usuario_id: usuario(id)
- 1 a N (pago)

### tipo_servicio
- id SERIAL PRIMARY KEY
- nombre VARCHAR(50) UNIQUE, NOT NULL
- estado BOOLEAN DEFAULT TRUE
- fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP
- fecha_actualizacion TIMESTAMP

Relacion: 
- 1 a N (empresa_servicio)

Seeds:
- Electricidad
- Agua

### empresa_servicio
- id SERIAL PRIMARY KEY
- nombre VARCHAR(150) NOT NULL
- tipo_servicio_id INTEGER NOT NULL
- tarifa_mensual NUMERIC(10,2) NOT NULL
- estado BOOLEAN DEFAULT TRUE
- fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP
- fecha_actualizacion TIMESTAMP

Relacion: 
- FOREIGN KEY tipo_servicio_id: tipo_servicio(id)
- 1 a N (contador_servicio)
- 1 a N (tarifa_servicio)
- 1 a N (pago)

### contador_servicio
- id SERIAL PRIMARY KEY
- numero_contador VARCHAR(50) UNIQUE, NOT NULL
- empresa_id INTEGER NOT NULL
- alias VARCHAR(100)
- estado BOOLEAN DEFAULT TRUE
- fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP

Relaciones: 
- FOREIGN KEY empresa_id: empresa_servicio(id)
- 1 a N (pago)

### pago
- id SERIAL PRIMARY KEY
- usuario_id INTEGER NOT NULL
- empresa_id INTEGER NOT NULL
- numero_contador VARCHAR(50) NOT NULL
- cuenta_bancaria_id INTEGER NOT NULL
- monto_pagado NUMERIC(10,2) NOT NULL CHECK (monto_pagado > 0)
- mes_correspondiente INTEGER NOT NULL CHECK (mes_correspondiente BETWEEN 1 AND 12)
- anio_correspondiente INTEGER NOT NULL CHECK (anio_correspondiente >= 2000)
- estado VARCHAR(20) NOT NULL
- fecha_pago TIMESTAMP
- comprobante_id INTEGER
- fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP

Relaciones: 
- FOREIGN KEY usuario_id: usuario(id)
- FOREIGN KEY empresa_id: empresa_servicio(id)
- FOREIGN KEY cuenta_bancaria_id: cuenta_bancaria(id)
- 1 a 1 (comprobante)

Restricción adicional:
UNIQUE (numero_contador, empresa_id, mes_correspondiente, anio_correspondiente)

Estados posibles:
- pendiente
- aprobado
- rechazado

### historial_pagos
- id SERIAL PRIMARY KEY
- pago_id INTEGER NOT NULL
- empresa_id INTEGER
- usuario_id INTEGER
- contador_id INTEGER
- tipo_servicio_id INTEGER
- fecha_evento TIMESTAMP DEFAULT CURRENT_TIMESTAMP

Relaciones: 
- FOREIGN KEY pago_id: pago(id)
- FOREIGN KEY empresa_id: empresa_servicio(id)
- FOREIGN KEY usuario_id:  usuario(id)
- FOREIGN KEY contador_id: contador_servicio(id)
- FOREIGN KEY tipo_servicio_id: tipo_servicio(id)

### tarifas_servicio
- id SERIAL PRIMARY KEY
- empresa_id INTEGER NOT NULL
- monto_mensual NUMERIC(10,2) NOT NULL
- fecha_inicio_vigencia DATE NOT NULL
- fecha_fin_vigencia DATE
- estado BOOLEAN DEFAULT TRUE

Relaciones:
- FOREIGN KEY empresa_id: empresa_servicio(id)

### comprobante
- id SERIAL PRIMARY KEY
- pago_id INTEGER UNIQUE
- numero_comprobante VARCHAR(50) UNIQUE, NOT NULL
- fecha_emision TIMESTAMP DEFAULT CURRENT_TIMESTAMP
- monto NUMERIC(10,2) NOT NULL
- empresa VARCHAR(150) NOT NULL
- numero_contador VARCHAR(50) NOT NULL

Relación:
- FOREIGN KEY pago_id: pago(id)

## 9. Reglas de negocio

### RN-001 Pago de servicio
Un pago debe debitarse de la cuenta seleccionada del cuentahabiente.

### RN-002 Validación de saldo
El sistema debe verificar que la cuenta tenga saldo suficiente antes de procesar el pago.

### RN-003 Tarifa del servicio
Cada empresa define una tarifa mensual para todos sus numeros de contador (clientes).

### RN-004 Cálculo de deuda
El monto a pagar debe calcularse como:
monto = tarifa_mensual × meses_pendientes

### RN-005 Pago atrasado
Si el contador tiene meses sin pagar, el sistema debe mostrar el atraso.

### RN-006 Generación de comprobante
Cada pago exitoso debe generar un comprobante único.

### RN-007 Estados del pago
Un pago puede tener los estados: pendiente, aprobado o rechazado.

### RN-008 Pago realizado
Una vez que un pago esté aprobado, no puede editarse o eliminarse.

## 10. Flujos principales
1. El usuario inicia sesión.
2. Selecciona la opción Pagos.
3. Selecciona el tipo de servicio.
4. Selecciona la empresa.
5. El sistema muestra formulario con el nombre de la empresa.
6. El usuario ingresa número de contador.
6. El sistema calcula el monto pendiente y lo muestra.
7. El usuario selecciona la cuenta a debitar.
8. El usuario confirma el pago.
9. El sistema valida saldo.
10. El sistema registra el pago.
11. Se genera comprobante.
12. Se actualiza el historial el pagos. 

## 11. Validaciones
- Todos los campos son obligatorios y deben validarse.
- El número de contador no puede estar vacío.
- El monto debe ser mayor que cero.
- La cuenta debe tener saldo suficiente.
- No se deben permitir pagos duplicados del mismo mes.
- No se debe permitir más de un pago aprobado para el mismo número de contador, empresa, mes y año.

## 12. Interfaz
El sistema debe incluir:
- Login
- Página Home
- Menú lateral
- Pantalla de pagos con Menu de Tipos de Servicios y menu de empresas del servicio seleccionado.
- Formulario de pago
- Historial de pagos
- Perfil de usuario
- Panel de empresa de servicios
- Panel de administración

## 13. Entregables esperados
- Proyecto completo funcional
- Base de datos con migraciones
- Documentación README
- Datos seeds de ejemplo

## 14. Restricciones importantes
- Priorizar código mantenible sobre código excesivamente complejo

## 15. Stack tecnologico esperado
