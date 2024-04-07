<script setup>
import { ref, computed, inject } from 'vue'
import axios from 'axios'

import DrawerHead from './DrawerHead.vue'
import CartItemList from './CartItemList.vue'
import InfoBlock from './InfoBlock.vue'

const props = defineProps({
  totalPrice: Number
})

const customerName1 = ref('')
const customerName2 = ref('')
const customerNumber1 = ref('')
const customerNumber2 = ref('')
const customerAddress = ref('')

const { Cart, closeDrawer } = inject('Cart')

const isCreating = ref(false)
const orderId = ref(null)
const deliveryMethod = ref('pickup')
const deliveryCost = computed(() => (deliveryMethod.value === 'courier' ? 500 : 0))

const createOrder = async () => {
  try {
    isCreating.value = true
    const { data } = await axios.post(`https://209e4e169cdaed8c.mokky.dev/orders`, {
      items: Cart.value,
      totalPrice: props.totalPrice.value + deliveryCost.value,
      customer: {
        name1: customerName1.value,
        name2: customerName2.value,
        number1: customerNumber1.value,
        number2: customerNumber2.value,
        address: customerAddress.value
      },
      deliveryMethod: deliveryMethod.value
    })

    Cart.value = []
    orderId.value = data.id
  } catch (err) {
    console.log(err)
  } finally {
    isCreating.value = false
    closeDrawer() // Close drawer after order is created
  }
}

const cartIsEmpty = computed(() => Cart.value.length === 0)
const isFormFilled = computed(() => {
  if (deliveryMethod.value === 'courier') {
    return (
      customerName1.value !== '' &&
      customerName2.value !== '' &&
      customerNumber1.value !== '' &&
      customerNumber2.value !== '' &&
      customerAddress.value !== ''
    )
  } else {
    return (
      customerName1.value !== '' &&
      customerName2.value !== '' &&
      customerNumber1.value !== '' &&
      customerNumber2.value !== ''
    )
  }
})

const createOrderIfFormFilled = () => {
  if (isFormFilled.value) {
    createOrder()
  } else {
    console.log('Please fill in all fields before placing the order.')
  }
}

const canPlaceOrder = computed(() => !cartIsEmpty.value && isFormFilled.value && !isCreating.value)
</script>

<template>
  <div>
    <div class="fixed top-0 left-0 h-full w-full bg-black opacity-50 z-20"></div>

    <div
      class="fixed top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 bg-white shadow-lg rounded-lg p-8 z-30 max-h-full overflow-y-auto"
    >
      <DrawerHead />

      <div v-if="!totalPrice || orderId" class="flex h-full items-center justify-center"></div>

      <div v-else>
        <CartItemList />

        <div class="flex flex-col gap-4 mt-7">
          <!-- Итоговая стоимость и способ доставки -->
          <div class="flex gap-2">
            <span> Итого:</span>
            <div class="flex-1 border-b border-dashed"></div>
            <b>{{ totalPrice + deliveryCost }} ₽ </b>
          </div>
          <div class="flex gap-2">
            <span> Доставка:</span>
            <div class="flex-1 border-b border-dashed"></div>
            <b>{{ deliveryCost }} ₽ </b>
          </div>

          <!-- Поля для оформления заказа -->
          <div v-if="!isCreatingOrder">
            <div class="flex gap-2">
              <label for="delivery-pickup">Самовывоз</label>
              <input
                type="radio"
                id="delivery-pickup"
                name="delivery-method"
                v-model="deliveryMethod"
                value="pickup"
              />
            </div>
            <div class="flex gap-2">
              <label for="delivery-courier">Курьер</label>
              <input
                type="radio"
                id="delivery-courier"
                name="delivery-method"
                v-model="deliveryMethod"
                value="courier"
              />
            </div>
            <div v-if="deliveryMethod !== 'courier'">
              <input
                type="text"
                v-model="customerName1"
                placeholder="Ваше имя"
                class="input-field"
              />
              <input
                type="text"
                v-model="customerNumber1"
                placeholder="Ваш телефон (мы свяжемся для подтверждения заказа)"
                class="input-field"
              />
              <input
                type="text"
                v-model="customerName2"
                placeholder="Имя получателя"
                class="input-field"
              />
              <input
                type="text"
                v-model="customerNumber2"
                placeholder="Телефон получателя"
                class="input-field"
              />
            </div>
            <div v-if="deliveryMethod === 'courier'">
              <input
                type="text"
                v-model="customerName1"
                placeholder="Ваше имя"
                class="input-field"
              />
              <input
                type="text"
                v-model="customerNumber1"
                placeholder="Ваш телефон (мы свяжемся для подтверждения заказа)"
                class="input-field"
              />
              <input
                type="text"
                v-model="customerName2"
                placeholder="Имя получателя"
                class="input-field"
              />
              <input
                type="text"
                v-model="customerNumber2"
                placeholder="Телефон получателя"
                class="input-field"
              />
              <input
                type="text"
                v-model="customerAddress"
                placeholder="Адрес доставки"
                class="input-field"
              />
            </div>
            <!-- Кнопка оформления заказа -->
            <button
              :disabled="!canPlaceOrder"
              @click="createOrderIfFormFilled()"
              class="mt-4 transition bg-lime-500 w-full rounded-xl py-3 text-white disabled:bg-slate-300 hover:bg-lime-600 active:bg-lime-700 cursor-pointer"
            >
              Оформить заказ
            </button>
          </div>
        </div>
      </div>
    </div>
    <div v-if="orderId">
      <h2>Заказ успешно создан!</h2>
      <p>Для оплаты перейдите на страницу платежей.</p>
      <a :href="paymentUrl" target="_blank" class="action-button">Оплатить заказ</a>
    </div>
    <div v-if="orderId">
      <!-- Блок с информацией о заказе -->
      <div v-if="totalPrice">
        <InfoBlock title="Итоговая стоимость" description="Цена без доставки: {{ totalPrice }} ₽" />
      </div>
    </div>
    <Header :totalPrice="totalPrice" />
  </div>
</template>

<style scoped>
.input-field {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.action-button {
  width: 100%;
  padding: 12px;
  background-color: #a52a2a; /* Розовый цвет кнопки */
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.action-button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}
</style>
