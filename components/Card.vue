<template>
  <div class="min-h-screen bg-gray-100 flex justify-center items-center">
    <div
      class="
        w-
        p-6
        bg-white
        rounded-xl
        shadow-xl
        hover:shadow-2xl hover:scale-105
        transition-all
        transform
        duration-500
      "
    >
      <img class="w-64 object-cover rounded-t-md" :src="`${img}`" alt="a" />
      <div class="mt-4">
        <h1 class="text-2xl font-bold text-gray-700">{{ name }}</h1>
        <p class="text-sm mt-2 text-gray-700">{{ id }}/151</p>
        <div class="mt-3 space-x-4 flex p-1">
          <div
            class="
              p-1
              border-4
              rounded-full
              cursor-pointer
              hover:border-green-200 hover:scale-105
              transition
              transform
              duration-200
            "
          >
            <span class="block h-6 w-6 bg-green-400 rounded-full"> </span>
          </div>
          <div
            class="
              p-1
              border-4
              rounded-full
              cursor-pointer
              hover:border-blue-200 hover:scale-105
              transition
              transform
              duration-200
            "
          >
            <span class="block h-6 w-6 bg-blue-400 rounded-full"> </span>
          </div>
        </div>
        <div class="mt-4 mb-2 flex justify-between pl-4 pr-2">
          <button class="block text-xl font-semibold text-gray-700 cursor-auto">
            $12.99
          </button>
          <a class="btn-blue" :href="`${url}`">Lien</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Card',
  props: {
    name: {
      type: String,
      required: true,
    },
    img: {
      type: String,
      required: true,
    },
    alt: {
      type: String,
      required: true,
    },
    url: {
      type: String,
      required: true,
    },
    id: {
      type: Number,
      required: true,
    },
  },
  data() {
    return {}
  },
}
</script>
