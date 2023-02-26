<template>
  <div class="">
    <div class="flex">
      <label class="flex mt-2" for="song_title">
        <p class="text-xl my-auto">Song title : </p>
        <input id="song_title" class="border border-slate-400 p-2 text-xl font-semibold" type="text" v-model="song_title">
      </label>
      <label class="flex mt-2" for="artist">
        <p class="text-xl my-auto">written by : </p>
        <input id="artist" class="border border-slate-400 p-2 text-xl font-semibold" type="text" v-model="artist">
      </label>
    </div>
    <label class="flex" for="checkbox">
      <input id="checkbox" type="checkbox" v-model="new_word_on_space">
      <p>new word on <code class="bg-gray-200 text-red-700 p-0.5"> space</code></p>
    </label>
    <ul class="flex flex-col gap-4 mx-auto px-8 my-4">
      <li draggable="true" class="px-4 py-2 bg-slate-200 " v-for="(item, index) in store">
        <input class="border border-green-600 px-4 py-2 font-bold" type="text" v-model.trim="store[index].word" @keyup.space="new_word_on_space && pushNewWord(index); "
          placeholder="Word">
        <ul class="mb-2">
          <!-- alternatif kata-kata -->
          <li draggable="true" class="flex gap-2 p-2 bg-slate-50 rounded w-fit"
            v-for="(alternative, i) in store[index].alternative">
            <input class="ml-4 px-2 border border-blue-400" type="text" v-model="alternative.word" @focusout="alternative.word === '' ? store[index].alternative.splice(i, 1) : null"
              placeholder="new alternative word">
            <button class="bg-green-200 px-1" @click="[store[index].word, store[index].alternative[i].word] = [store[index].alternative[i].word, store[index].word]">set as main word</button>
            <button class="bg-red-200 px-1 " @click="store[index].alternative.splice(i, 1);">delete alternative word</button>
          </li>
        </ul>
        <div class="flex justify-between">
          <button class="bg-green-200 px-1" @click="store[index].alternative.push({ word: '' })">new alternative word</button>
          <button class="bg-red-200 px-1 " @click="store.splice(index, 1)">delete word</button>
        </div>
      </li>
    </ul>
    <button class="bg-green-200 px-1" @click="store.push({ word: '', alternative: [] })">New Word</button>
  </div>
</template>

<script setup lang="ts">
import { useIDBKeyval } from '@vueuse/integrations/useIDBKeyval'
const new_word_on_space = ref(false)
const song_title = ref('right where you left me')
const artist = ref('taylor swift')

type Word = {
  word: string,
  alternative: Alternative[]
}
type Alternative = {
  word: string
}
function pushNewWord(index: number) {
  if (index == store.value.length - 1) {
    store.value.push({ word: '', alternative: [] })
  } else {
    store.value.splice(index + 1, 0, { word: '', alternative: [] })
  }

  // const nextWord = document.querySelectorAll<HTMLElement>('input[type="text"]')[index + 1]
  // nextWord.focus()

}
const store = useIDBKeyval<Word[]>('store', [
  { word: "", alternative: [] },
])
</script>
