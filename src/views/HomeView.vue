<template>
  <main>
    <header>
      <form class="link" @submit="updateId">
        <span> {{ url }}</span>
        <input
          required
          class="pixels-input"
          type="number"
          min="1"
          max="5000"
          size="6"
          :value="id"
          ref="input"
        />
      </form>
      <div class="actions">
        <div @click="focus">
          <PrevButton @click="id == 1 ? err() : id-- && show()" />
          <NextButton @click="id == 5000 ? err() : id++ && show()" />
        </div>
        <CopyButton :id="id" />
      </div>
    </header>
    <iframe :src="link" frameborder="0"></iframe>
  </main>
  <GitHub />
</template>

<script setup>
import { computed, ref, watch } from 'vue'
import { toast } from '../modules/swal'
import Swal from 'sweetalert2'
import CopyButton from '@/components/CopyButton.vue'
import GitHub from '@/components/GitHub.vue'
import PrevButton from '@/components/PrevButton.vue'
import NextButton from '@/components/NextButton.vue'

const id = ref(localStorage.getItem('id') ? Number(localStorage.getItem('id')) : 1)
const url = 'https://play.pixels.xyz/pixels/share/'
const link = computed(() => url + id.value)
const input = ref(null)

watch(id, (newVal) => {
  localStorage.setItem('id', String(newVal))
})

function err() {
  toast({
    title: 'Invalid map ID',
    text: 'The map ID must be between 1 and 5000.',
    icon: 'error'
  })
}

function show() {
  toast({
    title: 'Warping to map #' + id.value,
    text: 'This may take a few seconds...',
    icon: 'info',
    didOpen() {
      Swal.showLoading()
    }
  })
}

function updateId() {
  const val = input.value.value
  if (val > 5000 || val < 1) {
    err()
    return false
  }

  id.value = val
  show()
}
</script>

<style lang="scss" scoped>
header {
  margin-bottom: 10px;
  display: flex;
  align-items: center;

  link {
    margin-right: 10px;
  }

  .pixels-input {
    margin-left: 3px;
    font-family: inherit;
    color: rgb(255, 255, 255);
    border-radius: 0px;
    border: none;
    outline: none;
    background-color: transparent;
    font-size: 18px;
    transition: box-shadow 0.1s ease-out 0s;
    box-shadow: rgba(255, 255, 255, 0.3) 0px 1px;
  }
  .actions {
    margin-left: 10px;
    margin-top: 5px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-grow: 1;
    div {
      display: flex;
      align-items: center;
      span {
        margin-right: 10px;
      }
    }
  }
}

iframe {
  width: 100%;
  height: 84vh;
}

@media only screen and (max-width: 768px) {
  header {
    display: block;
    .actions {
      margin-left: 0;
    }
  }
  /* Target tablets and smaller devices */
  iframe {
    height: 70vh;
  }
}
</style>
