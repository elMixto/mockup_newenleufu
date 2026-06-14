<script setup lang="ts">
import { computed } from 'vue';

interface Experience {
  id: number;
  name: string;
  shortName: string;
  duration: string;
  price: number;
  desc: string;
  img: string;
}

const props = withDefaults(defineProps<{
  experience: Experience;
  activeVariant?: 'modern' | 'traditional';
}>(), {
  activeVariant: 'modern'
});

const emit = defineEmits<{
  (e: 'view-detail', id: number): void;
}>();

const formatCLP = (value: number) => {
  return new Intl.NumberFormat('es-CL', { style: 'currency', currency: 'CLP' }).format(value);
};

const isTraditional = computed(() => props.activeVariant === 'traditional');

const visualTag = computed(() => {
  if (isTraditional.value) {
    if (props.experience.id === 1) return { text: 'Más popular', class: 'bg-(--color-green-newen) text-(--color-crema-newen)' };
    if (props.experience.id === 2) return { text: 'Apto para grupos', class: 'bg-(--color-crema-newen) text-(--color-carbon-newen) border border-(--color-gold-newen)/50' };
    return { text: 'Nuevo', class: 'bg-(--color-carbon-newen) text-(--color-crema-newen)' };
  } else {
    if (props.experience.id === 1) return { text: 'Más popular', class: 'bg-green-100 text-green-800' };
    if (props.experience.id === 2) return { text: 'Apto para grupos', class: 'bg-blue-100 text-blue-800' };
    return { text: 'Nuevo', class: 'bg-amber-100 text-amber-800' };
  }
});
</script>

<template>
  <article 
    class="group overflow-hidden transition-all duration-300 flex flex-col hover:-translate-y-1 cursor-pointer" 
    :class="isTraditional 
      ? 'bg-white rounded-md shadow-md border-2 border-(--color-gold-newen)/10 hover:border-(--color-gold-newen)/40 hover:shadow-lg' 
      : 'bg-white rounded-2xl shadow-sm border border-gray-100 hover:shadow-xl'"
    @click="emit('view-detail', props.experience.id)"
  >
    <div class="relative overflow-hidden h-64 bg-gray-200">
      <img :src="props.experience.img" :alt="props.experience.name" class="w-full h-full object-cover group-hover:scale-105 transition-transform duration-500" />
      
      <!-- Visual Tag -->
      <div 
        class="absolute top-4 left-4 px-3 py-1 font-bold shadow-sm uppercase tracking-wider"
        :class="[
          isTraditional ? 'text-[10px] rounded-sm' : 'text-xs rounded-full',
          visualTag.class
        ]"
      >
        {{ visualTag.text }}
      </div>
      
      <div 
        class="absolute top-4 right-4 px-3 py-1 text-xs font-bold rounded shadow-sm"
        :class="isTraditional 
          ? 'bg-(--color-crema-newen) border border-(--color-gold-newen)/20 text-(--color-carbon-newen) rounded-sm' 
          : 'bg-white/95 backdrop-blur text-gray-900 rounded-full'"
      >
        {{ props.experience.duration.split(' ')[0] }} {{ props.experience.duration.split(' ')[1] }}
      </div>
    </div>
    
    <div 
      class="p-6 flex-1 flex flex-col"
      :class="isTraditional ? 'bg-stone-50/50 wood-texture' : ''"
    >
      <span 
        class="text-xs font-bold uppercase mb-1"
        :class="isTraditional ? 'text-(--color-gold-newen) tracking-widest' : 'text-(--color-gold-newen) tracking-wider'"
      >{{ props.experience.shortName }}</span>
      
      <h3 
        class="text-xl font-bold text-gray-900 mb-3 group-hover:text-(--color-green-newen) transition-colors leading-snug"
        :class="isTraditional ? 'font-serif-artisanal text-2xl' : 'leading-tight'"
      >
        {{ props.experience.name }}
      </h3>
      <p class="text-gray-600 text-sm mb-6 leading-relaxed line-clamp-3">{{ props.experience.desc }}</p>
      
      <div 
        class="mt-auto flex items-center justify-between border-t pt-5"
        :class="isTraditional ? 'border-(--color-gold-newen)/10' : 'border-gray-100'"
      >
        <div>
          <span class="text-gray-500 block mb-1" :class="isTraditional ? 'text-[10px] uppercase tracking-wider' : 'text-xs'">Desde</span>
          <div class="text-lg font-extrabold text-gray-900" :class="isTraditional ? 'font-serif-artisanal' : ''">{{ formatCLP(props.experience.price) }}</div>
        </div>
        <div 
          class="transition-colors duration-300"
          :class="isTraditional 
            ? 'bg-(--color-crema-newen) text-(--color-carbon-newen) border-2 border-(--color-gold-newen)/20 hover:bg-(--color-gold-newen) hover:text-white px-4 py-2 rounded-md font-bold text-xs uppercase tracking-wider' 
            : 'bg-gray-50 text-(--color-green-newen) px-4 py-2 rounded-lg font-bold text-sm group-hover:bg-(--color-green-newen) group-hover:text-white border border-gray-200 group-hover:border-transparent'"
        >
          {{ isTraditional ? 'Ver Ficha' : 'Ver Detalles' }}
        </div>
      </div>
    </div>
  </article>
</template>
