<script setup lang="ts">
// import HelloWorld from './components/HelloWorld.vue'
// import TheWelcome from './components/TheWelcome.vue'
import { ref, reactive } from 'vue'

interface imageData {
  id: string,
  file: string
}

const isOpenModal = ref(false)
const type = ref('')
const title = ref('')
const content = ref('')

const isSend = ref(false)

const fileArray = reactive<{ data: imageData[] }>({
  data: []
})

const selectedFile = ref('')

function submitFrom() {
  isOpenModal.value = false
  send()
}

function previewImage($event: Event) {
  const target = $event.target as HTMLInputElement
  const reader = new FileReader()
  if (target && target.files) {
    reader.readAsDataURL(target.files[0])
    reader.addEventListener('load', (event) => {
      fileArray.data.push({
        id: `d-${fileArray.data.length}`,
        file: event.target?.result as string
      })
    })
  }
}

function deletePicture(id: string) {
  fileArray.data = fileArray.data.filter(p => p.id !== id)
}

function send() {
  isSend.value = true
  setTimeout(() => {
    isSend.value = false
  }, 5000)
}
</script>

<template>
  <div class="relative bg-white w-screen h-svh">
    <div @click="isOpenModal = true"
      class="bottom-8 right-8 text-center absolute size-10 text-3xl bg-blue-400 rounded-full text-white">
      !
    </div>

    <Teleport to="body">
      <div v-if="isOpenModal" class="modal">
        <form @submit.prevent="submitFrom" class="bg-neutral-100 rounded p-4">
          <h1>意見反饋</h1>

          <div class="flex flex-col mt-4">
            <label for="a">
              <span class="text-red-500 text-sm mr-2">*</span>
              <span>類別</span>
            </label>
            <select id="a" v-model="type" class="h-10 p-2 mt-2">
              <option value="" disabled selected>請選擇類別</option>
              <option value="a-1">操作問題</option>
              <option value="a-2">優化建議</option>
              <option value="a-3">bug反饋</option>
              <option value="a-4">其他</option>
            </select>
          </div>

          <div class="flex flex-col mt-4">
            <label for="b">
              <span class="text-red-500 text-sm mr-2">*</span>
              <span>標題</span>
              <span class="text-gray-500 text-sm">(限30字符內)</span>
            </label>
            <input id="b" type="text" class="h-10 p-2 mt-2" v-model="title" maxlength="30">
          </div>

          <div class="flex flex-col mt-4">
            <label for="c">
              <span class="text-red-500 text-sm mr-2">*</span>
              <span>描述</span>
              <span class="text-gray-500 text-sm">(限300字符內)</span>
            </label>
            <textarea id="c" type="text" class="h-10 p-2 mt-2" v-model="content" maxlength="300" />
          </div>

          <div class="mt-4">
            <p>參考圖片
              <span class="text-gray-500 text-sm">(僅支持PNG、JPG格式，每張5MB內)</span>
            </p>

            <div class="w-full flex p-4">

              <div v-for=" p in fileArray.data" :key="p.id"
                class="group relative w-40 h-40 overflow-hidden mr-4 cursor-pointer ">
                <img :src="p.file" class="group-hover:opacity-10">
                <div class="group-hover:flex hidden absolute inset-0">
                  <div class="w-1/2 text-center my-auto" @click="selectedFile = p.file">
                    🔍
                  </div>
                  <div class="w-1/2 text-center my-auto" @click="deletePicture(p.id)">
                    🗑️
                  </div>
                </div>
              </div>

              <div v-show="fileArray.data.length < 3">
                <label for="d"
                  class="flex justify-center items-center size-40 border-dashed border-2 rounded text-center cursor-pointer">
                  <div class="text-neutral-600">+</div>
                </label>
                <input id="d" type="file" accept="image/*" name="d-0" class="hidden" @change="previewImage($event)">
              </div>

            </div>
          </div>

          <p>參考圖片
            <span class="text-gray-500 text-sm"> 可提供意見／問題截圖（上傳數量 {{ fileArray.data.length }} /3）</span>
          </p>

          <div class="h-full">
            <button class="bg-sky-200 text-sky-500 w-full p-3 rounded mt-5" type="submit">
              提交
            </button>
          </div>
        </form>

        <div v-if="selectedFile" class="bg-slate-800/20 absolute inset-0 flex justify-center items-center">
          <div class="h-fit w-80 bg-white">
            <div class="text-right w-full p-6 cursor-pointer" @click="selectedFile = ''">X</div>
            <img :src="selectedFile" class="p-2">
          </div>
        </div>
      </div>
    </Teleport>

    <div v-if="isSend" class="bg-slate-800/20 absolute inset-0 flex justify-center items-center">
      <div class="h-fit w-96 bg-white">
        <div class="flex justify-around mt-6">
          <h2 class="text-green-500 ml-8">
            <span class="inline-block size-7 bg-green-500 rounded-full text-center text-white">v</span> 提交成功
          </h2>
          <div class="text-right cursor-pointer" @click="isSend = false">X</div>
        </div>

        <p class="p-8">謝謝您的反饋，我們將繼續努力，提供更優值的服務</p>
      </div>
    </div>
  </div>

</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}

.modal {
  position: fixed;
  z-index: 999;
  top: 20%;
  left: 50%;
  width: 800px;
  margin-left: -400px;
}
</style>
