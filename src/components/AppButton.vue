<script setup lang="ts">
import { computed } from 'vue';

interface Props {
  variant?: 'primary' | 'secondary' | 'light' | 'dark' | 'outline' | 'text';
  size?: 'sm' | 'md' | 'lg';
  type?: 'button' | 'submit' | 'reset';
  disabled?: boolean;
  activeVariant?: 'modern' | 'traditional';
}

const props = withDefaults(defineProps<Props>(), {
  variant: 'primary',
  size: 'md',
  type: 'button',
  disabled: false,
  activeVariant: 'modern'
});

const buttonClass = computed(() => {
  const isTraditional = props.activeVariant === 'traditional';
  
  // Base classes adaptadas al tema
  const baseClasses = `inline-flex items-center justify-center font-bold border-2 transition-all duration-300 outline-none focus:outline-none disabled:opacity-50 disabled:cursor-not-allowed ${
    isTraditional ? 'font-serif-artisanal tracking-wider uppercase' : 'font-sans'
  }`;
  
  const sizeClasses = {
    sm: isTraditional ? 'px-4 py-2 text-xs rounded-sm' : 'px-4 py-2 text-xs rounded-xl border-transparent',
    md: isTraditional ? 'px-6 py-2.5 text-sm rounded-md' : 'px-6 py-3 text-sm rounded-xl border-transparent',
    lg: isTraditional ? 'px-8 py-3.5 text-base rounded-md' : 'px-8 py-4 text-base rounded-xl border-transparent'
  };

  const variantClasses = {
    primary: isTraditional 
      ? 'bg-(--color-green-newen) hover:bg-emerald-950 text-(--color-crema-newen) border-(--color-green-newen) shadow-md hover:shadow-lg' 
      : 'bg-(--color-green-newen) hover:bg-green-800 text-white shadow-sm hover:shadow-md hover:-translate-y-0.5 transform active:translate-y-0',
    secondary: isTraditional 
      ? 'bg-(--color-gold-newen) hover:bg-amber-900 text-(--color-crema-newen) border-(--color-gold-newen) shadow-md hover:shadow-lg' 
      : 'bg-(--color-gold-newen) hover:bg-yellow-600 text-gray-950 shadow-sm hover:shadow-md hover:-translate-y-0.5 transform active:translate-y-0',
    light: isTraditional 
      ? 'bg-(--color-crema-newen) hover:bg-amber-100 text-(--color-carbon-newen) border-(--color-gold-newen)/30' 
      : 'bg-white hover:bg-gray-100 text-gray-800 border border-gray-200 shadow-sm',
    dark: isTraditional 
      ? 'bg-(--color-carbon-newen) hover:bg-black text-(--color-crema-newen) border-(--color-carbon-newen) shadow-md' 
      : 'bg-gray-900 hover:bg-black text-white shadow-md',
    outline: 'bg-transparent hover:bg-white/10 text-white border border-white/30',
    text: 'bg-transparent border-transparent hover:text-(--color-green-newen) text-gray-600 font-medium px-2 py-1'
  };

  return `${baseClasses} ${sizeClasses[props.size]} ${variantClasses[props.variant]}`;
});
</script>

<template>
  <button :type="props.type" :class="buttonClass" :disabled="props.disabled">
    <slot />
  </button>
</template>
