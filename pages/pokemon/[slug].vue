<template>
  <NuxtLayout
    name="pokemon-detail"
    :color="color"
    :baseColor="baseColor"
    :title="data?.num.toString().padStart(3, '0')"
  >
    <img
      :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/official-artwork/${data.num}.png`"
      class="w-[60%] mx-auto"
      loading="lazy"
    />
    <div class="bg-white mx-5 p-5 -mt-32 rounded-lg">
      <div class="mt-28 text-center">
        <h1 class="font-bold text-3xl text-gray-700">{{ pokemon.name }}</h1>
        <div class="flex flex-wrap gap-2 justify-center mt-3">
          <Badge v-for="type in pokemon.types" :key="type" :type="type" />
        </div>
      </div>
      <div class="mt-5">
        <h2 class="text-lg font-bold text-gray-700 mb-3">Information</h2>
        <p class="text-gray-500 leadi mb-3">{{ pokemon.description }}</p>
        <div class="flex text-gray-500 gap-3">
          <label class="w-20">Weight</label>
          <p>:</p>
          <p>{{ pokemon.weight }} KG</p>
        </div>
        <div class="flex text-gray-500 gap-3">
          <label class="w-20">Height</label>
          <p>:</p>
          <p>{{ pokemon.height }} M</p>
        </div>
        <div class="flex text-gray-500 gap-3">
          <label class="w-20">Abilities</label>
          <p>:</p>
          <p>{{ pokemon.abilities.join(", ") }}</p>
        </div>
        <div class="flex text-gray-500 gap-3">
          <label class="w-20">Specie</label>
          <p>:</p>
          <p>{{ pokemon.specie }}</p>
        </div>
      </div>
      <div class="mt-5">
        <h2 class="text-lg font-bold text-gray-700 mb-3">Statistic Basic</h2>
        <Stat label="HP" :color="baseColor" :value="pokemon.stats.hp" />
        <Stat label="Attack" :color="baseColor" :value="pokemon.stats.attack" />
        <Stat
          label="Defense"
          :color="baseColor"
          :value="pokemon.stats.defense"
        />
        <Stat label="Speed" :color="baseColor" :value="pokemon.stats.speed" />
        <Stat
          label="SP Attack"
          :color="baseColor"
          :value="pokemon.stats.speedAttack"
        />
        <Stat
          label="SP Defense"
          :color="baseColor"
          :value="pokemon.stats.speedDefense"
        />
      </div>

      <ClientOnly>
        <div class="mt-5">
          <h2 class="text-lg font-bold text-gray-700 mb-3">Evolution</h2>
          <div class="flex space-x-5 overflow-auto">
            <Evolution
              v-for="evolution in pokemon.evolutions"
              :key="evolution"
              :name="evolution"
              :color="baseColor"
              :is-last="
                evolution === pokemon.evolutions[pokemon.evolutions.length - 1]
              "
            />
          </div>
        </div>
      </ClientOnly>
    </div>
    <br />
  </NuxtLayout>
</template>

<script setup>
import Badge from "~/components/Badge.vue";
import api from "~/services/axiosIntance";
import { pokemonColor } from "~/shared/pokemonColor";
import { StatusBar, Style } from "@capacitor/status-bar";
import { Capacitor } from "@capacitor/core";
import { onMounted } from "vue";

definePageMeta({
  layout: false,
});

const { slug } = useRoute().params;
const { data } = await useAsyncData("pokemon-detail", async () => {
  const req = await api.get(`pokedex/${slug.replace("-", " ")}`);
  return req.data;
});

const pokemon = data.value.variations[0];
const color = pokemonColor(pokemon.types[0], 0.8);
const baseColor = pokemonColor(pokemon.types[0], 1, true);
useHead({
  title: `${pokemon.name} | Pokedex`,
  meta: [
    {
      name: "description",
      content: pokemon.description,
    },
    {
      name: "theme-color",
      content: baseColor,
    },
  ],
});

onMounted(async () => {
  if (Capacitor.isNativePlatform()) {
    await StatusBar.setOverlaysWebView({ overlay: false });
    await StatusBar.setStyle({
      style: Style.Dark,
    });
    await StatusBar.setBackgroundColor({
      color: baseColor,
    });
  }
});
</script>
