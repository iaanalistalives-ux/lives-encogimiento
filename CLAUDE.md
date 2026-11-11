# CLAUDE.md — Sistema de Encogimiento

Las reglas generales están en el repo `lives-instrucciones` (CLAUDE.md). Aquí solo lo propio de esta herramienta.

## Qué hace
Registra el recorrido de cada prueba de encogimiento de Desarrollo (Planta Barranco): llega la tela de producción, la modelista libera, Corte corta, Costura cose y queda entregado. Pedido de José Palacios, aprobado por la señora Yvonne. Reemplaza al Google Sheet + Apps Script.

## Pantalla
Un solo archivo: `index.html` (GitHub Pages). Un solo enlace para todos: el menú lo decide el rol de la persona, que responde la base en la acción `inicio`. `?area=corte|costura|modelista` solo le sirve a quien tiene el sistema completo, para mirar la pantalla de un área.

| Rol | Qué ve y qué guarda |
|---|---|
| COMPLETO | Todo: Telas de producción, Ingresar encogimientos, Programa, Búsqueda (con Excel), Gráficos, Generar prueba |
| MODELISTA | Generar prueba de encogimiento (libera o manda a residual); Seguimiento y Búsqueda solo para ver |
| CORTE | Registrar corte; Programa solo para ver |
| COSTURA | Registrar costura; Programa solo para ver |
| VER (quien no está en la lista) | Programa y Búsqueda solo para ver |
| SUPERVISION (rcamogliano) | Ve todo, la base no le guarda nada |

Llegada de tela (desde el 11/11/2026): «Registrar llegada de tela» abre una ventana encima de Telas de producción (dos columnas en PC, pantalla completa en celular; ✕ Cerrar, Escape o toque en el fondo; con datos escritos pregunta «¿Salir sin guardar?»). «Lo último registrado» (20 telas más nuevas) va dentro de la ventana, plegado, y se abre solo tras grabar. Los campos de elegir usan una lista corta propia (hasta 12 opciones, se filtra al escribir), no el desplegable del navegador.

Fuera del portal (link sin `?usuario=`) sale el botón «Identificarme»: se escribe el usuario del portal.

## Base (Supabase, prefijo `encog_`)
- Puerta única: `encog_api(p_accion, p)`. Las funciones `encog_a_*` están cerradas a la web. Las tablas no se leen directo.
- Tablas: `encog_encogimientos`, `encog_telas`, `encog_movimientos`, `encog_prioridad`, `encog_costureras`, `encog_costura_personas`, `encog_cortadores`, `encog_modelistas`, `encog_estados`, `encog_parametros`, `encog_tokens`, `encog_accesos`.
- Accesos (desde el 11/11/2026): `encog_accesos` (usuario de Sistemas → rol), `encog_rol`, `encog_puede`, `encog_puede_accion`. Cada guardado se valida por rol. Corte firma con su código de cortador (campo `usuario`) y manda además `quien` (usuario del portal), que es con lo que se revisa el permiso.
- Selección desde la base (desde el 11/11/2026): en Llegada de tela, Editar tela y Editar datos, cliente, grupo, código de tela, estilo, partida y color se ELIGEN de la lista (acción `getCatalogo`: `ops` + `partidas` + lo ya registrado); no se escriben. `encog_api` lo valida al guardar (`encog_valida_base`, `encog_en_base`). Al editar, lo que no se toca no se revisa. «Ingresar encogimientos» (pegar del Excel) no se valida.
- `encog_parametros.registro_libre`: `no`. Si se pone `si`, todos vuelven a poder guardar todo.
- Lee además `ops` y `partidas` (listas para elegir), `cat_clientes`, `nombres_sistemas` y `cod_cliente_alias`.

## Quién la usa
Armando Berrocal y José Palacios (completo), las modelistas, Robert Gutierrez (corte) y Lidia Candela (costura). Para dar o quitar un acceso (desde el 11/11/2026): Christian lo hace en el panel "Quién registra" (`lives-portal/admin.html`), con las cuatro opciones «Encogimiento · sistema completo / Modelista / Corte / Costura». El panel escribe en `encog_accesos`: un solo rol por persona (al dar uno se reemplaza el anterior); al apagar queda `activo = false` y la persona solo ve.

## Para Sistemas
`encog_accesos` + `encog_rol` + `encog_puede_accion` son la pieza que reemplazan con su seguridad. En SQL Server se exige usuario para abrir y leer.
