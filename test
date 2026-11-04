/**
 * AlisonShop - pruebas unitarias de la logica de busqueda de n8n.
 * Ejecutar desde la raiz del repositorio: node --test tests/busqueda.test.cjs
 * No utiliza credenciales, WhatsApp, Google Sheets real ni OpenAI.
 * Los casos ejecutan el JavaScript DEL JSON PUBLICADO, no una copia de su logica.
 */
'use strict';

const { test } = require('node:test');
const assert = require('node:assert/strict');
const fs = require('node:fs');
const path = require('node:path');
const vm = require('node:vm');

const rutaWorkflow = path.join(__dirname, '..', 'alisonshop-workflow-publico.json');
const workflow = JSON.parse(fs.readFileSync(rutaWorkflow, 'utf8'));
const nodoBusqueda = workflow.nodes.find((nodo) => nodo.name === 'Code in JavaScript');
assert.ok(nodoBusqueda?.parameters?.jsCode, 'Falta el nodo Code in JavaScript');

const codigoBusqueda = new vm.Script(`(function () {\n${nodoBusqueda.parameters.jsCode}\n})()`);

/** Simula las entradas que n8n proporciona al nodo Code, sin conectar servicios externos. */
function buscar({ producto = 'A27', presupuesto = 0, cliente = 'Cliente de prueba', wa_from = ' +591 700-00000 ' } = {}, inventario = []) {
  const contexto = {
    $input: { all: () => inventario.map((fila) => ({ json: fila })) },
    $: (nombre) => {
      assert.equal(nombre, 'SolicitudCliente');
      return { first: () => ({ json: { producto, presupuesto, cliente, wa_from } }) };
    },
  };
  // La serializacion normaliza objetos que salen del contexto VM.
  return JSON.parse(JSON.stringify(codigoBusqueda.runInNewContext(contexto, { timeout: 1500 })[0].json));
}

const inventario = [
  { 'IDENTIFICACIÓN': 1, Marca: 'Samsung', Modelo: 'A27', Precio: 1850, Stock: 5 },
  { 'IDENTIFICACIÓN': 2, Marca: 'Samsung', Modelo: 'A15', Precio: 1200, Stock: 4 },
  { 'IDENTIFICACIÓN': 3, Marca: 'Xiaomi', Modelo: 'Redmi Note 13', Precio: 1650, Stock: 3 },
  { 'IDENTIFICACIÓN': 4, Marca: 'Motorola', Modelo: 'G54', Precio: 1400, Stock: 0 },
];

test('01 - Encuentra un celular por modelo', () => {
  const res = buscar({ producto: 'A27' }, inventario);
  assert.equal(res.estado, 'encontrado');
  assert.equal(res.productos[0].modelo, 'A27');
});

test('02 - La busqueda no distingue mayusculas de minusculas', () => {
  const res = buscar({ producto: 'SAMSUNG' }, inventario);
  assert.equal(res.cantidad_encontrada, 2);
});

test('03 - Excluye productos que superan el presupuesto', () => {
  const res = buscar({ producto: 'Samsung', presupuesto: 1500 }, inventario);
  assert.equal(res.cantidad_encontrada, 1);
  assert.equal(res.productos[0].modelo, 'A15');
  assert.ok(res.productos.every((p) => p.precio <= 1500));
});

test('04 - Con presupuesto 0, no aplica filtro maximo', () => {
  const res = buscar({ producto: 'Samsung', presupuesto: 0 }, inventario);
  assert.equal(res.cantidad_encontrada, 2);
});

test('05 - Nunca recomienda productos sin stock', () => {
  const res = buscar({ producto: 'Motorola' }, inventario);
  assert.equal(res.productos.length, 0);
  assert.ok(res.alternativas.every((p) => p.existencias > 0));
});

test('06 - Nunca recomienda productos con precio cero', () => {
  const res = buscar({ producto: 'Xiaomi' }, [
    { Marca: 'Xiaomi', Modelo: 'Gratis', Precio: 0, Stock: 2 },
    ...inventario,
  ]);
  assert.ok(res.productos.every((p) => p.precio > 0));
});

test('07 - Si no coincide el producto, clasifica solo alternativas', () => {
  const res = buscar({ producto: 'Nokia' }, inventario);
  assert.equal(res.estado, 'solo_alternativas');
  assert.equal(res.cantidad_encontrada, 0);
  assert.ok(res.cantidad_alternativas > 0);
});

test('08 - Sin productos vendibles, clasifica sin resultados', () => {
  const res = buscar({ producto: 'A27', presupuesto: 200 }, inventario);
  assert.equal(res.estado, 'sin_resultados');
  assert.equal(res.cantidad_encontrada, 0);
  assert.equal(res.cantidad_alternativas, 0);
});

test('09 - Ordena resultados por precio descendente (comportamiento actual)', () => {
  const res = buscar({ producto: 'Samsung' }, inventario);
  assert.deepEqual(res.productos.map((p) => p.precio), [1850, 1200]);
});

test('10 - Limita las coincidencias a tres', () => {
  const masProductos = Array.from({ length: 5 }, (_, i) => ({
    Marca: 'Samsung', Modelo: `A${i + 10}`, Precio: 1000 + i * 100, Stock: 1,
  }));
  const res = buscar({ producto: 'Samsung' }, masProductos);
  assert.equal(res.cantidad_encontrada, 3);
});

test('11 - Limita las alternativas a tres', () => {
  const masProductos = Array.from({ length: 5 }, (_, i) => ({
    Marca: 'Marca', Modelo: `X${i}`, Precio: 1000 + i * 100, Stock: 1,
  }));
  const res = buscar({ producto: 'No existe' }, masProductos);
  assert.equal(res.cantidad_alternativas, 3);
});

test('12 - Normaliza nombre, producto y numero de contacto', () => {
  const res = buscar({ producto: ' A27 ', cliente: ' Ana ', wa_from: '+591 700-00000' }, inventario);
  assert.equal(res.producto_buscado, 'a27');
  assert.equal(res.cliente, 'Ana');
  assert.equal(res.wa_from, '59170000000');
});

test('13 - Acepta nombre de columna Existencias como alternativa a Stock', () => {
  const res = buscar({ producto: 'Poco' }, [
    { ID: 'P1', Marca: 'Marca', Modelo: 'Poco', Precio: 1000, Existencias: 4 },
  ]);
  assert.equal(res.productos[0].existencias, 4);
  assert.equal(res.productos[0].identificacion, 'P1');
});

test('14 - No consulta servicios externos y admite inventario vacio', () => {
  const res = buscar({ producto: 'A27' }, []);
  assert.equal(res.estado, 'sin_resultados');
});

// Caso conocido que requiere mejorar el algoritmo del nodo Code. Se documenta
// como pendiente para no anunciar como aprobada una funcionalidad que hoy falla.
test.todo('Pendiente: buscar marca y modelo juntos, p. ej. "Samsung A27"');
