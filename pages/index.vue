<template>
  <div class="m-4">
    <p v-if="$fetchState.pending">Fetching Pokemon...</p>
    <p v-else-if="$fetchState.error">An error occurred :(</p>
    <div v-else class="flex flex-col items-center">
      <h1 class="text-6xl font-normal leading-normal mt-0 mb-2 text-teal-800">
        Nuxt Poke API
      </h1>
      <div class="mb-3 pt-0">
        <input
          v-model="search"
          placeholder="Search Pokemon..."
          class="
            search
          "
        />
      </div>
      <input
        id="multiType"
        v-model="isMultiTypes"
        type="checkbox"
        name="multiType"
      />
      <label for="multiType">Multi types</label>
      <input id="heavy" v-model="isHeavy" type="checkbox" name="heavy" />
      <label for="heavy">Heavy</label>
      <div class="bg-green-500 p-2 m-2">
        <select id="select" v-model="sortBy" name="sortBy">
          <option value="pokedex">Pokedex</option>
          <option value="height">Height</option>
        </select>
      </div>
      <button class="block w-8 p-2" @click="ascending = !ascending">
        <i v-if="ascending" class=""
          ><svg
            xmlns="http://www.w3.org/2000/svg"
            className="h-1 w-1"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              strokeLinecap="round"
              strokeLinejoin="round"
              strokeWidth="{2}"
              d="M5 15l7-7 7 7"
            /></svg
        ></i>
        <i v-else class="">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            className="h-1 w-1"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              strokeLinecap="round"
              strokeLinejoin="round"
              strokeWidth="{2}"
              d="M19 9l-7 7-7-7"
            />
          </svg>
        </i>
      </button>

      <ul class="flex flex-wrap justify-center">
        <li
          v-for="pokemon in filteredList"
          :key="pokemon.name"
          class="flex-initial m-5"
        >
          <card
            :id="pokemon.id"
            :name="pokemon.name"
            :alt="`Pikachu`"
            :url="pokemon.species.url"
            :weight="pokemon.weight"
            :type="pokemon.type"
            :img="pokemon.sprites.front_default"
          />
        </li>
      </ul>
      <button class="btn-blue" @click="$fetch">Refresh</button>
      <button class="btn-blue" @click="next()">Next</button>
      <button class="btn-blue" @click="more()">More</button>
    </div>
  </div>
</template>

<script lang="ts">
import Card from '~/components/Card.vue'
const API = 'https://pokeapi.co/api/v2/pokemon'

export default {
  name: 'IndexView',
  components: { Card },
  props: {
    img: {
      type: String,
      required: false,
      default: 'f',
    },
    imgAlt: {
      type: String,
      required: false,
      default: 'Image of pokemon',
    },
    name: {
      type: String,
      required: true,
    },
    url: {
      type: String,
      required: false,
      default: 's',
    },
  },

  data() {
    return {
      pokemons: [] as any,
      search: '',
      sortBy: 'pokedex',
      ascending: true,
      isMultiTypes: false,
      isHeavy: false,
    }
  },

  async fetch(): Promise<any> {
    let results
    const p = localStorage.getItem('pokemons')
    if (p !== null) {
      this.pokemons = JSON.parse(p)
      return
    }
    try {
      const pokemonsUrl = await fetch(`${API}?limit=25&offset=0`)
        .then((res) => res.json())
        .then((res) => res.results)

      const responses = await Promise.all(
        pokemonsUrl.map((pokemon: { url: string }) => {
          return fetch(pokemon.url)
        })
      )

      const filteredResponses = responses.filter(
        (res: any) => res.status === 200
      )

      results = Promise.all(
        // Get the project name from the URL and skills from the file
        filteredResponses.map(async (fr: any) => {
          const pokemon: any = await fr.json()
          return pokemon
        })
      )
      results.then((r) => localStorage.setItem('pokemons', JSON.stringify(r)))
      const parsed: any = localStorage.getItem('pokemons')
      this.pokemons = JSON.parse(parsed)
    } catch (err) {
      console.log('Error: ', err)
    }
  },
  computed: {
    filteredList(): [] {
      let pokemons = this.pokemons

      // Sort by height
      pokemons = pokemons.sort((a: any, b: any) => {
        if (this.sortBy === 'pokedex') {
          if (a.id < b.id) {
            return -1
          }
          if (a.id > b.id) {
            return 1
          }
          return 0
        } else if (this.sortBy === 'height') {
          return a.height - b.height
        }
        return 0
      })

      if (!this.ascending) {
        pokemons.reverse()
      }

      if (this.isMultiTypes) {
        pokemons = pokemons.filter((pokemons: any) => {
          return pokemons.types.length === 2
        })
      }

      if (this.isHeavy) {
        pokemons = pokemons.filter((pokemons: any) => {
          return pokemons.weight >= 500
        })
      }

      return pokemons.filter((pokemon: any) => {
        return pokemon.name.toLowerCase().includes(this.search.toLowerCase())
      })
    },
  },
  methods: {
    next(): [] {
      return []
    },
    async more(): Promise<any> {
      let results
      try {
        const pokemonsUrl = await fetch(`${API}?limit=150&offset=25`)
          .then((res) => res.json())
          .then((res) => res.results)

        const responses = await Promise.all(
          pokemonsUrl.map((pokemon: { url: string }) => {
            return fetch(pokemon.url)
          })
        )

        const filteredResponses = responses.filter(
          (res: any) => res.status === 200
        )

        results = Promise.all(
          // Get the project name from the URL and skills from the file
          filteredResponses.map(async (fr: any) => {
            const pokemon: any = await fr.json()
            return pokemon
          })
        )

        results.then((r) => (this.pokemons = this.pokemons.concat(r)))
      } catch (err) {
        console.log('Error: ', err)
      }
    },
  },
}
</script>
