<script setup lang="ts">
import { ref, computed, watch } from 'vue';
import AppButton from './AppButton.vue';

interface Experience {
  id: number;
  name: string;
  shortName: string;
  duration: string;
  price: number;
  desc: string;
}

const props = withDefaults(defineProps<{
  experiences: Experience[];
  selectedId: number;
  activeVariant?: 'modern' | 'traditional';
}>(), {
  activeVariant: 'modern'
});

const emit = defineEmits<{
  (e: 'book-now', data: { experienceId: number; adults: number; students: number; date: string; includePhotos: boolean }): void;
  (e: 'update-selected', id: number): void;
}>();

const isTraditional = computed(() => props.activeVariant === 'traditional');

// Internal state
const localSelectedId = ref(props.selectedId);
const adultsCount = ref(1);
const studentsCount = ref(0);
const selectedDate = ref('');
const includePhoto = ref(false);

watch(() => props.selectedId, (newId) => {
  localSelectedId.value = newId;
});

watch(localSelectedId, (newId) => {
  emit('update-selected', newId);
});

const selectedExpDetails = computed(() => props.experiences.find(e => e.id === localSelectedId.value));

const totalPrice = computed(() => {
  if (!selectedExpDetails.value) return 0;
  let total = 0;
  const adultTotal = adultsCount.value * selectedExpDetails.value.price;
  const studentTotal = studentsCount.value * (selectedExpDetails.value.price * 0.8);
  total = adultTotal + studentTotal;
  
  if (includePhoto.value) {
    total += 25000; // Profesional photography fee
  }
  return total;
});

const formatCLP = (value: number) => {
  return new Intl.NumberFormat('es-CL', { style: 'currency', currency: 'CLP' }).format(value);
};

const formatUSD = (value: number) => {
  return new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD' }).format(value / 950);
};

const handleBookingSubmit = () => {
  emit('book-now', {
    experienceId: localSelectedId.value,
    adults: adultsCount.value,
    students: studentsCount.value,
    date: selectedDate.value,
    includePhotos: includePhoto.value
  });
};
</script>

<template>
  <div 
    class="sticky top-24 p-8"
    :class="isTraditional 
      ? 'bg-white rounded-md shadow-lg border-2 border-(--color-gold-newen)/15 wood-texture' 
      : 'bg-white rounded-3xl shadow-2xl border border-gray-100'"
  >
    <h3 
      class="font-bold text-gray-900 mb-8 flex items-center gap-3"
      :class="isTraditional 
        ? 'text-3xl font-serif-artisanal border-b border-(--color-gold-newen)/10 pb-4' 
        : 'text-2xl'"
    >
      <div 
        class="w-10 h-10 flex items-center justify-center"
        :class="isTraditional 
          ? 'rounded-md bg-(--color-crema-newen) border border-(--color-gold-newen)/20' 
          : 'rounded-full bg-green-50'"
      >
        <svg class="w-5 h-5" :class="isTraditional ? 'text-(--color-green-newen)' : 'text-(--color-gold-newen)'" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 7h6m0 10v-3m-3 3h.01M9 17h.01M9 14h.01M12 14h.01M15 11h.01M12 11h.01M9 11h.01M7 21h10a2 2 0 002-2V5a2 2 0 00-2-2H7a2 2 0 00-2 2v14a2 2 0 002 2z"></path>
        </svg>
      </div>
      {{ isTraditional ? 'Cotizador Territorial' : 'Cotizador Inteligente' }}
    </h3>
    
    <div class="space-y-6" :class="isTraditional ? 'text-sm' : ''">
      <!-- Step 1 -->
      <div>
        <label 
          class="block font-bold text-gray-700 mb-2"
          :class="isTraditional ? 'text-xs uppercase tracking-wider' : 'text-sm'"
        >Paso 1: Selecciona Experiencia</label>
        <div class="relative">
          <select 
            v-model="localSelectedId" 
            class="w-full appearance-none border-2 bg-stone-50 px-4 py-3.5 focus:border-(--color-green-newen) outline-none transition-colors font-semibold text-gray-700"
            :class="isTraditional ? 'rounded-md border-(--color-gold-newen)/20' : 'rounded-xl border-gray-200'"
          >
            <option v-for="exp in experiences" :key="exp.id" :value="exp.id">{{ exp.shortName }}</option>
          </select>
          <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-4 text-gray-500">
            <svg class="h-5 w-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path>
            </svg>
          </div>
        </div>
      </div>

      <!-- Step 2 -->
      <div>
        <label 
          class="block font-bold text-gray-700 mb-2"
          :class="isTraditional ? 'text-xs uppercase tracking-wider' : 'text-sm'"
        >Paso 2: Participantes</label>
        <div class="grid grid-cols-2 gap-4">
          <div>
            <span class="text-xs text-gray-500 block mb-1">Adultos</span>
            <input 
              type="number" 
              min="1" 
              v-model="adultsCount" 
              class="w-full border-2 bg-stone-50 px-4 py-3 focus:ring-0 focus:border-(--color-green-newen) outline-none transition-colors text-center font-bold text-gray-900"
              :class="isTraditional ? 'rounded-md border-(--color-gold-newen)/20' : 'rounded-xl border-gray-200'"
            />
          </div>
          <div>
            <span class="text-xs text-gray-500 block mb-1">Estudiantes</span>
            <input 
              type="number" 
              min="0" 
              v-model="studentsCount" 
              class="w-full border-2 bg-stone-50 px-4 py-3 focus:ring-0 focus:border-(--color-green-newen) outline-none transition-colors text-center font-bold text-gray-900"
              :class="isTraditional ? 'rounded-md border-(--color-gold-newen)/20' : 'rounded-xl border-gray-200'"
            />
          </div>
        </div>
      </div>

      <!-- Step 3 -->
      <div>
        <label 
          class="block font-bold text-gray-700 mb-2"
          :class="isTraditional ? 'text-xs uppercase tracking-wider' : 'text-sm'"
        >Paso 3: Fecha (Sujeto a disp.)</label>
        <input 
          type="date" 
          v-model="selectedDate" 
          class="w-full border-2 bg-stone-50 px-4 py-3 focus:ring-0 focus:border-(--color-green-newen) outline-none transition-colors text-gray-700 font-semibold"
          :class="isTraditional ? 'rounded-md border-(--color-gold-newen)/20' : 'rounded-xl border-gray-200'"
        />
      </div>

      <!-- Step 4: Adicionales -->
      <div>
        <label 
          class="block font-bold text-gray-700 mb-2"
          :class="isTraditional ? 'text-xs uppercase tracking-wider' : 'text-sm'"
        >Paso 4: Adicionales</label>
        <label 
          class="flex items-center gap-3 p-3 border rounded-md cursor-pointer transition-colors"
          :class="isTraditional 
            ? 'border-(--color-gold-newen)/20 bg-stone-50 hover:bg-stone-100' 
            : 'border-gray-200 bg-gray-50 hover:bg-green-50'"
        >
          <input type="checkbox" v-model="includePhoto" class="w-5 h-5 text-(--color-green-newen) rounded focus:ring-(--color-green-newen) border-gray-300" />
          <div class="flex-1">
            <span class="block text-xs font-bold text-gray-900" :class="isTraditional ? '' : 'text-sm'">Fotografía Profesional</span>
            <span class="block text-[10px] text-gray-500">+ $25.000 CLP</span>
          </div>
        </label>
      </div>

      <hr :class="isTraditional ? 'border-(--color-gold-newen)/15 my-6' : 'border-gray-100 my-8'" />

      <!-- Summary -->
      <div 
        class="p-5 border"
        :class="isTraditional 
          ? 'bg-(--color-crema-newen)/50 rounded-lg border-(--color-gold-newen)/20' 
          : 'bg-green-50/50 rounded-2xl border-green-100'"
      >
        <div 
          class="flex justify-between text-xs mb-3 text-gray-600 font-bold border-b pb-3"
          :class="isTraditional ? 'border-(--color-gold-newen)/10 font-serif-artisanal text-sm' : 'border-green-100 font-medium'"
        >
          <span class="truncate pr-2">{{ selectedExpDetails?.shortName }}</span>
          <span class="shrink-0">{{ formatCLP(selectedExpDetails?.price || 0) }} /pp</span>
        </div>
        <div class="flex justify-between items-end mt-3">
          <span class="text-xs text-gray-500 font-bold mb-1.5 uppercase tracking-wider">Total:</span>
          <div class="text-right">
            <div 
              class="text-3xl font-extrabold text-gray-950 tracking-tight"
              :class="isTraditional ? 'font-serif-artisanal font-bold' : 'text-(--color-green-newen)'"
            >{{ formatCLP(totalPrice) }}</div>
            <div class="text-[10px] text-gray-400 font-medium mt-1">Aprox. {{ formatUSD(totalPrice) }}</div>
          </div>
        </div>
      </div>

      <AppButton variant="primary" :active-variant="activeVariant" class="w-full py-4 text-lg" @click="handleBookingSubmit">
        Confirmar y reservar
      </AppButton>
      
      <button 
        class="w-full px-4 py-3 rounded-md text-xs font-bold tracking-wider uppercase transition-colors mt-2 border-2"
        :class="isTraditional 
          ? 'bg-(--color-crema-newen) hover:bg-white text-gray-700 border-(--color-gold-newen)/30' 
          : 'bg-white hover:bg-gray-50 text-gray-700 border-gray-200 rounded-xl text-sm'"
      >
        Enviar cotización por correo
      </button>
      
      <div class="flex items-center justify-center gap-2 text-xs text-gray-400 mt-4 font-medium">
        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z"></path>
        </svg>
        Pagos seguros mediante Webpay Plus / Stripe
      </div>
    </div>
  </div>
</template>
