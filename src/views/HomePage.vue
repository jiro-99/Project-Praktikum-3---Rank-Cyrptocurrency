<template>
  <ion-page>
    <ion-header>
      <ion-toolbar color="primary">
        <ion-title>Data Cryptocurrency</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content class="ion-padding">
      <div class="container">
        <ion-button @click="ambilDataCrypto" class="refresh-button">
          Refresh
        </ion-button>

        <p v-if="loading" class="info-text">
          Sedang mengambil data...
        </p>

        <p v-if="error" class="error-text">
          Gagal mengambil data cryptocurrency.
        </p>

        <div v-if="dataCrypto.length > 0" class="crypto-list">
          <div
            class="crypto-item"
            v-for="crypto in dataCrypto"
            :key="crypto.rank"
          >
            <div class="rank-box">
              <span class="rank-label">Rank</span>
              <strong>{{ crypto.rank }}</strong>
            </div>

            <div class="crypto-info">
              <span class="crypto-name">{{ crypto.name }}</span>
              <strong class="crypto-symbol">{{ crypto.symbol }}</strong>
            </div>

            <div class="price-box">
              <span class="usd-label">USD</span>
              <strong>{{ crypto.price_usd }}</strong>
            </div>
          </div>
        </div>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonButton
} from '@ionic/vue'

import { ref, onMounted } from 'vue'
import axios from 'axios'

interface Crypto {
  rank: string
  name: string
  symbol: string
  price_usd: string
}

const dataCrypto = ref<Crypto[]>([])
const loading = ref(false)
const error = ref(false)

const ambilDataCrypto = async () => {
  loading.value = true
  error.value = false
  dataCrypto.value = []

  try {
    const response = await axios.get(
      'https://api.coinlore.net/api/tickers/'
    )

    dataCrypto.value = response.data.data.slice(0, 100)
  } catch (err) {
    error.value = true
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  ambilDataCrypto()
})
</script>

<style scoped>
.container {
  max-width: 420px;
  margin: 0 auto;
  padding-top: 20px;
}

.refresh-button {
  display: block;
  width: 120px;
  margin: 0 auto 20px auto;
}

.info-text {
  text-align: center;
  margin-top: 16px;
}

.error-text {
  text-align: center;
  margin-top: 16px;
  color: red;
}

.crypto-list {
  border: 1px solid #d6c27a;
  border-radius: 4px;
  overflow: hidden;
}

.crypto-item {
  display: grid;
  grid-template-columns: 70px 1fr 110px;
  align-items: center;
  background-color: #fff3c4;
  border-bottom: 10px solid #000000;
  padding: 10px;
  color: #000000;
}

.crypto-item:last-child {
  border-bottom: none;
}

.rank-box {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.rank-label {
  font-size: 11px;
}

.rank-box strong {
  font-size: 24px;
}

.crypto-info {
  display: flex;
  flex-direction: column;
}

.crypto-name {
  font-size: 12px;
}

.crypto-symbol {
  font-size: 24px;
}

.price-box {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.usd-label {
  font-size: 11px;
}

.price-box strong {
  font-size: 20px;
}
</style>