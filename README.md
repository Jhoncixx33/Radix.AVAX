#🌿 Radix.AVAX
Infraestructura descentralizada sobre Avalanche para trazabilidad agrícola sustentable y comercio justo en Latinoamérica

BY:Shadow Garden - 2026

Radix Trace es la columna vertebral Web3 del proyecto Radix.AVAX. Combina identidad digital de lotes (NFT), pagos instantáneos nativos x402 y privacidad financiera mediante eERC20 para empoderar a los pequeños productores de café y cacao, eliminando intermediarios y protegiendo su rentabilidad.

🚨 El Problema
Desafío	Impacto real en el productor
Trazabilidad no auditable	Pérdida de certificaciones internacionales (orgánico, comercio justo) que elevan el precio del grano.
Exposición financiera	Los balances públicos en blockchains tradicionales exponen al productor a extorsión, secuestro o competencia desleal.
Liquidaciones lentas y costosas	Semanas de espera por transferencias SWIFT, múltiples comisiones y riesgo cambiario.
La digitalización tradicional no resuelve la raíz del problema porque centraliza el control de los datos y el flujo del dinero. Radix Trace utiliza la pila tecnológica de Avalanche para devolver la soberanía al agricultor.

🧱 Arquitectura del Sistema – Tres pilares core
1. 🌱 Pasaporte Digital Dinámico (NFT)
Contrato PasaporteVerde – estándar ERC-721URIStorage
Cada lote de café o cacao se registra como un NFT no fungible. Sus metadatos (ubicación, variedad, certificaciones) residen en IPFS y se enriquecen con una línea de tiempo inmutable de puntos de control:
struct PuntoDeControl {
    string descripcion;
    uint256 timestamp;
    address operador;
}
Sólo entidades con el rol CHECKPOINT_ROLE pueden añadir hitos (cosecha, beneficio, embarque).

La función registrarLote(productor, tokenURI) acuña el NFT y deja trazabilidad desde el instante cero.
2. 🔐 Privacidad Financiera Absoluta (eERC20)
Interfaz IeERC20 y mock MockeERC20
Los pagos no utilizan un token ERC20 tradicional, cuyos balances serían visibles. En su lugar, el sistema está preparado para interactuar con un token Encrypted ERC-20 (eERC20) que aplica encriptación homomórfica (FHE).

transferConfidencial(destinatario, monto): El monto y el nuevo balance se mantienen cifrados en la cadena. Sólo las partes implicadas pueden descifrarlos.

El contrato MockeERC20 simula esta conducta durante las pruebas; en producción se reemplazará por una implementación FHE real (por ejemplo, utilizando la biblioteca de cifrado de Avalanche o un zk-SNARK).

Ni siquiera los operadores de la red pueden conocer el capital recibido por un agricultor.

3. ⚡ Automatización de Liquidaciones (Flujos Nativos x402)
Contrato MockX402Handler
Avalanche introduce x402 Payments, pagos nativos por HTTP sin permisos que ejecutan directamente un hook on-chain.

El manejador (con rol X402_ROLE en PasaporteVerde) invoca procesarPagoX402(tokenId, monto) cuando recibe una firma de pago HTTP válida.

El pago se registra inmutablemente en el NFT y los fondos se transfieren al productor mediante la función confidencial del eERC20.

El comprador envía una petición HTTP, el pago se verifica y el dinero llega al productor en segundos, sin bancos ni cartas de crédito.

🔄 Flujo Lógico del Ciclo de Vida
registrarLote
Un administrador registra un nuevo lote asignándolo a un productor y subiendo sus metadatos iniciales a IPFS. Se acuña el NFT.

agregarPuntoDeControl
A lo largo de la cadena de suministro, operadores autorizados añaden hitos ("Cosecha", "Beneficio", "Embarque") al array del token, construyendo una línea de tiempo inmutable.

Depósito del comprador
El comprador aprueba al contrato PasaporteVerde para gastar sus tokens eERC20 y llama a depositarTokens(monto). El contrato queda como custodia temporal.

Pago HTTP nativo (x402)
Un servicio externo (o el MockX402Handler durante pruebas) recibe una petición de pago con firma. Tras verificar la validez, invoca:
pasaporte.procesarPagoX402(tokenId, monto);
El contrato ejecuta transferConfidencial(productor, monto), marca el lote como pagado y emite PagoComercioJustoProcesado.

Verificación pública
Cualquier comprador o certificador puede consultar los puntos de control y el estado del pago mediante obtenerPuntosDeControl(tokenId) y lotePagado(tokenId), sin exponer los montos.
🧪 Notas de Producción y Escalabilidad
Componente actual (hackathon)	Mejora planificada en producción
MockeERC20 – simula transferencias confidenciales	Token eERC20 real con FHE (cifrado completamente homomórfico). La función transferConfidencial ocultará montos y balances incluso para observadores de la cadena.
MockX402Handler – rol manual	Endpoint oficial x402 de Avalanche. Verificará firmas criptográficas de peticiones HTTP y ejecutará procesarPagoX402 automáticamente, sin intervención humana.
Contador manual _tokenIdCounter	Herencia de ERC721Enumerable (OpenZeppelin) para enumeración nativa, consultas eficientes y un totalSupply preciso sin sobrecarga de gas adicional.
Gobernanza con DEFAULT_ADMIN_ROLE	Estructura descentralizada con roles asignados a cooperativas y verificadores independientes mediante AccessControl.
Una sola clase de token	Soporte para múltiples tokens eERC20 (stablecoins locales, puntos de recompensa) permitiendo pagos flexibles y confidenciales.
Adicionalmente, se integrarán oráculos descentralizados (Chainlink) para automatizar la certificación de puntos de control en campo (datos IoT de humedad, temperatura, GPS), reforzando la trazabilidad sin confianza.

©️ Propiedad Intelectual
Copyright © 2026 – Todos los derechos reservados.

Este repositorio se comparte públicamente exclusivamente para fines de evaluación por el jurado del hackathon. Queda expresamente prohibida cualquier acción de copia, bifurcación comercial, modificación, reproducción o uso no autorizado del código, la documentación o las ideas contenidas en este proyecto, sin el consentimiento previo y por escrito de los autores.

Para solicitar permisos de uso o colaboración, contactar a los titulares del proyecto a través de los canales oficiales de Radix.AVAX.
