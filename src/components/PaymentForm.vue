<script setup>
import { ref } from 'vue'
import axios from 'axios'

const customerId = ref('657b928db4c08f6f66847b09')
const currency = ref('MXN')
const notifyUrl = ref('https://example.com/webhook')
const itemName = ref('Brazalete de plata')
const itemQuantity = ref(1)
const itemPrice = ref(250.0)

const handleSubmit = async () => {
  console.log('Iniciando proceso de creación de orden...')

  try {
    // --- 1: CREAR EL TOKEN DE AUTORIZACIÓN ---
    console.log('Paso 1: Solicitando token de autorización...')

    const tokenResponse = await axios.post(
      'https://sandbox.ecartpay.com/api/authorizations/token',
      {},
      {
        headers: {
          accept: 'application/json',
          authorization:
            'Basic cHViNjhhNGMzMTUwYjI4YTk1ODQzMDVhMmE4OnByaXY2OGE0YzMxNTBiMjhhOTU4NDMwNWEyYTk=',
        },
      },
    )

    const authToken = tokenResponse.data.token
    console.log('✅ Token de autorización obtenido!')

    // --- 2: CREAR LA ORDEN USANDO EL TOKEN ---
    console.log('Paso 2: Creando la orden...')

    const orderPayload = {
      customer_id: customerId.value,
      currency: currency.value,
      items: [
        {
          name: itemName.value,
          quantity: itemQuantity.value,
          price: itemPrice.value,
        },
      ],
      notify_url: notifyUrl.value,
    }

    const orderResponse = await axios.post(
      'https://sandbox.ecartpay.com/api/orders',
      orderPayload,
      {
        headers: {
          accept: 'application/json',
          'content-type': 'application/json',
          Authorization: authToken,
        },
      },
    )

    const payLink = orderResponse.data.pay_link
    console.log('¡Orden creada con éxito! Redireccionando a:', payLink)

    // --- 3: REDIRECCIONAR AL PAY_LINK ---
    window.location.href = payLink
  } catch (error) {
    // Manejo de errores para cualquiera de las dos llamadas
    console.error('Hubo un error en el proceso:', error.response?.data || error.message)
    alert('Hubo un error. Revisa la consola para ver los detalles.')
  }
}
</script>

<template>
  <div class="order-form">
    <h2>Crear Nueva Orden</h2>

    <form @submit.prevent="handleSubmit">
      <div class="form-group">
        <label for="customer-id">ID del Cliente</label>
        <input id="customer-id" type="text" v-model="customerId" />
      </div>

      <div class="form-group">
        <label for="currency">Moneda</label>
        <input id="currency" type="text" v-model="currency" />
      </div>

      <div class="form-group">
        <label for="notify-url">URL de Notificación (Webhook)</label>
        <input id="notify-url" type="url" v-model="notifyUrl" />
      </div>

      <hr class="divider" />
      <h3>Ítems de la Orden</h3>

      <div class="form-group">
        <label for="item-name">Nombre del Producto</label>
        <input id="item-name" type="text" v-model="itemName" />
      </div>

      <div class="form-row">
        <div class="form-group">
          <label for="item-quantity">Cantidad</label>
          <input id="item-quantity" type="number" v-model="itemQuantity" />
        </div>
        <div class="form-group">
          <label for="item-price">Precio</label>
          <input id="item-price" type="number" step="0.01" v-model="itemPrice" />
        </div>
      </div>

      <button type="submit" class="submit-btn">Crear Orden y Pagar</button>
    </form>
  </div>
</template>

<style scoped>
.order-form {
  max-width: 400px;
  margin: 2rem auto;
  padding: 2rem;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.05);
  font-family: sans-serif;
  background-color: palegreen;
}
h2,
h3 {
  text-align: center;
  color: #333;
}
h3 {
  margin-top: 1.5rem;
  font-size: 1.1rem;
  color: #555;
}
.divider {
  border: none;
  border-top: 1px solid #eee;
  margin: 1.5rem 0;
}
.form-group {
  margin-bottom: 1rem;
}
label {
  display: block;
  margin-bottom: 0.5rem;
  font-size: 0.9rem;
  color: #555;
}
input {
  width: 100%;
  padding: 0.75rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 1rem;
  box-sizing: border-box;
}
.form-row {
  display: flex;
  gap: 1rem;
}
.form-row .form-group {
  flex: 1;
}
.submit-btn {
  width: 100%;
  padding: 0.85rem;
  border: none;
  border-radius: 4px;
  background-color: #28a745;
  color: white;
  font-size: 1.1rem;
  font-weight: bold;
  cursor: pointer;
  margin-top: 1rem;
}
.submit-btn:hover {
  background-color: #6bed87;
}
</style>
